---
layout: post
title: "[Git] window에서 filename too long 문제 해결"
categories: [git]
tags: [git, window]
---

## 오류
윈도우에서 git으로 repository를 clone하거나 checkout을 할 때

```
unable to create file C:\dev\my_project\........ (filename too long)
```

이라고 뜨면서 커맨드를 실패 할 때가 있다.


## 원인

말 그대로 `파일명(단순 파일명뿐 아니라 파일의 전체 경로 포함)`이 너무 길어서 생기는 문제다.

윈도우는 내부 API를 위해서 파일명을 260자리로 제한한다고 한다.

## 해결

### 1. 파일명 조정 

전체경로까지 포함해서 260자이기 때문에 조절을 하려면 가능은하다.

하지만 코드 파일이면 경로에 패키지까지 포함되어있기 때문에 쉽게 바꾸기는 어렵다고 생각한다.

### 2. git config

git에서 파일명 길이 상관없이 생성이 가능하도록 변경한다.

```
git config --system core.longpaths true
``` 
    



**참고**

- [윈도우 git 체크아웃 Filename too long 오류 처리](https://javacan.tistory.com/entry/window-git-filename-too-long-error)






