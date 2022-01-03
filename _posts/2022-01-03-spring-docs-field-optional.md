---
layout: post
title: "[SPRING] Spring rest docs에서 원하는 필드만 문서화하기"
categories: [spring]
tags: [spring, spring docs]
---

테스트코드를 작성하고 Spring rest docs를 만들 때 

사용되는 body의 필드, path parameter, query parameter를 모두 명시해주어야 

오류없이 정상적으로 실행이 되고 문서가 만들어진다. 없으면 바로 오류가 나온다.

명시 안된 부분에 대해서 명시하라고 한다.

```

org.springframework.restdocs.snippet.SnippetException: The following parts of the payload were not documented:
{
  "code" : "code-01",
  "message" : "THIS IS TEST"
}

```

정확한 문서를 위해 모든 항목이 명시되는게 좋지만

때로는 부분적으로 원하는것만 문서화 하고싶을 때도 있다.

그럴 땐 필드를 명시할 때 `relaxed` 접두사가 붙은 메서드를 사용하면 된다.

![image](https://user-images.githubusercontent.com/29051992/147918178-66737fc6-ae50-4c18-a836-7bfb00a0a076.png)
(다양한 메서드들이 있다.)


예를들어, body의 필드 중 일부분만 문서화 하고 싶을 때

`requestFields` 앞에다가 `relaxed`를 붙인 `relaxedRequestFields` 메서드를 사용하면된다.





