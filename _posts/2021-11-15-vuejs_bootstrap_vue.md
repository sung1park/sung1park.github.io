---
layout: post
title: '[Vue.js] Bootstrap Vue'
date: 2021-11-15 18:00:00 +0900
category: vue.js
---

# Bootstrap Vue

> Vue에서 사용하는 Bootstrap
>
> https://bootstrap-vue.org/docs



## 설치

```
npm install vue bootstrap bootstrap-vue
```

```js
import Vue from 'vue'
import { BootstrapVue, BootstrapVueIcons } from 'bootstrap-vue'

import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

Vue.use(BootstrapVue)
Vue.use(BootstrapVueIcons)
```

- npm 명령어로 설치한 후, 위 코드를 App.vue나 main.js에 넣어 bootstrap을 사용할 수 있다



## Components

### Table

```html
<b-table striped hover :items="items"></b-table>
```

`<b-th>`, `<b-tr>`을 이용해 이전과 같이 사용할 수 있음 

```js
data() {
  return {
    isBusy: true,
    fields: [
      { key: "no", label: "글번호", thStyle: { width: "10%" } },
      { key: "title", label: "제목", thStyle: { width: "50%" } },
      { key: "writer", label: "작성자", thStyle: { width: "20%" } },
      { key: "regtime", label: "작성일", thStyle: { width: "20%" } },
    ],
    articles: [],
  };
},
```

- data 안에서 field를 선언해 column의 세부 속성을 설정할 수 있다
- 테이블을 사용하는 방법은 다양하기 때문에 [bootstrap-vue](https://bootstrap-vue.org/docs/components) 문서를 참고하면 좋다



### Grid System

```vue
<b-container class="bv-example-row">
  <b-row>
    <b-col>1 of 3</b-col>
    <b-col>2 of 3</b-col>
    <b-col>3 of 3</b-col>
  </b-row>
</b-container>
```

- `b-container`, `b-row`, `b-col` 등을 이용해 이전 bootstrap과 동일하게 12열 체계의 그리드 시스템을 이용할 수 있다
