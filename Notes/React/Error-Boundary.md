# 에러 바운더리란?

<br />

## 🐛 에러 바운더리

에러 바운더리(Error Boundary)는 React 컴포넌트에서 발생하는 **오류를 잡아내고**, **전체 애플리케이션이 다운되는 것을 방지하기 위한 특수한 컴포넌트**입니다. 일반적으로 클라이언트에서 오류가 발생할 때 표시할 UI를 제공하여 애플리케이션의 신뢰성과 사용자 경험을 높이는데 활용됩니다. 클래스형 컴포넌트의 `conponentDidCatch`와 `getDerivedStateFromError` 두 가지 라이프사이클 메서드를 이용해 오류 발생 시의 행동을 정의할 수 있습니다.

### 에러 바운더리가 필요한 이유

React는 기본적으로 비동기 작업에서 발생하는 오류를 자동으로 처리하지 않으므로, 오류가 발생할 경우 페이지 전체가 하얗게 변하거나 사용자 입장에서 알 수 없는 화면이 표시될 수 있습니다. 이는 사용자 경험을 크게 저해하고 신뢰성에 큰 문제가 됩니다. 에러 바운더리는 **에러가 발생한 영역에서 대체 UI를 표시하고, 애플리케이션의 나머지 부분은 정상적으로 동작**하게 해 줍니다. 에러 바운더리를 적절히 배치하면 오류가 발생한 컴포넌트만 대체 UI로 전환되어 애플리케이션의 안정성을 유지하고 더 나은 사용자 경험을 제공할 수 있습니다. 더불어 에러 바운더리는 오류 발생 시의 대체 UI 로직을 <sup>*</sup>선언형으로 작성할 수 있게 하여 코드의 가독성과 유지보수성을 높이는 데 도움이 됩니다.

> **<sup>*</sup> 선언형**
>
> 선언형으로 처리한다는 의미는 **개발자가 무엇을 해야 하는지 정의**한다는 것으로, 어떻게 할지에 대해 세부적인 절차를 직접 작성하지 않아도 된다는 것을 의미합니다. 에러 바운더리에서 "이 컴포넌트가 오류를 감지하면 특정 대체 UI를 보여준다"와 같은 목적을 코드 상에 명시함으로써 실제로 오류 발생 시 실행되는 세부적인 절차는 컴포넌트가 알아서 처리할 수 있도록 합니다.

<br />

## 📦 `react-error-boundary`

앞서 살펴본 에러 바운더리는 **클래스형 컴포넌트에서만 사용**할 수 있습니다. 또한 에러 바운더리는 다음과 같은 경우에는 오류를 잡아내지 못합니다.

- 이벤트 핸들러
- 비동기 코드
- 서버 측 렌더링
- 에러 바운더리 자체에서 발생한 오류

현재 React의 개발 트렌드는 함수형 컴포넌트를 사용하는 방식이므로 클래스형 컴포넌트와 그 생명주기 메서드를 사용해 에러 바운더리를 구현하기보다는 `react-error-boundary`라는 현대적인 접근 방식을 이용해 React 컴포넌트에서 오류를 유연하게 처리할 수 있습니다.

### `react-error-boundary`

`react-error-boundary` 라이브러리는 오류가 발생할 가능성이 있는 코드를 감싸는 데 사용할 수 있는 **`ErrorBoundary`라는 간단한 컴포넌트를 제공**합니다.

<img width="730" height="455" alt="image" src="https://github.com/user-attachments/assets/0a302fe4-1bad-446b-8488-308fab36ed73" />

<br />

다음은 `react-error-boundary`를 사용하는 예시입니다.

```jsx
import { ErrorBoundary } from "react-error-boundary";

function Fallback({ error, resetErrorBoundary }) {
  // Call resetErrorBoundary() to reset the error boundary and retry the render.

  return (
    <div role="alert">
      <p>문제가 발생했습니다:</p>
      <p>{error.message}</p>
      <button onClick={resetErrorBoundary}>retry</button>
    </div>
  );
}

function MyComponent() {
  // 오류가 발생할 수 있는 컴포넌트 로직
}

function App() {
  return (
    <ErrorBoundary
      FallbackComponent={Fallback}
      onReset={(details) => {
        // Reset the state of your app so the error doesn't happen again
      }}
    >
      <MyComponent />
    </ErrorBoundary>;
  );
}
```

`ErrorBoundary`의 `FallbackComponent` prop으로 전달한 `Fallback`은 `ErrorBoundary`가 오류를 감지했을 때 렌더링됩니다. `Fallback` 컴포넌트는 `error`와 `resetErrorBoundary`를 에러 객체와 에러 바운더리를 초기화할 수 있는 핸들러 함수를 prop으로 전달받아 사용할 수 있습니다.

<br />

### `useErrorBoundary`

`useErrorBoundary`는 `react-error-boundary`에서 제공하는 또다른 유용한 기능입니다. 이 Hook은 에러 바운더리를 쉽게 표시하고 해제할 수 있게 해주는 커스텀 Hook입니다. `showBoundary`로 던져진 에러는 가장 가까운 에러 바운더리에 의해 포착됩니다.

`useErrorBoundary`는 특히 **비동기 코드에서 유용**합니다. 이 경우 컴포넌트의 에러 바운더리로는 잡아낼 수 없는 에러를 잡아낼 수 있습니다.

```jsx
import { useErrorBoundary } from "react-error-boundary";

function Example() {
  const { showBoundary } = useErrorBoundary();

  useEffect(() => {
    fetchGreeting(name).then(
      response => {
        // Set data in state and re-render
      },
      error => {
        // Show error boundary
        showBoundary(error);
      }
    );
  });

  // Render ...
}

function App() {
  return (
    <ErrorBoundary FallbackComponent={Fallback}>
      <Example />
    </ErrorBoundary>
  );
}
```

위 예시에서 `fetchGreeting` 함수는 데이터를 가져오는 비동기 함수이며 오류가 발생할 경우 해당 오류를 `showBoundary` 함수에 전달해 `ErrorBoundary`가 이를 잡아낼 수 있도록 합니다.

> 🔍 **참고**
>
> Tanstack-Query의 `useQuery`를 사용하는 경우, `useQuery`의 `throwOnError` 옵션을 `true`로 설정하면 에러가 발생했을 때 자동으로 에러 바운더리가 실행됩니다. 에러를 던지기 위해서 `queryFn`에서 에러를 던지는 `throw Error` 구문을 이용해 에러를 전달하면 됩니다.

<br />

## :book: 참고

- [React의 Error Boundary를 이용하여 효과적으로 에러 처리하기](https://tech.kakaoent.com/front-end/2022/221110-error-boundary/)
- [react-error-boundary](https://www.npmjs.com/package/react-error-boundary)
- [react-error-boundary로 에러 핸들링하기](https://developer-haru.tistory.com/86)
