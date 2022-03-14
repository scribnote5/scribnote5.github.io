---
title: "Project SW Test Forum 6. CRUD 게시판 구현 및 오류 해결(프론트엔드) - 1"
excerpt: "- Vue.js 3와 Spring boot로 CRUD 게시판 구현 및 오류 해결 과정을 소개한다.  "
categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2022-02-02
layout: post
---
- Vue.js 3와 Spring boot로 CRUD 게시판 구현 및 오류 해결 과정을 소개한다.
- github: <https://github.com/scribnote5/sw_test_forum>



## 프로젝트 레이아웃 변경
- Bootstrap 공식 홈페이지에 있는 예제 프로젝트를 참고하여 반응형 레이아웃을 개발 하였으며, 이중 Header, Sidebar, Footer 템플릿을 사용하였다.

출처: <https://getbootstrap.com/docs/5.0/examples/>



## Vue.js 환경 변수 설정
- 프론트엔드 서버와 백엔드 서버 간의 비동기 통신을 위해서 사용하는 백엔드 서버 주소는  개발 환경과 운영 환경에서 다르다.
- 실행될 때 호출되는 벡엔드 서버 주소를 다르게 호출하기 위해서는 Vue.js 환경 변수를 사용해야 한다.

- 각 환경에 따른 .env 파일을 생성한다.

```
<module-app-web\front\.env.local>

NODE_ENV=local
BASE_URL=/login
VUE_APP_MODULE_APP_API_URL=http://localhost:8082
```

```
<module-app-web\front\.env.prod>

NODE_ENV=prod
BASE_URL=/login
VUE_APP_MODULE_APP_API_URL=http://서버주소:8082
```

- 터미널에서 프론트엔드 서버를 환경에 맞게 실행한다.

```bash
# local 환경에서 수행
$ npm run local
```

- ‘process.env.환경변수 이름’으로 환경 변수에 접근할 수 있다.

```javascript
<module-app-web\front\src\components\cwe\cwe\CweList.vue>

// ...

// onBeforeMount, init
onBeforeMount(async () => {
 fireSuccessToast("cwe");

 await searchList({"page": 1});

 await axios.get(process.env.VUE_APP_MODULE_APP_API_URL + "/api/cwe/list-access-authority",
     {},
 )
     .then((response) => {
       access.value = response.data;
     })
     .catch((error) => {
       parseErrorMsg(error.response);
     })
     .then(() => {
     });
})

// ...
```

출처: <https://velog.io/@skyepodium/vue-%EC%8B%A4%ED%96%89-%EB%AA%A8%EB%93%9C%EC%99%80-%ED%99%98%EA%B2%BD-%EB%B3%80%EC%88%98-%EC%84%A4%EC%A0%95>



## Vue.js Composition API 적용
- 재사용성과 가독성을 높이기 위해서 Vue.js 3에서 새로 도입된 Composition API를 적용하였다.

출처:
<https://kyounghwan01.github.io/blog/Vue/vue3/composition-api/#composition-api%E1%84%80%E1%85%A1-%E1%84%82%E1%85%A1%E1%84%8B%E1%85%A9%E1%84%80%E1%85%A6-%E1%84%83%E1%85%AC%E1%86%AB-%E1%84%87%E1%85%A2%E1%84%80%E1%85%A7%E1%86%BC>



## Vue 전역 Sass 변수 설정
- 다음 명령어로 sass-loader를 설치할 수 있다.

```console
$ npm install sass-loader sass webpack --save-dev
```

- 본 프로젝트에서는 sass-loader가 8버전을 사용하므로, prependData proeprty를 사용하여 전역에서 scss 파일을 include 하였다.

```javascript
<module-app-web\front\vue.config.js>

module.exports = {
   css: {
       loaderOptions: {
           sass: {
               prependData:
                   `
                       @import "@/assets/css/variable.scss";
                       @import "@/assets/css/general.scss";
                       @import "@/assets/css/layout.scss";
                   `
           }
       }
   }
}
```

출처: <https://m.blog.naver.com/mgveg/221900939600>



## axios 설정
- main.js에서는 다음과 같이 전역으로 axios csrf 전달 옵션, JWT 토큰 전달 옵션, 로딩 바를  설정하였다.
- 로딩 바는 각 컴포넌트 상단에 위치한다. axios 인터셉터를 사용하여 요청이 시작할 때 로딩 바가 생성되며  종료될 때 로딩 바가 사라진다.

