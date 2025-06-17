# Controlled Component와 Uncontrolled Component의 차이점은?

<br />

## ⛓️ Controlled Component

Controlled Component는 React 상태(State)를 통해 입력값을 제어하는 컴포넌트입니다. 이 방식에서는 입력 요소의 값(`value`)를 React의 State와 동기화하고, 사용자가 입력을 변경할 때마다 `onChange` 이벤트 핸들러를 통해 업데이트합니다.

Controlled Component는 값이 React의 State로 관리되므로 입력 시마다 값을 검증하거나 자유롭게 변경할 수 있으며, 복잡한 `form` 로직 처리에 유용합니다. 값을 입력할 때마다 유효성 검증을 실시간으로 해 주어야 하는 경우 Controlled Component를 사용해야 합니다.

<br />

## ⛓️‍💥 Uncontrolled Component

Uncontrolled Component는 입력 값을 React의 State로 관리하지 않고, DOM을 통해 입력 값을 제어하는 방식입니다. 입력 요소의 값(`value`)는 DOM에서 직접 관리되며 React가 이를 제어하지 않습니다. 이 방식에서는 `useRef`를 사용하고 참조 객체인 Ref를 통해 DOM 요소에 직접 접근하여 값을 읽거나 조작합니다.

Uncontrolled Component는 React 상태 관리에 따른 성능 비용이 들지 않으므로 상대적으로 간단한 `form`에서 주로 사용됩니다. 사용자가 제출 버튼을 클릭했을 때만 입력 값을 가져오는 경우처럼 입력 필드가 단순한 `form`에서는 입력 요소의 값을 React의 State로 관리할 필요성이 적으므로 Uncontrolled Component를 사용하는 것이 더 간단하고 성능이 좋을 것입니다.
