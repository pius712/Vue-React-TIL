# State 끌어 올리기

종종 동일한 데이터에 대한 변경사항을 여러 컴포넌트에 반영해야 할 필요가 있는데, 이럴 때는 가장 가까운 공통 조상으로 state를 끌어올리는 것이 좋다.

온도를 섭씨와 화씨로 받은 다음, 이 온도에서 물이 끓는지 안 끓는지를 확인 하는 로직의 컴포넌트를 만들 것이다. 

## Calculator 컴포넌트(상위)

```js
class Calculator extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {temperature: ''};
  }

  handleChange(e) {
    this.setState({temperature: e.target.value});
  }

  render() {
    const temperature = this.state.temperature;
    return (
      <fieldset>
        <legend>Enter temperature in Celsius:</legend>
        <input
          value={temperature}
          onChange={this.handleChange} />
        <BoilingVerdict
          celsius={parseFloat(temperature)} />
      </fieldset>
    );
  }
  // 위의 input 부분을 섭씨와 화씨로 나눈다. 
}
```

## 섭씨 화씨 input 분리하기

```js
const scaleNames = {
  c: 'Celsius',
  f: 'Fahrenheit'
};

class TemperatureInput extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {temperature: ''};
  }

  handleChange(e) {
    this.setState({temperature: e.target.value});
  }

  render() {
    const temperature = this.state.temperature;
    const scale = this.props.scale;
    return (
      <fieldset>
        <legend>Enter temperature in {scaleNames[scale]}:</legend>
        <input value={temperature}
               onChange={this.handleChange} />
      </fieldset>
    );
  }
}
```

기존의 Calculator의 JSX 부분 변경
```js
class Calculator extends React.Component {
  render() {
    return (
      <div>
        <TemperatureInput scale="c" />
        <TemperatureInput scale="f" />
      </div>
    );
  }
}
```

## 문제점 
TemperatureInput Component는 각각의 state로 temperature를 가지고 있다. 
따라서 한쪽의 state를 변경해도 다른 한쪽의 state는 reactivity하게 바뀌지 않는다. 

## 해결법? 
TemperatureInput의 state를 상위인 Calculator 컴포넌트로 보내준다. 그리고 각각의 하위 컴포넌트에 props를 전달하게 되면, 각 컴포넌트는 동기화된 상태를 유지하게 된다. 

```js
// TemperatureInput Component
render() {
  // Before: const temperature = this.state.temperature;
  const temperature = this.props.temperature;
  // ...
```

## note// vue는 ?

하위 티어에서 다음과 같이 `$emit('이벤트이름', 인자)`로 전달하면 
상위 티어에서 이벤트를 받고, `$event`로 인자를 받을 수 있다.
```html
<button v-on:click="$emit('enlarge-text', 0.1)">
  Enlarge text
</button>
```

```html
<blog-post
  ...
  v-on:enlarge-text="postFontSize += $event"
></blog-post>
```