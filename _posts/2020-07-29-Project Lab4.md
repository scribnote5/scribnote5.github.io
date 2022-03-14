---
title: "Project Lab 4. 게시판 개발 - 1"
excerpt: "Spring Boot CRUD 게시판 개발 과정을 소개한다."
categories:
  - Web
  - Project Lab
last_modified_at: 2021-04-17
layout: post
---
- Spring Boot CRUD 게시판 개발 과정을 소개한다.



- github: <https://github.com/scribnote5/lab>
- github commit: <https://github.com/scribnote5/lab/commit/f788602d24b6fd1791f3e8ca2d8f379852103f5b>



## 프로젝트 설계 및 리펙토링 내용
- 프로젝트의 기본 프레임(CRUD 게시판, 디자인 등)은 '처음 배우는 스프링 부트 2' 도서를 참고하였다.
- CRUD 게시판은 프로젝트의 기본이 되는 기능으로 '이런 부분에서는 어떻게 하면 코드 가독성과 속도를 향상시킬 수 있을까?'라는 생각을 가지고 다음과 같이 소스 코드를 리펙토링 하였다.

<br>
- '처음 배우는 스프링 부트 2' 도서 자세히 보기: <http://www.hanbit.co.kr/store/books/look.php?p_code=B4458049183>
- 프로젝트 구조 및 흐름: <https://gmlwjd9405.github.io/2018/12/25/difference-dao-dto-entity.html>
- REST 개념: <https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html>


### 영문화
- 웹 페이지의 기본 언어는 한글 대신 영어를 사용한다.


### properties 파일을 yml 파일로 변경
- yml 파일은 설정 및 기타 정적 값을 키 값 형식으로 관리하므로, properties 파일보다 가독성이 좋다. 따라서 기존 application.properties 파일을 application.yml 파일로 변경하였다.


### RESTful
- 도서에서는 RESTful 조건을 지키기 위해서 두 개의 서버로 분리하였다.
- View를 담당하는 서버와 REST API를 제공하는 서버 총 2개가 동시에 실행된다. 즉, module-app-web(@Controller, view를 담당) 서버와 module-app-api(@RestController, api를 담당) 서버 2개가 동시에 실행된다.


### JPA 영속성 컨텍스트 에러
- 도서를 참고 하다가 다음 에러가 발생하였다. 최신 버전의 Spring Boot를 사용하여 발생한 문제로 추측된다.

```
Failed to lazily initialize a collection of role could not initialize proxy – no Session
```

- JPA는 매번 데이터베이스에 접근하는 비효율적인 계산을 방지하기 위해 영속성 컨텍스트에  엔티티를 관리한다.
- findOne은 엔티티의 데이터를 가져오지만, getOne은 엔티티의 참조를 가져온다. JPA는 Lazy Evaluation는 엔티티의 참조가 필요한 경우 해당 트렌젝션에서만 사용되며 프록시(중계 역할)를 반환한다.
- 기존 소스 코드는 getOne으로 레퍼런스를 가져왔지만, 서로 다른 트랜잭션에서 사용하였기에 위와 같은 에러가 발생한다. 따라서 getOne으로 반환된 레퍼런스가 같은 트랜잭션에서 사용되도록 @Transactional 애노테이션을 Service 계층에서 사용하는 메소드에 명시하였다.

```java
@Transactional
public Long updateNoticeBoard(Long idx, NoticeBoard noticeBoard) {
   NoticeBoard persistNoticeBoard = noticeBoardRepository.getOne(idx);
   persistNoticeBoard.update(noticeBoard);

   return noticeBoardRepository.save(noticeBoard).getIdx();
}
```

출처: <https://bebong.tistory.com/entry/JPA-Lazy-Evaluation-LazyInitializationException-could-not-initialize-proxy-%E2%80%93-no-Session>


### jQuery 지양, Vanilla Javascript 지향
- jQuery 사용을 지양하고 Vanilla Javascript 사용을 지향한다. 분명 jQuery는 훌륭하고 웹 개발에 빠질 수 없이 사용되는 라이브러리지만 현재 트렌드와 맞지 않는다. ajax와 Javascript를 대체할 수 없는 경우에만 jQuery를 사용할 것이다.

출처: <https://marshall-ku.com/web/%EC%99%9C-%EB%B8%94%EB%A1%9C%EA%B7%B8%EC%97%90-vanilla-js%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%96%88%EB%82%98>


### 'read.html' 페이지 추가
- 게시글을 읽는 'read.html' 페이지를 생성하였다.


### HTML id, name, class 속성 구분
- HTML id, name, class 속성은 각 목적에 따라 사용이 다르다. 도서에서는 이를 구분하지 않고 id 속성만 사용한다. 서버로 데이터를 전송하는 태그는 id 대신 name 속성을 사용하였다.
- id 속성은 Element를 구분하는 고유한 식별자로서 태그를 변경하기 위해서 사용된다.
- name 속성은 form 이벤트 발생 시 서버로 데이터를 전송하기 위한 식별자로 사용된다. 다음 설명하는 serializeObject 함수를 사용하기 위해서는 input 태그의 식별자로 name 속성을 사용해야 한다.

