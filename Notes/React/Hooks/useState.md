# useState란?

<br />

## 📈 state

컴포넌트는 상호작용의 결과로 화면의 내용을 변경해야 할 경우가 많습니다. 이 때 컴포넌트는 현재 입력값, 이미지 등을 **기억**해야 합니다. React는 이러한 **컴포넌트별 메모리를 state**라고 정의합니다.

컴포넌트 내부에서 지역 변수(Local Variable)를 이용해 값을 관리할 수도 있습니다. 하지만 이러한 경우 컴포넌트가 새롭게 렌더링 될 때마다 지역 변수의 값이 초기화될 것이고, React는 지역 변수의 값이 변경되더라도 이를 리렌더링에 반영하지 않을 것입니다. 따라서 React는 값이 변경되었을 때 리렌더링을 진행하도록 하면서 컴포넌트가 새롭게 렌더링되었더라도 변경된 값을 그대로 유지하도록 하는 장치를 **state**로 구현하였습니다.

state 변수와 state 변수를 업데이트하고 React가 컴포넌트를 다시 렌더링하도록 유발하기 위해 `useState` Hook을 사용합니다.

<br />

## 🪝 `useState`

`useState`는 컴포넌트에 state 변수를 추가할 수 있는 React Hook입니다.

### `useState(initialState)`

- 컴포넌트의 최상위 레벨에서 `useState`를 호출하여 state 변수를 선언합니다. 이 때 배열의 구조분해 할당을 이용해 `[something, setSomething]`의 형태로 변수의 이름을 지정합니다.

- **매개변수**
  
  - `initialState`는 state의 초기값으로, `initialState`에는 어떤 유형의 값이든 지정할 수 있으며 초기 렌더링 이후에는 무시됩니다.
  - 단, 함수를 `initialState`로 전달할 때에는 주의해야 합니다.
    - 함수를 전달하면 이 함수를 **초기화 함수**로 취급하여 초기 렌더링 시에만 호출하고 반환값을 state에 저장합니다.
    - 이 함수는 **순수해야 하고**, **인수를 받지 않아야 하며**, **반드시 값을 반환**해야 합니다.
    - `useState(initialFunction())`처럼 초기값 설정 시 **함수를 호출**하면 안 됩니다. 이 경우 리렌더링이 일어날 때마다 매번 함수가 호출되어, 함수 내에서 값비싼 계산이 이루어진다면 메모리 낭비가 발생할 수 있습니다.

- **반환값**

  - 현재 state를 반환하며, 첫 번째 렌더링시에는 전달한 `initialState`를 반환합니다.
  - state를 다른 값으로 업데이트하고 리렌더링을 일으킬 수 있는 `set` 함수를 반환합니다.

- **주의사항**

  - React Hook이므로 컴포넌트의 최상위 레벨 또는 커스텀 Hook에서만 호출할 수 있습니다.

### `setSomething(nextState)`

- `useState`가 반환하는 `set` 함수를 사용해 state를 다른 값으로 업데이트하고 리렌더링을 일으킬 수 있습니다. **state는 불변성을 유지해야 하기 때문에, state를 직접 변경하지 않고 `set` 함수를 이용해 업데이트해야 합니다.**

- **매개변수**

  - `nextState`에 다음 state가 될 값을 전달합니다. `nextState`에는 모든 데이터 타입이 허용되지만 함수에 대해서는 특별하게 동작합니다.

    - 함수를 `nextState`로 전달하면 업데이터 함수로 취급하여 대기열에 넣고 컴포넌트를 리렌더링하며, 다음 렌더링 중에 대기열에 있는 모든 업데이터를 이용해 다음 state를 계산합니다.
    - 이 함수는 **순수해야 하고**, **대기 중인 state를 유일한 인수로 사용해야 하며**, **다음 state를 반환**해야 합니다.

- **주의사항**

  - `set` 함수는 **다음 렌더링에 대한 state 변수만 업데이트**합니다. `set` 함수 호출 이후에도 state에는 여전히 호출 전 이전 값이 담겨 있습니다.
  - 사용자가 제공한 새로운 값이 `Object.is`에 의해 현재 state와 동일하다고 판단되면 React는 컴포넌트와 그 자식들을 리렌더링하지 않습니다.
  - React는 state 업데이트를 batch한다. 모든 이벤트 핸들러가 실행되고 set 함수를 호출한 후 화면을 업데이트합니다.

