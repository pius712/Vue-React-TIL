# react-spring

리액트 스프링은 react-motion 처럼 리액트에서 애니메이션을 처리할 수 있도록 해주는 라이브러리이다.

리액트 스프링은 5개의 hooks로 구성되어 있다.
우선 react-spring은 spring physics(축 법칙)을 기반으로 만든 라이브러리로 익히 알고 있는 그 스프링을 뜻한다.
css에서 opacity : 0 -> opacity :1 로 스프링이 튀어가듯 바뀌는 것을 의미한다.

기존에는 duration을 주고 이를 이용해서 animation을 구현했는데, 여기서 생기는 문제점들이 많았다. 가령, 모든 모션에 싱크를 맞추어야 한다던가, 사용자가 애니메이션을 끝까지 보지 않고 올라갈 때 time을 다시 맞춰서 올라간다던가 하는 문제를 해결할 수 있다.

- `useSpring` a single spring, moves data from a -> b
- `useSprings` multiple springs, for lists, where each spring moves data from a -> b
- `useTrail` multiple springs with a single dataset, one spring follows or trails behind the other
- `useTransition` for mount/unmount transitions (lists where items are added/removed/updated)
- `useChain` to queue or chain multiple animations together

## useSpring

하나의

스크롤을 내리다가 일정수준 이상 내려가게 되면 색이 바뀌는 header를 구현할 것이다.

우선 아래와 같이, scroll event에 대한 처리를 해주어야한다.
useEffect는 컴포넌트가 리렌더링되는 시점에서도 호출되기 때문에, 이 과정에서 모두 unsubscribing을 해줘야 성능저하를 잡고, 버그를 일으키지 않는다.

```js
useEffect(() => {
  window.addEventListener("scroll", handleScroll);
  return () => {
    window.addEventListener("scroll", handleScroll);
  };
});
```

처음에는 isReversed는 `false` 값을 가지고 있다. 만약 reversed되지 않은 상태에서, yOffset이 200을 넘으면,
isReversed 값을 true로 준다.  
만약, 위로 다시 돌아온다면, reversed 상태인데 `0<yOffset<200` 이기때문에, false를 준다.

만약 isReversed 값을 주지 않는다면, 스크롤을 할 때마다 handleScroll의 모든 로직을 수행하기 때문에, 아래의 set함수를 많이 호출하게 되고 버그가 발생한다.

아래의 set함수는 spring 효과를 만들어주는 함수이다.
이를 통해 만들어진 값을 `animated`로 래핑된 엘리먼트에 style로 넣어주면 애니메이션이 동작한다.

y가 200 이하일때는, defaultHeader를 opacity(불투명도)를 0 -> 1로 애니메이션 효과를 주고, reverseHeader는 1->0을 주어 투명하게 만들어준다.  
그 이상일때는, 반대로 진행한다.

```js
const handleScroll = useCallback(() => {
  if (isReversed && 0 <= window.pageYOffset && window.pageYOffset < 200) {
    setIsReversed(false);
    setDefaultHeader({ opacity: 1 });
    setReveredHeader({ opacity: 0 });
  } else if (!isReversed && 200 < window.pageYOffset) {
    setIsReversed(true);
    setDefaultHeader({ opacity: 0 });
    setReveredHeader({ opacity: 1 });
  }
});
```

아래는 useSpring을 통해서 spring의 값과 set함수를 가져왔다.  
처음에 빈 객체를 던져준 이유는, 처음부터 값을 넣어두면 해당 spring 효과를 렌더링하자마자 주기 때문이다.

```js
const [defaultHeader, setDefaultHeader] = useSpring(() => ({}));
const [reversedHeader, setReveredHeader] = useSpring(() => ({}));
```

animated로 wrapping된 함수에 style로 값을 넣어준다.

```jsx
<>
  <animated.div style={defaultHeader}>
    <div className="default">
      <div className="logo"></div>
    </div>
  </animated.div>
  <animated.div style={reversedHeader}>
    <div ref={reverseRef} className="reverse">
      <div className="logo"></div>
      <button className="btn btn-download"></button>
    </div>
  </animated.div>
</>
```

## 결과물

```js
import React, { useState, useCallback, useEffect, useRef } from "react";
import { useSpring, animated } from "react-spring";

import "./Header.scss";
const Header = () => {
  const [isReversed, setIsReversed] = useState(false);
  const [defaultHeader, setDefaultHeader] = useSpring(() => ({}));
  const [reversedHeader, setReveredHeader] = useSpring(() => ({}));
  const reverseRef = useRef(null);
  const handleScroll = useCallback(() => {
    if (isReversed && 0 <= window.pageYOffset && window.pageYOffset < 200) {
      setIsReversed(false);
      setDefaultHeader({ opacity: 1 });
      setReveredHeader({ opacity: 0 });
    } else if (!isReversed && 200 < window.pageYOffset) {
      setIsReversed(true);
      setDefaultHeader({ opacity: 0 });
      setReveredHeader({ opacity: 1 });
    }
  });
  useEffect(() => {
    window.addEventListener("scroll", handleScroll);
    return window.addEventListener("scroll", handleScroll);
  });

  return (
    <header className="header-component">
      <section className="content">
        <animated.div style={defaultHeader}>
          <div className="default">
            <a href="/">
              <div className="logo"></div>
            </a>
          </div>
        </animated.div>
        <animated.div style={reversedHeader}>
          <div ref={reverseRef} className="reverse">
            <a href="/">
              <div className="logo"></div>
            </a>
            <button className="btn btn-download"></button>
          </div>
        </animated.div>
      </section>
    </header>
  );
};

export default Header;
```
