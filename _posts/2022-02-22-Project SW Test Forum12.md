---
title: "Project SW Test Forum 12. pdf 파일 출력 기능 구현 - 1"
excerpt: "- Vue.js 3에서 pdf 파일 출력 기능 개발 과정을 소개한다."

categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2022-02-22
layout: post
---
- - Vue.js 3에서 pdf 파일 출력 기능 개발 과정을 소개한다.
- github: <https://github.com/scribnote5/sw_test_forum>



## pdf 파일 출력 기능 및 구현 결과
- 아직 Project SW Test Forum이 활성화 되어 있지 않은 단계에서, 작성한 게시글을 pdf로 출력하여 공유하는 방식이 많은 사람의 관심 및 이목을 끄는데 효과적이라고 생각하였다.
- 또한 본사 직원이 아닌 다른 사람에게 정보를 공유해야 하는 경우, 회원 권한을 줄 수 없으므로 게시글을 pdf로 출력하여 전달해야 한다.
- 하단 이미지에서 ‘pdf 출력’ 버튼을 선택하면 로딩바가 보여지고, 로딩바가 사라지면 생성된 pdf 파일을 다운 받을 수 있다.

![image](/assets/img/2022-02-22-Project SW Test Forum12/image1.png)

<br>
- 생성된 pdf 파일은 다음 이미지와 같다.

![image](/assets/img/2022-02-22-Project SW Test Forum12/image2.png)

<br>
- pdf 파일 상단, 좌우에 약간의 여백이 존재한다.

![image](/assets/img/2022-02-22-Project SW Test Forum12/image3.png)

<br>
- pdf 파일 페이지 변경시 이미지가 잘리는 부분은 라이브러리의 한계로 해결이 어렵다. html 태그 식별자(일반적으로 id) 마다 출력하는 페이지를 지정한다면, 해당 문제를 해결 할 수 있다.

![image](/assets/img/2022-02-22-Project SW Test Forum12/image4.png)

<br>
- 읽기 페이지에서만 ‘pdf 출력’ 버튼이 존재한다. 다음 이미지와 같이 리스트 페이지에서는 버튼이 없다. ‘pdf 출력’ 버튼을 읽기 페이지에서만 출력하도록 구현하고, 버튼 유무에 따라 레이아웃이 깨지지 않도록 수정하는데 생각보다 많은 시간이 소요되었다.

![image](/assets/img/2022-02-22-Project SW Test Forum12/image5.png)



## pdf 파일 출력 기능 구현
- pdf 파일 출력 html2canvas(html 코드를 이미지 파일로 생성)와 jspdf(이미지 파일을 pdf 파일로 생성)라이브러리를 사용하여 기능을 개발하였다
- 하단 출처를 참고하여 개발하였다. 이외 pdf 파일 내 상단, 좌우 여백은 html2canvas와 jspdf 높이 및 넓이 설정을 알아본 다음 수정하였다.

```
module-app-web\front\src\components\common\Breadcrumb.vue
```

```javascript
<script>
import {jsPDF} from "jspdf";
import html2canvas from "html2canvas";

export default {
 name: "Breadcrumb",
 components: {},
 props: {
   page: String,
   subPage: String,
   paths: Array,
   title: String,
 },
 setup(props) {
   const printPdf = async () => {
     document.getElementById("loading-wrapper").style.visibility = "visible";
     await createPdf();
     document.getElementById("loading-wrapper").style.visibility = "hidden";
   }

   const createPdf = () => {
     return new Promise((resolve, reject) => {
       html2canvas(document.getElementsByClassName("page-content")[0],
           {
             logging: false,
             allowTaint: true,
             useCORS: true,
             scale: 3   // 기본 해상도 3배 증가
           }
       ).then(canvas => {
         let filename = 'OTA-REPORT_' + Date.now() + '.pdf';
         let doc = new jsPDF('p', 'mm', 'a4');
         let imgData = canvas.toDataURL('image/png');
         let imgWidth = 200; // A4: 210
         let pageHeight = 297; // A4: 297
         let imgHeight = (canvas.height * imgWidth / canvas.width) - 10;
         let heightLeft = imgHeight;
         let position = 10;

         doc.addImage(imgData, 'png', 5, position, imgWidth, imgHeight);
         heightLeft -= pageHeight;

         while (heightLeft >= 0) {
           position = heightLeft - imgHeight + 10;
           doc.addPage();
           doc.addImage(imgData, 'png', 5, position, imgWidth, imgHeight);
           heightLeft -= pageHeight;
         }
         doc.save(props.title);
         resolve(true);
       });
     });
   }

   return {
     // function
     printPdf, isEmpty
   }
 }
}
</script>
```

출처: <https://soye0n.tistory.com/247>
