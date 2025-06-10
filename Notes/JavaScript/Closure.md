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

## 📖 참고

- 모던 JavaScript Deep Dive
