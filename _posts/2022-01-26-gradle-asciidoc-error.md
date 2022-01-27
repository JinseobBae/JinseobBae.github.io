---
layout: post
title: "[SPRING] Asciidoctor 빌드 오류(asGemPath())"
categories: [spring]
tags: [gradle7, spring-rest-docs, asciidoctor]
---

## 오류

```
 In plugin 'org.asciidoctor.convert' type 'org.asciidoctor.gradle.AsciidoctorTask' method 'asGemPath()' should not be annotated with: @Optional, @InputDirectory.
```

잘 사용하던 spring-rest-docs에서 갑자기 오류가 발생했다.

`asGemPath()`에 문제가 있다면서 안된다.

확인을 해보니 스니펫에 adoc파일들은 만들어지는데 html이 안만들어졌다.


## 원인

gradle 버전이 변경되면서 발생한 오류다.

최근 gradle6에서 7로 버전을 올렸는데 버전을 올리자마자 발생했다.

검색을 해보니 `org.asciidoctor.convert(v 1.5.9)` 플러그인이 아직 gradle7과는 호환이 안되는 것 같다. 


## 해결

해결은 2가지 방법으로 가능했다.

### 1. Gradle 다운그레이드
 
정말 간단한 방법으로 gradle 버전을 다시 내려주면 된다. 


### 2. 플러그인 및 build.gradle 수정

플러그인과 build.gradle의 코드를 일부 수정해서 해결 할 수 있다.

```groovy
plugins{
  // id 'org.asciidoctor.convert' version "1.5.9" 
  id 'org.asciidoctor.jvm.convert' version "3.3.2" 
}
```

먼저 플러그인을 `org.asciidoctor.convert`에서 `org.asciidoctor.jvm.convert`로 변경해준다. 

```groovy
configurations {
        asciidoctorExtensions
}
```

그리고 `asciidoctorExtensions`를 사용할 수 있도록 설정해준다.

`asciidoctorExtensions`는 기존의 `asciidoctor`를 대체한다.

```groovy
dependencies{
//  asciidoctor 'org.springframework.restdocs:spring-restdocs-asciidoctor'
  asciidoctorExtensions 'org.springframework.restdocs:spring-restdocs-asciidoctor'
}
```

`dependencies` 부분의 `asciidoctor`를 `asciidoctorExtensions`로 수정해준다.

만약 `configurations`에 `asciidoctorExtensions`를 선언하지 않으면 알 수 없는 명령어 라면서 실행이 안된다. 

```groovy
asciidoctor {
        dependsOn test
        configurations 'asciidoctorExtensions' // 이부분!
        inputs.dir snippetsDir
  }
```

마지막으로 `asciidoctor` task에다가 **configurations 'asciidoctorExtensions'** 구문을 추가해주면 끝이다.


```groovy
plugins{
  id 'org.asciidoctor.jvm.convert' version "3.3.2" 
}

configurations {
  asciidoctorExtensions
}

ext {
  snippetsDir = file("${buildDir}/generated-snippets")
}

dependencies {
  testImplementation 'org.testcontainers:junit-jupiter:1.16.2'
  asciidoctorExtensions 'org.springframework.restdocs:spring-restdocs-asciidoctor'
  testImplementation 'org.springframework.restdocs:spring-restdocs-mockmvc:2.0.6.RELEASE'
}

test {
  outputs.dir snippetsDir
  useJUnitPlatform()
  finalizedBy 'asciidoctor'
}


asciidoctor {
  dependsOn test
  configurations 'asciidoctorExtensions'
  inputs.dir snippetsDir
}

```

최종적으로 이런 모습이 된다.

단, 이렇게 할 경우 html의 경로가 달리진다.

나같은 경우에는 `/build/docs/html5` 에서 html5가 없어지고 `/build/docs/asciidoc`에 생성되었다.

static으로 copy를 할 때 경로를 다시 확인해봐야 할 듯 하다.


**참고**

- [stackoverflow](https://stackoverflow.com/questions/68539790/configuring-asciidoctor-when-using-spring-restdoc)
- [https://xlffm3.github.io/spring%20&%20spring%20boot/rest-docs/](https://xlffm3.github.io/spring%20&%20spring%20boot/rest-docs/)






