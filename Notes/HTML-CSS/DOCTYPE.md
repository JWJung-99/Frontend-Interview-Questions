# DOCTYPE이란?

<br />

## 📄 DOCTYPE

HTML의 `<!DOCTYPE>`은 **웹 브라우저에 해당 문서가 어떤 HTML 버전을 기반으로 작성되었는지 알려주는 선언문**입니다. 문서 최상단에 위치하며 브라우저가 HTML 문서를 해석하고 렌더링하는 방식을 결정합니다.

과거에는 HTML의 다양한 버전이 존재했기 때문에 브라우저가 문서를 올바른 방식으로 해석하려면 적절한 방식으로 `DOCTYPE`을 지정해야 했지만, HTML5 이후로는 선언 방식이 단순화되어 `<!DOCTYPE html>`로 간단하게 선언할 수 있습니다.

`DOCTYPE` 선언이 없다면 브라우저느 문서를 쿼크 모드(quirks mode)로 렌더링할 수 있기 때문에 정확하고 일관된 렌더링을 위해 DOCTYPE 선언은 필수입니다.

> **Quirks 모드**
>
> 웹 브라우저의 레이아웃 엔진에서 사용하는 모드는 **Quirks 모드**, **Limited-Quirks 모드**, **No-Quirks 모드** 세 가지입니다.
>
> - **Quirks 모드**
>
>   Quirks 모드에서 레이아웃은 Navigator 4와 Internet Explorer5의 동작을 모방합니다. 이것은 웹 표준이 널리 채택되기 전에 구축된 웹 사이트를 지원하기 위해 필수적입니다.
>
> - **No-Quirks 모드 (구. full standards 모드)**
>
>   No-Quirks 모드에서 레이아웃은 현대 HTML 및 CSS 명세에서 설명한대로 동작합니다.
>
> - **Limited-Quirks 모드 (구. almost-standards 모드)**
>
>   Limited-Quirks 모드에서 레이아웃은 일부 Quirks만 구현됩니다.

<br />

## 📖 참고

- [Quirks 모드](https://developer.mozilla.org/ko/docs/Web/HTML/Guides/Quirks_mode_and_standards_mode)
