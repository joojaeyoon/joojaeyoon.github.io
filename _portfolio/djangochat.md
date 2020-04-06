---
title: "DjangoChat"
excerpt: "Django Channels를 사용한 실시간 채팅 사이트"

classes: wide
header:
  overlay_image: /assets/images/splash/chat.jpg
  caption: "Photo by Daniel Korpai on [**Unsplash**](https://unsplash.com)"
  teaser: /assets/images/portfolio/chat.jpg
  actions:
    - label: "Open"
      url: "http://34.230.193.35/"

sidebar:
  - image: /assets/images/portfolio/chat/chat.jpg
    image_alt: "djangochat"
  - title: "DjangoChat"
gallery:
  - url: /assets/images/portfolio/chat/chat.jpg
    image_path: /assets/images/portfolio/chat/chat.jpg
    alt: "chat"
  - url: /assets/images/portfolio/chat/chat_room.jpg
    image_path: /assets/images/portfolio/chat/chat_room.jpg
    alt: "chat room"
  - url: /assets/images/portfolio/chat/friend.jpg
    image_path: /assets/images/portfolio/chat/friend.jpg
  - url: /assets/images/portfolio/chat/find.png
    image_path: /assets/images/portfolio/chat/find.png
    alt: "friend list"
  - url: /assets/images/portfolio/chat/login.png
    image_path: /assets/images/portfolio/chat/login.png
  - url: /assets/images/portfolio/chat/register.png
    image_path: /assets/images/portfolio/chat/login.png
---

# DjangoChat

## 프로젝트 소개

**Django**와 **React**를 활용해서 만든 프로젝트로 **Djagno Channels**와
**Websocket**을 활용하여 실시간으로 채팅을 주고 받을 수 있는 기능을
제공합니다.

[웹사이트](http://34.230.193.35/)
_(AWS EC2 서버를 사용중입니다. AWS 북미서버 사용중이라 속도가 느립니다.)_

{% include gallery %}

## 구현 기능

- 로그인 / 회원가입
- 친구 찾기 및 친구 추가
- Websocket을 통한 실시간 채팅

## 프로젝트를 통해 배운 것들

<p>
    <ul>
        <li>Redis와 Websocket을 사용한 실시간 통신 방법</li>
        <li>Docker Compose를 사용한 멀티 컨테이너 사용 방법</li>
    </ul>
</p>

<a href="https://github.com/joojaeyoon/djangochat" itemprop="sameAs" rel="nofollow noopener noreferrer">
  <i class="fab fa-fw fa-github" aria-hidden="true"></i>
  <span class="label">GitHub</span>
</a>
