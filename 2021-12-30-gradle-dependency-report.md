---
layout: post
title: "[Gradle] gradle dependency report 보기"
categories: [gradle]
tags: [gradle, report]
---

## Gradle Report

가끔 의존성을 설정하지않은 라이브러리가 보일때도 있고 버전이 여러개로 나오는 경우도 있다.

특정 상위패키지로 인해 자동으로 들어온건데, 그 경로를 추적해서 찾기가 생각보다 힘들다.

`gradle`에서 지원하는 `task` 중 `dependencies`를 보여주는 것도 있지만 텍스트형태의 트리구조라 깔끔하지는 않다.

그런 이유로 `gradle`에서 플러그인으로 `reporting` 기능을 지원해준다.


### Plugin 설정

`build.gradle`에 플러그인을 입력해주면 된다. (gradle 6.9 기준)

```
plugins {
    id 'project-report'
}
```

### Reporting

플러그인을 설정하고 리프레쉬를 해주면 `task`에 `reporting` 이라는 항목이 생긴다. (인텔리J 기준)

![image](https://user-images.githubusercontent.com/29051992/147739637-6ade06aa-8676-46ba-a042-d143acb8a0c5.png)

해당 테스크를 실행하면 바로 리포트가 생성되면서 파일 경로를 알려준다. 해당 파일을 실행시키면 내용을 볼 수 있다.

command로 할 경우에는

```shell

./gradlew projectReport

```

![image](https://user-images.githubusercontent.com/29051992/147740457-6312d2d8-e818-409f-8461-74410e483fdd.png)


리포트는 build 밑에 생성이 되며 총 4개가 생성된다.

 - dependencyReport (dependency tree형 리포트)
 - htmlDependencyReport (dependency html 리포트)
 - propertyReport (property 리포트)
 - taskReport (task 리포트)

dependency 리포트를 보면 어떤 상황에 해당 의존성이 설정되는지 보여준다.

![image](https://user-images.githubusercontent.com/29051992/147740271-26ebf448-8dd5-465f-b5bc-6638b9d6367d.png)

항목을 눌러보면 트리 구조로 깔끔하게 보여준다.

- - -
**참고**

 - [gradle docs](https://docs.gradle.org/current/userguide/project_report_plugin.html)



