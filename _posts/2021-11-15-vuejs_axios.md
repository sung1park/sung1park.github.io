---
layout: post
title: '[Vue.js] Axios'
date: 2021-11-15 18:00:00 +0900
category: vue.js
---

# Axios

> HTTP 통신 라이브러리



### Vue에서의 Axios

- Vue에서 권고하는 http 통신 라이브러리
- 'promise' 기반
  - 비동기 로직 처리에 유용한 자바스크립트 라이브러리
- 자바스크립트는 단일 스레드로 코드를 처리하기 때문에 끝날 때까지 기다려주지 않음. 따라서 데이터를 요청하고 받아올 때까지 기다렸다가 화면에 나타내는 로직을 실행할 때 주로 promise를 활용



### Axios 설치

- CDN 방식

  ```vue
  <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
  ```

- NPM 방식

  ```bash
  npm install axios
  ```



### Axios API

- 서버에서 보낸 데이터를 정상적으로 받아오면 then()에서 로직 처리, 그렇지 않으면 catch()에서 로직 처리

##### GET

```
axios('<URL주소>').then().catch() // axios의 기본 요청 방식은 GET
axios.get('<URL주소>').then().catch()
```



##### POST

```
axios.post('<URL주소>').then().catch()
axios.post('<URL주소>', {
	<보낼 데이터 객체>
}).then().catch()
```



##### PUT, DELETE

```
axios.put('<URL주소>').then().catch()
axios.delete('<URL주소>').then().catch()
```



##### 옵션 속성

```
axios({<옵션 속성>});
axios({
	method: 'post',
	url: '/user',
	data: {
		name: '박성일',
		userid: 'sungil',
	},
});
```

- 옵션 속성에는 url, http 요청 방식, 보내는 데이터 유형 등을 직접 정의하여 보낼 수 있음



### async, await

```js
const todosURL = 'https://jsonplaceholder.typicode.com/todos';

async function getTodo(todosURL) {
  const response = await axios.get(todosURL);
  const id = response.data[0].id;
  const todo = await axios.get(`${todosURL}/${id}`);
  console.log(todo.data);
}

getTodo(todosURL).catch((error) => {
  console.log('error : ' + error);
})
```

- 처리하는 함수 앞에 `async`를 붙여 비동기적으로 처리함을 표시한다
- axios 구문 앞에 `await`을 붙여 데이터를 다 처리할때까지 기다린다
