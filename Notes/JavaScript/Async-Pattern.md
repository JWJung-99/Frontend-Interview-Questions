# 비동기 패턴과 Promise

<br />

## 🧩 비동기 패턴

비동기 코드를 작성하는 패턴은 크게 콜백과 Promise 패턴으로 나눌 수 있습니다. Promise 패턴에서는 `.then()` 메서드를 사용하거나, `async`/`await` 키워드를 사용하는 방식으로 나뉩니다.

<br />

### 콜백

아래 예시 코드에서 `a`는 1초 뒤에 숫자 `1`을 출력하는 비동기 함수이고, `b`와 `c`는 각각 숫자 `2`와 `3`을 출력하는 동기함수입니다. 각 함수를 순서대로 호출해도 `a`는 비동기 함수이기 때문에 실행 후 완료되지 않아도 다음 함수 실행으로 넘어갈 수 있습니다. 따라서 `2`, `3`, `1` 순으로 출력됩니다.

```js
const a = () => {
  setTimeout(() => {
    console.log(1);
  }, 1000);
}
const b = () => console.log(2);
const c = () => console.log(3);

a();
b();
c();

// 2 -> 3 -> 1
```

만약 `1`, `2`, `3` 순서로 결과를 출력하고 싶다면 다음과 같이 `a` 함수 콜백을 사용하도록 수정할 수 있습니다.

```js
const a = (callback) => {
  setTimeout(() => {
    console.log(1);
    callback();
  }, 1000);
}

a(() => {
  b();
  c();
});
```

이처럼 콜백 방식은 비동기 코드를 처리할 때 유용하지만, 순서 보장을 위한 함수를 여러 번 중첩하면 코드가 복잡해지고 가독성이 떨어지기 쉽습니다. 콜백 함수가 여러 번 중첩되어 점점 들여 쓰여지는 패턴이 마치 개미 지옥과 비슷하다고 하여 **콜백 지옥(Callback Hell)**이라고 부릅니다.

```js
// 콜백 지옥!
a(() => {
  b(() => {
    c(() => {
      d(() => {
        // ...
      })
    })
  })
})
```

<br />

### `.then()`

콜백 패턴의 단점을 보완하기 위해 ES2015에서 `Promise` 클래스가 도입되었습니다. `Promise` 클래스를 통해 콜백 대신 `resolve` 메서드를 호출하면 **모든 동작이 완료된 시점을 보장**할 수 있습니다. 함수를 호출할 때, `.then()` 메서드를 체이닝으로 사용할 수 있고, `.then()` 메서드의 콜백은 모든 동작이 완료된 시점(`resolve`의 호출 위치)에서 실행됩니다.

앞서 콜백 방식에서 살펴 본 순차적으로 실행할 여러 비동기 함수를 다음과 같이 `Promise` 클래스를 사용해 수정할 수 있습니다.

```js
const a = () => {
  return new Promise(resolve => {
    setTimeout(() => {
      console.log(1);
      resolve();
    }, 1000);
  });
}
const b = () => console.log(2);
const c = () => console.log(3);

a().then(() => {
  b();
  c();
});
```

하지만 여전히 콜백 지옥과 같은 패턴 문제가 발생합니다.

```js
a().then(() => {
  b().then(() => {
    c().then(() => {
      d().then(() => {
        // ...
      })
    })
  })
})
```

이는 `.then()`의 체이닝 특성을 이용해 `.then()`에서 각 비동기 함수 호출의 결과(`Promise` 인스턴스)를 반환하면, 다시 `.then()`을 체이닝으로 연결해 해결할 수 있습니다.

```js
a()
  .then(() => b())
  .then(() => c())
  .then(() => d())
  .then(() => {
    // ...
  })
```

또는 비동기 함수 자체를 반환해도 동일한 결과를 얻을 수 있습니다.

```js
a()
  .then(b)
  .then(c)
  .then(d)
  .then(() => {
    // ...
  })
```

<br />

### `async`/`await`

`.then()` 메서드를 사용하는 방법이 콜백 패턴보다 가독성이 훨씬 좋지만, 여전히 일부 콜백 구조를 가지고 있습니다. ES2017에서 도입된 `async`/`await` 키워드를 사용하면 더 쉽게 비동기 코드를 작성할 수 있습니다.

