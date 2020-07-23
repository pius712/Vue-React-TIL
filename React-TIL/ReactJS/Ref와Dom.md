# Ref 와 Dom

Ref는 render 메서드에서 생성된 DOM 노드나 React 엘리먼트에 접근할 수 있게 해준다.

## Ref를 사용해야 할 때
Ref의 바람직한 사용 사례는 다음과 같습니다.

- 포커스, 텍스트 선택영역, 혹은 미디어의 재생을 관리할 때.
- 애니메이션을 직접적으로 실행시킬 때.
- 서드 파티 DOM 라이브러리를 React와 같이 사용할 때.

선언적으로 해결될 수 있는 문제에서는 ref 사용을 지양하세요.

예를 들어, Dialog 컴포넌트에서 open()과 close() 메서드를 두는 대신, isOpen이라는 prop을 넘겨주세요.


## Ref 생성하기
Ref는 React.createRef()를 통해 생성되고 ref 어트리뷰트를 통해 React 엘리먼트에 부착됩니다. 보통, 컴포넌트의 인스턴스가 생성될 때 Ref를 프로퍼티로서 추가하고, 그럼으로서 컴포넌트의 인스턴스의 어느 곳에서도 Ref에 접근할 수 있게 합니다.

```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```

## Ref에 접근하기
render 메서드 안에서 ref가 엘리먼트에게 전달되었을 때, 그 노드를 향한 참조는 ref의 current 어트리뷰트에 담기게 됩니다.

`const node = this.myRef.current;`
ref의 값은 노드의 유형에 따라 다릅니다.

- ref 어트리뷰트가 HTML 엘리먼트에 쓰였다면, 생성자에서 React.createRef()로 생성된 ref는 자신을 전달받은 DOM 엘리먼트를 current 프로퍼티의 값으로서 받습니다.
- ref 어트리뷰트가 커스텀 클래스 컴포넌트에 쓰였다면, ref 객체는 마운트된 컴포넌트의 인스턴스를 current 프로퍼티의 값으로서 받습니다.
- 함수 컴포넌트는 인스턴스가 없기 때문에 함수 컴포넌트에 ref 어트리뷰트를 사용할 수 없습니다.

아래의 예제들은 위에서 언급한 차이점들을 보여줍니다.


### Ref와 함수 컴포넌트

함수 컴포넌트는 인스턴스가 없기 때문에 함수 컴포넌트에 ref 어트리뷰트를 사용할 수 없습니다.

```js
function MyFunctionComponent() {
  return <input />;
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }
  render() {
    // 이 코드는 동작하지 않습니다.
    return (
      <MyFunctionComponent ref={this.textInput} />
    );
  }
}
```
함수 컴포넌트에 ref를 사용할 수 있도록 하려면, forwardRef (높은 확률로 useImperativeHandle와 함께)를 사용하거나 클래스 컴포넌트로 변경할 수 있습니다.

다만, DOM 엘리먼트나 클래스 컴포넌트의 인스턴스에 접근하기 위해 ref 어트리뷰트를 함수 컴포넌트에서 사용하는 것은 됩니다.

```js
function CustomTextInput(props) {
  // textInput은 ref 어트리뷰트를 통해 전달되기 위해서
  // 이곳에서 정의되어야만 합니다.
  const textInput = useRef(null);

  function handleClick() {
    textInput.current.focus();
  }

  return (
    <div>
      <input
        type="text"
        ref={textInput} />
      <input
        type="button"
        value="Focus the text input"
        onClick={handleClick}
      />
    </div>
  );
}
```