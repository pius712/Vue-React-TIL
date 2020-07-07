# redux-thunk

## install & setting

`npm install redux-thunk`

`import ReduxThunk from 'redux-thunk'; // no changes here 😀`

## Thunk의 의미?

thunk는 계산을 지연시키기 위해 expression의 감싸는 함수이다. 

```js
// calculation of 1 + 2 is immediate
// x === 3
let x = 1 + 2;

// calculation of 1 + 2 is delayed
// foo can be called later to perform the calculation
// foo is a thunk!
let foo = () => 1 + 2;
```
## 필요성

일반적인 redux store에서는 action dispatch를 통해, 동기적인 update만이 가능하다. 

미들웨어를 통해서 async 로직을 짤 수 있게 된다. 

## Motivation

`Redux Thunk` 미들웨어는 action creators를 action 객체를 return하는 것이 아니라 함수를 리턴 하는 방식으로 코드를 작성할 수 있다.   
`Redux Thunk`는 action dispatch를 지연시킬 수 있고, 특정 조건이 충족 될 경우에만 dispatch 시키는 방식으로 동작시킬 수 있다.  

내부의 함수는 인자로 store의 메서드 dispatch와 getState를 받을 수 있다. 

예시) 비동기 dispatch를 수행하는 함수를 리턴하는 action creator.

```js
const INCREMENT_COUNTER = 'INCREMENT_COUNTER';

function increment() {
  return {
    type: INCREMENT_COUNTER,
  };
}

function incrementAsync() {
  return (dispatch) => {
    setTimeout(() => {
      // Yay! Can invoke sync or async actions with `dispatch`
      dispatch(increment());
    }, 1000);
  };
}
```

예시) 조건에 따라 dispatch하는 함수를 리턴하는 action creator

```js
function incrementIfOdd() {
  return (dispatch, getState) => {
    const { counter } = getState();

    if (counter % 2 === 0) {
      return;
    }

    dispatch(increment());
  };
}
```
## 간단한 예시

```js
export default loginAction(data=>{
  return (dispatch,getState) => {
    const state = getState();
    dispatch(loginRequestAction());
    axios.post('login')
      .then((res)=>{
        dispatch(loginSuccesAction(res.data));
      }).catch((err)=>{
        dispatch(loginFailureAction(err));
      })
    }
})
```
## Composition

inner function에서 리턴되는 어떠한 값이라도 dispatch의 return value로 사용이 가능하다.  
Any return value from the inner function will be available as the return value of dispatch itself. This is convenient for orchestrating an asynchronous control flow with thunk action creators dispatching each other and returning Promises to wait for each other’s completion:

