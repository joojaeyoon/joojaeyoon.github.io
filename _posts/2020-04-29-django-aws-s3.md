---
title: Django와 AWS S3 연결하기
excerpt: Django 프로젝트에 S3 연결하고 파일 업로드하기

header:
  overlay_image: /assets/images/splash/aws.png
  overlay_filter: 0.5
  caption: Photo by Christian Wiediger on [**Unsplash**](https://unsplash.com/)
  teaser: /assets/images/teaser/django.png

categories:
  - Django

tags:
  - django
  - aws
---

Django 프로젝트와 AWS S3를 연결하기

```
📦Joodemy
 ┣ 📂Joodemy
 ┃ ┣ 📜asgi.py
 ┃ ┣ 📜settings.py
 ┃ ┣ 📜urls.py
 ┃ ┣ 📜wsgi.py
 ┃ ┗ 📜__init__.py
 ┗ 📜manage.py
```

# AWS IAM 설정

S3를 생성하기 전에 우선 [AWS IAM](https://console.aws.amazon.com/iam/home)에서 사용자를 생성하고 권한을 부여해야한다. IAM 대시보드로 들어가서 사용자 탭으로 이동한다.

사용자 추가 버튼을 눌러준다.

![aws_iam_1](/assets/images/2020/04/29/aws_iam_1.png)

사용자 이름을 입력하고 액세스 유형에서 프로그래밍 방식 엑세스를 체크해준다.

![aws_iam_2](/assets/images/2020/04/29/aws_iam_2.png)

여기서 그룹을 생성해서 S3에 대한 권한을 부여해야한다. 그룹 생성을 눌러 그룹을 생성해준다.

![aws_iam_3](/assets/images/2020/04/29/aws_iam_6.png)

그룹 생성을 누르면 이런 화면이 나오는데, S3를 검색해서 `AmazonS3FullAccess`를 체크하고 생성해준다.

![aws_iam_4](/assets/images/2020/04/29/aws_iam_3.png)

생성한 그룹을 체크하고 다음으로 넘어간다.

![aws_iam_5](/assets/images/2020/04/29/aws_iam_4.png)

태그는 따로 건드리지 않고 이대로 생성한다.

![aws_iam_6](/assets/images/2020/04/29/aws_iam_5.png)

사용자를 생성하면 이렇게 `액세스 키`와 `비밀 액세스 키`를 얻을 수 있는데 나중에 S3연결시에 사용해야 하므로 `.csv 다운로드`를 눌러서 다운받아놓자.

![aws_iam_6](/assets/images/2020/04/29/aws_iam_7.png)

# S3 버킷 생성

이제 AWS에서 `S3` 버킷을 생성하자

![aws_s3_1](/assets/images/2020/04/29/aws_s3_1.png)

버킷 생성 버튼을 누르고,

![aws_s3_2](/assets/images/2020/04/29/aws_s3_2.png)

버킷 이름과 지역을 설정해준다.

![aws_s3_3](/assets/images/2020/04/29/aws_s3_3.png)

퍼블릭 액세스 차단은 체크를 해제해주고 생성하면 끝.

# Django 설정

Django에서 S3 스토리지를 사용하기 위해 django_storages와 S3와의 통신을 위해 boto3 패키지를 설치해준다.

```py
~# pip install django_storages boto3
```

`settings.py` 의 `INSTALLED_APPS`에 `storages`를 추가해주자.

```py
# Joodemy/Joodemy/settings.py

...

INSTALLED_APPS=[
	...
	'storages',
	...
]
```

## AWS 관련 설정

아까 얻은 `ACCESS_KEY_ID`와 `SECRET_ACCESS_KEY`를 사용해서 settings.py 하단에 다음과 같이 설정해준다.

```py
# Joodemy/Joodemy/settings.py

...

DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
STATICFILES_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'

AWS_ACCESS_KEY_ID = "your access key" # 액세스 키
AWS_SECRET_ACCESS_KEY = "your secret access key" # 비밀 액세스 키

AWS_REGION = "ap-northeast-2" # AWS 지역

AWS_STORAGE_BUCKET_NAME = "your bucket name" # 버킷 이름
AWS_S3_CUSTOM_DOMAIN = '%s.s3.%s.amazonaws.com' % (
    AWS_STORAGE_BUCKET_NAME, AWS_REGION)

```

## Storages 설정 (선택)

여기까지만 설정하고 사용해도 사용하는데 문제는 없지만 `media`파일과 `static`파일들을 구분지어서 편하게 관리하기 위해서 각 파일들이 저장될 장소를 지정하려고한다.

```
📦Joodemy
 ┣ 📂Joodemy
 ┃ ┣ 📜asgi.py
 ┃ ┣ 📜settings.py
 ┃ ┣ 📜storages.py
 ┃ ┣ 📜urls.py
 ┃ ┣ 📜wsgi.py
 ┃ ┗ 📜__init__.py
 ┗ 📜manage.py
```

`Jodemy/Joodemy/storages.py` 파일을 생성하고 다음과 같이 작성한다.

```py
# Jodemy/Joodemy/storages.py

from django.conf import settings
from storages.backends.s3boto3 import S3Boto3Storage


class StaticStorage(S3Boto3Storage):
    location = settings.AWS_STATIC_LOCATION


class PublicMediaStorage(S3Boto3Storage):
    location = settings.AWS_PUBLIC_MEDIA_LOCATION
    file_overwrite = False


class PrivateMediaStorage(S3Boto3Storage):
    location = settings.AWS_PRIVATE_MEDIA_LOCATION
    default_acl = 'private'
    file_overwrite = False
    custom_domain = False

```

`Joodemy/Joodemy/settings.py` 하단에 아래와 같이 추가해주자.

```py
# Joodemy/Joodemy/settings.py

...

AWS_STATIC_LOCATION = 'static'
STATICFILES_STORAGE = 'Joodemy.storages.StaticStorage'

AWS_PUBLIC_MEDIA_LOCATION = 'media/public'
DEFAULT_FILE_STORAGE = 'Joodemy.storages.PublicMediaStorage'

AWS_PRIVATE_MEDIA_LOCATION = 'media/private'
PRIVATE_FILE_STORAGE = 'Joodemy.storages.PrivateMediaStorage'
```

# 결과

`collectstatic` 명령어를 사용하면 정상적으로 S3에 파일들이 생성되는것을 확인할 수 있다.

```sh
~# python manage.py collectstatic
```

![aws_result_1](/assets/images/2020/04/29/aws_result_1.png)
