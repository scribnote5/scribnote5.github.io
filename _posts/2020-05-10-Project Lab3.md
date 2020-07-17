---
title: "Project Lab 3. Gradle Multi Module 프로젝트 구성"
excerpt: "Gradle Multi Module 기반의 프로젝트 구조와 구성 절차를 소개한다."

categories:
  - Web
  - Project Lab

last_modified_at: 2020-07-17
---
- Gradle Multi Module 기반의 프로젝트 구조와 구성 절차를 소개한다.



## RESTful 시스템을 위한 Gradle Multi Module 프로젝트


### REST & RESTful
- REST란, '웹에 존재하는 모든 자원(이미지, 동영상, DB 자원)에 고유한 URI를 부여해 활용'하는 것으로, 자원을 정의하고 자원에 대한 주소를 지정하는 방법론을 의미한다.
이런 REST의 형식을 따른 시스템을 RESTful 이라고 부른다.
- 서버-클라이언트의 역할이 명확하게 분리되면서 하나의 큰 애플리케이션이 여러 개의 작은 애플리케이션으로 분리된다.
- 이러한 RESTful 시스템을 개발할 때 가장 큰 문제점은 소스 코드의 중복 처리 및 동일성 보장이다. 여러 개의 작은 애플리케이션이 공통으로 가지는 Domain(구조와 규칙 등)을 동일하게 보장하는 메커니즘이 없다. 따라서, 개발자는 동일한 Domain을 가지고 있는 애플리케이션을 복붙하며 개발하게 된다.

출처: <https://medium.com/@hckcksrl/rest%EB%9E%80-c602c3324196><br>
REST 개념: <https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html><br>


### Gradle Multi Module
- 멀티 모듈 프로젝트는 기존의 단일 프로젝트를 프로젝트 안의 모듈로서 갖을 수 있을 수 있는 구조를 제공한다.
- 개발자는 하나의 시스템에서 중심 도메인을 모듈로 분리하여 위와 같은 소스 코드의 중복 처리 및 동일성을 보장한다. 
- 하단의 우아한형제 기술 블로그를 참고하여, 다음과 같이 프로젝트를 구성하였고 그 원칙을 따르고자 하였다. 비교적 작은 시스템을 구축하는 것이기에, 필요하다고 생각하는 모듈을 생성하였고 다음과 같이 역할을 위임하였다. 전체적인 구조는 다음과 같다. 

lab: 루트 프로젝트<br>
└─ module-system-common<br>
- 하나의 프로젝트에서 모든 모듈이 사용된다. 
- 가능하면 해당 모듈을 사용하지 않는다.
- util 클래스, error 클래스, validation 클래스, Web Resources(html, css, javascript 등)과 같이 공통으로 사용하는 파일이 위치한다.

└─ module-domain-core<br>
- 시스템 핵심 도메인을 다루는 모듈이 위치한다.
- domain 클래스, service 클래스, repository 클래스 파일이 위치한다.

└─ module-app-api<br>
- API 서버를 당담하는 모듈이 위치한다.
- controller(RestControllr) 클래스 파일이 위치한다.

└─ module-app-admin<br>
- 관리자 계층을 다루는 모듈이 위치한다.
- controller 클래스, Web Resources(html, css, javascript 등) 파일이 위치한다.

└─ module-app-web<br>
- 일반 사용자 계층을 다루는 모듈이 위치한다.
- controller 클래스, Web Resources(html, css, javascript 등) 파일이 위치한다.

출처: <https://woowabros.github.io/study/2019/07/01/multi-module.html>


## Gradle api, implementation 키워드 차이
- gradle 버전이 업데이트 되면서 compile 키워드는 deprecated(앞으로 사라지게 됨) 되었다. 대신 api와 implementation을 키워드가 새로 생겨났다. 
- api 키워드는 기존 compile 키워드와 동일하며 연관된 모든 의존 라이브러리를 재빌드한다. 
- implementation 키워드는 연관된 단일 의존 라이브러리만 재빌드한다. 
- 따라서, 무분별한 api 키워드 대신  implementation 키워드를 사용하면 빌드 시간을 감소시킬 수 있다.

ex) <br> 
api: 의존 라이브러리 수정시 해당 모듈을 의존하고 있는 모듈들 또한 재빌드<br>
A(api) <- B <- C 일 때, C에서 A 접근 가능<br>
A 수정시 B 와 C 모두 재빌드

implementaion: 의존 라이브러리 수정시 본 모듈까지만 재빌드<br>
A(implementation) <- B <- C 일 때, C에서 A 접근 불가<br>
A 수정시 B 까지 재빌드

출처: <https://jongmin92.github.io/2019/05/09/Gradle/gradle-api-vs-implementation/>
<https://sikeeoh.github.io/2017/08/28/implementation-vs-api-android-gradle-plugin-3/>


