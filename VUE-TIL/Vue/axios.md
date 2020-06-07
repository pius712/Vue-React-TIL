# Axios

## vue resource

더 이상 공식 라이브러리가 아니다. 현재는 axios를 권장함.

## axios? 

> 뷰에서 권고하는 http 통신 라이브러리  
> 프로미스 기반의 HTTP 통신 라이브러리. 

## post

`axios.post('url', {})`  

```js
document.querySelector('#form').onsubmit = (e)=>{
    e.preventDefault();
    // console.log(e.target.value);
    var name = document.querySelector('#username').value;
    // console.log(name);
    if(name === ''){
        alert('이름을 입력해주세요');
    }else{
        axios.post('/users',{
            "name" : name
        })
        .then((res)=>{
            console.log(res);
            getUser();
            
        })
        .catch((err)=>{
            console.log(err);
        })
        document.querySelector('#username').value = '';    
    }    
};
```


위 코드는 `form` 태그 내의 name을 /users로 post 요청을 보낸다. test라고 적고 submit을 하게되면  {"name": "test"} JSON이 post로 날아간다. 

이 JSON 문자열 `'{"name": "test"}'`을 JSON.parse()를 통해 자바스크립트 객체로 만들어준다. 즉, const obj = JSON.parse(body);를 해주면 위의 JSON이 자바스크립트에서의 객체가 된다.

```js
else if(req.method === 'POST'){  
    if(req.url === '/users'){
        let body = '';
        req.on('data', (chunk)=>{
            body += chunk;
        });
        req.on('end', ()=>{
            console.log( 'POST 본문(body)', body);
            const { name } = JSON.parse(body);
            const id = +new Date();
            users[id] = name;
            console.log(users);
            res.writeHead(201);
            res.end('사용자 등록 성공');
        })
    }
}
```


## axios.create()

custom config를 가지고 axios의 instance를 생성할 수 있다.
```js
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```

이렇게 만들어진 instance는 아래와 같은 메서드를 사용할 수 있다.

```js
instance.request(config)
instance.get(url[, config])
instance.delete(url[, config])
instance.head(url[, config])
instance.options(url[, config])
instance.post(url[, data[, config]])
instance.put(url[, data[, config]])
instance.patch(url[, data[, config]])
instance.getUri([config])
```

## intercetor

request 또는 response 요청을 할 때, 이를 중간에 가로챌 수 있다. 

request 인터셉터는 axios롤 request를 보내기 전에 config를 설정해서 보낼 수 있다. 

response는 response의 status code가 200번대이면 response data를 조작할 수 있다. 

아래는 예시 코드이다. 

```js
// Add a request interceptor
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });

// Add a response interceptor
axios.interceptors.response.use(function (response) {
    // Any status code that lie within the range of 2xx cause this function to trigger
    // Do something with response data
    return response;
  }, function (error) {
    // Any status codes that falls outside the range of 2xx cause this function to trigger
    // Do something with response error
    return Promise.reject(error);
  });
```

axios의 커스텀 인스턴스에도 아래와 같이 interceptor를 추가할 수 있다. 

```js
const instance = axios.create();
instance.interceptors.request.use(function () {/*...*/});
```

아래는 인터셉터 기능을 처리하는 모듈을 만든는 예이다.

```js
//api/common/interceptors.js

import store from '@/store/index';
function setInterceptors(instance) {
	instance.interceptors.request.use(
		function(config) {
			config.headers.Authorization = store.state.token;
			console.log(config);
			return config;
		},
		function(error) {
			return Promise.reject(error);
		},
	);
	instance.interceptors.response.use(
		function(response) {
			return response;
		},
		function(error) {
			return Promise.reject(error);
		},
	);
	return instance;
}

export default setInterceptors;
```

함수의 인자로 넘겨받은 axios의 instance에 inteceptors에 대한 설정을 마치고 instance를 return 해준다. 