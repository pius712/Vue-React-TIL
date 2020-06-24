# Conditional Rendering

- Element Variables
- Inline If with Logical && Operator
- Inline If-Else with Conditional Operator
- Preventing Component from Rendering

JS의 if 혹은 조건 연산자를 사용하여 현재 상태를 나타내는 element들을 만들 수 있고, react가 업데이트 되는 데이터에 맞추어 UI를 업데이트 시킬 수 있도록 해준다. 

아래 두 가지 컴포넌트가 있다고 가정해보자. 

```js
function UserGreeting(props) {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}
```

이 컴포넌트를 유저의 로그인 상태에 따라 둘 중 하나를 보여줄 수 있다.

```js
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

ReactDOM.render(
  // Try changing to isLoggedIn={true}:
  <Greeting isLoggedIn={false} />,
  document.getElementById('root')
```


## 엘리먼트 변수(Element Variables)

element를 저장하는 변수를 사용할 수 있다. 이 변수를 사용해서, 다른 부분은 그대로 두고,  특정한 부분만 조건부로 렌더링할 수 있도록 도와준다. 

로그인 버튼과 로그아웃 버튼이 있다고 가정해보자. 

```js
function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Login
    </button>
  );
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Logout
    </button>
  );
}
```

현재의 state에 따라서 어떤 버튼 컴포넌트를 렌더링할지 정한다. 


```js
class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = {isLoggedIn: false};
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true});
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false});
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;
    let button;
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
);
```

## Inline If with Logical && Operator

`{}` 괄호안에 expressions를 감싸서 JSX안에 넣을 수 있다. 이때 JS의 `&&` 연산자를 포함한다. 

`true && expression` -> evalute expression
`false && expresssion` -> evaluate X 
위와 같은 JS의 특징으로 인해서 가능하다. 

따라서, 조건이 `true`이면, && 바로 옆의 element는 렌더링이 되고, `false`이면 리액트는 이 expression을 무시하고 넘어간다. 

```js
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);
```


## Inline If-Else with Conditional Operator

또 다른 방법은 삼항 연산자를 이용하는 것이다 .

아래는 간단한 string을 rendering.

```js
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
  );
}
```

삼항 연산자를 사용해서 더 큰 expression도 렌더링할 수 있다. 하지만 코드를 읽기 어려워진다. 

```js
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn
        ? <LogoutButton onClick={this.handleLogoutClick} />
        : <LoginButton onClick={this.handleLoginClick} />
      }
    </div>
  );
}
```

## Preventing Component from Rendering

드물게 컴포넌트가 렌더링되었는데도 불구하고, 컴포넌트를 가리고 싶은 경우가 있을때, return null을 해주면 된다. 

아래의 예는  prop value에 따라서  `<WarningBanner />` 컴포넌트가 렌더링 될지 말지가 결정된다. 

```js
function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }

  return (
    <div className="warning">
      Warning!
    </div>
  );
}

class Page extends React.Component {
  constructor(props) {
    super(props);
    this.state = {showWarning: true};
    this.handleToggleClick = this.handleToggleClick.bind(this);
  }

  handleToggleClick() {
    this.setState(state => ({
      showWarning: !state.showWarning
    }));
  }
  render() {
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? 'Hide' : 'Show'}
        </button>
      </div>
    );
  }
}

ReactDOM.render(
  <Page />,
  document.getElementById('root')
);
```