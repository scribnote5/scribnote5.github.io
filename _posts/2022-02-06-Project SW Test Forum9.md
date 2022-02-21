---
title: "Project SW Test Forum 9. 기타 기능 구현 - 1"
excerpt: "- Vue.js 3와 Spring boot로 다양한 기능 구현 과정을 소개한다. "
categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2022-02-06
layout: post
---
- Vue.js 3와 커스텀 플러그인(에디터 크기 변경) 구현 과정을 소개한다.
- github: <https://github.com/scribnote5/sw_test_forum>



## 해시태그 구현
- 페이스북, 트위터 등에서 사용하는 해시태그를 구현하였다. 게시글의 메타 데이터로 분류 및 검색에 큰 도움이 될 것이라고 생각하였다.
- 해시태그는 ‘#태그 이름’ 으로 등록할 수 있으며, 특수 문자는 사용 할 수 없다. 등록 버튼을 클릭하면 해시태그가 추가되며, x 버튼을 선택하여 등록된 해시태그를 삭제 할 수 있다.

![image](/assets/img/2022-02-06-Project SW Test Forum9/image1.png)

- 등록된 해시태그는 드래그 앤 드랍으로 이동할 수 있다.

![image](/assets/img/2022-02-06-Project SW Test Forum9/image2.png)

- 해시태그는 다음과 같이 출력된다.

![image](/assets/img/2022-02-06-Project SW Test Forum9/image2.png)

- Vue에서 HashTags 컴포넌트로 props에 pageInformation(리스트 페이지인지, 등록 페이지인지, 수정 페이지인지에 대한 정보)와 hashTags(해시태그 데이터)를 전달하면 된다.
- DB에는 ‘#해시태그#해시태그#해시태그’ 데이터 형태로 저장된다.

```
module-app-web\front\src\components\common\HashTags.vue
```

