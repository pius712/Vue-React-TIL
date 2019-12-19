# Component Registration

## Local Registration

글로벌 컴포넌트 등록은 웹팩과 같은 빌드 시스템을 사용할 경우에 사용을 중단한 경우에도 마지막 빌드시에 포함이 될 수 있기 때문에, 이상적인 경우는 아니다. 즉 글로벌 등록은 불필요하고 고객들이 많은 자바스크립트를 다운로드해야할 수 있다. 

In these cases, you can define your components as plain JavaScript objects:

```javascript
var ComponentA = { /* ... */ }
var ComponentB = { /* ... */ }
var ComponentC = { /* ... */ }
```

```javascript
new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```