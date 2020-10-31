# React Component , Instance



**들어가기전에 용어정리**

`return <div />` --> host element
`return <Component/>` --> composite element



```js
class FormComponent extends Component {
  /* ... */
}

function FormComponent () {
  /* ... */
}

class App extends Component{
  render() {
    return <FormComponent />
  }
}
```

App 컴포넌트의 return 하는 Element는 정확히 무엇을 말하는 것일까?

`ReactDOM.render(<App />, rootEl);` index에는 이러한 함수를 호출한다. 

이 함수에서 <App /> 은 객체로 대체되는데, 이것이 정확히 어떻게 동작할까?

## Summary

리액트에서 <Component/> 는 정확히 무엇을 뜻하는 건가? 

먼저 아래의 내용을 요약하자면, 

1. 함수형 컴포넌트는 함수를 호출하는 것이다.  
2. 클래스 컴포넌트는 클래스 컴포넌트로 객체를 만드는 것이다. 정확히는 해당 클래스의 인스턴스는 아니고, 해당 클래스의 객체 정보를 가지고 있는 객체이다. 이는 팩토리 패턴을 사용해서 팩토리 클래스를 통해서 만들어지는 것으로 아래에서 다시 설명한다. 

그리고 state, props가 업데이트 될 때는 어떻게 되는 것인가?

1. 함수 컴포넌트는 함수를 다시 호출한다. 
2. 클래스 컴포넌트는 위의 팩토리 클래스를 통해 만들어진 인스턴스가 업데이트된 데이터를 가지고 user-defined 컴포넌트 클래스 인스턴스의 `componentWillUpdate`를 호출한다.  


## 함수 호출, 인스턴스화 ?

함수형 컴포넌트는 정확히 해당 함수를 호출하는 것이라고 볼 수 있다. 


클래스 컴포넌트는 처음에 이 클래스를 인스턴스화 하고, `componentWillMount()`을 호출하게 된다.

즉, 아래의 컴포넌트는 ReactDOM.render() 함수가 호출되면서, App 아래의 컴포넌트들을 재귀적으로 돌면서 함수형 컴포넌트는 `FormComponent()`를 호출하고, 클래스형 컴포넌트는 `new FormComponent(props)`로 인스턴스화 되고, 라이프 사이클에 의해서 componentWillMount가 호출되는 것이다. 

```js
class FormComponent extends Component {
  /* ... */
}

```

이렇게 만들어진 App element는 리액트의 Reconciler에 의해서 렌더링 된다.


## Rerendering? 

리렌더링 과정을 알기 위해서는, 컴포넌트 클래스가 어떻게 마운팅 되는지를 먼저 알아야한다. 

### Mount

만약 리렌더링 되고 난 후에는 어떻게 될 것인가? 다시 함수 호출, 인스턴스화?

`ReactDOM.render()` 기본적으로 이 함수는 초기 mount할 때, Element Tree"만" 만드는 함수이다.

reconciler는 `CompositeComponent` `DOMComponent` 클래스를 만들어서 이를 업데이트하려고 한다.

아래는 컴포넌트를 초기화하는 메서드의 슈도 코드이다.
```js
function instantiateComponent(element) {
    if(User-defined components) 
    return new CompositeComponent(element);
  } else if (Platform-specific component) {
    return new DOMComponent(element);
  }  
}
```

우선, 아래의 클래스를 인스턴스화 하는 것은 유저가 만든 컴포넌트 클래스의 인스턴스와는 다른 인스턴스이다.
팩토리 패턴을 이용해서 새로운 객체를 만들어낸다. 아래는 팩트리 클래스이다.

아래의 `“public instance”`는 유저가 정의한 클래스의 인스턴스이다. 
```js
class CompositeComponent {
  constructor(element) {
    this.currentElement = element;
    this.renderedComponent = null;
    this.publicInstance = null;
  }

  getPublicInstance() {
    // For composite components, expose the class instance.
    return this.publicInstance;
  }

  mount() {
    var element = this.currentElement;
    var type = element.type;
    var props = element.props;

    var publicInstance;
    var renderedElement;
    if (isClass(type)) {
      // Component class
      publicInstance = new type(props);
      // Set the props
      publicInstance.props = props;
      // Call the lifecycle if necessary
      if (publicInstance.componentWillMount) {
        publicInstance.componentWillMount();
      }
      renderedElement = publicInstance.render();
    } else if (typeof type === 'function') {
      // Component function
      publicInstance = null;
      renderedElement = type(props);
    }

    // Save the public instance
    this.publicInstance = publicInstance;

    // Instantiate the child internal instance according to the element.
    // It would be a DOMComponent for <div /> or <p />,
    // and a CompositeComponent for <App /> or <Button />:
    var renderedComponent = instantiateComponent(renderedElement);
    this.renderedComponent = renderedComponent;

    // Mount the rendered output
    return renderedComponent.mount();
  }
}
```

아래는 `ReactDOM.render()` 함수의 정이이다.

컴포넌트 클래스를 인스턴스화 해서, mount 시킨다.

