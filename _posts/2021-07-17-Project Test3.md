---
title: "Project SW Test Forum 3. Vue.js CKEditor 5 - 1"
excerpt: "Vue.js 3에서 CKEditor5를 적용하는 과정을 소개한다."
categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2021-07-10
layout: post
---
- Vue.js 3에서 CKEditor5를 적용하는 과정을 소개한다.
- github: <https://github.com/scribnote5/sw_test_forum>



## CKEditor란?
- 일반적으로 게시판에 글을 작성할 때 다양한 편집 기능을 제공하는 WYSIWYG 중 하나다.
- CKEditor 이외에도 Summernote, TinyMCE, Froala Editor 등 다양한 WYSIWYG가 존재한다.
- CKEditor를 선택한 계기는 기존에는 summernote를 사용하였지만, 애니메이션 효과가 적용되지 않아서 변경하게 되었다. 또한 summernote에 비하여 다양한 기능을 제공하고, 디자인도 조금 더 깔끔한 것 같아서 최신 버전인 CKEditor5를 선택하였다.



## CKEditor Vue.js에 적용
- Project Lab에서는 Spring Boot 템플릿 엔진인 Thymeleaf를 사용하였으며, CKEditor Online Builder를 사용하여 CKEditor를 적용하였다. 하단 출처의 웹페이지에서 원하는 템플릿, 플러그인, 메뉴바 위치 등을 선택하여 빌드하면 이를 다운 받아 사용 할 수 있다.
출처: <https://ckeditor.com/ckeditor-5/online-builder>

- Vue.js에서 CKEditor를 사용하려면 CKEditor Online Builder가 아닌 CKEditor 공식 문서를 참고해야 한다.
출처: <https://ckeditor.com/docs/ckeditor5/latest/builds/guides/integration/frameworks/vuejs-v3.html>



## CKEditor 관련 모듈 설치
- CKEidtor를 사용하기 위한 필수 모듈이다.

```
npm install --save \
    @ckeditor/ckeditor5-vue \
    @ckeditor/ckeditor5-dev-webpack-plugin \
    @ckeditor/ckeditor5-dev-utils \
    postcss-loader@3 \
    raw-loader@0.5.1
```

- CKEditor 관련 모듈(템플릿, plugins)를 설치한다.
- CKEditor 플러그인을 알아보면서 편집에 필요한 기본 플러그인, 편의성을 제공하는 플러그인, 응용 플러그인을 모두 추가하였다. 해당 설정대로 진행하면 어느정도 보장된 수준의 플러그인을 제공한다.
- 개발자가 구현하려는 시스템에 따라서 CKEditor에서 사용할 모듈을 추가 및 제거하면 된다.

```
package.json
```

```
...
  "dependencies": {
    "@ckeditor/ckeditor5-adapter-ckfinder": "^27.1.0",
    "@ckeditor/ckeditor5-alignment": "^27.1.0",
    "@ckeditor/ckeditor5-basic-styles": "^27.1.0",
    "@ckeditor/ckeditor5-build-classic": "^27.1.0",
    "@ckeditor/ckeditor5-ckfinder": "^27.1.0",
    "@ckeditor/ckeditor5-cloud-services": "^27.1.0",
    "@ckeditor/ckeditor5-code-block": "^27.1.0",
    "@ckeditor/ckeditor5-dev-utils": "^24.4.2",
    "@ckeditor/ckeditor5-dev-webpack-plugin": "^24.4.2",
    "@ckeditor/ckeditor5-editor-classic": "^27.1.0",
    "@ckeditor/ckeditor5-essentials": "^27.1.0",
    "@ckeditor/ckeditor5-font": "^27.1.0",
    "@ckeditor/ckeditor5-highlight": "^27.1.0",
    "@ckeditor/ckeditor5-horizontal-line": "^27.1.0",
    "@ckeditor/ckeditor5-html-embed": "^27.1.0",
    "@ckeditor/ckeditor5-image": "^27.1.0",
    "@ckeditor/ckeditor5-indent": "^27.1.0",
    "@ckeditor/ckeditor5-link": "^27.1.0",
    "@ckeditor/ckeditor5-media-embed": "^27.1.0",
    "@ckeditor/ckeditor5-paragraph": "^27.1.0",
    "@ckeditor/ckeditor5-paste-from-office": "^27.1.0",
    "@ckeditor/ckeditor5-remove-format": "^27.1.0",
    "@ckeditor/ckeditor5-special-characters": "^27.1.0",
    "@ckeditor/ckeditor5-table": "^27.1.0",
    "@ckeditor/ckeditor5-theme-lark": "^27.1.0",
    "@ckeditor/ckeditor5-vue": "^2.0.1",
    "@ckeditor/ckeditor5-word-count": "^27.1.0",
}
...
```

