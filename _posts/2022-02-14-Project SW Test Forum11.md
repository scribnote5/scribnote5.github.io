---
title: "Project SW Test Forum 11. CodeMirror 하이라이트 기능 구현 - 1"
excerpt: "- Vue.js 3에서 CodeMirror 하이라이트 기능 개발 과정을 소개한다."

categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2022-02-08
layout: post
---
- Vue.js 3에서 CodeMirror 하이라이트 기능 개발 과정을 소개한다.
- github: <https://github.com/scribnote5/sw_test_forum>



## 코드 하이라이트 기능?
- 회사 업무에서 정적시험 규칙 자료 및 가이드라인을 작성할 때, 규칙 위배 코드에는 빨간 글자색을, 규칙 수정 코드에는 파란 글자색으로 표현하였다. 사용자가 문서를 읽을 때 가독성을 향상시키지만, Project SW Test Forum에서는 코드 색상 처리 기능을 고려하지 못하였다.
- 따라서 가독성을 향상시키기 위해서 코드 색상 처리 기능을 추가 구현하게 되었다.

![image](/assets/img/2022-02-14-Project SW Test Forum11/image0.png)

- 코드 색상 처리 기능은 글자 색을 변경하는 방식으로 기획하였으나, 코드 배경 색상이 더 가독성을 뛰어나다고 생각하여 해당 방식으로 구현하였다.
- 그리고 CodeMirror 관련 코드를 크게 수정하였다. 전반적인 코드 가독성 및 알고리즘 로직을 수정하였다.

![image](/assets/img/2022-02-14-Project SW Test Forum11/image1.png)



## CodeMirror 하이라이트(코드 배경 색상 변경) 기능 구현
- CodeMirror 라이브러리를 사용하여 하이라이트 기능을 개발하였다.
- 코드를 블록 후 ‘하이라이트’ 버튼을 선택하면 블록된 코드 배경색이 빨간색 또는 초록색으로 변경된다.
- ‘하이라이트’된 코드 블록 후 ‘되돌리기’ 버튼을 선택하면 배경색이 원래대로 돌아온다.
- 참고한 코드는 다음 출처와 같다.(CodeMirror 메뉴얼과 구글링을 통해서 기능을 구현 하였다. 그러나 구글링을 통하여 이미 구현된 코드를 발견하였다. 메뉴얼을 숙지하지 않은 상태로 구현하다 보니 코드가 깔끔하지 못하고 엉망진창 상태라서, 하단 출처 코드로 대체하였다. 구글링을 꼼꼼히 하자...)


### CodeMirror Marker 생성
- CodeMirror에서는 marker 기능을 통하여 코드 내 클래스를 삽입할 수 있다. 해당 기능을 통하여 하이라이트 기능을 구현하였다.
- marker는 line(코드 행)과 ch(코드 열)로 생성 할 수있다.
- marker.getCursor 함수는 CodeMirror 에디터에서 선택된 line(행)과 ch(열) 영역을 반환한다.

```javascript
nonCompliantExample.markText(nonCompliantExample.getCursor(true), nonCompliantExample.getCursor(false), {className: "bad-case-highlight"});
```


### CodeMirror Marker 검색 및 삭제
- marker.findMarkers() 함수는 line과 ch 사이에 존재하는 모든 marker을 검색한다.
- marker.clear() 함수는 mark를 삭제한다.

```javascript
nonCompliantExample.findMarks(nonCompliantExample.getCursor(true), nonCompliantExample.getCursor(false)).forEach(marker => marker.clear());
```


### CodeMirror Marker 경로 저장
- marker.find() 함수는 marker의 시작 위치와 종료 위치를 반환한다.
- 반환된 값을 사용하여 marker 경로를 배열에 저장한다.
- marker 경로는 DB에 저장되고, CodeMirror를 호출 할 때 marker 경로를 불러와 marker을 생성한다.

```javascript
for (const marker of nonCompliantExample.getAllMarks()) {
 badCasePosition.push([marker.find().from.line, marker.find().from.ch, marker.find().to.line, marker.find().to.ch]);
}
```

![image](/assets/img/2022-02-14-Project SW Test Forum11/image2.png)

출처: <http://jsfiddle.net/aljordan82/4ewe9/>



## 구현 결과
- CodeMirror 에디터에서 블럭 지정 후, 하이라이트 버튼을 클릭하면 코드 배경화면이 빨간색(Bad case)와 초록색(Good case)로 변경된다.
- CodeMirror 에디터에서 하이라이트된 지정된 코드를 블럭 지정 후, 되돌리기 버튼을 클릭하면 배경화면 색상이 원래대로 돌아온다.

![image](/assets/img/2022-02-14-Project SW Test Forum11/image3.png)
