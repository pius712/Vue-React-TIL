# ref

## API

```html
<!-- vm.$refs.p will be the DOM node -->
<p ref="p">hello</p>

<!-- vm.$refs.child will be the child component instance -->
<child-component ref="child"></child-component>
```

## Accessing Child Component Instances & Child Elements

props나 event가 있음에도, 때때로 js로 자식 컴포넌트를 직접적으로 접근해야할 수도 있다. 이렇게 하기 위해서는 자식 컴포넌트에 `ref` attribute를 통해서 reference ID를 부여해야한다.  
For example:

```html
<child-component ref="usernameInput"></child-component>
```

```js
this.$refs.usernameInput;
```

DOM element ref: reference -> DOM element
Child component : reference -> component instance

base-component 안에 input태그에 아래와 같이 ref를 주고 focus를 주는 함수를 정의하면... 그 부모의 컴포넌트에서 이를 사용할 수 있다.

```html
<!-- child component -->
<template>
    <input ref="input"></input>
<template>

<script>

export default:{
  methods: {
    focus():{
        this.$refs.input.focus();
    }
  }
}
```

```js
//  parent component
this.$ref.username.focus();
```
