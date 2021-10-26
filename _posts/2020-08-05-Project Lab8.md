---
title: "Project Lab 8. 게시판 개발(파일 업로드 및 다운로드) - 5"
excerpt: "드래그앤드랍 기능을 지원하는 다중 파일 업로드 및 파일 다운로드를 ajax로 개발한 과정을 소개한다."
categories:
  - Web
  - Project Lab
last_modified_at: 2021-04-19
layout: post
---
- 드래그 앤 드랍 기능을 지원하는 다중 파일 업로드 및 파일 다운로드를 ajax로 개발한 과정을 소개한다.



- github: <https://github.com/scribnote5/lab>
- github commit: <https://github.com/scribnote5/lab/commit/c7dd944785ff76133498ff6e95df748b140c717b>

- 최신 프로젝트 코드와 형상이 다를 수 있습니다. 게시글 코드는 참고만 하시되, 최신 코드는 github에서 확인 부탁드립니다.



## 파일 업로드 및 다운로드 설계
- Spirng은 MultipartResolver 인터페이스와 Servlet Multipart Request 그리고 Apache Commons FileUpload API 두 개의 구현체로 파일 업로드를 지원한다. 본 프로젝트에서는 Servlet Multipart Request를 사용하여 파일 업로드를 구현한다.
- module-app-web 모듈 서버에서 다중 파일 업로드를 하게 되면 ajax를 통하여 요청하며, REST api를 사용하는 module-app-api 모듈 서버가 응답한다.
- 다중 파일 업로드는 드래그앤드랍 기능을 지원한다.

출처: <https://advenoh.tistory.com/26>
<https://mkyong.com/spring-boot/spring-boot-file-upload-example-ajax-and-rest/><br>
<https://doublesprogramming.tistory.com/130><br>
<https://sooin01.tistory.com/m/entry/jQuery-ajax-%ED%8C%8C%EC%9D%BC%EC%97%85%EB%A1%9C%EB%93%9C><br>
<https://cofs.tistory.com/181><br>
<https://offbyone.tistory.com/69><br>
<https://okky.kr/article/610701><br>
<https://gofnrk.tistory.com/80>



## Table 설계
- 프로젝트에서 사용할 게시판 첨부 파일 table을 생성한다.

```sql
# notice_board_attached_file
$ CREATE TABLE notice_board_attached_file (
 idx               bigint auto_increment    primary key,
 created_by        varchar(255)    null,
 created_date      datetime(6)     null,
 file_name         varchar(255)    null,
 saved_file_name   varchar(255)    null,
 notice_board_idx  bigint          null,
 file_size         varchar(255)    null
);
```



## Config
- max-swallow-size: 요청 body의 크기를 설정한다. 업로드되는 파일 크기가 제한(20MB)을 초과하여 예외가 발생하는 경우, 사용자 정의 예외처리 방식으로 수행되도록 구현하였다.
- max-file-size과 max-request-size: 업로드되는 파일 크기를 제한한다. 만약 제한된 파일 업로드 크기보다 큰 파일이 업로드되는 경우 예외가 발생한다. 
- <span style="color:red; font-weight: bold">파일 업로드 되는 경로는 /upload 폴더이므로, 해당 경로에 upload 폴더를 필수로 생성해야 한다.(root 프로젝트에 upload 폴더를 생성하면 된다.)</span>
 
```
module-app-api/src/main/resources/application.yml
```

```
spring:
 servlet:
   multipart:
     # 한개의 파일의 최대 크기
     max-file-size: 20MB
     # form-data 요청에 따른 모든 파일의 최대 크기
     max-request-size: 20MB
server:
   tomcat:
     max-swallow-size: -1
...
```



## Domain 및 DTO 설계
- 모든 Attachedfile에서 공통적으로 사용하는 Domain다.

```
module-domain-core/src/main/java/kr/ac/univ/common/domain/AttachedFileAudit.java
```

```java
import lombok.Getter;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import javax.persistence.*;
import java.time.LocalDateTime;

@MappedSuperclass
@Getter
@EntityListeners(AuditingEntityListener.class)
public abstract class AttachedFileAudit {
   @Id
   @GeneratedValue(strategy = GenerationType.IDENTITY)
   protected Long idx;

   private LocalDateTime createdDate;

   private String createdBy;
}
```

<br>
- NoticeBoard attachedfile에서 사용하는 DTO다.

```
module-domain-core/src/main/java/kr/ac/univ/noticeBoard/domain/NoticeBoardAttachedFile.java
```

