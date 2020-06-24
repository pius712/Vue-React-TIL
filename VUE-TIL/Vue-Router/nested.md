# nested router

## children

```html
<div id="app">
  <router-view></router-view>
</div>
```

```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}

const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User }
  ]
})
```
위의 `<router-view>`는 가장 탑 레벨에 있다. 이 `router-view`는 최상위 라우트에 매칭되는 컴포넌트를 렌더링한다.  

이와 비슷하게, 렌더링된 컴포넌트 내에도 `<router-view>`를 가지는 경우가 있다. 

```js
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  `
}
```

이 nested된 컴포넌트를 렌더링하기 위해서는 vuerouter 설정에 children 옵션을 사용해야한다. 

```js
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // UserProfile will be rendered inside User's <router-view>
          // when /user/:id/profile is matched
          path: 'profile',
          component: UserProfile
        },
        {
          // UserPosts will be rendered inside User's <router-view>
          // when /user/:id/posts is matched
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```

nested path에서 '/'를 사용하면 루트 path로 간주되는 것을 주의해야한다. 

위 코드에서 볼 수 있듯, children children 옵션은 기존의 route 배열의 route 객체와 같기 때문에 이와 같이 작성하면 된다.

주의해야할 점은, `/user/foo` 라우트에 방문할 시에는, 하위의 라우트에 매칭되는 것이 없기 때문에, 아무것도 렌더링 되지 않는다.  
따라서, 렌더링 되기를 바란다면, 비어있는 하위 라우트도 작성하여 적어줘야 한다. 

```js
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id', component: User,
      children: [
        // UserHome will be rendered inside User's <router-view>
        // when /user/:id is matched
        { path: '', component: UserHome },

        // ...other sub routes
      ]
    }
  ]
})
```