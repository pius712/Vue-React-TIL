# Built-in CSS Support

Next.js는 JS 파일에서 import CSS를 할 수 있게 해준다. 

## Adding a Global Stylesheet

application에 stylesheet를 추가하려면, `pages/_app.js`에 CSS file을 import 하면 된다.

---
**note**
`pages/_app.js`는 모든 page들의 공통 부분이다.

---

아래의 예를 보자. styles.css 파일이다. 

```css
/* styles.css */
body {
  font-family: 'SF Pro Text', 'SF Pro Icons', 'Helvetica Neue', 'Helvetica',
    'Arial', sans-serif;
  padding: 20px 20px 60px;
  max-width: 680px;
  margin: 0 auto;
}
```
`pages/_app.js` 파일을 만들고, `styles.css` 파일을 import 하면 된다. 

```js
// pages/_app.js

import '../styles.css'
// This default export is required in a new `pages/_app.js` file.
export default function MyApp({ Component, pageProps }) {
  return <Component {...pageProps} />
}
```

이러한 css파일은 모든 페이지와 컴포넌트에 적용이 된다. stylesheets의 전역적인 특성 때문에 생기는 여러가지 conflict를 피하기 위해서는, `pages/_app.js`에서만 import를 하는 것이 좋다. 


In development, expressing stylesheets this way allows your styles to be hot reloaded as you edit them—meaning you can keep application state.

In production, all CSS files will be automatically concatenated into a single minified .css file.

## CSS SSR(styled component)

바벨 플러그인 설치
`npm install --save-dev babel-plugin-styled-components`

```json
// .babelrc
{
  "presets":["next/babel"],
  "plugins":[["babel-plugin-styled-components",{
    "ssr":true,
    "displayName": true
    }]]
}
```

```js
// pages/_document.js
import Document from 'next/document'
import { ServerStyleSheet } from 'styled-components'

export default class MyDocument extends Document {
  static async getInitialProps(ctx) {
    const sheet = new ServerStyleSheet()
    const originalRenderPage = ctx.renderPage

    try {
      ctx.renderPage = () =>
        originalRenderPage({
          enhanceApp: (App) => (props) =>
            sheet.collectStyles(<App {...props} />),
        })

      const initialProps = await Document.getInitialProps(ctx)
      return {
        ...initialProps,
        styles: (
          <>
            {initialProps.styles}
            {sheet.getStyleElement()}
          </>
        ),
      }
    } finally {
      sheet.seal()
    }
  }
}
```