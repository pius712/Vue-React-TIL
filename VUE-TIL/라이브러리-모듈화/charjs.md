# Chart.js

1. NPM 설치
2. 설치된 라이브러리 import
3. mounted()에서 차트 사용.
4. 차트 컴포넌트화
5. 컴포넌트의 플러그인 화
6. 컴포넌트 통신을 이용한 차트 컴포넌트 기능 결합.

## mounted()

아래와 같이 DOM객체를 접근하여 차트를 그리기 때문에, `mounted` 라이프 사이클을 이용해야 한다.

```js
var ctx = document.getElementById('myChart');
    var myChart = new Chart(ctx, {
      type: 'bar',
      // ...
```

## 플러그인화

src/plugins 디렉터리를 만든다.

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

## 컴포넌트 통신을 이용한 차트 컴포넌트 기능 결합

부모 컴포넌트에서 `methods`나 `life cycle hook` 에서 데이터를 받은 다음에, 하위의 차트 컴포넌트에 props를 통해서 데이터를 넘겨주는 방식으로 차트 컴포넌트를 다른 컴포넌트와 통신을 하여, 기능을 결합할 수 있다.

```html
//parent component
<template>
  <div id="app">
    <bar-chart :datasets="chartDataSets"></bar-chart>
  </div>
</template>

<script>
  import BarChart from './components/BarChart';
  import LineChart from './components/LineChart';
  export default {
    name: 'app',
    data() {
      return {
        chartDataSets: [],
      };
    },
    components: {
      BarChart,
      LineChart,
    },
    created() {
      axios
        .get('../..')
        .then(res => {
          this.chartDataSets = res.data;
        })
        .catch(err => {
          console.error(err);
        });
    },
  };
</script>
```

```html
//bar-chart.vue
<template>
  <div>
      <canvas ref="barChart" id="myChart"></canvas>
  </div>
</template>

<script>
export default {
  props: ['datasets'],
  mounted() {
    var ctx = this.$refs.barChart;
    var myChart = new this.$_Chart(ctx, {
      data: {
        datasets: this.datasets,
      },
      //...
```
