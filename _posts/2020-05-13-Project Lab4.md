---
title: "&nbsp;4. 게시판 개발 - 1"
excerpt: "Spring boot CRUD 게시판 개발 과정을 소개한다. "

categories:
  - Web
  - Project Lab

last_modified_at: 2020-05-13
---
- Spring boot CRUD 게시판 개발 과정을 소개한다. 
- github: <https://github.com/scribnote5/lab>
- github commit: <https://github.com/scribnote5/lab/commit/25823d6f4c8b10426da173d327ceabf4421c8721>



## 프로젝트 흐름을 이해하는 데 도움이 되는 웹 페이지 
- 프로젝트의 세부적인 메커니즘 설명은 생략한다. 따라서 프로젝트의 전체적인 흐름이 잘 이해 되지 않는다면 도서와 하단 웹 페이지들을 공부하는 것을 추천한다. 

- 도서 자세히 보기: <http://www.hanbit.co.kr/store/books/look.php?p_code=B4458049183>
- 프로젝트 전체 흐름: <https://gmlwjd9405.github.io/2018/12/25/difference-dao-dto-entity.html>
- REST 개념: <https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html>



## 변경된 사항
- '처음 배우는 스프링 부트 2' 도서와 필자가 개발한 게시판 코드와의 차이점을 기술한다. 
- 도서에서 제공하는 게시판 코드는 체계적으로 잘 구현된 코드지만 '이런 부분에서는 이렇게 하면 가독성이 좋아지고 개발이 조금더 빨라지지 않을까?'라는 생각을 하였다.(물론 게시판 코드를 처음 구현하라고 하면 절대 못할 것이다.) 게시판 코드를 공부 후 이러한 필자의 생각을  반영하여 게시판 코드를 변경하였다. 변경된 게시판 코드는 완벽하다고 보장할 수 없지만, 계속 공부하고 고민하면서 변경히였기에 나름 좋은 게시판 코드라고 생각한다.(필자의 기준으로만...) 
- 이 후 부족한 게시판 기능은 추후 게시글에서 구현하도록 하겠다. 


### 게시판 이름 변경
- 연구실 홈페이지는 새로운 소식을 전달하는 'Newsline' 게시판이 존재한다. 연구실 홈페이지를 따라서 기존 코드의 'Board' 패키지명, 파일명, 클래스명 등을 'NoticeBoard'로 변경하였다.


### 하나의 서버로 통합
- 도서에서는 RESTful의 제약 조건을 지키기 위해서 클라이언트-서버를 명확하게 분리하였다. 게시판 view를 보여주는 서버와 REST api를 제공하는 서버가 2개가 실행된다. 즉 @Controller 서버와 @RestController 서버 2개가 동시에 실행된다. 
- 하지만 본 프로젝트에서는 소규모 프로젝트로서 모든 브라우저와 모바일 기기 환경을 대응할 수 없기에, 단일 서버로 개발한다. 즉 @Controller와 @RestController를 하나의 서버에서 실행한다. 따라서 본 프로젝트는 REST를 지향했지만 Restful 하다고 할 수 없다.


### JQuery 지양, vanilla javascrip 지향
- JQuery 사용을 지양하고 vanilla javascript를 지향한다. 분명 jquery는 훌륭하고 많이 사용되는 라이브러리로서 웹 개발에 빠질 수 없지만 현재 트렌드와 맞지 않는다. ajax 사용과 이외 특별한 경우에만 JQuery를 사용할 것이다. 

출처: <https://marshall-ku.com/web/%EC%99%9C-%EB%B8%94%EB%A1%9C%EA%B7%B8%EC%97%90-vanilla-js%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%96%88%EB%82%98>


### 'read.html' 페이지 추가
- 게시글을 읽는 'read.html' 페이지를 생성하였다.



### form 태그 사용
- form 태그를 사용한다. 도서에서는 form 태그를 사용하지 않는다. 이 또한 개발 방식의 차이라고 생각한다. ajax 통신을 위한 JSON 객체를 생성할 때 필요한 반복되는 변수 초기화 코드는 비효율적이라고 생각하여 form 태그를 사용하였다. 
- form 태그를 사용하는 경우 github에서 제공하는 js 플러그인의 serializeObject() 함수를 사용하여 반복되는 변수 초기화 코드를 제거하였다. 

