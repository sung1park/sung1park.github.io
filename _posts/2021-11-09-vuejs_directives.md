---
layout: post
title: '[Vue.js] Directives'
date: 2021-11-09 18:00:00 +0900
category: vue.js
---

# Vue Directives

> Vue의 지시자

- 데이터 바인딩의 기본 형태는 'Mustache' 구문 `{{ <내용> }}`



### v-once

```vue
<span v-once>다시는 변경하지 않겠습니다. : {{ msg }}</span>
```

- 데이터 변경 시 업데이트 되지 않는 DOM을 지정



### v-text, v-html

```vue
<div id="app">
    <!-- msg: <h2>Hello Vue!</h2> -->
    <div v-text="'msg: ' + msg"></div>
    <!-- Hello Vue! -->
    <div v-html="msg"></div>
</div>
<script>
	...
    msg: '<h2>Hello Vue!</h2>'
    ...
</script>
```

- `v-text`는 data의 변수를 불러오지만 텍스트 그 자체로 가져옴
- `v-html`은 data의 변수를 html로 가져옴

:white_check_mark: `v-html`은 사용자가 입력한 것을 html로 이용할 수 있어, 프로그램의 안전이나, 보안상의 이유로 지양한다



### v-model

```vue
<input type='text' v-model='message'>
입력 메세지 : {{ message }}
```

- form의 input, textarea 같은 곳에 양방향 바인딩 처리를 위해 사용



### v-bind

```vue
<div id="app">
    <a v-bind:href="link1">네이버로 가기</a>
    <a :href="link1">네이버로 가기</a>
    <div v-bind:[key]="value"></div>
</div>
<script>
	...
    key: 'id',
    value: 'linkToNaver',
    link1: 'https://www.naver.com',
	...
</script>
```

- data의 변수와 바인딩 처리를 위해 사용
- 약어로 `:` 사용 가능
- `[<attrname>]`을 통해 속성 명을 변수에서 가져올 수 있음. 이 때 속성 값도 변수에 있어야 함



### v-show

```vue
<div id="app">
    <div v-show="isShow">{{ msg }}</div>
</div>
<script>
	...
    isShow: true,
    msg: 'Hello!'
    ...
</script>
```

- `v-show="condition"`에서 조건이 true면 화면에 보이고, false면 화면에서 숨김
- css의 `display: none`과 같이 작용



### v-if, v-else-if, v-else

```vue
<div id="app">
    <div>
        <span>나이: </span>
        <input type="number" v-model="age">
    </div>
    <div>
        <span>요금: </span>
        <span v-if="age < 10">3000원</span>
        <span v-else-if="age < 20">7000원</span>
        <span v-else-if="age < 65">10000원</span>
        <span v-else>무료</span>
    </div>
</div>
<script>
	...
    	age: 0,
    ...
</script>
```

- `if` ~ `else if` ~ `else` 구문
- 조건에 맞는 요소들만 보여준다는 점에서 v-show와 비슷하지만, v-show와는 다르게 조건이 false일 경우 엘리먼트를 렌더링하지 않고 조건 변경 시 삭제한다
- template를 지원한다



### v-for

```vue
<div id="app">
    <ul>
        <li v-for="(item, index) in items" :key="index">
        	{{ index }} : {{ item.key }}
        </li>
    </ul>
</div>
<script>
	...
    items = [
    	item1: { key1: value1, key2, value2, ... }
        ...
    ]
    ...
</script>
```

- key를 왜 써야 하는가?

  - Vue가 예측 가능하게 작동하도록 하기 위해 (Edge case에서)
  - 일반적으로 <u>유일값</u>을 지정
  - https://kr.vuejs.org/v2/style-guide/#v-for-%EC%97%90-key-%EC%A7%80%EC%A0%95-%ED%95%84%EC%88%98

- v-if와 v-for를 같이 쓰지 말것

  - https://kr.vuejs.org/v2/style-guide/#v-if%EC%99%80-v-for%EB%A5%BC-%EB%8F%99%EC%8B%9C%EC%97%90-%EC%82%AC%EC%9A%A9%ED%95%98%EC%A7%80-%EB%A7%88%EC%84%B8%EC%9A%94-%ED%95%84%EC%88%98

  - Vue가 directive를 처리할 때 v-for가 v-if보다 우선순위가 높음

  - 쓸데 없는 for문을 만들어 조건에 맞지 않는 항목을 삭제하다 보니 성능이 떨어짐

  - 'computed' 속성을 이용하기

    ```vue
    <!-- items의 객체들 중 data의 key의 value값과 같은 객체들을 찾아냄 -->
    computed: {
    	matchItems: function() {
    		return this.items.filter(item => item.key === this.key)
    	}
    }
    ```

    

### template

```vue
<template v-if="count % 2 == 0">
	<span>여러개의 태그를 묶어서 처리해야 할 경우</span>
	<span>각각의 태그에 v-if를 달 필요가 없다</span>
</template>
```

- 여러개의 태그를 묶어서 처리해야 할 경우 사용한다



### v-cloak

```vue
<style>
    [v-cloak]::before {
        content: '로딩중,,,',
    }
</style>
<div id="app">
    <div v-cloak>
        <h1> {{ msg }} </h1>
    </div>
</div>
```

