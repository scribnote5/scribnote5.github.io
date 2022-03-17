---
title: "Project SW Test Forum 13. 유지보수 - 1"
excerpt: "유지보수 현황을 소개한다."

categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2022-02-08
layout: post
---
- 유지보수 현황을 소개한다.
- 프로젝트에 MISRA C++ 규칙을 입력하는 도중 다양한 이슈를 발견하고 유지보수 하였다. 테스트에 많은 시간을 투자하지 못하였기에, 사용 도중에 예기치 못한 이슈가 많이 발견되었다. 역시 테스트에 많은 시간을 투자하여 이슈를 찾는 것이 중요하다.(QA팀이 있는 이유)



## feat: 비밀번호 문의 구현
- 로그인 페이지에서 '비밀번호 문의'를 선택하면 관리자 권한 사용자의 간략한 정보와 메일 주소를 출력



## fix: 가이드라인 결과 출력 수정
- 가이드라인 결과에서 ','가 잘못 출력 되는 부분 수정



## refactor: 폰트 저장소 변경
- 웹 폰트에서 로컬로 변경



## chore: / 경로 이동 redirect
- / 경로 이동시 /login으로 redirect



## chore: ckeditor content 변경
- ckeditor 규칙 설명 문구 변경



## chore: JWT 유효기간 변경
- 하루에서 한 달로 변경



## chore: Spring boot XSS filter(HTML escape) 제거
- Vue에서는 XSS 공격 대비를 위한 HTML escape가 이미 구현되어 있으므로, Spring boot HTML escape 코드 주석 처리



## fix: 데이터 기록 검색 기능 오류 수정
- 게시글 수정할 때 auditType 잘못 입력되는 것 수정
- 데이터 기록 검색 기능이 로그인 기록 검색 기능으로 구현된 오류 수정



## fix: get 방식 검색 오류 수정
- 특수 문자 입력 후 검색하면, 특수 문자가 누락되는 오류 encodeURIComponet 함수 사용하여 해결



## feat: 검색 편의성 기능 구현
- 검색어 입력 후 엔터키를 입력하면 검색되는 기능 구현
