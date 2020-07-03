# List Rendering

처음에 배울때, `map()` 함수를 사용하면 된다는 것을 배웠는데, `Array.map()` 의 결과는 배열을 반환한다. 


```js
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number) => number * 2);
console.log(doubled);
//  [2, 4, 6, 8, 10]
```

처음에는 각각의 값들을 한번씩 만드는줄 알았는데...

```js
const numbers = [1, 2, 3, 4, 5];

ReactDOM.render(
  <ul>{number.map(number=> 
    <li>{number}</li>)}</ul>,
  document.getElementById('root')
);
```

위의 방식은 아래와 같다. 

```js
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
```

```js
ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);
```

위와 같이 배열을 태그 안에 넣으면 렌더링이 되는 것이다.

