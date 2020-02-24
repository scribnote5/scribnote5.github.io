---
title: "Naming Rule(변수명, 함수명 정하기) - 종류"
excerpt: "대표적으로 알려진 유명한 Naming Rule에 대하여 소개하기"

categories:
  - Software Testing

  
last_modified_at: 2020-04-14
---
- 대표적으로 알려진 유명한 naming rule에 대하여 소개한다.
- 소프트웨어 테스팅 엔지니어로서, 다른 사람들의 코드를 리뷰하면서 어떠한 naming rule이 존재하는지 궁금하여 조사하였다. 
- 프로젝트의 상황에 가장 적합한 naming rule은 팀에서 결정한다.
- 프로젝트의 초기에 명명법을 결정하고 모든 개발자들이 규칙을 따라 코드를 작성한다.



## PascalCasing(파스칼 케이싱)
- 클래스, 열거형, 이벤트, 메서드 등의 이름을 만들 때에는 대문자로 시작하는 변수명을 사용한다.
- 복합어일 경우 중간에 시작하는 새로운 단어는 대문자를 사용한다.
ex) UtilityBox, MainFrame



## CamelCasing(카멜 케이싱)
- 메서드의 매개변수의 이름에 적용되는데 첫번째 문자는 소문자로 시작하고 복합어인 경우 파스칼 케이싱과 동일하게 적용한다.
- 동일한 이름을 가지는 두 항목을 구분하는 용도로도 사용한다.
ex) utilityBox, mainFrame



## GNU Naming Convention
- Linux의 프로젝트들은 GNU Naming Convention이라는 형태의 명명법을 주로 사용한다.
모두 소문자를 사용하고 복합어 사이를 '_'를 사용하여 연결한다.
ex) gtk_widget_activate



## Constant(상수)
- 거의 모든 명명법에서 상수를 표기하는 방법은 거의 동일하다.
- 모든 문자를 대문자로 사용하는 GNU Naming Convention의 형태를 사용한다.
ex) DEFAULT_COUNTRY_CODE

출처: <http://androphil.tistory.com/42>