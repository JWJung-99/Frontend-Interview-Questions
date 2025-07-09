# 타입 별칭이란?

<br />

## 📇 타입 별칭

타입 별칭(Type Alias)을 이용하면 **`type 타입_이름 = 타입`** 형태로 변수를 선언하듯 타입을 별도로 정의할 수 있습니다.

```ts
type User = {
  id: number;
  name: string;
  nickname: string;
};
```

이렇게 만든 타입 별칭은 다음과 같이 변수의 타입을 지정할 때 타입 주석과 함께 이용할 수 있습니다.

```ts
let user1: User = {
  id: 1,
  name: "홍길동",
  nickname: "gildong",
};
```

변수처럼 동일한 스코프에 동일한 이름의 타입 별칭을 선언하는 것은 불가능합니다.

<br />

## 🗂️ 인덱스 시그니처

인덱스 시그니처(Index Signature)는 객체 타입을 유연하게 정의할 수 있도록 돕는 특수한 문법입니다.

아래 예시처럼 다양한 항공사의 코드를 저장하는 객체가 있다고 가정합니다.

```ts
type AirlineCodes = {
  KoreanAir: string;
  AsianaAirlines: string;
  JinAir: string;
  ...
};
```

만약 `AirlineCodes`에 100개 이상의 항공사 코드가 추가되어야 한다면, 각 프로퍼티를 모두 정의해야 하기 때문에 매우 불편할 것입니다. 이 때, 인덱스 시그니처 문법(**`[key: key_타입]: value_타입`**)을 활용해 간단하게 타입을 정의할 수 있습니다.

```ts
type AirlineCodes = {
  [key: string]: string;
};
```

위의 예시는 "객체 타입에 `key`가 `string` 타입이고 `value`가 `string` 타입인 모든 프로퍼티가 포함된다" 라는 의미입니다.

<br />

> [!WARNING]
>
> 인덱스 시그니처를 사용하면서 동시에 추가적인 프로퍼티를 정의할 때에는, **인덱스 시그니처의 `value`의 타입과 직접 추가한 프로퍼티의 `value`의 타입이 호환되거나 일치**해야 합니다.
> 
> ```ts
> type AirlineCodes = {
>   [key: string]: string;
>   Korea: number;
> };
> ```
>
> 다음과 같이 서로 호환되지 않는 타입으로 설정하면 오류가 발생합니다.

<br />

## 📖 참고

- [한입타스 - 타입 별칭과 인덱스 시그니쳐](https://ts.winterlood.com/156628c8-e779-4ea9-b40b-a77dd083e214)
