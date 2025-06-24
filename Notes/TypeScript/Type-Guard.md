# 타입 가드란?

<br />

## 💂 타입 가드

타입 가드(Type Guard)란 **여러 개의 타입 중 원하는 하나의 타입으로 타입을 좁히는 것**을 의미합니다.

```ts
type Age = 'string' | 'number'

function getAge(age: Age) {
  age.length;
}
```

`Age` 타입은 문자열 또는 숫자가 될 수 있습니다. 이 타입을 함수의 파라미터로 사용하여 함수 안에서 `age` 파라미터의 문자열의 길이를 구하려고 하면 에러가 발생합니다. `age`의 타입이 문자열 뿐만 아니라 숫자일 수도 있기 때문입니다. 타입 관점에서는 `age`의 타입이 문자열이라는 것을 보장해야 안전하게 `length` 속성에 접근할 수 있습니다.

따라서 다음과 같이 원하는 타입으로 좁히는 과정이 필요합니다.

```ts
function getAge(age: Age) {
  if (typeof age === 'string') {
    age.length;
  }
}
```

`if (typeof === 'string')`처럼 조건문과 함께 사용해 타입을 좁히는 표현을 **타입 가드**라고 부릅니다.

<br />

## ➕ 타입 가드 연산자

`typeof`, `instanceof`, `in` 연산자를 이용해 여러 개의 타입 중 하나의 타입으로 걸러낼 수 있습니다.

### `typeof` 타입가드

`typeof` 연산자는 **피연산자의 평가 전 자료형을 나타내는 문자열**을 반환합니다.

|**Type**|**Result**|
|---|---|
|undefined|`"undefined"`|
|null|`"object"`|
|boolean|`"boolean"`|
|number|`"number"`|
|bigint|`"bigint"`|
|string|`"string"`|
|symbol|`"symbol"`|
|function|`"function"`|
|다른 모든 객체|`"object"`|

이를 이용해 원하는 타입과 일치하는지 확인하는 타입 가드를 만들 수 있습니다.

<br />

### `instanceof` 타입가드

`instanceof` 연산자는 **생성자의 `prototype` 속성이 객체의 프로토타입 체인 어딘가 존재하는지 판별하여 `boolean` 값을 반환`**합니다. 이를 이용하면 내장 클래스 타입을 보장할 수 있는 타입 가드를 만들 수 있습니다.

<br />

### `in` 타입가드

아래 예시 코드와 같이 우리가 직접 만든 타입과 함께 사용하려면 `in` 연산자를 이용해야 합니다.

```ts
type Person = {
  name: string;
  age: number;
};

function func(value: number | Person) {
  if (typeof value === "number") {
    console.log(value.toFixed());
  } else if ("age" in value) {
    console.log(`${value.name}은 ${value.age}살 입니다`);
  }
}
```

<br />

## 🏭 커스텀 타입가드 함수

`typeof`, `instanceof` 연산자 말고도 `is`를 사용해 타입 가드 역할을 하는 함수를 만들 수 있습니다.

```ts
function isString(age: string | number): age is string {
  return typeof age === 'string';
}
```

위의 `isString()` 함수는 문자열이거나 숫자인 타입을 받아 문자열 타입으로 좁혀주는 커스텀 타입 가드 함수입니다. `age`가 `string` 타입인지 확인하고 맞으면 `true`, 아니면 `false`를 반환하는데, 이에 그치지 않고 `true`가 반환되면 `age`는 `string` 타입이라는 것을 TypeScript에게 알려주는 역할을 합니다.

다른 함수 안에서 `typeof` 연산자 대신 `isString()`과 같은 커스텀 타입 가드 함수를 적용할 수 있습니다.

```ts
function getAge(age: string | number) {
  if (isString(age)) {
    age.length;
  }
}
```

<br />

## :book: 참고

- [캡틴 판교 타입스크립트 핸드북 - 타입 가드](https://joshua1988.github.io/ts/guide/type-guard.html)
- [한입 타스 - 타입 좁히기](https://ts.winterlood.com/92c2035a-49bc-4585-9e3d-43206ce92d59)
