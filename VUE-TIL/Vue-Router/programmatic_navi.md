# Programmatic Navigation

[공식문서](https://router.vuejs.org/guide/essentials/navigation.html)

## router.push(location, onComplete?, onAbort?)

___
**Note**  
vue 인스턴스 내부에는, `$router`를 통해서 router 인스턴스를 접근할 수 있다. 따라서 `this.$router.push`를 콜할 수 있다. 
____

`this.$router.push()` 메소드는 URL을 history stack에 새로운 entry로 넣기 때문에, 사용자가 이전 페이지를 클릭하면 함수 호출 전으로 돌아가게 된다. 이것은 `<router-link>` `<router-link :to="">`와 같이 동작한다.  