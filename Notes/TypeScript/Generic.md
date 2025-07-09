# 제네릭이란?

<br />

## 🎭 제네릭

제네릭(Generic)이란 함수나 인터페이스, 타입 별칭, 클래스 등을 **다양한 타입과 함께 동작하도록 만들어 주는** TypeScript의 기능 중 하나입니다.

다음과 같이 다양한 타입의 매개변수를 받고 해당 매개변수를 그대로 반환하는 함수가 필요하다고 가정합니다.

```ts
function func(value: unknown) {
  return value;
}

let num = func(10);
let str = func("string");

num.toFixed(); // ❌
str.toUpperCase(); // ❌
```

다양한 타입의 매개변수를 제공받아야 하므로 매개변수 타입을 `unknown` 타입이라고 가정하면, 변수에 값은 잘 저장될 지 몰라도 `unknowm` 타입으로 추론되어 `num` 변수에 `number` 타입의 메서드를 사용해도 TypeScript가 오류로 판단하는 문제가 생깁니다. 따라서 `num` 값이 `10`일 것이 분명함에도 아래와 같이 타입 좁히기를 이용해야 합니다.

```ts
...

if (typeof num === "number") {
  num.toFixed();
}
```

### 제네릭 함수

이러한 경우에 제네릭 함수를 이용할 수 있습니다. 제네릭 함수는 **모든 타입의 값을 적용할 수 있는 범용적인 함수**입니다. 함수 이름 뒤에 꺽쇠(`<`)를 열고 타입을 담는 변수인 **타입 변수 `T`를 선언**합니다. 그리고 매개변수와 반환값의 타입을 이 타입 변수 `T`로 설정합니다.

```ts
function func<T>(value: T): T {
  return value;
}

let num = func(10); // number 타입
```

`T`에 어떤 타입이 할당될 지는 **함수가 호출될 때 결정**됩니다. `func(10)`처럼 `number` 타입의 값을 인수로 전달하면 `value`에 `number` 타입의 값이 저장되면서 **`T`가 `number` 타입으로 추론**됩니다. 이 때 `func` 함수의 반환값 또한 `number` 타입이 됩니다.

![image](https://github.com/user-attachments/assets/abe790b1-bd57-47c0-a56e-0f405d4df6b0)

<br />

## 🧐 제네릭의 사용 - `타입 변수 제한`

제네릭을 이용해 타입 변수를 제한할 수 있습니다. 타입 변수를 제한한다는 것은 함수를 호출하고 **인수로 전달할 수 있는 값의 범위에 제한을 두는 것**을 말합니다.

```ts
function getLength<T extends { length: number }>(data: T) {
  return data.length;
}

getLength("123");            // ✅

getLength([1, 2, 3]);        // ✅

getLength({ length: 1 });    // ✅

getLength(undefined);        // ❌

getLength(null);             // ❌
```

위 예시는 타입 변수를 **적어도 `length` 프로퍼티를 갖는 객체 타입으로 제한**한 예시입니다. 타입 변수를 제한할 때에는 **확장(`extends`)** 을 이용합니다.

위와 같이 `T extends { length: number }`라고 정의하면 `T`는 `{ length: number }` 객체 타입의 서브 타입이 됩니다. 다시 말해, `T`는 무조건 `length` 프로퍼티를 가지고 있는 타입이 되어야 한다는 것입니다. 따라서 이렇게 `extends`를 이용해 타입 변수를 제한하면 아래와 같은 결과가 나타납니다.

- 1번 호출은 인수로 `length` 프로퍼티가 존재하는 `string` 타입의 값을 전달했으므로 허용됩니다.
- 2번 호출은 인수로 `length` 프로퍼티가 존재하는 `number[]` 타입의 값을 전달했으므로 허용됩니다.
- 3번 호출은 인수로 `length` 프로퍼티가 존재하는 객체 타입의 값을 전달했으므로 허용됩니다.
- 4번 호출은 인수로 `undefined`을 전달했으므로 오류가 발생합니다.
- 5번 호출은 인수로 `null`을 전달했으므로 오류가 발생합니다.

<br />

## 👺 제네릭 인터페이스/타입 별칭

### 제네릭 인터페이스

제네릭은 인터페이스에도 적용할 수 있습니다.

```ts
interface KeyPair<K, V> {
  key: K;
  value: V;
}

let keyPair: KeyPair<string, number> = {
  key: "key",
  value: 0,
};
```

이 때 제네릭 인터페이스는 제네릭 함수와는 달리 마땅히 추론할 수 있는 값(매개변수)이 없기 때문에 변수의 타입으로 정의할 때 반드시 꺽쇠와 함께 타입 변수에 할당할 타입을 명시해야 합니다.

제네릭 인터페이스는 인덱스 시그니처와 함께 사용해 훨씬 더 유연한 객체 타입을 정의할 수 있습니다.

```ts
interface Map<V> {
  [key: string]: V;
}

let stringMap: Map<string> = {
  key: "value",
};
```

<br />

### 제네릭 타입 별칭

타입 별칭에도 제네릭을 적용할 수 있습니다. 

```ts
type Map2<V> = {
  [key: string]: V;
};

let stringMap2: Map2<string> = {
  key: "string",
};
```

<br />

## :book: 참고

- [한입타스 - 제네릭](https://ts.winterlood.com/0e41a293-21d9-419e-8e2a-57b5813e0582)
