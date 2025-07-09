# 타입 호환이란?

<br />

## 타입 호환성

타입 호환성이란, 예를 들어 A와 B 두 개의 타입이 존재할 때 A 타입의 값을 B 타입으로 취급해도 괜찮은지 판단하는 것을 의미합니다. 그래서 만약 A 타입의 값이 B 타입의 값으로 취급 되어도 괜찮다면 **호환된다**고 표현하고, 그렇지 않다면 **호환되지 않는다**고 표현합니다.

다음 그림처럼 **슈퍼(부모) 타입**인 Number 타입과 **서브(자식) 타입**인 Number Literal 타입이 있을 때, Number Literal 타입을 슈퍼 타입인 Number 타입으로 취급하는 것은 가능합니다. 하지만 Number 타입이 더 큰 타입이기 때문에 Number 타입을 서브 타입인 Number Literal 타입으로 취급하는 것은 불가능합니다.

![image](https://github.com/user-attachments/assets/e9f98143-1482-4fe3-b3e2-a1ebe8b11f11)

따라서 `num1`에 `num2`의 값을 저장하는 아래와 같은 코드는 문제가 되지 않습니다.

```ts
let num1: number = 10; // number 타입
let num2: 15 = 15; // 15 - number literal 타입

num1 = num2;
```

하지만 반대의 경우 문제가 발생합니다.

```ts
let num1: number = 10; // number 타입
let num2: 15 = 15; // 15 - number literal 타입

num2 = num1;
```

`num2`의 값에는 `15` 외의 다른 값은 절대로 담을 수 없기 때문입니다.

특별히 **서브 타입의 값을 슈퍼 타입의 값으로 취급하는 것을 업 캐스팅**이라고 부르며, 반대는 **다운 캐스팅**이라고 부릅니다. 업 캐스팅은 모든 상황에서 가능하지만 다운 캐스팅은 대부분의 상황에서 불가능하다고 할 수 있습니다.

![image](https://github.com/user-attachments/assets/918e343c-9bdf-4227-8ef2-bf3888a9edc0)

<br />

## :book: 참고

- [한입타스 - 타입 호환](https://ts.winterlood.com/9610eed7-2b66-4645-9181-483243a2089a#156c940aebd6440681cc74649100f836)
