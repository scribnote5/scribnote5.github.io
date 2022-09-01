---
title: "Github Page jekyll-theme-yat 테마 설정"
excerpt: "Github Page jekyll minimal-mistakes 테마 사용 중 jekyll-theme-yat 테마로 변경한 과정을 소개한다."
categories:
  - Github Page
last_modified_at: 2021-07-17
layout: post
---
- Github Page jekyll minimal-mistakes 테마에서 jekyll-theme-yat 테마로 변경한 과정을 소개한다.

- 본 블로그는 github page의 jekyll 템플릿을 사용하여 개발되었으며, 블로그 생성 및 설정은 <https://devinlife.com/howto> 페이지를 참고하였다.



## 템플릿 비교
- 템플릿의 디자인을 평가하는 것은 매우 주관적이기에 이를 평가하기는 어렵다.
- 필자는 jekyll-theme-yat theme이 기존 minimal-mistakes theme 보다 더 깔끔하고 게시글 가독성이 더 좋다고 생각하여 변경하게 되었다.
- 각 테마는 하단 이미지와 출처를 통해서 확인할 수 있다.


### minimal-mistakes 테마

![image](/assets/img/2021-07-17-GithubPage2/image1.png)

출처: <https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/>


### jekyll-theme-yat 테마

![image](/assets/img/2021-07-17-GithubPage2/image2.png)

출처: <https://jeffreytse.github.io/jekyll-theme-yat/>



## disqus 오류
- jekyll-theme-yat에 disqus를 적용하는 도중 config.yml 파일에 ‘disqus.shortname’에 유효한 설정 값이 존재하지만 다음과 같은 disqus 오류 메시지와 브라우저 log 오류 메시지 발생하였다.

![image](/assets/img/2021-07-17-GithubPage2/image3.png)

![image](/assets/img/2021-07-17-GithubPage2/image4.png)

- 여러 방법을 검색하는 찾아보는 도중 jekyll-theme-yat에서 제공하는 disqus.html 코드가 잘못되어 발생하는 문제를 발견하였고, 구글링을 통해서 새로운 disqus.html 코드로 변경한 결과 정상적으로 disqus가 작동하는 것을 확인하였다.

출처: <https://stackoverflow.com/questions/54632451/disqus-showing-same-comments-on-all-pages-with-unique-page-url-and-page-id-set>



## Github page 빌드 에러
- Github page로 push 하는 경우 다음과 같이 빌드 에러가 발생하여 메일로 전송될 것이다.

![image](/assets/img/2021-07-17-GithubPage2/image5.png)

- 빌드 에러는 ‘Please set the Token environment variable.’로서 Github에서 인증을 위해 사용하는 Personal access tokens이 jekyll 프로젝트에 등록되지 않아서 발생하였다.

![image](/assets/img/2021-07-17-GithubPage2/image6.png)

- 해당 에러를 해결하기 위해서는 Github에서 ‘Personal access tokens’을 발급 받아 jekyll 프로젝트에 등록하면 된다.
- 하단 출처를 참고하여, ‘Personal access tokens’를 발급 받은 다음 build-jekyll.yml 파일의 line 26에 발급 받은 토큰을 입력하면 된다.

```
<build-jekyll.yml>

      # Use GitHub Deploy Action to build and deploy to Github
      - uses: jeffreytse/jekyll-deploy-action@master
        with:
          provider: 'github'
          token: '토큰을 입력하시면 됩니다.' # It's your Personal Access Token(PAT)
```

출처: <https://calvinjmkim.tistory.com/19>
