---
title: "Project SW Test Forum Forum 4. Vue.js와 Spring boot에서 JWT 기반 로그인 구현 - 1"
excerpt: "Vue.js와 Spring boot에서 JWT 기반 로그인을 구현한 과정을 소개한다. "
categories:
  - Web
  - Project SW Test Forum Forum
last_modified_at: 2021-08-08
layout: post
---
- Vue.js와 Spring boot에서 JWT 기반 로그인을 구현한 과정을 소개한다.
- github: <https://github.com/scribnote5/sw_test_forum>



## JWT(JSON Web Token)란?
- JSON 포맷에 사용자 인증정보를 저장하는 토큰이다. JWT를 사용하여 토큰 기반 사용자 인증 시스템 즉 로그인을 구현할 수 있다.
- 인프런 Spring Boot JWT Tutorial 인강을 기반으로 개발하였다.

출처: <https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-jwt>


### JWT과 세션 인증 방법과 비교
- 로그인 중인 사용자의 수가 많아지면 시스템에 과부하가 발생한다. 로그인한 사용자가 많아지면 메모리를 많이 사용하게 되기 때문이다.
- 서버 확장에 유리하다. 분산 시스템을 설계하는 경우, 세션 정보를 다른 프로세스간 동기화
하는 과정이 어렵다.

출처: <https://backend-intro.vlpt.us/4/>



## JWT는 어디에 저장해야 하는가?
- 두 방식 모두 보안에 완벽한 것은 없다.
- 취약점을 보완한다고 하면 cookie에 저장하는 방식이 local storage에 저장하는 방식보다 더 안전하다고 생각한다.
- 많은 개발자들이 JWT 토큰을 cookie에 저장하는 방식을 사용하고 있다.
- 클라이언트 뿐만 아니라 서버에서 추가적으로 XSS 취약점을 보완해야 한다.


### HTML Web Storage(Local Storage)
- 브라우저에 데이터를 저장하는 방법이다.

- XSS 취약점이 존재한다.
  -> 프론트엔드 프레임워크에서 XSS 취약점을 예방하기 위한 방법을 제공한다. 그러나 완벽하지 않다.


### Cookie
- 브라우저에 쿠키로 저장하는 방법으로, HTTP 요청을 보낼 때 마다 자동으로 쿠키가 서버에 전송된다.

- XSS 취약점이 존재한다.
  -> httpOnly 옵션을 추가하여 서버에 쿠키를 저장하면, 클라이언트는 쿠키에 접근할 수 없다. 또한 secure 옵션을 추가하면 https를 통해서만 접근 가능하다. 해당 옵션들은 클라이언트가 아닌 서버에서 설정한다. 그러나 완벽하지 않다.
- CSRF 취약점이 존재한다.
  -> CSRF 위조를 검사하는 토큰을 사용한다.


### 나의 결론?
- Local Storage는 Cookie 보다 사용하기 편하지만, 결국 Cookie를 선택하게 되었다.
- Cookie의 경우 CSRF 토큰을 사용하면 CSRF 취약점을 보완 할 수 있다.
- Cookie에 httpOnly 옵션과 secure 옵션을 추가하여도 XSS 공격을 보완 할 수 없다. 그러나 Local Storage 보다 조금 더 보안에 유리하기 때문에 Cookie를 선택하였다. 자세한 내용은 하단 출처를 참고하였다.

>- Conclusion
>- Although cookies still have some vulnerabilities, it's preferable compared to localStorage whenever possible. Why?
>- Both localStorage and cookies are vulnerable to XSS attacks but it's harder for the attacker to do the attack when you're using httpOnly cookies.
>- Cookies are vulnerable to CSRF attacks but it can be mitigated using sameSite flag and anti-CSRF tokens.
>- You can still make it work even if you need to use the Authorization: Bearer header or if your JWT is larger than 4KB. This is also consistent with the recommendation from the OWASP community:


출처:
<https://dev.to/cotter/localstorage-vs-cookies-all-you-need-to-know-about-storing-jwt-tokens-securely-in-the-front-end-15id><br>
<https://velog.io/@0307kwon/JWT%EB%8A%94-%EC%96%B4%EB%94%94%EC%97%90-%EC%A0%80%EC%9E%A5%ED%95%B4%EC%95%BC%ED%95%A0%EA%B9%8C-localStorage-vs-cookie><br>
<https://mygumi.tistory.com/375>



## JWT 토큰 연장 방식
- Access Token의 만료 시간이 짧은 경우, 보안에는 유리하지만 자주 로그인을 해야하는 단점이 있다.


