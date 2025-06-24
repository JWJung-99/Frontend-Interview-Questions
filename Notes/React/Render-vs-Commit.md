# Render phase와 Commit phase의 차이는?

<br />

## 📱 Render Phase

Render phase는 React가 변화된 상태나 props에 따라 **어떤 UI가 변경되어야 할 지 결정하는 단계**입니다. 이 과정에서 실제로 DOM을 업데이트 하지는 않고, **변경사항을 Virtual DOM에서 계산하여 비교**합니다. 이 단계는 순수한 계산과정으로 성능에 영향을 주지 않도록 중단되거나 다시 실행될 수 있으며, React 18에서 도입된 Concurrent Mode를 통해 비동기적으로 처리될 수도 있습니다.

<br />

## 📲 Commit phase

Commit phase는 **실제로 변화된 UI를 DOM에 반영**하는 단계입니다. 이때 React는 Virtual DOM에서 계산된 결과를 실제 DOM에 적용하고 변화된 UI를 브라우저에 렌더링합니다. DOM 업데이트 이후에 `useEffect`와 같이 side effect를 발생시키는 훅들이 실행됩니다.

<br />

## 🔄 동기화

Render phase가 완료되면 React는 즉시 Commit phase를 실행하지 않고, 다른 높은 우선순위 작업이 있다면 먼저 처리한 후 나중에 Commit phase를 실행할 수 있습니다. 이러한 단계적 진행을 통해 React는 동기화가 필요한 작업을 효율적으로 관리하여 사용자 경험을 개선합니다.

Render Phase에서 모든 변경사항이 Fiber Tree에 준비된 상태에서 Commit phase로 넘어가기 때문에 Render 단계와 Commit 단계의 일관성이 유지됩니다. 이렇게 두 단계가 순차적으로 작동하기 때문에 UI가 정확하게 동기화되고 불필요한 리렌더링을 방지합니다.
