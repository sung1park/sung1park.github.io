---
layout: post
title: '[Vue.js] Module'
date: 2021-11-09 18:00:00 +0900
category: vue.js
---

# Module

> 모듈이란?, 모듈을 가져오고 사용하는 방법



### Module 이란?

- 프로그램을 기능별로 여러 개의 파일로 나누는 형태



### Module 시스템

- CommonJS (NodeJS) - 웹 브라우저 밖에서도 동작될 수 있는 모듈 규칙 설립
- AMD (Asynchronous Module Definition) - 비동기적으로 모듈을 로딩
- <u>ESM (ECMAScript Module, ECMA215, es6)</u> - 자바스크립트 자체 모듈



### Module 정의 및 사용 방법 1

##### 내보내기 : export

```
const title = '계산기 모듈';
function add(i, j) { return i+j; }
function sub(i, j) { return i-j; }
export {title, add, sub}
```

##### 가져오기 : import

```
import {title, add, sub} from <파일명>;
```



### Module 정의 및 사용 방법 2

##### 내보내기 : export

```vue
export default {
	title = '계산기 모듈',
	add(i, j) { return i+j; },
	sub(i, j) { return i-j; },
}
```

##### 가져오기 : import

```
import calc from <파일명>;
calc.title
calc.add(20, 10)
calc.sub(20, 10)
```