```js
function mountTree(element, containerNode) {
  // Create the top-level internal instance
  var rootComponent = instantiateComponent(element);

  // Mount the top-level component into the container
  var node = rootComponent.mount();
  containerNode.appendChild(node);

  // Return the public instance it provides
  var publicInstance = rootComponent.getPublicInstance();
  return publicInstance;
}

var rootEl = document.getElementById('root');
mountTree(<App />, rootEl);
```

### Update

만약 데이터가 바뀔때, 컴포넌트를 모두 unmount 시키고, 다시 mount 시키는 것은 굉장히 비효율적인 일이 될 것이다.
이 부분이 "virtual DOM diffing”라고 하는 작업이다.

이 작업을 하기 위해서는 기존의 팩토리 클래스를 확장해야한다. 

```js
class CompositeComponent {
  // ...
  receive(nextElement) {
    // ...
  }
}
```

```js
function mountTree(element, containerNode) {
  // 기존의 트리를 검사한다. 
  if (containerNode.firstChild) {
    var prevNode = containerNode.firstChild;
    var prevRootComponent = prevNode._internalInstance;
    var prevElement = prevRootComponent.currentElement;

    // 만약 이전의 컴포넌트의 type와 새로운 컴포넌트의 type이 같다면 이를 재사용한다.
    if (prevElement.type === element.type) {
      prevRootComponent.receive(element);
      return;
    }
    // 그렇지 않은 경우에는 기존의 tree를 unmount시킨다.
    unmountTree(containerNode);
  }
  // ...
}
```
위와 같이 함수를 짜면 mountTree를 두번 호출해도, 해당 함수를 두번 호출해도 DOM 전체를 unmount하고 mount하지 않고 기존의 DOM을 사용하게 된다.

```js
var rootEl = document.getElementById('root');

mountTree(<App />, rootEl);0
// Reuses the existing DOM:
mountTree(<App />, rootEl);
```


```js
class CompositeComponent {
  // ...
  receive(nextElement) {
    // 현재 Element의 프롭스와 이전의 프롭스
    var prevProps = this.currentElement.props;
    // 현재 들어온 인스턴스
    var publicInstance = this.publicInstance;
    var prevRenderedComponent = this.renderedComponent;
    var prevRenderedElement = prevRenderedComponent.currentElement;
    /* 
      <Button color="blue"> (current) -> <Button color="black"> (next)
      라고 할 때, 앞의 컴포넌트는 현재 화면에 보이는 컴포넌트인데 
      이를 아래의 연산을 위해서 prev 변수에 담고 있는 것이다. 
      위의 연산이 끝나고 난 다음에는  
      prevProp은 color="blue"이다. 
      var publicInstance --> 컴포넌트의 인스턴스
    */

    // Update *own* element
    this.currentElement = nextElement;
    var type = nextElement.type;
    var nextProps = nextElement.props;
    /* 
      next의 type(class or function)인지, nextProps(color="black")을 저장한다.
    */

    // Figure out what the next render() output is
    var nextRenderedElement;
    
    // 클래스 컴포넌트이면 ? 
    if (isClass(type)) {
      // 라이프 사이클이 있으면, 라이프사이클 함수(componentWillUpdate)를 호출한다. 
      if (publicInstance.componentWillUpdate) {
        publicInstance.componentWillUpdate(nextProps);
      }
      // 사용자 정의 컴포넌트 인스턴스의 프롭을 교체해준다. 
      publicInstance.props = nextProps;
      // 그 값을 통해서 렌더 함수를 호출한다. 
      // 인스턴스를 새로 만드는 것이 아니다. 사용자 정의 클래스 인스턴스를 가지고 있다가 
      // 그 안의 라이프 사이클 메서드를 바뀐 프롭으로 호출하는 것이다.
      nextRenderedElement = publicInstance.render();
    
    // 함수형 컴포넌트트이면 ?
    } else if (typeof type === 'function') {
      // 해당 함수를 다시 호출한다. 
      // 이는 타당한 것이, 함수 호출은 객체를 만들지 않는다. 
      nextRenderedElement = type(nextProps);
    }
    // ...

 ```

 Next, we can look at the rendered element’s type. If the type has not changed since the last render, the component below can also be updated in place.

For example, if it returned <Button color="red" /> the first time, and <Button color="blue" /> the second time, we can just tell the corresponding internal instance to receive() the next element:
```js
  // ...

  /* 
  만약 렌더링된 element의 type이 변하지 않았다면, 
  기존의 컴포넌트 인스턴스를 그대로 사용한다. 
   
   여기서 type이란 단순히 function/class 만을 뜻하는 것은 아니다. element type은 funciton component, class component, host element가 있기 때문에 <button> -> <input>이 되면 그 element를 다시 사용하지 않는다. (자세한 내용은 [reconcilation](https://reactjs.org/docs/reconciliation.html) 참고)
  */
  // If the rendered element type has not changed,
  // reuse the existing component instance and exit.
  if (prevRenderedElement.type === nextRenderedElement.type) {
    prevRenderedComponent.receive(nextRenderedElement);
    return;
  }

  // ...
```