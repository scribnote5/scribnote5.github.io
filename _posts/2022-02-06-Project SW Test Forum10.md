---
title: "Project SW Test Forum 10. 운영 서버 배포 - 1"
excerpt: "- Vue.js 3와 Spring boot로 개발한 프로젝트 배포 과정을 소개한다."

categories:
  - Web
  - Project SW Test Forum Forum
last_modified_at: 2022-02-06
layout: post
---
- Vue.js 3와 Spring boot로 개발한 프로젝트 배포 과정을 소개한다.
- github: <https://github.com/scribnote5/sw_test_forum>



## 운영서버 배포를 위한 Vue.js 프로젝트 빌드
- 운영서버 환경은 prod 실행모드에서 환경변수 설정되어 있으므로, 다음 명령어를 사용하여 빌드한다.
- ‘프로젝트명-dist’라는 디렉터리(module-app-web-dist)가 생성되며, nginx를 사용하여 서버를 실행한다.

```console
$ npm run build -- --mode prod
```

![image](/assets/img/2022-02-06-Project SW Test Forum10/image1.png)

- 빌드하면 생성되는 ‘프로젝트명-dist’ 디렉터리 경로는 다음과 같이 설정하면 변경할 수 있다.

```
module-app-web\front\vue.config.js
```

```javascript
// ...

module.exports = {
   outputDir: path.resolve(__dirname, "./module-app-web-dist"),
    // ...
```

출처: <https://stackoverflow.com/questions/50772662/npm-run-build-mode-env-mode-not-working-as-expected><br>
<https://hello-bryan.tistory.com/268>



## 운영서버 배포를 위한 Spring boot 프로젝트 빌드
- 다음 명령어를 사용하여 빌드한다.

```
$ ./gradlew clean bootJar
```

![image](/assets/img/2022-02-06-Project SW Test Forum10/image2.png)

## 서버 설치 및 설정
- 서버 운영체제는 Ubuntu 20.04 이며, 서버 구축에 필요한 패키지 설치 과정은 ‘Project Lab 23, 26. 개발 및 운영 서버 배포 - 1’ 게시글을 참고하였다.



## DevTools failed to load SourceMap 경고 메시지 없애기
- 운영서버에 접속하면 다음 하단 이미지와 같은 DevTools failed to load SourceMap  경고 메시지가 출력되었다.
- 오류가 발생하여 출력되는 메시지는 아니지만, 은근히 신경쓰여서? 다음과 같이 코드를 수정하여 해결하였다.

![image](/assets/img/2022-02-06-Project SW Test Forum10/image3.png)

- 오류가 출력되는 파일로 이동하여, // # sourceMappingURL=ckeditor.js.map 코드를 다음과 같이 주석 처리하거나 삭제하면 된다.

![image](/assets/img/2022-02-06-Project SW Test Forum10/image4.png)

출처: <https://sens.tistory.com/566>
