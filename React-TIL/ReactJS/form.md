# form

HTML의 form element는 react의 DOM element와는 동작하는 방식이 약간 다르다. 왜냐하면, HTML의 form element는 기본적으로 내부 state를 가지고 있기 때문이다. 

- Controlled Components
- The textarea Tag
- Select Tag
- multiple inputs
## Controlled Components

HTML에서 `<input>`, `<textarea>`, `<select>`와 같은 form element들은 그들만의 state를 가지고 유저의 input에 따라서 그 state를 업데이트 한다. 

리액트에서는 변경할 수 있는 state는 일반적으로 컴포넌트의 state property에 의해 관리되고, setState() 함수 호출을 통해서만 변경할 수 있다. 

이러한 두개의 방식을 React state를 'single source of truth'가 되도록 만들면서 합칠 수 있다. react component는 form을 렌더링하면서, user input에 따른 변경사항을 제어할 수 있는데, 이러한 방식을 `controlled component`라고 한다.

For example, if we want to make the previous example log the name when it is submitted, we can write the form as a controlled component:

```js
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

value 속성이 form element에 설정되어 있기 때문에, input에서 보이는 값은 항상 this.state.value이다.  
handleChange 함수는 매 keystroke마다 실행되기 때문에, react state를 매번 실행하여 업데이트 시켜준다. 


With a controlled component, the input’s value is always driven by the React state. While this means you have to type a bit more code, you can now pass the value to other UI elements too, or reset it from other event handlers.

## The textarea Tag

textarea의 경우도 일반적인 input 태그와 다르지 않다. 

```js
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 'Please write an essay about your favorite DOM element.'
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('An essay was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Essay:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

## multiple inputs

여러개의 input elements를 가지고 있다면, 각각의 element에 name 속성을 부여해서, handler function이 어떤 value에 접근하고 있는지 알려줘야 한다. 

```js
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.name === 'isGoing' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```