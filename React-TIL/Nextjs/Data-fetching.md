# Data Fetching

Next.js에서 pre-rendering을 위한 데이터 fetching 함수 세 개. 

- getStaticProps (Static Generation): Fetch data at build time.
- getStaticPaths (Static Generation): Specify - - dynamic routes to pre-render based on data.
- getServerSideProps (Server-side Rendering): Fetch data on each request.

client side에서 데이터를 fetching하는 방법에 대해 간단히 설명할 것이다. 


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

- `params` contains the route parameters for pages using dynamic routes. For example, if the page name is [id].js , then params will look like { id: ... }. To learn more, take a look at the Dynamic Routing documentation. You should use this together with getStaticPaths, which we’ll explain later.
- `preview` is true if the page is in the preview mode and false otherwise. See the Preview Mode documentation.
- `previewData` contains the preview data set by setPreviewData. See the Preview Mode documentation.

---
**Note**: You can import modules in top-level scope for use in getStaticProps. Imports used in getStaticProps will not be bundled for the client-side, as explained below.

---