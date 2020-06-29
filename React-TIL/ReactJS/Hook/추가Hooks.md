# 추가 Hook


## useMemo

```js
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

## useCallback

일반적인 함수 선언은 component가 리렌더링될 때마다 새롭게 만들어진다. `useCallback` 훅을 사용하면 메모이제이션된 콜백을 반환하게 된다.


```js
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

인라인 콜백과 해당 콜백 함수가 의존하고 있는 값(state or props)이 있다면, 그것을 배열로 전달해야한다.  useCallback은 콜백의 메모이제이션된 버전을 반환할 것입니다. 그 메모이제이션된 버전은 콜백의 의존성이 변경되었을 때에만 변경됩니다. 이것은, 불필요한 렌더링을 방지하기 위해 (예로 shouldComponentUpdate를 사용하여) 참조의 동일성에 의존적인 최적화된 자식 컴포넌트에 콜백을 전달할 때 유용합니다.

useCallback(fn, deps)은 useMemo(() => fn, deps)와 같습니다.

--- 
**note**

의존성 값의 배열이 콜백에 인자로 전달되지는 않습니다. 그렇지만 개념적으로는, 이 기법은 콜백 함수가 무엇일지를 표현하는 방법입니다. 콜백 안에서 참조되는 모든 값은 의존성 값의 배열에 나타나야 합니다. 나중에는 충분히 발전된 컴파일러가 이 배열을 자동적으로 생성할 수 있을 것입니다.

eslint-plugin-react-hooks 패키지의 일부로써 exhaustive-deps 규칙을 사용하기를 권장합니다. 그것은 의존성이 바르지 않게 정의되었다면 그에 대해 경고하고 수정하도록 알려줍니다.

---