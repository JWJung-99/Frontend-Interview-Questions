# Reflow와 Repaint의 차이점은?

<br />

## 📚 정의

`reflow`와 `repaint`는 [웹 페이지가 렌더링](https://github.com/JWJung-99/Frontend-Interview-Questions/blob/main/Notes/CS/Browser-Rendering.md)되는 과정에서 발생하는 중요한 작업들입니다. 

만약 JavaScript 코드에 DOM이나 CSSOM을 변경하는 DOM API가 사용된 경우 DOM이나 CSSOM이 변경됩니다. 이 떄 변경된 DOM과 CSSOM은 다시 렌더 트리로 결합되고 변경된 렌더 트리를 기반으로 레이아웃과 페인트 과정을 거쳐 브라우저의 화면에 다시 렌더링합니다. 이를 `reflow`, `repaint`라고 합니다.

|**DOM API에 의한 `reflow`와 `repaint`**|
|:---:|
|<img src="https://github.com/user-attachments/assets/2d919f1a-5cf0-48c0-9e41-46c082d8f504" />|

<br />

`reflow`는 **브라우저가 페이지의 레이아웃을 다시 계산하는 과정**을 의미합니다. DOM의 구조가 변경되거나 CSS 스타일이 변경되면, 브라우저는 각 요소가 화면에 어떻게 배치될지 다시 계산해야 합니다. 노드 추가/삭제, 요소의 크기/위치 변경, 윈도우 리사이징 등 레이아웃에 영향을 주는 변경이 발생한 경우에 `reflow`가 실행됩니다. 예를 들어 CSS에서 요소의 `width`나 `height`를 변경하면, 브라우저는 해당 요소 뿐만 아니라 연관된 모든 요소의 배치를 다시 계산해야 합니다. 이 과정은 모든 부모 요소와 자식 요소까지 영향을 주기 때문에 비용이 많이 드는 작업입니다. 

`repaint`는 재결합된 렌더트리를 기반으로 다시 페인트를 하는 것으로, **요소의 모양이나 스타일이 변경될 때** 발생합니다. 요소의 레이아웃은 그대로이고 색상이나 배경 등 스타일만 변경되는 경우를 말합니다. 이 경우 브라우저 요소는 모양만 다시 그리면 되기 때문에 `reflow`보다 비용이 덜 들지만 여전히 성능에 영향을 줄 수 있습니다.

`reflow`와 `repaint`가 반드시 순차적으로 동시에 실행되는 것은 아니며 레이아웃에 영향이 없는 변경은 `reflow` 없이 `repaint`만 실행됩니다.

<br />

## 📊 최적화

<br />

## 📖 참고