```java
package kr.ac.univ.noticeBoard.domain;


import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.EntityListeners;
import javax.persistence.Table;

import kr.ac.univ.common.domain.AttachedFileAudit;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;


import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.ToString;

@Getter
@NoArgsConstructor
@Entity
@Table
@ToString
@EntityListeners(AuditingEntityListener.class)
public class NoticeBoardAttachedFile extends AttachedFileAudit {
   @Column
   private Long noticeBoardIdx;

   @Column
   private String fileName;

   @Column
   private String savedFileName;

   @Column
   private String fileSize;

   @Builder
   public NoticeBoardAttachedFile(Long noticeBoardIdx, String fileName, String savedFileName, String fileSize) {
       this.fileName = fileName;
       this.noticeBoardIdx = noticeBoardIdx;
       this.savedFileName = savedFileName;
       this.fileSize = fileSize;
   }
}
```

<br>
- NoticeBoard 파일 업로드에 사용하는 DTO다.

```
module-domain-core/src/main/java/kr/ac/univ/noticeBoard/dto/NoticeBoardDto.java
```

```java
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

   /* 첨부 파일 */
   private List<NoticeBoardAttachedFile> attachedFileList = new ArrayList<NoticeBoardAttachedFile>();

}
```

<br>
- NoticeBoard DTO <-> Entity간 객체 mapping 소스 코드가 Mapstruct에 의해 생성되도록 메소드를 선언 및 정의하는 클래스다.
- default 메소드는 사용자가 정의한 메소드로, NoticeBoardAttachedFile 파일 리스트를 NoticeBoardDto의 파일 리스트로 매핑한다.

```
module-domain-core/src/main/java/kr/ac/univ/noticeBoard/dto/mapper/NoticeBoardMapper.java
```

```java
package kr.ac.univ.noticeBoard.dto.mapper;

import kr.ac.univ.common.dto.mapper.EntityMapper;
import kr.ac.univ.noticeBoard.domain.NoticeBoard;
import kr.ac.univ.noticeBoard.domain.NoticeBoardAttachedFile;
import kr.ac.univ.noticeBoard.dto.NoticeBoardDto;
import org.mapstruct.Mapper;
import org.mapstruct.factory.Mappers;

import java.util.List;

@Mapper(componentModel = "spring")
public interface NoticeBoardMapper extends EntityMapper<NoticeBoardDto, NoticeBoard> {
   NoticeBoardMapper INSTANCE = Mappers.getMapper(NoticeBoardMapper.class);

   default NoticeBoardDto toDto(NoticeBoardDto noticeBoardDto, List<NoticeBoardAttachedFile> attachedFileList) {
       for (NoticeBoardAttachedFile attachedFile : attachedFileList) {
           noticeBoardDto.getAttachedFileList().add(attachedFile);
       }

       return noticeBoardDto;
   }
}
```



## Repository
- QueryDsl를 사용하여 다음과 같은 쿼리를 작성하였다.(JPA로 대체 가능하다.)
- findAttachedFileByNoticeBoardIdx: 매개변수의 게시글 idx와 같은 파일을 모두 검색한다.
- deleteAttachedFileByNoticeBoardIdx: 매개변수의 게시글 idx와 같은 파일을 모두 삭제한다

```
module-domain-core/src/main/java/kr/ac/univ/noticeBoard/repository/NoticeBoardAttachedFileRepositoryImpl.java
```

```java
package kr.ac.univ.noticeBoard.repository;

import java.util.List;

import javax.transaction.Transactional;

import kr.ac.univ.noticeBoard.domain.NoticeBoardAttachedFile;
import kr.ac.univ.noticeBoard.domain.QNoticeBoardAttachedFile;
import org.springframework.data.jpa.repository.support.QuerydslRepositorySupport;
import org.springframework.stereotype.Repository;

import com.querydsl.jpa.impl.JPAQueryFactory;

@Repository
@Transactional
public class NoticeBoardAttachedFileRepositoryImpl extends QuerydslRepositorySupport {
   private final JPAQueryFactory queryFactory;

   public NoticeBoardAttachedFileRepositoryImpl(JPAQueryFactory queryFactory) {
       super(NoticeBoardAttachedFile.class);
       this.queryFactory = queryFactory;
   }

   public List<NoticeBoardAttachedFile> findAttachedFileByNoticeBoardIdx(Long noticeBoardIdx) {
       QNoticeBoardAttachedFile noticeBoardAttachedFile = QNoticeBoardAttachedFile.noticeBoardAttachedFile;

       /* SELECT *
        *   FROM AttachedFile
        *  WHERE noticeBoardIdx = 'noticeBoardIdx'
        *  ORDER BY idx asc
        */
       return queryFactory
               .selectFrom(noticeBoardAttachedFile)
               .where(noticeBoardAttachedFile.noticeBoardIdx.eq(noticeBoardIdx))
               .orderBy(noticeBoardAttachedFile.idx.asc())
               .fetch();
   }

   public Long deleteAttachedFileByNoticeBoardIdx(Long noticeBoardIdx) {
       QNoticeBoardAttachedFile noticeBoardAttachedFile = QNoticeBoardAttachedFile.noticeBoardAttachedFile;

       /* DELETE FROM AttachedFile
        *  WHERE noticeBoardIdx = 'noticeBoardIdx'
        */
       return queryFactory
               .delete(noticeBoardAttachedFile)
               .where(noticeBoardAttachedFile.noticeBoardIdx.eq(noticeBoardIdx))
               .execute();
   }
}
```



