# 합성 (Composition)

## 컴포넌트 안에 다른 컴포넌트 담기

어떤 컴포넌트들은 어떤 자식 엘리먼트가 들어올 지 미리 예상할 수 없는 경우가 있는데, 

이러한 컴포넌트에서는 특수한 children prop을 사용하여 자식 엘리먼트를 출력에 그대로 전달하는 것이 좋다.

```js
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}
```

```js
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}
```

이 경우에는 border에 dialog를 담는 경우로 다른 컴포넌트 안에 JSX를 중첩시켜서 입력하면, 해당 컴포넌트는 props.children 을 통해서 JSX를 받을 수 있다. 일종의 vue의 slot과 같은 기능.