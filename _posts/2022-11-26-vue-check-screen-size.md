---
layout: post
title: "[Vue.js] Vue에서 디스플레이 사이즈 구하기"
categories: [vuejs]
tags: [vuejs]
---

## Screen 객체

---

vue에서도 **Screen** 객체를 이용해 현재 디스플레이의 **width**와 **height**를 구할 수 있다.

| function           | 설명                     |
|--------------------|--------------------------|
| screen.width       | 현재 디스플레이의 width  |
| screen.height      | 현재 디스플레이의 height |
| screen.availWidth  | 실제 사용 가능한 width   |
| screen.availHeight | 실제 사용 가능한 height  |



해당 정보로 브라우저 사이즈에 따라 데이터를 변경 할 수도 있다.

@media로 css 조정이 어렵거나 다른 컴포넌트로 style을 넘길 때 사용하면 좋을까한다.

혹은 모바일여부보단 디스플레이 크기 기준으로 style 조작을 할 때도 좋을듯하다.

물론 더 좋은 방법도 있겠지만 간단하게 할거면 충분하지싶다.

## 예시

---

```vue

export default {
  name: "MyVue",
  data() {
    return{
      myCustomStyle: {}
    }
  },

  created() {
    if(this.isSmallView()){
      this.myCustomStyle = {
        width: '90vw',
        position: 'relative'
      }
    }else{
      this.myCustomStyle = {
        width: '50vw',
        position: 'relative'
      }
    }

  },

  methods : {
    isSmallView() {
      if (screen.width <= 768) {
        return true
      } else {
        return false
      }
    },
  }
}

```

**참고**

---
- [stackoverflow](https://stackoverflow.com/questions/60322876/vuejs-way-to-detect-a-mobile-device-when-we-need-to-render-two-different-views)
- [TCPschool.com](http://www.tcpschool.com/javascript/js_bom_screen)
---





