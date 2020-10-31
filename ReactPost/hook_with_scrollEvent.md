# Hook with Scroll Event

함수형 컴포넌트에서 useEffect를 사용할 때, 클로저 문제가 생기는 일이 꽤나 빈번하다.

## useEffect

useEffect 훅은 라이프 사이클과 관련이 있다.  
`componentDidMount`, `compontDidUpdate`, `componentWillUnmount`를 합쳐놓은 것으로도 볼 수 있다.

그렇기 때문에, 이 곳에서 data fectching, subscription과 같은 작업을 진행할 수 있다.

### dependency

dependency는 useEffect의 두번째 인자로 배열 형태로 들어가게 된다.
eslint-plugin-react-hooks을 사용하게 되면, 이 에러가 발생하게 되는데 왜 dependency를 적어줘야하는지 알아볼 것이다. 

**성능 최적화**  
dependency를 적는 이유는 성능 최적화를 위해 하는 것이다. 
subscribe나 data fetching등을 작업할 때, class component에서는 라이프 사이클 메서드를 이용하면 된다.
그리고 기본적으로 subscribe 작업은 `componetDidMount`, `componentWillUnmount` 같은 곳에서 진행되기 때문에 크게 문제가 없다. 하지만 아래의 `useEffect` 코드를 보자.

```js
useEffect(()=>{
  window.addEventListener('scroll', handleScroll);
  return () =>{
    window.removeEventListener('scroll', handleScroll);
  }
})
```
위의 코드는 윈도우 객체에 스크롤 이벤트를 붙이는 작업을 한다. 기본적으로 state, props가 변하면 useEffect 함수가 실행되는데, 컴포넌트에서 어떤 state, props가 변할때마다 해당 함수의 작업이 수행된다.  
이는 성능저하로 이어질 것이다. (state나 props가 빠르게 변한다면 더더욱 그렇다)

최악의 경우를 가정해서 코드를 작성해보자.

```js
const [scrollTop, setScrollTop] = useState(0);

const handleScroll = useCallback(()=>{
  setScrollTop(window.pageYOffset);
}) 
useEffect(()=>{
  window.addEventListener('scroll', handleScroll);
  return () =>{
    window.removeEventListener('scroll', handleScroll);
  }
})
```
스크롤이 돌때마다 scrollTop이 변하고, 계속해서 useEffect가 변하게 될 것이다.  

만약 mount, unmount에만 실행시키고 싶다면 빈 배열(`[]`)을 넣어주면 된다. 이는 아무런 값에 의존하지 않기 때문에, 상태값이나 프롭스가 바뀐다고 하여도 실행되지 않는다.



