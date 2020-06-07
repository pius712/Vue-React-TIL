# Plugin

## Using Plugin

## Writing Plugin

플러그인에는 `install` 메서드가 있어야 한다.  
이 메소드는 가능한 옵션과 함께 Vue 생성자를 첫 번째 인수로 사용하여 호출된다.

```js
MyPlugin.install = function (Vue, options) {
  // 1. add global method or property
  Vue.myGlobalMethod = function () {
    // some logic ...
  }

  // 2. add a global asset
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // some logic ...
    }
    ...
  })

  // 3. inject some component options
  Vue.mixin({
    created: function () {
      // some logic ...
    }
    ...
  })

  // 4. add an instance method
  Vue.prototype.$myMethod = function (methodOptions) {
    // some logic ...
  }
}
```

### 예시 코드

```js
// plugins/ChartPlugin.js
import Chart from 'chart.js';

export default {
  install(Vue) {
    Vue.prototype.$_Chart = Chart;
  },
};
```

```js
//main.js
import ChartPlugin from './plugins/CharPlugin';
Vue.use(ChartPlugin);
```

```js
//components/BarComponent.vue
export default {
  mounted() {
    var ctx = this.$refs.barChart;
  },
};
```
