# useCallback이란?

<br />

## `useCallback`

`useCallback`은 리렌더링 간에 **함수 정의를 캐싱**해 주는 React Hook입니다.

### `useCallback(fn, dependencies)`

```jsx
import { useCallback } from 'react';

export default function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);

  ...

}
```

- **매개변수**

  - `fn`

    - 캐싱할 함수값으로, 이 함수는 어떤 인자나 반환값을 가질 수 있습니다.
    - React는 첫 렌더링에서 이 함수를 **반환**합니다.
    - 다음 렌더링에서 `dependencies` 값이 이전과 같다면 React는 같은 함수를 다시 반환하고, `dependencies` 값이 변경되었다면 이번 렌더링에서 전달한 함수를 반환하고 나중에 재사용할 수 있도록 이를 저장합니다.
    - 이 함수의 호출 여부와 호출 시점은 개발자가 결정할 수 있도록 React는 반환만 합니다. 

  - `dependencies`

    - `fn` 내에서 참조되는 props, state, 컴포넌트 내에서 선언된 모든 변수와 함수를 포함한 **반응형 값**의 목록입니다.
    - React는 `Object.is` 비교 알고리즘을 이용해 각 의존성을 이전 값과 비교합니다.

- **반환값**

  - 최초 렌더링에서 `useCallback`은 전달한 `fn` 함수를 그대로 반환합니다.
  - 후속 렌더링에서 이전 렌더링에서 이미 저장해두었던 `fn` 함수를 반환하거나, 현재 렌더링 중에 전달한 `fn` 함수를 그대로 반환합니다.

- **주의사항**

  - `useCallback`은 Hook이므로 컴포넌트의 최상위 레벨 또는 커스텀 Hook에서만 호출할 수 있습니다.
  - React는 특별한 이유가 없는 한 **캐시된 함수를 삭제하지 않습니다**. 
    
<br />

### 사용법

- **컴포넌트의 리렌더링 건너뛰기**

  렌더링 성능을 최적화할 때 자식 컴포넌트에게 넘기는 함수를 캐싱할 필요가 있습니다. 위에서 정의한 `handleSubmit` 함수를 `ProducgPage`에서 `ShippingForm` 컴포넌트로 전달한다고 가정해봅니다.

  ```jsx
  function ProductPage({ productId, referrer, theme }) {
  // ...
  return (
    <div className={theme}>
      <ShippingForm onSubmit={handleSubmit} />
    </div>
  );
  ```

  `theme` prop을 토글하면 앱이 잠시 멈추는 듯한 현상이 보이는데, JSX에서 <ShippingForm />을 제거하면 앱이 빨라진 것처럼 느껴지는 것을 보니 `ShippingForm` 컴포넌트의 최적화를 시도해 볼 가치가 있다는 것을 나타냅니다.

  **컴포넌트가 리렌더링할 때 React가 해당 컴포넌트의 모든 자식을 재귀적으로 렌더링**합니다. 따라서 `ProductPage`가 `theme` 값으로 리렌더링될 때`ShippingForm` 또한 리렌더링됩니다. `ShippingForm`을 `memo`로 감싸면 마지막 렌더링과 동일한 props일 때 리렌더링을 건너뛰도록 할 수 있습니다.

  ```jsx
  import { memo } from 'react';
  
  const ShippingForm = memo(function ShippingForm({ onSubmit }) {
    // ...
  });
  ```

  이렇게 변경한 `ShippingForm`은 모든 props가 마지막 렌더링과 같다면 리렌더링을 건너뜁니다. 하지만 JavaScript에서 **`function () {}`나 `() => {}`은 항상 다른 함수를 생성**합니다. 따라서 `ShippingForm`의 props로 전달하는 함수는 **절대 같을 수 없고** `memo` 최적화가 동작하지 않을 것입니다. 이 때 `useCallback`을 사용합니다.

  ```jsx
  function ProductPage({ productId, referrer, theme }) {
    // React에게 리렌더링 간에 함수를 캐싱하도록 요청합니다...
    const handleSubmit = useCallback((orderDetails) => {
      post('/product/' + productId + '/buy', {
        referrer,
        orderDetails,
      });
    }, [productId, referrer]); // ...이 의존성이 변경되지 않는 한...
  
    return (
      <div className={theme}>
        {/* ...ShippingForm은 같은 props를 받게 되고 리렌더링을 건너뛸 수 있습니다.*/}
        <ShippingForm onSubmit={handleSubmit} />
      </div>
    );
  }
  ```

  `handleSubmit`을 `useCallback`으로 감쌈으로써 리렌더링 간에 **의존성이 변경되지 않는다면 같은 함수라는 것을 보장**합니다. 이 예시에서는 `memo`로 감싼 컴포넌트에 전달하기 때문에 해당 함수가 리렌더링을 건너뛸 수 있게 됩니다.

> [!IMPORTANT]
>
> **`useCallback`은 성능 최적화 용도로만 사용해야 합니다!**

<br />
    
> 🔍 **`useCallback`과 `useMemo`**
>
> 두 Hook 모두 자식 컴포넌트를 최적화할 때 유용합니다. 무언가를 전달할 때 memoization을 할 수 있도록 해 줍니다. 차이점은 **무엇을 캐싱하는지**입니다.
>
> - **`useMemo`**
>
>   - `useMemo`는 **호출한 함수의 결과값을 캐싱**하고 `dependencies`가 변경되지 않는 한 이 값이 변경되지 않도록 합니다.
>   - 필요할 때 React는 렌더링에 넘겨주었던 함수를 호출해 결과를 계산합니다.
>
> - **`useCallback`**
>
>   - `useCallback`은 **함수 자체를 캐싱**합니다.
>   - `useMemo`와 달리 전달한 함수를 호출하지 않지만, 전달한 함수를 캐싱합니다.

<br />

> 🔍 **`useCallback`의 사용**
>
> `useCallback`으로 함수를 캐싱하는 것은 몇 가지 경우에만 가치 있습니다.
>
> - `memo`로 감싸진 컴포넌트에 prop으로 넘기는 경우
> - 넘긴 함수가 나중에 어떤 Hook의 의존성으로 사용되는 경우
>
> 다른 경우에서 `useCallback`으로 함수를 감싸는 것은 아무런 이익이 없습니다. `useCallback`이 함수의 **생성을 막는 것은 아니**고, 함수를 생성은 하지만 변경이 없는 경우 이를 무시하고 캐시된 함수를 반환하는 것입니다.

<br />

- **Memoized 콜백에서 상태 업데이트하기**

  때때로 memoized 콜백에서 이전 상태를 기반으로 상태를 업데이트해야 하는 경우가 있습니다. 하지만 memoized 함수가 가능한 적은 의존성을 가지는 것이 좋으므로 다음 상태를 계산하기 위해 어떤 상태를 읽는 경우, 업데이트 함수를 대신 넘겨줌으로써 의존성을 제거할 수 있습니다.

  ```jsx
  function TodoList() {
    const [todos, setTodos] = useState([]);
  
    const handleAddTodo = useCallback((text) => {
      const newTodo = { id: nextId++, text };
      setTodos([...todos, newTodo]); // ❌
      setTodos(todos => [...todos, newTodo]); // ✅
    }, [todos]); 
    // ...
  ```

<br />

- **커스텀 Hook 최적화하기**

  커스텀 Hook을 작성하는 경우, 반환하는 모든 함수를 `useCallback`으로 감싸는 것이 좋습니다.

<br />

## :book: 참고

- [공식문서 - useCallback](https://ko.react.dev/reference/react/useCallback)
