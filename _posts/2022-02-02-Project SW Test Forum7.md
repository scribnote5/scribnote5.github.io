---
title: "Project SW Test Forum 7. CRUD 게시판 구현 및 오류 해결(벡엔드) - 2"
excerpt: "- Vue.js 3와 Spring boot로 CRUD 게시판 구현 및 오류 해결 과정을 소개한다.  "
categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2022-02-02
layout: post
---
- Vue.js 3와 Spring boot로 CRUD 게시판 구현 및 오류 해결 과정을 소개한다.
- github: <https://github.com/scribnote5/sw_test_forum>



## 테이블 조회할 때 비즈니스 로직에서 사용하는 컬럼만 조회
- 공지사항 목록 페이지에서 noticeRepository.findAll 메소드를 사용하면 모든 컬럼을 조회한다. 해당 페이지에서 사용하지 않는 컬럼 또한 조회하게 되며, 이는 불필요한 컴퓨터 자원을 낭비한다고 생각하였다.
- 따라서 QueryDSL를 사용하여, 비즈니스 로직에서 사용하는 컬럼만 조회하도록 변경하였다.

```
module-domain-core\src\main\java\com\suresoft\sw_test_forum\admin_page\notice\repository\NoticeRepositoryImpl.java
```

```java
/**
* 우선순위가 높은 리스트 조회
*
* @return
*/
public List<NoticeDto> findAllByHighPriorityAsc() {
   return queryFactory.select(
                   Projections.bean(
                           NoticeDto.class,
                           notice.idx,
                           notice.createdDate,
                           notice.createdByIdx,
                           notice.activeStatus,
                           notice.views,

                           notice.title,
                           notice.priority
                   )
           )
           .from(notice)
           .where(notice.priority.loe(5))
           .orderBy(notice.priority.asc())
           .fetch();
}
```

출처: <https://icarus8050.tistory.com/5>



## QueryDsl 조인
- QueryDsl에서 연관관계 없이 다른 엔티티과의 조인은 ‘join(엔티티).on(엔티티.키.eq(엔티티.키)’로 사용할 수 있다.

```
module-domain-core\src\main\java\com\suresoft\sw_test_forum\misra_c\misra_c\repository\MisraCRepositoryImpl.java
```

```java
/**
* 우선순위 낮은 리스트 조회
*
* @param pageable
* @param misraCSearchDto
* @return
*/
public PageImpl<MisraCDto> findAll(Pageable pageable, MisraCSearchDto misraCSearchDto) {
   JPQLQuery<MisraCDto> query = queryFactory.select(
                   Projections.bean(
                           MisraCDto.class,
                           misraC.idx,
                           misraC.createdDate,
                           misraC.createdByIdx,
                           misraC.activeStatus,
                           misraC.views,

                           misraC.title,
                           misraC.priority,
                           misraC.frequency,
                           hashTags.content.as("hashTags")
                   )
           )
           .from(misraC)
           .join(hashTags).on(misraC.hashTagsIdx.eq(hashTags.idx))
           .where(searchCondition(misraCSearchDto))
           .orderBy(misraC.idx.desc());

   long totalCount = query.fetchCount();
   List<MisraCDto> results = getQuerydsl().applyPagination(pageable, query).fetch();

   return new PageImpl<>(results, pageable, totalCount);
}
```

출처: <https://jojoldu.tistory.com/396><br>
<https://velog.io/@geunwoobaek/Query-Dsl-%ED%99%9C%EC%9A%A9>