## Service
- NoticeBoard attachedfile의 비즈니스 로직이다. 
- uploadAttachedFile: view에서 전달받은 파일을 저장하는 로직이다. 
- 파일명 앞에 고유한 식별문자를 생성하는 UUID를 사용하여 파일명 중복이 발생하지 않도록 하였다.
- 자바에서는 입출력 방법으로 IO 라이브러리와  NIO(New IO) 라이브러리를 사용할 수 있다. 
- NIO 라이브러리는 연결 클라이언트 수가 많고 하나의 입출력 처리 작업이 오래 걸리지 않는 경우에 사용하는 것이 좋다.
- IO 라이브러리는 연결 클라이언트 수가 적고 전송되는 데이터가 대용량이면서 순차적으로 처리될 필요성이 있는 경우 사용하는 것이 좋다.
- 프로젝트에서는 업로드하는 파일 크기를 20 MB로 제한할 예정이므로, 적은 시간이 소요되는 입출력 처리 작업이 많은 프로젝트의 특성상 NIO 라이브러리가 IO 라이브러리 보다 성능상 더 유리하다고 생각하였다. 따라서 NIO 라이브러리를 사용하여 파일 업로드 및 다운로드를 구현하였다.

출처: <https://m.blog.naver.com/PostView.nhn?blogId=rain483&logNo=220636709530&proxyReferer=https:%2F%2Fwww.google.com%2F><br>
<http://eincs.com/2009/08/java-nio-bytebuffer-performance/>

```
module-domain-core/src/main/java/kr/ac/univ/noticeBoard/service/NoticeBoardAttachedFileService.java
```

```java
package kr.ac.univ.noticeBoard.service;

import kr.ac.univ.noticeBoard.domain.NoticeBoardAttachedFile;
import kr.ac.univ.noticeBoard.dto.NoticeBoardDto;
import kr.ac.univ.noticeBoard.dto.mapper.NoticeBoardMapper;
import kr.ac.univ.noticeBoard.repository.NoticeBoardAttachedFileRepository;
import kr.ac.univ.noticeBoard.repository.NoticeBoardAttachedFileRepositoryImpl;
import kr.ac.univ.util.FileUtil;
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.List;
import java.util.UUID;


@Service
public class NoticeBoardAttachedFileService {
   private final NoticeBoardAttachedFileRepository noticeBoardAttachedFileRepository;
   private final NoticeBoardAttachedFileRepositoryImpl noticeBoardAttachedFileRepositoryImpl;

   public NoticeBoardAttachedFileService(NoticeBoardAttachedFileRepository noticeBoardAttachedFileRepository, NoticeBoardAttachedFileRepositoryImpl noticeBoardAttachedFileRepositoryImpl) {
       this.noticeBoardAttachedFileRepository = noticeBoardAttachedFileRepository;
       this.noticeBoardAttachedFileRepositoryImpl = noticeBoardAttachedFileRepositoryImpl;
   }

   public NoticeBoardDto findAttachedFileByNoticeBoardIdx(Long noticeBoardIdx, NoticeBoardDto noticeBoardDto) {

       return NoticeBoardMapper.INSTANCE.toDto(noticeBoardDto, noticeBoardAttachedFileRepositoryImpl.findAttachedFileByNoticeBoardIdx(noticeBoardIdx));

   }

   public void insertAttachedFile(NoticeBoardAttachedFile attachedFile) {
       noticeBoardAttachedFileRepository.save(attachedFile);
   }

   public NoticeBoardAttachedFile findAttachedFileByIdx(Long idx) {
       return noticeBoardAttachedFileRepository.findById(idx).orElse(new NoticeBoardAttachedFile());
   }

   public NoticeBoardAttachedFile getAttachedFileByIdx(Long idx) {
       return noticeBoardAttachedFileRepository.getOne(idx);
   }

   public void deleteAttachedFileByIdx(Long idx) {
       noticeBoardAttachedFileRepository.deleteById(idx);
   }

   public Long deleteAttachedFileByNoticeBoardIdx(Long idx) {
       return noticeBoardAttachedFileRepositoryImpl.deleteAttachedFileByNoticeBoardIdx(idx);
   }

   /**
    * 첨부 파일 업로드
    *
    * @param noticeBoardIdx
    * @param files
    */
   public void uploadAttachedFile(Long noticeBoardIdx, MultipartFile[] files) throws Exception {
       NoticeBoardAttachedFile uploadFile = new NoticeBoardAttachedFile();

       for (MultipartFile file : files) {
           String uuid = UUID.randomUUID().toString().replaceAll("-", "");
           String savedFileName = uuid + "_" + file.getOriginalFilename();

           // 대체 가능
           // File savedFile = new File("./upload/", savedFileName);
           // FileCopyUtils.copy(file.getBytes(), savedFile);

           Path path = Paths.get("./upload/" + savedFileName);
           Files.write(path, file.getBytes());

           uploadFile = NoticeBoardAttachedFile.builder()
                   .noticeBoardIdx(noticeBoardIdx)
                   .fileName(file.getOriginalFilename())
                   .savedFileName(savedFileName)
                   .fileSize(FileUtil.convertFileSize(file.getSize()))
                   .build();

           insertAttachedFile(uploadFile);
       }
   }

   /**
    * 첨부 파일 삭제
    *
    * @param deleteAttachedFileIdxList
    */
   public void deleteAttachedFile(List<Long> deleteAttachedFileIdxList) throws Exception {
       for (Long idx : deleteAttachedFileIdxList) {
           NoticeBoardAttachedFile attachedFile = findAttachedFileByIdx(idx);

           Path path = Paths.get("./upload/" + attachedFile.getSavedFileName());
           Files.delete(path);

           deleteAttachedFileByIdx(attachedFile.getIdx());
       }
   }

   /**
    * 모든 첨부 파일 삭제
    *
    * @param noticeBoardIdx
    */
   public void deleteAllAttachedFile(Long noticeBoardIdx) throws Exception {
       List<NoticeBoardAttachedFile> attachedFileList = noticeBoardAttachedFileRepositoryImpl.findAttachedFileByNoticeBoardIdx(noticeBoardIdx);

       for (NoticeBoardAttachedFile attachedFile : attachedFileList) {
           Path path = Paths.get("./upload/" + attachedFile.getSavedFileName());
           Files.delete(path);
       }

       deleteAttachedFileByNoticeBoardIdx(noticeBoardIdx);
   }
}
```



