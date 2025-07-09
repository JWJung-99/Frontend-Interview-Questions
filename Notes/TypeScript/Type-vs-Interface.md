# 타입과 인터페이스의 차이는?

<br />

## 🕴🏼 TL;DR

인터페이스의 주요 장점은 병합 가능성입니다. 동일한 이름으로 여러 번 선언할 수 있으며, 이 선언들은 자동 병합되어 확장성과 재사용성을 높이는 데 도움이 됩니다. 또한 인터페이스는 클래스가 특정 구조를 따르도록 강제하여 코드 일관성을 유지하는 데 도움이 됩니다.

타입은 병합은 불가능하지만 튜플, 유니언 타입, 리터럴 타입 등 더 다양한 타입을 정의할 수 있어 TypeScript의 강력한 타입 시스템을 활용하는 데 도움이 됩니다.

<br />

## ↔️ 타입과 인터페이스의 차이점

[타입 (타입 별칭)](https://github.com/JWJung-99/Frontend-Interview-Questions/blob/main/Notes/TypeScript/Type-Alias.md)과 [인터페이스](https://github.com/JWJung-99/Frontend-Interview-Questions/blob/main/Notes/TypeScript/Interface.md)는 TypeScript에서 객체의 구조를 정의하는 주요 방법입니다. 타입은 주로 변수, 함수 반환 값, 매개변수 등의 타입을 정의하는 데 사용되며 인터페이스는 객체의 구조를 정의하고, 클래스가 특정 구조를 따르도록 강제하는 데 사용됩니다.

타입과 인터페이스의 가장 큰 차이점은 **병합 가능 여부**입니다. 인터페이스는 동일한 이름으로 여러 번 선언할 수 있으며 이 선언들이 자동으로 병합되는 반면, 타입은 동일한 이름으로 여러 번 선언할 수 없으며 병합되지 않습니다.

- 병합할 수 있는 **인터페이스**

  ```ts
  interface User {
    name: string;
  }
  
  interface User {
    age: number;
  }
  
  const user: User = {
    name: 'John',
    age: 30,
  };
  ```

이로 인해 인터페이스는 확장성과 재사용성이 높아 TypeScript 팀에서도 인터페이스 사용을 권장하고 있습니다. 

- 병합할 수 없는 **타입**

  ```ts
  type User = {
    name: string;
  };
  
  type User = {
    age: number;
  }; // 오류 발생

  ---------------------------------------

  type NameInfo = {
    name: string;
  };

  type AgeInfo = {
    age: number;
  };

  // 인터섹션 타입 (&)을 사용해 두 타입을 결합하여 하나의 타입으로 생성
  type UserInfo = NameInfo & AgeInfo;

  const user: UserInfo = {
    name: 'John',
    age: 30,
  };
  ```

대신 타입은 튜플과 같은 복잡한 타입 표현이 가능하며, 복잡한 타입 조합을 위해 유니온(`|`) 연산자와 인터섹션 연산자(`&`)를 지원합니다.


또한 인터페이스는 클래스가 특정 구조를 따르도록 강제할 수 있어 코드 일관성을 유지하는 데 도움이 되지만, 타입은 이러한 기능을 제공하지 않는다는 특징이 있습니다.

<br />

## :book: 참고

- [타입스크립트에서 타입과 인터페이스의 차이점 이해하기](https://f-lab.kr/insight/typescript-type-vs-interface-20240801?gad_source=1&gad_source=1&gclid=Cj0KCQiAs5i8BhDmARIsAGE4xHwNInBLKRs3N4UC8MmWPHxUB6qvBo9LhNWqlFQJ0zHBGlwgZL5j6tkaAm5oEALw_wcB)
