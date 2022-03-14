---
title: "Project Lab 5. 게시판 개발(QueryDsl, 조회수 개발) - 2"
excerpt: "QueryDsl를 사용한 게시판 조회수 개발 과정을 소개한다."
categories:
  - Web
  - Project Lab
last_modified_at: 2021-04-19
layout: post
---
- QueryDsl를 사용한 게시판 조회수 개발 과정을 소개한다.



- github: <https://github.com/scribnote5/lab>
- github commit: <https://github.com/scribnote5/lab/commit/f4397007c39e0844c2c05822a640561636b14631>

- 최신 프로젝트 코드와 형상이 다를 수 있습니다. 게시글 코드는 참고만 하시되, 최신 코드는 github에서 확인 부탁드립니다.



## QueryDsl를 사용하는 이유
- 1. QueryDsl을 사용하는 가장 큰 이유는 JPA 에서 제공하지 않는 동적 쿼리를 수행할 수 있기 때문이다.
- 2. 문자열이 아닌 자바 소스 코드로 쿼리를 작성하기에 컴파일 시점에 문법 오류를 발견할 수 있다.
- 3. DB 종류와 관계없이 하나의 통일된 문법으로 쿼리를 작성할 수 있다.
- 4. IDE에서 제공하는 소스 코드 자동 완성 기능을 활용 할 수 있다.

출처: <https://ict-nroo.tistory.com/117>



## QueryDsl 적용
- QueryDsl을 적용하는 방법은 크게 두 가지가 있다.


### 1. QueryDsl plugins 사용
- 'com.ewerk.gradle.plugins.querydsl' 플러그인을 사용하는 방법으로, <span style="color:red; font-weight: bold"> 여러 문제가 발생하기에 '2. Gradle annotationProcessor' 방법을 사용해야 한다.</span>
- Gradle 버전 4.6 이전과 이후일 때 적용 방법이 다르고, 2018년 이후 플러그인이 더이상 업데이트 되지 않고 있으며, Q Domain을 IntelliJ가 자동으로 인식하지 못하여 프로젝트에서 수동으로 Q Domain 경로를 지정해야 하는 번거로움이 있다.
- 해당 에러는 프로젝트 빌드할 때 중간에 멈추게 만들어 devtools를 통한 auto 빌드에 큰 불폄함을 초래한다. 하지만 에러 해결 방법을 찾지 못했다.

출처: <https://www.inflearn.com/questions/23530>


### 2. Gradle annotationProcessor 사용
- 기존 QueryDsl plugins 사용으로 발생하는 불편함과 에러를 해결하기 위한 방법을 찾던 도중 Gradle annotationProcessor로 QueryDsl을 사용하는 방법을 적용하였다.
- 해당 방법은 Gradle build 에러도 발생하지 않고 Q Domain 경로를 자동으로 인식하므로, <span style="color:red; font-weight: bold">해당 방법을 사용해야 한다.</span>

출처: <http://honeymon.io/tech/2020/07/09/gradle-annotation-processor-with-querydsl.html>


## 의존성 관리
- Querydsl 의존성과 Q Domain를 생성하는 annotationProcessor를 추가한다.

```
<build.gradle>

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
       delete file("src/main/generated") // intelliJ Annotation processor Q Domain 생성 위치
   }
}
```

<br>
- Gradle build를 수행하면 다음 이미지와 같이 프로젝트에서 Q Domain을 자동으로 생성하고, Q Domain 경로를 자동으로 인식한다.
- 다음은 Gradle build 할 때 Q Domain을 자동으로 생성한 코드다.

![image](/assets/img/2020-07-30-Project Lab5/image1.png)

![image](/assets/img/2020-07-30-Project Lab5/image2.png)

출처: <http://honeymon.io/tech/2020/07/09/gradle-annotation-processor-with-querydsl.html>



## Config
- QueryDsl을 프로젝트 내에서 사용할 수 있도록 필요한 부분을 설정한다.

```java
<module-domain-core/kr/ac/univ/common/config/QueryDslConfig>

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



## Repository
- QueryDsl를 사용하여 다음과 같은 쿼리를 작성하였다.
- findByTitle: 제목으로 게시글을 검색한다.(테스트 용도로 구현)
- updateViewCountById: 게시글 조회수를 1 증가시킨다.

```java
<module-domain-core/kr/ac/univ/noticeBoard/repository/NoticeBoardRepositoryImpl>

package kr.ac.univ.noticeBoard.repository;

import com.querydsl.jpa.impl.JPAQueryFactory;
import kr.ac.univ.noticeBoard.domain.NoticeBoard;
import kr.ac.univ.noticeBoard.domain.QNoticeBoard;
import org.springframework.data.jpa.repository.support.QuerydslRepositorySupport;
import org.springframework.stereotype.Repository;

import javax.transaction.Transactional;
import java.util.List;

import static kr.ac.univ.event.domain.QNoticeBoard.noticeBoard;

@Repository
@Transactional
public class NoticeBoardRepositoryImpl extends QuerydslRepositorySupport {
   private final JPAQueryFactory queryFactory;

   public NoticeBoardRepositoryImpl(JPAQueryFactory queryFactory ) {
       super(NoticeBoard.class);
       this.queryFactory = queryFactory;
   }

   public List<NoticeBoard> findByTitle(String title) {
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



## JUnit Test
- 200개의 데이터를 등록한 다음, QueryDsl 작성한 findByTitle과 updateViewCountById 쿼리가 정상적으로 동작하는지 테스트 하였다.

```java
<module-app-web/src/test/java/kr/ac/univ/QueryDslTest>

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



## Service
- NoticeBoard의 비즈니스 로직이다.
- findNoticeBoardByIdx: 게시글을 조회할 때 NoticeBoardReposityImpl의 updateViewCountById 메소드를 호출하여 게시글 조회수를 1 증가시킨다.

```java
<module-domain-core/src/main/java/kr/ac/univ/noticeBoard/service/NoticeBoardService>

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
- 다음 이미지와 같이 게시글을 조회하는 경우 조회수가 1 증가하는 것을 확인할 수 있다.

![image](/assets/img/2020-07-30-Project Lab5/image3.png)
