---
title: "Project Lab 9. 게시판 개발(검색) - 6"
excerpt: "게시글 검색 기능 개발 과정을 소개한다."
categories:
  - Web
  - Project Lab
last_modified_at: 2020-08-13
layout: post
---
- 게시글 검색 기능 개발 과정을 소개한다.



- github: <https://github.com/scribnote5/lab>
- github commit: <https://github.com/scribnote5/lab/commit/b806ca1160cca021c5e1abdc2ad012686965dee8>

- 최신 프로젝트 코드와 형상이 다를 수 있습니다. 게시글 코드는 참고만 하시되, 최신 코드는 github에서 확인 부탁드립니다.



## 페이징 처리 방법: JPA vs DataTables?
- 게시판의 페이징 처리 방법으로 Spring JPA 또는 Javascript DataTables 라이브러리를 사용할 수 있다.
- Spring JPA는 페이징 처리 기능을 제공한다. 반면 Javascript DataTables 라이브러리는 페이징 처리 뿐만 아니라 다양한 기능(복잡한 검색 기능, ajax를 사용한 페이징 처리, 모바일 지원 등)들을 간단한 설정과 최소한의 소스 코드 추가를 통하여 사용할 수 있다.
- 게시판의 다양한 기능들을 직접 구현하지 않고 빠르게 사용하기를 원한다면, Javascript DataTables 라이브러리를 사용하는 것을 적극 추천한다. 본 프로젝트에서는Spring JPA로 페이징 처리를 구현하였다.(DataTables 라이브러리를 너무 늦게 알게됨)

출처: <https://datatables.net/><br>
<https://medium.com/@gustavo.ponce.ch/spring-boot-jquery-datatables-a2e816e2b5e9>



## 검색 기능 설계
- 본 프로젝트에서는 Spring JPA의 메소드 이름으로 쿼리 작성하는 방법으로 기본적인 검색 기능을 구현하였다.
- 검색 기능은 검색 유형(searchType)과 검색어(keyword)로 구성되며, 검색 조건이 여러 개인 복잡한 검색 기능은 다루지 않는다. 복잡한 검색 기능은 추후 기회가 되면 개발할 예정이다.



## DTO 설계
- 검색 유형과 검색어를 저장하는 공통 DTO다.

```
module-domain-core/src/main/java/kr/ac/univ/common/dto/SearchDto.java
```

```java
package kr.ac.univ.common.dto;

import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import lombok.ToString;

@Getter
@Setter
@NoArgsConstructor
@ToString
public class SearchDto {
   private String searchType = "";
   private String keyword = "";
}
```



## 비즈니스 로직
- DB에 접근하여 데이터를 조회하는 메소드다.
- findAllByTitleContaining: 제목에 키워드가 포함된 게시글을 모두 검색한다.
- findAllByContentContaining: 내용에 키워드가 포함된 게시글을 모두 검색한다.
- findAllByCreatedByContaining: 작성자에 키워드가 포함된 게시글을 모두 검색한다.

```
module-domain-core/src/main/java/kr/ac/univ/noticeBoard/repository/NoticeBoardRepository.java
```

```java
package kr.ac.univ.noticeBoard.repository;

import kr.ac.univ.noticeBoard.domain.NoticeBoard;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface NoticeBoardRepository extends JpaRepository<NoticeBoard, Long> {
   Page<NoticeBoard> findAllByTitleContaining(Pageable pageable, String title);

   Page<NoticeBoard> findAllByContentContaining(Pageable pageable, String content);

   Page<NoticeBoard> findAllByCreatedByContaining(Pageable pageable, String memberId);
}
```

<br>
- 게시글의 리스트를 조회하는 findNoticeBoardList 메소드에 검색 기능을 추가하였다.
- searchDto의 searchType에 따라서 수행되는 메소드가 변경된다.
- searchType이 "TITLE"(제목)이면 제목에 키워드가 포함된 게시글을 모두 검색한다.

```
module-domain-core/src/main/java/kr/ac/univ/noticeBoard/service/NoticeBoardService.java
```

