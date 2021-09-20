---
layout: post
title: "[JAVA] Weblogic 환경에서 Rest Template 작동 안할 때(EOF Exception)"
categories: [java, error]
tags: [java, weblogic, error]
---

톰캣에서 문제 없이 사용하던 Rest API기능이 Weblogic 환경에서는 중간에 멈춰버리는 현상이 발생했다.

WAS는 Weblogic 12c를 사용했고 Rest 기능은 Spring RestTemplate를 이용해서 구현되어있다.

### 현상

 RestTemplate를 이용해 API를 호출하면 EOF Exception 발생
 
 ```
I/O error on GET request for [URL] : Response had end of stream after 0 bytes; nested exception is java.io.EOFException;
 ```
 
### 해결

RestTemplate 객체를 생성 할 때 인자값으로 `new HttpComponentsClientHttpRequestFactory()`를 넣어준다.

```java
private RestTemplate restTemplate = new RestTemplate(new HttpComponentsClientHttpRequestFactory())
```  
 
 
docs에 따르면 `HttpComponentsClientHttpRequestFactory` 사용 시 HttpComponents 4.3 이상이 필요하다고 한다.


참고

 - [HttpComponentsClientHttpRequestFactory docs](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/HttpComponentsClientHttpRequestFactory.html)
   
