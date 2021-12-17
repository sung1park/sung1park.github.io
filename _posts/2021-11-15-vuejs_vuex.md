---
layout: post
title: '[Vue.js] Vuex'
date: 2021-11-15 18:00:00 +0900
category: vue.js
---

# Vuex

> https://vuex.vuejs.org/kr/



## Introduction

### Vuex란?

- Vue.js 어플리케이션에 대한 <u>상태관리패턴 + 라이브러리</u>
- 어플리케이션의 모든 Component의 <u>중앙 집중식 저장소</u> 역할
- 예측 가능한 방식으로 상태를 변경하도록 함
- 어플리케이션이 커지고 여러 구성 요소로 구성되는 경우 데이터를 공유하는 문제가 발생하는데, 이를 해결하기 위해 사용



### 상태관리패턴

#### 예시 (Vue 카운터 앱)

```js
new Vue({
  // State
  data () {
    return {
      count: 0
    }
  },
  // View
  template: `
    <div>{{ count }}</div>
  `,
  // Action
  methods: {
    increment () {
      this.count++
    }
  }
})
```

##### 

#### 단방향 데이터 흐름

- **State** : 앱을 작동하는 원본 소스
- **View** : State의 선언적 매핑
- **Action** : View에서 사용자 입력에 대해 responsive 하게 상태를 바꾸는 방법

![image-20211115164502960](/assets/img/image-20211115164502960.png)



#### 문제점

- 여러 컴포넌트가 하나의 데이터를 `props`와 `$emit`으로 주고받는 경우, 코드가 복잡해지고 유지보수가 매우 어려워진다



#### Vuex의 상태관리패턴

- **State**를 싱글톤으로 관리
- 상태 관리에 특정 규칙을 적용해 코드의 구조와 유지 관리를 유용하게 함



### Vuex 설치

- NPM

  ```bash
  npm install vuex --save
  ```

- Vue-cli

  ```
  vue add vuex
  ```



### Vuex 사용

- `main.js`

```js
import Vue from 'vue';
import App from './App.vue';
import store from './store/store';

Vue.config.productionTip = false;

new Vue({
  render: (h) => h(App),
  store,
}).$mount('#app');
```





## 핵심 컨셉

> State, Getters, Mutations, Actions
>
> MVC 패턴에서 Controller, Service, DAO의 관계와 비슷해보인다.

![image-20211115165301975](/assets/img/image-20211115165301975.png)

#### State

- 저장소에서 data 속성의 역할
- `this.$store.state.data_name`으로 State에 직접 접근할 수 있지만, state에 접근할 수 있는 것은 위의 다이어그램처럼 Mutations로 한정



#### Getters

```js
// store > index.js
getters: {
  // 복잡한 계산식을 처리 : computed
  countMsg(state) {
    // return state.count + '번 호출됨';
    let msg = "10번보다 ";
    if (state.count > 10) {
      msg += "많이 ";
    } else {
      msg += "적게 ";
    }
    return msg + " 호출됨(" + state.count + ")";
  },
},
```

```js
// getter에 접근하는 곳
export default {
  computed: {
    countMsg() {
      return this.$store.getters.countMsg;
    }
  }
};
```

- Vue 인스턴스의 `computed`와 같은 역할
- state가 변한다면 내부 코드를 처리하여 반환하고, 그렇지 않으면 캐싱된 데이터를 반환
- 첫번째 인자는 무조건 'state'가 되어야한다



#### mapGetters

```js
import { mapGetters } from 'vuex';

export default {
  computed: {
    ...mapGetters({
      countMsg: 'countMsg',
      msg1: 'msg1',
      msg2: 'msg2',
      msg3: 'msg3',
    }),
    // ...mapGetters(["countMsg", "msg1", "msg2", "msg3"]),
  },
}
```

- 여러 getter를 더 간단하게 호출할 수 있다 
- ES6의 Destructuring assignment 기능을 이용해 더 짧은 코드로 작성할 수 있다 



#### Mutations

```js
mutations: {
  ADD_ONE(state) {
    state.count += 1;
  },
  ADD_TEN_COUNT(state, payload) {
    state.count += payload;
  },
  ADD_OBJ_COUNT(state, payload) {
    state.count += payload.num;
  },
},
```

```js
methods: {
  addCount: function() {
    this.count += 1;
    this.$store.commit('ADD_ONE');
    // this.$store.state.count++;
  },
  addTenCount: function() {
    this.count += 10;
    this.$store.commit('ADD_COUNT', 10);
  },
  addObjCount: function() {
    let num = Math.round(Math.random() * 100);
    console.log(num);
    this.count += num;
    this.$store.commit('ADD_OBJ_COUNT', { num });
  },
},
```

- **동기** method, 첫 번째 인자로 'state'를 받음
- State에 직접 접근할 수 있는 유일한 방법
- <u>commit</u>으로 접근, 객체를 같이 넘길 수 있음
- 실무에서는 Actions와 구분하기 위해 이름을 대문자로 쓰는 편

  

#### mapMutations

```js
import { mapMutations} from "vuex";

export default {
  ...
  methods: {
    ...mapMutations({
      addMOne: "ADD_ONE",
      addMTenCount: "ADD_TEN_COUNT",
      addMObjCount: "ADD_OBJ_COUNT",
    }),
    addCount: function() {
      this.count += 1;
      // this.$store.commit('addOne');
      this.addMOne();
    },
    addTenCount: function() {
      this.count += 10;
      // this.$store.commit('addCount', 10);
      this.addMTenCount(10);
    },
    addObjCount: function() {
      let num = Math.round(Math.random() * 100);
      this.count += num;
      // this.$store.commit('addObjCount', { num });
      this.addMObjCount({ num });
    },
  },
};
```



#### Actions

```js
actions: {
  asyncAddOne(context) {
    setTimeout(() => {
      context.commit("addOne");
    }, 2000);
  },
},
```

```js
methods: {
  asyncCount() {
    this.$store.dispatch("asyncAddOne");
  },
},
```

- **비동기** method, 첫 번째 인자로 'context'를 받음
- 비동기 작업의 결과를 적용하려고 할 때 사용
- 비동기 로직이 끝나고 mutations을 동기적으로 처리
- <u>dispatch</u>로 접근



```js
actions: {
  createTodo({ commit }, todoItem) {
      commit('CREATE_TODO', todoItem);
  },
}
```

- actions의 메서드의 첫 인자는 'context'이지만, 위처럼 객체 타입으로 'commit'을 직접 받아 사용할 수 있다



#### mapActions

```js
import { mapActions } from "vuex";

export default {
  methods: {
    ...mapActions(["asyncAddOne"]),
    asyncCount() {
      this.asyncAddOne();
    },
  },
};
```



#### 정리

![vuex](/assets/img/vuex.png)
