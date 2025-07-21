# 패키지 매니저란?

<br />

## 📦 패키지 매니저란?

JavaScript나 TypeScript를 사용하면 `require`나 `import` 구문을 사용해서 외부 의존성을 참조하는데, 패키지 매니저는 이를 올바르게 참조할 수 있도록 보장해주는 프로그램입니다. JavaScript의 표준인 ECMAScript에 따르면, 원래 `import` 구문을 사용할 때에는 정확한 절대 경로나 상대 경로를 명시해야 합니다.

```js
import React from '/Users/Jinwook/path/to/react/index.js';
```

하지만 모듈이나 라이브러리를 `import` 할 때 우리는 보통 이렇게 사용하곤 합니다.

```js
// CommonJS
const _ = require('lodash');

// ESModule
import React from 'react';
import { useForm } from 'react-hook-form';
```

아무런 조치없이 이렇게 사용한다면 문제가 발생합니다. 예를 들어 `react`가 정확히 어떤 버전인지, `react-hook-form`이 어떤 버전인지 모호하다는 것입니다. 예를 들면 React는 18.0.1일 수도, 18.3.1일 수도 있고, 최근에 나온 19 버전일 수도 있기 때문입니다. 따라서 이에 대해 정확히 명시할 필요가 있는데, 이를 소스 코드보다 상위 디렉토리인 **`package.json` 파일에 다음과 같이 명시**합니다.

```json
{
  "dependencies": {
    "react": "^18.2.0" // react는 ≥ 18.2.0, <19 사이의 어떤 버전이든지 쓸 수 있다고 명시
  }
}
```

이렇게 명시된 의존성 정보를 바탕으로, 모든 소스 코드 파일이 특정 버전의 라이브러리를 쓸 수 있도록 보장합니다. `package.json` 파일에 의존성을 명시하고 `npm install` 혹은 `yarn install`을 하면 해당 의존성에 명시된 버전을 설치하게 됩니다. 즉, **패키지 매니저가 모호한 버저닝 문제를 해결**해주는 것입니다.

<br />

### 패키지 매니저가 동작하는 세 단계

패키지 매니저가 버저닝 문제를 해결하기 위해 실제 동작하는 방식은 꽤나 복잡합니다. 다음은 `yarn`을 터미널에서 실행했을 때 볼 수 있는 cli 화면입니다.

<div align="center">
  <img width="50%" alt="image" src="https://github.com/user-attachments/assets/c5eb114b-228e-4673-961c-aa81fed04838" />
</div>

`yarn`을 포함한 패키지 매니저는 위와 같이 **Resolution**, **Fetch**, **Link**의 세 단계로 동작합니다.

- **Resolution 단계**

  첫 번째는 Resolution 단계입니다.

  이 단계의 첫 번째 문제는 **라이브러리를 정확한 버전으로 고정하는 것**입니다. 패키지 매니저는 Resolution 단계에서 `package.json` 파일에 명시된 버전 범위에 따라 정확한 버전을 결정합니다. 예를 들어 `"react": "^18.2.0"`이라고 되어 있으면, `^`이 나타내는 규칙에 따라 ` 18.2.0 ≤ react 버전 ≤ 19` 범위를 만족하는 선에서 가능한 최신 버전을 사용하려고 합니다.

  두 번째 문제는 설치한 라이브러리가 사용하는 다른 라이브러리, 즉 의존성의 의존성 문제입니다. JavaScript에서는 패키지끼리 의존성을 갖는 경우가 흔하기 때문에 **의존성이 또 어떤 의존성을 가지는지 확인하는 작업**이 필요합니다.

  마지막으로, 그 의존성의 버전도 고정해야 합니다. JavaScript에서는 위와 같이 의존성의 버전을 범위로 명시하고, 패키지 간에도 의존성을 가지기 때문에 똑같은 `package.json`에 대해서도 사용하는 의존성 버전이 달라질 수 있습니다.

  예를 들어 어떤 기기에서는 Next.js 14버전과 React 18.1.0 버전을 사용하고, 다른 기기에서는 Next.js 15버전과 React 19.1.0 버전을 사용할 수도 있는데 버전마다 동작이 똑같다면 정말 좋겠지만 아쉽게도 동작이 다를 수 밖에 없고, 버그가 생길 수도 있습니다. 이러한 의존성 문제를 해결하는 것이 Resolution 단계입니다.

  정리하자면, Resolution 단계에서는 모든 기기에서 고정된 버전을 사용할 수 있도록 합니다. 의존성 버전을 전부 고정시키고, 의존성의 의존성을 찾아 그 버전도 고정시키며 결과물을 `package-lock.json` 또는 `yarn.lock` 등에 저장합니다.

