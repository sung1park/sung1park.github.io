---
layout: post
title: '[Vue.js] Vue Router'
date: 2021-11-11 18:00:00 +0900
category: vue.js
---

# Vue Router

> Vue에서의 라우팅



### vue-router

- 라우팅 : 웹 페이지 간 이동 방법
- Vue.js의 공식 라우터
- 라우터는 컴포넌트와 매핑
- Vue를 이용한 SPA(Single Page Application)를 제작할 때 유용
- URL에 따라 컴포넌트를 연결하고 설정된 컴포넌트를 보여준다



### vue-router 설치

- CDN

  ```
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
  ```

- NPM 방식

  ```
  npm install vue-router
  ```
  ```vue
  import Vue from 'vue'
  import VueRouter from 'vue-router'
  
  Vue.use(VueRouter)
  ```
  - 모듈 시스템에서 사용하면 `Vue.use()`를 통해 명시적으로 라우터를 추가한다
​	


### vue-router 연결

- `new VueRouter()`를 통해 라우터 인스턴스를 생성하고 `routes` 옵션을 넘겨준다

```vue
const Main = {
  template: `<div>메인 페이지</div>`,
};
const Board = {
  template: `<div>자유 게시판</div>`,
}

const router = new VueRouter({
  routes: [
    { path: '/', component: Main },
    { path: '/board', component: Board },
  ]
})
```



### vue-router 이동 및 렌더링

##### router-link

```vue
<router-link to="/"></router-link>
```

- 내비게이션을 위해 router-link 컴포넌트를 사용
- 속성은 `to`를 사용. `v-bind`를 이용해 Vue 객체의 데이터를 사용 가능
- 기본적으로 `<a>` 태그로 렌더링

##### router-view

```vue
<router-view></router-view>
```

- 현재 라우트에 맞는 컴포넌트가 렌더링 된다



### 예시

##### index.html

```vue
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      .router-link-exact-active {
        color: red;
      }
    </style>
    <script src="https://unpkg.com/vue/dist/vue.js"></script>
    <script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
  </head>

  <body>
    <div id="app">
      <h1>Router</h1>
      <p>
        <router-link :to="{name: 'main'}">HOME</router-link>
        <router-link :to="{name: 'board'}">게시판</router-link>
      </p>
      <router-view></router-view>
    </div>

    <script type="module" src="app.js"></script>
  </body>
</html>
```

##### app.js

```js
import Main from "./components/Main.js";
import Board from "./components/Board.js";
import BoardDetail from "./components/board/BoardDetail.js";
import BoardUpdate from "./components/board/BoardUpdate.js";
import BoardWrite from "./components/board/BoardWrite.js";
import BoardList from "./components/board/BoardList.js";

// 라우터 객체 생성
const router = new VueRouter({
  mode: "history",
  routes: [
    {
      path: "/",
      name: "main",
      component: Main,
    },
    {
      path: "/board",
      name: "board",
      component: Board,
      redirect: "/board/list",
      children: [
        {
          path: "list",
          name: "boardlist",
          component: BoardList,
        },
        {
          path: "write",
          name: "boardwrite",
          component: BoardWrite,
        },
        {
          path: "update/:no",
          name: "boardupdate",
          component: BoardUpdate,
        },
        {
          path: "detail/:no",
          name: "boarddetail",
          component: BoardDetail,
        },
      ],
    },
  ],
});

// Vue 인스턴스에 라우터 주입
const app = new Vue({
  el: "#app",
  router,
});
```

##### BoardList.js

```js
export default {
  template: `<div>
    <ul>
      <li v-for="i in 5">
        <router-link :to="'/board/detail/' + i">{{i}}번 게시글</router-link>
      </li>
    </ul>
    <router-link to="/board/write">등록</router-link>
  </div>`,
};
```

- BoardList, BoardDetail, BoardWrite, BoardUpdate의 template를 import 하여 component로 사용한다
- 라우터 객체를 생성하고 Vue 인스턴스에 라우터를 주입한다
- html에서 `<router-view>`를 이용해 라우트에 맞는 컴포넌트를 렌더링한다
- 라우트에 이름을 설정할 수 있고, 다른 곳에서 이름을 사용하여 부를 수 있다
- 중첩되는 라우트에 대해 `children`을 이용해 중복되는 부분의 반복을 줄일 수 있다
- `redirect`할 수 있다.



### 프로그래밍 방식 라우터

```vue
$router.push('home')
$router.push({ path: 'home' })
$router.push({ name: 'boardview', params: {no: 3} })
$router.push({ path: '/board', query: {pg: 1} })
```

- 자바스크립트의 `location.href`에 대응되는 방식
- 화면 상에서 이벤트와 같은 것들에 사용할 수 있지만, 링크의 경우에는 `<router-link>`를 사용
- Vue 객체 안의 method 같은 곳에서 주로 이용한다



### History mode

`vue-router`의 기본 모드는 hash mode이다. URL 해시(#)를 사용해 전체 URL을 시뮬레이트 하여 URL이 변경될 때 페이지가 다시 로드 되지 않는다. 해시를 제거하기 위해 아래와 같이 **history** 모드를 사용할 수 있다.

```vue
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```

