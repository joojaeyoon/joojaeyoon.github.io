---
title: "DjangoTube"
excerpt: "Django를 사용한 영상 스트리밍 서비스"

classes: wide
header:
  overlay_image: /assets/images/splash/laptop.jpg
  caption: "Photo by Clément H on [**Unsplash**](https://unsplash.com)"
  teaser: /assets/images/portfolio/tube/main.jpg
  actions:
    - label: "Open"
      url: "http://54.86.62.77/"

sidebar:
  - image: /assets/images/portfolio/tube/main.jpg
    image_alt: "dtube"
  - title: "DjangoTube"
gallery:
  - url: /assets/images/portfolio/tube/main.jpg
    image_path: /assets/images/portfolio/tube/main.jpg
    alt: "main page"
  - url: /assets/images/portfolio/tube/login.png
    image_path: /assets/images/portfolio/tube/login.png
    alt: "Login"
  - url: /assets/images/portfolio/tube/upload.png
    image_path: /assets/images/portfolio/tube/upload.png
    alt: "Upload"
  - url: /assets/images/portfolio/tube/video.jpg
    image_path: /assets/images/portfolio/tube/video.jpg
    alt: "Video detail"
---

# DjangoTube

## 프로젝트 소개

**Django Rest Framework**를 사용해 만든 API 기반의 영상 스트리밍 서비스 입니다. API를 활용하여 서버에 업로드된 영상들의 정보를 받아오거나 영상을 업로드하는 기능을 제공합니다.

[웹사이트](http://54.86.62.77/)
_(AWS EC2 서버를 사용중입니다. AWS 북미서버 사용중이라 속도가 느립니다.)_

{% include gallery %}

## 구현 기능

- 로그인 / 회원가입
- 비디오 업로드
- 비디오 댓글

## 테스트

**TDD(Test Driven Development)**방식의 개발을 진행했습니다. 앞서 다른
프로젝트들을 통해 테스트 코드가 없는 프로젝트는 불안정하다는 것을
느꼇기 때문에 새로운 API를 만들 때 마다 그에 해당하는 테스트 코드를 작성했습니다.

테스트를 적용한 경우는 아래와 같습니다.

- 기존에 의도했던 데이터를 받아오는가?
- 잘못된 요청에 대해 그에 맞는 상태 코드를 반환하는가?

## 프로젝트를 통해 배운 것들

- REST API를 통한 파일 업로드 방법
- DRF APIView에서 GET, POST, PUT, PATCH, DELETE 전의 데이터 전처리 방법
- Nginx 와 Django를 연동해서 배포하는 방법
- Travis를 통한 테스트 자동화

<a href="https://github.com/joojaeyoon/DjangoTube" itemprop="sameAs" rel="nofollow noopener noreferrer">
  <i class="fab fa-fw fa-github" aria-hidden="true"></i>
  <span class="label">GitHub</span>
</a>
