---
title: "Resume"
permalink: /resume/
layout: resume
classes: wide

header:
  overlay_image: /assets/images/portfolio/resume-overlay.jpg
  caption: "Photo by Bram Naus on [**Unsplash**](https://unsplash.com/)"
  overlay_filter: 0.3

images:
  - image_path: /assets/images/portfolio/resume.jpg
    title: Resume
---

<div>
    {% assign profile=page.images[0] %}
    <img class="resume-profile" src="{{ profile.image_path }}" alt="{{ profile.title}}"/>
</div>

### 주재윤 Jaeyoon Joo

#### 백엔드 개발자

안녕하세요 서버 개발에 흥미를 느껴 백엔드 개발을 공부하고 있는 주재윤입니다.<br/>
테스트 코드의 중요성을 느껴 항상 테스트 코드를 작성하는 습관을 들이려 노력하고 있습니다.<br/>
새로운 기술을 배우는 것을 좋아합니다.

#### 📚 Stack

다룰 수 있는 기술/언어들 입니다.

- Python
- Django / Django Rest Framework
- Docker
- MySQL
- git

#### 💻 Projects

공부하면서 만든 개인 프로젝트들 입니다.

<div class="resume-projects">
  {% for post in site.portfolio reversed limit:6 %}
    {% include portfolio-archive.html type="grid" %}
  {% endfor %}
</div>

#### 📧 jaeyoon@jooz.dev
