---
layout: post
title: "[JAVA] UsernamePasswordAuthenticationFilter 이용한 커스텀 필터 설정"
categories: [study, spring]
tags: [java , spring, custom filter]
---

`Spring-security` 를 이용해 로그인을 구현할 때 

`UsernamePasswordAuthenticationFilter`를 상속한다음 커스터마이징해서 사용해야하는 경우가 있다.

프로세스 실행시 내가 만든 클래스로 가려면 config 파일에 filter설정이 필요하다.

내가 만든 클래스를 `CustomAuthFilter`라고 하면

config 파일에

```java
@Bean

public CustomAuthFilter customAuthFilter(){

 CustomAuthFilter customFilter = new CustomAuthFilter()

 customFilter.setRequiresAuthenticationRequestMatcher(new AntPathRequestMatcher("/login","POST")) 

 customFilter.set~~~~~ 

 customFilter.set~~~~~ 

return customFilter

}
```

이런식으로 빈 하나를 만들어서 설정들을 셋팅한 다음 filter 관련 설정하는 부분에 (webConfig 등등) 
```java
http.addFilterBefore(customAuthFilter(), UsernamePasswordAuthenticationFilter.class) 
```
를 입력해주면 된다.

만약 따로 filter를 정의한 곳이 없으면 config 클래스의 configure 메소드 안에 넣어주면 된다. 

**로그인 프로세스 디폴트 URL이 `/j_spring_security_check` 인걸로 알고 있었는데 spring-security 3.2.7 부터는 `/login`으로 바뀌었다고 한다.**



