---
title: "정적 및 동적시험과 화이트박스 및 블랙박스 테스팅"
excerpt: "정적 및 동적시험과 화이트박스 및 블랙박스 테스팅을 소개한다."
categories:
  - SW Test
last_modified_at: 2020-07-30
layout: post
---
- 정적시험 및 동적시험과 화이트박스 및 블랙박스 테스팅을 소개한다.


## 정적시험
- 소프트웨어를 실행하지 않은 상태에서 잠재적인 결함을 검출하는 시험을 말하며, 코딩 규칙(Coding Rule) 검증, 취약점 점검 그리고 소스코드 메트릭(Code Metrics) 점검을 의미한다.
- 개발자들은 신뢰성 시험을 피하기 위해서 파이썬 등의 신뢰성 시험에 지정되지 않는 언어를 사용하는 경우가 있다. 해당 경우, 신뢰성 검증을 위한 방안을 서로 협의해야 한다.
- 일반적인 무기체계 소프트웨어에서는 보안성 시험을 수행하지 않는다. 전장관리시스템, 그룹웨어 같은 극히 제한된 시스템에서 보안성 시험을 수행한다. 보안성 시험은 행정안전부의 '소프트웨어 개발보안 가이드'를 준수해야 한다.


### 코딩 규칙 검증
- 소프트웨어 구현에 적용하는 소스 코드 작성 규칙을 점검한다.
- 적용 대상 언어는 C, C++, C#, JAVA 이며. 대상 언어별 적용되는 표준은 다음과 같다.
- C: MISRA C 2012
- C++: MISRA C++: 2008, JSF++(Koint Strike Figher Air Vehicle C++) => 일반적으로 MISRA C++: 2008 규칙으로 검증한다.
- Java: Code conventions for the Java Programming Language(Oracle)
- C#: C# Coding conventions(StyleCop) 또는 .NET Framework Design Guideline(Microsoft, FxCop) => C# Coding conventions은 단순 코드 스타일을 수정하는 규칙으로 .NET Framework Design Guideline 보다 정적시험 수정이 쉽다.

- 전력형 무기 즉, 방위력 개선사업으로 개발되는 소프트웨어는 MISRA C 2012, MISRA C++: 2008 가이드라인을 를 통해 검증하며, 국산화 소프트웨어는 DAPA 가이드라인을 통해 검증한다.<br>
ex) KF-X에 탑재되는 소프트웨어는 국산화 소프트웨이므로 DAPA 규칙으로 검증한다.

- MISRA C 2012에 대한 자세한 내용은 다음 글에서 확인할 수 있다.

참고: <https://scribnote5.github.io/categories/#misra-c>

- C언어 보안 취약점 방지를 위한 시큐어 코딩 표준에는 CWE, CERT C, IS/IOEC TS 17961:2013, C Secure, MISRA C가 존재한다.
- MISRA C/C++ 가이드라인은 다른 시큐어 코딩 표준과 겹치는 내용을 포함하고 있기에, MISRA C/C++ 가이드라인을 검증하면 보안 취약점을 준수할 수 있다. 

출처: <https://m.blog.naver.com/PostView.nhn?blogId=mds_datasecurity&logNo=221422521951&proxyReferer=https%3A%2F%2Fwww.google.com%2F>

- Java 정적 시험 규칙인 Oracle Code Convention의 경우 현재 상용 중인 정적 시험 도구에서 모든 규칙을 다 지원하지 않으므로 여러 도구를 같이 사용하여, 정적 시험 규칙을 최대한 준수한다.
- 어느 도구에서 어떤 규칙을 지원하는지 매핑 테이블을 통하여 관리해야 한다.(실사 대응)
ex) 정적시험 도구 STATIC과 Sparrow를 동시에 사용


### 소스코드 메트릭 점검
- 소프트웨어의 복잡도 감소, 유지보수 용이성 증대 등 소프트웨어 품질향상을 위한 소스 코드의 품질 측정지표다.
- 소스코드 메트릭에 대한 자세한 내용은 '소스코드 메트릭(Code Metrics) 개요' 게시글에서 확인할 수 있다. 



### 취약점 점검
- 소프트웨어 소스 코드가 CWE(Common Weakness Enumeration) 목록에 정의된 취약점을 포함하고 있는지 점검한다. 
- CWE는 언어에 따라 CWE-658: C, CWE-659: C++, CWE-660: Java로 분류된다.
- MISRA C/C++ 가이드라인을 준수 한다면, CWE에서 선정한 취약점도 보완할 수 있다. 
- CWE에 대한 자세한 내용은 'CWE(Common Weakness Enumeration) 개요' 게시글에서 확인할 수 있다.

- Java 취약점 점검 규칙인 CWE-660의 경우, 현재 상용 중인 정적 시험 도구에서 모든 규칙을 다 지원하지 않으므로 여러 도구를 같이 사용하여, 정적 시험 규칙을 최대한 준수한다.
- 어느 도구에서 어떤 규칙을 지원하는지 매핑 테이블을 통하여 관리해야 한다.(실사 대응)
ex) STATIC, Sparrow, PMD, FindBugs를 사용



## 동적시험
- 소프트웨어를 실제 하드웨어(Target)에 탑재한 상태에서 소프트웨어통합시험절차서에 기술된 시험절차에 따라 요구사항기반으로 소프트웨어 코드 실행률(Coverage)을 점검하는 것을 말한다.
- 동적시험에 대한 자세한 내용은 '무기체계 소프트웨어의 동적시험 개요' 게시글에서 확인할 수 있다. 



## 블랙박스 및 화이트박스 테스팅

![image](/assets/img/2020-07-30-Reliability Test2/image1.png)


출처: <https://m.blog.naver.com/PostView.nhn?blogId=suresofttech&logNo=220965464819&proxyReferer=https%3A%2F%2Fwww.google.com%2F>