위에서 `.then()` 메서드로 사용하던 패턴을 다음과 같이 `async`/`await` 키워드로 변경할 수 있습니다.

```js
;(async () => {
  await a()
  await b()
  await c()
  await d()
  // ...
})()
```

`await` 키워드는 비동기 함수의 실행 후 완료를 기다리는 역할을 합니다. **`await` 키워드는 `async` 키워드가 붙은 함수 안에서만 사용할 수 있다는 점에 주의**해야 합니다.

<br />

## 🤙 Promise

`Promise` 클래스를 생성자 함수로 호출하면, 비동기 함수를 랩핑한 `Promise` 인스턴스를 반환합니다. 이 `Promise` 인스턴스는 `.then()` 메서드나 `await` 키워드로 사용하며, 이 때 비동기 함수의 실행부터 완료까지의 각 상태를 가지게 됩니다.

- 대기(Pending): 약속한 동작이 시작된 후, 약속이 이행되거나 거부되기 전까지 기다리는 상태
- 이행(Fulfilled): 약속한 동작이 정상적으로 완료된 상태
- 거부(Rejected): 약속한 동작이 완료되지 못한 상태

```js
function 함수(resolve, reject) {
  // ... 대기(..)
  if (error) reject(에러); // 거부(X)
  resolve(데이터); // 이행(O)
}

const promise = new Promise(함수);
```

`resolve(데이터)` 호출의 인수(데이터)는 `.then()` 메서드의 콜백 매개변수로 전달되거나 `await` 키워드의 반환값으로 사용됩니다. `reject(에러)` 호출의 인수(에러)는 `.catch()` 메서드의 콜백 매개변수로 전달되거나 `try/catch` 구문의 `catch` 블록의 에러 변수로 전달됩니다.

```js
promise
  .then(데이터 => {})
  .catch(에러 => {})
  .finally(() => {});

// 또는
;(async () {
  try {
    const 데이터 = await promise;
  } catch (에러) {
    //
  } finally {
    //
  }
})();
```

<br />

## 🔁 반복문에서 비동기 처리

비동기 함수를 반복 호출하는 경오, `for` 반복문 (`for of`, `for in` 포함)을 사용해야 이전 작업이 완료된 후 다음 작업이 실행됩니다. `.forEach()`, `.map()` 등의 반복 메서드는 이전 작업 완료 여부와 상관없이 실행되기 때문에 비동기 함수의 반복 호출에 적합하지 않습니다.

```js
const getMovies = title => {
  return new Promise(resolve => {
    fetch(`https://omdbapi.com/?apikey=7035c60c&s=${title}`)
      .then(res => res.json())
      .then(res => resolve(res));
  });
}

const titles = ['frozen', 'avengers', 'avatar']

// 잘못된 방법!
titles.forEach(async title => {
  const movies = await getMovies(title);
  console.log(title, movies);
})

// 올바른 방법!
;(async () => {
  for (const title of titles) {
    const movies = await getMovies(title);
    console.log(title, movies);
  }
})()
```

<br />

## 🔣 정적 메서드

<br />

### `Promise.resolve`

`resolve` 되는 `Promise`를 반환합니다.

```js
const promise = Promise.resolve(123);

promise
  .then((res) => console.log(res)) // 123
  .catch((err) => console.log(err)) // 실행되지 않음!

// 또는,

;(async () => {
  try {
    console.log(await promise); // 123
  } catch (err) {
    console.log(err); // 실행되지 않음!
  }
})();
```

<br />

### `Promise.reject`

`reject` 되는 `Promise`를 반환합니다.

```js
const promise = Promise.reject('거부됨!');

promise
  .then((res) => console.log(res)) // 실행되지 않음!
  .catch((err) => console.log(err)) // '거부됨!'

// 또는,

