---
layout: post
title: "[JAVA] File 생성 시 기본 경로"
categories: [java]
tags: [java , file]
---

자바에서 파일을 만들어서 FTP로 전송하는 기능을 구현하게 되었다.



파일에 대한 개념이 확실하지 않아서 많은 시행착오가 있었다.


```java
File file = new File("test.txt")
```


경로설정없이 파일명만 적었을 때 내가 만든 파일이 어느 위치에 생성되었는지 제대로 알지 못해서 고생했다.


멍청하게 고민하다가 로그 한번 찍어보고 바로 알게 되었다.

`톰캣 : apache-tomcat\bin`

`웹로직 : \base_domain`



프로젝트 내부가 아닌 프로젝트를 실행중인 WAS 내부에 파일이 생성이 되었다.



톰캣은 bin에 웹로직은 도메인 바로 밑에 생겼다.



처음 알았다. 아직 많이 부족한 것 같다.

