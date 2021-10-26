---
title: "무기체계 소프트웨어의 신뢰성 시험 개요"
excerpt: "무기체계 소프트웨어의 신뢰성 시험을 소개한다."
categories:
  - SW Test
last_modified_at: 2020-07-20
layout: post
---
- 본 글은 무기체계 소프트웨어의 신뢰성 시험을 소개하는 글이며, '방위사업청 매뉴얼 제2020-8호'에 참고하여 작성하였다.



- 고 신뢰성이 요구되는 무기체계 소프트웨어는 '방위사업청 메뉴얼'을 준수하여 개발된다.
- '방위사업청 메뉴얼'은 요구 사항 분석에서의 오류 등에 대한 테스트를 포함한다는 내용을 체계화한 'V-모델' 소프트웨어 개발 프로세스에 기반한다.
- '방위사업청 메뉴얼'은 'V-모델' 소프트웨어 개발 프로세스에 기반하여 '신뢰성 시험' 방법이 작성되어 있다. 



## 신뢰성 시험
- 신뢰성 시험이란 소프트웨어 코드가 일으킬 수 있는 결함을 사전에 식별하여 제거하기 위한 시험이다.
- 소프트웨어 신뢰성 시험은 정적시험(코딩규칙 검증, 취약점 점검, 소스코드 메트릭 점검)과 동적시험(코드실행률 점검)으로 구분하여 수행한다.



## 정적시험
- 소프트웨어를 실행하지 않은 상태에서 잠재적인 결함을 검출하는 시험을 말하며, 코딩 규칙(Coding Rule) 검증, 취약점 점검 그리고 소스코드 메트릭(Code Metrics) 점검을 의미한다.
- 개발자들은 신뢰성 시험을 피하기 위해서 파이썬 등의 신뢰성 시험에 지정되지 않는 언어를 사용하는 경우가 있다. 해당 경우, 신뢰성 검증을 위한 방안을 서로 협의해야 한다.
- 일반적인 무기체계 소프트웨어에서는 보안성 시험을 수행하지 않는다. 전장관리시스템, 그룹웨어 같은 극히 제한된 시스템에서 보안성 시험을 수행한다. 보안성 시험은 행정안전부의 '소프트웨어 개발보안 가이드'를 준수해야 한다.


### 코딩 규칙 검증
- 소프트웨어 구현에 적용하는 소스 코드 작성 규칙을 점검한다.
- 적용 대상 언어는 C, C++, C#, JAVA 이며. 대상 언어별 적용되는 표준은 다음과 같다.<br>
C: MISRA C: 2012<br>
C++: MISRA C++: 2008, JSF++(Koint Strike Figher Air Vehicle C++) <br>
=> 일반적으로 MISRA C++: 2008 규칙으로 검증한다.<br>
Java: Code conventions for the Java Programming Language(Oracle)<br>
C#: C# Coding conventions 또는 .NET Framework Design Guideline<br>(Microsoft)

- 전력형 무기 즉, 방위력 개선사업으로 개발되는 소프트웨어는 MISRA C: 2012, MISRA C++: 2008 가이드라인을 를 통해 검증하며, 국산화 소프트웨어는 DAPA 가이드라인을 통해 검증한다.<br>
ex) KF-X에 탑재되는 소프트웨어는 국산화 소프트웨이므로 DAPA 규칙으로 검증한다.

- MISRA C: 2012에 대한 자세한 내용은 다음 글에서 확인할 수 있다.

참고: <https://scribnote5.github.io/categories/#misra-c>

- C언어 보안 취약점 방지를 위한 시큐어 코딩 표준에는 CWE, CERT C, IS/IOEC TS 17961:2013, C Secure, MISRA C가 존재한다.
- MISRA C/C++ 가이드라인은 다른 시큐어 코딩 표준과 겹치는 내용을 포함하고 있기에, MISRA C/C++ 가이드라인을 검증하면 보안 취약점을 준수할 수 있다. 

출처: <https://m.blog.naver.com/PostView.nhn?blogId=mds_datasecurity&logNo=221422521951&proxyReferer=https%3A%2F%2Fwww.google.com%2F>



### 소스코드 메트릭 점검
- 소프트웨어의 복잡도 감소, 유지보수 용이성 증대 등 소프트웨어 품질향상을 위한 소스 코드의 품질 측정지표다.
- 소스코드 메트릭에 대한 자세한 내용은 다음 글에서 확인할 수 있다. 

참고: <https://scribnote5.github.io/code%20metrics/static%20test/Code-Metrics1/>



### 취약점 점검
- 소프트웨어 소스 코드가 CWE(Common Weakness Enumeration) 목록에 정의된 취약점을 포함하고 있는지 점검한다. 
- CWE는 언어에 따라 CWE-658: C, CWE-659: C++, CWE-660: Java로 분류된다.
- MISRA C/C++ 가이드라인을 준수 한다면, CWE에서 선정한 취약점도 보완할 수 있다. 
- CWE에 대한 자세한 내용은 다음 글에서 확인할 수 있다.

참고: <https://scribnote5.github.io/cwe/static%20test/CWE658-1/>



## 동적시험
- 소프트웨어를 실제 하드웨어(Target)에 탑재한 상태에서 소프트웨어통합시험절차서에 기술된 시험절차에 따라 요구사항기반으로 소프트웨어 코드 실행률(Coverage)을 점검하는 것을 말한다.

- 동적시험에 대한 자세한 내용은 다음 글에서 확인할 수 있다. 

참고: <https://scribnote5.github.io/dynamic%20test/Dynamic-Test1/>
