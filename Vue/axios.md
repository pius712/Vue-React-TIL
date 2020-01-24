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
