---
title: "Github Page Jekyll 설정"
excerpt: "Github page jekyll 사용 중 뷰에서 마음에 들지 않은 부분을 변경하고 커스터마이징 한 코드를 설명한다."

categories:
  - Github Page

last_modified_at: 2020-05-05
---
- Github page jekyll 사용 중 뷰에서 마음에 들지 않은 부분을 변경하고 커스터마이징 한 코드를 설명한다.
- 본 블로그는 github page의 jekyll 템플릿을 사용하여 개발되었으며, jekyll 템플릿의 minimal-mistakes 테마를 사용하였다. 
- 블로그 생성 및 설정은 https://devinlife.com/howto/ 페이지를 참고하였다. 해당 페이지는 github page 배포, 댓글 달기, 구글 애널리스틱(통계 및 검색)까지 처음 github page 생성에 필요한 기초 내용을 친절하게 설명하고 있다. 해당 페이지를 따라한다면 1시간 안에 무난하고 웬만한 기본 기능이 들어간 gitub page를 생성할 수 있을 것이다.



## Sidebar 카테고리 리스트 출력하기
- 처음 github page을 생성하면서 가장 먼저 고려하였던 레이아웃은 그림처럼 sidebar에 카테고리 리스트가 나오게 하는 것이었다. 하지만 기본 설정을 변경하는 것으로 카테고리 리스트가 나오는 레이아웃을 추가할 수 없었다. sidebar에 해당 레이아웃을 추가하기 위해서 기존 categories 리스트를 출력하는 파일인 _layouts/categories.html의 코드를 바탕으로  복붙하고 수정하였다. 
- 카테고리 링크는 하단의 jekyll 문법을 참고하였다.

출처: <https://jekyllrb.com/docs/liquid/filters/>

![image](/assets/images/2020-05-05-GithubPage1/image1.png)

```
$ _includes/sidebar.html
```

```html
{% if page.author_profile or layout.author_profile or page.sidebar %}
  <div class="sidebar sticky scrollbar__hide" style="-ms-overflow-style: none;">
  {% if page.author_profile or layout.author_profile %}{% include author-profile.html %}{% endif %}
  {% if page.sidebar %}
    {% for s in page.sidebar %}
      {% if s.image %}
        <img src=
        {% if s.image contains "://" %}
          "{{ s.image }}"
        {% else %}
          "{{ s.image | relative_url }}"
        {% endif %}
        alt="{% if s.image_alt %}{{ s.image_alt }}{% endif %}">
      {% endif %}
      {% if s.title %}<h3>{{ s.title }}</h3>{% endif %}
      {% if s.text %}{{ s.text | markdownify }}{% endif %}
      {% if s.nav %}{% include nav_list nav=s.nav %}{% endif %}
    {% endfor %}
    {% if page.sidebar.nav %}
      {% include nav_list nav=page.sidebar.nav %}
    {% endif %}
  {% endif %}
  
  <!-- 카테고리 갯수를 세고 이를 출력하는 코드 -->
  {% assign categories_max = 0 %}
  {% for category in site.categories %}
    {% if category[1].size > categories_max %}
      {% assign categories_max = category[1].size %}
    {% endif %}
  {% endfor %}
 
  <hr>
  <h3><a href="/categories/"><span style="font-weight: bold;">Categories</span></a></h3>
  <ul class="taxonomy__index__sidebar">
    {% for i in (1..categories_max) reversed %}
      {% for category in site.categories %}
        {% if category[1].size == i %}
        <li>
          <a href="{{ '/categories/#' | append: category[0] | camelcase | downcase | prepend: site.url | replace: ' ', '-' }}">
            {{ category[0] }}&nbsp;&nbsp;<span class="taxonomy__count">{{ i }}
          </a> 
        </li>
        {% endif %} 
      {% endfor %}
    {% endfor %}
  </ul>
  </div>
{% endif %}
```

- _includes/sidebar.html 파일에 코드를 추가하면 카테고리 리스트가 성공적으로 나온다. 만약 카테고리 리스트의 개수가 많아진다면 scroll bar가 생겨 side bar 일부를 가릴 수 있다. 이는 전체적인 뷰의 아름다움?을 해친다고 생각한다. 따라서 카테고리 리스트에 scroll bar가 생기지 않도록 코드를 추가하였다. 

```
$ _includes/sidebar.html
```

```html
<!-- 새로운 클래스 및 style 태그 추가 -->
<div class="sidebar sticky scrollbar__hide" style="-ms-overflow-style: none;">
```

```
$ _sass/minimal-mistakes/_page.scss
```

```html
<!-- 파일 제일 하단에 추가 -->
.scrollbar__hide::-webkit-scrollbar {
  display:none;  
}
```



## body와 sidebar간의 padding 조절 및 게시 글 공간 늘리기
- sidebar와 body(본문 내용)의 여백이 부족하여서, sidebar와 body의 레이아웃이 전체적인 여백의 미?를 해치게 되었다. 다음 그림처럼 여백의 미? 느끼기 위해서 sidebar과 body의 좌측 padding 키웠다.

![image](/assets/images/2020-05-05-GithubPage1/image2.png)

- minimal-mistakes 테마의 경우 body 공간이 너무 작게 설정되어, 게시글들의 문자열이 길어지는 경우 1줄로 나오는 문자열들이 2줄로 나왔다. 다음 그림처럼 body 공간을 키우기 위해서 body의 우측 padding을 줄였다. 

![image](/assets/images/2020-05-05-GithubPage1/image3.png)

```
_layouts/single.html
```

```html
<!-- style 태그 추가하여 padding 조절(좌우 50px 여백 추가) -->
<article class="page" itemscope itemtype="https://schema.org/CreativeWork" style="padding: 0px 50px 0px 50px;">
```

```
_layouts/taxonomy.html
```

```html
<!-- style 태그 추가하여 padding 조절(좌우 50px 여백 추가) -->
<div class="archive" style="padding: 0px 50px 0px 50px;">
```

```
_layouts/archive.html
```

```html
<!-- style 태그 추가하여 padding 조절(좌우 50px 여백 추가) -->
<div class="archive" style="padding: 0px 50px 0px 50px;">
```

```
_layouts/search.html
```

```html
<!-- style 태그 추가하여 padding 조절(좌우 50px 여백 추가) -->
<div class="archive" style="padding: 0px 50px 0px 50px;">
```