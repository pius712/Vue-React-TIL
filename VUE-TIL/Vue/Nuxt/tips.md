# tips

## 반복되는 컴포넌트

모양은 같은데 데이터만 다른 컴포넌트 두개르 만들때, 두개의 파일을 만들지 않는법.  
`props`를 넘겨주면 된다. 

```html
<v-container>
    <v-subheader>팔로잉</v-subheader>
    <follow-list :users="followingList" :remove="removeFollowing"/>
</v-container>
</v-card>
<v-card style="margin-bottom: 20px">
<v-container>
    <v-subheader>팔로워</v-subheader>
    <follow-list :users="followerList" :remove="removeFollower"/>
</v-container>
```

위에는 `follow-list`라는 하나의 컴포넌트만을 사용한다. 하지만, `props`를 통해서 이를 구분.

```js
  computed:{
    followerList(){
      return this.$store.state.users.followerList;
    },
     followingList(){
      return this.$store.state.users.followingList;
    }
  },
  methods:{
    removeFollower(payload){
            this.$store.dispatch('users/removeFollower', payload);
    },
    removeFollowing(payload){
        console.log(payload);
        // console.log("팔로워");
        this.$store.dispatch('users/removeFollowing',payload);
    }
  },
```

아래의 follow-list는 이것을 받아 데이터를 뿌려주면 된다. 
![예시](../../img/nuxt-tips.png)

## virtualized list 

--> 따로 알아보기
vue virtual scroll list 

## infinite scrolling

(page에서) window의 스크롤 이벤트를 기본적으로 사용한다. 

```js
  methods:{
    scroll(){

    }
  },
  mounted(){
    window.addEventListener('scroll', this.onScroll);
  },
  beforeDestory(){
    window.removeEventListener('scroll', this.onScroll);
  } // remove하는 이유는 이 페이지에 접속할 때마다 addEventListener를 달아주기 때문에 메모리 누수가 생긴다. 따라서 페이지(컴포넌트)가 없어질때 해제해줘야한다. 
```

* Element.clientHeight  
: clientHeight는 스크롤을 포함하지 않는 우리가 보는 화면의 높이를 말한다. 즉 window.clientHeight라고 하면 브라우저의 높이를 말하는 것이다. (padding까지 포함)
* Element.scrollHeight  
: 스크롤 때문에 현재 보이지 않지만, 존재하는 컨텐츠영역의 높이이다. (padding까지 포함)  
* scrollY  //element.scrollTop이랑 같은 개념이다.   
: 가장 위에서 스크롤이 얼마나 내려왔는지를 나타냄.  

스크롤이 가장 아래로 내려갔을 때 
window.scrollY + document.documentElement.clientHeight 
=== document.documentElement.scrollHeight

즉, 어느 일정 스크롤을 내렸을 때 다음 게시물을 불러와야 한다. 아래의 식은 스크롤이 맨 아래에 닫기 300px 전을 의미한다. 
window.scrollY + document.documentElement.clientHeight >= document.documentElement.scrollHeight -300 

## pagenation 

## scroll behavior