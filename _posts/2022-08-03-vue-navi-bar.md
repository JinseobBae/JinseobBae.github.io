---
layout: post
title: "[Vue.js] 뷰에서 navigation bar 사용해보기(vue-navigation-bar)"
categories: [vuejs]
tags: [vuejs]
---

검색해보면 Vue.js에서 navigation bar(menu bar?)를 위한 다양한 컴포넌트들이 있다.

그 중 내가 사용한건 `vue-navigation-bar` 다. 

`vue-navigation-bar`는 상단 메뉴처럼 나오는데 항목에 

이미지, 부가적인 텍스트 등이 추가로 사용이 가능하며

반응형, router에 대한 지원도 해주기때문에 해당 컴포넌트를 선택했다.

![image](https://user-images.githubusercontent.com/29051992/182426893-b10784b4-6204-4212-9ea2-6d7badc2dd2e.png)


![image](https://user-images.githubusercontent.com/29051992/182426938-8f41c9b3-fe2c-46e5-a7b6-4b5fffd4a1a7.png)


`vue-navigation-bar`는 Vue2, Vue3 모두 지원을 한다.

현재 `5.0.0` 버전까지 나왔는데 Vue2는 `4.1.0` 버전을 사용해야 정상적으로 빌드가 된다.


## 설치

```
//Vue3
npm i vue-navigation-bar

//Vue2
npm i vue-navigation-bar@4.1.0
```

Vue2로 실행 할 경우 4.1.0 버전으로 설치한다.


## 활용

**Vue2 기준! (v4.1.0)**

### Import
설치 후 main.js에 import 해주면 사용이 바로 가능하다.

```javascript
import VueNavigationBar from "vue-navigation-bar";

Vue.component('navigation-bar', VueNavigationBar)
```

### 컴포넌트 사용

```vue
<template>
  <div id="app">
    <navigation-bar :options="navigationData"/>
  </div>
</template>

<script>
export default {
  name: 'app',
  data(){
    return {
      navigationData: {}
    }
  }
}
</script>
``` 

지정한 명칭(navigation-bar)으로 template에 바로 사용하면 된다.

데이터는 :options에 바인딩하면 메뉴바가 그려진다.

### 데이터

navigation-bar에 바인딩되는 데이터는 메뉴데이터, 엘리멘트ID, 라우터 사용여부 등이 있다.

```javascript
const data = {
    elementId: "유니크ID",
    isUsingVueRouter: true, // 라우터 사용여부(boolean)
    menuOptionsLeft: [ // 메뉴 좌측에 들어갈 항목(Array)
        {
            type: "link", // 메뉴 종류. link, spacer, button, dropdown이 있다.
            text: "한국", // 보여줄 text            
            arrowColor: "#659CC8", // 화살표 색상
            subMenuOptions: [ // 하위 메뉴 목록 (Array)
                {
                    type: "link", // 메뉴 종류. link, spacer, button, dropdown이 있다.
                    text: "서울", // 보여줄 text
                    path: "/seoul",  // 클릭 시 이동 할 경로
                },
                {
                    type: "link", // 메뉴 종류. link, spacer, button, dropdown이 있다.
                    text: "부산", // 보여줄 text
                    path: "/seoul",  // 클릭 시 이동 할 경로
                }
            ]   
        }                        
    ]   
}
```

기본적으로 이렇게 사용한다. 이외에도 이미지 넣기, 툴팁넣기, 애니메이션넣기 등 다양한 기능을 제공한다.

더 자세한 사용법은 아래 사이트에서 확인 가능하다.

- [vue-navigation-bar github](https://github.com/johndatserakis/vue-navigation-bar)

- [vue-navigation-bar npm](https://www.npmjs.com/package/vue-navigation-bar)


## 느낌

아직 Vue2를 사용하고있고 front-end를 전문적으로 하는게 아니다보니

최대한 심플하고 사용하기 편한걸 찾았다.

`vue-navigation-bar`는 단순한 사용법이지만 이미지, 애니메이션등 의외로 지원하는것도 많았다.

또 지속적으로 개발되고 있다는것도 좋았다.


