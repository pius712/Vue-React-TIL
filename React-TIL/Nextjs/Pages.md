# Pages

- Pages
- Create a New Page
- 선요약
- Pre-rendering
  - Two forms of Pre-rendering
- Static Generation 
  - getStaticProps
  - getStaticPaths
- When should I use Static Generation?
- Server-side Rendering
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
## Summary

Next.js에서 사용되는 두가지 형태의 pre-rendering에 대해 알아본다. 

### `Static Generation (Recommended)`

HTML이 build time에 생성되고, 매 request에서 재사용 될 수 있다. Static Generation을 페이지에서 사용하기 위해서는 page component를 export 하고, getStaticProps(필요시 getStaticPaths)를 export 해야한다.  
유저의 request 이전에 페이지를 pre-render하는 것이 좋은 경우에 사용한다.  
또한 추가적인 데이터를 가져오기 위해서 Client-side Rendering을 사용할 수도 있다.

### `Server-side Rendering`

HTML이 매 request마다 생성된다. 페이지에서 Server-side Rendering을 사용하기 위해서는 export getServerSideProps를 해줘야한다.  
Server-side Rendering은 비교적 느리기 때문에, 필요로 할 때 사용해야한다. 

### 개인적인 의견

Static Generation이 Next에서 권장하는 방식이다. 하지만 이러한 경우 결국 HTML이 만들어져서 나오기 때문에, interaction이 빈번한 웹의 경우에는 사용하기가 어렵다. 
정확히 말하자면, Static Generation이 사용하기 어렵다기보다, getStaticProps 함수를 사용하는 것이 어렵다.  

Static Generation + client side rendering
Or Server-side Rendering을 쓰는 방식이 더 좋지 않을까..? 퍼포먼스 측면에서는 떨어지더라도.


## Pre-rendering

기본적으로, Next.js는 모든 페이지를 pre-render해준다. 즉, Next.js는 각각의 페이지들을 미리 HTML을 만들어주는 것이다. 
Client-side에서 모든 js 코드를 해석해서 만들어 주는 것이 아니기 때문에, 보다 나은 퍼포먼스를 기대할 수 있다는 장점이 있다.

생성된 HTML은 해당 페이지에 필요로하는 최소한의 js code를 가진다. 브라우저에 의해 page가 로딩될 때, 그 js 코드가 실행되고 해당 페이지가 완전히 interactive 하게 만들어준다. (이러한 과정을 hydration이라고 부른다.) 
active. (This process is called hydration.)

### Two forms of Pre-rendering

Next.js는 두가지 형태의 pre-rendering이 있다. `Static Generation`과 `Server-side Rendering` 이 둘의 차이는 HTML이 언제 생성되는지에 있다. 

- `Static Generation` (권장): HTML이 build time에 생성되고, 각각의 request에 따라 언제든 재사용이 가능하다. 
- `Server-side Rendering`: HTML이 각각의 request에 따라 생성된다. 

Next.js에서는 각 페이지마다 사용할 수 있는 `pre-rendering` 형태를 골라서 사용할 수 있도록 해준다. 즉, 대부분의 페이지에서는 `Static Generation`를 사용하고, 나머지 페이지에서는 `Server-side Rendering`을 사용하는 혼합된 형태로 사용이 가능하다. 

`Static Generation`이 권장되는 이유는 퍼포먼스때문이다. 정적으로 생성된 페이지들은 캐싱될 수 있기 때문이다.  
물론, 때때로 `Server-side Rendering`이 꼭 필요한 경우도 있다. 

그리고 Client-side Rendering을 `Static Generation`과 `Server-side Render`와 함께 사용할 수 있다.  
page의 몇 부분은 client side js에 의해 렌더링 될 수 있다. (Data Fetching 문서에서 자세하게 다룬다.)

## Static Generation

### Static Generation without data
기본적으로 Next.js는 페이지를 data fetching 없이 `Static Generation`을 사용해서 pre-redner한다.

```js
function About() {
  return <div>About</div>
}

export default About
```
이 페이지는 외부의 어떠한 data도 fetch하지 않는다. 이러한 예와 같이, Next.js는 단일 HTML 파일을 페이지마다 빌드 타임에 생성한다. 

### Static Generation with data

몇몇의 페이지는 외부의 데이터를 fetching해서 pre-rendering을 해야할 수도 있다.  
여기에는 두가지 시나리오가 있는데, 하나 혹은 둘 모두 적용하여 사용할 수 있다.  
각각의 케이스에 Next.js가 제공하는 special function을 사용할 수 있다. 

- `getStaticProps`: 페이지의 컨텐츠가 외부의 data에 의존적일 때 사용  
- `getStaticPaths`: 페이지의 paths가 외부의 데이터에 의존적일 때 사용. (일반적으로는 getStaticProps에 추가적으로 사용)

#### Scenario 1: Your page content depends on external data

예시: 블로그 페이지가 CMS(content managemenet system)으로부터 게시글 리스트를 fetch 해야할 때.  

```js
// TODO: Need to fetch `posts` (by calling some API endpoint)
//       before this page can be pre-rendered.
function Blog({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li>{post.title}</li>
      ))}
    </ul>
  )
}

export default Blog
```

이 데이터를 pre-render시에 fetch 하기 위해서는, Next.js는 `getStaticProps`라고 불리는 비동기 함수를 같은 파일에서 exprot 할 수 있도록 해준다. 이 함수는 build time에 호출되어, pre-render 시에 page의 props로 넘겨준다. 

