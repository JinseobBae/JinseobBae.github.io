---
layout: post
title: "[JAVAJ/MYBATIS] XML fragments parsed from previous mappers already contains value for 에러"
categories: [error, mybatis]
tags: [mybatis, error]
---


## 오류
```java
java.lang.IllegalArgumentException: XML fragments parsed from previous mappers already contains value for com.my.dao.select
```

## 원인

동일한 namespace에 동일한 mapper id가 존재하기 떄문에 발생하는 에러다.

에러 문구처럼 이미 등록된 mapper id를 또 등록하려해서 생긴 오류다. 


```java
java.lang.IllegalArgumentException: Mapped Statements collection already contains value for
```

이 오류랑 원인이 비슷한데 동일한 오류인지는 좀 더 알아봐야한다.


## 해결


중복되는 namespace, mapper id를 제거해주면 된다. 심플하다.

무분별한 복사/붙여넣기를 안하면 발생하지 않는다. 반성해본다.


참고

- [https://www.python2.net/](https://www.python2.net/questions-1564187.htm)