```java
...
public Page<NoticeBoardDto> findNoticeBoardList(Pageable pageable, SearchDto searchDto) {
   Page<NoticeBoard> noticeBoardList = null;
   Page<NoticeBoardDto> noticeBoardDtoList = null;

   pageable = PageRequest.of(pageable.getPageNumber() <= 0 ? 0 : pageable.getPageNumber() - 1, pageable.getPageSize(), Sort.Direction.DESC, "idx");

   switch (searchDto.getSearchType()) {
       case "TITLE":
           noticeBoardList = noticeBoardRepository.findAllByTitleContaining(pageable, searchDto.getKeyword());
           break;
       case "CONTENT":
           noticeBoardList = noticeBoardRepository.findAllByContentContaining(pageable, searchDto.getKeyword());
           break;
       case "ID":
           noticeBoardList = noticeBoardRepository.findAllByCreatedByContaining(pageable, searchDto.getKeyword());
           break;
       default:
           noticeBoardList = noticeBoardRepository.findAll(pageable);
           break;
   }

   noticeBoardDtoList = new PageImpl<NoticeBoardDto>(NoticeBoardMapper.INSTANCE.toDto(noticeBoardList.getContent()), pageable, noticeBoardList.getTotalElements());

   // NewIcon 판별
   for (NoticeBoardDto noticeBoardDto : noticeBoardDtoList) {
       // 추후 변경
       noticeBoardDto.setNewIcon(NewIconCheck.isNew(LocalDateTime.now()));
   }

   return noticeBoardDtoList;
}
...
```

<br>
- View에서 전달한 SearchDto(검색 관련 데이터)를 Service 계층으로 전달한다.

```
module-app-web/src/main/java/kr/ac/univ/controller/NoticeBoardController.java
```

```java
...

// List
@GetMapping("/list")
public String noticeBoardList(@PageableDefault Pageable pageable, SearchDto searchDto, Model model) {
   model.addAttribute("noticeBoardDtoList", noticeBoardService.findNoticeBoardList(pageable, searchDto));

   return "/noticeBoard/list";
}

...
```



## View
- 게시글 리스트 상단에 검색 UI를 추가하였다.
- 페이징 번호를 클릭할 때 URI 뒤에 검색 데이터를 함께 제공하여(쿼리 스트링), Controller에서 get 방식으로 검색 데이터를 받을 수 있도록 하였다.
- 기존 페이징 처리 소스 코드에서 문제점이 발견되었는데, 하단 이미지처럼 검색 결과가 하나도 없는 경우 페이징 번호가 '1'만 출력되어야 하지만 페이징 번호가 '1' '0' 두 개가 출력된다.
- 해당 오류를 수정하기 위해서 마지막 페이징 번호인 endNumber를 정하는 로직에, ${noticeBoardDtoList.totalPages} == 0 ? 1 : ${noticeBoardDtoList.totalPages} 소스 코드를 추가하였다. 검색 결과가 하나도 없는 경우, 즉 totalPages가 0인 경우 endNumber에 1을 대입하여 해결하였다.

![image](/assets/img/2020-08-13-Project Lab9/image1.png)

```
module-app-web/src/main/resources/templates/noticeBoard/list.html
```

```html
...
...
   <form name="form" id="form" th:object="${searchDto}" action="#">
       <div class="pull-right">
           <div class="pull-left" style="width: 100px; margin: 10px 10px;">
               <select name="searchType" th:field="*{searchType}" class="form-control input-sm">
                   <option th:value="TITLE">Title</option>
                   <option th:value="CONTENT">Content</option>
                   <option th:value="ID">ID</option>
               </select>
           </div>

           <div class="pull-left" style="width: 200px; margin: 10px 10px; float: left;">
               <input type="text" name="keyword" class="col-md-1 form-control input-sm"
                      th:value="${searchDto?.keyword}"/>
           </div>

           <div class="pull-left" style="width: 100px; margin: 10px 0; float:left;">
               <button id="search" type="button" class="btn btn-primary btn-block">Search</button>
           </div>
       </div>
...
   </form>
</div>

...

<script>
   $('#search').click(function () {
       document.form.action = '/notice-board/list';
       document.form.method = 'get';
       document.form.submit();
   });
</script>
```



## 프로젝트 실행 결과
- 검색 유형에 "TITLE" 검색어에 "12"를 입력하고 페이징 번호를 클릭한 경우 다음 이미지와 같이 페이징 번호, searchType 그리고 keyword가 포함된 쿼리 스트링을 확인할 수 있다.

![image](/assets/img/2020-08-13-Project Lab9/image2.png)

<br>
- 검색 결과, 제목에 "12"가 포함된 게시글 리스트가 출력된다.

![image](/assets/img/2020-08-13-Project Lab9/image3.png)
