# Slot

자식 컴포넌트에 데이터를 넘길때, props가 아니라 태그 사이에 다른 태그를 넘겨주는 방식이 있다. 그러면 그것을 받는 컴포넌트가 <slot/> 을 통해서 받을 수 있다.

```html
<!-- parent vue -->
<template>
    <div>
        <child-component>
            <div>
            <!-- 이런식으로 -->
            </div>
        </child-component>
    </div>
</template>
<!-- child vue -->
<template>
    <div>
        <slot />
    </div>
</template>
```

slot을 사용해서 자식 컴포넌트에서 렌더링할 내용을 부모 컴포넌트에 적어서 부모 컴포넌트의 데이터, 메소드, 컴퓨티드 속성을 사용할 수 있다는 장점이 있다. 

## 스코프

```HTML
// parent template
<navigation-link url="/profile">
  Logged in as {{ user.name }}
</navigation-link>
``` 

이와 같은 경우 user.name은 해당 template 스코프를 가진다. 즉, `navigation-link`에 있는 스코프는 사용할 수 없다.  

___
**note**  
Everything in the parent template is compiled in parent scope; everything in the child template is compiled in the child scope.
___



## named slots

여러 개의 슬롯을 사용하고 싶을 경우, `name` 속성을 사용하면 된다.

```HTML
// base-layout component
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

```html
<base-layout>
  <template v-slot:header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template v-slot:footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>
``` 
`template`에 `v-slot` 디렉티브를 주고 그 argument를 해당 슬롯 이름을 적어주면 된다. 그리고 template에 래핑되지 않은 나머지 부분은 `default`로 간주되어 name이 없는 슬롯 안으로 들어간다. 
 