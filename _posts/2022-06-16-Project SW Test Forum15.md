---
title: "Project SW Test Forum 15. 유지보수 - 3"
excerpt: "유지보수 현황을 소개한다."

categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2022-06-16
layout: post
---
- 유지보수 현황을 소개한다.
- branch는 master branch와 hanwha branch 총 두 개로 나누어서 관리한다.
- hanwha branch에는 ‘직급 정보 변경’, ‘코딩 규칙에 해당 되는 도구 규칙명을 출력하도록 UI 변경’을 적용하였다.
- github: <https://github.com/scribnote5/sw_test_forum>



## feat: 리스트 페이지에서 다른 페이지로 이동시 정보 유지 구현
- 리스트 페이지에서 다른 페이지로 이동시 검색 정보, 페이지 정보를 유지함
- vuex로 구현



## feat: 코딩 규칙에 해당 되는 도구 규칙명을 출력하도록 UI 변경 
- 코딩 규칙에 해당 되는 도구 규칙명을 출력하도록 UI 변경
- hanwha branch 만 적용



## feat: ckeditor 'FindAndReplace' 플러그인 추가
- ckeditor 'FindAndReplace' 플러그인 추가



## fix: MisraCppExampleComment 오류 해결
- 잘못된 Listner 등록으로 이슈 발생 해결



## fix: MisraCppExampleUpdate에서 코드 강조 기능 오류 해결
- 코드를 강조하기 위한 배열을 전달하지 않은 것을 수정



## chore: 자바, 자바스크립트 최신 라이브러리 버전으로 변경
- 자바, 자바스크립트 최신 라이브러리 버전으로 변경



## chore: localhost url cors에 등록
- localhost url cors에 등록



## chore: 잘못되거나 불필요한 코드 수정
- 이외 사소한 오류 수정 및 코드 리펙토링