# Lazy란?

<br />

## 🦥`lazy`

`lazy`를 사용하면 컴포넌트가 처음 렌더링될 때까지 해당 컴포넌트의 코드를 로딩하는 것을 지연할 수 있습니다.

<br />

### `lazy(load)`

`lazy`를 컴포넌트 외부에서 호출하여 지연 로딩된 React 컴포넌트를 선언합니다.

```jsx
import { lazy } from 'react';

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
```

- **매개변수**

  - `load`: Promise 혹은 다른 *thenable*(`then` 메서드가 있는 Promise 유사 객체)을 반환하는 함수입니다.
    
    - **매개변수**
      - `load`는 매개변수를 받지 않습니다.
    
    - **반환값**
      - Promise 혹은 다른 *thenable*(`then` 메서드가 있는 Promise 유사 객체)을 반환합니다. `.default` 프로퍼티가 함수, `memo`, `forwardRef` 컴포넌트와 같은 유효한 React 컴포넌트 유형인 객체로 해석되어야 합니다.
    - React는 반환된 컴포넌트를 처음 렌더링하려고 시도할 때까지 `load`를 호출하지 않습니다. React가 처음으로 `load`를 호출하면, 그것이 `resolve` 될 때까지 기다리고, 그 후 `resolve` 된 값의 `.default`를 React 컴포넌트로 렌더링합니다.
    - 반환된 Promise와 Promise의 `resolve` 된 값은 캐시되므로, React는 `load`를 한 번만 호출합니다.
    - 만약 Promise가 `reject`되면, React는 그 이유를 가장 가까운 에러 바운더리가 처리할 수 있도록 `throw` 합니다.

- **반환값**

  - `lazy`는 트리에 렌더링할 수 있는 React 컴포넌트를 반환합니다.
  - 컴포넌트의 코드가 여전히 로딩되는 동안 렌더링을 시도하면 일시 중지됩니다. 로딩 중 Loading Indicator를 표시하려면 `<Suspense>`를 사용하면 됩니다.

<br />

### 사용법

- **Suspense와 지연 로딩 컴포넌트**

  일반적으로 정적 `import` 선언으로 컴포넌트를 가져옵니다.

  ```jsx
  import MarkdownPreview from './MarkdownPreview.js';
  ```

  해당 컴포넌트 코드가 처음 렌더링 될 때까지 로드하는 것을 연기하려면 `import`를 다음과 같이 대체합니다.

  ```jsx
  import { lazy } from 'react';

  const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
  ```

  이제 요청에 따라 컴포넌트의 코드가 로딩되므로, 로딩하는 동안 표시할 항목도 지정해야 합니다. `lazy` 컴포넌트 또는 해당 부모 컴포넌트 중 하나를 `<Suspense>` 경계로 감싸서 이 작업을 수행할 수 있습니다.

  ```jsx
  <Suspense fallback={<Loading />}>
    <h2>Preview</h2>
    <MarkdownPreview />
  </Suspense>
  ```

<br />

### 문제 해결

- **`lazy` 컴포넌트를 다른 컴포넌트 내부에서 선언하면 안 됩니다.**

  ```jsx
  import { lazy } from 'react';
  
  function Editor() {
    // 🔴 잘못된 방법: 이렇게 하면 다시 렌더링할 때 모든 상태가 재설정됩니다.
    const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
    // ...
  }
  ```

  항상 모듈의 최상위 수준에서 선언해야 합니다.

  ```jsx
  import { lazy } from 'react';
  
  // ✅ 올바른 방법: `lazy` 컴포넌트를 컴포넌트 외부에 선언합니다.
  const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'));
  
  function Editor() {
    // ...
  }
  ```

<br />

## 📖 참고

- [공식문서 - `lazy`](https://ko.react.dev/reference/react/lazy)
