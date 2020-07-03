# Devtools

[공식문서](https://github.com/zalmoxisus/redux-devtools-extension)


## Use redux-devtools-extension package from npm 

다른 방법도 있는데, 쉬운 방법. 

`npm i redux-devtools-extension`

스토어에 추가해준다. 
```js
import { createWrapper } from 'next-redux-wrapper';
import { createStore, applyMiddleware, compose } from 'redux';
import { composeWithDevTools } from 'redux-devtools-extension';
import reducer from '../reducer/index.js';
const configureStore = () => {
	const middleware = [];
	const enhancer =
		process.env.NODE_ENV === 'production'
			? compose(applyMiddleware(...middleware))
			: composeWithDevTools(applyMiddleware(...middleware));
	const store = createStore(reducer, enhancer);
	return store;
};
const wrapper = createWrapper(configureStore, {
	debug: process.env.NODE_ENV === 'development',
});

export default wrapper;

```