---
layout: post
title: '[Vue.js] Basics'
date: 2021-11-08 18:00:00 +0900
category: vue.js
---

# Vue.js

> Javascript Framework



### MVVM 패턴

![MVVM](/assets/img/mvvm.png)

- Model + View + ViewModel
- Model : 순수 자바스크립트 객체
- View : 웹페이지의 DOM
- ViewModel : Vue의 역할



![mvcmvvm](/assets/img/mvcmvvm.png)

- 기존에는 자바스크립트로 view에 해당하는 DOM에 접근함거나 수정하기 위해 jQuery와 같은 라이브러리를 이용
- Vue는 view와 Model을 연결하고 자동으로 바인딩 하므로 양방향 통신을 가능하게 함



### Vue.js 설치

- `<script>` include (Download, CDN)
- NPM
- CLI



### Chrome Extension

- Vue.js devtools
  - 크롬에서 디버깅 할 수 있음



### Vue Instance 생성

```vue
<script>
	new Vue({
        el: '#app',
        data() {
            return {
                message: 'Hello Vue!',
            }
        }
    })
</script>
```

##### 속성

- `el`: Vue가 적용될 요소 지정
- `data`: Vue에서 사용되는 정보 저장. 객체 또는 함수 형태
- `template`: 화면에 표시할 html, css 등의 마크업 요소를 정의하는 속성
- `methods`: 화면 로직 제어와 관계된 method를 정의하는 속성. 이벤트 처리나 화면 동작과 관련된 로직을 추가
- Life cycle에 맞춰 실행되는 함수들



### Vue Instance의 유효 범위

- el 속성에 



### Life Cycle

![image-20211104171659932](/assets/img/image-20211104171659932.png)

##### Life Cycle Hook

- beforeCreate : Vue Instance가 생성되고 각 정보의 설정 전에 호출
- created : Vue Instance가 생성된 후 데이터들의 설정이 완료된 후 호출
- beforeMount : 마운트가 시작되기 전에 호출
- mounted : 지정된 element에 Vue Instance 데이터가 마운트 된 후에 호출
- beforeUpdate : 데이터가 변경될 때 virtual DOM이 랜더링
- updated : Vue에서 관리되는 데이터가 변경되어 DOM이 업데이트 된 상태
- beforeDestroy : Vue Instance가 제거되기 전에 호출
- destroyed : Vue Instance가 제거된 후 호출



### Vue 객체 내의 변수에 접근하기

```vue
<script>
  const vm = new Vue ({
    el: '#app',
    data() {
      return {
        ...
      }
    },
    methods() {
      ...
    },
  })
</script>
```

- 위와 같이 Vue 인스턴스에 이름을 붙이고 `vm.method()` 혹은 `vm.variable`로 내부 변수나 메서드에 접근할 수 있다.