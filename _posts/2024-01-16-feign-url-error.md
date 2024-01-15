---
layout: post
title: "[JAVA] feign url values must be not be absolute 오류"
categories: [fegin]
tags: [feign, error]
---


## 오류

```java

@FeignClient(url="http://localhost:8080")
public interface MyFeignClient{

  @GetMapping("{url}")
  Object getCall(@PathVariable String url, @RequemstParam("param") String param);

}

@Service
public MyService {

  @Autowired
  MyFeignClient myFeignClient;

  public Object getCall() {

    return myFeignClient.getCall("http://localhost:8080/getInfo", null);

  }

}

```

위와 같이 만든 후 이용해 호출 시 해당 에러가 발생한다.

```java

java.lang.IllegalArgumentException: url values must be not be absolute
at feign.RequestTemplate.uri(RequestTemplate.java:434)
at feign.RequestTemplate.uri(RequestTemplate.java:421)
at feign.RequestTemplate.resolve(RequestTemplate.java:221)
at feign.ReflectiveFeign$BuildTemplateByResolvingArgs.resolve(ReflectiveFeign.java:334)
at feign.ReflectiveFeign$BuildTemplateByResolvingArgs.create(ReflectiveFeign.java:232)
...

```

## 원인

오류난 지점을 찾아보면 아래와 같다.

![image](https://github.com/JinseobBae/JinseobBae.github.io/assets/29051992/efd43ab5-6e9c-418a-895a-4c36abe4c3fa)

(feign-core.jar의 RequestTemplate.java)

주석을 보면 알 수 있듯 mapping 되는 url은 상대경로가 들어가야하는데 url 변수에 절대경로를 넣어서 생긴 오류다.

Feign client에서 자체적으로 검증하는데 http로 시작하면 안된다.

![image](https://github.com/JinseobBae/JinseobBae.github.io/assets/29051992/c5f808c7-254c-47db-8a28-143b335271ba)

(feign-core.jar의 UriUtils.java)


## 해결

도메인은 @FeignClient 의 url에 넣어주고 mapping되는 value는 상대경로로 넣는다.

```java

@FeignClient(url="http://localhost:8080")
public interface MyFeignClient{

  @GetMapping("{url}")
  Object getCall(@PathVariable String url, @RequemstParam("param") String param);

}

@Service
public MyService {

  @Autowired
  MyFeignClient myFeignClient;

  public Object getCall() {

    return myFeignClient.getCall("/getInfo", null);

  }

}

```

굳이 동적으로 안하고 1:1 매핑으로 쓴다.