```html
<template>
   <span v-if="pageInformation === 'list'">
     <span v-if="isEmpty(hashTags)">
       #-
     </span>
     <span v-else v-for="(hashTag, i) in hashTags.split('#')" :key="i">
       <span v-if="i === 0" class="hash-tag-in-list">#{{ hashTag }}</span>
     </span>
   </span>

 <div v-if="pageInformation === 'write' || pageInformation === 'update'">
   <div class="autoComplete_wrapper d-flex justify-content-between">
     <div class="d-flex w100-80px">
       <input type="text" name="hashTag" id="hashTag" class="form-control" placeholder="#태그 이름↵(특수 문자 사용 불가)"/>
     </div>
     <div class="d-flex">
       <button @click="hashTagsAddEvent()" class="btn btn-sm btn-outline-main-blue">등록<img :src="require(`@/assets/images/write-main-blue.svg`)" class="ms-2"></button>
     </div>
   </div>

   <div id="hashTagsWrapper" class="my-2 py-1">
     <input type="hidden" name="hashTags" id="hashTags" model="hashTags" class="form-control" readonly/>

     <span v-if="pageInformation === 'update' && !isEmpty(hashTags)" v-for="(hashTag, i) in hashTags.split('#')" :key="i">
       <span draggable="true" :id="'hashTagData' + i">
         <span :id="'hashTagSpace' + i" class="hash-tag-space"></span>
           <span :id="'hashTagContent' + i" class="hash-tag">
             #{{ hashTag }}<img :id="'cancelFileIcon' + i" src="/x-circle-main-black.svg" class="ms-2" @click="cancelHashTagEvent(i)">
           </span>
       </span>
     </span>
   </div>

   <div id="hashTagsStatementWrapper">
     <img :src="require(`@/assets/images/drag.png`)" style="height: 20px"> &nbsp;<span id="hashTagsStatement">해시태그는 드래그 앤 드랍으로 위치를 변경할 수 있습니다.</span>
   </div>
   <p id="hashTagsErrorMessage" class="error-message"></p>
 </div>

 <div v-if="pageInformation === 'read'">
   <span v-if="isEmpty(hashTags)">
       #-
   </span>
   <span v-else v-for="(hashTag, i) in hashTags.split('#')" :key="i">
     <span class="hash-tag">#{{ hashTag }}</span>&nbsp;&nbsp;&nbsp;
   </span>
 </div>
</template>

<style lang="scss">
.hash-tag-in-list {
 font-size: $small-font-size;
 background-color: $darkest-main-white;
 color: $the-darkest-main-grey;
 border-radius: .5rem;
 padding: .3rem;
}

.hash-tag {
 background-color: $darkest-main-white;
 color: $the-darkest-main-grey;
 border-radius: .5rem;
 display: inline-block;
 padding: .3rem;
 margin: .2rem 0;
}

.hash-tag-space {
 padding: .25rem .5rem;
}
</style>

<script>
import {onBeforeMount, onBeforeUnmount} from "vue";
import {isEmpty} from "@/utils/empty-util";

export default {
 name: "HashTags",
 props: {
   pageInformation: String,
   hashTags: String
 }, setup(props) {
   let hashTagId = 0;

   onBeforeMount(async () => {
     if (props.pageInformation === 'write' || props.pageInformation === 'update') {
       document.addEventListener("dragstart", dragstart);
       document.addEventListener("dragend", dragend);
       document.addEventListener("dragenter", dragenter);
       document.addEventListener("dragover", dragover);
       document.addEventListener("dragleave", dragleave);
       document.addEventListener("drop", drop);
     }
   });

   onBeforeUnmount(async () => {
     if (props.pageInformation === 'write' || props.pageInformation === 'update') {
       document.removeEventListener("dragstart", dragstart);
       document.removeEventListener("dragend", dragend);
       document.removeEventListener("dragenter", dragenter);
       document.removeEventListener("dragover", dragover);
       document.removeEventListener("dragleave", dragleave);
       document.removeEventListener("drop", drop);
     }
   });

   /* dragstart event */
   const dragstart = (event) => {
     event.dataTransfer.setData("Text", event.target.id);
     document.getElementById("hashTagsStatement").innerHTML = "드래그 앤 드랍으로 위치를 이동 해주세요.";
     document.getElementById("hashTagsStatement").style.color = "red";
     event.target.style.opacity = "0.4";
   }

   /* dragend event */
   const dragend = (event) => {
     document.getElementById("hashTagsStatement").innerHTML = "해시태그는 드래그 앤 드랍으로 위치를 변경할 수 있습니다.";
     document.getElementById("hashTagsStatement").style.color = "black";
     event.target.style.opacity = "1";
   }

   /* dragenter event */
   const dragenter = (event) => {
     let eventTargetId = event.target.id;

     event.preventDefault();

     // hashTagsWrapper인 경우 hashTag가 가장 마지막으로 이동
     if (event.target.id == "hashTagsWrapper") {
       event.target.style.border = "2px dotted red";
     }
     // hashTagSpace인 경우 hashTag를 hashTagSpace 앞으로 이동
     else if (/[0-9]/g.test(eventTargetId) && /hashTagSpace/.test(eventTargetId)) {
       event.target.style.border = "2px dotted red";
     }
   }

   /* dragover event */
   const dragover = (event) => {
     event.preventDefault();
   }

   /* dragleave event */
   const dragleave = (event) => {
     let eventTargetId = event.target.id;
     event.preventDefault();

     // hashTagsWrapper인 경우 hashTag가 가장 마지막으로 이동
     if (event.target.id == "hashTagsWrapper") {
       event.target.style.removeProperty("border");
     }
     // hashTagSpace인 경우 hashTag를 hashTagSpace 앞으로 이동
     else if (/[0-9]/g.test(eventTargetId) && /hashTagSpace/.test(eventTargetId)) {
       event.target.style.removeProperty("border");
     }
   }

   /* drop event */
   const drop = (event) => {
     let eventTargetId = event.target.id;
     let numberIndex;
     let targetIdIndex;

     event.preventDefault();

     // hashTagsWrapper인 경우 hashTag가 가장 마지막으로 이동
     if (event.target.id == "hashTagsWrapper") {
       document.getElementById("hashTagsStatement").style.color = "";
       event.target.style.border = "";
       document.getElementById("hashTagsWrapper").appendChild(document.getElementById(event.dataTransfer.getData("Text")));
     }
     // hashTagSpace인 경우 hashTag를 hashTagSpace 앞으로 이동
     else if (/[0-9]/g.test(eventTargetId) && /hashTagSpace/.test(eventTargetId)) {
       numberIndex = eventTargetId.search(/[0-9]/g);
       targetIdIndex = eventTargetId.substring(numberIndex, eventTargetId.length);
       document.getElementById("hashTagsStatement").style.color = "";
       event.target.style.border = "";
       document.getElementById("hashTagData" + targetIdIndex).before(document.getElementById(event.dataTransfer.getData("Text")));
     }
   }

   /* hash tags add event */
   const hashTagsAddEvent = () => {
     hashTagId = isEmpty(props.hashTags) ? hashTagId : hashTagId + (hashTagId < props.hashTags.split('#').length ? props.hashTags.split('#').length : 0);
     let regExp = /^[#][a-zA-Zㄱ-힣0-9\s|s]*$/;
     let hashTag = document.getElementsByName("hashTag")[0].value;

     if (regExp.test(hashTag)) {
       const tempHashTagId = hashTagId;
       const tag = '<span draggable="true" id="hashTagData' + hashTagId + '">'
           + '<span id="hashTagSpace' + hashTagId + '" class="hash-tag-space">    </span>'
           + '<span id="hashTagContent' + hashTagId + '" class="hash-tag">'
           + hashTag
           + '<img id="cancelFileIcon' + tempHashTagId + '" src="/x-circle-main-black.svg" class="ms-2">'
           + '</span>'
           + '</span>';

       document.getElementById("hashTagsWrapper").insertAdjacentHTML("beforeend", tag);
       document.getElementById("cancelFileIcon" + tempHashTagId).onclick = function () {
         cancelHashTagEvent(tempHashTagId);
       }

       document.getElementsByName("hashTag")[0].value = "";
       hashTagId++;
     } else {
       document.getElementById("hashTagsErrorMessage").innerText = "해시태그를 잘못 입력했습니다.";
       document.getElementsByName("hashTag")[0].focus();
     }
   }

   /* hash tag를 취소하는 경우 */
   const cancelHashTagEvent = (hashTagId) => {
     document.getElementById("hashTagData" + hashTagId).remove();
   }

   return {
     // variable

     // function
     isEmpty, hashTagsAddEvent, cancelHashTagEvent
   }
 }
}
</script>
```



