---
title: Django와 Redis 연동하기
excerpt: Redis를 사용하여 Caching 사용하기

header:
  overlay_image: /assets/images/splash/laptop.jpg
  caption: "Photo by Clément H on [**Unsplash**](https://unsplash.com)"
---

# Redis 설정하기

```sh
$ docker run redis
```

`django-redis` 패키지를 설치해주자

```sh
$ pip install django-redis
```

redis IP주소 가져오기

{% raw %}

```sh
$ docker inspect -f "{{ .NetworkSettings.IPAddress }}" CONTAINER_ID
```

{% endraw %}