- **Fetch 단계**

  두 번째는 Fetch 단계입니다.

  이 단게에서는 Resolution의 결과로 `package-lock.json` 또는 `yarn.lock` 등에 저장된 버전을 실제로 다운로드 합니다. 패키지 매니저는 이 과정에서 네트워크를 통해 필요한 파일들을 가져오는데, 보통 99%는 npm 레지스트리에서 받아오게 됩니다.
  
- **Link 단계**

  세 번째는 Link 단계입니다. 이 단계가 가장 까다로우며, 패키지 매니저마다 상이합니다.

  - npm Linker

    첫 번째로 가장 익숙한 `node_modules` 기반의 Linker입니다. npm Linker의 역할은 **`package.json`에서 명시하는 모든 의존성을 그냥 `node_modules` 디렉토리 밑에 하나씩 쓰는 것**입니다. 예를 들어 소스 코드에서 React와 tds-mobile이라는 라이브러리를 사용한다면, 프로젝트 루트의 `node_modules` 하위에 React와 tds-mobile 패키지를 추가합니다. tds-mobile에도 `node_modules`가 있다면 그 밑에 또 깔아주는 것이 npm Linker가 하는 일입니다.

    ```
    my-project/
    └─ node_modules/
    |  ├─ react/
    |  |  
    |  └─ vite/
    |     └─ node_modules/
    |         └─ @radix-ui/react-dialog
    |
    └─ src
        └─ index.ts
    ```

    이 방식에는 단점이 꽤 많습니다. 먼저 패키지를 찾으려고 하면 `node_modules`를 계속 타고 올라가면서 파일을 여러 번 읽어야 합니다. 그래서 `import`나 `require` 하는 속도가 느려집니다. 또한 **실제로 파일 시스템에 디렉토리와 파일을 하나씩 만들고 쓰기 때문에 디렉토리 크기가 너무 커지게 됩니다**.

    예를 들어, 100개의 프로젝트에서 React 18.2.0 버전을 사용한다고 하면 React 18.2.0이 100번 추가되는 것입니다. 이를 방지하기 위해 호이스팅이라는 방법을 사용하기도 하지만 최적화가 완전히 되는 것이 아니고 불안정해서 좋은 방법은 아닙니다.

  - pnpm Linker
 
    이러한 단점 때문에 만들어진 것이 `pnpm`입니다. 퍼포먼스가 향상된(performant) `npm`이라는 뜻입니다.

    pnpm Linker는 기존의 `node_modules` 디렉토리를 그대로 사용하지만, 보다 빠르고 용량을 최적화하는 방식을 사용합니다. 이는 `npm`처럼 단순히 복붙하는게 아니라 **alias가 생기면 그 곳으로 바로 접근하여 의존성을 디스크에 하나만 설치하는** Hard Link 방식을 사용하기 때문입니다. 따라서 `node_modules` 파일을 쓸 때도 파일을 하나하나 쓸 필요가 없어지고 속도도 훨씬 빨라집니다.

    또한 `node_modules` 디렉토리의 크기도 매우 작습니다. `node_modules` 디렉토리를 계속 돌면서 alias를 걸어야 해서 약간 느리긴 하지만 `npm`처럼 파일 하나씩 쓰는 것은 아니기 때문에 속도가 훨씬 빠르고 호환성이 좋습니다. 다만 `node_modules` 디렉토리를 그대로 유지하기 때문에 `require`, `import` 시 파일 읽기가 많이 발생해서 중간중간 멈추기도 합니다.

  - PnP Linker

    `node_modules` 디렉토리에서 벗어나고 싶다는 생각으로 접근한 것이 PnP(Plug and Play)입니다. PnP는 패키지를 `import`할 때 중요한 것은 단 두 가지라는 관점에서 접근합니다. **어떤 파일에서 `import` 하는가**, 그리고 **무엇을 `import` 하는가**입니다. `npm`이나 `pnpm`처럼 `node_modules`를 순회하는 것이 중요하지 않다고 생각해 `node_modules` 디렉토리가 아니라 JavaScript 객체로 똑똑하게 처리합니다.

    `yarn install`을 하게 되면, 다음과 같은 `.pnp.cjs`라는 파일이 생깁니다. `my-project`라는 디렉토리에서 React를 `import` 할 수 있고, 18.2.0 버전을 사용하면 된다는 뜻입니다.

    ```
    ["my-project", /* ... */ [{
      // ./my-project...
      "packageLocation": "./my-project/",
      "packageDependencies": [
        // React를 import 하면 18.2.0 버전을 제공하라.
        ["react", "npm:18.2.0"]
      ]
    ]
    ```

    이렇게 PnP는 의존성을 찾는 방법을 JavaScript Map으로 관리합니다. `yarn`을 실행하는 순간, Node.js 프로세스가 PnP Map을 메모리에 전부 로드하고 `import`와 `require` 문에서 이 Map을 참조합니다. 이렇게 하면 설치 속도가 빠릅니다. `yarn.lock`을 기반으로 `.pnp.cjs` 파일만 만들어서 쓰면 끝나기 때문입니다. 또한 메모리에 파일이 로드되고 나면, Map 연산만 하면 되기 때문에 `node_modules` 디렉토리를 순회할 필요가 없어 `import`나 `require`하는 속도도 빠릅니다. 이 방법은 Node.js 프로세스가 뜨는 속도가 느리고, `node_modules` 디렉토리와 호환성이 낮다는 단점도 존재합니다.
    
