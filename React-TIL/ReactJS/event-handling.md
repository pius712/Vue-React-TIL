# Event Handling

- React의 이벤트는 소문자 대신 캐멀 케이스(camelCase)를 사용합니다.
- JSX를 사용하여 문자열이 아닌 함수로 이벤트 핸들러를 전달합니다

```js
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

## 기본 이벤트 방지

`<a>` 태그나 `<form>`태그의 기본 이벤트는 `e.preventDefault()`로 방지할 수 있다.

```js
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

## 이벤트 핸들러 함수에 인자 전달

```js
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

## this 바인딩

JSX 콜백 안에서 this의 의미에 대해 주의해야 하는데, JavaScript에서 클래스 메서드는 기본적으로 바인딩이 되지 않는다.  
this.handleClick을 바인딩하지 않고 onClick에 전달하였다면, 함수가 실제 호출될 때 this는 undefined가 됩니다.

이는 React만의 특수한 동작이 아니며, JavaScript에서 함수가 작동하는 방식이다. 일반적으로 `onClick={this.handleClick}`과 같이 뒤에 ()를 사용하지 않고 메서드를 참조할 경우, 해당 메서드를 바인딩 해야 합니다.


그러니까, `this.handleClick`이라는 함수는 현재 context가 없는 함수 handleClick이고 이 함수를 this라는 컨텍스트에 바인딩해주는 것이다. `Toggle.handleClick()`을 하게 되면, 해당 함수 내의 this는 Toggle과 바인딩 되어 있지만, 
`const newFunc = Toggle.handleClick`을 하고 newFunc()를 실행하면 함수 내의 this는 context를 잃는 것인데, 생성자에서 this.handleClick은 컨텍스트가 없는 함수이고, 이 함수를 this에 바인딩 하는 것이다. 

```js
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }
}
```