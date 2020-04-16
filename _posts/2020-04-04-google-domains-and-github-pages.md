---
title: "Github Pages와 Google Domains 연결하기"
excerpt: ".dev 도메인을 구매하고 Github Pages와 연결하기"

header:
  teaser: /assets/images/2020-04-04/google_domains.png
  overlay_image: /assets/images/splash/book.jpg
  caption: "Photo credit: [**Unsplash**](https://unsplash.com)"
  overlay_filter: 0.5

categories:
  - Blog
tags:
  - Github Pages
  - Google Domains
---

## Goolge Domains에서 도메인 구매하기

도메인 구매를 위해 hosting.kr 등등 국내 사이트들을 찾아봤다. 원래는 `jooz.com`을 얻고 싶었는데 이미 사용 중이어서
다른 도메인을 찾다가 `.dev` 도메인이 있는 걸 알게되고 찾아보게 됐는데, 국내 호스팅 사이트들은 `.dev` 도메인을 지원하는 곳이 없었다.
여러 호스팅 사이트들을 찾다가 [Google Domains](https://domains.google/)를 찾게 됐는데, 한국에서는 정식으로 지원을 하지 않는 상태였다.
![google_domains_region_lock](/assets/images/2020-04-04/region_lock.png)

한국에서 구매할 수 있는 다른 방법이 없나 찾아봤는데, 그냥 좌측 패널에서 국가를 지원하는 다른 국가로 변경만 해주면 별다른 설정 없이 쉽게 구매가 가능했다.<br/>
![google_domains_select_region](/assets/images/2020-04-04/select_region.png)

사용하려는 도메인을 검색하면 아래와 같이 나오는데, 다행히 내가 사용하고 싶었던 도메인은 구매가 가능했다.
![google_domains_search](/assets/images/2020-04-04/domain_search.png)

## Google Domains 설정하기

도메인을 구매하고 나면 아래와 같이 관리 페이지에서 내 도메인들을 확인할 수 있는데,<br/> 여기서 관리 버튼을 눌러 설정하려는 도메인의 관리 페이지로 들어간다.
![google_domains_manage](/assets/images/2020-04-04/manage_domains.png)

설정하려는 도메인 관리 페이지로 들어가서 좌측 패널의 DNS 페이지로 들어가면<br/>
![google_left_panel](/assets/images/2020-04-04/left_panel.png)

아래와 같이 맞춤 리소스 레코드 설정을 할 수 있는데, Github Pages에서 지원하는 A 레코드 4개를 추가해준다.<br/>
([Github Pages Help](https://help.github.com/en/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site) 참조)
![google_add_record](/assets/images/2020-04-04/add_record.png)

여기까지 하면 Google Domains에서 하는 설정은 끝이다.

## Github pages 설정

우선 github pages를 사용하는 repository에서 설정 페이지로 들어간다.
![github_settings](/assets/images/2020-04-04/git_settings.png)

아래쪽 Github Pages 탭에서 Custom domain에 등록한 도메인을 등록한 뒤 Save를 하고,
Enforce HTTPS를 활성화하면 끝. Enforce HTTPS가 활성화되어있지 않다면 Google Domains의 세팅이 제대로 되었는지 확인해봐야 한다.
![page_settings](/assets/images/2020-04-04/pages_settings.png)

## 결과

설정을 모두 마치면 등록한 도메인 주소로 정상적으로 접속이 되는걸 확인할 수 있으며,
기존의 Github pages 주소로 접속을 시도해도 아래와 같이 등록한 도메인으로 바뀌는 것을 볼 수 있다.

![access_domain](/assets/images/2020-04-04/access_domain.gif)
