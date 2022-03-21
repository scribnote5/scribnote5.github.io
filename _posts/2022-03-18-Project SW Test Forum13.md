---
title: "Project SW Test Forum 13. 유지보수 - 1"
excerpt: "유지보수 현황을 소개한다."

categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2022-03-18
layout: post
---
- 유지보수 현황을 소개한다.
- 프로젝트에 MISRA C++ 규칙을 입력하는 도중 다양한 이슈를 발견하고 유지보수 하였다. 테스트에 많은 시간을 투자하지 못하였기에, 사용 도중에 예기치 못한 이슈가 많이 발견되었다. 역시 테스트에 많은 시간을 투자하여 이슈를 찾는 것이 중요하다.(QA팀이 있는 이유)
- github: <https://github.com/scribnote5/sw_test_forum>



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



## fix: 데이터 기록 검색 기능 오류 수정
- 게시글 수정할 때 auditType 잘못 입력되는 것 수정
- 데이터 기록 검색 기능이 로그인 기록 검색 기능으로 구현된 오류 수정



## fix: get 방식 검색 오류 수정
- 특수 문자 입력 후 검색하면, 특수 문자가 누락되는 오류 encodeURIComponet 함수 사용하여 해결

출처: <https://steady-snail.tistory.com/111>



## feat: 검색 편의성 기능 구현
- 검색어 입력 후 엔터키를 입력하면 검색되는 기능 구현



## feat: 좋아요 기능 구현
- 규칙 가이드라인 페이지에서 좋아요 기능 구현, 가이드라인을 ‘좋아요‘ 내림차순으로 정렬
- 규칙 가리드라인 리스트 페이지에서 ‘좋아요(하트)’ 갯수를 확인할 수 있다.

![image](/assets/img/2022-03-18-Project SW Test Forum13/image1.png)

- 규칙 가리드라인 읽기 페이지에서 ‘좋아요(하트)’ 갯수를 확인할 수 있고, ‘좋아요’와 ‘좋아요 취소’를 할 수 있다.

![image](/assets/img/2022-03-18-Project SW Test Forum13/image2.png)



## fix: 예제 및 가이드라인 목록 버튼 선택 시 잘못된 경로 이동 수정
- 규칙 읽기 페이지 -> 예제 및 가이드라인 게시글 선택 -> 목록 버튼 선택 시 전체 목록 페이지 이동을 해당 규칙 예제 및 가이드라인으로 이동



## style: 비어있는 해시태그 스타일 적용
- 해시태그에도 스타일 적용



## fix: Spring boot XSS filter(HTML escape) 제거
- Vue에서는 XSS 공격 대비를 위한 HTML escape가 이미 구현되어 있으므로, Spring boot HTML escape 코드 주석 처리
- 이에 따라 댓글 등록시 XSS 취약점이 발생하지 않도록 코드 수정

출처: <https://kr.vuejs.org/v2/guide/security.html><br>
<https://stackoverflow.com/questions/54979287/replace-n-to-new-line-on-vuejs>



## chore: 잘못된 코드 수정
- 규칙 리스트 페이지 잘못된 출력, 리스트 정렬 중복 수정, 잘못된 변수 사용, 태그 및 스타일 중복 사용
