# 인터페이스란?

<br />

## 🗝️ 인터페이스

인터페이스는 타입 별칭과 동일하게 타입에 이름을 지어주는 또 다른 문법입니다. 예를 들어 간단한 `Person` 객체의 타입을 다음과 같이 정의할 수 있습니다.

```ts
interface Person {
  name: string;
  age: number;
}
```

이렇게 정의한 인터페이스를 타입 주석과 함께 사용해 변수의 타입을 정의할 수 있습니다.

```ts
const person: Person = {
  name: '홍길동',
  age: 27
}
```

<br />

### 선택적/읽기 전용 프로퍼티 정의

인터페이스에서도 타입 별칭과 마찬가지로 선택적 프로퍼티, 읽기 전용 프로퍼티 설정이 가능합니다.

```ts
interface Person {
  name: string;
  age?: number;
  readonly nationality: string;
}

const person: Person = {
  name: '홍길동',
  nationality: '대한민국',
  // age: 27
}

person.nationality = '일본' // ❌
```

<br />

### 메서드 타입 정의

다음과 같이 메서드의 타입을 정의하는 것 또한 가능합니다.

```ts
interface Person {
  readonly name: string;
  age?: number;
  sayHi1: () => void; // 함수 표현식 이용
  sayHi2(): void; // 호출 시그니처 이용
}
```

<br />

### 하이브리드 타입 정의

인터페이스 또한 함수이자 일반 객체인 하이브리드 타입을 정의할 수 있습니다.

```ts
interface Func2 {
  (a: number): string;
  b: boolean;
}

const func: Func2 = (a) => "hello";
func.b = true;
```

<br />

### 주의할 점

인터페이스는 대부분의 상황에 타입 별칭과 동일하게 동작하지만 몇 가지 차이점이 존재합니다. **타입 별칭에서는 Union이나 Intersection 타입을 정의할 수 있지만, 인터페이스에서는 할 수 없습니다**.

```ts
type Type1 = number | string;
type Type2 = number & string;

interface Person {
  name: string;
  age: number;
} | number // ❌
```

따라서 인터페이스를 Union이나 Intersection으로 이용해야 한다면 다음과 같이 타입 별칭과 함께 사용하거나, 타입 주석에서 직접 사용해야 합니다.

```ts
type Type1 = number | string | Person;
type Type2 = number & string & Person;

const person: Person & string = {
  name: "이정환",
  age: 27,
};
```

<br />

## 🧑‍🧑‍🧒‍🧒 인터페이스 확장

인터페이스 확장이란 하나의 인터페이스를 다른 인터페이스들이 상속받아 중복된 프로퍼티를 정의하지 않도록 도와주는 문법입니다. 아래 예시를 보면, `Animal` 타입에 있는 `name`과 `age` 프로퍼티가 모든 타입에 중복해 정의되어 있고, `Dog`, `Cat`, `Chicken`이 각각의 추가적인 프로퍼티를 가지고 있습니다.

```ts
interface Animal {
  name: string;
  age: number;
}

interface Dog {
  name: string;
  age: number;
  isBark: boolean;
}

interface Cat {
  name: string;
  age: number;
  isScratch: boolean;
}

interface Chicken {
  name: string;
  age: number;
  isFly: boolean;
}
```

이럴 때 인터페이스 확장 기능을 이용하면 좋습니다. **`interface 타입_이름 extends 확장할_타입_이름`** 형태로 `extends` 키워드 뒤에 확장할 타입의 이름을 정의하면 해당 타입에 정의된 모든 프로퍼티를 다 가지고 오게 됩니다.

```ts
interface Animal {
  name: string;
  color: string;
}

interface Dog extends Animal {
  breed: string;
}
```

확장과 동시에 프로퍼티의 타입을 재정의하는 것 또한 가능합니다. 프로퍼티를 재정의 할 때에는 **원본 타입이 재정의된 타입의 슈퍼 타입이 되도록** 재정의 해야 합니다.

```ts

interface Animal {
  name: string;
  color: string;
}

interface Dog extends Animal {
  name: '와드'; // 타입 재 정의
  breed: string;
}
```

인터페이스는 인터페이스 뿐만 아니라 타입 별칭으로 정의된 객체도 확장할 수 있습니다.

```ts
type Animal = {
  name: string;
  color: string;
};

interface Dog extends Animal {
  breed: string;
}
```

또한 여러 개의 인터페이스를 확장하는 것 또한 가능합니다.

```ts
interface DogCat extends Dog, Cat {}

const dogCat: DogCat = {
  name: "",
  color: "",
  breed: "",
  isScratch: true,
};
```

<br />

## 🔗 선언 합침

타입 별칭은 동일한 스코프 내에 중복된 이름으로 선언할 수 없지만 인터페이스는 가능합니다.

```ts
interface Person {
  name: string;
}

interface Person { // ✅
  age: number;
}
```

이것은 중복된 이름의 인터페이스 선언이 **모두 하나의 인터페이스로 합쳐지기 때문**입니다.

```ts
interface Person {
  name: string;
  age: number;
}
```

이렇게 동일한 이름의 인터페이스들이 합쳐지는 것을 **선언 합침(Declaration Merging)** 이라고 부릅니다. 하지만 만약 동일한 이름의 인터페이스의 동일한 프로퍼티가 서로 다른 타입으로 정의된다면 오류가 발생할 수 있습니다.

<br />

## :book: 참고

- [한입타스 - 인터페이스](https://ts.winterlood.com/205a2c68-50a6-47f7-bd61-61bd47d4287a)
