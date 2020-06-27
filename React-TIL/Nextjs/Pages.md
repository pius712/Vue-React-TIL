# Pages

## Pages

Next.js에서 페이지는  `pages` 디렉터리에 있는 리액트 컴포넌트이다. 
In Next.js, a page is a React Component exported from a file in the pages directory.

Nextjs에서 pages는  file 이름을 기반으로 하는 라우트와 관련이 되어있다.  

- `pages/index.js`  => `/` route.
- `pages/posts/first-post.js` => `/posts/first-post` route.


## Create a New Page

```js
// pages/posts/first-post.
export default function FirstPost() {
  return <h1>First Post</h1>
}
```

함수의 이름은 아무거나 해도 상관없지만, export `default`로 넘겨줘야 한다. `/posts/first-post.`

## Link Component

페이지 간에 링크를 걸때, html에서는 일반적으로 `<a>` tag를 사용한다. 

Next.js에서는 `<Link>` 라고 하는 React Component를 사용하게 된다(`<a>` 태그를 감싸고 있는 컴포넌트).  
이 `<Link>` client-side navigation이 가능하도록 한다. 

<Link>를 사용하기 위해서는, Link Component를 import 해야한다. 

```js
import Link from 'next/link'
```

```js
Learn <a href="https://nextjs.org">Next.js!</a>
// 위는 html의 a tag이다. 
// 아래는 react component Link를 사용한 것.
Read <Link href="/posts/first-post"><a>this page!</a></Link>
```

`pages/posts/first-post.js` 에 적용.

```js
// pages/posts/first-post.js

import Link from 'next/link'

export default function FirstPost() {
  return (
    <>
      <h1>First Post</h1>
      <h2>
        <Link href="/">
          <a>Back to home</a>
        </Link>
      </h2>
    </>
  )
}
```

Link component는 `<a>` tags와 비슷하다. 하지만, `<a href="…">` 방식 대신에,  `<Link href="…">`를 쓰고 안에  `<a>` 태그를 쓰면 된다.


## code splitting

Next.js의 경우 코드 스플릿팅을 자동적으로 해준다. 그렇기 때문에, 각 페이지는 그 해당 페이지에 필요한 것만을 로드한다. 

즉, 수많은 페이지들이 있어도, homepage에 들어갈 때, 해당 페이지만을 로드하기 때문에, 로드가 빠르다.  
그리고, 각각의 페이지가 분리되어 있기 때문에, 하나의 페이지가 오류를 던져도 나머지 application은 정상 작동한다. 

그리고 production build시, 페이지에 링크가 걸려있는 경우, 이를 prefetching 해준다. 그렇기 때문에, 어떤 링크를 클릭하면 이미 해당 페이지 코드는 fetching 되어 있기 때문에, 빠르게 전환이 된다. 

### 요약.
Next.js는 자동으로 code splitting해서 최적화를 한다. 

라우트를 pages 디렉터리 내의 파일로 만들고, 빌트인 Link component를 사용하면 된다. 다른 routing 라이브러리는 필요 없다. 

You can learn more about the Link component in the API reference documentation and routing in general in the routing documentation.