```javascript
<module-app-web\front\src\main.js>

// axios 설정
axios.defaults.xsrfCookieName = 'XSRF-TOKEN' // csrf 기본 설정을 명시적으로 선언
axios.defaults.xsrfHeaderName = 'X-XSRF-TOKEN' // csrf 기본 설정을 명시적으로 선언
axios.defaults.withCredentials = true; // 다른 origin에 JWT를 전달하기 위한 설정
// axios.defaults.headers.common['Access-Control-Allow-Origin'] = '*'; // cors 설정
// axios.defaults.headers.common['Access-Control-Allow-Headers'] = 'Origin, Content-Type, X-Auth-Token'; // cors 설정

// 요청 인터셉터 추가
axios.interceptors.request.use(
   (config) => {
       // 요청을 보내기 전에 수행할 일
       document.getElementById("loading-wrapper").style.visibility = "visible";
       return config;
   },
   (error) => {
       // 오류 요청을 보내기전 수행할 일
       document.getElementById("loading-wrapper").style.visibility = "hidden";
       return Promise.reject(error);
   });

// 응답 인터셉터 추가
axios.interceptors.response.use(
   (response) => {
       document.getElementById("loading-wrapper").style.visibility = "hidden";
       // 응답 데이터를 가공
       return response;
   },
   (error) => {
       // 오류 응답을 처리
       document.getElementById("loading-wrapper").style.visibility = "hidden";
       return Promise.reject(error);
   });
```

```html
<module-app-web\front\src\components\common\Loading.vue>

<template>
 <div class="container-fluid">
   <div id="loading-wrapper">
     <img :src="require(`@/assets/images/loading.gif`)" id="loading" alt="Loading">
   </div>
 </div>
</template>

<style lang="scss">

#loading-wrapper {
 position: fixed;
 left: 50%;
 top: 50%;
 transform: translate(-50%, -50%);
 z-index: 1000;
 visibility: hidden;
}
</style>

<script>
// vue.js

export default {
 name: "Loading"
}
</script>
```

출처: <https://jess2.xyz/vue/axios/><br>
<https://heewon26.tistory.com/103>



## 동적으로 img 태그 생성할 때 img src의 절대 경로를 불러오는 방법
- 파일 드래그 앤 드랍 기능을 개발할 때, 동적으로 생성되는 첨부 파일의 취소 이미지 파일을 img 태그의 src를 바인딩하여 불러 올 수 없다.
- public 폴더에 위치된 정적한 assets 들은 webpack으로 복사되지 않으므로, 절대 경로를 통하여 접근할 수 있다.

![image](/assets/img/2022-02-02-Project SW Test Forum6/image1.png)

- 하단 출처를 참고하여, 동적으로 img 태그가 생성되는 경우, public 폴더에 위치시킨 다음 img 파일을 호출하였다.

```javascript
<module-app-web\front\src\components\common\FileUpload.vue>

const tag = '<div id="uploadFileId' + tempUploadFileId + '" + class="d-flex">'
   + '<span class="d-flex align-items-center">'
   + file.name + ",&nbsp; 파일 크기: " + convertFileSize(file.size)
   + '</span>'
   + '<img id="cancelFileIcon' + tempUploadFileId + '" src="/x-circle-main-black.svg" class="ms-2">'
   + '</div>';
```

![image](/assets/img/2022-02-02-Project SW Test Forum6/image2.png)

출처: <https://cli.vuejs.org/guide/html-and-static-assets.html#the-public-folder>



## 자바스크립트: 일반 함수를 모두 화살표 함수로 변경
- 자바스크립트 함수 표현식 보다 화살표 함수를 사용하면 조금 더 간결하게 함수를 만들 수 있다. 따라서 화살표 함수로 모두 대체 하였다.

```javascript
 await axios.get(process.env.VUE_APP_MODULE_APP_API_URL + "/api/misra-cpp/list-access-authority",
     {},
 )
  .then(function (response) {
    // ...
  })
  .catch(function (error) {
    // ...
  })
  .then(function () {
    // ...
  });
```