## 우선순위 구현
- 리스트 페이지에서 우선순위에 따라 게시글 출력 순서를 변경하는 기능을 구현하였다.
- 리스트 페이지에서 우선순위가 있는 게시글은 상단에 고정되고 스피커 아이콘이 출력되어, 우선순위가 적용된 것을 확인 할 수 있다.
- 우선순위가 적용된 게시글은 페이지 이동, 검색 할 때에 영향을 받는다. 만약 1번 페이지에서 2번 페이지로 이동하거나, 검색 결과에 해당되지 않는 경우 리스트 페이지에서 출력되지 않는다.

![image](/assets/img/2022-02-06-Project SW Test Forum9/image4.png)

![image](/assets/img/2022-02-06-Project SW Test Forum9/image5.png)

- 우선순위 구현할 때 고려해야 할 점은 크게 2가지가 있었다.


### 1. 우선순위가 등록된 게시글이 있는 상태에서, 게시글 새로 등록
- 우선순위가 등록된 게시글이 있을 때, 우선순위를 중복하여 등록하면 문제가 발생한다. 따라서 다음과 같이 이미 등록된 우선순위는 select에서 선택할 수 없도록 구현하였다.

![image](/assets/img/2022-02-06-Project SW Test Forum9/image6.png)

### 2. 우선순위가 설정된 게시글 수정
- 1번 문제를 해결하면, 우선순위가 설정된 게시글의 우선순위를 변경하지 않고 수정하면 기존 게시글의 우선순위가 중복되므로 게시글을 등록할 수 없다.
- 따라서 다음과 같이 현재 게시글의 우선순위를 select에서 출력하도록 구현하였다.

