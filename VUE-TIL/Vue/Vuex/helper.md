# Helper

Store에 있는 아래 4가지 속성들을 간편하게 코딩하는 방법

* state -> mapState
* getters -> mapGetters
* mutations -> mapMutations
* actions -> mapActions

헬퍼의 사용법

* 헬퍼를 사용하고자 하는 vue 파일에서 아래와 같이 해당 헬퍼를 로딩

```javascript
//App.vue
// import { mapState } from 'vuex'
// import { mapGetters } from 'vuex'
// import { mapMutations } from 'vuex'
// import { mapActions } from 'vuex'
import { mapState, mapGetters, mapMutations, mapActions} from 'vuex'

export default{
    computed(){ ...mapState(['num']), ...mapGetters(['contedNum'])},
    methods:{ ...mapMutations(['clickBtn']), ...mapActions(['asyncClickBtn'])}
}
```


mapState

* Vuex에 선언한 state 속성을 뷰 컴포넌트에 더 쉽게 연결해주는 헬퍼

```javascript
// App.vue
import { mapState } from 'vuex'

computed(){
    ...mapState(['num'])
    //num(){ return this.$state.num; }
}

// store.js
state:{
    num: 10
}
```

```HTML
<!-- <p> {{ this.$store.state.num }}</p> -->
<p> {{ this.num }} </p>
```

mapGetters

* Vuex에 선언한 getters 속성을 뷰 컴포넌트에 더 쉽게 연결해주는 헬퍼

```javascript
//App.vue
import { mapGetters } from 'vuex'

computed(){ ...mapGetters(['reversMessage'])}

//stroe.js
getters:{
    reverseMessage(state){
        return state.msg.split('').revers().join('');
    }
}
```

```HTML
<!-- <p> {{ this.$store.getters.reverseMessage }}</p> -->
<p>{{ this.reverseMessage }}</p>
```

## mapMutations

* Vuex에 선언한 mutations 속성을 뷰 컴포넌트에 더 쉽게 연결해주는 헬퍼 

```javascript
// App.vue
import { mapMutations } from 'vuex'

methods:{
    ...mapMutations(['clickBtn']),
    authLogin(){},
    displayTable(){}
}

// store.js
mutations:{
    clickBtn(state){
        alert(state.msg);
    }
}
```

```HTML
<button @click="clickBtn">popup message </button>
```

## mapActions

* vuex에 선언한 actions 속성을 뷰 컴포넌트에 더 쉽게 연결해주는 헬퍼

```javascript
// App.vue
import { mapActions } from 'vuex'

methods:{
    ...mapActions(['delayClickBtn']),
    authLogin(){},
    displayTable(){}
}

// store.js
mutations:{
    delayClickBtn(context){
        setTimeout(()=> context.commit('clickBtn'), 2000);
    }
}
```

```HTML
<button @click="delayClickBtn">delay popup message </button>
```

## 헬퍼의 유연한 문법

* vuex에 선언한 속성을 그대로 컴포넌트에 연결하는 문법

```javascript
// 배열 리터럴
...mapMutations([
    'clickBtn',  // 'clickBtn' : clickBtn 
    'addNumber' // addNumber(인자)
])
```

```javascript
//객체 리터럴
...mapMutations({
    popupMsg: 'clickBtn' // 컴포넌트 메서드 명: store의 뮤테이션 명
})
```

