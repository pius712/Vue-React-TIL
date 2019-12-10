## 1. ESSENTITAL

### declarative rendering

```html
<body>
    <div id="app">
        {{ message }}
    </div>
    <div id="app-2">
            <span v-bind:title="???">
              Hover your mouse over me for a few seconds
              to see my dynamically bound title!
            </span>
          </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el: '#app',
            data: {
                message: 'hello vue'
            }
        })

        var app2 = new Vue({
        el: '#app-2',
        data: {
            message: 'You loaded this page on ' + new Date().toLocaleString()
        }
        })
    </script>  
</body>
```

위의 Vue 인스턴스와 message 사이에 바인딩이 일어남.

아래의 경우에는 `v-` 라는 디렉티브(directive)를 통해서 속성을 바인딩 해주었다. 렌더링된 DOM에 reactive behavior을 적용시켜준다. 

## Conditionals and Loops
### Conditionals 
DOM의 structure을 바인딩 할 수도 있다.  `v-if=" "`
``` html
<div id="app-3">
  <span v-if="seen">Now you see me</span>
</div>
```
```javascript
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```

### Loops
배열에 있는 데이터를 사용해서 아이템의 리스트를 보여줄때 사용한다. `v-for=" "` 
```html
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```
 ```javascript
 var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Learn JavaScript' },
      { text: 'Learn Vue' },
      { text: 'Build something awesome' }
    ]
  }
})
```

## Handling User Input
### listener 
`v-on:event= " "` 방식으로 v-on 디렉티브를 사용한다. 이벤트 리스너를 달아서 vue 인스턴스의 methods를 invoke한다. 
```html
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Reverse Message</button>
</div>
```
```javascript
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```
### two-way binding
`v-model=` two-way binding. input과 app state의 message를 바인딩 해준다. 

```html 
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
```
```javascript
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```

## Composing with Components


### Vue Instance
Vue의 인스턴스 생성  // MVVM 패턴에 영향을 받았다고 한다.  
Vue의 인스턴스 생성시에는 옵션 객체가 들어간다.   
모든 Vue Components는 vue instance이다. 
```javascript
var vm = new Vue({
  // options
})
```

### Data and Methods 
Vue의 인스턴스가 생성되면 뷰 인스턴스 내 옵션의 데이터 값은 Vue’s reactivity system에 등록이 된다. 그래서 그 데이터 값이 바뀌면  view가 업데이트 되면서 새로 업데이트된 값과 매칭이 된다. 
```javascript
// Our data object
var data = { a: 1 }

// The object is added to a Vue instance
var vm = new Vue({
  data: data
})

// Getting the property on the instance
// returns the one from the original data
vm.a == data.a // => true

// Setting the property on the instance
// also affects the original data
vm.a = 2
data.a // => 2

// ... and vice-versa
data.a = 3
vm.a // => 3
```

인스턴스를 생성할 때, 없던 값은 reactive하지 않아서 view가 변하지 않는다. 
```javascript
vm.b = 'hi'
```
위와 같은 코드를 작성한다고 하여도 b 값의 변화가 view의 업데이트에 아무런 트리거가 되지 않는다.  만약에 필요하다면 선언하고 초기화해야한다.
```javascript
data: {
  newTodoText: '',
  visitCount: 0,
  hideCompletedTodos: false,
  todos: [],
  error: null
}
```

## Template Systax

### Interpolation

* text  
가장 간단한 방법으로 {{ }} 콧수염 괄호를 통해서 데이터 바인딩을 하는 것이다. data 객체의 값이 바뀌면 업데이터 된다.  
```html
<span>Message: {{ msg }}</span
```
* raw html  
* Attribute  
HTML의 attribute를 위해 `v-bind`가 사용된다. 이렇게 작성시 실제 html에서는 `<p id=pius>` 로 나온다. 

```html
<body>
    <div id="app">
       <p v-bind:id="pius712">hello</p> 
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el : '#app',
            data : {
                pius712: 'pius' 
            }
        })
    </script>
</body>
```
boolean 형의 attribute는 위와는 다르게 동작한다. 아예 `<button>` 태그에서 없는 속성으로 처리된다.
```html
<body>
    <div id="app">
       <button v-bind:disabled="isButtonDisabled">Button</button>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el : '#app',
            data : {
                isButtonDisabled: false,
            }
        })
    </script>
</body>
```
### Directives
`v-` 전치사가 붙는 속성을 디렉티브라고 한다. 

- Argument  
    몇몇 디렉티브는 argument를 가진다. `v-bind`가 대표적인 예이다. 
    ```html
    <a v-bind:href="url"> ... </a> 
    ```
    위의 경우에는 href가 argument이다. 또 다른 예는 `v-on`이 있다. 
    ```html
    <a v-on:click="doSomething"> ... </a>
    ``` 
    위의 경우에는 click이 arugment이고 주로 이벤트에 관한 내용이 속한다. 

### Shorthands  
* `v-bind` shorthand
    ```html
    <!-- full syntax -->
    <a v-bind:href="url"> ... </a>

    <!-- shorthand -->
    <a :href="url"> ... </a>

    <!-- shorthand with dynamic argument (2.6.0+) -->   
    <a :[key]="url"> ... </a>
    ```
* `v-on` shorthand
    ```html
    <!-- full syntax -->
    <a v-on:click="doSomething"> ... </a>

    <!-- shorthand -->
    <a @click="doSomething"> ... </a>

    <!-- shorthand with dynamic argument (2.6.0+) -->
    <a @[event]="doSomething"> ... </a>
    ```