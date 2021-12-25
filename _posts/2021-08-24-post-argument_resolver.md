---
layout: post
title: "[JAVA/SPRING] Argument resolver java config로 구현하기"
categories: [spring]
tags: [java, spring, config]
---

Argument resolver는 컨트롤러에서 특정값을 인자값으로 받을 때 사용된다. 

예를 들어 컨트롤러에 다음과 같은 메서드가 있을 때
```java
@RequestMapping("/temp")
public ResponseEntity<Object> temp(HttpServletRequest request){ }
```

별다른 설정이 없어도 request 에 데이터가 담겨있는걸 확인 할 수 있다.

이는 기본 HttpServletRequest가 기본 argument resolver에 포함되어있기 때문이다.

이렇게 기본적으로 제공하는 resolver 외에도 직접 만들어서 사용 할 수 있다. 

resolver 클래스를 만든 후 java config나 xml방식 둘 중 하나로 등록이 가능하다. 

boot환경은 대부분 java config로 하는듯 하다.

### 1. resolver 클래스 만들기

User 클래스가 있고, 해당 User를 컨트롤러에서 인자값으로 받는다고 가정해본다.

resolver는 HandlerMethodArgumentResolver를 구현해주면 된다. 

supportsParameter는 인자값의 타입을 검증한다.

resolveArgument는 인자값에 전달 할 데이터를 반환한다.

```java
public class UserArgumentResolver implements HandlerMethodArgumentResolver{
  
    public boolean supportsParameter(MethodParameter parameter) {
      return User.class.equals(parameter.getParameterType()); // 인자의 타입이 User인지 검증
    }
    
    public Object resolveArgument(MethodParameter parameter
                                , ModelAndViewContainer mavContainer
                                , NativeWebRequest webRequest
                                , WebDataBinderFactory binderFactory) throws Exception {
			
      User userArgument = new User();
      userArgument.setName("jinseob");
      userArgument.setId("js");
      ....
      return userArgument;		
	}
}
```

### 2. resolver 등록하기

config를 생성해서 해당 resolver를 등록해주면 된다.

WebMvcConfigurer에서 addArgumentResolvers를 구현해주면 된다.

Spring 5 이전 버전에서는 WebMvcConfigurerAdapter를 상속받아서 정의 할 수도 있다.
(WebMvcConfigurerAdapter는 Spring 5에서 Deprecated 되었다.)


```java

@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(new UserArgumentResolver());
    }
}
```

이렇게 하면 모든 설정이 끝이다.

### 3. 값 확인해보기 

이제 컨트롤러에서 다음과 같은 메서드가 있을 때

```java
@RequestMapping("/temp")
public ResponseEntity<Object> temp(User user){ }
```

`user.getName` 의 값은 `jinseob` 으로 나온다.

