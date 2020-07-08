# API 

## Middleware API

### createSagaMiddleware(options)

Redux의 미들웨어를 만들고 Saga를 Redux store에 연결시킨다. 

- options: Object - A list of options to pass to the middleware. Currently supported options are:
  - context: Object - initial value of the saga's context.

  - sagaMonitor : SagaMonitor - If a Saga Monitor is provided, the middleware will deliver monitoring events to the monitor.

  - onError: (error: Error, { sagaStack: string }) - if provided, the middleware will call it with uncaught errors from Sagas. useful for sending uncaught exceptions to error tracking services.

  - effectMiddlewares : Function [] - allows you to intercept any effect, resolve it on your own and pass to the next middleware. See this section for a detailed example

예제

Below we will create a function configureStore which will enhance the Store with a new method runSaga. Then in our main module, we will use the method to start the root Saga of the application.

```js
configureStore.js

import createSagaMiddleware from 'redux-saga'
import reducer from './path/to/reducer'

export default function configureStore(initialState) {
  // Note: passing middleware as the last argument to createStore requires redux@>=3.1.0
  const sagaMiddleware = createSagaMiddleware()
  return {
    ...createStore(reducer, initialState, applyMiddleware(/* other middleware, */sagaMiddleware)),
    runSaga: sagaMiddleware.run
  }
}
```

```js
main.js

import configureStore from './configureStore'
import rootSaga from './sagas'
// ... other imports

const store = configureStore()
store.runSaga(rootSaga)
```

Notes
See below for more information on the sagaMiddleware.run method.

### middleware.run(saga, ...args)
Dynamically run saga. Can be used to run Sagas only after the applyMiddleware phase.

saga: Function: a Generator function
args: Array<any>: arguments to be provided to saga
The method returns a Task descriptor.

Notes
saga must be a function which returns a Generator Object. The middleware will then iterate over the Generator and execute all yielded Effects.

saga may also start other sagas using the various Effects provided by the library. The iteration process described below is also applied to all child sagas.

In the first iteration, the middleware invokes the next() method to retrieve the next Effect. The middleware then executes the yielded Effect as specified by the Effects API below. Meanwhile, the Generator will be suspended until the effect execution terminates. Upon receiving the result of the execution, the middleware calls next(result) on the Generator passing it the retrieved result as an argument. This process is repeated until the Generator terminates normally or by throwing some error.

If the execution results in an error (as specified by each Effect creator) then the throw(error) method of the Generator is called instead. If the Generator function defines a try/catch surrounding the current yield instruction, then the catch block will be invoked by the underlying Generator runtime. The runtime will also invoke any corresponding finally block.

In the case a Saga is cancelled (either manually or using the provided Effects), the middleware will invoke return() method of the Generator. This will cause the Generator to skip directly to the finally block.

## Effect-Creator

Notes:

- 각 함수는 일반 js 객체를 리턴하고, 어떠한 수행도 하지 않는다. 
- 실행은 미들웨어에 의해 수행된다.  during the Iteration process described above.
- 미들웨어는 각 Effect의 description을 확인하고, 적절한 액션을 수행한다. 

- take
- put
- call 
- fork

### take(pattern)

미들웨어에게 특정 액션을 기다리게 하는 것을 지시하는 Effect description을 만든다.  
Generator는 pattern에 맞는 액션이 dispatch 될 때까지 suspend 상태로 있는다. 


The result of `yield take(pattern)`의 결과는 dispatch 될 action 객체이다. 

### put

### call


### fork