---
title: Django social login 추가하기
excerpt: django app에 google, github 소셜 로그인 추가하기

header:
  overlay_image: /assets/images/splash/phone.jpg
  overlay_filter: 0.5
  caption: Photo by William Hook on [**Unsplash**](https://unsplash.com/)
  teaser: /assets/images/teaser/django.png

github: "https://github.com/joojaeyoon/DjangoTube"

categories:
  - Django

tags:
  - django
---

# Project Tree

```
# Django project tree

📦app
 ┣ 📂app
 ┃ ┣ 📜asgi.py
 ┃ ┣ 📜settings.py
 ┃ ┣ 📜urls.py
 ┃ ┣ 📜wsgi.py
 ┃ ┗ 📜__init__.py
 ┣ 📂login
 ┃ ┣ 📜apps.py
 ┃ ┣ 📜views.py
 ┃ ┗ 📜__init__.py
 ┣ 📂templates
 ┃ ┣ 📜login.html
 ┃ ┗ 📜success.html
 ┗ 📜manage.py
```

# OAuth App 생성

Django 로그인 폼에 google과 github 소셜 로그인을 추가하려고 한다.

우선 [Github 개발자 페이지](https://github.com/settings/developers), [Google 개발자 콘솔](https://console.developers.google.com/) 두 곳에서 각각 **Oauth App**을 생성하고

**Client ID**와 **Client Secret**을 얻어오자.

**Authorization callback**도 꼭 등록해주자.

ex) http://localhost:8000/accounts/github/login/callback/

# django-allauth 설치

우선 `django-allauth` 패키지를 설치해주자.

```sh
~# pip install django-allauth
```

`settings.py`의 INSTALLED_APPS에 allauth를 추가해주자.

```py
# app/app/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    "django.contrib.sites",		# 이것도 추가

    'allauth',
    'allauth.account',
    'allauth.socialaccount',

    # providers
    'allauth.socialaccount.providers.facebook',
    'allauth.socialaccount.providers.github',
    'allauth.socialaccount.providers.google',
    'allauth.socialaccount.providers.kakao',
    'allauth.socialaccount.providers.naver',
]
```

allauth에서는 많은 provider들을 지원하는데 그 중에 필요한 provider만 추가해주자.

더 많은 provider는 [allauth 공식 문서](https://django-allauth.readthedocs.io/en/latest/overview.html)를 참고하자.

`settings.py` 하단에 아래 설정들도 입력하자.

```py
# app/app/settings.py

...

SITE_ID = 1

AUTHENTICATION_BACKENDS = (
	# 'allauth'와 관계없이 Django 관리자에서 사용자 이름으로 로그인 해야함.
    'django.contrib.auth.backends.ModelBackend',

    # e-mail로 로그인 하는 것과 같은 'allauth' 특정 인증 방법
    'allauth.account.auth_backends.AuthenticationBackend',
)

# Provider 세부 설정
SOCIALACCOUNT_PROVIDERS = {
    'google': {
        'SCOPE': [
            'profile',
            'email',
        ],
        'AUTH_PARAMS': {
            'access_type': 'online',
        }
    },
    'github': {
        'SCOPE': [
            'user',
            'repo',
            'read:org',
        ],
    }
}

# 로그인 성공 시에 연결될 URL
LOGIN_REDIRECT_URL = '/success/'
```

등록 한 뒤에는 migrate 명령어를 사용해서 db에 적용해주자.

```sh
~# python manage.py migrate
```

마이그레이션을 하고 Django 어드민 페이지에 가면 하단에 **SOCIAL ACCOUNTS** 탭이 생성되어 있는데,

**SOCIAL ACCOUNTS** 탭의 **Social application**에 들어가서 google과 github application을 생성해준다.

![Django-admin](/assets/images/2020/04/django-admin.png)

위에서 얻은 **Client ID**와 **Secret Key**를 입력하고 **Sites**를 추가해준다.

![social-application](/assets/images/2020/04/social-application.png)

# 로그인 뷰 생성

`LoginView`와 로그인 성공 시 연결될 `SuccessView`를 생성한다.

```py
from django.shortcuts import render

def LoginView(request):
    return render(request, "login.html")

def SuccessView(request):
    return render(request, "success.html")

```

`urls.py`에 `allauth.urls`를 연결해주고 `LoginView`와 `SuccessView`를 등록해준다.

```py
# app/app/urls.py

from django.contrib import admin
from django.urls import path, include
from login.views import LoginView, SuccessView

urlpatterns = [
    path('admin/', admin.site.urls),

    path('accounts/', include('allauth.urls')),
    path('login/', LoginView),
    path("success/", SuccessView),
]

```

# 로그인 템플릿 설정

## 로그인 폼 수정하기

기존 로그인 폼

![Login Form](/assets/images/2020/04/login_before.png)

Django 로그인 템플릿(app/templates/login.html)의 상단에

{% raw %}

```html
{% load socialaccount %}
```

를 추가하고

로그인 폼 아래에 google과 github 로그인 링크를 생성한다.

```html
<a
  type="button"
  class="btn-floating btn-li btn-sm"
  href="{% provider_login_url 'google' %}"
>
  <i class="fab fa-google"></i>
</a>
<a
  type="button"
  class="btn-floating btn-git btn-sm"
  href="{% provider_login_url 'github' %}"
>
  <i class="fab fa-github"></i>
</a>
```

{% endraw %}

생성된 소셜 로그인 링크

![Login Form](/assets/images/2020/04/login_after.png)

## 로그인 성공 페이지 작성하기

{% raw %}

```html
<!-- app/templates/success.html -->

{% load socialaccount %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Login Success!</title>
</head>
  <h2>Login Success!</h2>
  {% for account in user.socialaccount_set.all %}
    <p><img src="{{ account.get_avatar_url }}"/></p>

    <p>UID: <a href="{{ account.extra_data.link }}">{{ account.uid }}</a></p>

    <p>email: {{ account.extra_data.email }}</p>
    <p>Name: {{ account.extra_data.name }}</p>
  {% endfor %}
</body>
</html>
```

{% endraw %}

# 결과

## Google

이제 구글 소셜 로그인 버튼을 눌러보면 정상적으로 구글 계정 선택 화면으로 넘어가고

![google_login](/assets/images/2020/04/google_auth_login.png)

로그인도 정상적으로 된다.

![google_login_success](/assets/images/2020/04/google_login_success.png)

## Github

Github도 마찬가지로 정상적으로 작동한다.

![github_login](/assets/images/2020/04/github_auth_login.png)

![github_login_success](/assets/images/2020/04/github_login_success.png)

<a href="{{ page.github }}" class="btn btn--primary btn--x-large">
<i class="fab fa-fw fa-github" aria-hidden="true"></i>
</a>