출처: <https://www.leafcats.com/28>
<https://kingbbode.tistory.com/28>
<https://github.com/macek/jquery-serialize-object>


### HTML id, name, class 속성 구분
- HTML id, name, class 속성은 각 목적에 따라 사용이 다르다. id 속성은 Element를 구분하는 고유한 식별자로서 태그를 변경하기 위해서 사용된다. name 속성은 form 이벤트 발생 시 서버로 데이터를 전송하기 위한 식별자로 사용된다. '처음 배우는 스프링 부트 2' 도서에서는 이를 구분하지 않고 id 속성만 사용한다. input 태그, textarea와 같이 데이터를 전송하는 태그는 id 대신 name을 사용하였다.

출처: <https://hashcode.co.kr/questions/7049/id-name-class-%EC%86%8D%EC%84%B1%EC%97%90-%EB%8C%80%ED%95%9C-%EC%82%AC%EC%9A%A9%EB%B2%95%EC%9D%B4-%EA%B6%81%EA%B8%88%ED%95%A9%EB%8B%88%EB%8B%A4>


### HTML id, name 명명규칙 변경
- HTML id와 name의 명명규칙은 각 회사마다 다르게 정의하였기에 정답이 없다.(대표적으로 NHN은 snake 표기법을 사용하고 다음에서는 camlecase를 사용한다.) 도서에서는 HTML id가 snake case를 사용하고 있다.(created_date) 하지만 serializeObject()를 사용하기 위해서는 자바의 필드명과 자바스크립트의 변수명이 같아야 하므로 HTML id와 name 명명규칙으로 camelcase로 적용하였다.(createdDate)


### 'list.html' 게시글 생성일자로 내림차순 
- 도서에서는 가장 최근에 작성한 게시글이 게시판의 가장 마지막에 페이지에서 조회된다(idx로 오름차순). 따라서 게시글 생성일자로 내림차순하도록 변경하였다.


## 프로젝트 구조
- 전체적인 프로젝트 구조는 다음과 같다. 각 파일들을 간략하게 설명하도록 하겠다.

![image](/assets/images/2020-05-13-Project Lab4/image1.png)



## Domian 계층


### PostStatus.java
- 게시판의 상태를 정의한 enum 파일이다. 
```
/lab/src/main/java/kr/ac/univ/lab/domain/enums/PostStatus.java
```

``` java
package kr.ac.univ.lab.domain.enums;

public enum PostStatus {
	active("active"), 
	inactive("inactive"),
	notice("notice");

	private String status;

	private PostStatus(String status) {
		this.status = status;
	}

	public String getStatus() {
		return this.status;
	}
}
```

### NewsBoard.java
- 게시판 정보가 정의된 파일이다. 
```
/lab/src/main/java/kr/ac/univ/lab/domain/NoticeBoard.java
```
``` java
package kr.ac.univ.lab.domain;

import java.time.LocalDateTime;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.EnumType;
import javax.persistence.Enumerated;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

import kr.ac.univ.lab.domain.enums.PostStatus;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
@Entity
@Table
public class NoticeBoard {
	@Id
	@Column
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long idx;

	@Column
	private String title;

	@Column
	private String content;

	@Column
	@Enumerated(EnumType.STRING)
	private PostStatus postStatus;
	
	@Column
	private Long viewCount;
	
	@Column()
	private LocalDateTime createdDate;
	
	@Column
	private LocalDateTime updatedDate;

	@Builder
	public NoticeBoard(String title, String content, PostStatus postStatus, Long viewCount, LocalDateTime createdDate, LocalDateTime updatedDate) {
		this.title = title;
		this.content = content;
		this.viewCount = viewCount;
		this.postStatus = postStatus;
		this.createdDate = createdDate;
		this.updatedDate = updatedDate;
	}
	
	public void setCreatedDateNow() {
        this.createdDate = LocalDateTime.now();
    }
    
    public void update(NoticeBoard noticeBoard) {
    	this.title = noticeBoard.getTitle();
		this.content = noticeBoard.getContent();
		this.postStatus = noticeBoard.getPostStatus();
		this.updatedDate = LocalDateTime.now();
    }
}
```


