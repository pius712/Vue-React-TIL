# Data Fetching

Next.js에서 pre-rendering을 위한 데이터 fetching 함수 세 개. 

- getStaticProps (Static Generation): Fetch data at build time.
- getStaticPaths (Static Generation): Specify - - dynamic routes to pre-render based on data.
- getServerSideProps (Server-side Rendering): Fetch data on each request.

client side에서 데이터를 fetching하는 방법에 대해 간단히 설명할 것이다. 

- getStaticProps
- getStaticPaths
- getServerSideProps
- Fetching data on the client side

## getStaticProps (Static Generation)

만약에 `async getStaticProps` 함수를 export한다면, Next.js는 이 페이지를 빌드할 때 getStaticProps 함수에서 리턴되는 props를 가지고 pre-render해줄 것이다.  

즉, return 되는 props를 해당 페이지 컴포넌트에 pass해줄 것이다. 


```js
export async function getStaticProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  }
}
```


`context` 파라미터는 아래의 key를 가지는 객체이다.

- `params` dynamic routes에서 페이지의 route 파라미터를 가지고 있다. 예를들어, page의 이름이 [id].js 인 경우에, params는 `{ id: ...}` 와 같은 모양이다.  To learn more, take a look at the Dynamic Routing documentation. You should use this together with getStaticPaths, which we’ll explain later.
- `preview` 만약 페이지가 preview mode에 있으면 true를 반환하고, 그렇지 않다면 false를 반환한다. See the Preview Mode documentation.
- `previewData` contains the preview data set by setPreviewData. See the Preview Mode documentation.

---
**Note**: You can import modules in top-level scope for use in getStaticProps. Imports used in getStaticProps will not be bundled for the client-side, as explained below.

---

예제 코드
아래는 `getStaticProps`를 사용해서 블로그의 post 리스트를 가져오는 예시이다. 

```js
// posts는 빌드 타임에 getStaticProps() 호출하여 생성된다. 
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

// 이 함수는 server-side에서 빌드 타임에 호출된다. 
// client-side에서는 호출되지 않는다. 
// 그렇기 때문에, database 쿼리도 직접 넣을 수도 있다. See the "Technical details" section. 
export async function getStaticProps() {
  // post들을 가져오기 위해 외부 API를 호출
  // You can use any data fetching library
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // By returning { props: posts }, the Blog component
  // will receive `posts` as a prop at build time
  return {
    props: {
      posts,
    },
  }
}

export default Blog
```
### When should I use getStaticProps?

언제 getStaticProps를 사용해야할까? 

- 페이지 렌더링에 필요한 user의 request 이전에 빌드 타임에 사용가능할 때
- 데이터가 headless CMS에서 올 때
- 데이터가 publicly 캐싱될 수 있을 때(user에 따라 다른게 아닐때)
- 페이지가 pre-rendered 되어야 하고(SEO를 위해서) 굉장히 빨라야할 때. getStaticProps가 HTML과 JSON 파일을 생선하고, 이 둘 모두가 퍼포먼스를 위해서 CDN으로 캐싱될 수 있을때.


getStaticPaths (Static Generation)
If a page has dynamic routes (documentation) and uses getStaticProps it needs to define a list of paths that have to be rendered to HTML at build time.

If you export an async function called getStaticPaths from a page that uses dynamic routes, Next.js will statically pre-render all the paths specified by getStaticPaths.

export async function getStaticPaths() {
  return {
    paths: [
      { params: { ... } } // See the "paths" section below
    ],
    fallback: true or false // See the "fallback" section below
  };
}
The paths key (required)
The paths key determines which paths will be pre-rendered. For example, suppose that you have a page that uses dynamic routes named pages/posts/[id].js. If you export getStaticPaths from this page and return the following for paths:

return {
  paths: [
    { params: { id: '1' } },
    { params: { id: '2' } }
  ],
  fallback: ...
}
Then Next.js will statically generate posts/1 and posts/2 at build time using the page component in pages/posts/[id].js.

Note that the value for each params must match the parameters used in the page name:

If the page name is pages/posts/[postId]/[commentId], then params should contain postId and commentId.
If the page name uses catch-all routes, for example pages/[...slug], then params should contain slug which is an array. For example, if this array is ['foo', 'bar'], then Next.js will statically generate the page at /foo/bar.
The fallback key (required)
The object returned by getStaticPaths must contain a boolean fallback key.

fallback: false
If fallback is false, then any paths not returned by getStaticPaths will result in a 404 page. You can do this if you have a small number of paths to pre-render - so they are all statically generated during build time. It’s also useful when the new pages are not added often. If you add more items to the data source and need to render the new pages, you’d need to run the build again.

Here’s an example which pre-renders one blog post per page called pages/posts/[id].js. The list of blog posts will be fetched from a CMS and returned by getStaticPaths . Then, for each page, it fetches the post data from a CMS using getStaticProps. This example is also in the Pages documentation.

// pages/posts/[id].js

function Post({ post }) {
  // Render post...
}

// This function gets called at build time
export async function getStaticPaths() {
  // Call an external API endpoint to get posts
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => ({
    params: { id: post.id },
  }))

  // We'll pre-render only these paths at build time.
  // { fallback: false } means other routes should 404.
  return { paths, fallback: false }
}

// This also gets called at build time
export async function getStaticProps({ params }) {
  // params contains the post `id`.
  // If the route is like /posts/1, then params.id is 1
  const res = await fetch(`https://.../posts/${params.id}`)
  const post = await res.json()

  // Pass post data to the page via props
  return { props: { post } }
}

export default Post
fallback: true
If fallback is true, then the behavior of getStaticProps changes:

