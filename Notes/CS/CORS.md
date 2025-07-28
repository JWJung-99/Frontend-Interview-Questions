# CORS란?

<br />

## 🛂 CORS

교차 출처 리소스 공유(Cross-Origin Resource Sharing)는 **브라우저가 자신의 출처가 아닌 다른 어떤 출처로부터 자원을 로딩하는 것을 허용하도록 서버가 허가해주는 HTTP 헤더 기반 메커니즘**입니다. 출처(origin)란 URL에서 도메인만 뜻하는 게 아니라 프로토콜과 포트까지 포함하는 개념입니다. 출처를 구성하는 세 요소는 프로토콜, 도메인, 포트로, 이 중 하나라도 다르면 CORS 에러를 만나게 됩니다.

- 도메인(Hostname): `domain-a.com`
- 출처(Origin): `https://www.domain-a.com`

<div align="center">
  <img width="70%" alt="image" src="https://github.com/user-attachments/assets/a2e2ef98-f594-4961-9aee-d5c9b9c9e35e" />
</div>

<br />

"출처가 교차한다"는 것은 리소스를 주고 받으려는 "두 출처가 서로 다르다"는 뜻입니다. CORS를 설정한다는 것은 "출처가 다른 서버 간의 리소스 공유를 허용한다"라는 것입니다. 클라이언트(Front-end)에서 API를 직접 호출하는 방식이 당연해지기 시작하면서, 다른 도메인에 있는 API(Back-end)와 출처가 다르더라도 요청과 응답을 주고받을 수 있도록 서버에 리소스 호출이 허용된 출처를 명시해 주는 CORS 정책이 생겼습니다.

<br />

### 에러 대응하기

- **서버에서 `Access-Control-Allow-Origin` 응답 헤더 세팅하기**

  서버에서 `Access-Control-Allow-Origin` 응답 헤더를 설정해 요청을 수락할 출처를 명시적으로 지정할 수 있습니다. 이 헤더를 세팅하면 출처가 다르더라도 `https://www.domain-a.com`의 리소스 요청을 허용하게 됩니다.

  ```json
  'Access-Control-Allow-Origin': <origin> | *
  ```

  `*`는 출처에 상관없이 리소스에 접근할 수 있는 와일드카드이기 때문에 보안에 취약할 수 있습니다. 따라서 `'Access-Control-Allow-Origin': https://www.domain-a.com`과 같이 허용할 출처를 직접 세팅하는 방법이 더 안전합니다.

<br />

- **Proxy 서버 사용하기**
  
  웹 애플리케이션이 리소스를 직접 요청하는 대신 Proxy 서버를 사용해 웹 애플리케이션에서 리소스로의 요청을 전달하는 방법도 있습니다. 이 방법을 사용하면 **웹 애플리케이션이 리소스와 동일한 출처에서 요청을 보내는 것처럼 보이므로** CORS 에러를 방지할 수 있습니다.

  예를 들어 `http://example.com`에서 동작하는 웹 애플리케이션이 `http://api.example.com`에 데이터를 요청하는 상황을 가정해 봅시다. 두 도메인이 다르기 때문에, 브라우저는 Cross-Origin 요청으로 판단하고 서버가 CORS 설정을 제대로 하지 않았다면 요청이 실패할 수 있습니다. 이 문제를 해결하기 위해, 웹 애플리케이션이 직접 `http://api.example.com`에 API 요청을 보내는 대신, **같은 출처(`http://example.com`)에 위치한 Proxy 서버**를 통해 API 요청을 중개하도록 구성하면 됩니다.

  ```
  웹 애플리케이션 → http://example.com/api/proxy → (서버 내부 요청) → http://api.example.com
  ```

  브라우저 입장에서는 `http://example.com`에 API 요청을 한 것처럼 보이기 때문에 CORS 검사 없이 응답을 받을 수 있습니다.

<br />

## :book: 참고

- [MDN - 교차 출처 리소스 공유 (CORS)](https://developer.mozilla.org/ko/docs/Web/HTTP/Guides/CORS)
- [CORS(교차 출처 리소스 공유)](https://docs.tosspayments.com/resources/glossary/cors)
