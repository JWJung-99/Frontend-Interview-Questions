# useEffect란?

<br />

## 🪝 `useEffect`

`useEffect`는 외부 시스템과 컴포넌트를 동기화하는 React Hook입니다. `useEffect`를 사용하면 컴포넌트가 **마운트될 때**, **업데이트될 때**, **마운트 해제될 때** 특정 작업을 처리할 수 있습니다.

`useEffect`를 사용하며 클래스형 컴포넌트에서 사용할 수 있었던 [생명주기 메서드](https://github.com/JWJung-99/Frontend-Interview-Questions/blob/main/Notes/React/React-Lifecycle.md)를 함수형 컴포넌트에서도 사용할 수 있게 되었습니다.

|**React 생명주기**|
|:---:|
|<img alt="image" src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FJ6VPG%2FbtreOs3fnFB%2FJ3PD4pk5vQfiHtSw0BKA51%2Fimg.png" />|

<br />

### `useEffect(setup, dependencies?)`

컴포넌트의 최상위 레벨에서 `useEffect`를 호출하여 Effect를 선언할 수 있습니다.

```js
import { useEffect } from 'react';

function MyComponent() {

  useEffect(() => {
    console.log('useEffect 셋업 함수입니다.');

    return () => {
      console.log('useEffect 클린업 함수입니다.');
    }
  }, [dependencies]);

  // ...
}
```

- **매개변수**

  - `setup`

    - Effect의 로직이 포함된 함수로, 선택적으로 클린업 함수를 반환할 수 있습니다.
    - React는 컴포넌트가 DOM에 추가(마운트)된 이후에 셋업 함수를 실행합니다. 이후 의존성의 변화에 따라 컴포넌트가 리렌더링 되었을 경우, React는 이전 렌더링에 사용된 값으로 클린업 함수를 실행한 후 새로운 값으로 셋업 함수를 실행합니다.
    - 컴포넌트가 DOM에서 제거(마운트 해제)된 경우에도 클린업 함수를 실행합니다.

  - `dependencies` (option)

    - 셋업 함수의 코드 내부에서 참조되는 props, state, 모든 변수 및 컴포넌트에 선언된 함수 등 **모든 반응형 값들이 포함된 배열**로 구성됩니다.
    - React는 각각의 의존성을 `Object.js` 비교를 통해 이전값과 비교하며, 의존성을 생략할 경우 컴포넌트가 리렌더링될 때마다 Effect가 실행됩니다.

- **반환값**

  - `useEffect`는 `undefined`를 반환합니다.

- **주의사항**

  - `useEffect`는 Hook이므로 컴포넌트의 최상위 또는 커스텀 Hook에서만 호출할 수 있습니다.
    
  - 외부 시스템과 컴포넌트를 동기화 할 필요가 없는 경우 Effect를 선언할 필요가 없을 수 있습니다.
    
  - 의존성이 객체이거나 컴포넌트 내부에 선언된 함수일 경우 Effect가 필요 이상으로 재실행될 수 있으므로, 객체나 함수를 Effect 내부에서 선언함으로써 불필요한 객체 의존성이나 함수 의존성을 제거해야 합니다.
    
  - React는 일반적으로 Effect를 실행하기 전에 브라우저가 업데이트된 화면을 먼저 렌더링하도록 합니다. 따라서 다음의 경우 `useEffect` 대신 `useLayoutEffect`를 사용해야 합니다.
    
    - Effect가 사용자 상호작용에 의해 발생하지 않았고, Effect가 시각적인 작업을 수행하며 이에 따라 지연이 눈에 띄게 나타나는 경우
      
    - Effect가 사용자 상호작용에 의해 발생했지만, 브라우저가 화면을 다시 그리지 않도록 막아야 하는 경우
      
  - 여러 컴포넌트에서 Effect를 자주 작성해야 한다면 컴포넌트가 의존하고 있는 공통적인 동작을 커스텀 Hook으로 추출하는 것이 좋습니다.
    
  - 컴포넌트에 데이터를 페칭하기 위해 Effect를 사용할 수 있지만, Effect에서 직접 데이터 페칭 로직을 작성하면 나중에 캐싱 기능이나 서버 렌더링과 같은 최적화를 추가하기 어려워지므로 자체 제작한 커스텀 Hook이나 커뮤니티에 의해 유지보수되는 Hook을 사용하는 편이 좋습니다.

  <br />
  
  > **서버와 클라이언트의 컨텐츠가 다른 경우**
  >
  > 앱이 서버 사이드 렌더링(SSR)을 사용하는 경우 컴포넌트는 초기 HTML을 생성하기 위해 서버에서 렌더링되고, React가 이벤트 핸들러를 해당 HTML에 연결하기 위해 클라이언트에서 다시 한 번 렌더링됩니다. 이를 **hydration**이라고 부르는데, 따라서 hydration이 작동하기 위해서는 초기 렌더링 출력이 서버와 클라이언트에서 동일해야 합니다.
  >
  > 드물게, 클라이언트에서 `localStorage`에 접근해야 하는 등 다른 내용을 표시해야 할 수 있습니다. 이는 서버에서 구현할 수 없기 때문에, `didMount`라는 상태를 이용해 서버 렌더링을 위한 JSX와 클라이언트 전용 JSX를 구분하여 반환하는 방법을 사용할 수 있습니다.
  > ```jsx
  > function MyComponent() {
  >   const [didMount, setDidMount] = useState(false);
  >
  >   useEffect(() => {
  >       setDidMount(true);
  >   }, []);
  >
  >   if (didMount) {
  >     // ... 클라이언트 전용 JSX 반환 ...
  >   }  else {
  >     // ... 초기 JSX 반환 ...
  >   }
  > }
  > ```

<br />

## 📖 참고

- [공식문서 - useEffect](https://ko.react.dev/reference/react/useEffect)
- [React 생명주기 메서드](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)
- [React Hooks : useEffect() 함수](https://xiubindev.tistory.com/100)
