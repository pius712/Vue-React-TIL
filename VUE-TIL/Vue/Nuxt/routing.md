# routing

## 동적라우팅

페이지 구조

```bash
pages/
--| _slug/
-----| comments.vue
-----| index.vue
--| users/
-----| _id.vue
--| index.vue
```

`pages/users/_id.vue`

```js
export default {
  validate ({ params }) {
    // Must be a number
    return /^\d+$/.test(params.id)
  }
}
```

위와 같은 방식으로 들어온 파라미터를 검증하는 함수를 만들 수 있고,
파라미터를 검증하고, true를 반환하지 않으면 404 error 페이지를 로딩한다. 

## 미들웨어

모든 미들웨어는 middleware/ 디렉토리에 있어야한다. 파일 이름은 곧 미들웨어의 이름이 됩니다. (예를 들어 middleware/auth.js는 auth 미들웨어가 됩니다.)

```js
//auth.js
export default function (context) {
  context.userAgent = process.server ? context.req.headers['user-agent'] : navigator.userAgent
}
```

___
**note**  
여기에서 `context`는 `vuex actions`의 context가 아니다.  
[nuxt/context 참고](https://nuxtjs.org/api/context)
___

미들웨어는 3군데에 지정 가능하고, 실행 순서는 아래와 같다. 

1. nuxt.config.js
2. 매칭된 레이아웃
3. 매칭된 페이지

`nuxt.config.js`

```js
module.exports = {
  router: {
    middleware: 'stats'
  }
}
```

`authenticated.js`

```js
export default function(context){
    if(!context.store.state.users.me){
        context.redirect('/');
    }
}
```

___
**note**
`v-if`와 같은 방식으로 버튼을 안보이게 처리하면 안되나?  
-> 주소로 접근하는 경우가 있기 때문에, 미들웨어로 처리는 어짜피 해줘야한다.
___
