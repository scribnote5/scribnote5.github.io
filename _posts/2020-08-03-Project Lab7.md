---
title: "Project Lab 7. 게시판 개발(새로운 글 표시) - 4"
excerpt: "- 새로운 게시글이 등록되었을 때, 사용자가 이를 확인 할 수 있는 기능 개발 과정을 소개한다."
categories:
  - Web
  - Project Lab
last_modified_at: 2020-09-07
layout: post
---
- 새로운 게시글이 등록되었을 때, 사용자가 이를 확인 할 수 있는 기능 개발 과정을 소개한다.



- github: <https://github.com/scribnote5/lab>
- github commit: <https://github.com/scribnote5/lab/commit/51751f0a9c6b8a7b2fc75e39f449697588f38026>

- 최신 프로젝트 코드와 형상이 다를 수 있습니다. 게시글 코드는 참고만 하시되, 최신 코드는 github에서 확인 부탁드립니다.



## 새로운 글 표시
- 하단 이미지는 네이버 카페에서 새로운 게시글을 등록하였을 때, N 아이콘을 사용하여 사용자에게 최근 등록된 게시글임을 알려주고 있다.
- 네이버 카페처럼 새로운 게시글을 등록하였을 때, 사용자가 이를 확인할 수 있는 기능을 개발하려고 한다.
- 새로운 글인지를 판별하기 위해서는 등록될 때 날짜와 현재 날짜를 비교해야 한다.

![image](/assets/img/2020-08-03-Project Lab7/image1.png)

출처: <https://cafe.naver.com/thisisjava>



## Util
- Java: 게시글이 등록된 날짜와 현재 날짜를 비교한다.
- 해당 메소드는 비교 단위가 시간(hour)으로 계산하기에, 시간에 따른 오차가 발생한다.
- '게시글이 등록된 날짜'가 '현재 시간 + 24시간'보다 이전인 경우(최근에 등록된 게시글) true를 반환하며, 아닌 경우 false를 반환한다.

```java
<module-system-common/src/main/java/kr/ac/univ/util/NewIconCheck.java>

package kr.ac.univ.util;

import java.time.LocalDateTime;
import java.time.temporal.ChronoUnit;

public class NewIconCheck {
   public static Boolean isNew(LocalDateTime pastLocalDateTime ) {
       LocalDateTime currentTime = LocalDateTime.now();
       // 현재 시간과 비교하여 24시간 이내인 경우 newIcon 생성
       boolean result;

       if(ChronoUnit.HOURS.between(currentTime , pastLocalDateTime)  >= -24 ) {
           result = true;
       } else {
           result = false;
       }

       return result;
   }
}
```



## JUnit Test
- '게시글이 등록된 날짜'와 '현재 시간 + 24시간'을 비교하여 최근에 등록된 게시글인지 판별하는 과정을 테스트하였다.
- 테스트를 수행하려면 비교하고 싶은 날짜와 시간 수정이 필요하다.

```java
<module-app-web/src/test/java/kr/ac/univ/NewIconCheckTest.java>

package kr.ac.univ;

import kr.ac.univ.noticeBoard.repository.NoticeBoardRepository;
import kr.ac.univ.noticeBoard.repository.NoticeBoardRepositoryImpl;
import kr.ac.univ.util.NewIconCheck;
import lombok.extern.slf4j.Slf4j;
import org.junit.Assert;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import java.time.LocalDateTime;

@Slf4j
@SpringBootTest
@EnableAutoConfiguration
@ExtendWith(SpringExtension.class)
public class NewIconCheckTest {
    @Autowired
    NoticeBoardRepository noticeBoardRepository;

    @Autowired
    NoticeBoardRepositoryImpl noticeBoardRepositoryImpl;

    @Test
    @DisplayName("Time 테스트")
    public void Test() {
        long days = 2;

        // 비교하고 싶은 날짜와 시간을 입력한다.
        LocalDateTime pastDateTime = LocalDateTime.of(2020, 7, 30, 0, 0, 0, 0);

        // 현재 날짜와 비교하고 싶은 날짜의 차이가 24시간인 경우 Test가 통과된다.
        Assert.assertEquals(NewIconCheck.isNew(pastDateTime),true);
    }
}
```



