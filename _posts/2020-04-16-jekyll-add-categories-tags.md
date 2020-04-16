---
title: Jekyll 카테고리, 태그 목록 추가하기
excerpt: jekyll 블로그에 카테고리, 태그 분류하기

header:
  overlay_image: /assets/images/splash/post-it.jpg
  filter: 0.3
  

categories:
  - Blog
tags:
  - jekyll
---

# 목표

아래 그림처럼 블로그 사이드바 아래쪽에 카테고리랑 태그 분류를 만들기로 했다.

![sidebar](/assets/images/2020/04/sidebar.png)

# 카테고리 추가

우선 \_include 폴더에 새로 category-tag-list.html 파일을 만들어준다

{% raw %}

```html
<!-- _include/category-tag-list.html -->

<p class="page__taxonomy">
    <strong
    ><i class="fas fa-fw fa-folder-open" aria-hidden="true"></i>
    Categories:
    </strong>
    <p >
    {% for category in site.categories %}
    <a
        href="/categories/{{ category[0] | slugify: 'pretty' }}"
        class="page__taxonomy-item category-item"
        rel="tag"
    >
        {{ category[0] }}
        <span class="category-count">{{category | last | size}}</span>
    </a>
    {% endfor %}
    </p>
</p>
```

{% endraw %}

**`site.categories`** 안에 블로그의 카테고리 데이터가 들어있고 각 데이터를 **`category`**에 넣어서

**`category[0]`**으로 카테고리명을, **`category | last | size`**을 사용해서 각 카테고리안의 항목의 수를 알 수 있다.

작성을 완료한 후에 기존의 사이드바 하단에 작성한 파일을 추가해준다.

{% raw %}

```
{% include category-tag-list.html %}
```

{% endraw %}

내용을 추가한 후 확인을 해보면 정상적으로 나오는 것을 확인할 수 있다.

![category](/assets/images/2020/04/category.png)

# 태그 추가

태그도 카테고리와 마찬가지로 **`site.tags`**를 참조해서 사용하면 된다.

하지만 많이 사용된 태그 순으로 정렬해서 출력하고 싶었기 때문에 먼저 태그를 정렬해서 사용하기로 했다.

{% raw %}
```html
<!-- _include/category-tag-list.html -->

...

{% capture tags %}
  {% for tag in site.tags %}
    ${{ tag[1].size | plus: 1000 }}#{{ tag[0] }}#{{ tag[1].size }}
  {% endfor %}
{% endcapture %}

{% assign sortedtags = tags | split:'$' | sort %}
{% assign sortedtags = sortedtags | slice: 1, sortedtags.size %}

```

각 태그의 **`tag[1].size`** 는 태그가 사용된 횟수, **`tag[0]`**는 태그명을 나타낸다.

각 태그들을 **$횟수+1000#태그명#횟수** 의 형태로 만들고, **`sortedtags`**에 담아서 **`'$'`**를 기준으로 split한 뒤 정렬을 한다.

정렬을 마친 **`sortedtags`**는 다음과 같은 형태가 된다.

```
1001#django#1
1002#python#2
1004#jekyll#4
```

그 다음 카테고리에서 한 것처럼 똑같이 추가해주면 된다.

```html
<!-- _include/category-tag-list.html -->

...
<p class="page__taxonomy">
    <strong
    ><i class="fas fa-tags" aria-hidden="true"></i> 
    Tags:
    </strong>
    <br/><br/>
    {% for tag in sortedtags reversed %}
    {% assign tagitems = tag | split: '#' %}
    <a
        href="/tags/{{ tagitems[1] | slugify: 'pretty' }}" 
        class="page__taxonomy-item" 
        rel="tag">
        {{ tagitems[1] }}
    </a>
    {% endfor %}
</p>
```
{% endraw %}

# 결과

## 생성 전

![before](/assets/images/2020/04/before.png)

## 생성 후

![after](/assets/images/2020/04/after.png)