```javascript
 await axios.get(process.env.VUE_APP_MODULE_APP_API_URL + "/api/misra-cpp/list-access-authority",
     {},
 )
     .then((response) => {
    // ...
     })
     .catch((error) => {
    // ...
     })
     .then(() => {
    // ...
     });
})
```

출처: <https://ko.javascript.info/arrow-functions-basics><br>
<https://ko.javascript.info/arrow-functions-basics>



## Vue.js TypeError: Cannot read property of undefined 오류
- 백엔드 서버에서 수신 받은 JSON key와 자바스크립트 객체 property가 매핑되지 않거나 객체 property가 선언되지 않으면, vue에서  브라우저로 렌더링 할 때 발생하는 오류다.
- 따라서 자바스크립트 객체 property를 JSON key와 매핑되도록 선언해야 한다.

```html
<module-app-web\front\src\components\admin_page\notice\NoticeRead.vue>

<tr>
 <td colspan="2">
   <div class="float-end">
     <span class="float-end">
       <strong class="additional-information-title">작성자: </strong><span class="additional-information-content">{{ notice.createdByUser.department }} {{ notice.createdByUser.name }}, </span>
       <strong class="additional-information-title">작성일: </strong><span class="additional-information-content">{{ notice.createdDate }}</span><br>

       <strong class="additional-information-title">최종 수정자: </strong><span class="additional-information-content">{{ notice.lastModifiedByUser.department }} {{ notice.lastModifiedByUser.name }}, </span>
       <strong class="additional-information-title">최종 수정일: </strong><span class="additional-information-content">{{ notice.lastModifiedDate }}, </span>
       <strong class="additional-information-title">조회수: </strong> <span class="additional-information-content">{{ notice.views }}</span>
     </span>
   </div>
 </td>
</tr>


// ...

setup() {
  // variable
  let notice = ref(); // 기존
  let notice = ref({createdByUser: {department: '', name: ''}, lastModifiedByUser: {department: '', name: ''}});
 // 수정
}
```



## Vue.js 라우트 메타 필드
- 라우트 메타 필드를 사용하여 웹 페이지 제목, 네비게이션 가드, 레이아웃을 설정하였다.


### 웹 페이지 제목
- DOM이 업데이트 된 후 실행되는 nextTick을 사용하여, 컴포넌트가 변경되는 경우 메타 필드 title 속성 값으로 웹 페이지 제목을 변경한다.


### 네비게이션 가드
- 로그인하지 않은 사용자가 로그인이 필요한 URI에 접근하거나 존재하지 않는 URI로 접근할 때 메타 필드 authRequired 속성 값으로 접근을 허용하지 않도록 구현하였다.
- vueCookies를 사용하여 로그인 한 사용자가 login 페이지로 이동하는 경우 경고창을 띄어서 원래 페이지로 이동하도록 구현하였다.


### 레이아웃 구성
- 로그인 페이지는 Header, Sidebar, Footer을 사용하여 템플릿을 구성하지 않는다. 라우트에서 meta 필드의 layoutView 속성 값을 사용하여 Header, Sidebar, Footer 레이아웃 적용 여부를 구분하였다.

```javascript
<module-app-web\front\src\router\index.js>

const routes = [
   {
       path: '/login',
       name: 'Login',
       component: Login,
       meta: {title: 'SW Test Forum - 로그인', layoutView: false, authRequired: false}
   },
   {
       path: '/dashboard',
       name: 'Dashboard',
       component: Dashboard,
       meta: {title: 'SW Test Forum - 대시보드', layoutView: true, authRequired: true}
   },

// ...
]

router.beforeEach(async function (to, from, next) {
   // page title 설정
   //nextTick은 Dom이 업데이트 된 후 실행
   nextTick(() => {
       document.title = to.meta.title;
   });

   // 잘못된 URI로 매핑되는 경우, Error404
   if (to.name === 'Error404') {
       await error.fire({
           text: "페이지를 찾을 수 없습니다.",
       });

       router.go(-1);
   }
   // to: 이동할 url에 해당하는 라우팅 객체
   else if (
       to.matched.some((routeInfo) => {
           return routeInfo.meta.authRequired;
       })) {
       if (vueCookies.get('isHasToken')) {
           next();
       }
       // 권한이 없는 경우, Error401
       else {
           await error.fire({
               text: "로그인이 필요합니다.",
           });

           next('/login');
           //router.go(-1);
       }
   } else {
       if (vueCookies.get('isHasToken')) {
           await warning.fire({
               text: "로그아웃을 하신 다음, 다른 계정으로 로그인 해주세요.",
           });

           next('/dashboard');
       } else {
           next();
       }
   }
});

export default router
```