The paths returned from getStaticPaths will be rendered to HTML at build time.
The paths that have not been generated at build time will not result in a 404 page. Instead, Next.js will serve a “fallback” version of the page on the first request to such a path (see “Fallback pages” below for details).
In the background, Next.js will statically generate the requested path HTML and JSON. This includes running getStaticProps.
When that’s done, the browser receives the JSON for the generated path. This will be used to automatically render the page with the required props. From the user’s perspective, the page will be swapped from the fallback page to the full page.
At the same time, Next.js adds this path to the list of pre-rendered pages. Subsequent requests to the same path will serve the generated page, just like other pages pre-rendered at build time.
fallback: true is not supported when using next export.

Fallback pages
In the “fallback” version of a page:

The page’s props will be empty.
Using the router, you can detect if the fallback is being rendered, router.isFallback will be true.
Here’s an example that uses isFallback:

// pages/posts/[id].js
import { useRouter } from 'next/router'

function Post({ post }) {
  const router = useRouter()

  // If the page is not yet generated, this will be displayed
  // initially until getStaticProps() finishes running
  if (router.isFallback) {
    return <div>Loading...</div>
  }

  // Render post...
}

// This function gets called at build time
export async function getStaticPaths() {
  return {
    // Only `/posts/1` and `/posts/2` are generated at build time
    paths: [{ params: { id: '1' } }, { params: { id: '2' } }],
    // Enable statically generating additional pages
    // For example: `/posts/3`
    fallback: true,
  }
}

// This also gets called at build time
export async function getStaticProps({ params }) {
  // params contains the post `id`.
  // If the route is like /posts/1, then params.id is 1
  const res = await fetch(`https://.../posts/${params.id}`)
  const post = await res.json()

  // Pass post data to the page via props
  return { props: { post } }
}

export default Post
When is fallback: true useful?
fallback: true is useful if your app has a very large number of static pages that depend on data (think: a very large e-commerce site). You want to pre-render all product pages, but then your builds would take forever.

Instead, you may statically generate a small subset of pages and use fallback: true for the rest. When someone requests a page that’s not generated yet, the user will see the page with a loading indicator. Shortly after, getStaticProps finishes and the page will be rendered with the requested data. From now on, everyone who requests the same page will get the statically pre-rendered page.

This ensures that users always have a fast experience while preserving fast builds and the benefits of Static Generation.

When should I use getStaticPaths?
You should use getStaticPaths if you’re statically pre-rendering pages that use dynamic routes.

getServerSideProps (Server-side Rendering)
If you export an async function called getServerSideProps from a page, Next.js will pre-render this page on each request using the data returned by getServerSideProps.

export async function getServerSideProps(context) {
  return {
    props: {}, // will be passed to the page component as props
  }
}
The context parameter is an object containing the following keys:

params: If this page uses a dynamic route, params contains the route parameters. If the page name is [id].js , then params will look like { id: ... }. To learn more, take a look at the Dynamic Routing documentation.
req: The HTTP IncomingMessage object.
res: The HTTP response object.
query: The query string.
preview: preview is true if the page is in the preview mode and false otherwise. See the Preview Mode documentation.
previewData: The preview data set by setPreviewData. See the Preview Mode documentation.
Note: You can import modules in top-level scope for use in getServerSideProps. Imports used in getServerSideProps will not be bundled for the client-side, as explained below.

Simple example
Here’s an example which uses getServerSideProps to fetch data at request time and pre-renders it. This example is also in the Pages documentation.

function Page({ data }) {
  // Render data...
}

// This gets called on every request
export async function getServerSideProps() {
  // Fetch data from external API
  const res = await fetch(`https://.../data`)
  const data = await res.json()

  // Pass data to the page via props
  return { props: { data } }
}

export default Page
When should I use getServerSideProps?
You should use getServerSideProps only if you need to pre-render a page whose data must be fetched at request time. Time to first byte (TTFB) will be slower than getStaticProps because the server must compute the result on every request, and the result cannot be cached by a CDN without extra configuration.

If you don’t need to pre-render the data, then you should consider fetching data on the client side. Click here to learn more.


## Fetching data on the client side

페이지가 자주 업데이트되는 data를 가지고 있을 때, 그리고 data를 pre-render할 필요가 없을때, client-side에서 data를 fetching할 수 있다.  
한가지 예는 user-specific 데이터이다. 

- 우산, page를 데이터 없이 즉각적으로 불러준다. 페이지의 다른 부분들은 Static Generation을 사용해서 pre-render 될 수 있다. missing data에 대해서는 loading state를 보여줄 수 있다. 
- 그리고, data를 fetch해서 준비가 되면 client side에서 보여준다. 

예를 들어, 이러한 접근법은 유저 dashboard 페이지에서 유용하게 사용된다.  
왜냐하면 dashboard는 private, user-specific 페이지이기 때문이다. SEO와 관계 없고, 해당 페이지는 pre-render될 필요가 없다.  
해당 데이터들은 자주 업데이트 되고, request-time이 필요하다. 

### SWR

SWR은 데이터를 fetching 하는 리액트의 Hook이다.   
client side에서 데이터를 fetching 할 때, 권장되는 hook이다. 이는 caching, revalidation, focus tracking, refetching을 간격을 두고 다룬다. 

`npm install swr`

```js
import useSWR from 'swr'

function Profile() {
  const { data, error } = useSWR('/api/user', fetch)

  if (error) return <div>failed to load</div>
  if (!data) return <div>loading...</div>
  return <div>hello {data.name}!</div>
}
```
Check out the SWR documentation to learn more. [공식문서](https://swr.vercel.app/)