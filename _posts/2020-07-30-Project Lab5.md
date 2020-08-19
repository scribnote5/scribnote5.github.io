---
title: "Project Lab 5. 게시판 개발(QueryDsl, 조회수 개발) - 2"
excerpt: "QueryDsl를 사용하여 게시판 조회수 증가 기능 개발 과정을 소개한다."

categories:
  - Web
  - Project Lab

last_modified_at: 2020-07-30
---
- QueryDsl를 사용하여 게시판 조회수 증가 기능 개발 과정을 소개한다.
- github: <https://github.com/scribnote5/lab>
- github commit: <https://github.com/scribnote5/lab/commit/f4397007c39e0844c2c05822a640561636b14631>



## QueryDsl를 사용하는 이유
- QueryDsl을 사용하는 가장 큰 이유는 JPA 에서 제공하지 않는 동적 쿼리를 수행할 수 있기 때문이다.
- 문자열이 아닌 자바 소스 코드로 쿼리를 작성하기에 컴파일 시점에 문법 오류를 발견할 수 있다.
- DB 종류와 관계없이 하나의 통일된 문법으로 쿼리를 작성할 수 있다.
- IDE에서 제공하는 소스 코드 자동 완성 기능을 활용 할 수 있다.

출처: <https://ict-nroo.tistory.com/117>



## QueryDsl 적용
- QueryDsl을 적용하는 방법은 크게 두 가지가 있다. 


### 1. QueryDsl plugins 사용
- 'com.ewerk.gradle.plugins.querydsl' 플러그인을 사용하는 방법이다. 
- Gradle 버전이 4.6 이후일 때 적용 방법이 다르고, 2018년 이후 플러그인이 더이상 업데이트 되지 않고 있다.
- Q도메인을 IntelliJ가 자동으로 인식하지 못하여 프로젝트에서 수동으로 Q도메인 경로를 지정해야 하는 번거로움이 있다. 

출처: <https://juneyr.dev/2019-07-11/querydsl-config>

- 해당 방법을 2020.01IntelliJ에 적용한 경우 gradle build 할 때 'error: package com.querydsl.core.types does not exist' Q도메인이 생성되지만 에러가 발생한다.
- 해당 에러는 프로젝트 빌드할 때 중간에 멈추게 만들어 devtools를 통한 auto 빌드를 어렵게 만든다. 하지만 에러를 해결하지 못하였다.

출처: <https://www.inflearn.com/questions/23530>


### 2. Gradle annotationProcessor 사용 
- 기존 QueryDsl plugins 사용으로 발생하는 불편함과 에러를 해결하기 위한 방법을 찾던 도중 Gradle annotationProcessor로 QueryDsl을 사용하는 방법을 적용하였다.
- 해당 방법은 Gradle build 에러도 발생하지 않고 Q도메인 경로를 자동으로 인식한다.



## Q도메인 생성

```
build.gradle
```

```
project(":module-domain-core") {
   dependencies {
       compile project(":module-system-common")

       implementation("com.querydsl:querydsl-core")
       implementation("com.querydsl:querydsl-jpa")
   }
}

...

def queryDslProjects = [project(":module-domain-core")]
configure(queryDslProjects) {
   dependencies {

       annotationProcessor("com.querydsl:querydsl-apt:${dependencyManagement.importedProperties["querydsl.version"]}:jpa") // querydsl JPAAnnotationProcessor 사용 지정
       annotationProcessor("jakarta.persistence:jakarta.persistence-api") // java.lang.NoClassDefFoundError(javax.annotation.Entity) 발생 대응
       annotationProcessor("jakarta.annotation:jakarta.annotation-api") // java.lang.NoClassDefFoundError (javax.annotation.Generated) 발생 대응
   }

   // clean 태스크 실행시 QClass 삭제
   clean {
       delete file("src/main/generated") // intelliJ Annotation processor Q도메인 생성 위치
   }
}
```

- Gradle build를 수행하면 다음 이미지와 같이 프로젝트에서 Q도메인 경로를 자동으로 인식하고 Q도메인을 자동으로 생성한다.

![image](/assets/images/2020-07-30-Project Lab5/image1.png)

![image](/assets/images/2020-07-30-Project Lab5/image2.png)

출처: <http://honeymon.io/tech/2020/07/09/gradle-annotation-processor-with-querydsl.html>


## 조회수 기능 개발
- 게시글을 클릭하였을 때, 조회수 올라가는 기능을 QueryDsl로 개발하였다.

- QueryDsl을 프로젝트 어느 곳에서나 사용할 수 있도록 설정한다.

```
module-domain-core/kr/ac/univ/common/config/QueryDslConfig
```

```java
package kr.ac.univ.common.config;

import com.querydsl.jpa.impl.JPAQueryFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;

@Configuration
public class QueryDslConfig {
   @PersistenceContext
   private EntityManager entityManager;

   @Bean
   public JPAQueryFactory jpaQueryFactory() {
       return new JPAQueryFactory(entityManager);
   }
}
```

<br>
- Q도메인을 사용하여 다음과 같은 쿼리를 작성하였다.
- findByTitle: 제목으로 게시글을 검색한다.(테스트 용도로 구현)
- updateViewCountById: 게시글 조회수를 1 증가시킨다.

```
module-domain-core/kr/ac/univ/noticeBoard/repository/NoticeBoardRepositoryImpl
```

