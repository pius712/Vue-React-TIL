# State

컴포넌트를 작성할 때, 이 데이터를 state에 저장할지 혹은 그냥 변수로 저장할지 고민이 될 때가 있다.

간단하게는 이 데이터가 전체적인 데이터 흐름에 포함이 된다면 state로 저장하는 것이 좋다.
하지만, 그것이 아니라면 일반 변수로 저장해도 좋다.

예를들어, timerID에 대해 생각해보자.
```js
const Component = () =>{
  // const [timerID, setTimerID] = useState(null);
  const timerID = null;
  useEffect(()=>{
    timerID = setInterval(()=>{
      this.tick();
    }, 1000)
    return () =>{
      clearInterval(timerId);
    }
  })
}
```
타이머가 이 컴포넌트의 전체적인 데이터 흐름과 크게 관련이 있을까?  
타이머 앱이 아니라면 state로 저장할 필요는 없다. 