## Controller
- NoticeBoard attachedfile 관련 클라이언트의 요청을 view로 매핑한다.
- noticeBoardForm, noticeBoardRead: noticeBoard와 연관된 noticeBoard attachedfile을 데이터를 조회 후 noticeBoard 데이터와 함께 view에 전달한다.

```
module-app-web/src/main/java/kr/ac/univ/controller/NoticeBoardController.java
```

```java
package kr.ac.univ.controller;

import kr.ac.univ.noticeBoard.dto.NoticeBoardDto;
import kr.ac.univ.noticeBoard.dto.mapper.NoticeBoardMapper;
import kr.ac.univ.noticeBoard.service.NoticeBoardAttachedFileService;
import kr.ac.univ.noticeBoard.service.NoticeBoardService;
import org.springframework.data.domain.Pageable;
import org.springframework.data.web.PageableDefault;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
@RequestMapping("/notice-board")
public class NoticeBoardController {
   private final NoticeBoardService noticeBoardService;
   private final NoticeBoardAttachedFileService noticeBoardAttachedFileService;

   public NoticeBoardController(NoticeBoardService noticeBoardService, NoticeBoardAttachedFileService noticeBoardAttachedFileService) {
       this.noticeBoardService = noticeBoardService;
       this.noticeBoardAttachedFileService = noticeBoardAttachedFileService;
   }

   // List
   @GetMapping("/list")
   public String noticeBoardList(@PageableDefault Pageable pageable, Model model) {
       model.addAttribute("noticeBoardDtoList", noticeBoardService.findNoticeBoardList(pageable));

       return "/noticeBoard/list";
   }

   // Form Update
   @GetMapping("/form{idx}")
   public String noticeBoardForm(@RequestParam(value = "idx", defaultValue = "0") Long idx, Model model) {
       NoticeBoardDto noticeBoardDto = null;

       noticeBoardDto = noticeBoardService.findNoticeBoardByIdx(idx);
       noticeBoardDto = noticeBoardAttachedFileService.findAttachedFileByNoticeBoardIdx(idx, noticeBoardDto);

       model.addAttribute("noticeBoardDto", noticeBoardDto);

       return "/noticeBoard/form";
   }

   // Read
   @GetMapping({"", "/"})
   public String noticeBoardRead(@RequestParam(value = "idx", defaultValue = "0") Long idx, Model model) {
       NoticeBoardDto noticeBoardDto = null;

       noticeBoardDto = noticeBoardService.findNoticeBoardByIdx(idx);
       noticeBoardDto = noticeBoardAttachedFileService.findAttachedFileByNoticeBoardIdx(idx, noticeBoardDto);

       model.addAttribute("noticeBoardDto", noticeBoardDto);

       return "/noticeBoard/read";
   }
}
```



