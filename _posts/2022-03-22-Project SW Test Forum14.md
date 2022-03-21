---
title: "Project SW Test Forum 14. 유지보수 - 2"
excerpt: "유지보수 현황을 소개한다."

categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2022-03-22
layout: post
---
- 유지보수 현황을 소개한다.
- MISRAMISRA C++ 규칙을 입력하는 도중 다양한 이슈를 발견하고 유지보수 하였다.
- 프로젝트에 MISRA C++ 규칙을 입력하는 도중 다양한 이슈를 발견하고 유지보수 하였다.
- github: <https://github.com/scribnote5/sw_test_forum>



## feat: 좋아요 기능 구현
- 규칙 가이드라인 페이지에서 좋아요 기능 구현, 가이드라인은 좋아요 내림차순으로 정렬
- 규칙 가리드라인 리스트 페이지에서 ‘좋아요(하트)’ 갯수를 확인할 수 있다.

![image](/assets/img/2022-03-22-Project SW Test Forum14/image1.png)

- 규칙 가리드라인 읽기 페이지에서 ‘좋아요(하트)’ 갯수를 확인할 수 있고, ‘좋아요’와 ‘좋아요 취소’를 할 수 있다.

![image](/assets/img/2022-03-22-Project SW Test Forum14/image2.png)



## fix: 예제 및 가이드라인 목록 버튼 선택 시 잘못된 경로 이동 수정
- 규칙 읽기 페이지 -> 예제 및 가이드라인 게시글 선택 -> 목록 버튼 선택 시 전체 목록 페이지 이동을 해당 규칙 예제 및 가이드라인으로 이동



## style: 비어있는 해시태그 스타일 적용
- 해시태그에도 스타일 적용



## fix: 백엔드 HTML escape 적용 해제에 따른 댓글 코드 수정
- Vue에서는 XSS 공격을 대비하기 위한 HTML escape를 이미 적용하고 있으므로, 백엔드 HTML escape 적용 해제
- 이에 따라, 댓글 등록시 XSS 취약점이 발생하지 않도록 코드 수정

출처: <https://stackoverflow.com/questions/54979287/replace-n-to-new-line-on-vuejs>



## chore: 잘못된 코드 수정
- 규칙 리스트 페이지 잘못된 출력, 리스트 정렬 중복 수정, 잘못된 변수 사용, 태그 및 스타일 중복 사용
