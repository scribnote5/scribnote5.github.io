---
title: "Project Lab 6. 게시판 개발(DTO, MapStruct) - 3"
excerpt: "MapStruct를 사용하여 Object Mapping 기능을 담당하는 DTO를 개발 과정을 소개한다."

categories:
  - Web
  - Project Lab

last_modified_at: 2020-08-02
---
- MapStruct를 사용하여 Object Mapping 기능을 담당하는 DTO를 개발 과정을 소개한다.
- github: <https://github.com/scribnote5/lab>
- github commit: <https://github.com/scribnote5/lab/commit/f0d7ab13069ebdb5ec8018f8da510c03790a08c5>



## DTO(Data Transfer Object)
- 계층간 데이터 교환을 위한 객체(Java Beans)이다.
- DTO는 Domain Model을 복사한 형태로, 다양한 Presentation Logic을 추가한 정도로 사용하며 Domain Model 객체는 Persistent만을 위해서 사용한다.



## DTO를 사용하는 이유
- DTO를 사용하지 않는 기존 domain model은 개발하기 빠르고 편리하다는 장점이 있지만, 다음과 같은 단점이 있다. 
- 본 프로젝트에서는 객체 지향 프로그래밍을 지향하기 위해서 DTO를 사용하여 개발하였다.


### 객체의 역할을 철저하게 분리하기 위해
-  Domain model에 view 계층의 로직이 포함되기에, 모든 클래스는 하나의 책임만 가지 객체의 '단일 책임 원칙'을 위반하게 된다. 


### Domain model은 View 계층의 요구사항을 모두 반영할 수 없음
- Domain은 실제 DB 테이블과 매칭된다. View 계층의 요구사항을 모두 반영하여 표현하기 어렵다. 이는 결합도가 증가하여 코드 수정이 불가피하게 발생할 수 있다.

출처: 
<https://netframework.tistory.com/entry/16-Model-%EA%B8%B0%EC%88%A0-%EC%A0%95%EB%A6%AC-%EB%B0%8F-%EB%B9%84%EA%B5%90><br>
<https://gmlwjd9405.github.io/2018/12/25/difference-dao-dto-entity.html>



## DTO 사용 범위
- 많은 블로그에서는 'DTO의 사용 범위를 어디까지 정해야 하는가?'에 대한 고민이 많았으며, 이에 대한 명확한 정답은 없었다. 
- DTO를 Controller 계층에서만 사용한다? DTO를 Service 계층까지 사용한다? 등 다양한 의견들이 있었다. 많은 블로그에서는 
- 많은 블로그에서 고민한 결과대로, <span style="color:red">본 프로젝트에서는 Service 계층까지 DTO를 사용하되 DTO <-> 변환은 Service 계층에서만 수행할 것이다.</span>
- 또한, DTO가 굳이 필요 없는 부분에서는 사용하지 않는 방식으로 DTO를 유연하게 사용할 것이다.

출처: <https://velog.io/@aidenshin/DTO%EC%97%90-%EA%B4%80%ED%95%9C-%EA%B3%A0%EC%B0%B0><br>
<https://os94.tistory.com/157><br>
<https://yonguri.tistory.com/m/entry/Entity-DTO-%EA%B7%B8-%EC%82%AC%EC%9D%B4%EC%9D%98-ModelMapper-%EC%9D%B4%EC%95%BC%EA%B8%B0>



## Mapstrcut
- DTO와 Entity간 객체 Mapping 소스 코드를 자동으로 생성하는 라이브러리다. 
- 하단 출처처럼 객체 Mapping을 지원하는 라이브러리가 많이 있다.
- 이 중 MapStruct의 처리 속도가 가장 빠르고, 구글링 결과 가장 보편적으로 사용하기에 MapStruct를 사용하였다. 

출처: 
https://www.baeldung.com/java-performance-mapping-frameworks


### 의존성 선언
- mapstruct의 의존성이 lombok 보다 먼저 선언되어야 한다. 
- 원인은 모르겠지만 의존성 순서가 변경되면 JUnit 테스트시 Mapper 인터페이스의 구현체인 MapperImpl 클래스를 인식하지 못한다.

```
build.gradle
```

```
...
// mapstruct, lombok 의존성보다 먼저 선언되어야 한다.
implementation "org.mapstruct:mapstruct:1.3.1.Final"
annotationProcessor "org.mapstruct:mapstruct-processor:1.3.1.Final"

// lombok
implementation "org.projectlombok:lombok"
annotationProcessor "org.projectlombok:lombok"
...
```