## RestController
- NoticeBoard attachedfile 관련 클라이언트의 요청을 처리 후 json 타입으로 응답한다.

```
module-app-api/src/main/java/kr/ac/univ/controller/NoticeBoardRestController.java
```

```java
package kr.ac.univ.controller;

import kr.ac.univ.noticeBoard.dto.NoticeBoardDto;
import kr.ac.univ.noticeBoard.service.NoticeBoardAttachedFileService;
import kr.ac.univ.noticeBoard.service.NoticeBoardService;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import java.util.List;

@RestController
@RequestMapping("/api/notice-boards")
public class NoticeBoardRestController {
   private final NoticeBoardService noticeBoardService;
   private final NoticeBoardAttachedFileService noticeBoardAttachedFileService;

   public NoticeBoardRestController(NoticeBoardService noticeBoardService, NoticeBoardAttachedFileService noticeBoardAttachedFileService) {
       this.noticeBoardService = noticeBoardService;
       this.noticeBoardAttachedFileService = noticeBoardAttachedFileService;
   }

   @PostMapping
   public ResponseEntity<?> postNoticeBoard(@RequestBody NoticeBoardDto noticeBoardDto) {
       Long idx = noticeBoardService.insertNoticeBoard(noticeBoardDto);

       return new ResponseEntity<>(idx, HttpStatus.CREATED);
   }

   @PutMapping("/{idx}")
   public ResponseEntity<?> putNoticeBoard(@PathVariable("idx") Long idx, @RequestBody NoticeBoardDto noticeBoardDto) {
       noticeBoardService.updateNoticeBoard(idx, noticeBoardDto);

       return new ResponseEntity<>("{}", HttpStatus.OK);
   }

   @DeleteMapping("/{idx}")
   public ResponseEntity<?> deleteNoticeBoard(@PathVariable("idx") Long idx) throws Exception {
       noticeBoardService.deleteNoticeBoardByIdx(idx);
       noticeBoardAttachedFileService.deleteAllAttachedFile(idx);

       return new ResponseEntity<>("{}", HttpStatus.OK);
   }

   // 첨부 파일 업로드
   @PostMapping("/attachedFile")
   public ResponseEntity<?> uploadAttachedFile(Long idx, MultipartFile[] files) throws Exception {
       noticeBoardAttachedFileService.uploadAttachedFile(idx, files);

       return new ResponseEntity<>("{}", HttpStatus.CREATED);
   }

   // 첨부 파일 삭제
   @DeleteMapping("/attachedFile")
   public ResponseEntity<?> deleteAttachedFile(@RequestBody List<Long> deleteAttachedFileIdxList) throws Exception {
       noticeBoardAttachedFileService.deleteAttachedFile(deleteAttachedFileIdxList);

       return new ResponseEntity<>("{}", HttpStatus.OK);
   }
}
```



- Attachedfile 다운로드 클라이언트의 요청을 응답한다.
- downloadAttachedFile: 모든 파일 다운로드는 요청은 하나의 URL에서 담당하며, 파일 이름을 같이 전달한다.
- 헤더, MimeType(웹을 통해 전달되는 다양한 형태의 파일 정보), 다운로드 파일의 bytes 총 3개의 정보로 구성되어 파일 다운로드 요청에 응답한다.

```
module-app-api/src/main/java/kr/ac/univ/controller/AttachedFileRestController.java
```

```java
package kr.ac.univ.controller;

import org.springframework.core.io.ByteArrayResource;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.net.URLEncoder;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

@RestController
@RequestMapping("/api/attachedFiles")
public class AttachedFileRestController {
   @GetMapping("/download/{savedFileName}")
   public ResponseEntity<?> downloadAttachedFile(@PathVariable("savedFileName") String savedFileName) throws Exception {
       // 파일 이름이 한글인 경우 인코딩이 깨지지 않도록 변경
       String encordedSavedFileName = URLEncoder.encode(savedFileName, "UTF-8").replace("+", "%20");

       // 헤더 추가
       HttpHeaders header = new HttpHeaders();
       header.add(HttpHeaders.CONTENT_DISPOSITION, "attachment;filename=" + encordedSavedFileName.substring(33));
       header.add("Cache-Control", "no-cache, no-store, must-revalidate");
       header.add("Pragma", "no-cache");
       header.add("Expires", "0");

       // MimeType 추가, application/octet-stream은 text/plain 타입을 제외한 기본 값
       MediaType mediaType = MediaType.parseMediaType("application/octet-stream");

       // 다운로드 파일 추가
       Path path = Paths.get("./upload/" + savedFileName);
       ByteArrayResource resource = new ByteArrayResource(Files.readAllBytes(path));

       return ResponseEntity.ok()
               .headers(header)
               .contentType(mediaType)
               .body(resource);
   }
}
```



