# HTTP란?

<br />

## 🛜 HTTP

HTTP란 HyperText Transfer Protocol의 약자로, 웹 상에서 **클라이언트와 서버 간 데이터를 주고받는 데 사용되는 통신 규약**입니다. 클라이언트가 서버에 요청을 보내고 서버가 이에 대한 응답을 반환하는 방식으로 동작합니다.

HTTP는 **비연결성(Stateless)** 을 특징으로 하여 한 번의 요청-응답이 끝나면 연결이 종료됩니다. 또한 통신이 안전하게 연결될 수 있도록 TCP 연결을 사용합니다.

HTTP는 HTML, JSON 등 다양한 데이터 포맷을 전달할 수 있습니다. 요청과 응답에 **URL 경로, 각종 메서드, 상태 코드와 헤더** 등 정해진 몇 가지 정보를 포함합니다.

|**HTTP 요청과 응답 형식**|**예시**|
|:---:|:--:|
|<img alt="image" src="https://www.oreilly.com/openbook/webclient/wcp_0301.gif" />|<img alt="image" src="https://www.oreilly.com/openbook/webclient/wcp_0302.gif" />|

HTTP의 보안을 강화한 HTTPS(HyperText Transfer Protocol Secure)는 HTTP에 TLS/SSL 프로토콜에 따라 **데이터를 암호화**하여 전송합니다. 이를 통해 보안상 중요한 정보들을 안전하게 보호하여 통신을 주고 받습니다.

<br />

## 📬 RESTful API

RESTful API는 **REST(Representational State Transfer) 스타일을 준수해 설계된 API**를 의미합니다. REST는 **웹의 리소스를 클라이언트와 서버가 일관된 방식으로 처리할 수 있도록 하는 설계 원칙**입니다.

기본적으로 REST는 리소스를 고유한 URI로 표현하고, HTTP 메서드(GET, POST, PUT, DELETE 등)를 사용해 행위를 표현합니다.

REST의 핵심 규칙들은 다음과 같습니다.

- **클라이언트와 서버의 분리**: 클라이언트와 서버 간의 역할을 명확히 분리합니다.
- **무상태성(Stateless)**: 서버는 클라이언트의 상태를 저장하지 않으며 각 요청은 독립적으로 처리합니다.
- **일관된 인터페이스(Uniform Interface)**: 고유한 URI로 리소스를 식별하고 일관된 인터페이스를 통해 클라이언트와 서버가 간단하고 예측 가능하게 통신할 수 있게 합니다.
- **캐시 가능성**: 가능하다면 서버의 응답 시간을 개선하기 위해 리소스 캐싱을 지원합니다.

<br />

## 참고

- [Learning HTTP](https://www.oreilly.com/openbook/webclient/ch03.html)

