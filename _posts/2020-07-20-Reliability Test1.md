---
title: "무기체계 소프트웨어의 신뢰성 시험 개요"
excerpt: "무기체계 소프트웨어의 신뢰성 시험을 소개한다."
categories:
  - SW Test
last_modified_at: 2022-07-10
layout: post
---
- 무기체계 소프트웨어의 신뢰성 시험을 소개하는 글이며, '방위사업청 매뉴얼 제2020-8호'에 참고하여 작성하였다.
- 고 신뢰성이 요구되는 무기체계 소프트웨어는 '방위사업청 메뉴얼'을 준수하여 개발된다.
- '방위사업청 메뉴얼'은 요구 사항 분석에서의 오류 등에 대한 테스트를 포함한다는 내용을 체계화한 'V-모델' 소프트웨어 개발 프로세스에 기반한다.
- '방위사업청 메뉴얼'은 'V-모델' 소프트웨어 개발 프로세스에 기반하여 '신뢰성 시험' 방법이 작성되어 있다. 



## 소프트웨어 신뢰성 시험
- 신뢰성 시험이란 소프트웨어 코드가 일으킬 수 있는 결함을 사전에 식별하여 제거하기 위한 시험이다.
- 소프트웨어 신뢰성 시험은 정적시험(코딩규칙 검증, 취약점 점검, 소스코드 메트릭 점검)과 동적시험(코드실행률 점검)으로 구분하여 수행한다.



## 소프트웨어 보안성시험
- 해킹 등 사이버공격의 원인인 보안약점을 개발단계에서 사전에 제거하기 위해 행정안전부 「소프트웨어 개발보안 가이드」를 적용하여 개발하였는지 확인하는 시험을 말하며 전장관리정보체계를 대상으로 수행한다.
- 전장관리정보체계에 사용되는 공개SW에 대해서는 보안성 시험을 수행하는 것을 원칙으로 한다.



## 소프트웨어 신뢰성/보안성 시험 확인
- 소프트웨어의 정적, 동적 시험과 전장관리정보체계 소프트웨어의 보안 취약점 제거를 위한 보안성 시험이 기술적인 정확성과 적절성을 가지고 정해진 요구사항을 충족하는 지를 결정하기 위해 검증을 통해 수집된 자료와 사실에 대해 검토하는 과정을 말한다.


## 소프트웨어 신뢰성 및 보안성 확보 활동 
- 사업관리부서장은 소프트웨어 신뢰성 및 보안성을 확보하기 위해 사업단계별로 다음 각 호와 같이 관리한다.


### 제안요청 단계
- 사업관리부서장은 제안요청서 작성 시 소프트웨어 신뢰성 및 보안성 확보방안을 제시 하도록 요청한다.
개발 준비단계
- 연구개발 주관기관은 체계개발실행계획서 및 소프트웨어 개발계획서 작성 시 소프트웨어 신뢰성 및 보안성 시험계획을 포함하여 작성한다.


### 소프트웨어 요구사항분석 단계
- 연구개발 주관기관은 소프트웨어요구사항명세서의 소프트웨어 품질특성 요구사항에 소프트웨어 신뢰성, 보안성 요구사항을 작성한다.


### 소프트웨어 구현단계
- 연구개발 주관기관은 소프트웨어요구사항명세서의 소프트웨어 품질특성 요구사항에 명시된 신뢰성 및 보안성 등 요구사항에 따라 소프트웨어를 개발하며, 전장관리정보체계는 행정안전부『소프트웨어 개발보안 가이드』에 따라 개발하여야 한다.
- 연구개발주관기관은 소프트웨어 신뢰성 및 유지보수성 등의 향상을 위하여 부록 7. 소프트웨어 신뢰성/보안성 시험 절차에 정의된 소스코드 메트릭(Code Metrics)를 준수하여 개발해야 한다.
- 연구개발 주관기관은 소프트웨어통합시험절차서에 소프트웨어의 신뢰성 및 보안성 시험절차를 포함하여 작성한다.


### 소프트웨어 통합 및 시험단계
- 연구개발주관기관은 부록 7. 소프트웨어 신뢰성/보안성 시험 절차에 따라 소프트웨어 신뢰성 시험(정적, 동적) 및 보안성 시험을 실시하고, 시험결과를 소프트웨어통합시험결과서에 반영한다.
- 기술지원기관은 소프트웨어 신뢰성 및 보안성 시험을 지원 및 점검하고, 시험결과를 확인한다.


### 개발시험평가 단계
- 연구개발주관기관, 사업관리부서장, 시험평가과장은 소프트웨어의 신뢰성 및 보안성 시험을 개발시험평가 항목으로 반영한다.


### 운용시험평가 단계
- 연구개발주관기관은 개발시험평가 단계에서 수행한 신뢰성/보안성 시험 이후 변경된 소프트웨어 대해서는 신뢰성/보안성 시험을 자체적으로 수행하고 그 결과를 사업관리부서에 제출한다.


### 규격화 단계
- 소스코드 제출시 최종 소스코드에 대한 신뢰성 및 보안성 시험 결과를 동시에 제출한다.


### 양산 단계
- 형상통제 제안기관(업체 포함)은 수정되는 소프트웨어에 대해 무기체계 개발단계의 소프트웨어 신뢰성 및 보안성 확보 활동 기준에 따라 신뢰성 및 보안성 시험을 실시하고, 그 결과(기술지원기관 검토 필요)를 형상통제 제안자료(형상통제제안서, 제안기관 자체검토서, 수정 후 국방규격 기술자료, 제안내용을 입증할 수 있는 관련자료 등)에 반영하여 형상관리책임기관에 제출한다.
 