## Repository 계층


### NoticeBoard.java
- DB에 접근하는 인터페이스가 정의된 파일이다. 
```
/lab/src/main/java/kr/ac/univ/lab/domain/NoticeBoard.java
```
```java
package kr.ac.univ.lab.repository;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import kr.ac.univ.lab.domain.NoticeBoard;

@Repository
public interface NoticeBoardRepository extends JpaRepository<NoticeBoard, Long> {
	
}
```



## Service 계층


### NoticeBoardService.java
- Business Logic(데이터 처리)을 담당하는 파일이다.
```
/lab/src/main/java/kr/ac/univ/lab/service/NoticeBoardService.java
```
```java
package kr.ac.univ.lab.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Service;

import kr.ac.univ.lab.domain.NewsBoard;
import kr.ac.univ.lab.repository.NewsBoardRepository;

@Service
public class NewsBoardService {
	@Autowired
    private NewsBoardRepository newsBoardRepository;

    public Page<NewsBoard> findNewsBoardList(Pageable pageable) {
        pageable = PageRequest.of(pageable.getPageNumber() <= 0 ? 0 : pageable.getPageNumber() - 1, pageable.getPageSize());
        return newsBoardRepository.findAll(pageable);
    }

    public NewsBoard findNewsBoardByIdx(Long idx) {
        return newsBoardRepository.findById(idx).orElse(new NewsBoard());
    }
    
    public void insertNewsBoard(NewsBoard newsBoard) {
    	newsBoardRepository.save(newsBoard);
    }
    
    public NewsBoard getNewsBoardByIdx(Long idx) {
    	return newsBoardRepository.getOne(idx) ;
    }

    public void deleteNewsBoardById(Long idx) {
    	newsBoardRepository.deleteById(idx) ;
    }
    
}
```



## Controller 계층


### NewsBoardController.java
- URI 요청에 따른 view를 매핑하는 파일이다.
```
/lab/src/main/java/kr/ac/univ/lab/controller/NoticeBoardController.java
```
``` java
package kr.ac.univ.lab.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Pageable;
import org.springframework.data.web.PageableDefault;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

import kr.ac.univ.lab.service.NoticeBoardService;

@Controller
@RequestMapping("/NoticeBoard")
public class NoticeBoardController {
	@Autowired
	private NoticeBoardService noticeBoardService;

	// Read
	@GetMapping({ "", "/" })
	public String noticeBoardRead(@RequestParam(value = "idx", defaultValue = "0") Long idx, Model model) {
		model.addAttribute("noticeBoard", noticeBoardService.findNoticeBoardByIdx(idx));
		
		return "/noticeBoard/read";
	}

	// Form Update
	@GetMapping("/form{idx}")
	public String noticeBoardForm(@RequestParam(value = "idx", defaultValue = "0") Long idx, Model model) {
		model.addAttribute("noticeBoard", noticeBoardService.findNoticeBoardByIdx(idx));
		
		return "/noticeBoard/form";
	}
	
	// List
	@GetMapping("/list")
	public String noticeBoardList(@PageableDefault Pageable pageable, Model model) {
		model.addAttribute("noticeBoardList", noticeBoardService.findNoticeBoardList(pageable));

		return "/noticeBoard/list";
	}
}
```