- vue.js ‘/’(root) 경로에 하단의 vue.js.config 파일을 생성한다.

```
vue.js.config
```

```
const path = require( 'path' );
const CKEditorWebpackPlugin = require( '@ckeditor/ckeditor5-dev-webpack-plugin' );
const { styles } = require( '@ckeditor/ckeditor5-dev-utils' );

module.exports = {
    // The source of CKEditor is encapsulated in ES6 modules. By default, the code
    // from the node_modules directory is not transpiled, so you must explicitly tell
    // the CLI tools to transpile JavaScript files in all ckeditor5-* modules.
    transpileDependencies: [
        /ckeditor5-[^/\\]+[/\\]src[/\\].+\.js$/,
    ],

    configureWebpack: {
        plugins: [
            // CKEditor needs its own plugin to be built using webpack.
            new CKEditorWebpackPlugin( {
                // See https://ckeditor.com/docs/ckeditor5/latest/features/ui-language.html
                language: 'en',

                // Append translations to the file matching the `app` name.
                translationsOutputFile: /app/
            } )
        ]
    },

    // Vue CLI would normally use its own loader to load .svg and .css files, however:
    //	1. The icons used by CKEditor must be loaded using raw-loader,
    //	2. The CSS used by CKEditor must be transpiled using PostCSS to load properly.
    chainWebpack: config => {
        // (1.) To handle the editor icons, get the default rule for *.svg files first:
        const svgRule = config.module.rule( 'svg' );

        // Then you can either:
        //
        // * clear all loaders for existing 'svg' rule:
        //
        //		svgRule.uses.clear();
        //
        // * or exclude ckeditor directory from node_modules:
        svgRule.exclude.add( path.join( __dirname, 'node_modules', '@ckeditor' ) );

        // Add an entry for *.svg files belonging to CKEditor. You can either:
        //
        // * modify the existing 'svg' rule:
        //
        //		svgRule.use( 'raw-loader' ).loader( 'raw-loader' );
        //
        // * or add a new one:
        config.module
            .rule( 'cke-svg' )
            .test( /ckeditor5-[^/\\]+[/\\]theme[/\\]icons[/\\][^/\\]+\.svg$/ )
            .use( 'raw-loader' )
            .loader( 'raw-loader' );

        // (2.) Transpile the .css files imported by the editor using PostCSS.
        // Make sure only the CSS belonging to ckeditor5-* packages is processed this way.
        config.module
            .rule( 'cke-css' )
            .test( /ckeditor5-[^/\\]+[/\\].+\.css$/ )
            .use( 'postcss-loader' )
            .loader( 'postcss-loader' )
            .tap( () => {
                return styles.getPostCssConfig( {
                    themeImporter: {
                        themePath: require.resolve( '@ckeditor/ckeditor5-theme-lark' ),
                    },
                    minify: true
                } );
            } );
    }
};
```



## CKEditor 설정
- CKEditor에서 사용하는 css 파일로, CKEditor를 사용할 때 import 하지 않으면 css가 적용되지 않아 레이아웃이 깨진다.

```
/src/assets/css/ckeditor.css
```

