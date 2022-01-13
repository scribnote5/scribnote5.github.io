---
title: "Project SW Test Forum 1. Spring Boot 환경 구축 및 프로젝트 구성 - 1"
excerpt: "Spring Boot 환경 구축 및 프로젝트 구성 과정을 소개한다."
categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2021-07-10
layout: post
---
- Spring Boot 환경 구축 및 프로젝트 구성 과정을 소개한다.



## Spring Boot 환경 구축
- Spring Boot 환경 구축 방법은 과거 작성하였던 ‘Project Lab 1-1. 개발 환경 구축(IntelliJ)
' github page 링크로 대체한다.

출처: <https://scribnote5.github.io/web/project%20lab/tool/Project-Lab1-1/>



## mariaDB 설치
- mariaDB 설치 방법은 과거 작성하였던 ‘Project Lab 2. Windows Subsytem for Linux(WSL)에 mariaDB 설치’ github page 링크로 대체한다.

출처: <https://scribnote5.github.io/web/project%20lab/Project-Lab2/>



## Gradle Multi Module 프로젝트 구성
- Gradle Multi Module 프로젝트 구성 방법은 과거 작성하였던 ‘Project Lab 3. Gradle Multi Module 프로젝트 구성’ github page 링크로 대체한다.
- Project Lab에서는 thymeleaf를 사용하였지만, Project SW Test Forum에서는 Vue.js를 사용하여 프론트엔드 환경을 구축하여 프로젝트 구성이 변경되었다. 웹 검색을 통해서 보여주는 프로젝트가 아니므로 module-app-admin 모듈은 module-app-web 모듈로 통합하였다. module-app-web 모듈 내에 front 폴더에 Vue.js 프로젝트가 위치하며, module-web-core에서 다른 모듈과 사용하는 Web Resources(html, css, javascript 등)을 Vue.js 프로젝트로 이동시켰다.

![image](/assets/img/2021-07-10-Project SW Test Forum1/image1.png)

출처: <https://scribnote5.github.io/web/project%20lab/Project-Lab3/>

- 추후 module-app-web 프로젝트 내 Vue.js 프로젝트는 하단 출처를 따라 Spring Boot 프로젝트와 연동시킬 예정이다.

출처: <https://amanokaze.github.io/blog/Vuejs-Setting-with-SB/>
