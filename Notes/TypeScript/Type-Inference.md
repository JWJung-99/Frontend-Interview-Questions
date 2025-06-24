# 타입 추론이란?

<br />

## 🤔 타입 추론

TypeScript는 **타입이 정의되어 있지 않은 변수의 타입을 자동으로 추론**하며 코드를 해석하는데, 이 동작을 타입 추론(Type Inference)이라고 합니다.

```ts
let num = 10; // number 타입으로 추론
```

위와 같이 변수에 타입이 지정되어 있지 않더라도 타입을 자동으로 추론하기 때문에 개발자에게 모든 변수에 일일이 타입을 정의하지 않아도 되는 편리함을 제공합니다. 하지만 모든 상황에 타입을 잘 추론하는 것은 아닙니다. 다음과 같이 함수의 매개변수 타입은 자동으로 추론할 수 없으며, 타입 추론이 불가능한 변수에는 암시적으로 `any` 타입이 추론됩니다.

```ts
function func(param) { // any
  ...
}
```

<br />

## 🚨 타입 추론이 가능한 상황들

따라서 어떤 상황에 타입 추론이 가능한지 파악하는 것이 중요합니다. 타입 추론이 가능한 상황들은 다음과 같습니다.

### 1. 변수 선언

일반적으로 변수 선언의 경우 초기값을 기준으로 타입이 추론됩니다. 복잡한 객체 타입도 문제 없이 잘 추론합니다.

```ts
let a = 10; // number 타입으로 추론

let b = "hello"; // string 타입으로 추론

let c = {
  id: 1,
  name: "이정환",
  profile: {
    nickname: "winterlood",
  },
  urls: ["https://winterlood.com"],
}; // id, name, profile, urls 프로퍼티가 있는 객체 타입으로 추론
```

<br />

### 2. 구조 분해 할당

객체와 배열을 구조 분해 할당하는 상황에서도 타입이 추론됩니다.

```ts
let { id, name, profile } = c; // id: number, name: string, profile: nickname 프로퍼티가 있는 객체 타입으로 추론

let [one, two, three] = [1, "hello", true]; // one: number, two: string, three: boolean 타입으로 추론
```

<br />

### 3. 함수의 반환값

함수의 반환값은 `return` 문을 기준으로 추론됩니다.

```ts
function func() { // 반환값이 string 타입으로 추론
  return "hello";
}
```

<br />

### 4. 기본값이 설정된 매개변수

기본값이 설정된 매개변수의 타입은 기본값을 기준으로 추론됩니다.

```ts
function func(message = "hello" ) { // 반환값이 string 타입으로 추론
  return message;
}
```

<br />

## ⚠️ 타입 추론 시 주의사항

### 1. 암시적으로 `any` 타입으로 추론

변수를 선언할 때 초기값을 생략하면 암시적으로 `any` 타입으로 추론됩니다. 그리고 이 변수에 값을 할당하면 그 다음부터 `any` 타입이 해당 값의 타입으로 변화하는 등 암시적으로 추론된 `any` 타입은 코드의 흐름에 따라 타입이 계속 변화합니다. 이를 **`any`의 진화**라고 표현하기도 합니다.

```ts
let d; // 암시적인 any 타입으로 추론
d = 10; // number 타입으로 변경
d.toFixed();

d = "hello"; // string 타입으로 변경
d.toUpperCase();
d.toFixed(); // 오류 
```

<br />

### 2. `const` 상수의 추론

`const`로 선언된 상수도 타입 추론이 진행되지만 `let`으로 선언한 변수와는 방식이 다릅니다. 상수는 초기화 때 설정한 값을 변경할 수 없기 때문에 **가장 좁은 타입**으로 추론됩니다.

```ts
const num = 10; // 10 Number Literal 타입으로 추론

const str = "hello"; // "hello" String Literal 타입으로 추론
```

<br />

## 👍 최적 공통 타입

다양한 타입의 요소를 담은 배열을 변수의 초기값으로 설정하면 가장 근접한 타입을 추론하게 되는데 이를 **최적 공통 타입(Best Common Type)** 이라고 합니다.

```ts
let arr = [0, 1, null]; // (number | null)[] 타입으로 추론
```

위 변수 `arr`의 타입을 추론하기 위해 각 배열의 아이템을 살펴보며 가장 잘 호환되는 타입을 선정합니다.

<br />

## :book: 참고