```css
/*
 * CKEditor 5 (v27.0.0) content styles.
 * Generated on Wed, 24 Mar 2021 08:00:59 GMT.
 * For more information, check out https://ckeditor.com/docs/ckeditor5/latest/builds/guides/integration/content-styles.html
 */

 :root {
    --ck-color-mention-background: hsla(341, 100%, 30%, 0.1);
    --ck-color-mention-text: hsl(341, 100%, 30%);
    --ck-highlight-marker-blue: hsl(201, 97%, 72%);
    --ck-highlight-marker-green: hsl(120, 93%, 68%);
    --ck-highlight-marker-pink: hsl(345, 96%, 73%);
    --ck-highlight-marker-yellow: hsl(60, 97%, 73%);
    --ck-highlight-pen-green: hsl(112, 100%, 27%);
    --ck-highlight-pen-red: hsl(0, 85%, 49%);
    --ck-image-style-spacing: 1.5em;
    --ck-todo-list-checkmark-size: 16px;
}
/* ckeditor5 height */
.ck-editor__editable {
    min-height: 500px;
}
/* ckeditor5-font/theme/fontsize.css */
.ck-content .text-tiny {
    font-size: .7em;
}
/* ckeditor5-font/theme/fontsize.css */
.ck-content .text-small {
    font-size: .85em;
}
/* ckeditor5-font/theme/fontsize.css */
.ck-content .text-big {
    font-size: 1.4em;
}
/* ckeditor5-font/theme/fontsize.css */
.ck-content .text-huge {
    font-size: 1.8em;
}
/* ckeditor5-code-block/theme/codeblock.css */
.ck-content pre {
    padding: 1em;
    color: hsl(0, 0%, 20.8%);
    background: hsla(0, 0%, 78%, 0.3);
    border: 1px solid hsl(0, 0%, 77%);
    border-radius: 2px;
    text-align: left;
    direction: ltr;
    tab-size: 4;
    white-space: pre-wrap;
    font-style: normal;
    min-width: 200px;
}
/* ckeditor5-code-block/theme/codeblock.css */
.ck-content pre code {
    background: unset;
    padding: 0;
    border-radius: 0;
}
/* ckeditor5-horizontal-line/theme/horizontalline.css */
.ck-content hr {
    margin: 15px 0;
    height: 4px;
    background: hsl(0, 0%, 87%);
    border: 0;
}
/* ckeditor5-highlight/theme/highlight.css */
.ck-content .marker-yellow {
    background-color: var(--ck-highlight-marker-yellow);
}
/* ckeditor5-highlight/theme/highlight.css */
.ck-content .marker-green {
    background-color: var(--ck-highlight-marker-green);
}
/* ckeditor5-highlight/theme/highlight.css */
.ck-content .marker-pink {
    background-color: var(--ck-highlight-marker-pink);
}
/* ckeditor5-highlight/theme/highlight.css */
.ck-content .marker-blue {
    background-color: var(--ck-highlight-marker-blue);
}
/* ckeditor5-highlight/theme/highlight.css */
.ck-content .pen-red {
    color: var(--ck-highlight-pen-red);
    background-color: transparent;
}
/* ckeditor5-highlight/theme/highlight.css */
.ck-content .pen-green {
    color: var(--ck-highlight-pen-green);
    background-color: transparent;
}
/* ckeditor5-image/theme/imagestyle.css */
.ck-content .image-style-side {
    float: right;
    margin-left: var(--ck-image-style-spacing);
    max-width: 50%;
}
/* ckeditor5-image/theme/imagestyle.css */
.ck-content .image-style-align-left {
    float: left;
    margin-right: var(--ck-image-style-spacing);
}
/* ckeditor5-image/theme/imagestyle.css */
.ck-content .image-style-align-center {
    margin-left: auto;
    margin-right: auto;
}
/* ckeditor5-image/theme/imagestyle.css */
.ck-content .image-style-align-right {
    float: right;
    margin-left: var(--ck-image-style-spacing);
}
/* ckeditor5-image/theme/imagecaption.css */
.ck-content .image > figcaption {
    display: table-caption;
    caption-side: bottom;
    word-break: break-word;
    color: hsl(0, 0%, 20%);
    background-color: hsl(0, 0%, 97%);
    padding: .6em;
    font-size: .75em;
    outline-offset: -1px;
}
/* ckeditor5-image/theme/image.css */
.ck-content .image {
    display: table;
    clear: both;
    text-align: center;
    margin: 1em auto;
}
/* ckeditor5-image/theme/image.css */
.ck-content .image img {
    display: block;
    margin: 0 auto;
    max-width: 100%;
    min-width: 50px;
}
/* ckeditor5-image/theme/imageresize.css */
.ck-content .image.image_resized {
    max-width: 100%;
    display: block;
    box-sizing: border-box;
}
/* ckeditor5-image/theme/imageresize.css */
.ck-content .image.image_resized img {
    width: 100%;
}
/* ckeditor5-image/theme/imageresize.css */
.ck-content .image.image_resized > figcaption {
    display: block;
}
/* ckeditor5-language/theme/language.css */
.ck-content span[lang] {
    font-style: italic;
}
/* ckeditor5-block-quote/theme/blockquote.css */
.ck-content blockquote {
    overflow: hidden;
    padding-right: 1.5em;
    padding-left: 1.5em;
    margin-left: 0;
    margin-right: 0;
    font-style: italic;
    border-left: solid 5px hsl(0, 0%, 80%);
}
/* ckeditor5-block-quote/theme/blockquote.css */
.ck-content[dir="rtl"] blockquote {
    border-left: 0;
    border-right: solid 5px hsl(0, 0%, 80%);
}
/* ckeditor5-basic-styles/theme/code.css */
.ck-content code {
    background-color: hsla(0, 0%, 78%, 0.3);
    padding: .15em;
    border-radius: 2px;
}
/* ckeditor5-table/theme/table.css */
.ck-content .table {
    margin: 1em auto;
    display: table;
}
/* ckeditor5-table/theme/table.css */
.ck-content .table table {
    border-collapse: collapse;
    border-spacing: 0;
    width: 100%;
    height: 100%;
    border: 1px double hsl(0, 0%, 70%);
}
/* ckeditor5-table/theme/table.css */
.ck-content .table table td,
.ck-content .table table th {
    min-width: 2em;
    padding: .4em;
    border: 1px solid hsl(0, 0%, 75%);
}
/* ckeditor5-table/theme/table.css */
.ck-content .table table th {
    font-weight: bold;
    background: hsla(0, 0%, 0%, 5%);
}
/* ckeditor5-table/theme/table.css */
.ck-content[dir="rtl"] .table th {
    text-align: right;
}
/* ckeditor5-table/theme/table.css */
.ck-content[dir="ltr"] .table th {
    text-align: left;
}
/* ckeditor5-page-break/theme/pagebreak.css */
.ck-content .page-break {
    position: relative;
    clear: both;
    padding: 5px 0;
    display: flex;
    align-items: center;
    justify-content: center;
}
/* ckeditor5-page-break/theme/pagebreak.css */
.ck-content .page-break::after {
    content: '';
    position: absolute;
    border-bottom: 2px dashed hsl(0, 0%, 77%);
    width: 100%;
}
/* ckeditor5-page-break/theme/pagebreak.css */
.ck-content .page-break__label {
    position: relative;
    z-index: 1;
    padding: .3em .6em;
    display: block;
    text-transform: uppercase;
    border: 1px solid hsl(0, 0%, 77%);
    border-radius: 2px;
    font-family: Helvetica, Arial, Tahoma, Verdana, Sans-Serif;
    font-size: 0.75em;
    font-weight: bold;
    color: hsl(0, 0%, 20%);
    background: hsl(0, 0%, 100%);
    box-shadow: 2px 2px 1px hsla(0, 0%, 0%, 0.15);
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
}
/* ckeditor5-media-embed/theme/mediaembed.css */
.ck-content .media {
    clear: both;
    margin: 1em 0;
    display: block;
    min-width: 15em;
}
/* ckeditor5-list/theme/todolist.css */
.ck-content .todo-list {
    list-style: none;
}
/* ckeditor5-list/theme/todolist.css */
.ck-content .todo-list li {
    margin-bottom: 5px;
}
/* ckeditor5-list/theme/todolist.css */
.ck-content .todo-list li .todo-list {
    margin-top: 5px;
}
/* ckeditor5-list/theme/todolist.css */
.ck-content .todo-list .todo-list__label > input {
    -webkit-appearance: none;
    display: inline-block;
    position: relative;
    width: var(--ck-todo-list-checkmark-size);
    height: var(--ck-todo-list-checkmark-size);
    vertical-align: middle;
    border: 0;
    left: -25px;
    margin-right: -15px;
    right: 0;
    margin-left: 0;
}
/* ckeditor5-list/theme/todolist.css */
.ck-content .todo-list .todo-list__label > input::before {
    display: block;
    position: absolute;
    box-sizing: border-box;
    content: '';
    width: 100%;
    height: 100%;
    border: 1px solid hsl(0, 0%, 20%);
    border-radius: 2px;
    transition: 250ms ease-in-out box-shadow, 250ms ease-in-out background, 250ms ease-in-out border;
}
/* ckeditor5-list/theme/todolist.css */
.ck-content .todo-list .todo-list__label > input::after {
    display: block;
    position: absolute;
    box-sizing: content-box;
    pointer-events: none;
    content: '';
    left: calc( var(--ck-todo-list-checkmark-size) / 3 );
    top: calc( var(--ck-todo-list-checkmark-size) / 5.3 );
    width: calc( var(--ck-todo-list-checkmark-size) / 5.3 );
    height: calc( var(--ck-todo-list-checkmark-size) / 2.6 );
    border-style: solid;
    border-color: transparent;
    border-width: 0 calc( var(--ck-todo-list-checkmark-size) / 8 ) calc( var(--ck-todo-list-checkmark-size) / 8 ) 0;
    transform: rotate(45deg);
}
/* ckeditor5-list/theme/todolist.css */
.ck-content .todo-list .todo-list__label > input[checked]::before {
    background: hsl(126, 64%, 41%);
    border-color: hsl(126, 64%, 41%);
}
/* ckeditor5-list/theme/todolist.css */
.ck-content .todo-list .todo-list__label > input[checked]::after {
    border-color: hsl(0, 0%, 100%);
}
/* ckeditor5-list/theme/todolist.css */
.ck-content .todo-list .todo-list__label .todo-list__label__description {
    vertical-align: middle;
}
/* ckeditor5-html-embed/theme/htmlembed.css */
.ck-content .raw-html-embed {
    margin: 1em auto;
    min-width: 15em;
    font-style: normal;
}
/* ckeditor5-mention/theme/mention.css */
.ck-content .mention {
    background: var(--ck-color-mention-background);
    color: var(--ck-color-mention-text);
}
@media print {
    /* ckeditor5-page-break/theme/pagebreak.css */
    .ck-content .page-break {
        padding: 0;
    }
    /* ckeditor5-page-break/theme/pagebreak.css */
    .ck-content .page-break::after {
        display: none;
    }
}
```

