---
title: "Project SW Test Forum 16. 유지보수 - 4"
excerpt: "유지보수 현황을 소개한다."

categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2022-09-11
layout: post
---
- 유지보수 현황을 소개한다.
- 프로젝트 유지보수 하고 추가 기능들을 구현하였다.
- github: <https://github.com/scribnote5/sw_test_forum>



## fix: 규칙 페이지에서 제목을 규칙명으로 변경
- 제목을 규칙명, 원제를 영어 규칙명으로 변경



## feat: 대시보드 페이지 정적시험 규칙 현황 개발
- 대시보드에서 출력되는 정적시험 규칙 현황에 모든 규칙 정보 현황 출력되도록 개발



## feat: 질문 답변 페이지 개발
- 질문 답변 페이지 개발



## feat: 규칙 페이지 입력 항목 추가
- 다른 도구에서 지원하는 규칙 정보 입력할 수 있도록 추가
- qac 규칙 제목 길이가 긴 경우 보여주기 감추기 기능 제공



## feat: 도구 사용 방법, 환경 설정 페이지 개발
- 사이드바 도구 탭에 사용 방법, 환경 설정, 트러블 슈팅 페이지 위치
- 도구 사용 방법, 환경 설정 페이지 개발
- 트러블 슈팅 게시판에서 사용하는 불필요한 테이블 삭제



## fix: 파일 확장자가 없는 파일 업로드시 오류
- 파일 확장자가 없는 파일의 경우, 확장자명 검사를 하지 않도록 로직 변경



## fix: 게시글 형상 변경시 audit 게시판에 잘못된 auditType으로 작성되는 오류
- audit 게시판에 정상적인 auditType으로 게시글이 등록되도록 수정



## style: sidebar 고정
- main 레이아웃 이동시에도 sidebar가 이동하지 않고 고정됨



## style: header 높이 조정
- header 70px -> 55px로 조정



## chore: 자바스크립트 최신 라이브러리로 버전 업데이트
- 자바스크립트 최신 라이브러리로 버전 업데이트



## chore: JPA getById 메소드 변경
- JPA getById 메소드가 deprecated 됨에 따라 getReferenceById 메소드로 변경



## chore: 이외 기타 사항 수정
- parseErrorMsg -> parseApiErrorMsg로 변경



## feat: Java Code Convetion 규칙 페이지 개발
- Java Code Convetion 규칙 페이지 개발



## feat: CWE Java 규칙 페이지 개발
- 기존 CWE 규칙 페이지를  CWE C/C++ 규칙 페이지로 변경하고, CWE Java 규칙 페이지 개발



## feat: 도구 사용방법, 도구 트러블슈팅 검색 항목에 도구 유형 추가
- 도구 사용방법, 도구 트러블슈팅 검색 항목에 도구 유형 추가



## style: 검색 입력 칸과 검색 아이콘 margin 변경
- ms-2 -> mx-2



## style: 도구 사용방법, 트러블슈팅 유형을 도구 유형으로 변경
- 도구 사용방법, 트러블슈팅 유형을 도구 유형으로 변경



## style: sidebar 메뉴 수정
- padding 축소
- font-size 축소
- 아이콘 크기 축소



## style: 대시보드 정적시험 규칙 현황 프로그래스 바 색상 변경
- 대시보드 정적시험 규칙 현황 프로그래스 바 색상 변경



## style: breadcrumb 수정
- 반응형 font-size 변경



## style: StyleCop, FxCop 규칙 이름 변경
- C# Coding Convention, .NET Framework Design Guideline 규칙 이름으로 변경



## style: MISRA C compiler placeholder 변경
- G++ 대신 GCC, Qt 5.15 대신 Hardware: TMS320F28xx로 변경



## style: 댓글 한 줄로 길게 출력 되는 버그 수정
- 댓글 한 줄이 너무 길어지는 경우 여러 줄로 출력 되도록 수정



## chore: 잘못된 출력 버그 수정
- 가이드라인 사례 -> 가이드라인 변경, 자동 완성 * 추가, 프론트엔드 오류 메시지 변경 등



## style: 읽기 페이지에서 위배 빈도 뒤에 있는 * 삭제
- 읽기 페이지에서 위배 빈도 뒤에 있는 * 삭제



## chore: ckeditor 템플릿 양식 수정
- ckeditor guideline 템플릿 양식 수정



## chore: 영어 규칙명 제거
- 영어 규칙명 제거



## chore: 지식 저장소 category 분류 추가
- 정적시험, 동적시험 category 분류 추가



## style: ckeditor 5 enter 입력 시<p> 태그 대신 <br> 태그 생성
- ckeditor 5 enter 입력 시 <p> 태그 대신 <br> 태그 생성



## build: application.yml 분리
- application.yml을 프로파일 환경에 따라 여러 의 yml 파일로 분리