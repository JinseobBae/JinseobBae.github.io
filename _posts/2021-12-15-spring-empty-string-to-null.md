---
layout: post
title: "[SPRING] Controller에서 빈 문자열을 NULL로 받기"
categories: [spring]
tags: [spring, custom]
---

컨트롤러에서 파라미터를 받을 때, 전달 값이 없는 String이 null이 아니라 "" 로 올 때가 있다.

값이 없는 경우 Null로 받기를 원해서 찾아보니 여러가지 방법이 있었다.

그 방법들을 정리해본다.

하나의 컨트롤러보단 글로벌하게 설정하는 방향으로 접근했다.


## @InitBinder

컨트롤러에 사용하는 어노테이션으로 데이터가 바인딩 될 때 호출이 된다.

보통 사용을 원하는 컨트롤러에 개별적으로 설정하는데, 공통적으로 하고 싶다면 `@ControllerAdvice` 를 이용해서 설정하면 된다고 한다.

```java

@ControllerAdvice
public class GlobalBinding{

  @InitBinder
  public void customBinding(WebDataBinder binder){
    binder.registerCustomEditor(String.class, new StringTrimmerEditor(true)); // empty string to null
  }

}

```

## WebBindingInitializer 

`WebBindingInitializer`을 직접 구현하는 방법과 상속을 이용한 방법이 있었다.

### WebBindingInitializer 직접 구현

#### WebBindingInitializer 구현

먼저 WebBindingInitializer를 구현해준다. `initBinder`를 구현하면 되는데

Spring 5.x로 오면서 인자값이 변경되었다. (WebRequest request 제거)

```java

public class GlobalBindingInitializer implements WebBindingInitializer{

  //Spring 5.x 미만 버전
  public void initBinder(WebDataBinder binder, WebRequest request){
    binder.registerCustomEditor(String.class, new StringTrimmerEditor(true)); // empty string to null
  }
  
  
  //Spring 5.x 버전
  public void initBinder(WebDataBinder binder){
    binder.registerCustomEditor(String.class, new StringTrimmerEditor(true)); // empty string to null  
  }

}

```

#### WebBindingInitializer 등록

구현을 한 뒤 HandlerAdapter에 넣어주면 되는데

`AnnotationMethodHandlerAdapter` 혹은 `RequestMappingHandlerAdapter`를 이용한다.

`AnnotationMethodHandlerAdapter`는 Spring3.2에서 Deprecated 되었는데 Spring4.3 까지는 사용이 가능은하다.

이 후 버전은 `RequestMappingHandlerAdapter`을 사용하면 된다고 한다.

```java
// AnnotationMethodHandlerAdapter 사용
@Bean
public AnnotationMethodHandlerAdapter annotationMethodHandlerAdapter(){
  AnnotationMethodHandlerAdapter adapter = new AnnotationMethodHandlerAdapter();
  adapter.setWebBindingInitializer(new GlobalBindingInitializer());
  return adapter;
}        

// RequestMappingHandlerAdapter 사용
@Bean
public RequestMappingHandlerAdapter requestMappingHandlerAdapter(){
  RequestMappingHandlerAdapter adapter = new RequestMappingHandlerAdapter();
  adapter.setWebBindingInitializer(new GlobalBindingInitializer());
  return adapter;
}        
```

### WebBindingInitializer 상속 

`ConfigurableWebBindingInitializer` 상속하는 방법에 대한 글도 찾았다. 글에 코드가 잘 정리되어 있으니 참고하면 좋을듯 하다.

(https://developer-ljo.tistory.com/19)


## WebMvcConfigurationSupport

Config에서 `WebMvcConfigurationSupport`을 이용해 `ConfigurableWebBindingInitializer`를 재정의해서 사용하는 방법이다.

`getConfigurableWebBindingInitializer`로 기존의 ConfigurableWebBindingInitializer를 가져온다음 커스텀에디터를 추가한다.

글을 읽었을 떄 `getConfigurableWebBindingInitializer`가 Spring5.0.2 까지는 인자값이 없었는데

현재 사용하고있는 Spring5.3.9 버전은 2개의 인자값이 추가되어있었다...  언제부터 생긴지는 못찾겠다.. ㅠ


```java
@Configuration
public class WebConfig extends WebMvcConfigurationSupport{

    //Spring 5.3.9
    @Override
    protected ConfigurableWebBindingInitializer getConfigurableWebBindingInitializer(FormattingConversionService mvcConversionService, Validator mvcValidator) {
        ConfigurableWebBindingInitializer initializer = super.getConfigurableWebBindingInitializer(mvcConversionService, mvcValidator);
        initializer.setPropertyEditorRegistrar( registry -> registry.registerCustomEditor(String.class, new StringTrimmerEditor(true))); // empty string to null
        return initializer;
    }
    
    //Spring 5.0.2 이하..?
    @Override
    protected ConfigurableWebBindingInitializer getConfigurableWebBindingInitializer() {
        ConfigurableWebBindingInitializer initializer = super.getConfigurableWebBindingInitializer();
        initializer.setPropertyEditorRegistrar( registry -> registry.registerCustomEditor(String.class, new StringTrimmerEditor(true))); // empty string to null
        return initializer;
    }

}

```

이외에도 다양한 방법이 있는듯하다.

`RequestMappingHandlerAdapter`을 사용하는 경우 new 로 생성하기보단 기존걸 가져와서 추가하는 방법이 더 안전할 것 같기는 하다.

Spring 5.x에서는 `RequestMappingHandlerAdapter` 자체적으로 bean을 생성 할 떄 인자값이 3개나 있고 set 해주는 항목들도 많아서...

직접 new로 생성하면 이상하게 동작 할 수 있을 것 같기도 하다.




## 참고

- https://www.logicbig.com/tutorials/spring-framework/spring-web-mvc/custom-web-binding-initializer.html
- https://stackoverflow.com/questions/1268021/how-can-i-register-a-global-custom-editor-in-spring-mvc
- https://developer-ljo.tistory.com/19









