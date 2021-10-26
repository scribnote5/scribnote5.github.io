---
title: "Github Page minimal-mistakes 테마 설정"
excerpt: "Github page jekyll 사용 중 뷰에서 마음에 들지 않은 레이아웃을 변경하고 커스터마이징 한 코드를 설명한다."
categories:
  - Github Page
last_modified_at: 2020-05-05
layout: post
---
- Github page jekyll 사용 중 뷰에서 마음에 들지 않은 레이아웃을 변경하고 커스터마이징 한 코드를 설명한다.

- 본 블로그는 github page의 jekyll 템플릿을 사용하여 개발되었으며, jekyll 템플릿의 minimal-mistakes 테마를 사용하였다.
- 블로그 생성 및 설정은 <https://devinlife.com/howto> 페이지를 참고하였다. 해당 페이지는 github page 배포, 댓글 달기, 구글 애널리스틱(통계 및 검색)까지 처음 github page 생성에 필요한 기초 내용을 친절하게 설명하고 있다. 해당 페이지를 따라한다면 1시간 안에 무난하고 웬만한 기본 기능이 들어간 gitub page를 생성할 수 있다.



## Sidebar Category 리스트 출력하기
- 처음 github page을 생성하면서 가장 먼저 고려하였던 레이아웃은 그림처럼 sidebar에 category 리스트가 나오는 것이었다. 하지만 기본 설정을 변경하는 것으로 category 리스트가 나오는 기능을 추가할 수 없었다. 기존 category 리스트를 출력하는 파일인 _layouts/categories.html의 코드를 바탕으로 해당 기능을 구현하였다.
- Category 리스트 링크는 하단의 jekyll 문법 페이지를 참고하였다. 만약 Category가 'Sabre Lite'처럼 공백이 포함되어 있다면 URL에서는 이를 'Sabre%Lite'로 인식하여, 원하는 category 리스트로 이동이 불가능하다. 이러한 문제를 인식 후 구현하였다.

출처: <https://jekyllrb.com/docs/liquid/filters/>

![image](/assets/img/2020-05-05-GithubPage1/image1.png)

```
$ _includes/sidebar.html
```

- github page는 해당 소스 코드의 jekyll 문법을 인식하여, 일부 소스 코드가 제대로 출력되지 않는다. 따라서 gitgub URI와 이미지 파일로 대체한다. ~~코드는 하단 댓글로 첨부한다.~~

github page Link: <https://github.com/scribnote5/scribnote5.github.io/blob/master/_includes/sidebar.html>

![image](/assets/img/2020-05-05-GithubPage1/image0.png)


- _includes/sidebar.html 파일에 코드를 추가하면 카테고리 리스트가 성공적으로 나온다. 만약 카테고리 리스트의 개수가 많아진다면 scroll bar가 생겨 sidebar 일부를 가릴 수 있다. 이는 전체적인 뷰의 아름다움?을 해친다고 생각하여, 카테고리 리스트에 scroll bar가 생기지 않도록 코드를 추가하였다.

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
- sidebar와 body(본문 내용)의 여백이 부족하여서, 전체적인 여백의 미?를 해치고 화면에 나오는 게시글의 폭이 너무 적어 scroll bar가 너무 길어지게 되었다. 다음 그림처럼 여백의 미? 느끼기 위해서 sidebar과 body의 좌측 padding 키웠다.

![image](/assets/img/2020-05-05-GithubPage1/image2.png)

- minimal-mistakes 테마의 경우 body 공간이 너무 작게 설정되어, 게시글들의 문자열이 길어지는 경우 1줄로 나오는 문자열들이 2줄로 나왔다. 다음 그림처럼 body 공간을 키우기 위해서 body의 우측 padding을 줄였다.

![image](/assets/img/2020-05-05-GithubPage1/image3.png)

```
_layouts/single.html
```

```html
<!-- style 태그 추가하여 padding 조절(좌우 25px 여백 추가) -->
<article class="page" itemscope itemtype="https://schema.org/CreativeWork" style="padding: 0px 35px 0px 35px;">
```

```
_layouts/taxonomy.html
```

```html
<!-- style 태그 추가하여 padding 조절(좌우 25px 여백 추가) -->
<div class="archive" style="padding: 0px 35px 0px 35px;">
```

```
_layouts/archive.html
```

```html
<!-- style 태그 추가하여 padding 조절(좌우 25px 여백 추가) -->
<div class="archive" style="padding: 0px 35px 0px 35px;">
```

```
_layouts/search.html
```

```html
<!-- style 태그 추가하여 padding 조절(좌우 25px 여백 추가) -->
<div class="archive" style="padding: 0px 35px 0px 35px;">
```

```
$ _sass/minimal-mistakes/_page.scss
```

```css
#main {
  @include clearfix;
  margin-left: auto;
  margin-right: auto;
  <!-- 주석 처리-->
  // padding-left: 1em;
  // padding-right: 1em;
  -webkit-animation: $intro-transition;
  animation: $intro-transition;
  max-width: 100%;
  -webkit-animation-delay: 0.15s;
  animation-delay: 0.15s;

  @include breakpoint($x-large) {
    max-width: $max-width;
  }
}
```



## 모바일 모드에서 sidebar의 padding 조절
- 다음 그림처럼 모바일 모드에서 화면 width와 sidebar의 여백이 부족하여서, 전체적인 여백의 미?를 해치고 있다. 여백의 미? 느끼기 위해서 sidebar에 우측과 좌측 padding 키웠다.

![image](/assets/img/2020-05-05-GithubPage1/image4.png)

```
$ _sass\minimal-mistakes\_sidebar.scss
```

```css
.sidebar {
  @include clearfix();
  @include breakpoint(max-width $large) {
    /* fix z-index order of follow links */
    position: relative;
    z-index: 10;
    padding: 0px 15px 0px 15px;
    -webkit-transform: translate3d(0, 0, 0);
    transform: translate3d(0, 0, 0);
  }
```