```js
function Blog({ posts }) {
  // Render posts...
}

// This function gets called at build time
export async function getStaticProps() {
  // Call an external API endpoint to get posts
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

To learn more about how getStaticProps works, check out the Data Fetching documentation.

#### Scenario 2: Your page paths depend on external data

Next.js는 dynamic routes에 따른 page들을 만들 수 있도록 해준다.  
예를 들어서, `pages/posts/[id].js` 와 같은 포스트의 id에 따른 단일 블로그 포스트를 페이지를 만들 수 있다.  

To learn more about dynamic routing, check the Dynamic Routing documentation.

하지만, 빌드 타임에 pre-render하고 싶은 id는 외부에 의존적일 수도 있다. 

예시: post의 id가 1인 blog post 하나만을 데이터 베이스에 저장했다고 가정할 때, posts/1 페이지만을 빌드 타임에 pre-render 하고 싶을 수도 있다. 

이후에, 2번째 post를 저장하고, 이를 pre-render하는 것도 마찬가지. 

따라서, pre-render 되어야할 페이지의 paths가 외부에 의존적인 상황이다. 이를 다루기 위해서는 `getStaticPathsTo` 비동기 함수를 dynamic page에서 호출 할 수 있다. 이 예에서는 pages/posts/[id].js. 이 함수는 build time에 호출 되고, 어떤 paths가 pre-render될지를 명시할 수 있다. 

```js
// This function gets called at build time
export async function getStaticPaths() {
  // Call an external API endpoint to get posts
  const res = await fetch('https://.../posts')
  const posts = await res.json()

  // Get the paths we want to pre-render based on posts
  const paths = posts.map((post) => `/posts/${post.id}`)

  // We'll pre-render only these paths at build time.
  // { fallback: false } means other routes should 404.
  return { paths, fallback: false }
}
```
또한, pages/posts/[id].js 에서는 post에 대한 data를 fetch하기 위해서 `getStaticProps`를 export 할 수도 있다. 

```js
function Post({ post }) {
  // Render post...
}

export async function getStaticPaths() {
  // ...
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
```

To learn more about how getStaticPaths works, check out the Data Fetching documentation.


## When should I use Static Generation?

가능하다면 `Static Generation`이 권장된다. 왜냐하면, 페이지가 한번 빌드되고, CDN에 의해서 제공되기 때문에 매 request마다 페이지를 server-rendering 하는 것보다 퍼포먼스가 좋기 때문이다. 

`Static Generation`는 다양한 타입의 페이지에서 사용될 수 있다.

- Marketing pages
- Blog posts
- E-commerce product listings
- Help and documentation

user의 reuqest 이전에 page가 렌더링 되어야 한다? 그렇다면 Static Generation을 사용하면 된다. 

반면에, user의 request가 있기 전에 페이지를 렌더링할 필요가 없다면, 사용하지 않는 것이 좋다. 
만약에, 페이지가 자주 업데이트 되는 page를 보여줘야 한다면, 매 request마다 page의 컨텐트가 바뀐다면..
이런 경우에는 아래와 같은 방법을 사용할 수 있다. 

- Use Static Generation with Client-side Rendering: 페이지의 일부분은 pre-rendering하지 않고, Client-side js를 사용한다. 이 부분은 Data fetching 문서에서 다룬다. 
- Use Server-Side Rendering: Next.js는 매 request에 따라 pre-render를 진행한다. 이는 CDN에 의해 캐싱되지 않기 때문에, 비교적 느리다. 하지만, pre-render되는 페이지가 항상 최신의 상태를 유지해야한다면, 이와 같은 방식을 사용해야한다. 

### Server-side Rendering

 "SSR" or "Dynamic Rendering".

만약 페이지가 Server-side Rendering을 사용한다면, page HTML은 매 request마다 생성된다. 
Server-side Rendering을 사용하기 위해서는, `getServerSideProps` 비동기 함수를 export 해야한다. 이 함수는 매 request마다 서버에서 호출 된다. 

예를 들어, 외부의 API를 사용하여 data를 불러와서 update 해야한다면, 데이터를 fetch하고 이 데이터를 넘겨주는 `getServerSideProps`를 사용할 수 있다. 

```js
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
```
위에서 보다 시피, `getServerSideProps`는 `getStaticProps`와 비슷하다. 하지만 `getServerSideProps`는 빌드 타임에 호출되는 것이 아니라, 매 request마다 실행된다는 것이다. 

To learn more about how getServerSideProps works, check out our Data Fetching documentation

## code splitting

Next.js의 경우 코드 스플릿팅을 자동적으로 해준다. 그렇기 때문에, 각 페이지는 그 해당 페이지에 필요한 것만을 로드한다. 

즉, 수많은 페이지들이 있어도, homepage에 들어갈 때, 해당 페이지만을 로드하기 때문에, 로드가 빠르다.  
그리고, 각각의 페이지가 분리되어 있기 때문에, 하나의 페이지가 오류를 던져도 나머지 application은 정상 작동한다. 

그리고 production build시, 페이지에 링크가 걸려있는 경우, 이를 prefetching 해준다. 그렇기 때문에, 어떤 링크를 클릭하면 이미 해당 페이지 코드는 fetching 되어 있기 때문에, 빠르게 전환이 된다. 

### 요약.
Next.js는 자동으로 code splitting해서 최적화를 한다. 

라우트를 pages 디렉터리 내의 파일로 만들고, 빌트인 Link component를 사용하면 된다. 다른 routing 라이브러리는 필요 없다. 

You can learn more about the Link component in the API reference documentation and routing in general in the routing documentation.

## Static Generation (Recommended)
 The HTML is generated at build time and will be reused on each request. To make a page use Static Generation, either export the page component, or export getStaticProps (and getStaticPaths if necessary). It's great for pages that can be pre-rendered ahead of a user's request. You can also use it with Client-side Rendering to bring in additional data.