### 중요

- React는 이전 state를 기반으로 state를 업데이트합니다.

  - `set` 함수를 호출하더라도 이미 실행 중인 코드에서는 state 변수가 업데이트 되지 않습니다.

    ```js
    function handleClick() {
      setAge(age + 1); // setAge(42 + 1)
      setAge(age + 1); // setAge(42 + 1)
      setAge(age + 1); // setAge(42 + 1)
    }
    ```

  - state가 1씩 증가하기를 원한다면 **대기 중인 state로 다음 state를 계산하는 업데이터 함수**를 사용해야 합니다.

    ```js
    function handleClick() {
      setAge(a => a + 1); // setAge(42 => 43)
      setAge(a => a + 1); // setAge(43 => 44)
      setAge(a => a + 1); // setAge(44 => 45)
    }
    ```

- State는 불변성을 유지해야 합니다.

  - React에서 state는 읽기 전용으로 간주되므로 기존 객체를 **변경하지 않고 교체해야 합니다.**

    ```js
    form.firstName = 'Taylor'; ❌
    
    setForm({
      ...form,
      firstName: 'Taylor'
    }); ✅
    ```

<br />

## 🔐 `useState`와 클로저

React의 함수형 컴포넌트는 렌더링이 발생하면 함수 자체가 다시 호출되는 방식입니다. 따라서 상태를 관리하려면 함수가 다시 호출되었을 때 이전 상태를 기억해야 합니다.

`useState`는 이 문제를 클로저를 통해 해결합니다. 아래의 예시는 `useState`를 직접 구현한 코드입니다.

```js
const MyReact = (function () {
  let state;

  return {
    render(Component) {
      const Comp = Component();
      Comp.render();
      return Comp;
    },

    useState(initialValue) {
      state ||= initialValue;

      const setState = (newValue) => {
        state = newValue;
      };

      return [state, setState];
    },
  };
})();

const Counter = () => {
  const [count, setCount] = MyReact.useState(0);

  return {
    click: () => setCount(count + 1),
    render: () => console.log('render:', { count }),
  };
};

let App;
App = MyReact.render(Counter); // render: { count: 0 }
App.click();
App = MyReact.render(Counter); // render: { count: 1 }
```

state 변수를 **외부에 선언**하고 반환된 `useState` 함수가 이 state 변수에 접근할 수 있도록 하여 **클로저**를 활용하고 있습니다. `setState`를 실행하면 상위 스코프에 있는 state 값을 변경할 수 있고, 컴포넌트가 리렌더링되어도 state 값을 유지할 수 있습니다.

하지만 위의 코드는 `useState`를 사용하는 컴포넌트가 여러 개일 때 문제가 발생합니다. 하나의 state에 여러 컴포넌트가 접근하기 때문에 모든 컴포넌트의 state가 동일할 것입니다. React는 state를 **배열 형식**으로 관리하여 이 문제를 해결합니다.

```js
let state = [];
let setters = [];
let cursor = 0;
let firstrun = true;

const createSetter = (cursor) => {
  return (newValue) => {
    state[cursor] = newValue;
  };
};

const useState = (initialValue) => {
  if (firstrun) {
    state.push(initialValue);
    setters.push(createSetter(cursor));
    firstrun = false;
  }

  const resState = state[cursor];
  const resSetter = setters[cursor];
  cursor++;

  return [resState, resSetter];
};
```

`useState`를 이용해 선언된 state들이 배열에 순서대로 저장되며, 이러한 state 배열은 컴포넌트를 유일하게 구분짓는 `key`를 이용해 접근할 수 있습니다.

<br />

## 📖 참고

- [공식문서 - State: 컴포넌트의 기억 저장소](https://ko.react.dev/learn/state-a-components-memory)
- [React의 state, 그리고 useState에 대해 더 알고 싶어졌다.](https://velog.io/@rookieand/React%EC%9D%98-state-%EA%B7%B8%EB%A6%AC%EA%B3%A0-useState%EC%97%90-%EB%8C%80%ED%95%B4-%EB%8D%94-%EC%95%8C%EA%B3%A0-%EC%8B%B6%EC%96%B4%EC%A1%8C%EB%8B%A4)
- [useState의 동작 원리와 클로저](https://seokzin.tistory.com/entry/React-useState%EC%9D%98-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC%EC%99%80-%ED%81%B4%EB%A1%9C%EC%A0%80)
