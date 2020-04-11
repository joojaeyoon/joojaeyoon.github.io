---
title: Django Commands 사용하기
excerpt: commands를 사용하여 db 연결 확인하기

header:
  overlay_image: assets\images\splash\laptop2.jpg
  caption: Photo by Caspar Camille Rubin on [**Unsplash**](https://unsplash.com)
---

# Docker Compose 문제

Docker compose를 통해 다음과 같이 여러 컨테이너를 사용할 때에 문제가 있는걸 발견했다.

```yml
# docker-compose.yml

version: "3.7"
services:
  django:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres
```

나는 위 코드처럼 `django`의 `depends_on` 에 `db`를 추가해놓으면
`docker-compose up`을 사용해서 서비스를 실행할 때 `db` 준비 -> `django` 실행이 되면서 `django`와 `db`연결에 아무 문제가 없다고 생각했다.

하지만 종종 `docker compose`를 실행했을 때 django 서버에서 db 연결에 실패하는 것을 발견했는데, [Docker 공식 문서](https://docs.docker.com/compose/compose-file/#depends_on)를 보고 왜 그 문제가 발생하는지 확인할 수 있었다.

> There are several things to be aware of when using depends_on:
>
> - **depends_on** does not wait for **db** and **redis** to be “ready” before starting **web** - only until they have been started. If you need to wait for a service to be ready, see Controlling startup order for more on this problem and strategies for solving it.
> - Version 3 no longer supports the **condition** form of **depends_on**.
> - The **depends_on** option is ignored when deploying a stack in swarm mode with a version 3 Compose file.

[Docker 공식 문서](https://docs.docker.com/compose/compose-file/#depends_on)의 depends_on 항목에는 위처럼 쓰여있었는데 `depends_on` 옵션은 `db`가 "준비"될 때까지 기다려주지 않고 단순히 실행 순서만 정해준다는 것이었다.

# Django Commands

위 문제를 해결하기 위해 Django Commands([Django 공식 문서](https://docs.djangoproject.com/en/3.0/howto/custom-management-commands/))를 작성하여 해결하기로 했다.

우선 아래와 같이 `app`을 만들고 그 안에 `management/commands` 폴더를 만들어주자

```
app/
    __init__.py
    models.py
    management/
        __init__.py
        commands/
            __init__.py
    tests.py
    views.py
```

그런 다음 `management/commands`에 `wait_for_db.py` 파일을 만들어준다.

```py
# app/management/commands/wait_for_db.py

import time

from django.db import connections
from django.db.utils import OperationalError
from django.core.management.base import BaseCommand


class Command(BaseCommand):
    help="Django command to pause execution until database is available"

    def handle(self, *args, **options):
        self.stdout.write('**Waiting for Database...**')
        db_conn = None
        while not db_conn:
            try:
                db_conn = connections['default']
            except OperationalError:
                self.stdout.write('**Database unavailable, waiting...**')
                time.sleep(1)

        self.stdout.write(self.style.SUCCESS('**Successfully connected to Database.**'))
```

위와 같이 작성을 하고 `docker-compose.yml` 파일을 수정해주자.

```yml
# docker-compose.yml

services:
  django:
    build:
      context: .

    ...
    command: >
      sh -c "python manage.py wait_for_db &&
            python manage.py runserver 0.0.0.0:8000"
    depends_on:
      - db
```

django 서버가 실행되기 전에 `wait_for_db` command가 먼저 실행되도록 command에 `python manage.py wait_for_db`를 추가해준다.

# 결과

```sh
$ docker-compose up
app_1  | **Waiting for database...**
app_1  | **Successfully connected to Database.**
app_1  | System check identified no issues (0 silenced).
app_1  | April 11, 2020 - 10:24:21
app_1  | Django version 2.1.15, using settings 'app.settings'
app_1  | Starting development server at http://0.0.0.0:8000/
app_1  | Quit the server with CONTROL-C.
```

`docker-compose up`을 실행했을 때 다음과 같이 정상적으로 `db`를 기다렸다가 서버가 실행되는 것을 확인할 수 있다.
