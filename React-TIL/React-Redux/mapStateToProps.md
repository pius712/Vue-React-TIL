# mapStateToProps

connect의 첫번째 인자인 `mapStateToProps`는 store로부터 특정부분의 데이터를 선택하는데 사용된다. 

- store state 가 변하면 매번 호출된다. 
- 전체 store state를 받는다. 그리고 해당 컴포넌트에서 필요로 하는 데이터의 object를 반환한다. 

## Defining mapStateToProps

`mapStateToProps` 는 함수로 정의되어야 한다. 

`function mapStateToProps(state, ownProps?)`  
또는
`const mapStateToProps = (state, ownProps?)=>{}`

첫 번째 인자는 state, 2번째 인자는 ownProps로 옵션이다. 이 함수는 연결된 컴포넌트가 필요로하는 데이터를 가지는 plain object 를 리턴한다. 

이 함수는 connect 의 첫 번째 인자로 전달되어야 한다. 그리고 Redux stroe state 가 변할때마다 호출된다. 만약 스토어를 subscribe 하지 않으려면 connect의 mapStateToProps 자리에 `null` or `undefined` 를 넘겨주면 된다. 

## Arguments

- state
- ownProps(optional)

### state

`mapStateToProps` 함수의 첫번째 인자는 리덕스 스토어 상태값이다. 이 때문에, 첫번째 인자는 일반적으로 state 라고 naming. 

The mapStateToProps function should always be written with at least state passed in.

```js
// TodoList.js

function mapStateToProps(state) {
  const { todos } = state
  return { todoList: todos.allIds }
}

export default connect(mapStateToProps)(TodoList)
```


## Return

`mapStateToProps` 함수는 컴포넌트에 필요로하는 데이터를 담은 plain object를 반환한다. 

객체내의 각각의 필드는 컴포넌트의 prop 이된다. 
필드의 값은 컴포넌트가 리렌더링 될지 말지를 결정하는데 사용된다. 

```js
function mapStateToProps(state) {
  return {
    a: 42,
    todos: state.todos,
    filter: state.visibilityFilter
  }
}

// component will receive: props.a, props.todos, and props.filter
```

---
**Note**  
In advanced scenarios where you need more control over the rendering performance, mapStateToProps can also return a function. In this case, that function will be used as the final mapStateToProps for a particular component instance. This allows you to do per-instance memoization. See the Advanced Usage: Factory Functions section of the docs for more details, as well as PR #279 and the tests it adds. Most apps never need this.

---

## Usage Guidelines

### Let mapStateToProps Reshape the Data from the Store#
mapStateToProps functions can, and should, do a lot more than just return state.someSlice. They have the responsibility of "re-shaping" store data as needed for that component. This may include returning a value as a specific prop name, combining pieces of data from different parts of the state tree, and transforming the store data in different ways.

### Use Selector Functions to Extract and Transform Data#
We highly encourage the use of "selector" functions to help encapsulate the process of extracting values from specific locations in the state tree. Memoized selector functions also play a key role in improving application performance (see the following sections in this page and the Advanced Usage: Performance page for more details on why and how to use selectors.)

### mapStateToProps Functions Should Be Fast#
Whenever the store changes, all of the mapStateToProps functions of all of the connected components will run. Because of this, your mapStateToProps functions should run as fast as possible. This also means that a slow mapStateToProps function can be a potential bottleneck for your application.

As part of the "re-shaping data" idea, mapStateToProps functions frequently need to transform data in various ways (such as filtering an array, mapping an array of IDs to their corresponding objects, or extracting plain JS values from Immutable.js objects). These transformations can often be expensive, both in terms of cost to execute the transformation, and whether the component re-renders as a result. If performance is a concern, ensure that these transformations are only run if the input values have changed.

### mapStateToProps Functions Should Be Pure and Synchronous#

Much like a Redux reducer, a mapStateToProps function should always be 100% pure and synchronous. It should simply take state (and ownProps) as arguments, and return the data the component needs as props. It should not be used to trigger asynchronous behavior like AJAX calls for data fetching, and the functions should not be declared as async.

## mapStateToProps and Performance#

Return Values Determine If Your Component Re-Renders#
React Redux internally implements the shouldComponentUpdate method such that the wrapper component re-renders precisely when the data your component needs has changed. By default, React Redux decides whether the contents of the object returned from mapStateToProps are different using === comparison (a "shallow equality" check) on each fields of the returned object. If any of the fields have changed, then your component will be re-rendered so it can receive the updated values as props. Note that returning a mutated object of the same reference is a common mistake that can result in your component not re-rendering when expected.

To summarize the behavior of the component wrapped by connect with mapStateToProps to extract data from the store: