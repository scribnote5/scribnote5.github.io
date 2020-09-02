---
title: "Project Lab 6. 게시판 개발(DTO, MapStruct) - 3"
excerpt: "Object Mapping를 제공하는 MapStruct를 사용하여, Domain 모델에서 DTO 모델로 변환하는 과정을 소개한다."

categories:
  - Web
  - Project Lab

last_modified_at: 2020-09-03
---
- Object Mapping를 제공하는 MapStruct를 사용하여, Domain 모델에서 DTO 모델로 변환하는  과정을 소개한다.
- github: <https://github.com/scribnote5/lab>
- github commit: <https://github.com/scribnote5/lab/commit/f0d7ab13069ebdb5ec8018f8da510c03790a08c5>



## DTO(Data Transfer Object)
- 계층간 데이터 교환을 위한 객체(Java Beans)이다.
- DTO는 데이터 접근만을 위해 사용하는 domain Model을 복사한 형태로, view에서 사용하는 부가적인 데이터를 추가하였다.



## DTO를 사용하는 이유
- 기존 domain model은 개발하기 빠르다는 장점이 있다.
- 하지만 프로젝트의 로직이 복잡해지는 경우, DTO를 사용하면 조금더 객체지향 프로그래밍을 할 수 있고, 추후 유지보수가 쉬워지는 장점이 있다.


### 객체의 역할을 철저하게 분리하기 위해
-  Domain에 view 로직이 포함될 수 있다. 따라서 모든 클래스는 하나의 책임만 가지 객체의 '단일 책임 원칙'을 위반하게 된다.


### Domain model은 View 계층의 요구사항을 모두 반영할 수 없음
- Domain은 실제 DB 테이블과 매칭되기에, view의 요구사항을 모두 반영하여 표현하기 어렵다. 만약 요구사항을 모두 반영하게 된다면 객체 간의 결합도가 증가하기 때문에, 코드 수정이 불가피하게 발생할 수 있다.

출처: 
<https://netframework.tistory.com/entry/16-Model-%EA%B8%B0%EC%88%A0-%EC%A0%95%EB%A6%AC-%EB%B0%8F-%EB%B9%84%EA%B5%90><br>
<https://gmlwjd9405.github.io/2018/12/25/difference-dao-dto-entity.html>



## DTO 사용 범위
- 구글링한 결과, 이미 많은 개발자들은 'DTO의 사용 범위를 어디까지 정해야 하는가?'에 대한 고민을 계속 해왔다.
- 'DTO를 controller에서만 사용한다? DTO를 service까지 사용한다?' 등 다양한 의견들이 있었지만, 이에 대한 명확한 정답은 없었다. 
- <span style="color:red; font-weight: bold">본 프로젝트에서는 service까지 DTO를 사용하되, DTO <-> domain 변환은 service에서만 수행할 것이다.</span>

출처: <https://velog.io/@aidenshin/DTO%EC%97%90-%EA%B4%80%ED%95%9C-%EA%B3%A0%EC%B0%B0><br>
<https://os94.tistory.com/157><br>
<https://yonguri.tistory.com/m/entry/Entity-DTO-%EA%B7%B8-%EC%82%AC%EC%9D%B4%EC%9D%98-ModelMapper-%EC%9D%B4%EC%95%BC%EA%B8%B0>



## Mapstrcut
- DTO <-> Entity간 객체 mapping 소스 코드를 자동으로 생성하는 라이브러리다. 
- 하단 출처처럼 객체 간 mapping을 지원하는 라이브러리가 많이 존재하지만 이 중 MapStruct의 처리 속도가 가장 빠르고 가장 보편적으로 사용하기에, MapStruct를 선택하게 되었다.

출처: 
<https://www.baeldung.com/java-performance-mapping-frameworks>



## 의존성 관리
- Mapstrcut 의존성을 추가한다.
- <span style="color:red; font-weight: bold">Mapstruct의 의존성이 lombok 보다 먼저 선언되어야 한다.</span> 원인은 모르겠지만 의존성 순서가 변경되면 JUnit 테스트시 Mapper 인터페이스의 구현체인 MapperImpl 클래스를 인식하지 못하는 문제가 발생한다.

출처: <https://huisam.tistory.com/entry/mapStruct>

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



## Domain 및 DTO
- 모든 Mapper 클래스가 공통적으로 사용하는 인터페이스다. 
- DTO <-> Entity mapping을 담당하는 MapperImpl 클래스는 EntityMapper 인터페이스를 구현받으며, EntityMapper 인터페이스의 메소드는 Mapstruct에 의해 자동으로 DTO <-> Entity mapping 소스 코드가 생성된다.

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

<br>
- NoticeBoard Entity<->DTO mapping 소스 코드가 Mapstruct에 의해 생성되도록 메소드를 선언 및 하는 클래스다. 

```
module-domain-core/src/main/java/kr/ac/univ/noticeBoard/NoticeBoardMapper
```

```java
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

<br>
- DTO 클래스가 공통적으로 사용하는 데이터를 담은 클래스다. 모든 DTO 클래스는 CommonDto 클래스를 상속 받는다.

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
```

<br>
- NoticeBoard에서 사용하는 DTO다. 

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

<br>
- Gradle build를 수행하면 다음 이미지와 같이 프로젝트에서 MapperImpl 경로를 자동으로 인식하고 Mapper 인터페이스가 구현되어 MapperImpl 클래스가 자동으로 생성한다.
- <span style="color:red; font-weight: bold">DTO <-> Entity mapping을 담당하는 소스 코드가 MapperImpl에 자동으로 생성되기 위해서는, domain에 builder pattern이 구현되야 한다. 자동으로 생성된 MapperImpl 클래스를 확인해 보면, builder pattern을 사용하여 mapping을 수행하는 것을 확인할 수 있다.</span>

![image](/assets/images/2020-08-02-Project Lab6/image1.png)

![image](/assets/images/2020-08-02-Project Lab6/image2.png)


## Test
- MapperImpl 클래스의 DTO <-> Entity mapping을 테스트 하였다.

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



## Service
- NoticeBoard의 비즈니스 로직이다. 
- MapStruct를 사용하여 DTO <-> Entitiy mapping 소스 코드를 추가하였다.
- findNoticeBoardList는 페이징 처리하여 NoticeBoard 리스트를 반환하는 메소드다. 해당 메소드에서 Pageable 객체는 Paging을 담당하고 NoticeBoard 리스트를 반환하는 객체로  DTO 변환 과정이 기존과 다르다. 해당 객체를 DTO로 변환하는 소스 코드는 하단 출처를 참고하였다.

출처: <https://effectivecode.tistory.com/1220>

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



## Controller
- NoticeBoard 관련 클라이언트의 요청을 view로 매핑한다.
- Controller에서 view로 전달하는 noticeBoard 변수명을 noticeBoardDto로 변경하였다.

```
module-app-web/src/main/java/kr/ac/univ/controller/NoticeBoardController
```

```java
model.addAttribute("noticeBoardDto", noticeBoardService.findNoticeBoardByIdx(idx));
```



## View
- NoticeBoard 관련 데이터를 화면에 출력한다.
- Controller에서 전달받은 noticeBoard 변수명을 noticeBoardDto로 변경하였다.

```
module-app-web/src/main/resources/templates/noticeBoard/list, form, read
```

```java
<tr th:each="noticeBoardDto : ${noticeBoardDtoList}">
```