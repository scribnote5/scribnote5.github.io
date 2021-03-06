---
title: "무기체계 소프트웨어의 동적시험 개요"
excerpt: "무기체계 소프트웨어의 동적시험을 소개한다."

categories:  
  - Dynamic Test

last_modified_at: 2020-07-25
---
- 본 글은 무기체계 소프트웨어의 동적시험을 소개하는 글이며, '방위사업청 매뉴얼 제2020-8호'에 참고하여 작성하였다.


## 동적시험
- 소프트웨어를 실제 하드웨어(Target)에 탑재한 상태에서 소프트웨어통합시험절차서에 기술된 시험절차에 따라 요구사항기반으로 소프트웨어 코드 실행률(Coverage)을 점검하는 것을 말한다.
- 동적시험을 수행함으로서 소스코드의 구조적 결함뿐 아니라 통합된 소프트웨어의 기능적 결함을 함께 검출하고, 요구사항 기반으로 설계된 소프트웨어 통합시험 절차서의 시험 충분성을 검증한다.
- 소프트웨어를 실제 하드웨어(타겟)에 탑재한 상태에서 수행한다. 타겟에서 테스트를 수행하는 이유는 타겟 환경의 경우 하드웨어에 사양(레지스터리 등)에 의해서 호스트 환경과 다른 결과를 얻을 수 있기 때문이다.
- 동적시험 커버리지 종류(문장, 분기, MC/DC) 커버리지를 100% 목표로 채워야 하며, 예외사항(방어코드, 협의된 코드 등)은 커버리지에서 제외할 수 있다.



## 동적시험 커버리지 종류
- 커버리지: 소프트웨어 테스트가 충분히 수행되어는가를 나타내는 지표 중 하나로서 말 그대로 테스트를 진행했을 때 코드 자체가 얼마나 실행되었나를 비율로 표현한 것이다. 


### 문장(Statement) 커버리지
- 코드 실행률의 가장 기본적인 수준에 해당되는 것으로 시험대상 소프트웨어 소스 코드내의 문장 중 동적시험간 적어도 한 번 이상 시험된 문장의 비율(%)을 의미한다.


### 분기(Branch) 커버리지
- 시험대상 소프트웨어 소스 코드내의 분기문 중 동적시험간 참(True), 거짓(False)이 적어도 한 번 이상 시험된 비율(%)을 의미한다.
MC/DC(Modified Condition/Decision Coverage) 커버리지
- 가장 높은 수준의 코드 실행률로써 시험대상 소프트웨어 소스 코드내 분기문에 있는 모든 조건식 중 개별 조건식의 독립적인 변화가 분기문의 참, 거짓에 영향을 미치는 모든 조합에 대해 동적시험간 적어도 한 번 이상 시험된 비율(%)을 의미한다.

![image](/assets/images/2020-07-25-Dynamic Test1/image1.png)

ex) OR 연산자 기준 설명<br>
조건 A가 T로 고정이고 조건 B가 T 또는 F로 변경 된다면, 이는 결과에 영향을 미치지 않는다. <br>
조건 B가 T로 고정이고 조건 A가 T 또는 F로 변경 된다면, 이는 결과에 영향을 미친다. <br>
조건 A가 F로 고정이고 조건 B가 T 또는 F로 변경 된다면, 이는 결과에 영향을 미친다. <br>
조건 B가 F로 고정이고 조건 A가 T 또는 F로 변경 된다면, 이는 결과에 영향을 미치지 않는다. <br>
따라서  조건 A와 조건 B가 T인 경우는 MC/DC 커버리지의 테스트케이스가 될 수 없다.<br>

ex) AND 연산자 기준 설명<br>
조건 A가 T로 고정이고 조건 B가 T 또는 F로 변경 된다면, 이는 결과에 영향을 미친다.<br>
조건 B가 T로 고정이고 조건 A가 T 또는 F로 변경 된다면, 이는 결과에 영향을 미친다. <br>
조건 A가 F로 고정이고 조건 B가 T 또는 F로 변경 된다면, 이는 결과에 영향을 미치지 않는다. <br>
조건 B가 F로 고정이고 조건 A가 T 또는 F로 변경 된다면, 이는 결과에 영향을 미치지 않는다. <br>
따라서  조건 A와 조건 B가 F인 경우는 MC/DC 커버리지의 테스트케이스가 될 수 없다.<br>

출처: <https://m.blog.naver.com/PostView.nhn?blogId=suresofttech&logNo=220636029506&proxyReferer=https%3A%2F%2Fwww.google.com%2F> <br>
<https://m.blog.naver.com/shiftspace/220561755364>



## Top-down(하향식) 테스팅
- 동적시험은 Top-down 테스팅으로 수행된다. 하위 수준의 컴포넌트를 스텁(stubs)으로 대체하면서 가장 상위 수준의 컴포넌트를 먼저 테스트하는 통합 테스팅(integration testin)을 점증적으로 접근하는 방법이다. 테스트된 컴포넌트는 하위 수준의 컴포넌트를 테스트하는데 사용된다. 가장 하위 레벨의 컴포넌트를 테스트 할 때까지 해당 절차가 반복된다. 



## 동적시험 수행 순서


### 1. 요구사항 기반 시험
- 소프트웨어 요구사항명세서에 정의된 요구사항들을 통합된 소프트웨어로 수행한다.
- 명세의 오류나 기능적 결함을 검출하고 수행하여 측정된 코드 실행률을 점검한다.
- 요구사항 기반으로 코드 실행률을 점검하는 grey box 테스팅이다.
- 요구사항 기반 시험만으로는 소스 코드상의 방어코드 및 소스 코드의 확장성 등의 부수적인 문제로 인하여 코드 실행률의 목표값 100%를 달성하기 어렵다.
- Top-Down(하향식) 방식을 적용하여 요구사항 기반 시험 후 목표값 미충족 부분에 한해 구조기반 시험을 수행 해야한다.
- 요구사항기반 시험 절차는 다음과 같다.
1. 요구사항 정의서와 같은 기술 문서를 분석하여, 소프트웨어 통합시험 절차서 작성
2. 소프트웨어 통합시험절차서에 기술된 시험절차에 따라 통합 시험 수행(입력 값 생성)
3. 타겟기반으로 테스팅을 수행하여 출력 값 확인

출처: <http://blog.naver.com/PostView.nhn?blogId=suresofttech&logNo=221250159366&parentCategoryNo=59&categoryNo=109&viewDate=&isShowPopularPosts=false&from=postList>


### 2. 구조기반 시험
- 소스 코드의 내부 구조를 분석하고 변수값을 제어하여 함수의 동작을 확인하는 White-Box 시험으로 소스 코드의 구조적 결함을 검출한다.
- 요구사항 기반 시험에서 채우지 못한 커버리지는 구조기반으로 채운다.
- 목표값 미달시 시험대상 소프트웨어 소스 코드를 수정하거나 또는 필요한 조치를 취한 후 재시험하여 목표값 달성 여부를 확인한다.
- 방어 코드(Defensive Code), 무한 반복문 등 소프트웨어 신뢰성 시험 도구의 한계로 인하여 목표값을 달성 할 수 없는 경우에는 미달성 분석 보고서를 작성해야 한다.