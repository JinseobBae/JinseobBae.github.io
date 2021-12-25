---
layout: post
title: "[Vue.js] git 없이 프로젝트 시작하기 "
categories: [vuejs]
tags: [vuejs]
---

vue.js 프로젝트를 생성 할 때 별다른 옵션이 없으면 자동으로 .git을 만들어주는듯하다.

```
vue create project_name
```

해당 커멘드로 프로젝트를 생성하면 project_name에 `.git`이 생기면서 git repository로 생성된다.

기존 git repository에서 생성 시 submodule처럼 별도의 repository로 인식된다.

만약에 하나의 repository로 관리하고 싶으면

```
vue create project_name --no-git
```

해당 커멘드로 생성하면 된다. 그럼 프로젝트만 생성해주고 `.git`은 생성하지 않는다.