```js
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers';

// Note: this API requires redux@>=3.1.0
const store = createStore(rootReducer, applyMiddleware(thunk));

function fetchSecretSauce() {
  return fetch('https://www.google.com/search?q=secret+sauce');
}

// 아래는 일반적인 action creator이다. 
// 아래의 action들은 middle ware 없이 dispatch될 수 있다. 
// 하지만, 이것들은 해야할 것을(fact)를 기술할 뿐, async flow는 표현할 수 없다. 

function makeASandwich(forPerson, secretSauce) {
  return {
    type: 'MAKE_SANDWICH',
    forPerson,
    secretSauce,
  };
}

function apologize(fromPerson, toPerson, error) {
  return {
    type: 'APOLOGIZE',
    fromPerson,
    toPerson,
    error,
  };
}

function withdrawMoney(amount) {
  return {
    type: 'WITHDRAW',
    amount,
  };
}

//미들웨어 없이도 action을 dispatch할 수 있다. 
store.dispatch(withdrawMoney(100));

// 하지만, API 호출이나 router transition과 같은 async action을 하려면...

// thunk는 async를 수행할 수 있는 함수이다. 
// 그리고 dispatch를 수행하고, state를 읽어들일 수 있다.
// 아래는 thunk를 리턴하는 action creator의 예이다 .

function makeASandwichWithSecretSauce(forPerson) {
  // We can invert control here by returning a function - the "thunk".
  // 아래의 thunk 함수가 dispatch를 인자로 받으면, thunk middleware가 이를 중간애 가로채서, dispatch와 getState 인자와 함께 함수를 호출한다. 
  // 이러한 방식은 thunk가 loigc을 작성, store와 소통할 수 있도록 해준다. 
  return function(dispatch) {
    return fetchSecretSauce().then(
      (sauce) => dispatch(makeASandwich(forPerson, sauce)),
      (error) => dispatch(apologize('The Sandwich Shop', forPerson, error)),
    );
  };
}
// Thunk 미들웨어는 thunk async action을 그들이 action인것 처럼 dispatch 시켜준다. 

store.dispatch(makeASandwichWithSecretSauce('Me'));

// It even takes care to return the thunk’s return value
// from the dispatch, so I can chain Promises as long as I return them.

store.dispatch(makeASandwichWithSecretSauce('My partner')).then(() => {
  console.log('Done!');
});

// In fact I can write action creators that dispatch
// actions and async actions from other action creators,
// and I can build my control flow with Promises.

function makeSandwichesForEverybody() {
  return function(dispatch, getState) {
    if (!getState().sandwiches.isShopOpen) {
      // 프로미스를 반환할 필요는 없지만, caller 함수가 async dispatch 결과에 .then() 함수를 사용할 수 있도록, 프로미스를 반환할 수도 있다. 

      return Promise.resolve();
    }

    // plain action을 dispatch할 수도 있고, thunk를 dispatch 할 수도 있다. 둘다 가능. We can dispatch both plain object actions and other thunks,
    // 이 방식으로 single flow 안에서 async action를 합칠 수 있다. 

    return dispatch(makeASandwichWithSecretSauce('My Grandma'))
      .then(() =>
        Promise.all([
          dispatch(makeASandwichWithSecretSauce('Me')),
          dispatch(makeASandwichWithSecretSauce('My wife')),
        ]),
      )
      .then(() => dispatch(makeASandwichWithSecretSauce('Our kids')))
      .then(() =>
        dispatch(
          getState().myMoney > 42
            ? withdrawMoney(42)
            : apologize('Me', 'The Sandwich Shop'),
        ),
      );
  };
}

// This is very useful for server side rendering, because I can wait
// until data is available, then synchronously render the app.

store
  .dispatch(makeSandwichesForEverybody())
  .then(() =>
    response.send(ReactDOMServer.renderToString(<MyApp store={store} />)),
  );

// I can also dispatch a thunk async action from a component
// any time its props change to load the missing data.

import { connect } from 'react-redux';
import { Component } from 'react';

class SandwichShop extends Component {
  componentDidMount() {
    this.props.dispatch(makeASandwichWithSecretSauce(this.props.forPerson));
  }

  componentDidUpdate(prevProps) {
    if (prevProps.forPerson !== this.props.forPerson) {
      this.props.dispatch(makeASandwichWithSecretSauce(this.props.forPerson));
    }
  }

  render() {
    return <p>{this.props.sandwiches.join('mustard')}</p>;
  }
}

export default connect((state) => ({
  sandwiches: state.sandwiches,
}))(SandwichShop);
Injecting a Custom Argument
Since 2.1.0, Redux Thunk supports injecting a custom argument using the withExtraArgument function:

const store = createStore(
  reducer,
  applyMiddleware(thunk.withExtraArgument(api)),
);

// later
function fetchUser(id) {
  return (dispatch, getState, api) => {
    // you can use api here
  };
}
```
To pass multiple things, just wrap them in a single object. Using ES2015 shorthand property names can make this more concise.

```js
const api = "http://www.example.com/sandwiches/";
const whatever = 42;

const store = createStore(
  reducer,
  applyMiddleware(thunk.withExtraArgument({ api, whatever })),
);

// later
function fetchUser(id) {
  return (dispatch, getState, { api, whatever }) => {
    // you can use api and something else here
  };
}
```