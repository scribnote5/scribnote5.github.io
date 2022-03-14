---
title: "Project SW Test Forum 2. Vue.js 환경 구축 및 프로젝트 구성 - 1"
excerpt: "Vue.js 환경 구축 및 프로젝트 구성 과정을 소개한다."
categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2021-07-10
layout: post
---
- Vue.js 환경 구축 및 프로젝트 구성 과정을 소개한다.
- github: <https://github.com/scribnote5/sw_test_forum>



## Vue.js란?
- Vue.js는 프론트엔드 프레임워크로서, 컴포넌트(Component) 기반의 SPA(Single Page Application)를 구축할 수 있게 하는 프레임워크다.


### 컴포넌트(Component)
- 웹을 구성하는 로고, 메뉴바, 버튼, 모달 창 등 웹 페이지 내의 다양한 UI 요소
- 재사용 가능하도록 구조화 한 것


### SPA(Single Page Application)
- 단일 페이지 애플리케이션으로, 하나의 페이지 안에서 필요한 영역 부분만 로딩되는 형태
- 빠른 페이지 변환
- 적은 트래픽 양



## Vue.js 3를 선택한 이유는?
- Vue.js가 다른 SPA 프레임워크(React, Angular) 보다 학습 곡선이 낮고, 성능도 우수하다고 하며, React 다음으로 많은 관심을 받고 있는 프로젝트이기 때문이다.
- 최신 릴리즈된 Vue.js 3는 기존 버전의 한계점을 해결하고자 노력하였다. 특히 기존 버전의 코드 복잡성을 해결하기 위한 Composition API 도입은 새로운 버전을 선택한 가장 큰 계기다. 새로운 버전인 만큼 구글링을 통하여 얻는 자료에 한계가 있지만, 처음 배우는 만큼 새로운 버전을 선택하였다.
- 최신 Vue.js 3 개발 가이드라인을 준수하여 개발할 것을 목표로 한다.

출처:
<https://joshua1988.github.io/web-development/vuejs/vue3-coming/#vuejs%EC%9D%98-%EA%B8%B0%EC%A1%B4-%ED%95%9C%EA%B3%84%EC%A0%90---%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EC%BD%94%EB%93%9C-%EC%9E%AC%EC%82%AC%EC%9A%A9><br>
<https://www.s-core.co.kr/insight/view/vue-js-3-0-%EB%AC%B4%EC%97%87%EC%9D%B4-%EB%8B%AC%EB%9D%BC%EC%A1%8C%EB%8A%94%EA%B0%80/>



## 공부한 Vue.js youtube 인강
- 하단의 두 개의 인강을 통하여 Vue.js의 기본 지식을 공부하였다.
- Vue.js 책 한 권을 또는 공식 홈페이지 튜토리얼을 모두 공부한 다음 개발하는 방식이 아닌, 직접 개발하면서 모르는 부분은 구글링을 통해서 찾아본 다음 개발하는 방식으로 진행할 예정이다.

출처: <https://www.youtube.com/watch?v=sqH0u8wN4Rs><br>
<https://www.youtube.com/playlist?list=PLfLgtT94nNq3Br68sEe26jkOqCPK_8UQ->



## Vue.js 3 설치 및 설정
- node.js 설치 후 npm을 설치한다.
- 다음 하단과 같이 진행한다.

```bash
# 최신 버전으로 업데이트
$ npm install -g npm

# vue 설치
$ npm i -g @vue/cli
$ npm i -g @vue/cli-init

# vue 버전 확인
$ vue --version

# module-app-web경로로 이동
$ cd module-app-web

# vue 프로젝트 생성
$ vue create front

# Manually select features 클릭

# Choose Vue version, Babel, Router, Vuex, CSS Pre-procesor 선택
# Linter / Formatter는 선택하지 않는 것을 권고(코드 포맷을 검사하는 설정으로 자잘한 버그가 많으며, 수정하기 곤란한 부분도 존재)

# 다음과 같이 선택 후 클릭
# ? Please pick a preset: Manually select features
# ? Check the features needed for your project: Choose Vue version, Babel, Router, Vuex, CSS Pre-processors
# ? Choose a version of Vue.js that you want to start the project with 3.x
# ? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
# ? Pick a CSS pre-processor (Postcss, Autoprefixer and CSS Modules are supported by default): Sass/SCSS (with node-sass)
# ? Where do you prefer placing config for Babel, ESLint, etc.? In package.json
# ? Save this as a preset for future projects? No

# 서버 실행 후, http://localhost:8080 에서 Vue.js 서버 페이지를 확인 가능
$ cd front
$ npm run serve
```



## Vue.js에서 사용하는 핵심 모듈
- Vue.js에서 사용하는 핵심 모듈은 다음과 같다.

- vuex: Vue.js의 store 관리를 위한 모듈
- vee-validate: Form 검증을 위한 모듈
- vue-router: 해당 부분만 화면을 갱신하는 사용하는 모듈
- axios: HTTP 비동기 통신을 위한 모듈(API 서버와의 통신)



### Vue.js 프로젝트 구조
- Vue.js 프로젝트를 어떤 방식으로 구성할지 고민을 많이 하였는데, 하단 출처의 'vue-enterprise-boilerplate’ 프로젝트를 참고하여 구성하였다.
- 해당 프로젝트 구조는 Vue의 기본 규칙을 가장 잘 따른 프로젝트 라고 하며, 현재 프로젝트 규모에 적용하기 알맞은 구조라고 생각한다.
- 여담으로 vuesion 프로젝트를 참고하여 프로젝트 구조를 구성하려고 하였지만 프로젝트 규모가 대규모가 아니므로 적합하지 않다고 판단하였다.
- Vue.js 프로젝트는 Spring Boot 프로젝트 module-app-web 모듈의 front 폴더에 위치하도록 하였다.

![image](/assets/img/2021-07-10-Project SW Test Forum2/image1.png)

출처: <https://velog.io/@cindy-choi/VUE-%EC%9A%B0%EC%95%84%ED%95%9C-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EA%B5%AC%EC%A1%B0-%EC%A7%9C%EA%B8%B0>
