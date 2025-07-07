# Content-Type 헤더란?

<br />

## 🎩 `Content-Type` 헤더

`Content-Type` 헤더는 서버와 클라이언트가 데이터를 주고받을 때 **HTTP 요청과 응답에서 전송되는 데이터의 타입을 명시하는 헤더**입니다.

예를 들어, 클라이언트가 JSON 데이터를 전송하는 경우 `Content-Type` 헤더에 `Content-Type: application/json`을 전달하면 서버는 해당 데이터가 JSON 형식이라는 것을 파악하고 적절한 방식으로 해석할 수 있습니다. 혹은 서버거 클라이언트에게 HTML을 응답할 때 `Content-Type` 헤더에 `Content-Type: text/html`을 지정하면 브라우저가 이를 HTML로 렌더링할 수 있습니다.

`Content-Type` 헤더는 MIME 타입을 기반으로 하며, `[type]/[subtype]` 형식으로 구성됩니다.

- JSON 데이터: `application/json`
- HTML 문서: `text/html`
- 파일 업로드: `multipart/form-data`

`Content-Type`을 정확하게 지정하지 않으면, 클라이언트나 서버에서 데이터를 올바르게 해석하지 못할 수도 있습니다. 예를 들어 JSON 데이터를 전송하면서 `Content-Type: application/x-www-form-urlencoded`로 설정하면 서버는 데이터를 잘못된 방식으로 처리하거나, `415 Unsupported Media Type`를 반환할 수 있습니다.

<br />

## ➕ `Accept` 헤더

`Content-Type`은 전송되는 데이터 타입을 지정하는 반면, `Accept` 헤더는 **응답으로 받고자 하는 데이터의 타입을 지정**합니다. 예를 들어 클라이언트가 JSON 응답을 원할 경우 `Accept: application/json`을 설정해 가능하다면 서버로부터 JSON 형식을 응답 받을 수 있습니다.