### 의존관계 설정
- 하단의 우아한형제 기술 블로그에서는 여러 의존성 관리 방법 중 gradle의 api, implementation 키워드를 사용한 의존관계 설정을 권장한다.
- 프로젝트의 모듈들은 해당 방식으로 의존 관계를 설정할 예정이다.

출처: <https://woowabros.github.io/study/2019/07/01/multi-module.html>



## Gradle Project 생성
- File -> New -> Project... -> Gradle 선택

![image](/assets/images/2020-05-10-Project Lab3/image1.png)

- 프로젝트 정보를 입력 후 생성한다.
- 프로젝트 생성이 완료되면 사용하지 않는 src 디렉터리를 삭제한다.

![image](/assets/images/2020-05-10-Project Lab3/image2.png)


## Gradle 버전 변경
```
# 프로젝트 경로로 이동한 다음, gradle 버전을 변경
$ ./gradlew.bat wrapper --gradle-version 6.5.1

# 변경된 gradle 버전 확인
$ ./gradlew.bat -v 
------------------------------------------------------------
Gradle 6.5.1
------------------------------------------------------------

Build time:   2020-06-30 06:32:47 UTC
Revision:     66bc713f7169626a7f0134bf452abde51550ea0a

Kotlin:       1.3.72
Groovy:       2.5.11
Ant:          Apache Ant(TM) version 1.10.7 compiled on September 1 2019
JVM:          11.0.7 (Oracle Corporation 11.0.7+8-LTS)
OS:           Windows 10 10.0 amd64
```


## Gradle Multi Module 생성 
- Proejct 우클릭 -> New -> Module... 

![image](/assets/images/2020-05-10-Project Lab3/image3.png)

- Gradle 선택 후 모듈 정보를 입력하여 모듈을 생성한다.
- 위와 같은 절차로 하나의 프로젝트 내에서 'module-system-common', 'module-domain-core', 'module-app-api', 'module-app-web', 'module-app-admin' 모듈을 생성한다. 

![image](/assets/images/2020-05-10-Project Lab3/image4.png)

- 모듈의 의존성을 설정한다.

```
lab/setting.gradle
```

```
rootProject.name = 'lab'
include 'module-system-common'
include 'module-domain-core'
include 'module-app-api'
include 'module-app-web'
include 'module-app-admin'
```

- root 프로젝트의 의존성 라이브러리를 선언한다. 

```
lab/build.gradle 
```

```
// 외부 의존 라이브러리를 클래스 패스에 추가한다.
buildscript {
   ext {
       // 스프링 부트의 버전을 지정한다.
       springBootVersion = '2.3.1.RELEASE'
   }
   // 의존성 라이브러리를 다운받는 원격 저장소를 지정한다.
   repositories {
       mavenCentral()
       jcenter()
   }
   // 프로젝트 개발에 필요한 의존성을 선언한다.
   dependencies {
       classpath "org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}"
       classpath "io.spring.gradle:dependency-management-plugin:1.0.9.RELEASE"
   }
}

subprojects {
   // 자바 플러그인을 설정한다.
   apply plugin: 'java'
   apply plugin: 'eclipse'
   apply plugin: 'org.springframework.boot'
   apply plugin: 'io.spring.dependency-management'
   apply plugin: 'idea'

   group = 'kr.ac.univ'
   version = '0.0.1-SNAPSHOT'
   // 자바 버전을 지정한다.
   sourceCompatibility = 11

   repositories {
       mavenCentral()
       jcenter()
   }

   // 프로젝트 개발에 필요한 공통 의존성 라이브러리를 선언한다.
   dependencies {

   }
}

// 모듈의 의존성 라이브러리를 선언한다.
project(':module-system-common') {
   dependencies {

   }
}

project(':module-domain-core') {
   dependencies {
       compile project(':module-system-common')
   }
}

project(':module-app-web') {
   dependencies {
       compile project(':module-system-common')
       compile project(':module-domain-core')
   }
}

project(':module-app-admin') {
   dependencies {
       compile project(':module-system-common')
       compile project(':module-domain-core')
   }
}

project('module-app-api') {
   dependencies {
       compile project(':module-system-common')
       compile project(':module-domain-core')
   }
}
```

- 모듈의 역할에 따라 실행 가능한 자바 파일이 생성되지 않도록 한다.

```
module-domain-core/build.gradle, module-system-common/build.gradle
```

```
// 해당 모듈은 실제 실행되는 모듈에 종속되므로, 실행 가능한 자바 파일을 생성하지 않는다.
bootJar { enabled = false }
jar { enabled = true }
```

출처: <https://ahndy84.tistory.com/16><br>
<https://jojoldu.tistory.com/123><br>
<https://cheese10yun.github.io/gradle-multi-module/><br>
<https://www.hanumoka.net/2019/10/04/springBoot-20191004-springboot-gradle-multimodule/><br>
<http://lyasee.com/articles/2018-09/%EC%8A%A4%ED%94%84%EB%A7%81-%EB%A9%80>
