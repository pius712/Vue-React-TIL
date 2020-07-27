
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