출처: <https://huisam.tistory.com/entry/mapStruct>


### 모든 Mapper 클래스가 구현 받는 공통 인터페이스
- Mapper 클래스가 공통적으로 사용하는 인터페이스다. 각 Mapper 클래스는 EntityMapper 인터페이스를 구현한다.

```
module-domain-core/src/main/java/kr/ac/univ/common/dto/mapper/EntityMapper
```

```java
package kr.ac.univ.common.dto.mapper;

import java.util.List;

public interface EntityMapper <Dto, Entity> {
   Entity toEntity(Dto dto);
   Dto toDto(Entity entity);
   List<Entity> toEntity(List<Dto> dto);
   List<Dto> toDto(List<Entity> entity);
}
```


### 모든 DTO 클래스가 상속 받아 사용하는 공통 DTO
- DTO 클래스가 공통적으로 사용하는 클래스다. 각 DTO 클래스는 CommonDto 클래스를 상속 받는다.

```
module-domain-core/src/main/java/kr/ac/univ/common/dto/CommonDto
```

```java
package kr.ac.univ.common.dto;

import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import lombok.ToString;

import java.time.LocalDateTime;

@Getter
@Setter
@NoArgsConstructor
@ToString
public class CommonDto {
   private Long idx;
   private LocalDateTime createdDate;
   private LocalDateTime lastModifiedDate;
   private String createdBy;
   private String lastModifiedBy;
   private boolean isAccess;
}
NoticeBoard Mapper 클래스
- 각 domain이 DTO로 매핑될 때 사용되는 Mapper 클래스다. 해당 클래스에서는 Mapping 규칙을 정의한다. 
module-domain-core/src/main/java/kr/ac/univ/noticeBoard/NoticeBoardMapper
package kr.ac.univ.noticeBoard.dto.mapper;

import kr.ac.univ.common.dto.mapper.EntityMapper;
import kr.ac.univ.noticeBoard.domain.NoticeBoard;
import kr.ac.univ.noticeBoard.dto.NoticeBoardDto;
import org.mapstruct.Mapper;
import org.mapstruct.factory.Mappers;

@Mapper(componentModel = "spring")
public interface NoticeBoardMapper extends EntityMapper<NoticeBoardDto, NoticeBoard> {
   NoticeBoardMapper INSTANCE = Mappers.getMapper(NoticeBoardMapper.class);

}
```


## NoticeBoard DTO 클래스
- 각 계층간 데이터를 전달할 때 데이터를 저장하는 DTO 클래스다. CommonDto 클래스를 상속 받는다.

```
module-domain-core/src/main/java/kr/ac/univ/noticeBoard/dto/NoticeBoardDto
```

```java
import kr.ac.univ.common.domain.enums.ActiveStatus;
import kr.ac.univ.common.dto.CommonDto;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;
import lombok.ToString;

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
}
```


### Gradle build
- Gradle build를 수행하면 다음 이미지와 같이 프로젝트에서 MapperImpl 경로를 자동으로 인식하고 Mapper 인터페이스가 구현되어 MapperImpl 클래스가 자동으로 생성한다.

![image](/assets/images/2020-08-02-Project Lab6/image1.png)

![image](/assets/images/2020-08-02-Project Lab6/image2.png)


### MapStruct로 생성된 MapperImpl 테스트
- 자동으로 구현된 MapperImpl 클래스의 DTO to Entity 과정을 테스트 하였다.

```
module-app-web/src/test/java/kr/ac/univ/MapStructTest
```

```java
package kr.ac.univ;

import kr.ac.univ.noticeBoard.domain.NoticeBoard;
import kr.ac.univ.noticeBoard.dto.NoticeBoardDto;
import kr.ac.univ.noticeBoard.dto.mapper.NoticeBoardMapper;
import lombok.extern.slf4j.Slf4j;
import org.junit.Assert;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit.jupiter.SpringExtension;


@SpringBootTest
@EnableAutoConfiguration
@ExtendWith(SpringExtension.class)
@Slf4j
public class MapStructTest {

   @Test
   @DisplayName("Mapstrcut 테스트")
   public void Test() {
       NoticeBoardDto noticeBoardDto = new NoticeBoardDto();
       noticeBoardDto.setTitle("제목입니다");
       noticeBoardDto.setContent("글입니다");

       NoticeBoard noticeBoard = NoticeBoardMapper.INSTANCE.toEntity(noticeBoardDto); // DTO -> Entity

       Assert.assertEquals(noticeBoardDto.getTitle(), noticeBoard.getTitle());
       Assert.assertEquals(noticeBoardDto.getContent(), noticeBoard.getContent());
   }
}
```


### 기존 Entity 대신 DTO 사용
- Entity 대신 DTO를 사용하도록 변경하였다. 
- 따라서 controller에서 view로 전달하는 noticeBoard를 noticeBoardDto로 변경하였고, view 또한 noticeBoard를 noticeBoardDto로 변경하였다.

```
module-app-web/src/main/java/kr/ac/univ/controller/NoticeBoardController
```

```java
model.addAttribute("noticeBoardDto", noticeBoardService.findNoticeBoardByIdx(idx));
```

```
module-app-web/src/main/resources/templates/noticeBoard/list, form, read
```

```java
<tr th:each="noticeBoardDto : ${noticeBoardDtoList}">
```


### Service 계층에 Entity <-> DTO 변환 소스 코드 추가
- Service 계층에서 MapStruct를 사용한 Entity와 DTO 간 변환 소스 코드를 추가하였다.
- findNoticeBoardList는 페이징 처리하여 NoticeBoard 리스트를 반환하는 메소드다. 해당 메소드에서 Pageable 객체는 Paging을 담당하고 NoticeBoard 리스트를 반환하는 객체로  DTO 변환 과정이 기존과 다르다. 해당 객체를 DTO로 변환하는 소스 코드는 하단 출처를 참고하였다. 

```
module-domain-core/src/main/java/kr/ac/univ/noticeBoard/service/NoticeBoardService
```

```java
package kr.ac.univ.noticeBoard.service;

import kr.ac.univ.noticeBoard.domain.NoticeBoard;
import kr.ac.univ.noticeBoard.dto.NoticeBoardDto;
import kr.ac.univ.noticeBoard.dto.mapper.NoticeBoardMapper;
import kr.ac.univ.noticeBoard.repository.NoticeBoardRepository;
import kr.ac.univ.noticeBoard.repository.NoticeBoardRepositoryImpl;
import org.springframework.data.domain.*;
import org.springframework.stereotype.Service;

import javax.transaction.Transactional;

@Service
public class NoticeBoardService {
   private final NoticeBoardRepository noticeBoardRepository;
   private final NoticeBoardRepositoryImpl noticeBoardRepositoryImpl;

   public NoticeBoardService(NoticeBoardRepository noticeBoardRepository, NoticeBoardRepositoryImpl noticeBoardRepositoryImpl) {
       this.noticeBoardRepository = noticeBoardRepository;
       this.noticeBoardRepositoryImpl = noticeBoardRepositoryImpl;
   }

   public Page<NoticeBoardDto> findNoticeBoardList(Pageable pageable) {
       Page<NoticeBoard> noticeBoardList = null;

       pageable = PageRequest.of(pageable.getPageNumber() <= 0 ? 0 : pageable.getPageNumber() - 1, pageable.getPageSize(), Sort.Direction.DESC, "idx");
       noticeBoardList = noticeBoardRepository.findAll(pageable);

       return new PageImpl<NoticeBoardDto>(NoticeBoardMapper.INSTANCE.toDto(noticeBoardList.getContent()), pageable, noticeBoardList.getTotalElements());
   }

   public Long insertNoticeBoard(NoticeBoard noticeBoard) {

       return noticeBoardRepository.save(noticeBoard).getIdx();
   }

   public NoticeBoardDto findNoticeBoardByIdx(Long idx) {
       noticeBoardRepositoryImpl.updateViewCountById(idx);

       return NoticeBoardMapper.INSTANCE.toDto(noticeBoardRepository.findById(idx).orElse(new NoticeBoard()));
   }

   @Transactional
   public Long updateNoticeBoard(Long idx, NoticeBoard noticeBoard) {
       noticeBoardRepository.getOne(idx).update(noticeBoard);

       return noticeBoardRepository.save(noticeBoard).getIdx();
   }

   public void deleteNoticeBoardByIdx(Long idx) {
       noticeBoardRepository.deleteById(idx);
   }
}
```

출처: <https://effectivecode.tistory.com/1220>