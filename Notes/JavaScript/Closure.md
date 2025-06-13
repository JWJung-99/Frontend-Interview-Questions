# 클로저란?

<br />

## 🕴🏼TL;DR

### 정의

클로저는 **함수가 선언될 때의 스코프를 기억하여, 함수가 생성된 이후에도 그 스코프에 접근할 수 있는 기능**을 말합니다. 클로저는 JavaScript의 함수가 <sup>*</sup>일급 객체라는 특성과 **렉시컬 스코프**의 조합으로 만들어집니다.

<br />

클로저의 예시는 다음과 같습니다.

```js
function outerFunction(outerVariable) {
  return function innerFunction(innerVariable) {
    console.log('Outer Variable: ' + outerVariable);
    console.log('Inner Variable: ' + innerVariable);
  };
}

const newFunction = outerFunction('outside');  // newFunction: innerFunction
newFunction('inside');  // 'Outer Variable: outside',
                        // 'Inner Variable: inside'
```

`innerFunction`은 `outerFunction`의 내부에 정의되어 있습니다. `innerFunction`은 자신이 생성된 스코프, 즉 `outerFunction`의 스코프를 기억하고 `outerFunction`의 포출이 완료된 이후에도 스코프에 접근할 수 있습니다. 따라서 `innerFunction`은 `outerVariable`에도 접근할 수 있습니다.

<br />

<details>
  <summary><sup>*</sup><b>일급 객체(first-class object)</b></summary>
  <br />
  <p>다른 객체들에 일반적으로 적용 가능한 연산을 모두 지원하는 객체로,<sup>1)</sup> 변수나 데이터에 담을 수 있고, <sup>2)</sup> 함수의 파라미터로 전달할 수 있고, <sup>3)</sup> 함수의 리턴값으로 사용할 수 있습니다.</p>
</details>

<br />

### 활용

클로저는 변수와 함수의 접근 범위를 제어하고, 특정 데이터와 상태를 유지하기 위해 자주 활용됩니다.

**첫째, 데이터 은닉에 활용됩니다.** 클로저는 외부에서 접근할 수 없는 비공개 변수와 함수를 만들 수 있습니다. 이를 통해 데이터를 은닉하여 외부 접근을 막고 데이터 무결성을 유지할 수 있습니다.

**둘째, 비동기 작업에 활용됩니다.** 클로저는 비동기 작업에서 이전의 실행 컨텍스트를 유지해야 할 때 유용합니다. 콜백 함수가 비동기적으로 실행될 때 클로저를 활용하면 실행 시점의 변수를 참조할 수 있습니다.

```js
function createLogger(name) {
  return function() {
    console.log(`Logger: ${name}`);
  };
}

const logger = createLogger('MyApp');
setTimeout(logger, 1000); // 1초 후에 'Logger: MyApp' 출력
```

** 셋째, 모듈 패턴을 구현하는데 활용됩니다.** 모듈 패턴은 특정 기능을 캡슐화하고, 외부에 공개하고자 하는 부분만 선택적으로 노출해 코드의 응집력을 높이고 유지보수성을 향상시키는 패턴입니다. 클로저를 활용하면 필요한 함수와 데이터만 외부로 노출함으로써 모듈 패턴을 쉽게 구현할 수 있다.

<br />

## 🔒 클로저

클로저는 함수를 일급 객체로 취급하는 함수형 프로그래밍 언어에서 사용되는 중요한 특성입니다. MDN에서는 클로저를 다음과 같이 정의하고 있습니다.

> **클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.**

이 정의가 무슨 의미인지 예시 코드를 통해 알아보겠습니다.

```js
const x = 1;

function outerFunc() {
  const x = 10;

  function innerFunc() {
    console.log(x); // 10
  }

  innerFunc();
}

outerFunc();
```

