---
layout: post
title: "[JAVA] Feign client 호출 결과 타입 커스텀하기"
categories: [java, feign]
tags: [java, feign, spring]
---

Feign Client를 통해 API를 호출하는데 API에서는 결과를 Wrap 해서 보내주고 있었다.

```java

public class ApiResult<T> {

 private int status;
 private String message;
 private T result;
 private boolean error;

}

```

위와 같이 보냈기때문에 계속 ApiResult로 결과값을 받고 getResult()를 통해 호출 결과값을 받아야했다.

status, error도 중요하지만 필요한건 `result` 였고 해당 값만 결과값으로 받고 싶었다.

그래서 결과값을 커스터마이징 하기로 했다. Config 파일을 직접 만들고 decoder를 직접 정의해서 커스텀을 진행했다.

결과값 decode는 Gson을 사용했다. (feign-gson-12.1)

```java
// 호출 return 클래스
public class MyApiResult {

 private int status;
 private String message;
 private Object result;
 private boolean error;

}


```

설정파일은 아래와 같이 했다.


```java

public class MyFeignConfiguration {

    @Bean
    public Decoder decoder() {
      return (response, type) -> { // response는 호출결과값, type은 호출 메서드에 선언한 return type
        
        MyApiResult apiResult = (MyApiResult) new GsonDecoder().decode(response, MyApiResult.class);
        
        if (type instanceof MyApiResult) { // ApiResult<T> 형태로 받을 땐 그대로 return
          return apiResult;        
        }
        
        ObjectMapper mapper = new ObjectMapper();
        
        Type realType;
        Object result;
        
        if (type instanceof ParameterizedType) { // 타입 인수가 있을 때 
            
            realType = Optional.ofNullable((((ParameterizedType) type).getActualTypeArguments())))
                      .map(types -> types[0]).orElse(type);
                                            
             Type rawType = ((ParameterizedType) type).getRawType();             
             
             if (rawType.equals(List.class)) { // List로 받을 때
                    result = mapper.convertValue(apiResult.getResult(), mapper.getTypeFactory().constructCollectionType(List.class, TypeFactory.rawClass(realType)));
                } else if(rawType.equals(Optional.class)){ // Optional로 받을 때
                    result = Optional.ofNullable(mapper.convertValue(apiResult.getResult(), TypeFactory.rawClass(realType)));
                } else if ( ... ) {
                   // ... 원하는 타입 있을 시 명시           
                } else {
                    result = mapper.convertValue(apiResult.getResult(), TypeFactory.rawClass(realType));
                }        
        } else { // 단순 객체일 때
        
            if (apiResult.getResult() != null) {
                result = mapper.convertValue(apiResult.getResult(), TypeFactory.rawClass(realType));
            } else {
                   // ... null인 경우 처리                                                
            }        
        }
        
       return result;
        
      }
    }
}


```

아래와 같이 호출해도 모두 정상적으로 결과값을 받는다.

``` java

@FeignClient(name = "myFeign", url = "https://xxxx/myApi", configuration = {MyFeignConfiguration.class})
public interface MyFeignClient {

  @GetMapping("/userList")
  MyApiResult findAllUsers();
  
  @GetMapping("/userList")
  List<User> findAllUsers();
  
  @GetMapping("/user")
  MyApiResult findUser(String id);
  
  @GetMapping("/user")
  Optional<User> findUser(String id);
  
  @GetMapping("/user")
  User findUser(String id);

}

```



참고

- https://sabarada.tistory.com/116
