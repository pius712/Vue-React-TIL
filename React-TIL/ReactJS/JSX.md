# JSX란?

JSX는 React “엘리먼트(element)” 를 생성한다. 

JSX는 html이 아니라, javascript의 객체로 변환된다. 

Babel은 JSX를 React.createElement() 호출로 컴파일하면 js 객체로 변환된다.

```js
import React from 'react';

const element = <h1 className="greeting">Hello, world!</h1>;
// 위와 아래는 같은 코드이다. 
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

위의 코드는 아래의 js 객체로 변환이 된다.

```js
// 간략화된 코드이다. 
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```
