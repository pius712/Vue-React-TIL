# Immer

Immer는 게르만어로 always라는 뜻으로, state의 불변성을 조금 더 편리한 방법으로 지킬 수 있도록 해주는 패키지이다. 

currentState에 대한 draftState를 만들어서 제공한다. 그리고 draftState를 변경하게 되는데 변경을 완료하면,
Immer가 draft state의 변경 부분을 바탕으로 nextState를 만들어낸다.  

## install

`npm i immer`

```js
import produce from 'immer';
```

## Example

```js
import produce from "immer"

const baseState = [
  {
    todo: "Learn typescript",
    done: true
  },
  {
    todo: "Try immer",
    done: false
  }
]
const nextState = produce(baseState, draftState => {
    draftState.push({todo: "Tweet about it"})
    draftState[1].done = true
})
```

Immer는 baseState는 건들지 않고, nextState는 새로운 immutable tree가 되는데, drfatState에 가해진 변경 사항들을 반영하고 있다.