### Sliding Session
- 이러한 보안과 편의성을 모두 해결할 수 있는 방법으로 Sliding Session 전략이 있다. 해당 방식은 특정 페이지에 접속하는 경우, Access Token을 새로 발급하여 로그인 기간을 연장하는 방식이다.


### Refresh Token
- 다른 방식으로는 Refresh Token 전략이 있다. 해당 방식은 처음 로그인할 때 Access Token과 Refresh Token을 발급한다. 만약 Access Token이 만료되는 경우 Refresh Token을 사용하여 Access Token을 새로 발급한다. Refresh Token은 Access Token 보다 더 긴 만료 기간을 부여한다.


### 나의 결론?
- 두 방식을 적용하지 않았다.
- Access Token의 만료 기간을 1일로 정하였으며, 이는 로그인이 유지되는 충분한 기간으로 판단하여 Sliding Session 전략을 사용하지 않았다.
- Refresh Token 전략의 필요성에 대하여 의문이 존재한다. Refresh Token은 Access Token을 발급 받기 위해 사용되므로, Refresh Token 역시 보안이 굉장히 중요하다. Refresh Token을 Cookie에 저장하든 Local Storage에 저장하든 보안 취약점이 발생하는데, 이를 공격하여 Access Token을 얻을 수 있기 때문이다.
- OAuth2 제공자 중 Refresh Token을 사용하지 않는 제공자(github, foursquare)도 존재한다.

출처: <https://zzossig.io/posts/etc/what_is_the_point_of_refresh_token/><br>
<https://velog.io/@insutance/JWT-token-%EB%A7%8C%EB%A3%8C><br>
<https://blog.ull.im/engineering/2019/02/07/jwt-strategy.html>



## Spring boot에서 JWT 구현
- Spring boot에서 JWT를 발급하는 기본 로직은 하단 출처를 참고하여 개발하였다.

출처: <https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8-jwt>



## Spring boot에서 JWT 구현 - 수정
- Access Token을 Cookie에 저장하도록 수정하였으며, 이로 인해 발생하는 문제점을 보완 하기 위해서 다음 항목 별로 코드를 수정 및 보완 하였다.


### Cookie httpOnly
- httpOnly 옵션을 추가하여 서버에서 쿠키를 저장하면, 클라이언트는 쿠키에 접근할 수 없다.
- 처음 인증할 때 Access Token를 쿠키에 저장하여 응답한다면, HTTP 통신을 할 때 자동으로  Set-Cookie 헤더에 Access Token이 저장된다.
- 쿠키 보안 문제를 해결하기 위해 만들어진 Samsite 쿠키는 None, Lax, Strict 정책이 있다. Samsite 쿠키는 서로 다른 도메인 간 쿠키를 전송하는 옵션으로, 같은 도메인을 사용하는 웹 페이지는 고려 대상이 아니다. 따라서 가장 높은 보안 단계인 Strict 정책을 적용하였다.
- Spring boot에서 Cookie에 httpOnly 옵션 등 기타 설정 방법은 하단 출처를 참고하였다.

출처: <https://dncjf64.tistory.com/292><br>
<https://seob.dev/posts/%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80-%EC%BF%A0%ED%82%A4%EC%99%80-SameSite-%EC%86%8D%EC%84%B1/><br>
<https://www.hahwul.com/2020/01/18/samesite-lax/>

```
module-app-api\src\main\java\com\suresoft\sw_test_forum\controller\common\AuthorityController.java
```

```java
    @PostMapping("/authenticate")
    public ResponseEntity authenticate(@Valid @RequestBody LoginDto loginDto, HttpServletResponse response) {
        // authenticationToken 생성, username + moduleName
        //...

        // authenticate 메소드가 실행될 때 loadUserByUsername 메소드 호출
        // ...

        // JWT token 생성, token subject는 userIdx와 username으로 구성
        User user = userService.findUserIdxByUsername(loginDto.getUsername());
        String jwt = tokenProvider.createToken(user.getIdx(), authentication);

        ResponseCookie resCookie = ResponseCookie.from("accessToken", jwt)
                .httpOnly(true)
//                .domain("") //  해당 도메인에서만 유효한 쿠키
                .sameSite("Strict") // None, Strict, Lax
//                .secure(true) // HTTPS가 적용된 요청에만 전송되는 쿠키
                .path("/")
                .maxAge(Math.toIntExact(1 * 24 * 60 * 60))
                .build();
        response.addHeader("Set-Cookie", resCookie.toString());

        return new ResponseEntity(user, HttpStatus.OK);
    }
```

