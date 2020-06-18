# navigation guard

- Global Before Guard
- Global Resolve Guards
- Per-Route Guard
- In-Component Guards

## Global Before Guard

```js
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  // ...
})
```

## Global Resolve Guards

`router.beforeEach`와 비슷하지만, `beforeResolve`는 navigation이 확정되기 바로 직전에 함수 호출이 된다는 것이다. 즉, component 내부의 가드와, async route comnponent가 처리 된 다음에 호출된다. 

---
**note**   
async route component?  

해당 라우트가 방문될 때, 그 라우트만 로딩되도록 하는 기술이다. 코드 스플릿팅에 해당하는 기술이다.   
___


## Per-Route Guard

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```
## In-Component Guards

- beforeRouteEnter
- beforeRouteUpdate
- beforeRouteLeave

### beforeRouterEnter

route가 렌더링되기 이전에 호출되는 함수.  
component instance가 형성되기 전이므로, `this`를 사용해서 접근할 수 없다. 

### beforeRouteUpdate

해당 컴포넌트가 변경될 때, 호출되는 함수.  
새 라우트에서 해당 컴포넌트가 재 사용된다. 
예를 들어, 다이나믹 라우트 매칭에서 `/foo/:id`에 해당하는 컴포넌트의 경우 `/foo/1` `/foo/2`의 경우에는 다른 라우트로 이동을 하게 되지만, 하나의 등록된 컴포넌트가 재사용이 된다.  
`this`를 통해 component에 접근할 수 있다. 

### beforeRouteLeave

해당 컴포넌트에서 떠날 때, 호출되는 함수. 
`this` 사용 가능.

```js
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {

  },
  beforeRouteUpdate (to, from, next) {
  },
  beforeRouteLeave (to, from, next) {
   }
}
```