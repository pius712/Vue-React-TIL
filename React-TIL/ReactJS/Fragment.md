# Fragment

컴포넌트가 여러 개의 엘리먼트를 반환할 때, 최상단에는 `<div></div>` 태그가 오기 마련이다. 하지만, 이 경우 아래와 같은 문제가 생길 수 있다. 

컴포넌트 : 
```js
class Table extends React.Component {
  render() {
    return (
      <table>
        <tr>
          <Columns />
        </tr>
      </table>
    );
  }
}

class Columns extends React.Component {
  render() {
    return (
      <div>
        <td>Hello</td>
        <td>World</td>
      </div>
    );
  }
}
```
결과: 
```html
<table>
  <tr>
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  </tr>
</table>
```

이를 해결해주기 위해서 Fragment가 필요한 것. 

해결법 

```js
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>
    );
  }
}
```

## 단축 문법

```js
class Columns extends React.Component {
  render() {
    return (
      <>
        <td>Hello</td>
        <td>World</td>
      </>
    );
  }
}
```