## View
- NoticeBoard attachedfiled 관련 데이터를 화면에 출력한다.
- input tag를 사용하거나, 파일 업로드 영역으로 파일을 드래그앤드랍 하면 파일을 업로드 할 수 있다.
- 파일을 업로드 하면 파일 데이터가 insertFileArray 배열에 추가되고 attachedFileList div 태그 내에 파일 데이터가 출력된다. 파일 데이터 오른쪽에 있는 X 아이콘을 클릭하면 해당되는 insertFileArray 배열의 요소와 attachedFileList div 태그내에 파일 데이터가 삭제되어 업로드 하는 파일을 취소할 수 있다. 
- 게시글을 수정하는 경우 업로드된 파일 데이터가 uploadedAttachedFileList div 태그내에 파일 데이터가 출력된다. 파일 데이터 오른쪽에 있는 X 아이콘을 클릭하면 해당되는 파일 idx(pk)가 deleteFileArray 배열의 요소에 추가되고 uploadedAttachedFileList div 태그내에 파일 데이터가 삭제되어 업로드된 파일을 삭제할 수 있다. 
- 게시글이 먼저 업로드된 다음 파일을 업로드가 진행되도록 구현하였다.(파일 업로드 수행시 게시글의 idx가 필요하기 때문이다.) 또한 파일을 수정하는 모든 경우를 고려하여 알고리즘 로직을 구현하였으며, 자세한 알고리즘 로직은 주석을 참고하면 된다.(3개의 첨부 파일 중 2개를 삭제하고 1개를 새로 업로드 하는 경우, 3개의 첨부 파일을 모두 삭제하는 경우, 첨부 파일이 없을 때 파일을 업로드하는 경우 등)
- formdata 객체는 서버 전송에 필요한 데이터를 저장할 수 있다. 하지만 해당 객체는 보안상의 이유로 console.log(formdata);을 사용하여 객체 정보를 확인할 수 없다. formdata 객체의 정보를 확인하는 방법은 다음과 같다.

```javascript
for (var key of formData.keys()) {
  console.log(key);
}
for (var value of formData.values()) {
  console.log(value);
}
```

출처 :<https://programmerpsk.tistory.com/177>

```
module-app-web/src/main/resources/templates/noticeBoard/form.html
```