![image](/assets/img/2022-02-06-Project SW Test Forum9/image7.png)

- 우선순위가 높은 리스트 조회, 우선순위가 낮은 리스트 조회 api가 2개로 분류된다.

```
module-app-web\front\src\components\misra_c\misra_c\MisraCList.vue
```

```html
<!-- ... -->
         <!-- misraCListByPriority -->
         <tr v-for="(misraC, i) in misraCListByPriority" :key="i">
           <!-- Desktop 번호 -->
           <td class="d-none d-lg-table-cell text-center">{{ misraC.idx }}</td>
           <td>
             <!-- Mobile -->
             <span class="d-inline d-lg-none mobile-number">{{ misraC.idx }}. </span>
             <!-- 공통 -->
             <img :src="require(`@/assets/images/speaker.jpg`)" class="speaker-icon"/>
             <Frequency page-information="list" :frequency="misraC.frequency"> </Frequency>
             <router-link :to="'/misra-c/read/' + misraC.idx">{{ misraC.title }}</router-link>
             <span class="comment-count">{{ misraC.commentDtoCount }}</span>
             <img v-if="misraC.newIcon" :src="require(`@/assets/images/new_post.svg`)" class="new-icon"/>
             <!-- Mobile -->
             <div class="d-inline d-lg-none">
               <div>
                 <span class="mobile-content">{{ misraC.createdByUser.department }} {{ misraC.createdByUser.name }} </span> <br>
                 <span class="mobile-content">{{ misraC.createdDate }}</span> &nbsp;
                 <span class="mobile-content"> 조회수: {{ misraC.views }}</span> &nbsp;
                 <span class="mobile-content"><HashTags pageInformation="list" :hash-tags="misraC.hashTags"></HashTags></span>
               </div>
             </div>
           </td>
           <!-- Desktop -->
           <td class="d-none d-lg-table-cell">{{ misraC.createdByUser.department }} {{ misraC.createdByUser.name }}</td>
           <td class="d-none d-lg-table-cell text-start">
             <HashTags pageInformation="list" :hash-tags="misraC.hashTags"></HashTags>
           </td>
           <td class="d-none d-lg-table-cell text-center">{{ misraC.createdDate }}</td>
           <td class="d-none d-lg-table-cell text-center">{{ misraC.views }}</td>
         </tr>

         <!-- misraCList -->
         <tr v-for="(misraC, i) in misraCList.content" :key="i">
           <!-- Desktop 번호 -->
           <td class="d-none d-lg-table-cell text-center">{{ misraC.idx }}</td>
           <td>
             <!-- Mobile -->
             <span class="d-inline d-lg-none mobile-number">{{ misraC.idx }}. </span>
             <!-- 공통 -->
             <img v-if="misraC.priority <= 5" :src="require(`@/assets/images/speaker.jpg`)" class="speaker-icon"/>
             <Frequency page-information="list" :frequency="misraC.frequency"> </Frequency>
             <router-link :to="'/misra-c/read/' + misraC.idx"> {{ misraC.title }}</router-link>
             <span class="comment-count">{{ misraC.commentDtoCount }}</span>
             <img v-if="misraC.newIcon" :src="require(`@/assets/images/new_post.svg`)" class="new-icon"/>
             <!-- Mobile -->
             <div class="d-inline d-lg-none">
               <div>
                 <span class="mobile-content">{{ misraC.createdByUser.department }} {{ misraC.createdByUser.name }} </span> <br>
                 <span class="mobile-content">{{ misraC.createdDate }}</span> &nbsp;
                 <span class="mobile-content">조회수: {{ misraC.views }}</span> &nbsp;
                 <span class="mobile-content"><HashTags pageInformation="list" :hash-tags="misraC.hashTags"></HashTags></span>
               </div>
             </div>
           </td>
           <!-- Desktop -->
           <td class="d-none d-lg-table-cell">{{ misraC.createdByUser.department }} {{ misraC.createdByUser.name }}</td>
           <td class="d-none d-lg-table-cell text-start">
             <HashTags pageInformation="list" :hash-tags="misraC.hashTags"></HashTags>
           </td>
           <td class="d-none d-lg-table-cell text-center">{{ misraC.createdDate }}</td>
           <td class="d-none d-lg-table-cell text-center">{{ misraC.views }}</td>
         </tr>
<!-- ... -->

<script>
// ...
   /* 검색 */
   const searchList = async (pageParam) => {
     //우선순위가 있는 list
     if (pageParam.page === 1 && isEmpty(searchKeyword.value)) {
       await axios.get(process.env.VUE_APP_MODULE_APP_API_URL + "/api/misra-c/high-priority-list",
           {},
       )
           .then((response) => {
             misraCListByPriority.value = response.data;
             // dayjs
             for (const misraC of misraCListByPriority.value) {
               misraC.createdDate = dayjs(misraC.createdDate).format("YYYY.MM.DD.");
             }
           })
           .catch((error) => {
             parseErrorMsg(error.response);
           })
           .then(() => {
           });
     } else {
       misraCListByPriority.value.length = 0;
     }

     //우선순위가 없는 list
     const searchParam = {
       "searchType": searchType.value,
       "searchKeyword": searchKeyword.value
     };
     const params = {...pageParam, ...searchParam};
     const uri = createUri(process.env.VUE_APP_MODULE_APP_API_URL + "/api/misra-c/list", params);

     await axios.get(uri,
         {},
     )
         .then((response) => {
           // misraCList.value.content.length = 0;
           misraCList.value = response.data;
           startNumber.value = Math.floor(misraCList.value.number / 10) * 10 + 1;
           endNumber.value = (misraCList.value.totalPages > startNumber.value + 9) ? startNumber.value + 9 : (misraCList.value.totalPages == 0 ? 1 : misraCList.value.totalPages);

           // dayjs
           for (const misraC of misraCList.value.content) {
             misraC.createdDate = dayjs(misraC.createdDate).format("YYYY.MM.DD.");
           }
         })
         .catch((error) => {
           parseErrorMsg(error.response);
         })
         .then(() => {
         });
   }

// ...
</script>
```

