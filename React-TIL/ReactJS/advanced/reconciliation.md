# Reconciliation

React provides a declarative API so that you don’t have to worry about exactly what changes on every update. This makes writing applications a lot easier, but it might not be obvious how this is implemented within React. This article explains the choices we made in React’s “diffing” algorithm so that component updates are predictable while being fast enough for high-performance apps.

## Motivation

React를 사용할 때, 특정 시점에 `render()` 함수를 React elements 트리를 만드는 것이라고 생각할 수 있다. 
next state나 props가 업데이트 될 때, `render()` 함수는 다른 React elements 트리를 반환한다. 
리액트는 그 때, 어떻게 가장 최신의 트리로 UI를 업데이트할지를 알아낸다. 


When you use React, at a single point in time you can think of the render() function as creating a tree of React elements. On the next state or props update, that render() function will return a different tree of React elements. React then needs to figure out how to efficiently update the UI to match the most recent tree.


과거의 트리에서 최신의 트리로 변환하기 위해서 가장 작은 수의 연산을 하기 위해서는 가장 빠른 알고리즘이 O(n^3) 이다. (n = elements의 수)


There are some generic solutions to this algorithmic problem of generating the minimum number of operations to transform one tree into another. However, the state of the art algorithms have a complexity in the order of O(n3) where n is the number of elements in the tree.

이 알고리즘을 사용하여 1000개의 elements를 보여주려고 한다면, 10억번의 비교작업이 필요하다. 그렇기 때문에, 리액트는 O(n)시간이 걸리는 휴리스틱 알고리즘을 사용한다. 
이 알고리즘을 사용하기 위해서는 아래와 같은 가정이 필요하다. 
- 2개의 서로 다른 type의 elements는 서로 다른 tree를 만든다. 
- 개발자는 key prop을 통해서 어떤 child elements가 다른 render 사이에도 변하지 않는지를 알려줄 수 있다. 

실제로 이러한 가정은 거의 모든 경우에 유효하다. 

## The Diffing Algorithm

When diffing two trees, React first compares the two root elements. The behavior is different depending on the types of the root elements.


### Elements Of Different Types

루트 엘리먼트가 다른 타입이라면, 리액트는 오래된 tree를 없애고 새로운 트리를 처음부터 빌드한다. 
Going from <a> to <img>, or from <Article> to <Comment>, or from <Button> to <div> - any of those will lead to a full rebuild.

tree를 없앨때, 오래된 DOM node들은 없어진다. 컴포넌트 인스턴스는 `componentWillUnmount()`를 받게 된다.  
새로운 트리를 빌드할 때, 새로운 DOM node들이 DOM에 삽입된다.  
컴포넌트 인스턴스는 `componentWillMount()` `componentDidMount()`를 받게 된다. 
old tree와 관련있는 state는 사라진다. 


Any components below the root will also get unmounted and have their state destroyed. For example, when diffing:
root 아래의 컴포넌트들은 unmounted되고, state가 사라진다.   
예를들어 아래와 같이 `<div>` -> `<span>`으로 root element가 바뀌는 경우에는 위쪽의 Counter 컴포넌트는 없어지고 새로운 Counter 컴포넌트가 remount 되는 것이다. 


```JSX
// 위
<div>
  <Counter />
</div>
// 아래
<span>
  <Counter />
</span>
```
This will destroy the old Counter and remount a new one.

### DOM Elements Of The Same Type

같은 타입의 React DOM elements를 비교할 때, 리액트는 두 elements의 attributes를 보고, DOM 노드는 유지한채 변경된 attributes만을 업데이트 한다. 
예를들어 아래의 경우에는 DOM node에서 className만 다르기 때문에, 이것만 수정해주는 것이다. 

```jsx
<div className="before" title="stuff" />

<div className="after" title="stuff" />
```

style을 업데이트 할때도, 두 노드의 다른 properties 만을 업데이트 해준다. 

```jsx
<div style={{color: 'red', fontWeight: 'bold'}} />

<div style={{color: 'green', fontWeight: 'bold'}} />
```

이 예시에서는 color style만 다르기 때문에, 이것만 수정하고, fontWeight는 수정하지 않는다. 
해당 DOM node를 변경하고는, 자식 노드에 대해서 반복해준다. 


### Component Elements Of The Same Type
When a component updates, the instance stays the same, so that state is maintained across renders. React updates the props of the underlying component instance to match the new element, and calls componentWillReceiveProps() and componentWillUpdate() on the underlying instance.

Next, the render() method is called and the diff algorithm recurses on the previous result and the new result.

Recursing On Children
By default, when recursing on the children of a DOM node, React just iterates over both lists of children at the same time and generates a mutation whenever there’s a difference.

For example, when adding an element at the end of the children, converting between these two trees works well:

<ul>
  <li>first</li>
  <li>second</li>
</ul>

<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
React will match the two <li>first</li> trees, match the two <li>second</li> trees, and then insert the <li>third</li> tree.

If you implement it naively, inserting an element at the beginning has worse performance. For example, converting between these two trees works poorly:

<ul>
  <li>Duke</li>
  <li>Villanova</li>
</ul>

<ul>
  <li>Connecticut</li>
  <li>Duke</li>
  <li>Villanova</li>
</ul>
React will mutate every child instead of realizing it can keep the <li>Duke</li> and <li>Villanova</li> subtrees intact. This inefficiency can be a problem.

Keys
In order to solve this issue, React supports a key attribute. When children have keys, React uses the key to match children in the original tree with children in the subsequent tree. For example, adding a key to our inefficient example above can make the tree conversion efficient:

<ul>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>

<ul>
  <li key="2014">Connecticut</li>
  <li key="2015">Duke</li>
  <li key="2016">Villanova</li>
</ul>
Now React knows that the element with key '2014' is the new one, and the elements with the keys '2015' and '2016' have just moved.

In practice, finding a key is usually not hard. The element you are going to display may already have a unique ID, so the key can just come from your data:

<li key={item.id}>{item.name}</li>
When that’s not the case, you can add a new ID property to your model or hash some parts of the content to generate a key. The key only has to be unique among its siblings, not globally unique.

As a last resort, you can pass an item’s index in the array as a key. This can work well if the items are never reordered, but reorders will be slow.

Reorders can also cause issues with component state when indexes are used as keys. Component instances are updated and reused based on their key. If the key is an index, moving an item changes it. As a result, component state for things like uncontrolled inputs can get mixed up and updated in unexpected ways.

Here is an example of the issues that can be caused by using indexes as keys on CodePen, and here is an updated version of the same example showing how not using indexes as keys will fix these reordering, sorting, and prepending issues.

Tradeoffs
It is important to remember that the reconciliation algorithm is an implementation detail. React could rerender the whole app on every action; the end result would be the same. Just to be clear, rerender in this context means calling render for all components, it doesn’t mean React will unmount and remount them. It will only apply the differences following the rules stated in the previous sections.

We are regularly refining the heuristics in order to make common use cases faster. In the current implementation, you can express the fact that a subtree has been moved amongst its siblings, but you cannot tell that it has moved somewhere else. The algorithm will rerender that full subtree.

Because React relies on heuristics, if the assumptions behind them are not met, performance will suffer.

The algorithm will not try to match subtrees of different component types. If you see yourself alternating between two component types with very similar output, you may want to make it the same type. In practice, we haven’t found this to be an issue.
Keys should be stable, predictable, and unique. Unstable keys (like those produced by Math.random()) will cause many component instances and DOM nodes to be unnecessarily recreated, which can cause performance degradation and lost state in child components.
