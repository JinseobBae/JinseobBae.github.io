---
layout: post
title: "[IntelliJ] Plugin 디버깅하기"
categories: [IntelliJ]
tags: [intellij, plugin]
---

인텔리J에서 사용하는 플러그인을 직접 만들거나 오픈라이센스면 기존걸 포크떠서 변경이 가능하다.
인텔리J는 플러그인 개발을 할 때 디버깅을 위한 gradle 명령어를 제공한다.

```
./gradlew runIde
```

해당 명령어를 실행하면 해당 플러그인이 인스톨된 intelliJ 커뮤니티 버전이 실행이 된다.

실행되는 버전은 `build.gradle`에 선언한 intelliJ 버전이다. 

break point도 사용이 가능하다.

아쉽게도 핫스왑기능은 제공하지 않는것같다.
