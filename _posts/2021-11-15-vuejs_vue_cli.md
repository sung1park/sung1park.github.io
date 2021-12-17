---
layout: post
title: '[Vue.js] Vue CLI'
date: 2021-11-15 18:00:00 +0900
category: vue.js
---

# Vue CLI

> Vue CLI의 설치 및 사용법



### Vue CLI

- CLI (Command Line Interface)
- 개발의 편리성을 위해 필수처럼 사용
- Vue와 관련된 오픈 소스들의 대부분이 CLI를 통해 구성이 가능하도록 구현되어 있음



### 설치

##### NodeJS 설치 (NPM을 같이 설치) 

- https://nodejs.org/ko/

##### cmd 창에서 설치 버전 확인

```bash
node -v
npm -v
```

##### NPM을 이용해 @vue/cli 설치

```bash
npm install -g @vue/cli
```

##### 외부 프로젝트를 가져와 설치

```bash
npm install
```



### 프로젝트 생성

```bash
vue create <project-name>
```

- 위 명령어 입력 시 설치 과정이 진행되며 아래와 같은 것들을 선택할 수 있음
  - 기본 설치(babel, eslint)만 진행할지, 다른 플러그인을 선택할지
  - 라우터 모드 선택 (history 모드, hash 모드)
  - ESLint와 함께 사용할 포매터
  - Lint 검사를 저장 시 수행할 지



### 그 외 명령어

##### @vue/cli 프로젝트 생성 후 별도 플러그인 설치

```bash
vue add <plugin-name>
```

- vuex 등

##### axios 추가

```bash
npm install axios
```

- https://www.npmjs.com/package/vue-axios

##### vue-moment (날짜 형식) 추가

```bash
npm install vue-moment
```



### 프로젝트 실행

```bash
npm run serve
```



### SFC (Single File Component)

- 확장자가 `.vue`인 파일
- `template`, `script`, `style`로 구성

##### template

- 기본 언어 : html
- `.vue` 파일은 한 번에 최대 하나의 template 블록을 포함할 수 있다
- 내용은 문자열로 추출되어 컴파일 된 Vue Component의 template 옵션으로 사용

##### script

- 기본 언어 : js
- `.vue` 파일은 한 번에 최대 하나의 script 블록을 포함할 수 있다
- ES6를 지원하여 import와 export를 사용할 수 있음

##### style

- `.vue` 파일은 여러 개의 style 태그를 지원
- `scoped`를 통해 컴포넌트에만 CSS의 범위를 제한할 수 있음



### Components vs Views

![image-20211115214533840](/assets/img/image-20211115214533840.png)

- 라우터에 등록하는 것은 전체 화면을 보여주므로 views
- 그 외 view에 들어가는 component 들은 components에 넣어둔다



### Axios baseUrl 및 헤더 설정

> https://github.com/axios/axios#axioscreateconfig

```js
import axios from "axios";

// axios 객체 생성
export default axios.create({
  baseURL: "http://localhost:9999/vue",
  headers: {
    "Content-type": "application/json",
  },
});
```

- axios 객체를 생성함으로써, 반복되는 baseURL을 줄이고 header와 같은 정보를 담아 보낼 수 있다
- 외부에서 객체를 import 해 사용할 수 있다
- `util` > `http-common.js` 에 작성한 코드



### 그 외

- `@`는 src 경로를 의미

- `props`로 Object를 바로 pass 할 수 없다

  ```
  <blog-post v-bind="post"></blog-post>
  ```

  ```
  post: {
    id: 1,
    title: 'My Journey with Vue'
  }
  ```

  위 처럼 `v-bind`를 이용해 모든 property를 pass할 수 있다

