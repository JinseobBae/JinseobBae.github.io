---
layout: post
title: "[SPRING/JPA] failed to lazily initialize a collection of role 오류"
categories: [jpa]
tags: [error, jpa]
---

## 오류

`fetch = FetchType.LAZY`로 설정 된 entity의 데이터를 사용할 때 데이터조회를 못하면서 아래 오류가 발생했다.

```
 failed to lazily initialize a collection of role: com.my.jpa.entity.xxx.yyy, could not initialize proxy - no Session
```


## 원인

Lazy 로딩을 하기 위해서는 조회를 했던 영속성 컨텍스트가 유지가 되어야 하는데, 해당 영속성 컨텍스트가 끝나버려서 없어진게 원인이다.

즉, 조회를 진행한 트랜잭션이 이미 종료되었다고 보면 될 듯 하다.

해당 트랜잭션이 유지가 되어야 Lazy 로딩이 가능하다.

트랜잭션 밖에서 Lazy 로딩을 하게되면 이미 해당 트랜잭션이 종료되었기 때문에 오류가 발생한다. 


## 해결

나같은 경우는 `@Transacional`을 선언하지 않아서 생긴 문제였다.

Lazy 로딩을 하려는 메서드가 하나의 Transacional 내에서 실행되도록 선언하면 된다.  

굳이 Lazy 로딩을 하지 않아도 된다면 `fetch = FetchType.EAGER`로 변경해도 된다.




참고

 - [stackoverflow](https://stackoverflow.com/questions/22821695/how-to-fix-hibernate-lazyinitializationexception-failed-to-lazily-initialize-a)
 - [인프런 failed to lazily initialize a collection of role 오류 관련 문의](https://www.inflearn.com/questions/33949)

  
  
  