### NewsBoardRestController.java
- ajax 요청에 따른 데이터를 처리하는 파일이다.
```
/lab/src/main/java/kr/ac/univ/lab/controller/NoticeBoardRestController.java
```
```java
package kr.ac.univ.lab.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.web.PageableDefault;
import org.springframework.hateoas.PagedModel;
import org.springframework.hateoas.PagedModel.PageMetadata;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import kr.ac.univ.lab.domain.NoticeBoard;
import kr.ac.univ.lab.service.NoticeBoardService;

@RestController
@RequestMapping("/api/NoticeBoards")
public class NoticeBoardRestController {
	@Autowired
	private NoticeBoardService noticeBoardSerive;

    @PostMapping
    public ResponseEntity<?> postNoticeBoard(@RequestBody NoticeBoard noticeBoard) {
        //valid 체크
    	noticeBoard.setCreatedDateNow();
        noticeBoardSerive.insertNoticeBoard(noticeBoard);
        
        return new ResponseEntity<>("{}", HttpStatus.CREATED);
    }

    @PutMapping("/{idx}")
    public ResponseEntity<?> putNoticeBoard(@PathVariable("idx")Long idx, @RequestBody NoticeBoard noticeBoard) {
        //valid 체크
        NoticeBoard persistNoticeBoard = noticeBoardSerive.getNoticeBoardByIdx(idx) ;
        persistNoticeBoard.update(noticeBoard);
        noticeBoardSerive.insertNoticeBoard(persistNoticeBoard);
        
        return new ResponseEntity<>("{}", HttpStatus.OK);
    }

    @DeleteMapping("/{idx}")
    public ResponseEntity<?> deleteNoticeBoard(@PathVariable("idx")Long idx) {
        //valid 체크
    	noticeBoardSerive.deleteNoticeBoardById(idx);
    	
        return new ResponseEntity<>("{}", HttpStatus.OK);
    }
}
```



## Spring boot 엔트리 포인트


### LabApplication.java
- CommandLineRunner는 서버 구동 시점에 초기화 작업을 수행한다. 샘플 데이터 200개를 DB에 삽입한다. 
```
/lab/src/main/java/kr/ac/univ/lab/LabApplication.java
```
```java
package kr.ac.univ.lab;

import java.time.LocalDateTime;
import java.util.stream.IntStream;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import kr.ac.univ.lab.domain.NoticeBoard;
import kr.ac.univ.lab.domain.enums.PostStatus;
import kr.ac.univ.lab.repository.NoticeBoardRepository;

@RestController
@SpringBootApplication
public class LabApplication {	
	public static void main(String[] args) {
		SpringApplication.run(LabApplication.class, args);
	}

	@GetMapping("/")
	public String Home() {
		
		return "Hello World";
	}

	@Bean
	public CommandLineRunner runner(NoticeBoardRepository noticeBoardRepository) {
		return (args) -> {
			IntStream.rangeClosed(1, 200).forEach(index -> 
				noticeBoardRepository.save(NoticeBoard.builder()
					.title("게시글" + index)
					.content("컨텐츠")
					.viewCount(0L)
					.postStatus(PostStatus.active)
					.createdDate(LocalDateTime.now())
					.updatedDate(LocalDateTime.now()).build()));
		};
	}
}
```



## View 계층


### header.html
- view의 상단 레이아웃을 구성한다.
```
src/main/resources/templates/layout/header.html
```
```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<!--header-->
<div th:fragment="header">
    <nav class="navbar navbar-default navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
                    <span class="sr-only">Toggle navigation</span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                <a href="/" class="navbar-brand" style="text-decoration:none;">
                    <img th:src="@{/images/spring_boot_gray.png}" class="img-rounded" style="display:inline-block;height:100%;margin-right:5px" />
                    <span style="vertical-align:middle;">SpringBoot Community Web</span>
                </a>
            </div>

            <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
                <ul class="nav navbar-nav navbar-right">
                    <li>
                        <a href="/logout">LOGOUT</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
</div>
<!--/header-->
</html>
```


### footer.html
- view의 하단 레이아웃을 구성한다.
```
src/main/resources/templates/layout/footer.html
```
```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<!--footer-->
<div th:fragment="footer">
    <footer class="footer">
        <div class="container">
            <p>Copyright 2017 young891221. All Right Reserved. Designed by ssosso</p>
        </div>
    </footer>
</div>
<!--/footer-->
</html>
```


