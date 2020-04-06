---
title: "Resume"
permalink: /resume/
layout: resume
classes: wide

header:
  overlay_image: /assets/images/portfolio/resume-overlay.jpg
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

#### 📚 Stack

다룰 수 있는 기술/언어들 입니다.

- Python
- Django
- Docker
- git

#### 💻 Projects

공부하면서 만든 개인 프로젝트들 입니다.

<div class="resume-projects">
  {% for post in site.portfolio limit:4 %}
    {% include portfolio-single.html type="grid" %}
  {% endfor %}
</div>

#### 📧 jaeyoon@jooz.dev
