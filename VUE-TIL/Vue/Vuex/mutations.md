mutations란?

* state의 값을 변경할 수 있는 유일한 방법이자 메서드
* 뮤테이션은 `commit()`으로 동작시킨다. 

```javascript
//store.js
state:{ num: 10},
mutationsL{
    printNumbers(state){
        return state.num
    },
    sumNumbers(state,anotherNum){
        return state.num + anotherNum;
    }
}
//App.vue
this.$store.commit('printNunmbers');
this.$store.commit('sumNumbers',20);
```

mutations의 commit() 형식

* state를 변경하기 위해 mutations를 동작시킬 때 인자(payload)를 전달할 수 있음. (-> payload는 임의의 관행적인 명이고, 객체이다.)

```javascript
// store.js
state: {
    storeNum:10
},
mutations:{
    modifyState(state, payload){
        console.log(payload.str);
        return state.storeNum += payload.num;
    }
}
// App.vue
this.$store.commit('modifystate',{
    str: 'passed from payload',
    num: 20
});

``` 

왜 state는 직접 변경하지 않고 mutations로 변경할까?  
여러 개의 컴포넌트에서 아래와 같이 state 값을 변경하는 경우 어느 컴포넌트에서 해당 state를 변경했는지 추적하기가 어렵다. 

```javascript
method:{
    increaseCounter(){ this.$store.state.counter++ }
}
```

특정 시점에 어떤 컴포넌트가 state를 접근하여 변경한 건지 확인하기 어렵기 때문  
따라서, 뷰의 반응성을 거스르지 않게 명시적으로 상태 변화를 수행. 반응성, 디버깅, 테스팅 혜택. 

왜 mutation은 commit을 통해서 ??  
-->  