### list.html
- 게시글 리스트를 보여주는 파일이다. 
```
/lab/src/main/resources/templates/noticeBoard/list.html
```
```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
<title>NoticeBoard List</title>

<link rel="stylesheet" th:href="@{/css/base.css}" />
<link rel="stylesheet" th:href="@{/css/bootstrap.min.css}" />

</head>
<body>
	<!-- header -->
	<div th:replace="layout/header::header"></div>

	<div class="container">
		<div class="page-header">
			<h1>게시글 목록</h1>
		</div>
		<div class="pull-right" style="width: 100px; margin: 10px 0;">
			<a href="/NoticeBoard/form" class="btn btn-primary btn-block">등록</a>
		</div>
		<br /> <br /> <br />
		<div id="mainHide">
			<table class="table table-hover">
				<thead>
					<tr>
						<th class="col-md-1">idx</th>
						<th class="col-md-4">제목</th>
						<th class="col-md-2">글쓴이</th>
						<th class="col-md-2">작성 날짜</th>
						<th class="col-md-2">수정 날짜</th>
						<th class="col-md-2">게시 상태</th>
					</tr>
				</thead>
				<tbody>
					<tr th:each="noticeBoard : ${noticeBoardList}">
						<td th:text="${noticeBoard.idx}"></td>
						<td><a th:href="'/NoticeBoard?idx='+${noticeBoard.idx}" th:text="${noticeBoard.title}"></a></td>
						<td>추가 예정</td>
						<td th:text="${#temporals.format(noticeBoard.createdDate,'yyyy-MM-dd HH:mm')}"></td>
						<td th:text="${#temporals.format(noticeBoard.updatedDate,'yyyy-MM-dd HH:mm')}"></td>
						
						<td th:if="${noticeBoard.postStatus?.name() == 'active'}" th:text="게시중"></td>
						<td th:if="${noticeBoard.postStatus?.name() == 'inactive'}" th:text="비게시중"></td>
						<td th:if="${noticeBoard.postStatus?.name() == 'notice'}" th:text="공지사항"></td>
					</tr>
				</tbody>
			</table>
		</div>
		<br>

		<!-- Pagination -->
		<nav aria-label="Page navigation" style="text-align: center;">
			<ul class="pagination" th:with="startNumber=${T(Math).floor(noticeBoardList.number/10)}*10+1, endNumber=(${noticeBoardList.totalPages} > ${startNumber}+9) ? ${startNumber}+9 : ${noticeBoardList.totalPages}">
				<li>
					<a aria-label="Previous" href="/NoticeBoard/list?page=1">&laquo;</a>
				</li>
				<li th:style="${noticeBoardList.first} ? 'display:none'">
					<a th:href="@{/NoticeBoard/list(page=${noticeBoardList.number})}">&lsaquo;</a>
				</li>

				<li th:each="page :${#numbers.sequence(startNumber, endNumber)}" th:class="(${page} == ${noticeBoardList.number}+1) ? 'active'">
					<a th:href="@{/NoticeBoard/list(page=${page})}" th:text="${page}"><span class="sr-only"></span></a>
				</li>

				<li th:style="${noticeBoardList.last} ? 'display:none'">
					<a th:href="@{/NoticeBoard/list(page=${noticeBoardList.number}+2)}">&rsaquo;</a>
				</li>
				<li>
					<a aria-label="Next" th:href="@{/NoticeBoard/list(page=${noticeBoardList.totalPages})}">&raquo;</a>
				</li>
			</ul>
		</nav>
		<!-- /Pagination -->
		
	</div>

	<!-- footer -->
	<div th:replace="layout/footer::footer"></div>
</body>
</html>
```