```html
<tr>
   <th>Attached File</th>
   <td>
       <input type="file" multiple="multiple" name="file" id="file"/>
       <div id="fileDrop" class="fileDrop"></div>
   </td>
</tr>
<tr>
   <th>Total file size</th>
   <td>
       <div><span id="totalFileSize"> 0 MB</span>, Up to 20 MB</div>
   </td>
<tr>
   <th>Uplaod Attached File</th>
   <td>
       <div id="attachedFileList"></div>
   </td>
</tr>
<tr>
   <th>Uploaded Attached File</th>
   <td>
       <div id="uploadedAttachedFileList" th:each="attachedFile : *{attachedFileList}">
           <div th:id="imgData + ${attachedFileStat.index}">
               <span th:text="${attachedFile.fileName} + ',&nbsp;' + 'File Size: ' + ${attachedFile.fileSize} + '&nbsp;'"></span>
               <img th:attr="src=@{|/images/cancel.png|}, onclick=|deleteFile('${attachedFileStat.index}','${attachedFile.idx}','${attachedFile.savedFileName}')|"
                    th:style="'width: 16px; height: 16px'"/>
           </div>
       </div>
   </td>
</tr>

...

<script>
   var totalFileSize = 0;
   var insertFileArray = [];
   var deleteFileArray = [];
   var imgDataId = 0;
   var exit = null;

   $(document).ready(function () {
       <!-- summernote setting -->
       $('#summernote').summernote({
           height: 250,   // 에디터 높이
           minHeight: null,   // 최소 높이
           maxHeight: null,   // 최대 높이
           // focus: true,    // 에디터 로딩후 포커스를 맞출지 여부
           lang: "ko-KR",// 한글 설정
           placeholder: "The editor's max input size of bytes is 16777215."   //placeholder 설정

       });

       <!-- File Drop -->
       $("#fileDrop").on("dragenter dragover", function (event) {
           event.preventDefault(); // 기본 이벤트 발생을 막음
       });
   });

   /* input tag event */
   $('#file').change(function () {
       var files = document.getElementsByName("file")[0].files;

       for (var i = 0; i < files.length; i++) {
           insertFileArray.push(files[i]);
           document.getElementById("totalFileSize").innerHTML = convertFileSize(totalFileSize);

           $("#attachedFileList").append('<div id="imgData' + imgDataId + '">'
               + '<span>'
               + files[i].name + ",&nbsp; File Size: " + convertFileSize(files[i].size) + "&nbsp;"
               + '<img src="/images/cancel.png" style="width: 16px; height: 16px" onClick="cancelFile(' + imgDataId + ')" />'
               + '</span>'
               + '</div>');

           imgDataId++;
       }
   });

   /* Drag & drop event */
   $("#fileDrop").on("drop", function (event) {
       event.preventDefault(); // 기본 효과를 막음
       // 드래그된 파일의 정보
       // event : jQuery의 이벤트
       // originalEvent : javascript의 이벤트
       var files = event.originalEvent.dataTransfer.files;

       for (var i = 0; i < files.length; i++) {
           insertFileArray.push(files[i]);
           document.getElementById("totalFileSize").innerHTML = convertFileSize(totalFileSize);

           $("#attachedFileList").append('<div id="imgData' + imgDataId + '">'
               + '<span>'
               + files[i].name + ",&nbsp; File Size: " + convertFileSize(files[i].size) + "&nbsp;"
               + '<img src="/images/cancel.png" style="width: 16px; height: 16px" onClick="cancelFile(' + imgDataId + ')" />'
               + '</span>'
               + '</div>');

           imgDataId++;
       }
   });

   // 새로 업로드한 파일을 취소하는 경우
   function cancelFile(fileId) {
       $('#imgData' + fileId).remove();

       insertFileArray[fileId] = null;
   }

   // 기존 업로드한 파일을 삭제하는 경우
   function deleteFile(fileId, idx, savedFileName) {
       $('#imgData' + fileId).remove();

       deleteFileArray.push(idx);
   }

   function deleteNoticeBoard(noticeBoardIdx) {
       // 게시글 삭제
       $.ajax({
           url: "/api/notice-boards/" + noticeBoardIdx,
           type: "delete",
           dataType: "text",
           contentType: "application/json",
           async: false,
       })
           .done(function (msg) {
               console.log("NoticeBoard delete success.");
           })
           .fail(function (msg) {
               console.log("NoticeBoard delete fail.");
           });
   }
</script>

<script th:if="!${noticeBoardDto?.idx}">
   $('#insert').click(function () {
       var jsonData = $("#form").serializeObject();
       var noticeBoardIdx;

       // 게시글 업로드
       $.ajax({
           url: "http://localhost:8081/api/notice-boards",
           type: "post",
           data: JSON.stringify(jsonData),
           dataType: "text",
           contentType: "application/json",
           async: false,
       })
           .done(function (msg) {
               noticeBoardIdx = msg;
               exit = false;
           })
           .fail(function (msg) {
               exit = true;
           });

       if (exit) return false;

       // 파일 업로드
       var formData = new FormData();

       for (var i = 0; i < insertFileArray.length; i++) {
           formData.append("files", insertFileArray[i]);
       }

       formData.append("idx", noticeBoardIdx);

       $.ajax({
           url: "http://localhost:8081/api/notice-boards/attachedFile",
           type: "post",
           data: formData,
           dataType: "text",
           enctype: 'multipart/form-data',
           processData: false,
           contentType: false,
           async: false,
       })
           .done(function (msg) {
               location.href = "/notice-board?idx=" + noticeBoardIdx;
           })
           .fail(function (msg) {
               deleteNoticeBoard(noticeBoardIdx);
           });

   });
</script>

<script th:if="${noticeBoardDto?.idx}" th:inline="javascript">
   $('#update').click(function () {
       var jsonData = $("#form").serializeObject();
       var idx = document.getElementsByName("idx")[0].value;

       // 게시글 수정
       $.ajax({
           url: "http://localhost:8081/api/notice-boards/" + document.getElementsByName("idx")[0].value,
           type: "put",
           data: JSON.stringify(jsonData),
           dataType: "text",
           contentType: "application/json",
           async: false,
       })
           .done(function (msg) {
               exit = false;

           })
           .fail(function (msg) {
               exit = true;
           });

       if (exit) return false;

       // 만일 파일이 수정되지 않은 경우 '파일 업로드' 및 '파일 삭제'를 수행하지 않음
       if (insertFileArray.length == 0 && deleteFileArray.length == 0) {
           location.href = '/notice-board?idx=' + document.getElementsByName("idx")[0].value;
       }

       // 파일 삭제
       if (deleteFileArray.length > 0) {
           $.ajax({
               url: "http://localhost:8081/api/notice-boards/attachedFile",
               type: "delete",
               data: JSON.stringify(deleteFileArray),
               contentType: "application/json",
               async: false,
           })
               .done(function (msg) {
                   console.log("AttachedFile delete success.");
                   exit = false;
               })
               .fail(function (msg) {
                   console.log("AttachedFile delete fail.");
                   exit = true;
               });
       }

       if (exit) return false;

       if (insertFileArray.length <= 0) location.href = "/notice-board?idx=" + document.getElementsByName("idx")[0].value;

       // 파일 업로드
       var formData = new FormData();

       for (var i = 0; i < insertFileArray.length; i++) {
           formData.append("files", insertFileArray[i]);
       }

       formData.append("idx", idx);

       $.ajax({
           url: "http://localhost:8081/api/notice-boards/attachedFile",
           type: "post",
           data: formData,
           dataType: "text",
           enctype: 'multipart/form-data',
           processData: false,
           contentType: false,
           async: false,
       })
           .done(function (msg) {
               location.href = "/notice-board?idx=" + document.getElementsByName("idx")[0].value;
           })
           .fail(function (msg) {

           })

   });
</script>

</body>
</html>
```

