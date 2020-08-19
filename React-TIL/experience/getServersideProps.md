# getServerSideProps

getServerSideProps는 페이지를 요청할 때, 서버에서 요청을 받아서 페이지를 렌더링해서 보내주는 방법이다.

하지만, 이를 `next/link`, 즉, client-side trainsition을 하면 약간의 문제가 발생할 수도 있다.


`next / link`

link의 경우에는 client side transition이 일어난다. 

만약에 getServerSideProps를 사용하는 경우에, url을 직접 입력하는 경우에는 getServerSideProps가 실행되서 페이지가 렌더링 된다. 

하지만 next/link 를 통해서 이동하는 경우에는 getServerSideProps에 있는 요청을 해서 결과 값을 json 으로 받아서 화면을 렌더링 한다. 그렇기 때문에, 먼저 화면을 page의 코드들을 실행한다. 

이 문제로 인해서 두가지 문제를 겪었다.  

## useEffect

따라서 store가 바뀌면서 useEffect가 실행되는 것이다. 

단적으로 

## Profile이 안 바뀌는 문제

아래의 초기 Profile 코드는 /pages/[username]/index.js에서 query의 username 부분을 props로 전달했을 때의 예이다.

로컬상에서는 문제가 없었는데, 배포이후, followerlist에서 `next/link`를 클릭해서 들어갔을때, 나의 usernote가 프로필에 그대로 적혀있는 버그를 발견했다. 

이유는 client-side transition을 할 때, 이미 화면을 렌더링하고, API를 호출하여 JSON으로 데이터를 불러오는데...

json이 오기 전에, 이미 아래의 코드가 모조리 실행되는데 store에 targetUserInfo 는 나의 정보가 들어있다. 

즉, `me.note === targetUserInfo.note` 인 상황이 되는 것이다. 그렇기 때문에 targetname !== me.nickname 이어도 결과는 같다. store가 아직 변하지 않은 상태로 화면을 렌더링 해버렸기 때문이다. 


```js
const isMyProfile = targetname === me.nickname;
const note = isMyProfile ? me.note : targetUserInfo.note;
```

아래와 같이, targetUserInfo를 사용하면, store의 값이 변할때, 다시 한번 실행되기 때문에, 화면에 제대로 그려진다. 

```js
const isMyProfile = targetUserInfo.nickname === me.nickname;
const note = isMyProfile ? me.note : targetUserInfo.note;
```

You can use this tool to verify what Next.js eliminates from the client-side bundle.

Only allowed in a page