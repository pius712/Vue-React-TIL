# Props & State

## props



## state

state는 object이다. state는 변할 수 있는 데이터이다. 
 
### 초기화

```js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

### 수정
state는 직접 접근하여 수정하면 안된다. 

아래와 같은 코드는 reactivity가 없다. 

```js
// Wrong
this.state.comment = 'Hello';
```

```js
// Correct
this.setState({comment: 'Hello'});
```

## 데이터는 위에서 아래로 흐른다.

데이터(state, props)는 상위의 컴포넌트에서 하위의 컴포넌트로 흘러간다. 

아래의 경우 FormmatedDate 컴포넌트에 date props를 넘기는 것이다. 
하지만 FormmatedDate 컴포넌트는 이 props가 상위 컴포넌트의 state인지 props인지는 알 지 못한다.

```js
<FormattedDate date={this.state.date} />

// FormattedDate 컴포넌트
function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}
```