출처: <https://hashcode.co.kr/questions/7049/id-name-class-%EC%86%8D%EC%84%B1%EC%97%90-%EB%8C%80%ED%95%9C-%EC%82%AC%EC%9A%A9%EB%B2%95%EC%9D%B4-%EA%B6%81%EA%B8%88%ED%95%A9%EB%8B%88%EB%8B%A4>


### serializeObject 라이브러리 사용
- ajax 통신을 위한 JSON 객체를 생성할 때 필요한 반복되는 변수 초기화 코드는 작성하기 번거롭고 비효율적이다. 이런 문제점을 해결하기 위해서 form 태그를 사용하는 경우 input tag의 value를 자동으로 JSON으로 변환하는 serializeObject 라이브러리를 사용하였다.
- form 태그를와 같이 serializeObject 라이브러리를 사용하면, 하단 예제와 같이 반복되는 변수 초기화 소스 코드를 제거할 수 있다.
- 기존 소스 코드: JSON 객체에 변수 직접 초기화

```javascript
var jsonData = JSON.stringify({
   title: $('#title').val(),
   content: $('#content').val(),
   activeStatus: $('#activeStatus option:selected').val()
});
```

<br>

- 변경된 소스 코드: serializeObject를 사용하여 JSON 객체 생성

```javascript
var jsonData = $("#form").serializeObject();
```

출처: <https://www.leafcats.com/28><br>
<https://kingbbode.tistory.com/28><br>
<https://github.com/macek/jQuery-serialize-object>


### HTML id, name 명명규칙 변경
- HTML id와 name의 명명 규칙은 각 회사마다 다르게 정의하였기에 정답이 없다. 네이버에서는 snake case를 사용하고 다음에서는 camel case를 사용하는 것으로 확인하였다.
-  serializeObject 라이브러리를 사용하기 위해서는 자바 멤버 필드명과 자바스크립트 변수명이 같아야 하므로, HTML id와 name 속성 명명규칙으로 camel case을 적용하였다.


### 'list.html' 게시글 생성일자로 내림차순
- 게시글 idx(PK) 순서대로 내림 차순으로 정렬되도록 변경하였다.

```java
pageable = PageRequest.of(pageable.getPageNumber() <= 0 ? 0 : pageable.getPageNumber() - 1, pageable.getPageSize(), Sort.Direction.DESC, "idx");
```


### HTML 파일에서 script 및 link 태그 위치
- script 및 css 파일은 html에 삽입되는 위치에 따라서 웹 페이지 로딩 속도가 차이가 발생한다.
- 하단 출처에서 권장하는 것처럼, css 파일은 head 태그 내에 그리고 script 파일은 body 태그 끝에 위치하도록 thymeleaf 레이아웃을 구성하였다.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <!-- css -->
    <th:block th:replace="layout/css.html"></th:block>

    <title>NoticeBoard Form</title>
</head>
<body>

...

<!-- script file -->
<th:block th:replace="layout/script.html"></th:block>

...

</body>
</html>
```

출처: <https://hahahoho5915.tistory.com/31><br>
Thymeleaf 레이아웃 적용 방법 출처: <https://eblo.tistory.com/57>


### WYSIWYG editor 적용
- 오픈 소스 WYSIWYG editor(웹 에디터)인 summernote를 게시판에 사용하였다.
- 프로젝트 게시판에서는 summernote 기본 기능만 제공하며, 이에 필요한 summernote font 파일, bootstrap.css, bootstrap.js를 추가하였다.

![image](/assets/img/2020-07-29-Project Lab4/image1.png)

```html
<body>
    ...
    <td colspan="2"><textarea name="content" id="summernote" class="summernote" th:text="*{content}"></textarea></td>
    ...
</body>

<!-- javascript -->
<script>
   $(document).ready(function() {
       <!-- summernote setting -->
       $('#summernote').summernote({
           height: 250,   // 에디터 높이
           minHeight: null,   // 최소 높이
           maxHeight: null,   // 최대 높이
           // focus: true,    // 에디터 로딩후 포커스를 맞출지 여부
           lang: "ko-KR",// 한글 설정
           placeholder: "The editor's max input size of bytes is 16777215."   //placeholder 설정

       });
   });
</script>
```

![image](/assets/img/2020-07-29-Project Lab4/image2.png)

summernote 사용법 및 적용 방법: <https://summernote.org/getting-started/><br>
<https://programmer93.tistory.com/27>



## 프로젝트 실행 및 결과
- Run -> Edit Configureations... -> module-app-api과 module-app-web 모듈의 Profile을 'local'로 변경 후 두 모듈을 동시에 실행한다. 이후 '<http://localhost:8080/notice-board/list>'에 접속하면 하단 그림과 같이 CRUD 게시판을 확인하였다.

![image](/assets/img/2020-07-29-Project Lab4/image3.png)

![image](/assets/img/2020-07-29-Project Lab4/image4.png)

![image](/assets/img/2020-07-29-Project Lab4/image5.png)

![image](/assets/img/2020-07-29-Project Lab4/image6.png)
