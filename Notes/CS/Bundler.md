# 번들러와 빌드 도구란?

<br />

## 💐 번들러

Webpack이나 Parcel, Rollup 등의 번들러는 현대적인 웹 개발의 필수적인 도구입니다. 모듈 시스템이 표준화되지 않았던 시기에는 여러 JavaScript 파일을 단순히 HTML `<script>` 태그로 연결하는 방식을 사용했는데, 이러한 방식은 전역 오염이나 의존성 관리가 어려워지는 문제가 있었습니다. 따라서 CommonJS, AMD, UMD 등의 모듈 시스템이 등장했고, 이러한 모듈 시스템을 효율적으로 브라우저에서 사용하기 위해 번들러가 등장했습니다.

번들러는 **웹 애플리케이션을 개발하기 위해 필요한 HTML, CSS, JavaScript 등의 모듈화된 자원들을 모아서 하나 혹은 최적의 소수 파일로 결합**하는 도구입니다. 결합을 위해 프로젝트를 해석하는 과정에서 불필요한 주석이나 공백 제거, 난독화, 파일 압축 등 기본적인 작업 뿐만 아니라 최신 문법이나 기타 개발에 편리한 특수 기능 등을 브라우저가 지원하는 형태로 변환하는 작업도 수행할 수 있도록 확장되었습니다. 결국, 현대의 번들러는 개발자의 작업 효율성을 높이고 브라우저의 호환성이나 성능 등을 개선하는데 크게 도움을 줍니다.

다음의 예시 코드에서 사용하는 각 JavaScript 파일은 모두 동일한 `title` 변수를 사용하고 있으며, 하나의 HTML 파일에 연결되어 있기 때문에 `title` 변수가 중복 선언되어 오류가 발생합니다.

```html
<link rel="stylesheet" href="./main.css">
<script defer src="./containerH1.js"></script>
<script defer src="./containerH2.js"></script>

<div class="container">
  <button>Toggle!</button>
  <h1></h1>
</div>
<div class="container">
  <h2></h2>
</div>
```

```js
// containerH1.js
let title = 'JavaScript!'
const h1El = document.querySelector('.container h1')
h1El.textContent = title

let isShow = true
const buttonEl = document.querySelector('.container button')
buttonEl.addEventListener('click', () => {
  isShow = !isShow
  isShow
    ? h1El.style.display = 'block'
    : h1El.style.display = 'none'
})

//containerH2.js
let title = 'Bundler!' // Error: Identifier 'title' has already been declared
const h2El = document.querySelector('.container h2')
h2El.textContent = title
```

간단히 다음과 같이 즉시 실행 함수를 사용한 지역화를 통해 중복 선언을 피할 수 있지만, 규모가 조금만 확장되더라도 모든 파일을 이와 같은 방식으로 관리하기는 어렵습니다.

```js
// containerH1.js
;(() => {
  let title = 'JavaScript!'
  const h1El = document.querySelector('.container h1')
  h1El.textContent = title

  let isShow = true
  const buttonEl = document.querySelector('.container button')
  buttonEl.addEventListener('click', () => {
    isShow = !isShow
    isShow
      ? h1El.style.display = 'block'
      : h1El.style.display = 'none'
  })
})()

//containerH2.js
;(() => {
  let title = 'Bundler!' // OK!
  const h2El = document.querySelector('.container h2')
  h2El.textContent = title
})()
```

<br />

이번에는 번들러를 사용하는 코드로 변경합니다. HTML에는 메인 스크립트 파일(`main.js`)만 연결하고, 그 파일에서 각 파일을 가져오도록 작성합니다. `import` 키워드로 가져오는 스크립트 파일은 모듈로 인식되어 각 파일의 변수는 전역으로 선언되지 않고 **모듈 내부에서만 유효한 지역 변수로 선언**됩니다.

```js
import './main.css'
import './containerH1.js'
import './containerH2.js'
//
import { fade } from './animate.ts'
import App from './App.vue'
import data from './data.json'
// ...
```

<br />

### Webpack

Webpack은 대표적인 JavaScript 번들러로, 많은 기능과 확장성으로 복잡한 프로젝트에서도 효율적으로 모듈을 관리할 수 있도록 도와줍니다. Webpack이 제공하는 기능은 다음과 같습니다.

- 모듈 번들링(Module Bundling): 진입점에 연결된 파일을 단일 파일로 묶어줍니다.
- 번들 최적화(Automatic Bundle Optimization): 번들 최적화를 통해, 보다 더 작은 번들을 생성하고 그만큼 빠르게 로딩할 수 있습니다.
- 코드 스플리팅(Code Splitting): 모듈을 청크(chunk)로 분리하여, 동적으로 필요한 모듈만 로딩할 수 있습니다.
- 트리 쉐이킹(Tree Shaking): 사용되지 않는 코드를 제거해 번들의 크기를 줄이고 성능을 향상시킬 수 있습니다.
- 개발 서버 실행(Development Server): 코드가 변경될 때마다 브라우저에 반영(HMR)되는 개발용 서버를 실행할 수 있습니다.

<br />

### Parcel

Parcel은 Webpack과 달리 별도의 구성 파일 없이 간단하게 사용할 수 있는 번들러로, 최소한의 구성(zero config)을 지향합니다. 주로 사용하는 기능이나 필요한 기능을 자동으로 설치하고 처리하기 때문에 쉽고 빠르게 프로젝트를 구성할 수 있습니다.

<br />

## 👷 빌드 도구

번들러는 여러 자원을 모아서 하나 혹은 최적의 소수 파일로 결합합니다. 빌드 도구는 프로젝트 빌드 과정의 전체적인 흐름을 관리하기 위한 도구로, 보통 번들러를 기본으로 포함하고 개발 서버나 린팅, 테스트 등 추가로 개발에 필요한 여러 도구를 가지고 있습니다.

<br />

### Vite

Vite는 Vue.js의 창시자인 Evan You가 만든 빌드 도구로, 처음에는 Vue.js 프로젝트를 위해 시작되었지만 React나 Svelte 같은 다른 프레임워크에서도 사용할 수 있도록 확장되었습니다.

- 프로젝트 크기와 상관없이 빠른 속도를 유지하는 개발 서버를 제공(Hot Module Replacement)합니다.
- `jsx`, `ts`, `tsx`, `scss` 등을 추가 구성 없이도 즉시 지원합니다.
- 개발 서버와 제품 빌드 간의 차이가 없도록 최적화되어 있습니다.

<br />

> [!NOTE]
>
> ESM 방식은 대부분의 브라우저 환경에서 지원되지만, 개발 모드가 아닌 프로덕션 모드에서 ESM 방식만 사용하는 것은 중첩 가져오기 등의 문제로 비효율적입니다. 코드 스플리팅, 트리 쉐이킹 등의 기능을 위해, 프로덕션 모드에서는 여전히 번들링이 필요하며, Vite 내부적으로 Rollup 번들러를 사용하고 있습니다.


<br />

다음 이미지에서 볼 수 있는 것처럼 번들 방식은 변경된 내용이 있는 경우 전체를 다시 빌드해야 하므로 프로젝트가 커질 수록 빌드 시간이 늘어납니다. 대신 브라우저의 Native ESM 방식을 사용하면 변경된 모듈만 바로 반영할 수 있어 프로젝트 크기와 상관없이 빠르게 개발할 수 있습니다. 

<img width="100%" alt="image" src="https://github.com/user-attachments/assets/05de226d-6a3b-49c1-ba32-b5877adda84f" />

<br />

## :book: 참고

- [번들러와 빌드 도구의 이해](https://www.heropy.dev/p/x8iedW)
