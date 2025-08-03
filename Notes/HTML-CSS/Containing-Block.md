# 컨테이닝 블록이란?

<br />

## 🧱 컨테이닝 블록

**CSS에서 요소의 위치와 크기를 결정할 때 기반이 되는 부모 또는 조상 요소**를 컨테이닝 블록이라고 합니다. 컨테이닝 블록이 부모 요소의 영역이라고 생각하기 쉽지만, 항상 그런 것은 아닙니다.

<br />

### 컨테이닝 블록이 중요한 이유

요소의 크기와 위치는 컨테이닝 블록의 영향을 받을 때가 많습니다. 특히 `position` 속성이 `absolute` 또는 `fixed`로 설정된 요소는 `width`, `height`, `padding`, `margin` 등의 크기를 결정하는 백분율(%) 값과 `top`, `left` 등 위치에 대한 오프셋을 결정하는 백분율(%) 값이 컨테이닝 블록을 기준으로 계산됩니다.

<br />

### 컨테이닝 블록을 결정하는 요인

- **루트 요소(`<html>`)의 컨테이닝 블록**

  - 문서에서 최상위 요소인 `<html>`의 컨테이닝 블록을 Initial 컨테이닝 블록이라고 합니다.
  - 연속적인 미디어(웹 브라우저 등)에서는 **뷰포트** 크기와 동일합니다.
    <div align="center">
      <img width="70%" alt="image" src="https://github.com/user-attachments/assets/85c44472-089a-4030-9fad-da37271011f8" />
    </div>
  - 페이지 미디어(인쇄물 등)에서는 페이지 영역입니다.
  - Initial 컨테이닝 블록의 `direction` 속성은 루트 요소의 해당 속성 값을 따릅니다.

<br />

- **다른 요소의 컨테이닝 블록**

  루트 요소를 제외한 다른 요소에서는 전적으로 요소의 `position` 속성 값에 따라 컨테이닝 블록이 결정됩니다.

  - `static`, `relative`, `sticky`인 경우

    컨테이닝 블록은 가장 가까운 조상 컨테이닝 블록(예: `display`가 `inline-block`, `block`, `list-item` 등의 요소), 또는 가장 가까우면서 서식 맥락을 형성하는 조상 요소(예: `display`가 `table`, `flex`, `grid` 등의 요소)의 **`Content box`를 따라 형성**됩니다.

    > **User Agent가 문서를 배치하면 모든 요소에 대한 사각형 상자를 생성합니다. 각 상자는 네 가지 영역으로 나뉩니다.**
    >
    > - `Content box`
    > - `Padding box`
    > - `Border box`
    > - `Margin area`
    > 
    > <div align="center">
    >   <img width="70%" alt="image" src="https://github.com/user-attachments/assets/bfdcd866-502e-4860-8bea-ec6101e66c2b" />
    > </div>

    - `static`

      `position: static`인 요소는 컨테이닝 블록이 요소 크기의 기준이 됩니다.
      
    - `relative`

      `position: relative`인 요소는 컨테이닝 블록이 요소 크기의 기준이 됩니다. 하지만 위치 기준은 요소 자체가 배치된 다음의 요소 영역 자체입니다. 위치 설정은 시각적 효과이며, 달리 지정되지 않는 한 조상의 스크롤 가능한 오버플로우 영역을 늘리는 경우를 제외하고는 다른 비자손 상자의 크기나 위치에 영향을 미치지 않습니다.
      
    - `sticky`

      `position: sticky`인 요소는 컨테이닝 블록이 요소 크기의 기준이 됩니다. 요소의 컨테이닝 블록인 가장 가까운 조상 블록 레벨 요소 영역이 가장 가까운 조상 Scroll Container의 Scrollport를 기준으로 `top`, `bottom`, `left`, `right` 속성으로 지정한 임계점에 도달하면 `fixed`처럼 해당 위치에서 고정됩니다. 고정 상태는 컨테이닝 블록의 반대편 가장자리를 만나면 해제됩니다. 지정된 임계점 위치에 도달되기 이전은 `relative`로 배치됩니다.

  - `absolute`인 경우

    `position: absolute`인 요소의 컨테이닝 블록은 가장 가까운 `position` 속성의 값이 `relative`, `absolute`, `fixed`, `sticky`인 조상 요소의 **`Padding box`** 입니다. 만약 그런 조상 요소가 없다면 루트 요소(`<html>`)의 Initial 컨테이닝 블록을 따릅니다.
 
  - `fixed`인 경우

    루트 요소(`<html>`)의 Initial 컨테이닝 블록을 따릅니다. 연속적인 미디어(웹 브라우저 등)에서는 **뷰포트** 크기와 동일합니다. 페이지 미디어(인쇄물 등)에서는 페이지 영역입니다.

  - `absolute`와 `fixed` 경우

    `position` 속성이 `absolute` 나 `fixed` 인 경우, 다음 조건 중 하나를 만족하는 가장 가까운 조상의 내부 여백 영역이 컨테이닝 블록이 될 수도 있습니다.

    - `transform`이나 `perspective` 속성이 `none`이 아님.
    - `will-change` 속성이 `transform`이나 `perspective`임.
    - `filter` 속성이 `none`임. (Firefox에선 `will-change`가 `filter`일 때도 적용)
    - `contain` 속성이 `paint`임.

    

<br />

## :book: 참고

- [CSS 컨테이닝 블록 이해하기](https://codingeverybody.kr/css-containing-block/)