```html
<module-app-web\front\src\App.vue>

<template>
 <body v-if="this.$route.meta.layoutView === true">
 <Header/>
 <main>
   <Sidebar/>
   <router-view/>
 </main>
 <Footer/>
 </body>

 <body v-else>
 <router-view/>

 </body>
</template>
```

출처: <https://joshua1988.github.io/web-development/vuejs/vue-router-navigation-guards/>



## Vue.js 브러우저 뒤로가기 클릭시 스크롤 위치
- 브라우저에서 뒤로가기 버튼을 클릭하면 브라우저의 scroll 위치가 최상단으로 변경된다.
- 브라우저 scroll 위치가 현재 위치로 고정하도록 변경하였다.

```javascript
<module-app-web\front\src\router\index.js>

const router = createRouter({
   history: createWebHistory(process.env.BASE_URL),
   routes,
   scrollBehavior(to, from, savedPosition, popstate) {
       //브러우저 뒤로가기 버튼을 클릭하는 경우, scroll 위치를 변경하지 않음
       if (isEmpty(savedPosition)) { // isEmpty는 사용자 정의 함수로 savedPosition 객체가 비어있는 경우 최상단으로 scroll 이동
           return new Promise((resolve, reject) => {
               resolve({left: 0, top: 0})
           })
       } else {
           return new Promise((resolve, reject) => {
               behavior: 'smooth', resolve({left: savedPosition.left, top: savedPosition.top})
           })
       }
   }
})
```



## 유효성 검사
- 프론트엔드와 백엔드 모두 유효성 검사 로직을 구현 하였다.
- 백엔드로 데이터를 송신 전, 각 데이터에 해당되는 유효성 검사 로직이 수행된다. 만약 유효성 검사에 실패하면, input 태그에 focus가 맞춰지며, input 태그 하단에 에러 메시지를 출력한다.

![image](/assets/img/2022-02-02-Project SW Test Forum6/image4.png)

```javascript
<module-app-web\front\src\utils\validation-util.js>

// ...

/* 길이 및 공백 validation */
const validateLengthAndIsEmpty = (name, value) => {
   let result;
   let errorMessage = document.getElementById(name + "ErrorMessage");

   if (!validateWhiteSpace(value)) {
       result = "공란이 될 수 없습니다.";
       document.getElementsByName(name)[0].focus();
   } else if (value.length > 255) {
       result = "길이는 255 보다 작아야 합니다. \n"
           + "(현재 입력된 길이: " + value.length + ")";
       document.getElementsByName(name)[0].focus();
   } else {
       result = "";
   }

   errorMessage.innerText = result;

   return isEmpty(result);
}

// ...
```

```html
<module-app-web\front\src\components\cwe\cwe\CweWrite.vue>

<tr>
 <th>제목<span class="required-field">*</span><span class="auto-completed-field">*</span></th>
 <td style="overflow: visible">
   <div class="autoComplete_wrapper">
     <input type="text" name="title" id="title" v-model="title" class="form-control" placeholder="[RTE_Buffer_Overrun] 배열 최대 범위보다 큰 요소 접근을 금지한다.">
     <p id="titleErrorMessage" class="error-message"></p>
   </div>
 </td>
</tr>

if (!(validateLengthAndIsEmpty("title", title.value)
   && validateLength("hashTags", hashTags.value)
   && validateLength("language", language.value)
   && validateLength("cweId", cweId.value)
)) {
 return false;
}
```



## Vue.js 공통 컴포넌트 분리
- 중복하여 많이 사용하는 레이아웃 및 기능들은 컴포넌트로 생성하여 common 폴더에 위치 시켰다. 해당 컴포넌트들은 다른 컴포넌트에 등록되어 사용된다.

![image](/assets/img/2022-02-02-Project SW Test Forum6/image5.png)

-  기능상 컴포넌트 및 자바스크립트 파일에서 많이 사용하는 객체 및 함수는 plugins 폴더에 위치 시켰다. 해당 파일들은 다른 파일에서 사용된다.

![image](/assets/img/2022-02-02-Project SW Test Forum6/image6.png)
