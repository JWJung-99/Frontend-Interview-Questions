# useMemo란?

<br />

## `useMemo`

`useMemo`는 리렌더링 사이에 **계산 결과를 캐싱**할 수 있게 해 주는 React Hook입니다.

### `useMemo(calculateBalue, dependencies)`

```jsx
import { useMemo } from 'react';

function TodoList({ todos, tab }) {
  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab]
  );
  // ...
}
```

- **매개변수**

  - `calculateValue`

    - 캐싱하려는 값을 계산하는 함수입니다.
    - React는 초기 렌더링 중에 함수를 호출하고, 다음 렌더링에서 마지막 렌더링 이후 `dependencies`가 변경되지 않았을 때 동일한 값을 다시 반환합니다. 그렇지 않다면 `calculateValue`를 호출하고 결과를 반환하며 나중에 재사용할 수 있도록 저장합니다.

  - `dependencies`

    - `calculateValue` 함수 내에서 참조된 props, state와 컴포넌트 바디에 직접 선언된 모든 변수와 함수 등 반응형 값들의 목록입니다.
    - React는 `Object.is` 비교를 통해 각 의존성 들을 이전 값과 비교합니다.

- **반환값**

  - 초기 렌더링에서 `useMemo`는 인자 없이 `calculateValue`를 호출한 결과를 반환합니다.
  - 다음 렌더링에서, 마지막 렌더링에서 `dependencies`가 변경되지 않은 경우 저장된 값을 반환하거나, 변경된 경우 `calculateValue`를 다시 호출하고 반환된 값을 저장합니다.

- **주의사항**

  - `useMemo`은 Hook이므로 컴포넌트의 최상위 레벨 또는 커스텀 Hook에서만 호출할 수 있습니다.
  - React는 특별한 이유가 없는 한 **캐시된 함수를 삭제하지 않습니다**.

<br />

### 사용법

- **비용이 높은 로직의 재계산 생략하기**

  리렌더링 사이에 계산을 캐싱하기 위해 컴포넌트 최상위 레벨에서 `useMemo`를 호출해 계산을 감싸면 됩니다.

  ```jsx
  import { useMemo } from 'react';
  
  function TodoList({ todos, tab, theme }) {
    const visibleTodos = useMemo(() => filterTodos(todos, tab), [todos, tab]);
    // ...
  }
  ```

  초기 렌더링에서 `useMemo`에서 얻을 수 있는 값은 `calculateValue`를 호출한 결과값입니다. 이후 모든 렌더링에서 React는 `dependencies`를 마지막 렌더링 중에 전달한 `dependencies` 목록과 비교합니다. `dependencies`가 변경되지 않았다면 `useMemo`는 이미 계산해둔 값을 반환하고 그렇지 않다면 계산을 다시 실행해 새로운 값을 반환합니다.

  기본적으로 React는 컴포넌트를 다시 렌더링할 때마다 컴포넌트의 전체 본문을 다시 실행합니다. 예를 들어 `TodoList`가 상태를 업데이트하거나 부모로부터 새로운 props를 받으면 `filterTodos` 함수가 다시 실행됩니다.

  ```jsx
  function TodoList({ todos, tab, theme }) {
    const visibleTodos = filterTodos(todos, tab);
    // ...
  }
  ```

  일반적으로 대부분의 계산이 빠르게 진행되지만, 큰 배열을 필터링 혹은 변환하거나 비용이 많이 드는 계산을 수행하는 경우, 데이터가 변경되지 않았다면 계산을 생략하는 것이 좋습니다. 만약 `todos`와 `tab`이 마지막 렌더링 때와 동일한 경우, 위에서 살펴본 것처럼 `useMemo`로 계산을 감싸면 이전에 계산된 `visibleTodos` 값을 재사용할 수 있습니다. 이러한 유형의 캐싱을 **메모이제이션**이라고 합니다.

  > 🔍 **`useMemo`의 사용**
  >
  > `useCallback`으로 함수를 캐싱하는 것은 몇 가지 경우에만 가치 있습니다.
  >
  > - `useMemo`에 입력하는 계산이 눈에 띄게 느리고 종속성이 거의 변경되지 않는 경우
  > - `memo`로 감싸진 컴포넌트에 prop으로 전달할 경우
  > - 전달한 값을 나중에 일부 Hook의 종속성으로 사용할 경우

<br />

## :book: 참고

- [공식문서 - useMemo](https://ko.react.dev/reference/react/useMemo)
