---
title: "Project SW Test Forum 10. 운영 서버 배포 및 후기 - 1"
excerpt: "- Vue.js 3와 Spring boot로 개발한 프로젝트 배포 과정과 후기를 소개한다."

categories:
  - Web
  - Project SW Test Forum
last_modified_at: 2022-02-08
layout: post
---
- Vue.js 3와 Spring boot로 개발한 프로젝트 배포 과정을 소개한다.
- github: <https://github.com/scribnote5/sw_test_forum>



## 운영서버 배포를 위한 Vue.js 프로젝트 빌드
- 운영서버 환경은 prod 실행모드에서 실행되도록 설정(환경변수가 prod 모드에 맞춰짐) 하였으며, 다음 명령어를 사용하여 프로젝트를 prod 실행모드로 빌드한다.
- ‘프로젝트명-dist’라는 디렉터리(module-app-web-dist)가 생성되며, nginx를 사용하여 서버를 실행한다.

```console
$ npm run build -- --mode prod
```

![image](/assets/img/2022-02-08-Project SW Test Forum10/image1.png)

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

![image](/assets/img/2022-02-08-Project SW Test Forum10/image2.png)

## 서버 설치 및 설정
- 서버 운영체제는 Ubuntu 20.04 이며, 서버 구축에 필요한 패키지 설치 과정은 ‘Project Lab 23, 26. 개발 및 운영 서버 배포 - 1’ 게시글을 참고하였다.
- nginx에서 Vue.js 프로젝트를 실행하는 설정은 다음과 같다. loaction의 root에 Vue.js 프로젝트 빌드한 디렉터리 경로를 넣어주면 된다. 이후 nginx 설정을 다시 불러오는 nginx -s reload 명령어를 수행하면 된다.

```
/etc/nginx/nginx.conf
```

```
# ...

        server {
                listen 8080;
                server_name ...;

                location / {
                        root <path>/module-app-web-dist;
                        index index.html;
                        try_files $uri $uri/ /index.html;
                        proxy_cookie_path / "/; secure; SameSite=None";

                }

                # 봇 정보 수집 방지
                location  /robots.txt {
                        return 200 "User-agent: *\nDisallow: /";
                }
        }
}
```



## DevTools failed to load SourceMap 경고 메시지 없애기
- 운영서버에 접속하면 다음 하단 이미지와 같은 DevTools failed to load SourceMap  경고 메시지가 출력되었다.
- 오류가 발생하여 출력되는 메시지는 아니지만, 은근히 신경쓰여서? 다음과 같이 코드를 수정하여 해결하였다.

![image](/assets/img/2022-02-08-Project SW Test Forum10/image3.png)

- 오류가 출력되는 파일로 이동하여, // # sourceMappingURL=ckeditor.js.map 코드를 다음과 같이 주석 처리하거나 삭제하면 된다.

![image](/assets/img/2022-02-08-Project SW Test Forum10/image4.png)

출처: <https://sens.tistory.com/566>



