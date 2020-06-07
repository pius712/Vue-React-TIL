# List Rendering

* Mapping an Array to Elements with v-for
* v-for with an Object
* Maintaining State
* Array Change Detection
* Object Change Detection Caveats

## Mapping an Array to Elements with v-for

`v-for` 디렉티브를 사용하면 배열 기반의 아이템들을 렌더링할 수 있다. `item in items`에서 `items`는 데이터 소스(배열)를 가리키고 `item`은 배열의 개별 엘리먼트들을 가리킨다. 그리고 `index`를 통해서 배열의 인덱스를 나타낼 수 있다.  

```HTML
<ul id="example-1">
  <li v-for="(item,index) in items">
    {{ item.message }} - {{ index }}
  </li>
</ul>
```

```javascript
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: [
      { message: 'Foo' },
      { message: 'Bar' }
    ]
  }
})
```

```bash
* Foo - 0
* Bar -1

```

## v-for with an Object

객체의 경우 `(value, key) in object`와 같은 방식으로 사용이 가능하다.

___
**note**  
객체를 반복해서 돌때는 그 순서가 `Object.keys()`에 의해서 정해지기 때문에 항상 보는 것처럼 나오지는 않는다. {100: 'a'. 2:'b'} 의 경우에는 ['2', '100'] 순서로 나올 수 있다. 

__

```HTML
<div v-for="(value, key) in object">
  {{ key }}: {{ value }}
</div>
```

```js
new Vue({
  el: '#v-for-object',
  data: {
    object: {
      title: 'How to do lists in Vue',
      author: 'Jane Doe',
      publishedAt: '2016-04-10'
    }
  }
})
``` 

## Maintaing State

## Array Change Detection

### Caveats 

* 배열을 인덱스로 접근하여 수정할 경우 reactivity하지 않을 수 있다.  
e.g. `vm.items[indexOfItem] = newValue

```js
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x' // is NOT reactive
```

이를 해결하기 위해서는 You can also use the vm.$set instance method, which is an alias for the global Vue.set:

```js
import Vue from 'vue';

// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
//위의 경우는 스크립트로 할때, 아래는 모듈로 나눴을때. 
Vue.set(this.items, indexOfItem, newValue)
//or
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
// or vm.$set
vm.$set(vm.items, indexOfItem, newValue)
//위의 경우는 스크립트로 할때, 아래는 모듈로 나눴을때.
this.$set(this.items, indexOfItem, newValue)
```