`outerFunc` 함수 **내부에서 중첩 함수 `innerFunc`가 정의**되고 호출되었습니다. 이때 `innerFunc`의 상위 스코프는 외부 함수 `outerFunc`의 스코프입니다. 따라서 `innerFunc`에서 자신을 포함하고 있는 외부 함수 `outerFunc`의 `x` 변수에 접근할 수 있습니다. 만약 아래 예시처럼 `innerFunc` 함수가 `outerFunc` 내부에서 정의된 중첩 함수가 아니라면 `innerFunc` 함수를 `outerFunc` 함수의 내부에서 호출한다 하더라도 `outerFunc` 함수의 변수에 접근할수 없습니다.

```js
const x = 1;

function outerFunc() {
  const x = 10;
  innerFunc();
}

function innerFunc() {
  console.log(x); // 1
}

outerFunc();
```

이 같은 현상이 발생하는 이유는 JavaScript가 렉시컬 스코프를 따르는 프로그래밍 언어이기 때문입니다.

<br />

## ⛓️ 렉시컬 스코프

JavaScript 엔진은 함수를 어디서 호출했는지가 아니라 **함수를 어디에 정의했는지**에 따라 상위 스코프를 결정하는데, 이를 **렉시컬 스코프**라고 합니다. [실행 컨텍스트](https://github.com/JWJung-99/Frontend-Interview-Questions/blob/main/Notes/JavaScript/Execution-Context.md#%EF%B8%8F-%EB%A0%89%EC%8B%9C%EC%BB%AC-%ED%99%98%EA%B2%BD)에서 살펴 보았듯이 스코프의 실체는 실행 컨텍스트의 렉시컬 환경입니다. 이 렉시컬 환경은 자신의 "외부 렉시컬 환경에 대한 참조"를 통해 상위 렉시컬 환경과 연결되며 이를 스코프 체인이라고 합니다.

따라서 "함수의 상위 스코프를 결정"하는 것은 "렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 저장할 참조값을 결정한다"는 것과 같습니다. 렉시컬 스코프는 **"외부 렉시컬 환경에 대한 참조"에 저장할 참조값, 즉 상위 스코프에 대한 참조는 함수 정의가 평가되는 시점에 함수가 정의된 환경에 의해 결정되는 것입니다.

<br />

## 🏞️ 함수 객체의 내부 슬롯 `[[Environment]]`

렉시컬 스코프가 가능하려면 함수는 자신이 정의된 환경, 즉 상위 스코프를 기억해야 합니다. 이를 위해 함수는 자신의 내부 슬롯 **`[[Environment]]`**에 자신이 정의된 환경, 즉 상위 스코프의 참조를 저장합니다. 다시 말해, 함수 정의가 평가되어 함수 객체를 생성할 때 자신이 정의된 환경에 의해 결정된 상위 스코프의 참조를 자신의 내부 슬롯 `[[Environment]]`에 저장하는데, 이는 현재 실행 중인 실행 컨텍스트의 렉시컬 환경을 가리킵니다. 왜냐하면 함수 정의가 평가되어 함수 객체를 생성하는 시점은 상위 함수가 평가 또는 실행되고 있는 시점이며, 이때 실행 중인 실행 컨텍스트는 상위 함수의 실행 컨텍스트이기 때문입니다.

함수 객체는 내부 슬롯 `[[Environment]]`에 저장한 렉시컬 환경의 참조, 즉 상위 스코프를 **자신이 존재하는 한** 기억합니다. 관련된 예시 코드를 살펴봅시다.

```js
const x = 1;

function foo() {
  const x = 10;
  bar(); // 1
}

function bar() {
  console.log(x);
}

foo();
bar(); // 1
```

위 예시 코드에서 `foo` 함수 내부에서 `bar` 함수가 호출되어 실행 중인 시점의 실행 컨텍스트는 다음과 같습니다.

|**함수 객체의 내부 슬롯 `[[Environment]]`**|
|:---:|
|<img alt="image" src="https://github.com/user-attachments/assets/11ca8cbc-b412-49fd-917d-721fe0edefbf" />|

`foo` 함수와 `bar` 함수 모두 전역에서 함수 선언문으로 정의되었으므로, 전역 코드가 평가된느 시점에 평가되어 함수 객체를 생성하고 전역 객체 `window`의 메서드가 됩니다. 이때 생성된 함수 객체의 내부 슬롯 `[[Environment]]`에는 함수 정의가 평가된 시점, 전역 렉시컬 환경의 참조가 저장됩니다.

함수가 호출되면 함수 내부로 코드의 제어권이 이동하여 함수 코드를 평가하기 시작합니다. 이때 함수 렉시컬 환경의 구성 요소인 "외부 렉시컬 환경에 대한 참조"에는 함수 객체의 내부 슬롯 `[[Environment]]`에 저장된 렉시컬 환경의 참조가 할당되며 이것이 함수의 상위 스코프를 의미합니다.

<br />

## 🔐 클로저와 렉시컬 환경

예시 코드를 하나 살펴봅시다.

```js
const x = 1;

function outer() {
  const x = 10;
  const inner = function () { console.log(x); };
  return inner;
}

const innerFunc = outer();
innerFunc(); // 10
```

`outer` 함수를 호출하면 `outer`는 중첩 함수 `inner`를 반환하고 생명 주길르 마감합니다. 즉, `outer` 함수의 실행이 종료되면 `outer` 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거됩니다. 이때 `outer` 함수의 지역 변수 `x`와 변수 값 `10`을 저장하고 있던 `outer` 함수의 실행 컨텍스트가 제거되었으므로 `outer` 함수의 지역변수 `x` 또한 생명 주기를 마감합니다. 따라서 `outer` 함수의 지역 변수 `x`에 접근할 수 있는 방법이 달리 없어 보입니다.

하지만 위 코드의 실행 결과는 `outer` 함수의 지역 변수 `x`의 값인 10입니다. 이미 생명 주기가 종료되어 실행 컨텍스트 스택에서 제거된 `outer` 함수의 지역 변수 `x`가 부활이라도 한 듯이 동작하고 있습니다. 이처럼 외부 함수보다 중첩 함수가 더 오래 유지되는 경우 **중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있으며, 이러한 중첩 함수를 클로저**라고 부릅니다.

JavaScript의 모든 함수는 자신의 상위 스코프를 기억하고, 모든 함수가 기억하는 상위 스코프는 함수를 어디서 호출하든 상관없이 유지됩니다. 따라서 함수는 언제나 자신이 기억하는 상위 스코프의 식별자를 참조할 수 있으며 식별자에 바인딩된 값을 변경할 수도 있습니다. 위 예시 코드에서 `inner` 함수는 자신이 평가도리 때 자신이 정의된 위치에 의해 결정된 상위 스코프를 `[[Environment]]` 내부 슬롯에 저장하며 이때 저장된 상위 스코프는 함수가 존재하는 한 유지됩니다.

`outer` 함수를 호출하면 `outer` 함수의 렉시컬 환경이 생성되고 `outer` 함수 객체의 `[[Environment]]` 내부 슬롯에 저장된 전역 렉시컬 환경을 `outer` 함수 렉시컬 환경의 "외부 렉시컬 환경에 대한 참조"에 할당합니다. 그리고 중첩 함수 `inner`가 평가됩니다. 이 때 중첩 함수 `inner`는 자신의 `[[Environment]]` 내부 슬롯에 현재 실행 중인 실행 컨텍스트의 렉시컬 환경, 즉 `outer` 함수의 렉시컬 환경을 상위 스코프로 저장합니다.

`outer` 함수의 실행이 종료되면 `inner` 함수를 반환하면서 `outer` 함수의 생명 주기가 종료되어 `outer` 함수의 실행 컨텍스트가 실행 컨텍스트 스택에서 제거됩니다. 이때 `outer`함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거되지만 `outer` 함수의 렉시컬 환경까지 소멸하는 것은 아닙니다. `outer` 함수의 렉시컬 환경은 `inner` 함수의 `[[Environment]]` 내부 슬롯에 의해 참조되고 있고 `inner` 함수는 전역 변수 `innerFunc`에 의해 참조되고 있으므로 가비지 컬렉션의 대상이 되지 않기 때문입니다. 가비지 컬렉터는 **누군가 참조하고 있는 메모리 공간을 함부로 해제하지 않습니다**.

|**외부 함수가 소멸되어도 외부 함수의 변수를 참조**|
|:---:|
|<img alt="image" src="https://github.com/user-attachments/assets/b4bdcfbc-55e4-4a3b-9027-3a4f4555ba0e" />|

`outer` 함수가 반환한 `inner` 함수를 호출하면 `inner` 함수의 실행 컨텍스트가 생성되고 실행 컨텍스트 스택에 푸시됩니다. 그리고 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에는 `inner` 함수 객체의 `[[Environment]]` 내부 슬롯에 저장되어 있는 참조값이 할당됩니다. 이처럼 중첩 함수 `inner`의 내부에서는 상우 ㅣ스코프를 참조할 수 있으므로 상위 스코프의 식별자를 참조할 수 있고 식별자의 값을 변경할 수도 있습니다.

클로저는 중첩 함수가 상위 스코프의 식별자를 참조하고 있고 **중첩 함수가 외부 함수보다 더 오래 유지되는 경우에 한정하는 것이 일반적**입니다. 클로저가 상위 스코프를 기억해야 하므로 불필요한 메모리의 점유를 걱정할 수도 있겠으나, 모던 JavaScript 엔진은 최적화가 잘 되어 있어서 클로저가 참조하고 있지 않은 식별자는 기억하지 않기 때문에 불필요한 메모리 낭비는 걱정할 필요가 없습니다.

<br />

## 🤹‍♂️ 클로저의 활용

클로저는 **상태를 안전하게 변경하고 유지하기 위해 사용**합니다. 상태가 의도치 않게 변경되지 않도록 **상태를 안전하게 은닉**하고 특정 함수에게만 상태 변경을 허용하기 위해 사용됩니다. (ex. [React의 useState Hook](https://github.com/JWJung-99/Frontend-Interview-Questions/blob/main/Notes/React/Hooks/useState.md))

함수가 호출될 때마다 호출된 횟수를 누적하여 출력하는 카운터를 만들어봅시다. 이 예시 코드의 호출된 횟수 `num` 변수가 안전하게 변경하고 유지해야 하는 상태입니다.

```js
let num = 0;

const increase = function () {
  return ++num;
}

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

위 코드는 잘 동작하지만 오류를 발생시킬 가능성을 내포하고 있어 좋지 않은 코드입니다. 바르게 동작하려면 다음의 전제 조건이 지켜져야 하기 때문이다.

- 카운트 상태는 `increase` 함수가 호출되기 전까지 변경되지 않고 유지되어야 합니다.
- 이를 위해 카운트 상태는 `increase` 함수만이 변경할 수 있어야 합니다.

하지만 `num` 상태가 전역 변수를 통해 관리되고 있기 때문에 언제든지 누구나 접근할 수 있고 변경할 수 있으며, 이는 의도치 않게 상태가 변경될 수 있다는 것을 의미합니다. 따라서 카운트 상태를 안전하게 변경하고 유지하기 위해서는 `increase` 함수만이 `num` 변수를 참조하고 변경할 수 있게 하는 것이 바람직합니다. 이를 위해 전역 변수 `num`을 `increase` 함수의 지역 변수로 바꾸어 의도치 않은 상태 변경을 방지해야 할 것입니다.

```js
const increase = function () {
  let num = 0;

  return ++num;
}

console.log(increase()); // 1
console.log(increase()); // 1
console.log(increase()); // 1
```

이제 카운트 상태를 `increase` 함수만이 안전하게 변경할 수 있게 되었지만, `increase` 함수가 호출될 때마다 지역 변수 `num`은 다시 선언되고 `0`으로 초기화되기 때문에 출력 결과는 언제나 `1`입니다. 다시 말해, 상태가 변경되기 이전 상태를 유지하지 못합니다. 이전 상태를 유지할 수 있도록 클로저를 사용해야 합니다.

```js
const increase = function () {
  let num = 0;

  return () => {
    return ++num;
  };
}

const increaseFunc = increase();

console.log(increaseFunc()); // 1
console.log(increaseFunc()); // 2
console.log(increaseFunc()); // 3
```

위 코드가 실행되면 `increaseFunc` 변수에 할당된 함수는 자신이 정의된 위치에 의해 결정된 상위 스코프인 `increase` 함수의 렉시컬 환경을 기억하는 클로저입니다. 따라서 클로저는 카운트 상태를 유지하기 위한 자유 변수 `num`을 언제 어디서 호출하든지 참조하고 변경할 수 있습니다. 또한 `num` 변수는 외부에서 직접 접근할 수 없는 은닉된 private 변수이므로 전역 변수를 사용했을 때와 같이 의도치 않은 변경을 걱정할 필요가 없어 안정적인 프로그래밍이 가능합니다.

이처럼 클로저는 상태가 의도치 않게 변경되지 않도록 안전하게 은닉하고, 특정 함수에게만 상태 변경을 허용하여 상태를 안전하게 변경하고 유지하기 위해 사용합니다.

다음과 같이 카운터를 감소할 수 있는 로직도 구현해 봅시다.

```js
const counter = function () {
  let num = 0;

  return {
    increase() {
      return ++num;
    },
    decrease() {
      return num > 0 ? --num : 0;
    }
  }
}

const { increaseFunc, decreaseFunc } = counter();

console.log(increaseFunc()); // 1
console.log(increaseFunc()); // 2

console.log(decreaseFunc()); // 1
console.log(decreaseFunc()); // 0
```

<br />

## 💊 캡슐화와 정보 은닉

**캡슐화**는 객체의 상태를 나타내는 **프로퍼티와** 프로퍼티를 참조하고 조작할 수 있는 동작인 **메서드**를 하나로 묶는 것을 말합니다. 객체의 특정 프로퍼티나 메서드를 감출 목적으로 사용하기도 하는데 이를 **정보 은닉**이라고 부릅니다. 정보 은닉은 외부에 공개할 필요가 없는 구현의 일부를 외부에 공개되지 않도록 감추어 적절치 못한 접근으로부터 객체의 상태가 변경되는 것을 방지해 보호하고, 객체 간의 상호 의존성, 즉 결합도를 낮추는 효과가 있습니다.

<br />

## 🤦 자주 발생하는 실수

아래는 클로저를 자주 사용할 때 자주 발생할 수 있는 실수를 보여주는 예시 코드입니다.

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = function () { return i };
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}
```

`funcs` 배열의 요소로 추가된 3개의 함수가 `0`, `1`, `2`를 반환할 것으로 기대하지만 아쉽지만 결과는 그렇지 않습니다. `for` 문의 변수 선언문에서 `var` 키워드로 선언한 `i` 변수는 블록 레벨 스코프가 아닌 함수 레벨 스코프를 갖기 때문에 전역 변수입니다. 전역 변수 `i`에는 `0`, `1`, `2`가 순차적으로 할당됩니다. 따라서 `funcs` 배열의 요소로 추가한 함수를 호출하면 전역 변수 `i`를 참조하여 `i`의 값 `3`이 출력됩니다. 클로저를 사용해 위 예시 코드를 올바르게 동작하는 코드로 만들어봅시다.

```js

for (var i = 0; i < 3; i++) {
  funcs[i] = (function (id) {
    return function () {
      return id;
    };
  }(i));
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}
```

사실 위 예시 코드는 JavaScript의 함수 레벨 스코프 특성으로 인해 `for` 문의 변수 선언문에서 `var` 키워드로 선언한 변수가 전역 변수가 되기 때문에 발생하는 현상으로, `let` 키워드를 사용하면 이 같은 번거로움이 깔끔하게 해결될 것입니다.

```js
const funcs = [];

for (let i = 0; i < 3; i++) {
  funcs[i] = function () { return i };
}

for (let i = 0; i < funcs.length; i++) {
  console.log(funcs[i]());
}
```

`for` 문의 변수 선언문에서 `let` 키워드로 선언한 변수를 사용하면 `for` 문의 코드 블록이 반복 실행될 때마다 새로운 독립적인 렉시컬 환경을 생성하여 식별자의 값을 유지할 것입니다.

<br />

## 📖 참고

- 모던 JavaScript Deep Dive
