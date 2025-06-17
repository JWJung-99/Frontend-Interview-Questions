# useRef란?

<br />

## 📚 Ref

컴포넌트가 일부 정보를 "기억"하고 싶지만, 해당 정보가 렌더링을 유발하지 않도록 하기 위해 Ref를 사용합니다. State와 달리 Ref는 읽고 수정할 수 있는 `current` 프로퍼티를 가진 일반 JavaScript 객체입니다. `ref.current` 프로퍼티를 통해 해당 Ref의 `current` 값에 접근할 수 있으며, 이 값을 의도적으로 변경할 수 있으므로 읽고 쓸 수 있습니다.

하지만 컴포넌트는 값의 변화에 대하여 **다시 렌더링되지 않습니다**. State와 마찬가지로 Ref도 리렌더링 시 값이 유지되지만, State를 설정하면 컴포넌트가 다시 렌더링되는 반면 Ref를 변경하면 다시 렌더링되지 않습니다.

|**Ref**|**State**|
|---|---|
|`useRef(initialValue)` 는 `{ current: initialValue }`를 반환합니다.|`useState(initialValue)`는 State 변수의 현재 값과 Setter 함수 `[value, setValue]`를 반환합니다.|
|State를 바꿔도 리렌더링 하지 않습니다.|State를 바꾸면 리렌더링 합니다.|
|Mutable: 렌더링 프로세스 외부에서 `current` 값을 수정 및 업데이트할 수 있습니다.|Immutable: State를 수정하기 위해서는 State 설정 함수를 반드시 사용하여 리렌더링 대기열에 넣어야 합니다.|
|렌더링 중에는 current 값을 읽거나 쓰면 안 됩니다.|언제든지 State를 읽을 수 있습니다. 그러나 각 렌더링마다 변경되지 않는 자체적인 State의 Snapshot이 있습니다.|

일반적으로 컴포넌트가 일부 값을 저장해야 하지만 렌더링 로직에 영향을 미치지 않는 경우, Ref를 선택합니다. Ref를 사용하는 몇 가지 특별한 상황은 다음과 같습니다.
- Timeout ID 저장
- DOM 엘리먼트 저장 및 조작
- JSX를 계산하는데 필요하지 않은 다른 객체 저장

렌더링 중 일부 정보가 필요한 경우 `ref.current` 값을 읽거나 쓰지 않아야 합니다. React는 `ref.current`가 언제 변하는지 모르기 때문에 렌더링 시에 값을 읽어도 컴포넌트의 동작을 예측하기 어렵습니다. 이 경우에는 State를 사용해야 합니다.

<br />

## 📑 `useRef`

`useRef`는 렌더링에 필요하지 않은 값을 Ref로 참조할 수 있는 사용합니다.

### `useRef(initialValue)`

- 컴포넌트의 최상위 레벨에서 `useRef`를 호출해 Ref를 선언합니다.

  ```js
  import { useRef } from 'react';
  
  function MyComponent() {
    const intervalRef = useRef(0);
    const inputRef = useRef(null);
    // ...
  ```

- **매개변수**

  - `initialValue`는 Ref 객체의 `current` 프로퍼티의 초기 설정값으로, 어떤 유형의 값이든 지정할 수 있으며 초기 렌더링 이후에는 무시됩니다.

- **반환값**

  - `useRef`는 `current`라는 단일 프로퍼티를 가진 객체를 반환합니다.
  - `current`는 처음에는 전달한 `initialValue`로 설정되며 나중에 다른 값으로 바꿀 수 있습니다. Ref 객체를 JSX 노드의 `ref` 어트리뷰트로 전달하면 React는 `current` 프로퍼티를 설정합니다.
  - 다음 렌더링 시 `useRef`는 동일한 객체를 반환합니다.

### 사용법

- **Ref로 값 참조**

  - Ref는 변경하더라도 리렌더링을 촉발하지 않는다는 점에서 State와는 차이가 있습니다. 따라서 Ref는 **컴포넌트의 시각적 출력에 영향을 미치지 않는 정보를 저장**하는데 적합합니다.
  - Ref를 사용하면 다음을 보장합니다.
    
    - 리렌더링 사이에 정보를 저장할 수 있습니다.
    - 변경해도 리렌더링을 촉발하지 않습니다.
    - 각각의 컴포넌트에 로컬로 저장됩니다.

- **Ref로 DOM 조작**

  - 일반적으로 Ref를 사용해 DOM을 조작합니다. 먼저 초기값이 `null`인 Ref 객체를 선언하고, Ref 객체를 조작하려는 DOM 노드의 JSX에 `ref` 속성으로 전달합니다.

    ```jsx
    import { useRef } from 'react';

    function MyComponent() {
      const inputRef = useRef(null);
      // ...
      return <input ref={inputRef} />;
     }
    ```

  - React가 DOM 노드를 생성하고 화면에 그린 후, React는 Ref 객체의 `current` 프로퍼티를 DOM 노드로 설정해 DOM 노드에 접근, 조작할 수 있습니다.
  - 노드가 화면에서 제거되면 React는 `current` 프로퍼티를 다시 `null`로 설정합니다.

### 주의사항

- **렌더링 중 `ref.current`를 읽거나 쓰지 않아야 합니다.**

  - React는 컴포넌트의 본문이 순수함수처럼 동작하기를 기대합니다.
    
    ```js
    function MyComponent() {
      // ...
      // 🚩 Don't write a ref during rendering
      myRef.current = 123;
      // ...
      // 🚩 Don't read a ref during rendering
      return <h1>{myOtherRef.current}</h1>;
    }
    ```
    
  - 따라서 이벤트 핸들러나 Effect에서 `ref.current`를 읽거나 쓸 수 있습니다.

    ```js
    function MyComponent() {
      // ...
      useEffect(() => {
        // ✅ You can read or write refs in effects
        myRef.current = 123;
      });
      // ...
      function handleClick() {
        // ✅ You can read or write refs in event handlers
        doSomething(myOtherRef.current);
      }
      // ...
    }
    ```
    
  - 렌더링 중 무언가를 읽거나 써야 하는 경우라면 **State를 사용해야 합니다**.

- **커스텀 컴포넌트에 대한 Ref**

  - 아래 코드와 같이 커스텀 컴포넌트에 `ref`를 전달하고자 하면 오류가 발생할 것입니다.

    ```jsx
    const inputRef = useRef(null);
    // ...
    return <MyInput ref={inputRef} />;
    ```

  - 기본적으로 컴포넌트는 내부의 DOM 노드에 대한 Ref를 외부로 노출하지 않습니다. 이 문제를 해결하려면 컴포넌트의 props에 `ref`를 추가한 후, 내장 컴포넌트에 `ref`를 전달하여 가져올 수 있습니다.

    > **React 19 업데이트**
    > 
    > React 19부터 함수 컴포넌트의 Prop으로 `ref`를 전달할 수 있습니다. 더이상 `forwardRef`를 사용해 자식 컴포넌트에 `ref`를 전달하지 않아도 됩니다!

    ```jsx
    const inputRef = useRef(null);
    // ...
    return <MyInput ref={inputRef} />;
    
    ...
    
    function MyInput({ value, onChange, ref }) {
      return (
        <input
          value={value}
          onChange={onChange}
          ref={ref}
        />
      );
    };
    
    export default MyInput;
    ```

<br />

## 📖 참고

- [공식문서 - Ref로 값 참조하기](https://ko.react.dev/learn/referencing-values-with-refs)
- [공식문서 - useRef](https://ko.react.dev/reference/react/useRef)
