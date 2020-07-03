# combineReducers

## combineReducers()

app의 크기가 커지면, reducer를 나누어서 각각의 리듀서가 특정 파트의 state를 관리하는 방식으로 나누게 된다. 

`combineReducers`는 이런 것을 돕는 헬퍼 함수로, 다른 reducer 함수들을 하나의 single reducer로 만들어준다, 

이 함수의 결과값은 각각의 자식 reducer를 호출하고, 그 결과를 하나의 state 객체로 모아준다. combinReducers로 생긴 state는 combineReducers() 함수로 넘겨줄때의 key로 네임스페이스가 생긴다. 

아래의 예를 보자. 

```js
rootReducer = combineReducers({potato: potatoReducer, tomato: tomatoReducer})

// key는 potato, tomato이다. 따라서,  
//위의 함수는 아래와 같은 state 객체를 만든다. 
{
  potato: {
    // ... potatoes, and other state managed by the potatoReducer ...
  },
  tomato: {
    // ... tomatoes, and other state managed by the tomatoReducer, maybe some nice sauce? ...
  }
}
```

`combineReducers()` 함수에 다른 key를 넣어서 state의 key name을 바꿀 수 있다.  
예를 들어 `combineReducers({ todos: myTodosReducer, counter: myCounterReducer })` 이 경우에는, state의 모양이 `{todos, counter}` 이 된다. 

일반적으로는 reducers의 이름을 state 조각으로 지어서 아래와 같이 축약 문법을 사용한다.  

 `combineReducers({ counter, todos })`=== `combineReducers({ counter: counter, todos: todos }).`