```
module-app-web\front\src\components\common\Priority.vue
```

```html
<template>
 <span v-if="pageInformation === 'read'">
   <span v-if="priority < maxPriority">{{ priority }}</span>
   <span v-else>우선순위가 설정되어 있지 않습니다.</span><br>
 </span>
 <div v-if="pageInformation === 'write' || pageInformation === 'update'">
   <select name="priority" id="priority" class="form-control" v-model="priority">
     <option v-for="(priority, i) in priorityArray" :key="i" :value="i+1" :disabled="priority !== null ? priority.disabled : false">
       <span v-if="priority === null">{{ i + 1 }}</span>
       <span v-else-if="priority !== null && i+1 !== maxPriority">{{ i + 1 }}. {{ priority.note }}</span>
       <span v-else>{{ priority.note }}</span>
     </option>
   </select>
 </div>
</template>

<style lang="scss">

</style>

<script>
export default {
 name: "Priority",
 props: {
   pageInformation: String,
   priority: Number,
   maxPriority: Number,
   priorityArray: Object
 }
}
</script>
```

- 6의 크기를 가지는 우선순위 배열을 생성하며, 마지막 배열 요소는 우선순위를 설정하지 않는 경우다.
- 우선순위가 설정된 게시글을 조회한 리스트를 반복문에서 순회하며, 우선순위가 설정되어 있는 요소이거나 현재 수정 중인 요소에 색인을 설정한다.

```
module-domain-core\src\main\java\com\suresoft\sw_test_forum\misra_c\misra_c\service\MisraCService.java
```

``java
// ...

/**
* 작성할 때, 우선순위가 높은 리스트 조회
*
* @return
*/
public PriorityDto[] findAllByHighPriorityAscWhenWrite() {
   List<MisraCDto> highPriorityList = misraCRepositoryImpl.findAllByHighPriorityAscCheckPriority();
   PriorityDto[] priorityDtoArray = new PriorityDto[6];
   priorityDtoArray[5] = new PriorityDto(false, "우선순위를 설정하지 않습니다.");

   for (MisraCDto highPriority : highPriorityList) {
       priorityDtoArray[(int) highPriority.getPriority() - 1] = new PriorityDto(true, "우선순위가 설정되어 있습니다.");
   }

   return priorityDtoArray;
}

