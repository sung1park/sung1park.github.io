---
layout: post
title: '[Vue.js] Filter, Computed, Watch, Methods'
date: 2021-11-09 18:00:00 +0900
category: vue.js
---

# Filter, Computed, Watch, Methods

> Vue의 filter, methods, computed, watch 비교



### Filter

```vue
<div>
  <h3>{{ msg | count1 }}</h3>
  <h3>{{ msg | count2('문자를 입력하세요.') }}</h3>
</div>
<script>
    // 전역 필터
    Vue.filter('count1', (val) => {
            if (val.length == 0) {
              return;
            }
            return `${val} : ${val.length}자`;
          });
    // 로컬 필터
    new Vue({
      el: '#app',
      data: {
        msg: '',
      },
      filters: {
        count2(val, alternative) {
          if (val.length == 0) {
            return alternative;
          }
          return `${val} : ${val.length}자`;
        },
      },
    });
</script>
```

- 전역 필터나 로컬 필터를 설정하여 화면에 표시될 값들을 설정할 수 있다.



### Computed

- 특정 데이터의 <u>변경사항</u>을 실시간으로 처리
- 캐싱을 이용하여 데이터의 변경이 없을 경우 캐싱된 데이터를 값으로 반환 (`return`이 필수)
- 작성은 `method`처럼 하지만 사용할때는 property를 호출하여 `()`가 필요없다. (ex. `{{ calc }}`)



### Computed vs Method

- `computed`는 property를 호출하고, `method`는 함수를 호출
- `computed`는 종속 데이터에 변경 사항이 있을때만 수행, `method`는 호출시마다 수행
  - `computed`는 처음 수행 후 캐싱해 놓고, 변경 사항이 발생하면 다시 수행한다
  - 종속 데이터를 처리한 값을 여러 곳에서 사용해야 할 때, 매번 호출될 필요가 없는 경우에 `computed` 사용



### Watch

- Vue 인스턴스의 특정 property가 변경될 때 실행할 콜백 함수

```vue
<script>
...
watch: {
  // 여기서 a는 감시할 property 이름과 같게 설정
  a: function (newVal, oldVar) {...}
}
</script>
```



### Watch vs Computed

- `computed`는 종속 데이터가 변경되었을 때 그 데이터를 다시 계산해 캐싱한다
- `watch`는 데이터가 변경되었을 때 다른 데이터를 변경하는 작업을 할 때 사용된다

```vue
<script>
...
watch: {
  a: function (newVal, oldVal) {
    console.log(newVal, oldVal);
    this.b = newVal.split('').reverse().join('');
  },
},
</script>
```

