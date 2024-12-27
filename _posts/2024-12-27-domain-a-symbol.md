---
layout: post
title: "URL 도메인의 @ 기호"
categories: [url, java]
tags: [url, java]
---

모의해킹 진행 중 처음보는 피싱 방법을 알게되었다. (실제로 언제부터 사용된지는 모르겠다..)

바로 callbackURL에 @를 넣어서 피싱 사이트로 연결시키는것. (혹은 리다이덱트 URL 등등)

예를 들면 `?callbackURL=https://www.naver.com@google.com` 이런식이다.

네이버 링크처럼 보이지만 `https://www.naver.com@google.com`를 호출해보면 네이버가 아닌 구글로 연결된다. 

이렇게 정상적인 도메인 뒤에 @를 넣고 피싱 도메인을 넣으면 피싱 사이트로 연결이 된다. 


## @는 무엇일까?

HTTP URL은 URI 문법을 따른다.

그럼 URI에서 `@`는 어떻게 인식되는것일까?

URI에서 `@`를 붙이면 앞쪽은 사용자정보(인증정보) 뒷쪽을 호스트로 인식한다.

`https://www.naver.com@google.com`를 예시로 보면

앞쪽의 www.naver.com은 사용자정보(인증정보)가 되는것이고 

뒷쪽의 google.com이 호스트가 돼서 구글로 랜딩된다.


## Java 에서도 확인 가능

Java의 URI 객체로도 확인이 가능하다. 

```Java
  URI uri = new URI("https://www.naver.com@google.com")

  System.out.println(uri.getHost());

  // 결과값은 google.com 출력
```

저렇게 리다이렉트하는 곳에서는 URL을 한 번 검증 해줄 필요가 있다.


참고
- [URL 위키](https://en.wikipedia.org/wiki/URL#Syntax)

