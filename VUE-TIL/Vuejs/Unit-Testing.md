# Unit Testing

## Jest

Jest 기본 내용은 TIL 문서에 `TDD/Jest` 문서 참조



## Simple example

```html
<template>
  <span>{{ message }}</span>
</template>

<script>
  export default {
    data () {
      return {
        message: 'hello!'
      }
    },
    created () {
      this.message = 'bye!'
    }
  }
</script>
```

```js
// Import `shallowMount` from Vue Test Utils and the component being tested
import { shallowMount } from '@vue/test-utils'
import MyComponent from './MyComponent.vue'

// Mount the component
const wrapper = shallowMount(MyComponent)

// Here are some Jest tests, though you can
// use any test runner/assertion library combo you prefer
describe('MyComponent', () => {
  // Inspect the raw component options
  test('has a created hook', () => {
    expect(typeof MyComponent.created).toBe('function')
  })

  // Evaluate the results of functions in
  // the raw component options
  test('sets the correct default data', () => {
    expect(typeof MyComponent.data).toBe('function')
    const defaultData = MyComponent.data()
    expect(defaultData.message).toBe('hello!')
  })

  // Inspect the component instance on mount
  test('correctly sets the message when created', () => {
    expect(wrapper.vm.$data.message).toBe('bye!')
  })

  // Mount an instance and inspect the render output
  test('renders the correct message', () => {
    expect(wrapper.text()).toBe('bye!')
  })
})
```

## shallowMount/mount

## find()

