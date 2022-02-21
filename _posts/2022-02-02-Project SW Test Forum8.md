---
title: "Project SW Test Forum 8. Vue.js CKEditor 5(Custom Pugin 개발) - 2"
excerpt: "- Vue.js 3와 커스텀 플러그인(에디터 크기 변경) 구현 과정을 소개한다."
categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2022-02-02
layout: post
---
- Vue.js 3와 커스텀 플러그인(에디터 크기 변경) 구현 과정을 소개한다.
- github: <https://github.com/scribnote5/sw_test_forum>



## 에디터 크기 변경 플러그인의 필요성
- 회사 그룹웨어에서 사용하는 TinyMCE WYSIWYG에서는 에디터 크기를 전체화면으로 변경하는 플러그인을 제공한다.
- 해당 기능은 내용이 길어지거나 이미지를 삽입하는 업무 메일을 작성할 때, 매우 유용하게 사용하였다. 그러나 아쉽게도 CKEditor 4에서는 에디터 크기 변경 플러그인을 지원하지만 CKEditor 5에서는 지원하지 않았다. CKEditor 5 공식 포럼에 메일로 문의하였지만 css로 크기 조절이 가능하기에, 동적으로 크기 조절 플러그인을 지원하지 않는다고 하였다.
- 따라서 해당 플러그인을 구현하기로 하였다.

![image](/assets/img/2022-02-02-Project SW Test Forum8/image1.png)



## CKEditor 5 커스텀 플러그인 개발
- CKEditor 5 커스텀 플러그인은 공식 문서에서 제공하며 하단 출처를 참고하여 개발하였다.

출처: <https://ckeditor.com/docs/ckeditor5/latest/framework/guides/plugins/creating-simple-plugin.html>


### 에디터 아이콘 추가
- 에디터를 ‘최대’ 크기로 늘리는 경우, ‘최소’ 크기로 줄이는 경우 기능과 일치하는 아이콘이 위치해야 한다. 아이콘 이미지를 public 폴더와 assets 폴더에 추가하여 읽어오려고 하였지만, CKEditor에서 해당 아이콘 이미지를 인식하지 못하여 오류가 발생하였다.
- 따라서 CKEditor의 이미지가 라이브러리 이미지 파일이 위치한 경로에 아이콘 이미지를 추가하여 읽어오는 방식으로 구현하였다. CKEditor 라이브러리에 의존적이라는 단점이 발생하지만 이를 해결하는 방법을 찾지 못하였다. 이는 CKEditor 라이브러리에 의존적이며 버전이 변경되는 경우 아이콘 이미지를 다시 옮겨야 하는 단점이 있지만, 이를 해결하는 방법을 찾지 못하였다.

```
module-app-web\front\src\assets\plugins\ckeditor\ckeditor-editor-resize.js
```

```javascript
// ...

// import extendIcon from '/public/extend-black.svg';
// import shrinkIcon from '/public/shrink-black.svg';
// import extendIcon from '@/assets/images/shrink-black.svg';
// import shrinkIcon from '@/assets/images/shrink-black.svg';
import extendIcon from '@ckeditor/ckeditor5-core/theme/icons/extend-black.svg';
import shrinkIcon from '@ckeditor/ckeditor5-core/theme/icons/shrink-black.svg';

// ...

에디터 크기 변경 플러그인 구현
- 에디터 크기를 조절 기능은 css 코드를 삽입하고 삭제하는 자바스크립트
‘document.styleSheets[0].insertRule’, ‘document.styleSheets[0].deleteRule’ 함수를 사용하여 구현하였다.
- 에디터 크기 변경 할 때 아이콘이 변경되며, HTML body의 scroll bar가 숨겨지거나 보여진다.
module-app-web\front\src\assets\plugins\ckeditor\ckeditor-editor-resize.js
import Plugin from '@ckeditor/ckeditor5-core/src/plugin';
import ButtonView from '@ckeditor/ckeditor5-ui/src/button/buttonview';

// import extendIcon from '/public/extend-black.svg';
// import shrinkIcon from '/public/shrink-black.svg';
// import extendIcon from '@/assets/images/shrink-black.svg';
// import shrinkIcon from '@/assets/images/shrink-black.svg';
import extendIcon from '@ckeditor/ckeditor5-core/theme/icons/extend-black.svg';
import shrinkIcon from '@ckeditor/ckeditor5-core/theme/icons/shrink-black.svg';

class EditorResize extends Plugin {
   init() {
       const editor = this.editor;

       editor.ui.componentFactory.add('editorResize', locale => {
           const view = new ButtonView(locale);
           let isFullSize = false;

           view.set({
               label: 'Extend editor',
               icon: extendIcon,
               tooltip: true
           });

           // Callback executed once the image is clicked.
           view.on('execute', () => {
               editor.model.change(writer => {
                   if (isFullSize) {
                       document.styleSheets[0].deleteRule(0);
                       document.styleSheets[0].deleteRule(0);
                       document.styleSheets[0].deleteRule(0);

                       document.styleSheets[0].insertRule(
                           ".ck-editor__editable:not(.ck-editor__nested-editable) { " +
                           "height: 550px; }");

                       view.set({
                           label: 'Extend editor',
                           icon: extendIcon,
                           tooltip: true
                       });
                       isFullSize = false;
                       editor.editing.view.focus()
                   } else {
                       document.styleSheets[0].insertRule(
                           ".ck.ck-editor { " +
                           "position: fixed !important; " +
                           "top: 0px ; " +
                           "left: 0px ; " +
                           "z-index: 1200 ; " +
                           "width:" + window.innerWidth + "px !important; }");
                       document.styleSheets[0].insertRule(
                           ".ck-editor__editable:not(.ck-editor__nested-editable) { " +
                           "height: calc(" + window.innerHeight + "px - 77px)  !important; }");
                       document.styleSheets[0].insertRule(
                           "body { overflow-y: hidden  }");

                       view.set({
                           label: 'Shrink editor',
                           icon: shrinkIcon,
                           tooltip: true
                       });
                       isFullSize = true;
                       editor.editing.view.focus()
                   }
               });
           });

           return view;
       });
   }
}

export {EditorResize}
```


### 에디터 초기화 시 커스텀 플러그인 호출
- editorConfig 객체의 plugins 속성에 EditorResize를, toolbar 속성에 ‘editorResize’를 추가하면 된다.

```
module-app-web\front\src\assets\plugins\ckeditor\ckeditor-init.js
```

```javascript
// ...
import {EditorResize} from "./ckeditor-editor-resize"

const editor = ClassicEditor;

let editorConfig = {
   plugins: [
       // ...
       Essentials,
       HtmlEmbed,
       UploadAdapter,
       //EasyImage,
       //CloudServices,
       Mention,
       // MentionCustomization,
       SourceEditing,

       EditorResize
   ],

   toolbar: {
       items: [
           // ...
           '|',
           'undo',
           'redo',
           '|',
           'sourceEditing',
           '|',
           'editorResize'
       ],
       shouldNotGroupWhenFull: true
   },
   // ...
};
```



## 구현 결과
- 에디터 크기가 최소 일 때와 에디터 크기가 최대 일 때의 이미지다.

![image](/assets/img/2022-02-02-Project SW Test Forum8/image2.png)

![image](/assets/img/2022-02-02-Project SW Test Forum8/image3.png)
