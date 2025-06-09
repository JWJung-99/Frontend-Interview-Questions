# React Hook이란?

<br />

## 🧩 React Hook

React Hook은 함수형 컴포넌트에서 React state와 생명주기 기능(lifecycle features)을 연동할 수 있게 해 주는 함수입니다. Hook은 React 16.8 버전에 새로 추가되었으며, 덕분에 클래스형 컴포넌트 없이도 대부분의 기능을 사용할 수 있게 되었습니다.

<br />

## ⚠️ 클래스형 컴포넌트의 단점

- **컴포넌트 사이에서 상태 로직을 재사용하기 어렵습니다.**

  - React는 컴포넌트 간에 재사용 가능한 로직을 붙이는 방법을 제공하지 않아서 render props나 고차 컴포넌트(High Order Component, HOC) 등을 사용해 문제를 해결해야 했습니다. 이런 패턴의 사용은 **컴포넌트의 재구성을 강요하고, 코드의 추적을 어렵게 만듭니다.**
  
  - React 애플리케이션에서 providers, consumers, 고차 컴포넌트, render props, 추상화에 대한 레이어로 둘러싸인 **래퍼 지옥(wrapper hell)** 을 경험할 수 있습니다.

<br />

- **복잡한 컴포넌트들은 이해하기 어렵습니다.**

  - 클래스형 컴포넌트는 `constructor`, `this`, 바인딩 같은 규칙을 따라야 하며, 이로 인해 코드가 복잡하고 길어질 수 있습니다. 또한 컴포넌트가 복잡해질수록 상태 관련 로직들의 관리가 어려워지고, 사이드 이펙트가 있는 컴포넌트는 유지보수가 필요해지면서 버그가 쉽게 발생하고 무결성을 해칠 수 있습니다.

<br />

## 👍 Hook의 장점

- **컴포넌트로부터 상태 관련 로직을 추상화할 수 있습니다.**

  - Hook을 사용하는 함수형 컴포넌트에서는 필요한 로직을 커스텀 훅으로 만들고, 이를 필요한 곳에 삽입하여 사용할 수 있으므로 독립적인 재사용이 가능합니다.

<br />

- **가독성이 크게 향상됩니다.**

  - 복잡한 클래스형 컴포넌트를 사용할 필요가 없어지면서 코드의 가독성이 크게 증가합니다.

<br />

## 🚨 Hook의 규칙

- **최상위 레벨에서만 Hook을 호출해야 합니다.**

  **반복문, 조건문, 혹은 중첩된 함수 내에서 Hook을 호출하면 안 됩니다.** 대신 React 함수의 최상위에서 Hook을 호출해야 합니다. 이 규칙을 따르면 컴포넌트가 렌더링될 때마다 항상 동일한 순서로 Hook이 호출되는 것이 보장됩니다. 이러한 점은 React가 Hook을 여러 번 호출하는 중에도 Hook의 상태를 올바르게 유지할 수 있도록 해 줍니다.

  > 👀 **참고**
  >
  > 한 컴포넌트에서 State나 Effect Hook을 여러 개 사용할 수도 있습니다. 이 과정에서 React는 특정 state가 어떤 `useState` 호출인지 **호출 순서**에 따라 파악합니다. 이는 React가 Hook이 호출되는 순서에 의존하기 때문입니다. **모든 렌더링에서 Hook의 호출 순서가 같아야 올바르게 동작할 수 있습니다.**
  > 
  > 하지만 다음과 같이 Hook을 조건문에서 사용한다면 어떻게 될까요?
  > ```js
  > // 🔴 조건문에 Hook을 사용함으로써 첫 번째 규칙이 깨졌습니다.
  > if (name !== '') {
  >   useEffect(function persistForm() {
  >     localStorage.setItem('formData', name);
  >    });
  > }
  > ```
  > `name`이 `false`일 경우 렌더링 간에 Hook을 건너뛰게 되어 Hook의 호출 순서가 달라지게 될 것입니다. 그 시점부터 건너뛴 Hook 다음에 호출되는 Hook의 순서가 하나씩 밀리면서 버그를 발생시키게 됩니다. 만약 조건부로 Effect를 실행하고 싶다면, 조건문을 Hook "내부에" 넣어야 합니다.

<br />

- **오직 React 함수 내에서 Hook을 호출해야 합니다.**

  **Hook을 일반적인 JavaScript 함수에서 호출하면 안 됩니다.** 대신 아래와 같이 호출할 수 있습니다.

    - React 함수 컴포넌트에서 Hook을 호출합니다.
    - [Custom Hook]()에서 Hook을 호출합니다.

<br />

## 🪝 대표적인 Hooks

### State Hooks

State를 통해 컴포넌트가 정보를 **기억**할 수 있습니다.

- [`useState`]() : 직접 업데이트할 수 있는 state 변수를 선언합니다.
- [`useReducer`]() : reducer 함수 내부의 업데이트 로직을 사용해 state 변수를 선언합니다.

<br />

### Context Hooks

Context를 사용하면 컴포넌트가 다른 컴포넌트로부터 **props로 전달하지 않으면서** 정보를 받을 수 있습니다.

- [`useContext`]() : context를 읽고 구독합니다.

<br />

### Ref Hooks

Ref를 사용하면 컴포넌트가 **DOM 노드나 렌더링에 사용되지 않는 일부 정보**를 보유할 수 있습니다.

- [`useRef`]() : ref를 선언하며, 주로 DOM 노드를 담는데 사용됩니다.

<br />

### Effect Hooks

Effect를 통해 **컴포넌트를 외부 시스템에 연결하고 동기화**할 수 있습니다.

- [`useEffect`]() : 컴포넌트를 외부 시스템에 연결합니다.
- [`useLayoutEffect`]() : 브라우저가 화면을 다시 그리기 전에 실행됩니다.

<br />

### Performance Hooks

이전 렌더링 이후 데이터가 변경되지 않은 경우 **캐시된 계산을 재사용하거나 재렌더링을 건너뛸** 수 있습니다.

- [`useMemo`]() : 비용이 많이 드는 계산 결과를 캐시합니다.
- [`useCallback`]() : 함수 정의를 최적화된 컴포넌트에 전달하기 전에 캐시합니다.

<br />

### Other Hooks

대부분 라이브러리 작성자에게 유용하며 애플리케이션 코드에서는 일반적으로 사용되지 않습니다.

- [`useActionState`]() : (React 19~) 액션을 통해 상태를 관리합니다.


<br />

### Custom Hooks

JavaScript 함수로 나만의 Custom Hook을 정의할 수도 있습니다.

- [Custom Hooks]()

<br />

## 📖 참고

- [공식문서 - 내장된 React Hook](https://ko-react-exy5xcwjj-fbopensource.vercel.app/reference/react/hooks)
- [공식문서(Legacy) - Hook의 개요](https://ko.legacy.reactjs.org/docs/hooks-intro.html)
- [React Hooks란?](https://velog.io/@niboo/React-Hooks-%EB%9E%80)
- [React Hook](https://codingbroker.tistory.com/23)
