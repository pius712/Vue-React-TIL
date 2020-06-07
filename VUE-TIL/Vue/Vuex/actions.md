# actions

## 비동기. 

```js
//store.js
mutations:{
    addCounter(state){
        state.counter++;
    },
},
actions:{
    delayedAddCounter(context){
        setTimeout(()=>{
            context.commit('addCounter')
        },2000);
    }
}
//App.vue
methods:{
    incrementCounter(){
        this.$stroe.dispatch('delayedAddCounter');
    }
}
```

```js
//store.js
mutations:{
    setData(state, fetchedData){
        state.product = fetchedData;
    }
},
actions:{
    fetchProductData(context){
        return axios.get('https://domain.com/products/1')
                    .then(response=>context.commit('setData',response));
    }
}
//app.js
methods:{
    getProduct(){
        this.$store.dispatch('fetchProductData');
    }
}
```


왜 actions에 비동기 로직을 선언해야 하는가??  
특정 컴포넌트에서 state를 호출하고 변경하는지 확인하기가 어렵다. 이를 mutation만을 가지고 처리하기에는 state값의 변화를 추적하기 어려워진다. 