<br />

### PnP vs. Zero-install

PnP와 Zero-install은 다릅니다. 먼저 PnP는 `node_modules` 없이 JavaScript Map 객체를 활용해 의존성을 엄격하고 빠르게 관리하는 접근 방식입니다. Zero-Install은 PnP의 JavaScript Map 객체와 Fetch 된 의존성들까지 모두 Git에 넣어 버전을 관리하자는 방식입니다. 쉽게 말해, `npm`을 사용하면서 `node_modules` 디렉토리를 버전 관리하는 것입니다.

<div align="center">
  <img width="70%" alt="image" src="https://github.com/user-attachments/assets/82ee90ad-e29c-4560-a522-acd93f8e0fc4" />
</div>

npm을 사용하면서 Zero-install을 할 수도 있고, PnP를 사용하면서 Zero-install을 할 수도 있습니다. 하지만 npm 방식으로 설치하면 중복된 의존성들이 너무 많아져서 용량이 커지는 반면, PnP는 효율적으로 하나의 의존성만 설치되므로 버전 관리가 용이합니다.

<br />

## 🗃️ `npm`

`npm`은 Node.js 설치 시에 기본적으로 제공되어 범용적으로 사용되고 있으나, 비효율적이거나 깨져 있는 부분이 많습니다.

<div align="center">
  <img width="50%" alt="image" src="https://github.com/user-attachments/assets/083712e0-3038-4927-ac6e-c2d44b567ff6" />
</div>

<br />

### 비효율적인 의존성 검색

`npm`은 파일 시스템을 이용해 의존성을 관리합니다. `require.resolve.paths()` 함수를 사용해 라이브러리를 찾기 위해 순회하는 디렉토리의 목록을 확인하면 `npm`은 패키지를 찾기 위해서 계속 상위 디렉토리의 `node_modules` 폴더를 탐색합니다. 따라서 패키지를 바로 찾지 못할수록 readdir, stat과 같은 느린 I/O 호출이 반복되고 경우에 따라서는 I/O 호출이 중간에 실패하기도 합니다.

```
$ node
Welcome to Node.js v12.16.3.
Type ".help" for more information.
> require.resolve.paths('react')
[
  '/Users/toss/dev/toss-frontend-libraries/repl/node_modules',
  '/Users/toss/dev/toss-frontend-libraries/node_modules',
  '/Users/toss/node_modules',
  '/Users/node_modules',
  '/node_modules',
  '/Users/toss/.node_modules',
  '/Users/toss/.node_libraries',
  '/Users/toss/.nvm/versions/node/v12.16.3/lib/node',
  '/Users/toss/.node_modules',
  '/Users/toss/.node_libraries',
  '/Users/toss/.nvm/versions/node/v12.16.3/lib/node'
]
```

<br />

### 환경에 따라 달라지는 동작

`npm`은 패키지를 찾지 못하면 상위 디렉토리의 `node_modules` 폴더를 계속 검색합니다. 이 특성 때문에 어떤 의존성을 찾을 수 있는지는 해당 패키지의 상위 디렉토리 환경에 따라 달라지는데, 이렇게 환경에 따라 동작이 변하는 것은 해당 상황을 재현하는 것이 까다로워지기 때문에 나쁜 징조입니다.

<br />

### 비효율적인 설치

