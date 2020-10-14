# Optimizing Performance

내부적으로, 리액트는 DOM 연산을 최소화 하기위한 테크닉들을 사용한다. DOM 연산은 UI를 업데이트 해주는데, 이 연산은 값비싼 연산이다. 

리액트를 사용하는 수많은 앱에서는 성능 최적화를 위해서 해야할 것이 별로 없지만, 성능을 끌어올리는 방법이 여러가지가 있다.


## Use the Production Build

If you’re benchmarking or experiencing performance problems in your React apps, make sure you’re testing with the minified production build.

By default, React includes many helpful warnings. These warnings are very useful in development. However, they make React larger and slower so you should make sure to use the production version when you deploy the app.

If you aren’t sure whether your build process is set up correctly, you can check it by installing React Developer Tools for Chrome. If you visit a site with React in production mode, the icon will have a dark background:

If you visit a site with React in development mode, the icon will have a red background:

It is expected that you use the development mode when working on your app, and the production mode when deploying your app to the users.

You can find instructions for building your app for production below.

### Create React App

If your project is built with Create React App, run:

npm run build
This will create a production build of your app in the build/ folder of your project.

Remember that this is only necessary before deploying to production. For normal development, use npm start.



## Avoid Reconciliation
React builds and maintains an internal representation of the rendered UI. It includes the React elements you return from your components. This representation lets React avoid creating DOM nodes and accessing existing ones beyond necessity, as that can be slower than operations on JavaScript objects. Sometimes it is referred to as a “virtual DOM”, but it works the same way on React Native.

When a component’s props or state change, React decides whether an actual DOM update is necessary by comparing the newly returned element with the previously rendered one. When they are not equal, React will update the DOM.

Even though React only updates the changed DOM nodes, re-rendering still takes some time. In many cases it’s not a problem, but if the slowdown is noticeable, you can speed all of this up by overriding the lifecycle function shouldComponentUpdate, which is triggered before the re-rendering process starts. The default implementation of this function returns true, leaving React to perform the update:

shouldComponentUpdate(nextProps, nextState) {
  return true;
}
If you know that in some situations your component doesn’t need to update, you can return false from shouldComponentUpdate instead, to skip the whole rendering process, including calling render() on this component and below.

In most cases, instead of writing shouldComponentUpdate() by hand, you can inherit from React.PureComponent. It is equivalent to implementing shouldComponentUpdate() with a shallow comparison of current and previous props and state.

