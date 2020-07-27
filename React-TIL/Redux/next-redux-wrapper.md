# next-redux-wrapper

## install

`npm install next-redux-wrapper react-redux --save`

## Motivation

Redux를 정적 앱에서 setting 하는 것은 쉽다. 하나의 Redux store를 만들고 그 store가 모든 페이지들에게 연결된다. 

하지만, Next.js를 사용하게 되면, Redux에 연결된 컴포넌트들을 렌더링하기 위해서 서버에 다른 store 인스턴스가 필요해지는데 이 때문에 redux를 붙이는 것이 어려워진다. 

그리고 Next.js `getInitialProps`를 하는 동안 Redux의 Store에 접근이 필요할 수도 있다. 

next-redux-wrapper는 이러한 과정을 편리하게 해준다. next-redux-wrapper가 자동으로 store 인스턴스를 생성해주고, 같은 state를 가지도록 해준다. 

## Usage

Next.js는 여러가지의 data fetching 메커니즘이 있다. `react-redux-wrapper`는 이러한 메커니즘에 붙일 수 있다. 

그 전에 기본적으로 세팅해야하는 부분이 있다. .

Please note that your reducer must have the HYDRATE action handler. HYDRATE action handler must properly reconciliate the hydrated state on top of the existing state (if any). This behavior was added in version 6 of this library. We'll talk about this special action later.

store 디렉터리 아래 configureStore.js 파일을 만든다. 
```js
// store/configureStore.js 
import {createStore} from 'redux';
import {createWrapper} from 'next-redux-wrapper';
import reducer from '../reducer/index.js;';

// create a makeStore function
const configureStore = () => {
	const store = createStore(reducer);
	return store;
};
// export an assembled wrapper
const wrapper = createWrapper(configureStore, {
	debug: process.env.NODE_ENV === 'development',
});
export default wrapper; 
```

그리고 `pages/_app.js`에 모든 페이지를 아래와 같이 설정해주어야 한다. 

```js
import React from 'react';
import {wrapper} from '../components/store';

const MyApp = ({Component}) => (
    <Component/>
);

export default wrapper.withRedux(MyApp);
```

Next.js provides generic getInitialProps when using class MyApp extends App which will be picked up by wrapper, so you must not extend App as you'll be opted out of Automatic Static Optimization: https://err.sh/next.js/opt-out-auto-static-optimization. Just export a regular Functional Component as in the example above.