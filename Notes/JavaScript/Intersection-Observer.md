# Intersection Observer란?

<br />

## 🧐 Intersection Observer

Intersection Observer는 브라우저 뷰포트와 설정한 요소의 **교차점을 관찰**하여, 요소가 뷰포트에 포함되는지 포함되지 않는지 구별하는 기능을 제공합니다. 이 기능은 비동기적으로 실행되기 때문에 `scoll` 같은 이벤트 기반의 요소 관찰에서 발생하는 렌더링 성능이나 이벤트 연속 호출 같은 문제 없이 사용할 수 있습니다.

<div align="center">
  <img width="70%" alt="image" src="https://github.com/user-attachments/assets/c52995c0-3fbc-421b-b5a6-c6dffd2b4f78" />
</div>

<br />

Intersection Observer API는 특정 요소가 다른 요소(또는 뷰포트)와의 교차점에 들어가거나 나갈 때, 또는 두 요소 간의 교차점이 지정된 양만큼 변화될 때 실행되는 콜백 함수를 코드에 등록할 수 있습니다.

<br />

### 사용법

- **교차 관찰자 생성하기**

  `new IntersectionObserver()`를 통해 생성한 인스턴스(`io`)로 관찰자(Observer)를 초기화하고 관찰할 대상(Element)을 지정합니다. 생성자는 두 개의 인수(`callback`, `options`)를 가집니다.
  
  ```js
  const observer = new IntersectionObserver(callback, options); // 관찰자 초기화
  observer.observe(element); // 관찰할 대상(요소) 등록
  ```

<br />

- **콜백**

  관찰할 대상(Target)이 등록되거나 가시성(Visibility)에 변화가 생기면 관찰자는 콜백을 실행합니다. 콜백은 두 개의 인수(`entries`, `observer`)를 가집니다.

  ```js
  const options = {};
  const io = new IntersectionObserver((entries, observer) => {}, options);
  io.observe(element);
  ```

  - `entries`

    ```js
    const io = new IntersectionObserver((entries, observer) => {
      entries.forEach(entry => {
        console.log(entry);
      })
    }, options);
    ```

    `entries`는 IntersectionObserverEntry 인스턴스의 배열로, `.observe()` 메서드로 관찰하는 대상들을 의미합니다. IntersectionObserverEntry는 읽기 전용의 다음 속성들을 포함합니다.

    - `boundingClientRect`: 관찰 대상의 사각형 정보
      
      이 값은 `Element.getBoundingClientRect()`를 사용해 동일하게 얻을 수 있으나 `getBoundingClientRect` 호출에서는 Reflow가 발생합니다.

      <div align="center">
        <img width="70%" alt="image" src="https://github.com/user-attachments/assets/a91b0436-e5b5-4e23-a84c-cb8a68a18546" />
      </div>
    
      |`boundingClientRect`|속성 정보|
      |:---:|:---:|
      |<img alt="image" src="https://github.com/user-attachments/assets/3401d9de-6c91-41f3-9d9f-13779ad07ca5" />|<img alt="image" src="https://github.com/user-attachments/assets/82698820-cf4b-42b7-a581-8c703567e3a3" />|
      
    - `intersectionRect`: 관찰 대상의 교차하는 영역에 대한 사각형 정보
   
      <div align="center">
        <img width="70%" alt="image" src="https://github.com/user-attachments/assets/7dde53f8-0a19-42d1-9e28-334973d056c0" />
      </div>

    - `intersectionRatio`: 관찰 대상의 교차한 영역 백분율(`intersectionRect` 영역에서 `boundingClientRect` 영역까지 비율)

      관찰 대상이 루트 요소와 얼마나 교차하는지의 수치를 `0.0`과 `1.0` 사이의 숫자로 반환합니다. 이는 `intersectionRect` 영역과 `boundingClientRect` 영역의 비율을 의미합니다.
      
    - `isIntersecting`: 관찰 대상의 교차 상태

      관찰 대상이 루트 요소와 교차 상태로 들어가거나(`true`) 교차 상태에서 나가는지(`false`) 여부를 나타내는 값입니다.

      <div align="center">
        <img width="70%" alt="image" src="https://github.com/user-attachments/assets/511108fd-3517-4f89-9541-40d4ab5f45a9" />
      </div>

    - `rootBounds`: 지정한 루트 요소의 사각형 정보

      옵션 `rootMargin`에 의해 값이 변경되며, 만약 별도의 루트 요소를 선언하지 않았을 경우 `null`을 반환합니다.

    - `target`: 관찰 대상 요소

      관찰 대상을 반환합니다.

    - `time`: 변경이 발생한 시간 정보

      문서가 작성된 시간을 기준으로 교차 상태 변경이 발생한 시간을 나타내는 TimeStamp를 반환합니다.

  - `observer`

    콜백이 실행되는 해당 인스턴스를 참조합니다.

    ```js
    const io = new IntersectionObserver((entries, observer) => {
      console.log(observer);
    }, options)
    
    io.observe(element);
    ```

<br />