### read.html
- 게시글 내용을 보여주는 파일이다. 
```
/lab/src/main/resources/templates/noticeBoard/read.html
```
```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
<title>NoticeBoard Read</title>

<link rel="stylesheet" th:href="@{/css/base.css}" />
<link rel="stylesheet" th:href="@{/css/bootstrap.min.css}" />

</head>
<body>
	<!-- header -->
    <div th:replace="layout/header::header"></div>

    <div class="container">
    	<form id="form" th:object="${noticeBoard}" action="#"  >
    
        <div class="page-header">
            <h1>게시글 읽기</h1>
        </div>
        <br/>
        <table class="table">
            <tr>
                <th style="padding:13px 0 0 15px">게시판 선택</th>
                <td>
                    <div class="pull-left">
                        <span class="col-md-1 form-control input-sm" th:if="*{postStatus?.name() == 'active'}" th:text="게시중"> </span>
                        <span class="col-md-1 form-control input-sm" th:if="*{postStatus?.name() == 'inactive'}" th:text="비게시중"> </span>
                        <span class="col-md-1 form-control input-sm" th:if="*{postStatus?.name() == 'notice'}" th:text="공지사항"> </span>
                    </div>
                </td>
            </tr>
            <tr>
                <th style="padding:13px 0 0 15px;">생성날짜</th>
                <td><input type="text" class="col-md-1 form-control input-sm" readonly="readonly" th:value="*{#temporals.format(createdDate,'yyyy-MM-dd HH:mm')}"/></td>
            </tr>
            <tr>
                <th style="padding:13px 0 0 15px;">수정날짜</th>
                <td><input type="text" class="col-md-1 form-control input-sm" readonly="readonly" th:value="*{#temporals.format(updatedDate,'yyyy-MM-dd HH:mm')}"/></td>
            </tr>
            <tr>
                <th style="padding:13px 0 0 15px;">제목</th>
                <td><span id="title" class="col-md-1 form-control input-sm" th:text="*{title}"></span></td>
            </tr>
            <tr>
                <th style="padding:13px 0 0 15px;">내용</th>
                <td><span id="content" class="col-md-1 form-control input-sm" style="height: 200px;" th:text="*{content}"></span></td>
            </tr>
            <tr>
                <td></td>
                <td></td>
            </tr>
        </table>
        
        <div class="pull-left">
            <a href="/NoticeBoard/list" class="btn btn-default">목록으로</a>
        </div>
        <div class="pull-right">
        	<a th:href="'/NoticeBoard/form?idx='+*{idx}" class="btn btn-info"> 수정</a>
            <button type="button" class="btn btn-danger" id="delete">삭제</button>
        </div>
        
        <!-- input type="hidden" -->
		<input type="hidden" name="idx" th:value="*{idx}"/>
        
        </form>
    </div>
    
	<!-- footer -->
    <div th:replace="layout/footer::footer"></div>
	
    <!-- script file -->
    <script th:src="@{/js/jquery.min.js}"></script>
    <script>
    	$('#delete').click(function () {
        	if(confirm("삭제 하시겠습니까?")) { 
        		$.ajax({
                    url: "/api/NoticeBoards/" + document.getElementsByName('idx')[0].value,
                    type: "DELETE",
                    dataType: "text",
                    contentType: "application/json",
                    
                    success: function () {
                        alert('삭제 성공!');
                        location.href = '/NoticeBoard/list';
                    },
                    error: function () {
                        alert('삭제 실패!');
                    }
                });
        	}
        });
    </script>
</body>
</html>
```