<br>
- 업로드된 파일 데이터를 클릭하면 api 서버가 요청에 응답하여, 파일을 다운로드 한다.

```
module-app-web/src/main/resources/templates/noticeBoard/read.html
```

```html
<tr>
   <th>Uploaded Attached File</th>
   <td>
       <div id="attachedFileList" th:each="attachedFile : *{attachedFileList}">
           <span th:attr="onclick=|location.href='http://localhost:8081/api/attachedFiles/download/${attachedFile.savedFileName}'|"
                 th:text="${attachedFile.fileName} + ',&nbsp;' + 'File size: ' + ${attachedFile.fileSize}"></span>
       </div>
   </td>
</tr>
```



## Util
- Java: 파일 업로드할 때 저장되는 파일의 크기 단위를 변경한다.

```
module-system-common/src/main/java/kr/ac/univ/util/FileUtil.java
```

```java
package kr.ac.univ.util;

import java.text.DecimalFormat;

public class FileUtil {
   public static String getExtension(String fileName) {
       return (fileName.substring(fileName.lastIndexOf("."))).toLowerCase();
   }

   public static String convertFileSize(long fileSize) {
       String retFormat = "0";
       String[] s = { "bytes", "KB", "MB", "GB", "TB", "PB" };
       DecimalFormat df = new DecimalFormat("#,###.##");

       if (fileSize != 0) {
           int idx = (int) Math.floor(Math.log(fileSize) / Math.log(1024));
           double ret = ((fileSize / Math.pow(1024, Math.floor(idx))));
           retFormat = df.format(ret) + " " + s[idx];
       } else {
           retFormat += " " + s[0];
       }

       return retFormat;
   }

}
```

<br>
- Javascript: 업로드 하는 파일의 크기 단위를 변경한다.(Byte 단위를 KB, MB 단위로 변경한다.)

```
module-app-web/src/main/resources/static/js/fileUtil.js
```

```javascript
/* 파일 크기 변환 */
function convertFileSize(fileSize) {
   var retFormat = "0";
   var s = ['bytes', 'KB', 'MB', 'GB', 'TB', 'PB'];
   var e = Math.floor(Math.log(fileSize) / Math.log(1024));

   if (fileSize != 0) {
       retFormat = (fileSize / Math.pow(1024, e)).toFixed(2) + " " + s[e];
   } else {
       retFormat = fileSize + " " + s[0];
   }

   return retFormat;
};
```

<br>
- Javascript: fileUtil.js 파일을 다른 파일에서 사용할 수 있도록 포함시킨다. 

```
module-app-web/src/main/resources/templates/layout/script.html
```

```html
...
<script th:src="@{/js/fileUtil.js}"></script>
...
```



## 프로젝트 실행 결과
- NoticeBoard form 페이지에서 파일을 업로드 하면 다음 이미지 처럼 프로젝트의 upload 폴더에 파일이 업로드 된다.

![image](/assets/images/2020-08-05-Project Lab8/image1.png)

![image](/assets/images/2020-08-05-Project Lab8/image2.png)

<br>
- 드래그앤드랍으로 파일을 이동시키는 경우 파일 업로드가 된다.
- 첨부 파일의 삭제(X 버튼 클릭) 클릭한 경우 업로드 하는 파일이 취소되며, 해당 상태에서 Update 버튼을 클릭하면 upload 폴더에 실제 파일이 삭제된다.

![image](/assets/images/2020-08-05-Project Lab8/image3.png)

![image](/assets/images/2020-08-05-Project Lab8/image4.png)
