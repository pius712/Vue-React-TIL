## render 함수

기존의 방식

```html
<body>
  <div id="app">
    {{ message }}
  <div>
</body>
```

```js
new Vue({
  el: '#app'
  data:{
    message: 'hello world"
  },
})
```

render 함수 사용

```js
new Vue({
  el: '#app'
  data:{
    message: 'hello world"
  },
  render(createdElement){
    return createElement('p', this.message);
    // return createElement('태그 이름', '태그 속성', '하위 태그 내용')
  }
})
```

### main.js

```js
new Vue({
  reunder: h => h(App),
  //1 createElement -> h
  render: function(h){
    return h(App);
  },
  //2 es6
  render: h=> h(App);
})
```
