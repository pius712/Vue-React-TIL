# Filter

text 포맷팅을 위해서 filter라는 기능을 사용할 수 있다.  
filter는 `{{ }}` 콧수염 괄호와 `v-bind`에서 사용할 수 있다.  
filter는 `|` 를 통해서 여러 개를  연결할 수 있다. 

## 로컬 필터

```html
<div>{{ message | capitalize }}</div>
```
컴포넌트의 옵션으로 filter를 등록하여 사용
```js
  filter: {
    capitalize(value ){
      if(!value)
        return ;
      value.toString(); 
      // 생략
    }
  }
```
## 전역 필터

아래와 같이 전역으로 필터를 등록할 수도 있다. 

```js
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

new Vue({
  // ...
})
``` 

## filter chaining

아래와 같은 방식으로 filter를 체이닝할 수 있다. 
` {{message | filter1 | filter2}} `
필터의 경우 인자로 이 전의 결과값을 받는다. 즉, filter1에서 message를 대문자로 변경했으면, 그 대문자로 변경된 값을 filter2 함수의 인자로 받게 된다. 