`npm`에서 구성하는 `node_modules` 디렉토리 구조는 매우 큰 공간을 차지합니다. 일반적으로 간단한 CLI 프로젝트도 수백 메가바이트의 `node_modules` 폴더가 필요합니다. 용량만 많이 차지할 뿐 아니라, 큰 `node_modules` 디렉토리 구조를 만들기 위해서는 많은 I/O 작업이 필요합니다.

`node_modules` 폴더는 복잡하기 때문에 설치가 유효한지 검증하기 어렵습니다. 예를 들어, 수백 개의 패키지가 서로를 의존하는 복잡한 의존성 트리에서 `node_modules` 디렉토리 구조는 깊어집니다.

이렇게 깊은 트리 구조에서 의존성이 잘 설치되어 있는지 검증하려면 많은 수의 I/O 호출이 필요합니다. 일반적으로 디스크 I/O 호출은 메모리의 자료구조를 다루는 것보다 훨씬 느립니다.

<br />

### 유령 의존성 (Phantom Dependency)

`npm` 및 `yarn` v1에서는 중복해서 설치되는 `node_modules`를 아끼기 위해 끌어올리기(호이스팅) 기법을 사용합니다.

<div align="center">
  <img width="50%" alt="image" src="https://github.com/user-attachments/assets/bffb3ed8-a341-4a59-af42-459d0bc490fe" />
</div>

예를 들어 의존성 트리가 왼쪽의 모습을 하고 있다고 가정하면, 왼쪽 트리에서 [A (1.0)]과 [B (1.0)] 패키지는 두 번 설치되므로 디스크 공간을 낭비합니다. `npm`과 `yarn` v1에서는 디스크 공간을 아끼기 위해 원래 트리의 모양을 오른쪽 트리처럼 바꿉니다. 오른쪽 트리로 의존성 트리가 바뀌면서 `package-1`에서는 원래 `require()` 할 수 없는 [B (1.0)] 라이브러리를 불러올 수 있게 되었습니다.

이렇게 끌어올리기에 따라 직접 의존하고 있지 않은 라이브러리를 `require()` 할 수 있는 현상을 유령 의존성(Phantom Dependency)이라고 부릅니다. 유령 의존성 현상이 발생할 때, `package.json`에 명시하지 않은 라이브러리를 조용히 사용할 수 있게 됩니다. 다른 의존성을 `package.json`에서 제거했을 때 소리없이 같이 사라지기도 합니다. 이런 특성은 의존성 관리 시스템을 혼란스럽게 만듭니다.

<br />

## 🍓 `yarn berry`

`yarn berry`는 위에서 언급된 문제를 Plug'n'Play 전략을 이용하여 해결합니다.

### Plug'n'Play

`node_modules` 파일 시스템을 이용한 의존성 관리는 깨지기 쉽습니다. 따라서 패키지 매니저들이 `node_modules` 디렉토리 구조를 만드는 것에 그치지 않고, 보다 근본적으로 안전하게 의존성을 관리하는 것이 필요해 Plug'n'Play 전략을 이용합니다.

`yarn install`로 의존성을 설치했을 때, `yarn berry`는 `node_modules`를 생성하지 않습니다. 대신 `.yarn/cache` 폴더에 의존성의 정보가 저장되고, `.pnp.cjs` 파일에 의존성을 찾을 수 있는 정보가 기록됩니다. .pnp.cjs를 이용하면 디스크 I/O 없이 어떤 패키지가 어떤 라이브러리에 의존하는지, 각 라이브러리는 어디에 위치하는지를 바로 알 수 있습니다.

<br />

### Zero-Install

의존성도 Git을 이용해 버전 관리를 하면 어떨까요? `yarn` PnP는 의존성을 압축 파일로 관리하기 때문에 의존성의 용량이 작습니다. 또한 각 의존성은 하나의 zip 파일로만 표현되기 때문에 의존성을 구성하는 파일의 숫자가 `npm`만큼 많지 않습니다. 이처럼 용량과 파일의 숫자가 적기 때문에 `yarn berry`를 사용하면 의존성을 Git으로 관리할 수 있습니다. 이렇게 `yarn berry`에서 의존성을 버전 관리에 포함하는 것을 Zero-Install이라고 합니다.

의존성을 버전 관리에 포함하면 많은 장점들이 생깁니다.

1. 새로 저장소를 복제하거나 브랜치를 바꾸었다고 해서 yarn install을 실행하지 않아도 됩니다.
2. CI에서 의존성을 설치하는 시간을 크게 절약할 수 있습니다.

<br />

## :book: 참고

- [패키지 매니저의 과거, 토스의 선택, 그리고 미래](https://toss.tech/article/lightning-talks-package-manager)
