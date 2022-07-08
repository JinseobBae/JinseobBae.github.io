---
layout: post
title: "[IntelliJ] IntelliJ에서 wildfly/jboss program argument 설정하기"
categories: [intelliJ]
tags: [intelliJ]
---

STS에서 wildfly를 사용할 때 보면 program argument와 VM argument가 나눠져있다.

VM argument에서는 힙 메모리나 로그같은 설정을 하고 

program argument에서는 standalone.xml 위치 설정이나 base dir 설정 등을 한다.

STS에서는 한 화면에서 가능하지만 IntelliJ는 따로 분리되어 있다.

다음 순서대로 진행하면 program argument 설정이 가능하다.

1. Run/Debug Congifuration 
2. Startup/Connection 
3. Run나 Debug 선택 
4. Startup script의 Use default 체크 해제 
5. 체크박스 옆 아이콘 클릭(체크 해제하면 아이콘이 생김)



![image](https://user-images.githubusercontent.com/29051992/177966401-0bf04cee-d408-42f4-b77e-0ab4cb329d03.png)



![image](https://user-images.githubusercontent.com/29051992/177966500-b9490fb7-6f21-4565-948e-e292d548d36e.png)



**Run , Debug, Coverage 를 전부 각각 설정해줘야한다.**