// ...

/**
* 수정할 때, 우선순위가 높은 리스트 조회
*
* @return
*/
public PriorityDto[] findAllByHighPriorityAscWhenUpdate(long idx) {
   List<MisraCDto> highPriorityList = misraCRepositoryImpl.findAllByHighPriorityAscCheckPriority();
   MisraC misraCPriority = misraCRepositoryImpl.findMisraCPriorityByIdx(idx);
   PriorityDto[] priorityDtoArray = new PriorityDto[6];
   priorityDtoArray[5] = new PriorityDto(false, "우선순위를 설정하지 않습니다.");

   for (MisraCDto highPriority : highPriorityList) {
       if (misraCPriority.getPriority() == highPriority.getPriority()) {
           priorityDtoArray[(int) highPriority.getPriority() - 1] = new PriorityDto(false, "지금 설정된 우선순위 입니다.");
       } else {
           priorityDtoArray[(int) highPriority.getPriority() - 1] = new PriorityDto(true, "우선순위가 설정되어 있습니다.");
       }
   }

   return priorityDtoArray;
}
```

```
module-domain-core\src\main\java\com\suresoft\sw_test_forum\misra_c\misra_c\repository\MisraCRepositoryImpl.java
```

```java
/**
* 우선순위 확인 할 때, 우선순위 높은 리스트 조회
*
* @return
*/
public List<MisraCDto> findAllByHighPriorityAscCheckPriority() {
   return queryFactory.select(
                   Projections.bean(
                           MisraCDto.class,
                           misraC.priority
                   )
           )
           .from(misraC)
           .where(misraC.priority.loe(5))
           .orderBy(misraC.priority.asc())
           .fetch();
}
```



## 자동완성(autoComplete.js) 구현
- 이미 등록된 게시글의 데이터를 바탕으로 자동으로 입력값을 제안하는 기능을 구현하였다.
- 비슷한 유형의 자바스크립트 라이브러리가 많이 존재하는데, 이중 바닐라 자바스크립트로 구현된 autoComplete.js 라이브러리가 장점으로 생각하여 선택하게 되었다.

![image](/assets/img/2022-02-06-Project SW Test Forum9/image8.png)

출처: <https://tarekraafat.github.io/autoComplete.js/#/>



## Vue.js Composition API에서 tooltip 구현
- 아이콘에 마우스 커서를 올리면 부가 설명이 출력되는 tooltip 기능을 구현하였다.

![image](/assets/img/2022-02-06-Project SW Test Forum9/image9.png)

- Vue.js에서 ‘Bootstrap Vue’를 사용하지 않고 일반 bootstrap으로 tooltip 기능을 적용은 쉽지 않았다.
- 하단 출처를 참고하여 bootstrap tooltip을 사용하였지만, bootstrap 라이브러리를 중복으로 import(main.js 파일과, bootstrap tooltip을 사용하는 파일)하여 다른 기능들이 작동하지 않는 오류가 발생하였다.

출처:
<https://therichpost.com/vue-3-bootstrap-5-tooltip-working-example/>

- 따라서 하단 출처를 참고하여 직접 구현하는 방식으로 변경하였다.

출처:
<https://www.w3schools.com/howto/howto_css_tooltip.asp>



## 코드 출력(CodeMirror) 구현
- IDE 처럼 코드 Syntax HighLighting 기능을 제공하는 CodeMirror을 사용하여 코드 출력 기능을 구현하였다.

![image](/assets/img/2022-02-06-Project SW Test Forum9/image10.png)

- Vue.js에서 CodeMirror를 사용하는 예제는 많지만 Vue.js 3 Composition API에서 CodeMirror을 사용하는 예제는 적어 구현에 많은 어려움을 겪었다.
- 이중 정적시험 규칙 상세보기 페이지에서 예저 코드를 출력하는 기능 구현이 쉽지 않았다.
- 이는 동적으로 예제 코드를 출력하는 기능(즉, 리스트 크기에 따라서 CodeMirror가 동적으로 출력)으로, CodeMirror을 동적으로 생성하면 에러가 발생했기 때문이다. 이를 해결하기 위해서 비효율적이지만 CodeMirror을 미리 생성하고 리스트 크기에 맞게 숨김 처리하는 로직으로 구현하였다.
- 간단한 코드 미러 코드는 다음과 같다.

```
module-app-web\front\src\components\common\ExampleList.vue
```

```html
<template>
 <div>
   <div v-if="pageInformation === 'write' || pageInformation === 'update'">
     <div class="mb-4">
       <div class="d-flex flex-column flex-lg-row">
         <div class="w-100 me-3 mb-3 mb-lg-0" style="overflow:hidden">
           <p><strong>Bad Case 코드</strong></p>
           <textarea name="nonCompliantExample" id="nonCompliantExample" v-model=nonCompliantExample :placeholder="codeMirror.editorData"/>
           <p id="nonCompliantExampleErrorMessage" class="error-message"></p>
         </div>
         <div class="w-100 mb-3 mb-lg-0" style="overflow:hidden">
           <p><strong>Good Case 코드</strong></p>
           <textarea name="compliantExample" id="compliantExample" v-model=compliantExample :placeholder="codeMirror.editorData"/>
           <p id="compliantExampleErrorMessage" class="error-message"></p>
         </div>
       </div>
     </div>
   </div>
