# Router

## Dynamic Route Matching

## Named Routes

```js
// routes/index.js
const router = new VueRouter({
  routes: [
    {
      path: '/user/:userId',
      name: 'user',
      component: User,
    },
  ],
});
```

이 경우 \$this.route.name을 하게 되면 값이 'user'이다.

아래는 위의 route에 link를 주기 위한 방법으로 위는 컴포넌트 적으로, 아래는 프로그래밍적으로 link를 주는 방법이다.

```html
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
```

```js
router.push({ name: 'user', params: { userId: 123 } });
```
