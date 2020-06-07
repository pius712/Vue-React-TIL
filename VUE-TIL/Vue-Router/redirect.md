# redirect

`/a`에서  `/b`로 리다이렉트 하는 방법
```js
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: '/b' }
  ]
})
```

## Alias

리다이렉트와 비슷하지만 조금 다른 경우로, `/b`에 접속을 했을 경우 URL에는 `/b`가 그대로 있지만 `/a`로 접속하는 경우이다. 

```js
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
})
```