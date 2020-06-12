# Component

리액트 컴포넌트에서 다루는 데이터는 props 와 state이다.  
props 는 부모 컴포넌트가 자식 컴포넌트에게 주는 값이다. 자식 컴포넌트에서는 props 를 받아오기만하고, 받아온 props 를 직접 수정 할 수 없다.

반면에 state 는 컴포넌트 내부에서 선언하며 내부에서 값을 변경 할 수 있다.


## 함수 컴포넌트

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

## 클래스 컴포넌트 

함수 컴포넌트는 간단하다는 장점이 있다. 하지만 클래스 컴포넌트에서 사용할 수 있는 라이프 사이클 API와 같은 몇몇의 기능을 사용할 수 없다.  
아래는 클래스 컴포넌트의 형태이다.  

```js
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

## 렌더링

컴포넌트의 이름은 대문자로 시작해야 한다.

```js
// Welcome 컴포넌트
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

1. `<Welcome name="Sara" />` 엘리먼트로 ReactDOM.render()를 호출합니다.
2. React는 {name: 'Sara'}를 props로 하여 Welcome 컴포넌트를 호출합니다.
3. Welcome 컴포넌트는 결과적으로 `<h1>Hello, Sara</h1>` 엘리먼트를 반환합니다.
4. React DOM은 `<h1>Hello, Sara</h1>` 엘리먼트와 일치하도록 DOM을 효율적으로 업데이트합니다.

## 컴포넌트 분해

```js
class Comment extends Component {
  render(){
    return (
      <div className="Comment">
  // 분해할 부분 - start
        <div className="UserInfo">
          <img className="Avatar"
            src={props.author.avatarUrl}
            alt={props.author.name}
          />
          <div className="UserInfo-name">
            {props.author.name}
          </div>
        </div>
  // end
        <div className="Comment-text">
          {props.text}
        </div>
        <div className="Comment-date">
          {formatDate(props.date)}
        </div>
      </div>
    );
  }
}

```

```js
// Avatar Component
class Avatar extends Component{
  render() {
    return(
      <img className="Avatar"
          src={this.props.user.avatarUrl}
          alt={this.props.user.name}
        />
    )
  }
}
// UserInfo Component

class UserInfo extends Component{
  render(){
    return (
      <div className="UserInfo">
        <Avatar user={this.props.author}/>
        <div className="UserInfo-name">
          {this.props.user.name}
        </div>
      </div>
    );
  }
}

class Comment extends Component {
  render(){
    return (
      <div className="Comment">
        <UserInfo user={this.props.author}/>
        <div className="Comment-text">
          {this.props.text}
        </div>
        <div className="Comment-date">
          {formatDate(this.props.date)}
        </div>
      </div>
    );
  }
}

```