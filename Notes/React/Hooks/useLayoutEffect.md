# useLayoutEffect란?

<br />

## `useLayoutEffect`

`useLayoutEffect`는 **브라우저가 화면을 다시 그리기 전에 실행**되는 `useEffect`입니다.

<br />

### `useLayoutEffect(setup, dependencies?)`

```jsx
import { useState, useRef, useLayoutEffect } from 'react';

function Tooltip() {
  const ref = useRef(null);
  const [tooltipHeight, setTooltipHeight] = useState(0);

  useLayoutEffect(() => {
    const { height } = ref.current.getBoundingClientRect();
    setTooltipHeight(height);
  }, []);

  // ...
  
}
```

- **매개변수**

  - `setup`

    - Effect의 로직이 포함된 함수로 선택적으로 cleanup 함수를 반환할 수 있습니다.
    - 컴포넌트가 **DOM에 추가되기 전**에 React는 셋업 함수를 실행합니다. 의존성이 변경되어 다시 렌더링될 때마다 React는 먼저 이전 값으로 클린업 함수를 실행한 후 새로운 값으로 셋업 함수를 실행합니다.
    - 컴포넌트가 DOM에서 제거(마운트 해제)되기 전에 클린업 함수를 실행합니다.

  - `dependencies` (option)

    - 셋업 함수의 코드 내부에서 참조되는 props, state, 모든 변수 및 컴포넌트에 선언된 함수 등 **모든 반응형 값들이 포함된 배열**로 구성됩니다.
    - React는 각각의 의존성을 `Object.js` 비교를 통해 이전값과 비교하며, 의존성을 생략할 경우 컴포넌트가 리렌더링될 때마다 Effect가 실행됩니다.

- **반환값**

  - `useLayoutEffect`는 `undefined`를 반환합니다.
 
- **주의사항**

  - `useLayoutEffect`는 Hook이므로 컴포넌트의 최상위 또는 커스텀 Hook에서만 호출할 수 있습니다.
  - 의존성이 객체이거나 컴포넌트 내부에 선언된 함수일 경우 Effect가 필요 이상으로 재실행될 수 있으므로, 객체나 함수를 Effect 내부에서 선언함으로써 불필요한 객체 의존성이나 함수 의존성을 제거해야 합니다.
  - `useLayoutEffect` 내부의 코드와 이로 인한 모든 state 업데이트는 **브라우저가 화면을 다시 그리는 것을 막아** 과도하게 사용하면 앱이 느려집니다.

<br />

### 사용법

- **브라우저가 화면을 다시 그리기 전에 레이아웃 계산하기**

  대부분의 컴포넌트는 렌더링을 위해 해당 컴포넌트의 화면상 위치와 크기를 알 필요가 없습니다. 컴포넌트가 JSX를 반환하면 브라우저가 컴포넌트의  레이아웃을 계산하고 화면을 다시 그립니다. 툴팁을 사용자가 확인하기 전에 올바른 위치에 렌더링하는 경우 등, 가끔은 **브라우저가 화면을 다시 그리기 전에 레이아웃을 계산**해야할 때도 있습니다. 이 때 `useLayoutEffect`를 사용합니다.

  ```jsx
  function Tooltip() {
    const ref = useRef(null);
    const [tooltipHeight, setTooltipHeight] = useState(0); // 아직 실제 높이를 모릅니다.
  
    useLayoutEffect(() => {
      const { height } = ref.current.getBoundingClientRect();
      setTooltipHeight(height); // 실제 높이를 알았으니 다시 렌더링합니다.
    }, []);
  
    // ...아래에 올 렌더링 로직에서 tooltipHeight를 사용하세요...
  }
  ```

  - 위 예시에서는 `useLayoutEffect`를 활용해 브라우저가 화면을 그리기 전에 툴팁의 높이를 계산한 후 다시 렌더링하여 적절한 위치에 툴팁을 표시합니다.
  
<br />

## 📖 참고

- [공식문서 - useLayoutEffect](https://ko.react.dev/reference/react/useLayoutEffect)
