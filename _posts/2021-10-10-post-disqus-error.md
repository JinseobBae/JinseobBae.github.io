---
layout: post
title: "[DISQUS] disqus 댓글 적용 시 'we were unable to load Disqus' 문제"
categories: [disqus]
tags: [git, disqus]
comments: true
---

Disqus 를 이용해 github io 댓글 기능을 적용하던 중 

```
we were unable to load Disqus. if you ar a moderator please see our troubleshooting guide.
```

오류(?)에 부딪혔다. 검색해서 신뢰하는 도메인 설정, ADMIN에서 url 재설정 등등 여러 방법을 시도 해봤지만 실패했다.


Disqus에서 제공하는 소스를 그대로 사용해도 문제가 있었다. 

```javascript
<div id="disqus_thread"></div>
<script>
    /**
    *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
    *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables    */
    /*
    var disqus_config = function () {
    this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
    this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
    };
    */
    (function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = 'https://사용자 short-name.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
```

`https://short-name(내 별칭).disqus.com/admin/install/platforms/universalcode/` 으로 들어가면 확인 가능하다.


여러 방법으로 확인해본 결과 config 설정 쪽 `this.page.url` 이 정상적이지 않았다.

해당 url에 전체 경로가 있어야한다. 

`https://~~~~~~~~.github.io/a/b/c` 이 아니라 `/a/b/c` 이렇게 값이 들어가서 오류가 있었다.

내가 쓰는 테마에서 해당 코드에 잘못된 값을 넣고 있었다.

만약 테마를 이용하고 있으면 해당 소스가 있는 부분이 있을것이다. 해당 부분도 확인해보자.  