- **옵션**

  관찰 범위를 설정하기 위한 몇 가지 옵션을 사용할 수 있습니다.

  - `root`

    관찰 대상의 가시성을 검사하기 위해 뷰포트 대신 사용할 요소 객체를 지정합니다. 관찰 대상의 조상 요소여야 하며 지정하지 않거나 `null`일 경우 브라우저의 뷰포트가 기본 사용됩니다. 기본값은 `null`입니다.

    ```js
    const io = new IntersectionObserver(callback, {
      root: document.getElementById('my-viewport'),
    });
    ```

    <div align="center">
      <img width="70%" alt="image" src="https://github.com/user-attachments/assets/fdf41979-6e14-46a5-a019-e204940d76db" />
    </div>

  - `rootMargin`

    바깥 여백(margin)을 이용해 `root`의 범위를 확장하거나 축소할 수 있습니다. CSS의 `margin`과 같이 4단계로 여백을 설정할 수 있으며, `px` 또는 `%`로 나타낼 수 있습니다.

    |`rootMargin: '0px'`|`rootMargin: '100px'`|`rootMargin: '-100px'`|
    |:---:|:---:|:---:|
    |<img alt="image" src="https://github.com/user-attachments/assets/13835881-a4d9-4630-892e-baf2f4b5cc14" />|<img alt="image" src="https://github.com/user-attachments/assets/e55b4f10-42ad-4f65-af94-6598640688ee" />|<img alt="image" src="https://github.com/user-attachments/assets/f16bdc94-326f-4214-afdd-69aaa569b921" />|

  - `threshold`

    옵저버가 실행되기 위해 관찰 대상의 가시성이 얼마나 필요한지 백분율로 표시합니다. 기본값은 Array 타입의 `[0]`이지만 Number 타입의 단일 값으로도 작성할 수 있습니다.

    - `0`: 관찰 대상의 가장자리 픽셀이 `root`를 교차하는 순간(가시성이 0% 이상일 때) 옵저버가 실행됩니다.
    - `0.3`: 관찰 대상의 가시성이 30% 이상일 때 옵저버가 실행됩니다.
    - `[0, 0.3, 1]`: 관찰 대상의 가시성이 0%, 30%, 100%일 때 모두 옵저버가 실행됩니다.
 
    <br />
    
    ```js
    const io = new IntersectionObserver(callback, {
      threshold: 0.3, // or `threshold: [0.3]`
    });
    ```
    <br />
    
    |`threshold: 0`|`threshold: 0.3`|`threshold: 1`|
    |:---:|:---:|:---:|
    |<img alt="image" src="https://github.com/user-attachments/assets/f59dcf94-32d1-4d78-9cf0-e1f229ef01b1" />|<img alt="image" src="https://github.com/user-attachments/assets/a2b9f62d-f5ed-49fd-b806-b6f26789be6f" />|<img alt="image" src="https://github.com/user-attachments/assets/c3a61bd1-9cae-4d6d-8420-e307f61c6e7c" />|

<br />

- **메서드**

  - `observe`

    대상 요소의 관찰을 시작합니다.

    ```js
    const io1 = new IntersectionObserver(callback, options);
    const io2 = new IntersectionObserver(callback, options);
    
    const div = document.querySelector('div');
    const li = document.querySelector('li');
    const h2 = document.querySelector('h2');
    
    io1.observe(div);
    io2.observe(li);
    io2.observe(h2);
    ```

  - `unobserve`

    대상 요소의 관찰을 중지합니다. 관찰을 중지할 하나의 대상 요소를 인수로 지정해야 합니다.
    
    ```js
    const io1 = new IntersectionObserver(callback, options);
    const io2 = new IntersectionObserver(callback, options);
    
    // ...
    
    io1.observe(div);
    io2.observe(li);
    io2.observe(h2);

    io1.unobserve(h2);
    io2.unobserve(h2);
    ```

    콜백의 두 번째 인수 `observe`가 해당 인스턴스를 참조하므로, 다음과 같이 작성할 수도 있습니다.
    
    ```js
    const io1 = new IntersectionObserver((entries, observer) => {
      entries.forEach(entry => {
        // 가시성의 변화가 있으면 관찰 대상 전체에 대한 콜백이 실행되므로,
        // 관찰 대상의 교차 상태가 false일(보이지 않는) 경우 실행하지 않음.
        if (!entry.isIntersecting) {
          return;
        }
        // 관찰 대상의 교차 상태가 true일(보이는) 경우 실행.
        // ...
    
        // 위 실행을 처리하고(1회) 관찰 중지
        observer.unobserve(entry.target);
      })
    }, options);
    ```

  - `disconnect`

    IntersectionObserver 인스턴스가 관찰하는 모든 요소의 관찰을 중지합니다.

    ```js
    const io1 = new IntersectionObserver(callback, options);
    const io2 = new IntersectionObserver(callback, options);
    
    // ...
    
    io1.observe(div);
    io2.observe(li);
    io2.observe(h2);

    io2.disconnect(h2);
    ```
  
<br />

## :book: 참고

- [MDN - Intersection Observer API](https://developer.mozilla.org/ko/docs/Web/API/Intersection_Observer_API)
- [JS Intersection Observer, 요소의 가시성 관찰](https://www.heropy.dev/p/ydKoQO)