- CKEditor 플러그인, 메뉴바를 초기화하는 파일이다.
- CKEditor 플러그인을 알아보면서 편집에 필요한 기본 플러그인, 편의성을 제공하는 플러그인, 응용 플러그인을 모두 추가하였다. 해당 설정대로 진행하면 어느정도 보장된 수준의 플러그인을 제공한다.
- 워드를 바탕으로 메뉴바 기능을 정렬하여, 익숙한 사용성을 제공하려고 하였다.
- 개발자가 구현하려는 시스템에 따라서 수정하면 된다.

```
/src/assets/plugins/ckeditor/ckeditor-init.js
```

```javascript
import ClassicEditor from '@ckeditor/ckeditor5-editor-classic/src/classiceditor'

import Essentials from '@ckeditor/ckeditor5-essentials/src/essentials'

import Bold from '@ckeditor/ckeditor5-basic-styles/src/bold'
import Italic from '@ckeditor/ckeditor5-basic-styles/src/italic'
import Code from '@ckeditor/ckeditor5-basic-styles/src/code'
import Strikethrough from '@ckeditor/ckeditor5-basic-styles/src/strikethrough'
import Subscript from '@ckeditor/ckeditor5-basic-styles/src/subscript'
import Superscript from '@ckeditor/ckeditor5-basic-styles/src/superscript'
import Underline from '@ckeditor/ckeditor5-basic-styles/src/underline'

import Image from '@ckeditor/ckeditor5-image/src/image'
import ImageCaption from '@ckeditor/ckeditor5-image/src/imagecaption'
import ImageStyle from '@ckeditor/ckeditor5-image/src/imagestyle'
import ImageToolbar from '@ckeditor/ckeditor5-image/src/imagetoolbar'
import ImageResize from '@ckeditor/ckeditor5-image/src/imageresize'
import ImageInsert from '@ckeditor/ckeditor5-image/src/imageinsert'
import ImageUpload from '@ckeditor/ckeditor5-image/src/imageupload'
import AutoImage from '@ckeditor/ckeditor5-image/src/autoimage'


import SpecialCharacters from '@ckeditor/ckeditor5-special-characters/src/specialcharacters'
import SpecialCharactersCurrency from '@ckeditor/ckeditor5-special-characters/src/specialcharacterscurrency'
import SpecialCharactersLatin from '@ckeditor/ckeditor5-special-characters/src/specialcharacterslatin'
import SpecialCharactersMathematical from '@ckeditor/ckeditor5-special-characters/src/specialcharactersmathematical'

import FontBackgroundColor from '@ckeditor/ckeditor5-font/src/fontbackgroundcolor'
import FontFamily from '@ckeditor/ckeditor5-font/src/fontfamily'
import FontSize from '@ckeditor/ckeditor5-font/src/fontsize'
import FontColor from '@ckeditor/ckeditor5-font/src/fontcolor'

import Link from '@ckeditor/ckeditor5-link/src/link'
import Linkimage from '@ckeditor/ckeditor5-link/src/linkimage'
import AutoLink from '@ckeditor/ckeditor5-link/src/autolink'

import Table from '@ckeditor/ckeditor5-table/src/table'
import TableToolbar from '@ckeditor/ckeditor5-table/src/tabletoolbar'
import TableProperties from '@ckeditor/ckeditor5-table/src/tableproperties'
import TableCellProperties from '@ckeditor/ckeditor5-table/src/tablecellproperties'

import Heading from '@ckeditor/ckeditor5-heading/src/heading'
import Title from '@ckeditor/ckeditor5-heading/src/title'

import Indent from '@ckeditor/ckeditor5-indent/src/indent'
import IndentBlock from '@ckeditor/ckeditor5-indent/src/indentblock'

import List from '@ckeditor/ckeditor5-list/src/list'
import ListStyle from '@ckeditor/ckeditor5-list/src/liststyle'

import MediaEmbed from '@ckeditor/ckeditor5-media-embed/src/mediaembed'
import BlockQuote from '@ckeditor/ckeditor5-block-quote/src/blockquote'
import RemoveFormat from '@ckeditor/ckeditor5-remove-format/src/removeformat'
import WordCount from '@ckeditor/ckeditor5-word-count/src/wordcount'
import PasteFromOffice from '@ckeditor/ckeditor5-paste-from-office/src/pastefromoffice'
import TextTransformation from '@ckeditor/ckeditor5-typing/src/texttransformation'
import CKFinderUploadAdapter from '@ckeditor/ckeditor5-adapter-ckfinder/src/uploadadapter'
import CodeBlock from '@ckeditor/ckeditor5-code-block/src/codeblock'
import Highlight from '@ckeditor/ckeditor5-highlight/src/highlight'
import HorizontalLine from '@ckeditor/ckeditor5-horizontal-line/src/horizontalline'
import Paragraph from '@ckeditor/ckeditor5-paragraph/src/paragraph'
import Alignment from '@ckeditor/ckeditor5-alignment/src/alignment'
import Htmlembed from '@ckeditor/ckeditor5-html-embed/src/htmlembed'
import UploadAdapter from '@ckeditor/ckeditor5-adapter-ckfinder/src/uploadadapter'
import Autoformat from '@ckeditor/ckeditor5-autoformat/src/autoformat'
import EasyImage from '@ckeditor/ckeditor5-easy-image/src/easyimage'
import CloudServices from '@ckeditor/ckeditor5-cloud-services/src/cloudservices'

import { CustomUploadAdapterPlugin } from './ckeditor-upload-adapter.js'

const editor = ClassicEditor;

const editorData = '<p>Content of the editor.</p>';

const editorConfig = {
    plugins: [
        Essentials,
        Bold,
        Italic,
        Code,
        Strikethrough,
        Subscript,
        Superscript,
        Underline,

        Image,
        ImageCaption,
        ImageStyle,
        ImageToolbar,
        ImageResize,
        ImageInsert,
        ImageUpload,
        AutoImage,

        SpecialCharacters,
        SpecialCharactersCurrency,
        SpecialCharactersLatin,
        SpecialCharactersMathematical,

        FontBackgroundColor,
        FontFamily,
        FontSize,
        FontColor,

        Link,
        Linkimage,
        AutoLink,

        Table,
        TableToolbar,
        TableProperties,
        TableCellProperties,

        Heading,
        Title,

        Indent,
        IndentBlock,

        MediaEmbed,

        List,
        ListStyle,

        BlockQuote,
        RemoveFormat,
        WordCount,
        PasteFromOffice,
        TextTransformation,
        CKFinderUploadAdapter,
        CodeBlock,
        Highlight,
        HorizontalLine,
        Paragraph,
        Alignment,
        Htmlembed,
        UploadAdapter,
        Autoformat,
        EasyImage,
        CloudServices
    ],

    toolbar: {
        items: [
            'heading',
            '|',
            'fontFamily',
            'fontSize',
            'fontColor',
            'fontBackgroundColor',
            'highlight',
            '|',
            'blockQuote',
            'bold',
            'italic',
            'underline',
            'strikethrough',
            'removeFormat',
            '|',
            'alignment',
            '|',
            'bulletedList',
            'numberedList',
            '|',
            'outdent',
            'indent',
            '|',
            'imageInsert',
            'insertTable',
            '|',
            'link',
            'horizontalLine',
            'specialCharacters',
            'superscript',
            'subscript',
            '|',
            'mediaEmbed',
            'codeBlock',
            'htmlEmbed',
            'code',
            '|',
            'undo',
            'redo'
        ]
    },

    language: 'en',

    image: {
        // Configure the available styles.
        styles: [
            'alignLeft', 'alignCenter', 'alignRight'
        ],

        // Configure the available image resize options.
        resizeOptions: [
            {
                name: 'imageResize:original',
                label: 'Original',
                value: null
            },
            {
                name: 'imageResize:75',
                label: '75%',
                value: '75'
            },
            {
                name: 'imageResize:50',
                label: '50%',
                value: '50'
            },
            {
                name: 'imageResize:25',
                label: '25%',
                value: '25'
            }
        ],

        // You need to configure the image toolbar, too, so it shows the new style
        // buttons as well as the resize buttons.
        toolbar: [
            'imageStyle:alignLeft', 'imageStyle:alignCenter', 'imageStyle:alignRight',
            '|',
            'imageResize',
            '|',
            'imageTextAlternative'
        ]
    },
    table: {
        contentToolbar: [
            'tableColumn',
            'tableRow',
            'mergeTableCells',
            'tableCellProperties',
            'tableProperties'
        ]
    },
    extraPlugins: [CustomUploadAdapterPlugin]
};

export { editor, editorData, editorConfig };
```

