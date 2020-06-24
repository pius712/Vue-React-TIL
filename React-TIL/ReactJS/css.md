# CSS

- 일반적인 방식
- CSS Module
- style component 

## 일반적인 방식

아래와 같이 일반적인 방식으로 CSS 스타일링을 할 수 있다.

```js
import React, { Component } from 'react'
import './App.css'

class App extends Component{
  render(){
    return (
      <div className="App">
      </div>
    )
  }
}
```

```css
.App {
  text-align: center;
}
```

이때 주의해야할 부분은 css는 global으로 적용되기 때문에, css의 이름이 중복되지 않도록 주의해야한다. 
이를 해결하기 위해 아래와 같이 `컴포넌트명-클래스명` 과 같은 방식으로 처리해줄 수 있다.

```js
class App extends Component{
  render(){
    return (
      <div className="App">
        <header>
          <img className="logo"/>
        </header>
      </div>
    )
  }
}
```

```css
.App header{

}
.App .logo{

}
```

## CSS Module

파일의 이름을 `Header.module.css`와 같이 `이름.module.css`으로 만들어 준다. 

그리고 이를 js파일에서 import 한 뒤, 사용하면 된다. CSS Module 은 CSS 클래스를 불러와서 사용 할 때 [파일이름]_[클래스이름]__[해쉬값] 형태로 클래스네임을 자동으로 고유한 값으로 만들어주어서 컴포넌트 스타일 중첩현상을 방지한다.  

따라서 클래스의 이름을 다른 CSS Module에서 중복으로 사용해도, 중첩되지 않게 된다. 

```css
// Header.module.css
.navList {
  display: flex;
}
```

```js
import React from 'react'
import style from './Header.module.css'

class Header extedns React.Component{
  render() {
    return (
      <header className={style.navList}>
        // 중략
      </header>
    )
  }
}
```

css 클래스의 이름을 기억하고 있어야 한다는 단점이 있다. 


## styled-components

`npm i styled-components`

styled-components는 하나의 자바스크립트 안에 스타일을 작성할 수 있기 때문에, 다른 css파일을 만들 필요 없다. 

아래는 간단한 예시이다. 

```js
import React from 'react'
import styled from 'styled-component' 

// Create a Title component that'll render an <h1> tag with some styles
const Title = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
`;
// Create a Wrapper component that'll render a <section> tag with some styles
const Wrapper = styled.section`
  padding: 4em;
  background: papayawhip;
`;
// Use Title and Wrapper like any other React component – except they're styled!
export default ()=>(
  <Wrapper>
    <Title>
      Hello World!
    </Title>
  </Wrapper>
);
```

### Extending Styles

컴포넌트를 사용하고 싶은데, 해당 컴포넌트의 css에 약간의 변화를 주고 싶을 때, 상황에 따라 props를 정의하고 props를 바탕으로 스타일을 바꿔주는 것은 번거로운 일이다. 

해결책

기존의 스타일을 상속받는 새로운 컴포넌트를 만들기 위해서, `styled()` 생성자로 감싸주기만 하면 된다.  
아래의 예는 기존의 button 컴포넌트를 상속받아 확장하는 예이다. 

```js
const Button = styled.button`
  color: palevioletred;
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

// A new component based on Button, but with some override styles
const TomatoButton = styled(Button)`
  color: tomato;
  border-color: tomato;
`;

class App extends Component{
  render() {
    return (
      <div>
        <Button>Normal Button</Button>
        <TomatoButton>Tomato Button</TomatoButton>
      </div>
    )
    }
  );
}
```
## styled-reset