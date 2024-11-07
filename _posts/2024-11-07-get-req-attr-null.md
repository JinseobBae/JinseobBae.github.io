---
layout: post
title: "[Spring] RequestContextHolder의 getRequestAttribute가 null이다?"
categories: [java, spring]
tags: [java, spring]
---

Spring이 제공해주는 `RequestContextHolder`는 Request 정보를 쉽게 갖고올 수 있는 참 좋은 유틸이다.

일반적으로 `((ServletRequestAttributes)RequestContextHolder.getRequestAttributes()).getRequest()`로 요청 정보를 가져온다.

하지만 getRequestAttributes()이 null이라 오류가 나는 경우가 있다.

가장 많이 겪은 원인은 메서드의 주석을 보면 알 수 있다.

> Return the RequestAttributes currently bound to the thread or null if none bound

해당 메서드는 동일한 thread 에서만 RequestAttributes를 반환해준다. 

만약 비동기 처리를 위해 별도의 thread에서 호출을 하게 되면 RequestAttributes를 `null`로 반환한다. 