<!-- ... -->
</template>

<style lang="scss">
// CodeMirror
@import '~@/assets/css/codemirror.css';
@import '~codemirror/theme/eclipse.css';
@import '~codemirror/theme/dracula.css';
</style>

<script>
// vue.js
import {onMounted, ref, watchEffect} from "vue";
// CodeMirror
import CodeMirror from 'codemirror/lib/codemirror.js';
import 'codemirror/mode/clike/clike.js';
import 'codemirror/addon/display/placeholder.js';
import {codeMirror} from '@/assets/plugins/code-mirror/code-mirror.js'

export default {
 name: "ExampleList",
 props: {
   pageInformation: String,
   nonCompliantExampleValue: String,
   compliantExampleValue: String,
   exampleList: Object,
   exampleLinkList: Array,
   exampleTitleList: Array,
   link: String,
   mode: String,
 }, setup(props) {
   let rulePageReadLength = 3;
   let displayNoneFlag = 0;
   let nonCompliantExample = ref("");
   let compliantExample = ref("");
   let nonCompliantExampleWhenRead = ref("");
   let compliantExampleWhenRead = ref("");
   let nonCompliantExampleWhenRulePageReadList = ref([]);
   let compliantExampleWhenRulePageReadList = ref([]);

   const writeAndReadOption = {
     lineNumbers: true,
     lineWrapping: true,
     indentWithTabs: true,
     indentUnit: 4,
     viewportMargin: Infinity,
     mode: props.mode,
     theme: 'eclipse'
   }

   const readOption = {
     readOnly: true,
     lineNumbers: true,
     indentUnit: 4,
     mode: props.mode,
     theme: 'eclipse',
   }

   onMounted(async () => {
     codeMirror.initCodeMirror();

     if (props.pageInformation === "write" || props.pageInformation === "update") {
       // ...
     } else if (props.pageInformation === "read") {
       nonCompliantExampleWhenRead = CodeMirror.fromTextArea(document.getElementsByName('nonCompliantExampleWhenRead')[0], readOption);
       nonCompliantExampleWhenRead.save();

       compliantExampleWhenRead = CodeMirror.fromTextArea(document.getElementsByName('compliantExampleWhenRead')[0], readOption);
       compliantExampleWhenRead.save();
     }

      // ...
</script>
```

출처: <https://velog.io/@sian/Vue%EC%97%90-Code-mirror-%EC%A0%81%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0><br>
<https://codemirror.net/2/mode/clike/><br>
<https://codepen.io/DerkJanS/pen/zzbRNQ>