- CKEditor Content 영역에 파일을 드래그앤드랍 하는 경우 발생하는 이벤트를 처리하는 파일이다. 드래그앤드랍 기능을 구현할 수 있다.
- API 서버를 구현되어 있을 때, 파일 드래그앤드랍 이벤트가 발생하는 경우 비동기 방식으로 파일이 API 서버로 전송되어 업로드 된다.
- xhr.open('POST', 'localhost' + '/api/attachedFiles/upload', true); 에 파일 업로드를 처리하는 API 서버 URI를 입력하면 된다.

```
/src/assets/plugins/ckeditor/ckeditor-upload-adapter.js
```

```javascript
/* ckeditor custom image upload */
class CustomUploadAdapter {
    constructor(loader) {
        this.loader = loader;
    }

    upload() {
        return this.loader.file.then(file => new Promise(((resolve, reject) => {
            this._initRequest();
            this._initListeners(resolve, reject, file);
            this._sendRequest(file);
        })))
    }

    _initRequest() {
        const xhr = this.xhr = new XMLHttpRequest();
        xhr.open('POST', 'localhost' + '/api/attachedFiles/upload', true); // 이미지 파일을 업로드하는 파일 주소
        xhr.responseType = 'json';
    }

    _initListeners(resolve, reject, file) {
        const xhr = this.xhr;
        const loader = this.loader;
        const genericErrorText = 'Couldn\'t upload file.'

        xhr.addEventListener('error', () => {
            reject(genericErrorText)
        })
        xhr.addEventListener('abort', () => reject())
        xhr.addEventListener('load', () => {
            const response = xhr.response
            if (!response || response.error) {
                return reject(response && response.error ? response.error.message : genericErrorText);
            }

            resolve({
                default: response.url
            })
        })
    }

    _sendRequest(file) {
        const data = new FormData()
        data.append("files", file)
        this.xhr.send(data)
    }
}

function CustomUploadAdapterPlugin(editor) {
    editor.plugins.get('FileRepository').createUploadAdapter = (loader) => {
        return new CustomUploadAdapter(loader)
    }
}

export { CustomUploadAdapterPlugin }
```