- Vue instance가 준비될 때 까지 mustache 바인딩을 숨기는데 사용한다



### v-on

- Vue Event Listener. 약어로 `@` 사용
- 메서드의 이름을 받아 처리하며, 필요한 경우 패러미터를 넘겨줄 수 있음

```vue
<button v-on:click="greet1('HELLO')">Greet</button>
<button v-on:click="greet2($event, 'Hello')">Greet</button>
```

##### Event modifier

- 'prevent'로 `form~submit`이나 `a`태그의 기본 동작을 막을 수 있음
  - `e.preventDefault()`
  - `@click.prevent="..."`

##### Key Event

- 다음과 같이 key에 이벤트를 줄 수 있음
  - `@keyup`, `@keyup.13`
  - `@keyup.enter`, `@keyup.tab`, ...



### ref, $refs

```vue
<input type="text" v-model="id" ref="id" @keyup="idCheck" />
<script>
...
methods: {
    idCheck() {
        this.$refs.id.focus();
        console.dir(this.$refs.id);
        ...
    }
}
...
</script>
```

- `$refs`를 이용해 DOM에 접근 가능
- Vue의 중요한 목적 중 하나는 개발자가 DOM을 직접 다루지 않게 하는 것이기에, 되도록 `ref`를 사용하는 것보다는 Vue 객체를 이용하는 것이 좋다



### Class bind

```vue
<div v-bind:class="{active: isActive}">VueCSS적용</div>
```

- 위의 예에서 `isActive`는 `boolean`형 뷰 객체이다. true/false 값에 따라 active 여부가 결정된다



### Form Input binding

##### form-수식어

- `v-model.trim` : 키 이벤트 발생 시 앞 뒤 공백 트림
- `v-model.lazy` : 기본적으로 v-model은 key stroke 발생 시 동기화 하는데, lazy를 이용해 change 이벤트에 동기화 하도록 할 수 있음 
- `v-model.number` : 사용자 입력을 자동으로 숫자로 형변환



##### form-text, textarea

- text : 내부에 `{{ }}`로 값 출력 가능
- textarea : 내부에 `{{ }}`을 사용할 수 없어, v-model을 사용



##### form-checkbox

- 하나의 체크박스는 단일 boolean 값을 가짐

- true-value, false-value 값을 지정할 수 있음

- v-model 값이 같은 체크박스의 값들은 하나의 배열에 값이 알아서 추가됨

  ```vue
  <div id="app">
    <div>당신이 가고 싶은 지역을 선택하시오</div>
    <div>  
      <input type="checkbox" id="seoul" value="서울" v-model="checkedAreas" />
      <label for="seoul">서울</label>
      <input type="checkbox" id="daejeon" value="대전" v-model="checkedAreas" />
      <label for="daejeon">대전</label>
      <input type="checkbox" id="daegu" value="대구" v-model="checkedAreas" />
      <label for="daegu">대구</label>
      <input type="checkbox" id="busan" value="부산" v-model="checkedAreas" />
      <label for="busan">부산</label>
      <input type="checkbox" id="jeju" value="제주" v-model="checkedAreas" />
      <label for="jeju">제주</label>
    </div>
    <span>체크한 지역: {{ checkedAreas }}</span>
  </div>
  <script>
    new Vue({
      el: '#app',
      data: {
        checkedAreas: [],
      },
    });
  </script>
  ```



##### form-radio

- Vue 객체의 변수값을 설정하고 v-model을 이용하면 초기 선택 항목을 지정할 수 있음

  ```vue
  <div id="app">
    <div>당신이 가고 싶은 지역을 선택하시오</div>
    <div>  
      <input type="radio" id="seoul" value="서울" v-model="checkedArea" />
      <label for="seoul">서울</label>
      <input type="radio" id="daejeon" value="대전" v-model="checkedArea" />
      <label for="daejeon">대전</label>
      <input type="radio" id="daegu" value="대구" v-model="checkedArea" />
      <label for="daegu">대구</label>
      <input type="radio" id="busan" value="부산" v-model="checkedArea" />
      <label for="busan">부산</label>
      <input type="radio" id="jeju" value="제주" v-model="checkedArea" />
      <label for="jeju">제주</label>
    </div>
    <span>선택한 지역 : {{ ckArea }}</span>
  </div>
  <script>
    new Vue({
      el: '#app',
      data: {
        checkedArea: '제주',
      },
    });
  </script>
  ```



##### form-select 

- select-option의 multiple 속성을 통해 다중 선택 가능

  ```vue
  <div id="app">
    <div>
      <p>여행하고 싶은 지역을 선택하세요. (다중선택 가능)</p>
      <select v-model="selectedArea" multiple>
        <option disabled value="">선택하세요</option>
        <option value="seoul">서울</option>
        <option value="daejeon">대전</option>
        <option value="daegu">대구</option>
        <option value="busan">부산</option>
        <option value="jeju">제주</option>
      </select>
    </div>
    <span>선택한 지역 : {{ selectedArea }}</span>
  </div>
  <script>
    new Vue({
      el: '#app',
      data: {
        selectedArea: [],
      },
    });
  </script>
  ```
  
  