## Project SW Test Forum. 개발하면서…
- 약 7개월 간 개발을 끝으로, 프로젝트를 마무리 지었다. 회사 업무 시스템에 한계를 느껴 개발한 두 번째 개인 프로젝트로서, Project Lab의 한계를 느껴 SPA(Single Page Application)인 Vue.js를 도입하였다. 이미 Project Lab을 통해서 개발된 코드를 재활용하면, Project SW Test Forum을 금방 개발할 수 있었지만, 새로운 기술에 대한 궁금증과 도전의식에 더 많은 시간을 투자하게 되었다. Vue.js를 새로 접한다고 해도 Project Lab. 코드를 재활용하면 길어야 4개월이면 개발 완료할 것이라고 생각했다. 그러나 개발 가능한 시간의 부족함(회사 일 병행, 그냥 개발 귀찮음, 예기치 못한 다양한 오류 등)을 접하면서 개발 완료까지 많은 시간이 걸렸다.
- 내 초기 의도와 다르게 회사 업무 시스템을 변경하는 것은 생각보다 어려웠다. 좋은 취지에서 개발하였지만 이를 활성화 하고 많은 사람들의 참여를 유도하는 것은 별개였다. 팀원분들과 직속 임원분에게 개발 의견을 전달하였고 많은 사람들이 프로젝트를 만든 취지에 대해서 모두 공감하였다. 그러나 내가 시간을 투자하여 유의미한 데이터를 넣지 않는한 묻혀버릴 프로젝트라고 생각한다. 아직 유의미한 데이터가 없는 웹사이트에 다른 사람들이 방문하지 않을 것이기 때문이다.
- 해당 프로젝트를 활성화 시키기 위해서는 초기 데이터를 넣는 작업이 중요하다. 프로젝트에 데이터는 시간이 될 때 마다 차츰 작성할 예정이다. 이는 업무 시간 뿐만 아니라 개인적인 시간이 많이 투자 될 수 있기에 조심스러운 부분이다. 넣어야 할 데이터는 산더미이다.
- 같이 일하고 있는 한화시스템에서 비슷한 웹사이트를 만들어서 사용 중이며, 이를 적극적으로 활용할 계획이다. 최근 해당 웹사이트 수정 요청이 들어왔는데, 오래전에 만들어진 웹사이트다 보니 완성도는 떨어진다.(무려 bootstrap 3를 사용하고 있고, 20년전 디자인 및 UI) 또한, Node.js 기반으로 개발된 프로젝트다 보니 코드를 분석하는데 쉽지 않았다. 담당자분에게 내가 만든 웹페이지를 권고하고 싶었지만, 차마 일이 커질 것 같아서 애기하지 못하였다.
- 프로젝트 완료하여 기분이 좋아야 하지만, 아직 사용자가 없어서 아쉬울 따름이다.
- 다음에는 무엇을 만들어 볼까?



## 전체적인 프로젝트 웹페이지
- 프로젝트의 주요 웹페이지 스크린샷은 다음과 같다.


### 로그인 페이지

![image](/assets/img/2022-02-08-Project SW Test Forum10/image5.png)


### 메인 페이지(대시 보드)
- 전체적인 통계 및 게시글 리스트를 확인 할 수 있다.

![image](/assets/img/2022-02-08-Project SW Test Forum10/image6.png)


### 정적시험 규칙 읽기 페이지
- 정적시험 규칙을 선택하면, 읽기 페이지로 이동한다. 해당 페이지에서 규칙에 대한 설명, 예제 코드, 가이드라인을 확인 할 수 있다.

![image](/assets/img/2022-02-08-Project SW Test Forum10/image7.png)


### 도구 트러블 슈팅 작성 페이지
- 도구 트러블 슈팅을 해결한 방법을 작성하는 페이지다. 어떠한 형식으로 작성해야 하는지에 대한 템플릿을 제공한다. 또한 에디터에서 # 특수문자로 문자를 입력하면, 오류 로그가 발생한 도구 경로를 자동으로 제안한다.(CKEditor mention 기능)

![image](/assets/img/2022-02-08-Project SW Test Forum10/image8.png)


### 지식 저장소 작성 페이지
- 테스트에 대한 다양한 지식을 작성하는 페이지다. 실사, 교육자료, 공부하면서 정리한 내용 등을 작성할 계획이다.

![image](/assets/img/2022-02-08-Project SW Test Forum10/image9.png)


### 로그인 기록
- 사용자들의 로그인 기록을 확인 할 수 있다.

![image](/assets/img/2022-02-08-Project SW Test Forum10/image10.png)


### 설정 페이지
- 메인 페이지에서 출력하는 정적시험 규칙 및 개발자에게 메일 보내기를 설정하는 페이지다.

![image](/assets/img/2022-02-08-Project SW Test Forum10/image11.png)