## Domain 및 DTO
- NoticeBoard에서 사용하는 DTO다.
- 새로 등록한 게시글인지 확인하는 boolean 자료형 isNewIcon 멤버 필드를 추가하였다.

```java
<module-domain-core/src/main/java/kr/ac/univ/noticeBoard/dto/NoticeBoardDto.java>

package kr.ac.univ.noticeBoard.dto;

import kr.ac.univ.common.domain.enums.ActiveStatus;
import kr.ac.univ.common.dto.CommonDto;
import kr.ac.univ.noticeBoard.domain.NoticeBoardAttachedFile;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import lombok.ToString;

import java.util.ArrayList;
import java.util.List;

@Getter
@Setter
@NoArgsConstructor
@ToString
public class NoticeBoardDto extends CommonDto {
    /* CommonDto: JPA Audit */

    /* 기본 정보 */
    private String title;
    private String content;
    private ActiveStatus activeStatus;
    private Long viewCount;

    /* newIcon */
    private boolean isNewIcon;
}
```



## Service
- NoticeBoard의 비즈니스 로직이다.
- findNoticeBoardList(모든 게시글 리스트 검색): 최근 등록된 게시글을 판별하는 로직을 해당  메소드에서 판별한다. 'noticeBoardDto.setNewIcon(NewIconCheck.isNew(LocalDateTime.now()));' 해당 소스 코드는 JPA Audit을 적용한 다음 createdDated(게시글 등록 날짜)로 변경할 예정이다.

```java
<module-domain-core/src/main/java/kr/ac/univ/noticeBoard/service/NoticeBoardService.java>

...

    public Page<NoticeBoardDto> findNoticeBoardList(Pageable pageable) {
        Page<NoticeBoard> noticeBoardList = null;
        Page<NoticeBoardDto> noticeBoardDtoList = null;

        pageable = PageRequest.of(pageable.getPageNumber() <= 0 ? 0 : pageable.getPageNumber() - 1, pageable.getPageSize(), Sort.Direction.DESC, "idx");
        noticeBoardList = noticeBoardRepository.findAll(pageable);
        noticeBoardDtoList = new PageImpl<NoticeBoardDto>(NoticeBoardMapper.INSTANCE.toDto(noticeBoardList.getContent()), pageable, noticeBoardList.getTotalElements());

        // NewIcon 판별
        for(NoticeBoardDto noticeBoardDto : noticeBoardDtoList) {
            // 추후 변경
            noticeBoardDto.setNewIcon(NewIconCheck.isNew(LocalDateTime.now()));
        }

        return noticeBoardDtoList;
    }
```



## View
- NoticeBoard 관련 데이터를 화면에 출력한다.
- 최근 등록된 게시글은 NoticeBoard list.html에서 newIcon의 조건을 판별 후 true인 경우 N 아이콘을 출력한다.
- 참고로 자바 DTO에서는 isNewIcon 변수를 사용하지만, thymeleaf에서는 이와 다르게 is가 생략된 newIcon 변수를 사용한다.

```html
<module-app-web/src/main/resources/templates/noticeBoard/list.html>

...
&nbsp;
<img th:if="${noticeBoardDto.newIcon}" th:attr="src=@{|/images/new_icon.png|}" th:style="'width: 15px; height: 15px'" />
...
```



## 프로젝트 실행 및 결과
- 다음과 같이 새로 등록된 게시글에 N 아이콘이 표시되는 것을 확인할 수 있다.

![image](/assets/img/2020-08-03-Project Lab7/image2.png)