- 인증된 JWT는 쿠키에 저장되어 클라이언트에 전송된다. 이후 클라이언트에서는 서버로 JWT를 전달해야 하는데,  axios에서는 하단 설정을 추가하면 JWT를 쿠키에 저장하여 전송 한다. 서버는 클라이언트 쿠키에 저장된 JWT를 사용하여 인증을 수행한다.

```
module-app-web\front\src\main.js
```

```javascript
// axios 설정
// ...
axios.defaults.withCredentials = true; // 다른 origin에 JWT를 전달하기 위한 설정
```



### CSRF(Cross Site Request Forgery)
- 웹 사이트의 취약점을 이용하여 이용자가 의도하지 하지 않은 요청을 통한 공격이다.

CSRF 시나리오 출처: <https://codevang.tistory.com/282>
<br>

- 가장 간단한 해결책으로는 CSRF 토큰을 헤더 정보에 포함하여 서버에 요청하는 것이다.
- 클라이언트에서 axios를 사용하는 경우 CSRF 토큰을 전송하는 방법은 다음과 같다.

```
module-app-web\front\src\main.js
```

```javascript
// axios 설정
axios.defaults.xsrfCookieName = 'XSRF-TOKEN' // csrf 기본 설정을 명시적으로 선언
axios.defaults.xsrfHeaderName = 'X-XSRF-TOKEN' // csrf 기본 설정을 명시적으로 선언
```

출처: <https://zetawiki.com/wiki/Vue.js_%2B_axios_%2B_django_CSRF_%ED%86%A0%ED%81%B0_%EC%84%A4%EC%A0%95_%EB%A7%9E%EC%B6%94%EA%B8%B0>
<br>

- 서버에서는 클라이언트에서 송신한 CSRF Token이 유효한지 검사해야 한다.
- Spring boot에서 CSRF 설정을 방법은 하단 출처를 참고하였다.

```
module-app-api\src\main\java\com\suresoft\sw_test_forum\config\SecurityConfig.java
```

```java
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    // ...

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                // csrf 설정
                .csrf()
                .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
                .ignoringAntMatchers("/api/auths/authenticate")
        // ...
```

출처: <https://cheese10yun.github.io/spring-csrf/>


### CORS preflight 요청은 권한 확인 제외
- 클라이언트(요청하는 쪽)이 서버(요청 받는 쪽)과 본격적인 통신을 수행하기 전에 OPTIONS 메소드로 preflight를 전송한다. 실제 요청과 응답을 주고 받기 전 클라이언트에 CORS 권한이 있는지 '사전검사'를 한 후에 클라이언트에서 실제 요청을 보낸다.
- 하지만 preflight를 보내는 경우에도 JWT가 있는지 검사하여 에러가 발생한다. 따라서 preflight(request method가 OPTIONS)를 전송할 때를 JWT 유효성 검사에서 제외하였다.

```
module-app-api\java\com\suresoft\sw_test_forum\jwt\JwtFilter.java
```

```java
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
        String requestURI = httpServletRequest.getRequestURI();
        Cookie cookie = WebUtils.getCookie(httpServletRequest, "accessToken");
        String jwt = "";

        if (!EmptyUtil.isEmpty(cookie)) {
            jwt = cookie.getValue();
        }

        log.info("jwt: " + jwt);
        log.info("requestURI: " + requestURI);

        if (StringUtils.hasText(jwt) && tokenProvider.validateToken(jwt)) {
            Authentication authentication = tokenProvider.getAuthentication(jwt);
            SecurityContextHolder.getContext().setAuthentication(authentication);
            log.info("Security Context에 '{}' 인증 정보를 저장했습니다, uri: {}", authentication.getName(), requestURI);
        } else if ("OPTIONS".equals(httpServletRequest.getMethod())) {
            log.info("preflight: OPTIONS 요청");
        } else if ("/api/auths/authenticate".equals(requestURI)) {
            log.info("/api/auths/authenticate 요청");
        } else {
            log.error("유효한 JWT 토큰이 없습니다, uri: {}", requestURI);
        }

        filterChain.doFilter(servletRequest, servletResponse);
    }
```

출처: <https://velog.io/@ojwman/spring-boot-cors-header-preflight>



## Spring boot에서 XSS(Cross-site Scripting) 취약점 방어
- Spring boot에서 XSS 취약점을 방어 수단으로 lucy-xss-servlet-filter를 사용할 수 있다. 그러나 Form Data에 대해서 적용되기에, 프론트엔드 서버와 백엔드 서버 간의 통신에서 사용하는 Request Payload에 대해서는 적용되지 않는다.
- 따라서 MessageConverter를 사용하여 JSON 문자열을 생성할 때 XSS 취약점을 방어하도록 처리하였다.

출처: <https://exhibitlove.tistory.com/3>
