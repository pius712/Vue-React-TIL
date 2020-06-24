# route meta filed

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      children: [
        {
          path: 'bar',
          component: Bar,
          // a meta field
          meta: { requiresAuth: true }
        }
      ]
    }
  ]
})
```
meta filed에 접근하는 방법

각각의 routes configuration에 있는 라우트 객체는 `route record`라고 불린다. 이 route record는 일반적으로 nested 객체인데이다. 따라서, route가 매치 될 때는 하나 이상의 route record에 매칭될 가능성이 있다.  

 예를 들어 `foo/bar` URL은 부모 route record와 child의 route record에 매칭될 것이다.  

모든 라우트 레코드는 $route 객체에 담겨있고, navigation guard 에서는 route 객체에 담겨있다. 이는 배열의 `$router.match` 형태로 담겨있고, 해당 배열을 반복해서 체크해야한다. 

An example use case is checking for a meta field in the global navigation guard:

```js
router.beforeEach((to, from, next) => {
  if (to.matched.some(record => record.meta.requiresAuth)) {
    // this route requires auth, check if logged in
    // if not, redirect to login page.
    if (!auth.loggedIn()) {
      next({
        path: '/login',
        query: { redirect: to.fullPath }
      })
    } else {
      next()
    }
  } else {
    next() // make sure to always call next()!
  }
})
```

---
**note**  
array.some(callback(element))  
해당 함수는 array에 있는 모든 값들을 순회하면서, callback 함수에 주어진 조건에 통과하는 것이 하나라도 있으면 `true` 아니면 `false`를 반환하는 함수이다. 

---