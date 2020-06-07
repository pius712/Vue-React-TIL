# State

The data you store in Vuex follows the same rules as the data in a Vue instance.  

## state란?

* 여러 컴포넌트 간에 공유할 데이터 - 상태  
Vuex에 저장하는 데이터는 뷰 인스턴스에 저장하는 데이터와 같은 규칙을 따른다. 

```javascript
// Vue
data:{
    message: 'Hello vue.js'
}
// Vuex
state:{
    message: 'hello vue.js'
}
```

```HTML

<!-- Vue -->
<p> {{ message }} </p>

<!-- Vuex-->
<p> this.$store.state.message </p>
```  

## Getting Vuex State into Vue Components

```javascript
// let's create a Counter component
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return store.state.count
    }
  }
}
```

위와 같은 방식으로 store에 접근하는 패턴은 state가 필요한 모든 컴포넌트에 store를 import 해야하는 상황이 생긴다. 

따라서, vuex는 `root component`에 옵션으로 `store`를 주면 모든 자식 컴포넌트가 store에 접근할 수 있도록 해준다. 

```javascript
const app = new Vue({
  el: '#app',
  // provide the store using the "store" option.
  // this will inject the store instance to all child components.
  store,
  components: { Counter },
  template: `
    <div class="app">
      <counter></counter>
    </div>
  `
})
```

실제 디렉토리 구조는 아래와 같다. 

```javascript
//main.js
import Vue from 'vue';
import App from './App.vue';
import { store } from './store/store.js';

Vue.config.productionTip = false

new Vue({
  store,
  render: h => h(App),
}).$mount('#app')
```

이렇게 설정을 해주면 하위의 컴포넌트에서 아래와 같이 접근이 가능하다. 

```javascript
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}
```

물론 `computed` 속성을 사용하지 않고 템플릿에서 `this.$store.state.coun`로 접근이 가능하나, 