```java
package kr.ac.univ.noticeBoard.repository;

import com.querydsl.jpa.impl.JPAQueryFactory;
import kr.ac.univ.noticeBoard.domain.NoticeBoard;
import kr.ac.univ.noticeBoard.domain.QNoticeBoard;
import org.springframework.data.jpa.repository.support.QuerydslRepositorySupport;
import org.springframework.stereotype.Repository;

import javax.transaction.Transactional;
import java.util.List;

@Repository
@Transactional
public class NoticeBoardRepositoryImpl extends QuerydslRepositorySupport {
   private final JPAQueryFactory queryFactory;

   public NoticeBoardRepositoryImpl(JPAQueryFactory queryFactory ) {
       super(NoticeBoard.class);
       this.queryFactory = queryFactory;
   }

   public List<NoticeBoard> findByTitle(String title) {
       QNoticeBoard noticeBoard = QNoticeBoard.noticeBoard;
       /*
        * SELECT *
        *   FROM notice_board
        *  WHERE title = 'title'
        */
       return queryFactory
               .selectFrom(noticeBoard)
               .where(noticeBoard.title.eq(title))
               .fetch();
   }

   public long updateViewCountById(Long idx) {
       QNoticeBoard noticeBoard = QNoticeBoard.noticeBoard;
       /*
        * UPDATE notice_board
        *    SET view_count = view_count + 1
        *  WHERE id = 'id';
        */
       return queryFactory
               .update(noticeBoard)
               .set(noticeBoard.viewCount, noticeBoard.viewCount.add(1))
               .where(noticeBoard.idx.eq(idx))
               .execute();
   }

}
```

<br>
- 200개의 데이터를 삽입한 다음, QueryDsl 작성한 findByTitle과 updateViewCountById 쿼리를 테스트 한다.

```
module-app-web/src/test/java/kr/ac/univ/QueryDslTest
```

```java
package kr.ac.univ;

import kr.ac.univ.common.domain.enums.ActiveStatus;
import kr.ac.univ.noticeBoard.domain.NoticeBoard;
import kr.ac.univ.noticeBoard.repository.NoticeBoardRepository;
import kr.ac.univ.noticeBoard.repository.NoticeBoardRepositoryImpl;
import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import java.util.List;
import java.util.Optional;
import java.util.stream.IntStream;

import static org.junit.jupiter.api.Assertions.assertEquals;

@Slf4j
@SpringBootTest
@EnableAutoConfiguration
@ExtendWith(SpringExtension.class)
public class QueryDslTest {
   @Autowired
   NoticeBoardRepository noticeBoardRepository;

   @Autowired
   NoticeBoardRepositoryImpl noticeBoardRepositoryImpl;

   @BeforeEach
   public void init() {
       IntStream.rangeClosed(1, 200)
               .forEach(index -> noticeBoardRepository.save(NoticeBoard.builder()
                       .title("게시글" + index)
                       .content("컨텐츠")
                       .viewCount(0L)
                       .activeStatus(ActiveStatus.ACTIVE)
                       .build()));
   }

   @Test
   @DisplayName("QueryDsl로 구현한 findByTitle 테스트")
   public void Test() {
       List<NoticeBoard> list = noticeBoardRepositoryImpl.findByTitle("게시글10");

       for (NoticeBoard noticeboard : list) {
           assertEquals(noticeboard.getTitle(), "게시글10");
       }
   }

   @Test
   @DisplayName("QueryDsl로 구현한 updateViewCountByIdx 테스트")
   public void Test2() {
       // update된 column 개수 반환
       Long cnt = noticeBoardRepositoryImpl.updateViewCountById(1L);
       assertEquals(cnt, 1);
   }

   @Test
   @DisplayName("JPA findById를 사용한 viewCount 테스트")
   public void Test3() {
       Optional<NoticeBoard> optionalNoticeBoard = noticeBoardRepository.findById(1L);
       NoticeBoard noticeBoard = null;

       if (optionalNoticeBoard.isPresent()) {
           noticeBoard = optionalNoticeBoard.get();
       }

       // idx 1인 column을 조회한다.
       assertEquals(noticeBoard.getIdx(), 1L);
       assertEquals(noticeBoard.getViewCount(), 1L);
   }
}
```

<br>
- Service 계층에 게시글을 읽을 때 조회수가 증가하는 메소드를 추가하였다.
- QueryDsl로 구현한 NoticeBoardReposityImpl 클래스를 생성자 주입으로 의존관계를 등록하였다.
- findNoticeBoardByIdx(게시글을 읽는 경우)에 updateViewCountById(게시글 조회수를 1 증가) 메소드에 의하여 조회수가 증가한다.

```
module-domain-core/src/main/java/kr/ac/univ/noticeBoard/service/NoticeBoardService
```

```java
@Service
public class NoticeBoardService {
    private final NoticeBoardRepository noticeBoardRepository;
    private final NoticeBoardRepositoryImpl noticeBoardRepositoryImpl;

    public NoticeBoardService(NoticeBoardRepository noticeBoardRepository, NoticeBoardRepositoryImpl noticeBoardRepositoryImpl) {
        this.noticeBoardRepository = noticeBoardRepository;
        this.noticeBoardRepositoryImpl = noticeBoardRepositoryImpl;
    }

    ...

    public NoticeBoardDto findNoticeBoardByIdx(Long idx) {
        noticeBoardRepositoryImpl.updateViewCountById(idx);

        return NoticeBoardMapper.INSTANCE.toDto(noticeBoardRepository.findById(idx).orElse(new NoticeBoard()));
    }
    
    ...
```



## 프로젝트 실행 및 결과
- 다음과 같이 게시글을 클릭하는 경우 조회수가 1씩 증가하는 것을 확인할 수 있다. 

![image](/assets/images/2020-07-30-Project Lab5/image3.png)