### form.html
- 게시글 등록 및 수정 페이지를 보여주는 파일이다. 
```
/lab/src/main/resources/templates/noticeBoard/form.html
```
```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">

<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
<title>NoticeBoard Form</title>

<link rel="stylesheet" th:href="@{/css/base.css}" />
<link rel="stylesheet" th:href="@{/css/bootstrap.min.css}" />

</head>
<body>
	<!-- header -->
    <div th:replace="layout/header::header"></div>
    
    <div class="container">
    	<form id="form" th:object="${noticeBoard}" action="#"  >
    
        <div class="page-header">
            <h1 th:if="!*{idx}">게시글 등록</h1>
            <h1 th:if="*{idx}">게시글 수정</h1>
        </div>
        <br/>
        <table class="table">
            <tr>
                <th style="padding:13px 0 0 15px">게시판 선택</th>
                <td>
                    <div class="pull-left">
                        <select name="postStatus" class="form-control input-sm">
                            <option>--분류--</option>
                            <option th:value="active" th:selected="*{postStatus?.name() == 'active'}">게시중</option>
                            <option th:value="inactive" th:selected="*{postStatus?.name() == 'inactive'}">비게시중</option>
                            <option th:value="notice" th:selected="*{postStatus?.name() == 'notice'}">공지사항</option>
                        </select>
                    </div>
                </td>
            </tr>
            <tr th:if="*{idx}">
                <th style="padding:13px 0 0 15px;">생성 날짜</th>
                <td><input type="text" class="col-md-1 form-control input-sm" readonly="readonly" th:value="*{#temporals.format(createdDate,'yyyy-MM-dd HH:mm')}"/></td>
            </tr>
            <tr>
                <th style="padding:13px 0 0 15px;">제목</th>
                <td><input type="text" name="title" class="col-md-1 form-control input-sm" th:value="*{title}"/></td>
            </tr>
            <tr>
                <th style="padding:13px 0 0 15px;">내용</th>
                <td><textarea name="content" class="col-md-1 form-control input-sm" maxlength="140" rows="7" style="height: 250px;" th:text="*{content}"></textarea></td>
            </tr>
            <tr>
                <td></td>
                <td></td>
            </tr>
            
        </table>
        
        <div class="pull-left">
            <a href="/NoticeBoard/list" class="btn btn-default">목록으로</a>
        </div>
        <div class="pull-right">
            <button th:if="!*{idx}" id="insert" type="button" class="btn btn-primary">저장</button>
            <button th:if="*{idx}" id="update" type="button" class="btn btn-info">수정완료</button>
        </div>
        
        <!-- input type="hidden" -->
		<input type="hidden" name="idx" th:value="*{idx}"/>
	    <input type="hidden" name="createdDate" th:value="*{createdDate}"/>
	    
	    </form>
    </div>
    
	<!-- footer -->
    <div th:replace="layout/footer::footer"></div>

	<!-- script file -->
    <script th:src="@{/js/jquery.min.js}"></script>
    <script th:src="@{/js/jquery.serialize-object.min.js}"></script>
    
	<!-- javascript -->   
    <script th:if="!${noticeBoard?.idx}">
   		$('#insert').click(function () {
	       	var jsonData = $("#form").serializeObject();
	       
            $.ajax({
				url: "/api/NoticeBoards",
				type: "POST",
                data: JSON.stringify(jsonData),
				dataType: "text",
				contentType: "application/json",
				
                success: function () {
                    alert('저장 성공!');
                    location.href = '/NoticeBoard/list';
              	},
                error: function () {
                    alert('저장 실패!');
               	}
			});
   		});
    </script>
    <script th:if="${noticeBoard?.idx}">
    	$('#update').click(function () {
        	var jsonData = $("#form").serializeObject();
        	
            $.ajax({
                url: "/api/NoticeBoards/" + document.getElementsByName('idx')[0].value,
                type: "PUT",
                data: JSON.stringify(jsonData),
                dataType: "text",
                contentType: "application/json",
                
                success: function () {
                    alert('수정 성공!');
                    location.href = '/NoticeBoard?idx=' + document.getElementsByName('idx')[0].value;
                },
                error: function () {
                    alert('수정 실패!');
                }
            });
    	});
    </script>
</body>
</html>
```



## static 폴더
- static 폴더 내의 파일들은 bootstrap, jquery, image 파일들로 구성된다. 
- 해당 파일들은 gituhb 링크로 대신하며, 프로젝트에 폴더를 생성하고 해당 파일들을 복사 붙여넣기하면 된다.

- src/main/resources/static/css/base.css
- src/main/resources/static/css/bootstrap.min.css
- src/main/resources/static/css/bootstrap.min.css.map
- src/main/resources/static/images/*
- src/main/resources/static/js/jquery.min.js 
- src/main/resources/static/js/jquery.serialize-object.min.js

- github: https://github.com/scribnote5/lab
- github commit: https://github.com/scribnote5/lab/commit/25823d6f4c8b10426da173d327ceabf4421c8721



## 구현 결과
- 프로젝트를 실행(단축키 F11) 후 브라우저로 'http://localhost:8080/NoticeBoard/list'에 접속하면 하단 그림과 같이 게시판을 확인할 수 있다.

![image](/assets/images/2020-05-13-Project Lab4/image2.png)