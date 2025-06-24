# 타입 단언이란?

<br />

## 📍 타입 단언

타입 단언(Type Assertion)은 **컴파일러에게 특정 값이 어떤 타입인지 개발자가 잘 알고 있다라고 알려주는 방법**입니다. 개발자가 `as`를 이용해 타입을 단언하면, 자동 추론된 타입을 덮어쓰게 됩니다. 이는 컴파일러가 타입을 <sup>1)</sup>자동으로 추론하지 못하거나, <sup>2)</sup>타입을 잘못 추론하는 상황에서 사용됩니다.

예를 들어, DOM 요소를 가져오는 `document.getElementById()` 메서드의 반환 타입은 `HTMLElement | null`로 정의되어 있지만 개발자가 해당 요소는 `HTMLDivElement`임을 확신한다면 `as HTMLDivElement`를 사용해 컴파일러에게 명시적으로 알려주는 것입니다. 이를 통해 타입 오류 없이 속성에 안전하게 접근할 수 있습니다.

```tsx
const element = document.getElementById("myElement") as HTMLDivElement;

element.style.backgroundColor = "blue"; 
```

타입 단언은 주로 API나 외부 라이브러리의 반환 타입을 확실히 알고 있을 때 사용됩니다.

<br />

## ⚠️ 타입 단언 사용시 주의사항

타입 단언은 컴파일러의 타입 단언을 **우회**하는 것이기 때문에 실제 값이 단언한 타입과 다른 경우 런타임 에러가 발생할 수 있습니다. 따라서 타입 단언은 **해당 타입을 확실히 알고 있을 때만 사용**해야 하며, 가능한 [타입 추론](https://github.com/JWJung-99/Frontend-Interview-Questions/blob/main/Notes/TypeScript/Type-Inference.md)과 [타입 가드](https://github.com/JWJung-99/Frontend-Interview-Questions/blob/main/Notes/TypeScript/Type-Guard.md)를 우선적으로 사용하는 것이 바람직합니다. 타입 단언의 잘못된/무분별한 사용은 TypeScript의 장점을 훼손하며 오히려 코드 안정성을 해칠 수 있습니다.

<br />

## 🧑‍⚖️ 안전한 사용을 위한 원칙

### 1. 타입 단언보다는 타입 내로잉(narrowing)을 우선적으로 활용합니다.

TypeScript는 조건문과 타입 체크를 통해 자동으로 타입을 좁힐(narrowing 할) 수 있으므로 가능한 타입 단언 없이 명확히 하는 것이 권장됩니다.

```tsx
function printLength(value: string | string[]) {
  if (Array.isArray(value)) {
    console.log(value.length); // 타입 내로잉으로 배열로 안전하게 처리
  } else {
    // 문자열로 처리
  }
}
```

<br />

### 2. type predicate를 활용한 타입 가드를 통해 타입 안정성을 높입니다.

type predicate function이라고도 불리는 type predicate(사용자 정의 타입가드)는 **`boolean` 값을 반환하고 반환 타입을 `value is type` 형태로 지정해주는 함수**를 말합니다.

```ts
function isString(age: string | number): age is string {
  return typeof age === 'string';
}
```

`age`가 `string` 타입인지 확인하고 맞으면 `true`, 아니면 `false`를 반환하는데, 이에 그치지 않고 `true`가 반환되면 `age`는 `string` 타입이라는 것을 TypeScript에게 알려주는 역할을 합니다.

<br />

이 type predicate를 통해 데이터 구조를 확인하여 런타임 오류를 줄이는 방식으로 타입 안정성을 높일 수 있습니다.

```tsx
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}

if (isFish(pet)) {
  pet.swim();
}
```

<br />

### 3. 최소한의 범위에서만 사용해야 합니다.

타입 단언은 전체 객체보다는 필요한 속성이나 특정 부분만 단언하는 방식으로 불필요한 위험을 줄이는 것이 바람직합니다.

```tsx
const element = document.getElementById("myElement");
if (element) {
  (element as HTMLDivElement).style.backgroundColor = "blue"; // 필요한 부분만 단언
}
```

<br />

## :book: 참고

- [캡틴 판교 타입스크립트 핸드북 - 타입 단언](https://joshua1988.github.io/ts/guide/type-assertion.html#%ED%83%80%EC%9E%85-%EB%8B%A8%EC%96%B8-type-assertion)
- [type narrowing과 type predicates](https://velog.io/@devshk447/TIL-typescript-type-guard%EC%99%80-type-predicates)