## CKEditor 5 적용 및 예시
- 지금까지 설정한 CKEditor를 적용한 예시다.
- CKEditor 5 메뉴얼에서는 main.js에 CKEditor 모듈을 import 하여 전역 변수로 사용하도록 가이드 하지만, 다음과 같이 지역 변수로 선언하여 사용 할 수 있다.
- CKEditor에 입력된 데이터는 vueEditorData 변수에 저장된다. 서버와의 비동기 통신시 vueEditorData 변수를 사용하면 된다.

```
/src/views/Board.vue
```

```javascript
<template>
 <div class="board">
   <h1>This is an board page</h1>
   <ckeditor :editor="vueEditor" v-model="vueEditorData" :config="vueEditorConfig"></ckeditor>
 <div>
</template>

<style lang="scss">
@import '/assets/css/ckeditor.css';
</style>

<script>
import {editor, editorData, editorConfig} from '@/assets/plugins/ckeditor/ckeditor-init.js'
import CKEditor from '@ckeditor/ckeditor5-vue'
import axios from "axios";

export default {
 components: {
   ckeditor: CKEditor.component
 },
 setup() {
   const vueEditor = editor;
   const vueEditorData = editorData;
   const vueEditorConfig = editorConfig;

   return {
     vueEditor, vueEditorData, vueEditorConfig
   }
 }
};
```

