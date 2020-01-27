# Mixin

Mixins는 Vue 컴포넌트에 재사용 가능한 기능을 배포하는 유연한 방법입니다. mixin 객체는 모든 구성 요소 옵션을 포함할 수 있습니다. 컴포넌트에 mixin을 사용하면 해당 mixin의 모든 옵션이 컴포넌트의 고유 옵션에 “혼합”됩니다.

## HOC의 단점

## 예시 코드

아래가 믹스인 코드인데, `computed`, `created()`가 NewsView, AskView,JobsView에서 공유되고 있는 부분을 가져온 것이다.

```js
//mixins/ListMixin.js
import bus from '../utils/bus';
export default {
  // 재사용할 컴포넌트 옵션
  computed: {
    list() {
      return this.$store.state.list;
    },
  },
  created() {
    bus.$emit('start:spinner');
    // setTimeout(() => {

    // }, 3000);
    this.$store
      .dispatch('FETCH_LIST', this.$route.name)
      .then(result => {
        bus.$emit('end:spinner');
        console.log('result');
        console.log(result);
        return result;
      })
      .catch(err => {
        console.error(err);
      });
  },
};
```

이를 아래와 같이 `mixins` 라는 키를 만들고 넣으면 된다.

```js
//pages/NewsView | AskView | JobsView
// template 생략
//...
import ListMixin from '../mixins/ListMixin';
export default {
  components: {
    ListItem,
  },
  mixins: [ListMixin],
};
// style 생략
```
