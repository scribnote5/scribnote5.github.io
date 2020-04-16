---
title: "&nbsp;3. Spring boot 프로젝트 초기 설정"
excerpt: "Spring boot 프로젝트 초기 설정 과정을 소개한다."

categories:
  - Web
  - Project Lab

last_modified_at: 2020-04-22
---
- Spring boot 프로젝트 초기 설정 과정을 소개한다.


## Spring boot 프로젝트 생성
- New -> 'Spring Starter Project'를 클릭한다. 
- 하단 그림과 같이 프로젝트의 정보를 입력한 다음 'Next'를 클릭한 다음 'Finish'를 클릭하여 프로젝트를 생성한다. 의존 라이브러리 추가는 'Gradle 설정' 목차에서 텍스트로 직접 입력할 것이므로 통과한다.


![image](/assets/images/2020-05-10-Project Lab3/image1.png)



## Gradle 설정


### Gradle 설정 파일 종류
- settings.gradle: gradle 멀티 프로젝트 설정 관련 파일이다. 
- gradlew.bat: 원도우 실행 배치 스크립트 파일이다.
- gradle/wrapper/gradle-wrapper.properties: gradle 버전 설정 관련 파일이다. 
- build.gradle: 라이브러리 의존성 설정 관련 파일이다.


### Gradle 버전 변경
- **Powershell**를 사용하여 gradle 버전을 6.3으로 변경한다.

```
# 프로젝트 경로로 이동
$ cd C:\Users\<User>\Desktop\Spring\workspace\<Project>

# gradle 버전 변경
$ ./gradlew.bat wrapper --gradle-version 6.3

# 변경된 gradle 버전 확인
$ ./gradlew.bat -v 
```

![image](/assets/images/2020-05-10-Project Lab3/image2.png)


### build.gradle 설정
```
$ build.gradle
```
```
// 외부 의존 라이브러리를 클래스 패스에 추가한다. 
buildscript {
	ext { 
		// 스프링 부트의 버전을 지정한다.
		springBootVersion = '2.2.6.RELEASE'
	}
	// 의존성 라이브러리를 다운받는 원격 저장소를 지정한다.  
	repositories {
		mavenCentral()
		jcenter()
	}
	// 프로젝트 개발에 필요한 의존성을 선언한다.
	dependencies {
		classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
	}
}

// 자바 플러그인을 설정한다. 
apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

group = 'kr.ac'
version = '0.0.1-SNAPSHOT'
// 자바 버전을 지정한다. 
sourceCompatibility = '1.8'

repositories {
	mavenCentral()
	jcenter()
}

// 프로젝트 개발에 필요한 의존 라이브러를 선언한다. 
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.data:spring-data-rest-hal-browser'
	
	compileOnly 'org.projectlombok:lombok'
	
	compileOnly 'org.bgee.log4jdbc-log4j2:log4jdbc-log4j2-jdbc4:1.16'
	
	runtimeOnly 'org.springframework.boot:spring-boot-devtools'
	
	runtimeOnly 'com.h2database:h2'
	runtimeOnly 'org.mariadb.jdbc:mariadb-java-client'
	
	annotationProcessor 'org.projectlombok:lombok'
	
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

- gradle 버전이 업데이트 되면서 compile 키워드는 deprecated(앞으로 사라지게 됨) 되었다. 대신 api와 implementation을 키워드가 새로 생겨났다. api 키워드는 기존 compile 키워드와 동일하며 연관된 모든 의존 라이브러리를 재빌드한다. 반면 implementation 키워드는 연관된 단일 의존 라이브러리만 재빌드한다. 따라서 implementation 키워드를 사용하는 경우 빌드 시간이 감소한다.
- 이후 추가적으로 사용하는 의존 라이브러리는 있는 경우 명시하도록 하겠다.

출처: <https://jongmin92.github.io/2019/05/09/Gradle/gradle-api-vs-implementation/>
<https://sikeeoh.github.io/2017/08/28/implementation-vs-api-android-gradle-plugin-3/>
<https://velog.io/@conatuseus/gradle-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EB%A5%BC-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EB%A1%9C-%EB%B3%80%EA%B2%BD%ED%95%98%EA%B8%B0-jzk45lg1en>


### Gradle Refresh
- 수정한 build.gradle 파일의 의존성 라이브러리를 프로젝트에 반영시킨다. 
- Project -> Alt + Enter -> Gradle - > Refresh Gradle Project

![image](/assets/images/2020-05-10-Project Lab3/image3.png)



## Lombok
- Lombok은 자바에서 Model(DTO, VO, Domian)객체를 생성할 때 필요한 필드들의 Getter/Setter, ToString 그리고 생성자 코드를 자동으로 생성하는 라이브러리이다. 
- 자세한 설치 및 설정 방법은 하단 출처에서 자세하게 다루고 있기에 참고하여 따라하면 된다.

출처: <https://medium.com/@dongchimi/%EC%9D%B4%ED%81%B4%EB%A6%BD%EC%8A%A4%EC%97%90-lombok-%EC%84%A4%EC%B9%98-%EB%B0%8F-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-b3489875780b>



## properties 파일 yml 파일로 변경
- 기존 application.properties 파일을 가독성이 좋고 이해하기 쉬운 yml 확장자로 변경한다.

![image](/assets/images/2020-05-10-Project Lab3/image4.png)

- 만약 여러 개의 yml 파일을 사용하고 싶으면 하단 출처를 참고한다.

출처: <https://dhsim86.github.io/web/2017/03/28/spring_boot_profile-post.html>
