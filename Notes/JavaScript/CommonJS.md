# CommonJS란?

<br />

## 🗃️ CommonJS

### 정의

CommonJS 모듈은 Node.js에서 JavaScript 패키지를 불러올 때 사용하는 방식입니다. `exports` 또는 `module.exports`로 모듈을 내보내고, `require`로 모듈을 가져옵니다.

<br />

> **[참고] CommonJS에서 모듈을 내보낼 때 주의할 점**
>
> Node.js에서 CommonJS 모듈은 다음과 같이 시작합니다.
>
> ```js
> exports = module.exports = {};
> ```
>
> `exports`는 `module.exports`의 별칭(alias)입니다. 따라서 `exports`와 `module.exports`는 처음에 같은 객체입니다.
>
> ```js
> exports === module.exports; // true
> ```
>
> 따라서 다음과 같이 내보내는 것은 괜찮습니다.
>
> ```js
> exports.func = () => console.log('true'); // module.exports.func = ...
> ```
>
> 하지만 클래스나 객체를 내보내기 위해 다음과 같이 `exports`에 할당하게 되면 문제가 발생합니다.
>
> ```js
> exports = class Square { ... };
> ```
>
> 이 시점부터는
> - `exports`는 `Square` 클래스를 가리킵니다.
> - `module.exports`는 초기의 빈 객체 `{}`를 가리킵니다.
> 
> **Node.js는 모듈을 불러올 때 항상 `module.exports`를 내보냅니다**. 따라서 `Square` 클래스를 불러오려고 다음과 같이 작성하면 빈 객체를 불러오게 되는 것입니다.
>
> ```js
> const Square = require('./square.js');
> console.log(typeof Square); // "object"
> console.log(Square); // {}
> ```
>
> 따라서 무언가를 모듈의 최종 결과로 내보내고 싶다면 **반드시 `module.exports = ...`를 사용해야 합니다.
>
> ```js
> module.exports = class Square {
>   constructor(width) {
>     this.width = width;
>   }
> 
>   area() {
>     return this.width ** 2;
>   }
> };
> 
> const Square = require('./square.js');
> console.log(typeof Square); // "function"
> const square = new Square(2);
> console.log(square.area()); // 4

<br />

### CommonJS 사용하는 경우

Node.js가 CommonJS 모듈 시스템을 사용하는 경우는 다음과 같습니다.

- 파일 확장자가 `.cjs`로 되어 있는 경우
- 파일 확장자가 `.js`로 되어 있으며 가장 가까운 부모의 `package.json` 파일의 `type` 필드 값이 명시되지 않았거나 `commonjs`인 경우
- 파일 확장자가 `.mjs`, `cjs`, `.json`, `.node`, `.js`가 아닌 경우
- 모듈이 `require()`로 호출되는 경우

`package.json` 또는 파일명에 별다른 조치를 취하지 않으면 항상 CommonJS를 사용한다는 점에 주목해야 합니다. CommonJS와 ESModule 간 호환이 되지 않고 이미 많은 패키지가 CommonJS를 기반으로 제작되었다는 점이 그 이유입니다.

<br />

### Module Wrapper

CommonJS 시스템에서, 모듈에서 `export` 되지 않은 값들은 로켤 변수로 남아 다른 파일에서 접근할 수 없는데 이는 Module Wrapper라는 함수 덕분입니다.

```js
(function (exports, require, module, __filename, __dirname) {
  ...
})()
```

이를 통해 모듈 최상단에 있는 `var`, `const`, `let`으로 선언된 변수가 글로벌 객체(`global`)에 등록되는 것을 막을 수 있습니다. 모듈에서 글로벌 객체에 있는 `exports`, `require`, `module`, `__filename`, `__dirname`을 사용할 수 있게 해 줍니다. 반면 ESModule에서는 Module Wrapper가 없기 때문에 `__filename`, `__dirname` 등을 사용할 수 없습니다.

<br />

### 특징

- **동기 실행**

  CommonJS는 모듈을 하나씩 순서대로 불러오고 처리합니다. `require`를 선언하면 디스크 또는 네트워크로 해당 모듈을 읽어 **즉시 스크립트를 실행**합니다.

  ```js
  // module1.js
  console.log('module1 로드 시작');
  
  setTimeout(() => {
    console.log('module1 실행');
  }, 2000)
  
  console.log('module1');

  // index.js
  console.log('시작');
  const module1 = require('./module1');
  console.log('index!');
  ```

  위 예시에서 `require`를 실행했을 때 `module1.js`의 전체 코드가 실행된 이후 다음 코드로 넘어가는 것을 확인할 수 있습니다.

  ```
  시작
  module1 로드 시작
  module1
  index!
  module1 실행
  ```

- **캐싱**

  모듈은 한 번 로딩되고 난 뒤에는 캐싱됩니다. 같은 `require`를 호출하게 되면 한 번 이 값을 `resolve` 한 후에는 동일한 값을 반환합니다. 캐싱의 기준은 **파일명**이 됩니다.

  ```js
  // data.js
  console.log('call data');
  module.exports = 'hello';
  
  // index.js
  const data1 = require('./data.js');
  const data2 = require('./data.js');
  const data3 = require('./data.js');
  
  console.log(data1, data2, data3);
  ```

  위 예시에서 `require('./data.js')`로 처음 모듈을 가져올 때는 evaluation하여 `console.log`가 실행되지만 한 번 캐싱된 이후에는 더 이상 `console.log`가 실행되지 않는 것을 확인할 수 있습니다.

  ```
  call data
  hello hello hello
  ```

  이러한 캐싱 정보는 `require.cache`에 존재하며 필요에 따라 이 캐시 정보를 삭제할 수도 있습니다.

- **Tree Shaking 불가**

  앞서 CommonJS 환경에서는 모든 모듈을 Module Wrapper라는 함수로 감싸서 실행한다는 것을 알아보았습니다. 이러한 CommonJS 방식은 브라우저에서 사용하기 시작하면서부터 문제가 되었습니다. 브라우저의 경우 사용자 컴퓨터의 성능을 담보할 수 없기 때문에, 각 모듈이 Module Wrapper로 인해 생성된 클로저에 래핑되어 실행된다는 점은 JavaScript의 성능을 매우 안 좋게 만들었습니다. 따라서 그 당시 인기 있던 번들러인 Closure Compiler나 Rollupjs 등은 모든 모듈을 하나의 클로저로 호이스팅하거나 연결해 `require`로 인한 성능 저하 현상을 방지했습니다.

  하지만 `module.exports`라는 객체의 특성 때문에 빌드 시 모듈에서 어떤 값을 불러와 사용해야 할 지 가늠할 수가 없습니다. 따라서 번들러들은 CommonJS로 되어 있는 모듈의 성능을 위해 하나의 거대한 클로저로 합쳐버리는 대신 무엇이 실행될 지 결정하는 작업을 포기해 버립니다. 따라서 트리 쉐이킹이 불가능한 것입니다. 반면 ESModule은 `export`라는 명확한 키워드를 사용하고 있으므로 사용 여부를 결정할 수 있기 때문에 트리 쉐이킹이 가능합니다.

<br />

## :book: 참고

- [commonjs란 무엇인가?](https://yceffort.kr/2023/05/what-is-commonjs)
