# Hooks

리액트는 함수형 컴포넌트가 side effect를 수행하고, component state를 가질 수 있도록 `hooks` API를 제공한다. 

React Redux도 기존의 `connect()` Higher Order Component를 대체할 수 있는 hook이 있다. 

이 API는 컴포넌트를 connect()로 감쌀 필요 없이, Redux store를 받아오고, action을 dispatch할 수 있도록 도와준다. 
 

## Using Hooks in a React Redux App

기존의 connect() 함수에서는 컴포넌트에서 store를 사용하려면 app 전체를 `<Provider>` component로 감싸줬어야 했다.  

```js
const store = createStore(rootReducer)

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

위와 같이 감싸주면, 각각의 컴포넌트에서 React-Redux 훅을 import 하여서 함수형 컴포넌트에서 사용할 수 있다.  
Next.js 환경에서는 해줄 필요 없다.
## useSelector()

`const result: any = useSelector(selector: Function, equalityFn?: Function)`

`useSelector` 함수를 통해서 Redux 스토어의 데이터를 가져올 수 있다. 

기본 예시

```js
import React from 'react'
import { useSelector } from 'react-redux'

export const CounterComponent = () => {
  const counter = useSelector(state => state.counter)
  return <div>{counter}</div>
}
```

## useDispatch()

`const dispatch = useDispatch()`

이 훅은 Redux store의 `dispatch` 함수의 참조 값을 리턴한다.  
dispatch action이 필요하면 이를 사용하면 된다. 


Examples
```js
import React from 'react'
import { useDispatch } from 'react-redux'

export const CounterComponent = ({ value }) => {
  const dispatch = useDispatch()

  return (
    <div>
      <span>{value}</span>
      <button onClick={() => dispatch({ type: 'increment-counter' })}>
        Increment counter
      </button>
    </div>
  )
}
```

dispatch 함수를 사용한 콜백을 자식 컴포넌트로 넘겨줄 때는, useCallback 함수를 사용하는 것이 좋다.  
왜냐하면, memoize하지 않으면, 자식 컴포넌트가 불필요하게 렌더링될 수 있기 때문이다. 

```js
import React, { useCallback } from 'react'
import { useDispatch } from 'react-redux'

export const CounterComponent = ({ value }) => {
  const dispatch = useDispatch()
  const incrementCounter = useCallback(
    () => dispatch({ type: 'increment-counter' }),
    [dispatch]
  )

  return (
    <div>
      <span>{value}</span>
      <MyIncrementButton onIncrement={incrementCounter} />
    </div>
  )
}

export const MyIncrementButton = React.memo(({ onIncrement }) => (
  <button onClick={onIncrement}>Increment counter</button>
))
```