;(async () => {
  try {
    console.log(await promise); // 실행되지 않음!
  } catch (err) {
    console.log(err); // '거부됨!'
  }
})();
```

<br >

### `Promise.all`

제공되는 배열 내의 모든 `Promise`를 동시에 실행해 모두 이행되기까지 기다립니다. 하나라도 `reject` 되면 처음으로 `reject` 된 이유로 모든 `Promise`가 함께 `reject` 됩니다.

```js
Promise.all([frozen, avengers, avatar])
  .then(res => {
    console.log(res); // [Frozen[], Avengers[], Avatar[]]
  })
  .catch(err => { // 실행되지 않음!
    console.error(err);
  });

// 또는,

;(async () => {
  try {
    const res = await Promise.all([frozen, avengers, avatar])
    console.log(res); // [Frozen[], Avengers[], Avatar[]]
  } catch (err) { // 실행되지 않음!
    console.error(err);
  }
})();
```

<br />

### `Promise.allSettled`

제공되는 배열 내의 모든 `Promise`를 동시에 실행해 모두 `resolve` 되거나 `reject` 될 때까지 기다립니다. `Promise.all`과 달리, `reject` 되더라도 모든 `Promise`는 완료되며 각 `Promise`의 `resolve`/`reject` 값을 반환합니다.

```js
const frozen = getMovies('frozen');
const avengers = getMovies(); // 거부되는 프로미스!
const avatar = getMovies('avatar');

Promise.allSettled([frozen, avengers, avatar])
  .then(res => {
    console.log(res);
    // [
    //   { status: 'fulfilled', value: Frozen[] }, 
    //   { status: 'rejected', reason: '검색어를 입력하세요.' }, 
    //   { status: 'fulfilled', value: Avatar[] }
    // ]
  });

// 또는,

;(async () => {
  const res = await Promise.allSettled([frozen, avengers, avatar]);
  console.log(res);
  // [
  //   { status: 'fulfilled', value: Frozen[] }, 
  //   { status: 'rejected', reason: '검색어를 입력하세요.' }, 
  //   { status: 'fulfilled', value: Avatar[] }
  // ]
})();
```

<br />

### `Promise.race`

제공되는 배열 내의 모든 `Promise`를 동시에 실행해, 가장 먼저 `resolve` 되거나 `reject` 된 `Promise`를 하나만 반환합니다.

```js
const frozen = getMovies('frozen', 1000); // 1초
const avengers = getMovies(null, 3000); // 3초, 거부되는 프로미스!
const avatar = getMovies('avatar', 2000); // 2초

Promise.race([frozen, avengers, avatar])
  .then(res => {
    console.log(res); // Frozen[]
  })
  .catch(err => {
    console.error(err);
  });

// 또는,

;(async () => {
  try {
    const res = await Promise.race([frozen, avengers, avatar]);
    console.log(res); // Frozen[]
  } catch (err) {
    console.error(err);
  }
})();
```

<br />

### `Promise.any`

제공되는 배열 내의 모든 `Promise`를 동시에 실행해, 가장 먼저 `resolve` 된 `Promise`를 반환합니다. `resolve` 된 `Promise`가 하나라도 있는 경우 `reject` 된 `Promise`는 무시되며, 모든 `Promise`가 `reject` 된 경우 `AggregateError`를 반환합니다.

```js
Promise.any([frozen, avengers, avatar])
  .then(res => {
    console.log(res); // Frozen[]
  })

// 또는,

;(async () => {
  const res = await Promise.any([frozen, avengers, avatar])
  console.log(res); // Frozen[]
})()
```

```js
const frozen = getMovies(null, 2000); // 2초, 거부되는 프로미스!
const avengers = getMovies(null, 1000); // 1초, 거부되는 프로미스!
const avatar = getMovies(null, 3000); // 3초, 거부되는 프로미스!

Promise.any([frozen, avengers, avatar])
  .then(res => {
    console.log(res); // AggregateError: All promises were rejected
  })

// 또는,

;(async () => {
  const res = await Promise.any([frozen, avengers, avatar]);
  console.log(res); // AggregateError: All promises were rejected
})();
```

<br />

## :book: 참고

- [JS 비동기 핵심 패턴](https://www.heropy.dev/p/Zr4RfI#h2_%EB%B9%84%EB%8F%99%EA%B8%B0_%EC%BD%94%EB%93%9C_%ED%8C%A8%ED%84%B4)
