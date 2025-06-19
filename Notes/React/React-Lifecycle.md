# React 생명주기란?

<br />

## 🔄 React 생명주기

모든 React 컴포넌트는 동일한 생명주기를 거칩니다.

- 컴포넌트가 화면에 추가될 때 **마운트**됩니다.
- 컴포넌트가 일반적으로 상호작용에 대한 응답으로 새로운 props나 state를 수신하면 **업데이트**됩니다.
- 컴포넌트가 화면에서 제거되면 **마운트가 해제**됩니다.

<br />

## 🧮 생명주기 메서드

클래스형 컴포넌트에서는 각 단계마다 필요한 생명주기 메서드가 호출되며 각각의 메서드는 컴포넌트의 초기화와 설정에 필수적인 역할을 했습니다. 현재는 React가 클래스형 컴포넌트가 아닌 함수형 컴포넌트를 사용하면서 `useEffect` Hook을 사용해 이를 대체하고 있습니다.

### 마운트 시 호출되는 메서드

- `constructor`: 컴포넌트를 새로 만들 때마다 호출되는 클래스 생성자 메서드
- `getDerivedStateFromProps`: props에 있는 값을 state에 넣을 때 사용하는 메서드
- `render`: 준비한 UI를 렌더링하는 메서드
- `componentDidMount`: 컴포넌트가 웹 브라우저상에 나타난 후 호출하는 메서드

<br />

### 업데이트 시 호출되는 메서드

- `getDerivedStateFromProps`: props 변화에 따라 state 값에도 변화를 주고 싶을 때 사용하는 메서드
- `shouldComponentUpdate`: 컴포넌트가 리렌더링을 해야 할지 말아야 할지를 결정하는 메서드

  - `true`를 반환하면 다음 라이프 사이클 메서드를 계속 실행
  - `false`를 반환하면 작업을 중지(리렌더링 X)

- `render`: 컴포넌트를 리렌더링하는 메서드
- `getSnapshotBeforeUpdate`: 컴포넌트 변화를 DOM에 반영하기 바로 직전에 호출하는 메서드
- `componentDidUpdate`: 컴포넌트의 업데이트 작업이 다 끝난 후 호출하는 메서드

<br />

### 마운트 해제 시 호출되는 메서드

- `componentWillUnmount`: 컴포넌트가 웹 브라우저상에서 사라지기 전에 호출하는 메서드

<br />

## 📖 참고

- [React.js - 컴포넌트 생명주기(Life Cycle)](https://velog.io/@gyumin_2/React.js-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0Life-Cycle)
- [프론트엔드 면접 질문 정리 Part1(CS, JS,React)](https://velog.io/@developer-sora/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C-%EB%A9%B4%EC%A0%91-%EC%A7%88%EB%AC%B8-%EC%A0%95%EB%A6%AC-Part1CS-JSReact)
