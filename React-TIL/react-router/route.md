# Route 심화

## route render method

- `<Route component>`
- `<Route render>`
- `<Route children> function`

가장 권고되는 렌더링 방법은 `<Route>`의 children element를 사용하는 것이다. 

```html
<Route exact path="/">
  <Home />
</Route>
```

하지만, `<Route>`와 렌더링하는 방법은 여러가지가 있고, 위의 3가지 방법이 hooks가 도입되기 전의 버전에서 가장 많이 사용되던 방식들이다. 


You should use only one of these props on a given <Route>. See their explanations below to understand the differences between them.

## Route props

렌더링을 하는 3가지 방식 모두 아래 3개의 route props를 넘겨준다. 
- match
- location
- history

---
**note**  
해당 props는 따로 다룰 것

---

## component 

```js
import React from "react";
import ReactDOM from "react-dom";
import { BrowserRouter as Router, Route } from "react-router-dom";

// (match, location and history) props는 모든 라우트 props에서 사용이 가능하다 

function User(props) {
  return <h1>Hello {props.match.params.username}!</h1>;
}

ReactDOM.render(
  <Router>
    <Route path="/user/:username" component={User} />
  </Router>,
  node
);
```

component를 사용하게 되면, router는 컴포넌트에서 react element를 만들어내기 위해서 `React.createElement` 사용하게 된다.  

만약, component prop에 인라인 function을 넣는다면, 매번 render할때마다, 새로운 컴포넌트를 만들게 된다. 결과적으로 기존에 있던 component가 unmount되고 새로 만들어진 component가 mounting 된다. 즉, 기존의 component가 업데이트 되는 것이 아니라 새로이 만들어지는 것이다. 
inline function이나, inline rendering을 사용하고자 한다면, `render` 혹은 `children` prop을 사용하자. 

```html
// 이런식으로 사용하지 말자는 뜻이다. 
<Router>
  <Route path="/user/:username" component={()=><div>hi</div>)} />
</Router>

```
## render: func

이 함수는 원치 않는 remounting 없이, inline rendering을 가능하게 해준다. 
component prop을 사용해서 새로운 react element를 가지는 것이 아니라, location이 매칭될 때, 호출되는 함수를 넘겨줄 수 있다. 이 render prop function 또한 위에서 설명한 route props(match, location, history)에 접근할 수 있다. 

```html
<Router>
  <Route path="/home" render={() => <div>Home</div>} />
</Router>,
```