---
title: "무기체계 소프트웨어의 정적시험 개요"
excerpt: "무기체계 소프트웨어의 정적시험을 소개한다."
categories:
  - SW Test
last_modified_at: 2022-07-15
layout: post
---
- 본 글은 무기체계 소프트웨어의 정적시험을 소개하는 글이며, '방위사업청 매뉴얼 제2020-8호'에 참고하여 작성하였다.


## 정적시험
- 소프트웨어를 실행하지 않은 상태에서 잠재적인 결함을 검출하는 시험을 말하며, 코딩 규칙(Coding Rule) 검증, 취약점 점검 그리고 소스코드 메트릭(Code Metrics) 점검을 의미한다.
- 개발자들은 신뢰성 시험을 피하기 위해서 파이썬 등의 신뢰성 시험에 지정되지 않는 언어를 사용하는 경우가 있다. 해당 경우, 신뢰성 검증을 위한 방안을 서로 협의해야 한다.
- 일반적인 무기체계 소프트웨어에서는 보안성 시험을 수행하지 않는다. 전장관리시스템, 그룹웨어 같은 극히 제한된 시스템에서 보안성 시험을 수행한다. 보안성 시험은 행정안전부의 '소프트웨어 개발보안 가이드'를 준수해야 한다.



### 정적 시험 예외 처리 사례


## 상용 소프트웨어(C 파일)을 참조하는 헤더 파일(코드를 수정하여 시험 대상에 포함)에서 위배 발생
- 상용 소프트웨어(C 파일)은 시험 제외 대상이고 이를 참조하는 헤더 파일에서 '함수의 정의가 없다'라는 위배가 발생

- 1번째 예외 처리 방법: 상용 소프트웨어(C 파일)임을 증빙할 수 있는 서류를 준비한다.
- 2번째 예외 처리 방법: 상용 소프트웨어(C 파일)은 시험 제외 대상 파일로서 헤더 파일이 함수의 정의를 찾을 수 없다고 작성한다.



## FA 보고서 작성 방법
- 예외 처리 규칙과 오탐 규칙 모두 FA 보고서에 작성해야 한다.


### 예외 처리
- 규칙을 위배하지만 프로그램 수행 로직을 위해서, 유지보수 용이성을 위해서 위배를 수정할 수 없는 경우다.<br>
ex) C/C++에서 포인터 간 형변환


### 오탐 처리
- 정적시험 도구가 규칙을 잘못 분석하여 위배로 판단하는 경우다.
- 슈어소프트에서는 발급한 고객지원 확인서를 발급 받아 이를 증빙자료로 첨부한다.
- QAC를 비롯한 다른 정적시험 도구 개발 회사에서는 별도의 고객지원 확인서를 발급하지 않는다. 



## 정적시험 추세 현황
- 22.07 기준으로 기존 C++ MFC 프로젝트를 생산성이 좋은 C# 프로젝트로 대체함에 따라, 정적시험 추세다.(모든 정적시험 수행 현황이 아님을 강조 드립니다.)
- 윈도우 GUI 프로젝트들은 과거 C++ MFC 프로젝트 대신 생산성이 좋은 C# 프로젝트로 대체하여 개발하는 사례가 많아지고 있다.
- C#은 코딩 규칙인 C# Coding conventions(StyleCop)의 경우 C/C++과 다르게 위배 수정이 쉽고, 취약점 규칙(CWE)이 없으므로 정적시험 수행 공수를 많이 절약 할 수 있다. 



## 1. 코딩 규칙 검증
- 소프트웨어 구현에 적용하는 소스 코드 작성 규칙을 점검한다.
- 적용 대상 언어는 C, C++, C#, JAVA 이며. 대상 언어별 적용되는 표준은 다음과 같다.
- C: MISRA C 2012
- C++: MISRA C++: 2008, JSF++(Koint Strike Figher Air Vehicle C++) => 일반적으로 MISRA C++: 2008 규칙으로 검증한다.
- Java: Code conventions for the Java Programming Language(Oracle)
- C#: C# Coding conventions(StyleCop) 또는 .NET Framework Design Guideline(Microsoft, FxCop) => <span style="color:red; font-weight: bold">C# Coding conventions은 단순 코드 스타일을 수정하는 규칙으로 .NET Framework Design Guideline 보다 정적시험 수정이 쉽다.</span>

- 전력형 무기 즉, 방위력 개선사업으로 개발되는 소프트웨어는 MISRA C 2012, MISRA C++: 2008 가이드라인을 를 통해 검증하며, 국산화 소프트웨어는 DAPA 가이드라인을 통해 검증한다.<br>
ex) KF-X에 탑재되는 소프트웨어는 국산화 소프트웨이므로 DAPA 규칙으로 검증한다.

- MISRA C 2012에 대한 자세한 내용은 다음 글에서 확인할 수 있다.

참고: <https://scribnote5.github.io/categories/#misra-c>

- C언어 보안 취약점 방지를 위한 시큐어 코딩 표준에는 CWE, CERT C, IS/IOEC TS 17961:2013, C Secure, MISRA C가 존재한다.
- MISRA C/C++ 가이드라인은 다른 시큐어 코딩 표준과 겹치는 내용을 포함하고 있기에, MISRA C/C++ 가이드라인을 검증하면 보안 취약점을 준수할 수 있다. 

출처: <https://m.blog.naver.com/PostView.nhn?blogId=mds_datasecurity&logNo=221422521951&proxyReferer=https%3A%2F%2Fwww.google.com%2F>

- Java 정적 시험 규칙인 Oracle Code Convention의 경우 현재 상용 중인 정적 시험 도구에서 모든 규칙을 다 지원하지 않으므로 여러 도구를 같이 사용하여, 정적 시험 규칙을 최대한 준수한다.
- 어느 도구에서 어떤 규칙을 지원하는지 매핑 테이블을 통하여 관리해야 한다.(실사 대응)<br>
ex) STATIC, Sparrow, PMD, FindBugs를 사용



## 2. 소스코드 메트릭 점검
- 소프트웨어의 복잡도 감소, 유지보수 용이성 증대 등 소프트웨어 품질향상을 위한 소스 코드의 품질 측정지표다.
- 소스코드 메트릭에 대한 자세한 내용은 '소스코드 메트릭(Code Metrics) 개요' 게시글에서 확인할 수 있다. 



## 3. 취약점 점검
- 소프트웨어 소스 코드가 CWE(Common Weakness Enumeration) 목록에 정의된 취약점을 포함하고 있는지 점검한다. 
- CWE는 언어에 따라 CWE-658: C, CWE-659: C++, CWE-660: Java로 분류된다.
- MISRA C/C++ 가이드라인을 준수 한다면, CWE에서 선정한 취약점도 보완할 수 있다. 
- CWE에 대한 자세한 내용은 'CWE(Common Weakness Enumeration) 개요' 게시글에서 확인할 수 있다.

 - <span style="color:red; font-weight: bold">22.07 기준 Java 취약점 점검 규칙인 CWE-660의 경우, 현재 상용 중인 정적시험 도구에서는 모든 규칙을 다 지원하지 않으므로 여러 도구를 같이 사용하여, 정적시험 규칙을 최대한 준수하는 방향으로 진행한다.</span>
- 실사 대응을 위해 어느 도구에서 어떤 규칙을 지원하는지 매핑 테이블을 통하여 관리해야 한다.<br>
ex) STATIC, Sparrow, PMD, FindBugs를 사용


### 대표적인 소프트웨어 실행시간 관련 오류 예시(C/C++)

![image](/assets/img/2020-07-30-Reliability Test3/image1.png)