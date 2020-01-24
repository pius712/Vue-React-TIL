- Reactivity
- 인스턴스  
- 컴포넌트 
- 컴포넌트 통신  
    - props 
    - event emit
- HTTP 통신 라이브러리(axios)
- 템플릿 문법 
    - 데이터 바인딩 
    - 뷰 디렉티브(v-)
- Vue CLI 
- 싱글 파일 컴포넌트 

vue 공식 문서 : https://vuejs.org/v2/guide/index.html (원문) <br/>
vue 스타일 가이드 : https://vuejs.org/v2/style-guide/ (한국어) <br/>


instance 


자바스크립트의 비동기 처리 패턴 
1. callback
2. promise
3. promise  + generator
4. async & await


## template 

### data binding

* {{}} - 콧수염 괄호

``` html
<body>
    <div id="app">
        <p>{{ num }}</p>
        <p> {{ doubleNum }} </p>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el: '#app',
            data: {
                num: 10,
            },
            computed: {
                doubleNum: function(){
                    return this.num * 2;
                }
            }
        })
    </script>
</body>
```
* v-  디렉티브

```html
<body>
    <div id="app">
        <p v-bind:id = "uuid" v-bind:class = 'name'>{{ num }}</p>
        <!-- <p id = "abc1234" class = "bluehall"></p> -->
        <p id="abc1234"> {{ doubleNum }} </p>
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        new Vue({
            el: '#app',
            data: {
                num: 10,
                uuid: 'abc1234',
                name: 'bluehall'
            },
            computed: {
                doubleNum: function(){
                    return this.num * 2;
                }
            }
        })
    </script>
</body>
```




### computed

### watch


### computed vs watch

computed의 경우에는 단순 값.
watch의 경우에는 데이터 요청과 같은 무거운 연산.


## Vue CLI

template 안에는 하나의 div만..

data 속성  
```javascript
new Vue({
  data: {
    str:'hi'
  }
})

<script>
export default {
  data: function(){
    return {
      str: 'hi'
    }  
  }
}
```
### command 

`vue create '프로젝트 폴더 위치'`

* 싱글 파일 컴포넌트 코드 단축키 

`srf` 

```html
<template>
    
</template>
    <!-- HTML -->
<script>
export default {
    // javascript -인스턴스 옵션
}
</script>

<style>
    /* CSS */
</style>

```


```html 
<template>
    <header>
        <h1>{{ propsdata }}</h1>
        <button v-on:click="sendEvent">send</button>
    </header>
</template>

<script>
export default {
    props :['propsdata'],
    methods: { 
        sendEvent: function(){
            this.$emit('renew');
        }

    }
}
</script>

<style>
```
여기서 emit renew 이벤트를 올리면 첫 번째로 이 컴포넌트를 추가한 상위 컴포넌트의 태그에 전달된다. 

```html
<template>
  <div>
    <!-- <app-header v-bind: 프롭스 속성 이름 = "상위 컴포넌트의 데이터 이름"></app-header> -->
    <app-header v-bind:propsdata ="str" v-on:renew="renewStr"></app-header>
    <!-- <app-header ></app-header> -->
  </div>
 
</template>


<script>
import AppHeader from './components/AppHeader.vue'

export default {
  data: function(){
    return {
      str: 'header'
    }  
  },
  components: {
    'app-header' : AppHeader,
  },
  methods:{
    renewStr: function(){
      this.str = 'hi';
    }
  }
}
</script>
```

상위 컴포넌트인 App에서 위와 같이 v-on:renew 로 받아준다. 


```javascript
export default {
  data: function(){
    return {
      str: 'header'
    }  
  },
```
이렇게 데이터를 객체를 리턴하는데 컴포넌트간에 데이터가 참조되지 않도록 새 객체를 리턴하는 방식으로 function을 만들어준다. 
### 파일 구조 
 
public 폴더의 

#### Components

최소 두 단어 이상을 조합. 기존의 html의 태그와 혼동되면 안된다. 
ex) AppHeader 


component 템플릿 구조 

cdn 
```javascript
var my_cmp = {
  template: '<div>my component</div>'
}

new Vue({
  el:'',
  components: {
    // '컴포넌트 이름': '컴포넌트 내용'
    'my-cmp' : my_cmp,
  }
})
```

cli 
```javascript
<script>
import TodoHeader from './components/TodoHeader.vue'

var my_cmp = {
  template: '<div>my component</div>'
}


export default {
  components:{
    'TodoHeader': TodoHeader,
  }
}
</script>
```

```html
<style scoped>

</style>
```

scoped 사용하면 캐스캐이딩 되지 않고 컴포넌트 내에서만 적용이 된다. 

v-model 
input에 입력된 값을 동적으로 인스턴스 안에 맵핑하는 역할. 
```html
<template>
    <div>
        <input type="text" v-model="newTodoItem">
        <button v-on:click="addTodo">add</button>
    </div>
</template>

<script>
export default {
    data: function(){
        return {
            newTodoItem: ""
        }
    },
    methods:{
        addTodo: function(){
            console.log(this.newTodoItem);
            localStorage.setItem(this.newTodoItem, this.newTodoItem);
            this.newTodoItem = "";
        }
    }
}
</script>

```
`storage.setItem(keyName, keyValue);`

network 탭에서 Application 누르면 local storage에 나온다.

### life cycle
* created
``` html
<script>
export default {    
    created: function(){
        console.log('created');
    }  // instance 생성시 바로 불러지는 ..!!;
}
</script>
```
var obj = {};
JSON.stringify();


splice()
https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/splice