## 소프트웨어 신뢰성시험 대상 소프트웨어

![image](/assets/img/2020-07-20-Reliability Test1/image1.png)

- 기개발, 상용 소프트웨어는 소프트웨어 상세설계 단계에서 수정없이 재사용이 확정된 경우 시험 대상에서 제외 할 수 있으나 수정을 한 경우는 소명 자료를 제출하고 해당 함수에 대하여 신뢰성시험을 수행해야 한다. 그러나 소명자료 작성이 더 어렵기에, 해당 함수가 아니라 파일 단위로 시함할 것을 권고한다.
- 범용적으로 사용하는 오픈소스 소프트웨어(ex, 소켓 관련 코드, 이더넷 관련 코드) 코드를 수정하는 경우 신뢰성 시험 대상에 포함된다.
- 신뢰성시험 대상이 아닌 코드를 신뢰성시험 대상 코드로 변환하면 해당 코드는 신뢰성시험 대상이 아니다. ex) MATLAB으로 작성된 코드를 C언어 코드로 변환하면, 해당 코드는 신뢰성시험 대상이 아님
- 보고서에 기재된 COTS 리스트와 프로젝트에서 사용하는 라이브러리 리스트를 비교한다. 만약 COTS에 등록되지 않은 라이브러리가 존재하는 경우 경로를 삭제해야 한다. 



## 실사 때 신뢰성시험 대상 항목 식별 유의사항
- 실사 때 보고서에 기재된 COTS 리스트와 프로젝트에서 사용하는 라이브러리 리스트를 비교하는 경우가 있다. 만약 COTS에 등록되지 않은 라이브러리가 존재하는 경우, 사전에 경로를 삭제해야 한다. 



## 기개발 코드(재사용 코드, 재활용 코드) 신뢰성 시험 적용 여부
- 재사용 코드: 기존 사업 코드를 수정하지 않고 사용 -> 신뢰성 시험 대상 X
- 재활용 코드: 기존 사업 코드를 수정하여 사용 -> 신뢰성 시험 O
- 재활용 코드 .c, .cpp 파일과 연관된 헤더 파일 수정 -> 신뢰성 시험 대상 X



## 상용 SW 증빙 방법
- 상용 소프트웨어를 증빙 할 수 있는 방법은 다음과 같다

1. 일반적인 방법: 구매 내역서(소프트웨어 리스트가 존재)
2. 최초로 받은 소프트웨어의 checksum을 증명하는 방법
3. 다운로드 링크를 통해서 소프트웨어를 다운받은 경우(visual studio 등): 다운로드 링크를 첨부하여 사용하고 있는 소프트웨어의 형상이 같은지 증명함
4. 소프트웨어가 국방 상용 제품 등록되어 있는 경우 등록증을 첨부함 



## 공개 SW 신뢰성시험 적용 기준
- 원칙적으로 공개 SW는 신뢰성 시험 대상이지만, 현재 순수 공개 SW 프로젝트는 신뢰성시험을 안하는 추세다. 
- 무기체계 연구개발 시 소스코드 공개 의무가 있는 공개SW를 사용해서는 안된다. 
- 단, 라이선스 구매 및 저작권자와 협의 등을 통해 소스 코드를 공개하지 않아도 되는 것을 증명한 경우에는 사용 가능하다.
* 공개SW인 운영체제 사용 필요시 운영체제만을 공개대상으로 하는 경우에 한하여 사업관리회의를 통하여 사용 여부를 결정한다.

- 연구개발 주관기관은 공개SW 사용 시 특허권 등 권리관계의 포함 여부를 사전에 확인하여 지식재산권 분쟁이 발생되지 않도록 관리하여야 한다.
- SW에 관한 지식재산권과 공개SW 라이선스 의무사항을 준수한다.
- 상세설계 단계에서 공개SW의 사용 필요성과 신뢰성 확보 여부를 제시하고 사업관리회의에서 신뢰성이 확보된 공개SW로 판단된 경우 신뢰성 시험 대상에서 제외할 수 있다. 그러나 신뢰성이 확보된 공개 SW(ex, 소켓 관련 코드, 이더넷 관련 코드)의 코드를 수정하는 경우 신뢰성시험 대상에 포함된다..
- 일부 방산회사에서는 공개 SW를 검사하는 툴을 사용하여 공개 SW 프로젝트임을 증명하고  신뢰성시험에서 제외하고 있다.
- 신뢰성이 확보되지 않은 공개 SW의 경우 시험 수행 범위를 결정한다.

- '⨂’에 대해서는 상세설계 단계에서 사용하고자 하는 공개SW 적용 사례, 커뮤니티 성숙도를 제시한 경우 이를 종합적으로 판단하여 사업관리회의를 통해 신뢰성 시험 대상에서 제외할 수 있다.
- '◯'는 정적시험 및 동적시험을 수행하고, 동적시험 수행 기준은 [부록 7]의 “제3장 소프트웨어 동적시험 절차”를 따른다.
- 수정 한 경우에는 해당 함수에 대하여 신뢰성 시험을 수행하여야 한다.

![image](/assets/img/2020-07-20-Reliability Test1/image2.png)