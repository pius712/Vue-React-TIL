# Rerendering

리렌더링을 잘 알기 위해서는, 컴포넌트의 LifeCycle에 대해서 알아야한다.
참고로 리렌더링은 컴포넌트 자체가 새로 생기는 것이 아니기 때문에, 내부의 state가 초기화되는 것이 아니다.

중간에 static 메서드들도 있지만 LifeCycle은 크게 아래와 구성된다. 

-  constructor
-  render
-  ComponentDidMount
-  ComponentDidUpdate
-  ComponentWillUnmount

우선 클래스가 렌더링되는 것은 해당 클래스가 인스턴스화되는 것이다. 
맨 처음으로 constructor가 실행이된다. 

그리고 render 함수가 실행이 된다. render 함수가 실행되면서, 컴포넌트는 react element로 바뀌게 된다. 
이 element의 Tree를 통해 DOM을 그릴 수 있게 된다. (이 트리가 이전의 트리와 다르면 교체되는 것을 Reconcile이라고 한다.)

ComponentDidMount 메서드는 이미 트리가 만들어진 상태이지만(컴포넌트가 마운트 된 상태), 아직 브라우저에 화면을 띄우기 전이다. 

ComponetDidUpdate 메서드는 컴포넌트가 업데이트되었을 때 실행된다.  
여기서 setState를 할 때는 유의해야한다. 만약 props가 바뀔때, 특정 state를 바꿔야하는 상황이라고 가정해보자.
아래와 같이 코드를 돌린다면 어떻게 될까? 

state의 값이 변경됨 -> 다시 componentDidUpdate 호출됨 -> ... -> 무한 반복된다.

```js
  componentDidUpdate() {
    setInterval(()=>{
      this.setState({
        users : [...this.state.users, this.state.count],
        count:  this.state.count + 1
      });
    },1000)
  }
```

## state, props

### 리렌더링/setState 타이밍

state, props가 바뀔 때, 라이프 사이클에 의하면 화면이 리렌더링이 된다.  
그렇다면 언제나 그런가?

아래의 경우 alert에서는 어떤 값이 찍히는가? 0이 찍한다. 
이는 리렌더링이 안되는 것이 아니라, setState는 본질적으로는 비동기로 처리된다는 뜻이다.
관련 로직들이 모두 소화된 다음에, state를 한꺼번에 업데이트하고 리렌더링이 되는 것이다. 

이를두고, state를 변경했는데, 리렌더링이 되면서 **초기화되었다고 생각하는 실수**를 하지말자.
비동기로 처리되어, 데이터가 아직 변경이 안된 것이고 데이터가 업데이트 되면서 리렌더링이 된다.

```js
const [count, setCount] = useState(0);
  const addCount = useCallback(()=>{
    setCount(count+1);
    showCount();
  },count)
  const showCount = useCallback(()=>{
    alert(count);
  },count)
```

반면 state가 아닌 일반 변수로 선언된 count를 사용할 경우에는 숫자 1이 찍히게 된다.
하지만, 이 경우에는 리렌더링이 일어나지 않는다. 

```js
let count = 0;
  const addCount = useCallback(()=>{
    count += 1
    showCount();
  })
  const showCount = useCallback(()=>{
    alert(count);
  })
```

### 왜 이전의 state가 나오나? 

state를 다룰때, 종종 state가 최신의 값이 아닌 경우가 있다. 
이 경우는 크게 두가지 이유 때문이다. 

- setState의 async
- hook dependency

1. setState의 async

위에서도 언급했듯, setState는 비동기적으로 처리된다.
이와 같은 특성은 아래의 코드에서 문제가 된다.

```js
const [count, setCount] = useState(0);
const [value, setValue] = useState(0);
const addValue = useCallback(()=>{
  alert(count, value);
  setValue(count);
},[count, value, setValue])
const addCount = useCallback(()=>{
  setCount((v) => v+1);
  addValue();
},[count, setCount, addValue])
```
이 경우에 addValue를 할 때, count를 이용하는데 count가 업데이트가 되지 않아서 addValue 호출 시에도 value는 0이 된다. 이 경우에는 ref, mutate 등을 사용하라고 한다. 
(결국 value 값을 바꾸기 때문에 mutate해도 상관이 없는 것이다.)

2. hook dependency


## state나 props가 아닌 데이터가 변할때도 화면이 리렌더링이 될까?

정답은 안된다. 


## useEffect


