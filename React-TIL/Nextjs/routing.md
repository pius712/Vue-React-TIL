# Routing

Next.js는 pages라는 컨셉의 파일 시스템 베이스의 router를 가지고 있다. 
pages 디렉터리에 file이 추가되면, 자동적으로 route로 사용이 가능하다. 
pages 디렉터리의 files은 일반적인 패턴으로 정의할 수 있다. 

### Index routes
 
The router will automatically route files named index to the root of the directory.

pages/index.js → /
pages/blog/index.js → /blog

### Nested routes

The router supports nested files. If you create a nested folder structure files will be automatically routed in the same way still.

pages/blog/first-post.js → /blog/first-post
pages/dashboard/settings/username.js → /dashboard/settings/username

### Dynamic route segments

To match a dynamic segment you can use the bracket syntax. This allows you to match named parameters.

pages/blog/[slug].js → /blog/:slug (/blog/hello-world)
pages/[username]/settings.js → /:username/settings (/foo/settings)
pages/post/[...all].js → /post/* (/post/2020/id/title)

## Linking between pages

Next.js SPA처럼 라우터는 페이지간의 client-side 라우트 트랜지션이 가능하다. 
Link라고 불리는 React Component가 이를 위해 사용된다, 

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