![image](/assets/img/2021-07-17-Project SW Test Forum3/image1.png)
<br>

- DB에 저장된 CKEditor 5에서 데이터를 프론트엔드로 전달하는 경우 HTML escape로 인하여 vue.js의 v-html(HTML 코드를 HTML 태그로 변환)을 사용하여도 HTML 태그로 변환되지 않는다.
- HTML 코드를 unescape 하는 함수를 사용해야 HTML 코드가 HTML 태그로 변환된다.
- 벡엔드에서 HtmlUtils.htmlUnescape 메소드를 사용하여 HTML unescape된 CKEditor 데이터를 전달하려고 하였다.
- 그러나 벡엔드에서 XSS 공격을 대비하기 위해서 HTML unescape 필터를 등록하여, 대신 프론트엔드에서 HTML unescape 함수를 사용하여 HTML 태그로 변환하였다

> HTML escape: HTML 문자를 이스케이프(escape) 처리하면 스크립트나 HTML 태그의 기능은 제거되지만 입력한 내용은 그대로 브라우저에서 확인할 수 있다. 예를들어 태그의 시작을 의미하는 < 문자를 이스케이프 처리하면 &lt;라는 문자로 바뀐다.

출처: <https://wikidocs.net/127508>

```
/src/views/BoardRead.vue
```

```javascript
<template>
    <div v-html="notice.content"></div>
</template>


<script>
const unescapeHtml = (str) =>
   str.replace(
       /&amp;|&lt;|&gt;|&#39;|&quot;/g,
       tag =>
           ({
               '&amp;': '&',
               '&lt;': '<',
               '&gt;': '>',
               '&#39;': "'",
               '&quot;': '"'
           }[tag] || tag)
   );

// 생략...

// onBeforeMount, init data
onBeforeMount(async () => {
  await axios.get(process.env.VUE_APP_MODULE_APP_API_URL + "/api/notices/read?idx=" + idx,
      {},
  )
      .then(function (response) {
        notice.value = response.data;
        notice.value.content = unescapeHtml(notice.value.content);

        // 생략...
      })
      .catch(function (error) {

      })
      .then(function () {

      });
})
</script>
```
