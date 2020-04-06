---
title: "실시간 객체 추적 시스템"
excerpt: "YOLO와 KCF 필터를 사용한 실시간 객체 추적 시스템"

classes: wide
header:
  overlay_image: /assets/images/portfolio/tracking.png
  teaser: /assets/images/portfolio/tracking.png

github: "https://github.com/joojaeyoon/YOLO_Tracker"

sidebar:
  - image: /assets/images/portfolio/tracking.png
    image_alt: "tracker"
  - title: "Object Tracker"
gallery:
  - url: /assets/images/portfolio/tracking/FlowChart.png
    image_path: /assets/images/portfolio/tracking/FlowChart.png
    alt: "FlowChart"
  - url: /assets/images/portfolio/tracking/image_processing.png
    image_path: /assets/images/portfolio/tracking/image_processing.png
    alt: "image post processing"
  - url: /assets/images/portfolio/tracking/performance.png
    image_path: /assets/images/portfolio/tracking/performance.png
    alt: "performance.png"
---

## 프로젝트 소개

졸업논문 **독거노인을 케어를 위한 딥 러닝 기반 실시간 객체 추적 시스템**에 사용된 프로젝트입니다.

<p>
    실시간 객체 인식 방법인 YOLO와 객체 추적 알고리즘 KCF를 결합하여
    기존의 YOLO만 사용하던 시스템보다 더 빠른 방식으로 객체를 추적하는
    시스템입니다.
</p>
<p>
    우선 가정에 설치된 웹캠으로부터 시스템에 사람이 포함된 이미지를
    전송받습니다. 전송 받은 이미지를 YOLO를 사용해서 이미지 안의 사람의
    위치를 찾습니다. YOLO로 인해 사람이 검출되면 다음 단계로 넘어가고,
    검출이 되지 않을 경우 검출이 될 때까지 계속해서 검출합니다. 이미지에서
    사람 위치가 포함된 경계 상자가 결정되면 KCF 트래커에 검출된 경계
    상자를 입력하고 다음 프레임에서의 독거노인의 위치를 추적합니다.
</p>

# Object Tracker

{% include gallery %}

## 구현 기능

- 이미지 전처리를 통한 속도 개선
- YOLO와 KCF를 사용한 실시간 객체 인식 + 추적

## 프로젝트를 통해 배운 것들

- pytorch를 사용해서 데이터를 학습시키고 인식하는 방법
