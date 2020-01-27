# HOC vs Mixin

## HOC

A higher-order component (HOC)는 리액트 넘어온 컴포넌트 로직을 재사용하기 위한 기술이다. API가 아니라 하나의 패턴이다.  
정확히는, HOC은 새로운 컴포넌트를 리턴해주는 함수이다.

즉, 컴포넌트의 코드를 재사용하는 것이다. 공통적으로 사용되는 컴포넌트의 코드를 상위 컴포

###

// 재사용할 인스턴스(컴포넌트) 옵션들이 들어갈 자리
// el :'',
// data: '',
// components:{
// },
// creatd(){
// }

```js
import ListView from './ListView.vue';

export default function createListView(name) {
  return {
    name: name,
    render(createElement) {
      return createElement(ListView);
    },
  };
}
```
