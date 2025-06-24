# 인터넷 창에 `www.google.com`을 입력하면 무슨 일이 일어날까?

<br />

## 🔍 `www.google.com`에 접속

### 1. DNS 조회

사용자가 `www.google.com`에 접속하면 브라우저가 이 도메인 이름을 IP 주소로 변환하는 **DNS 조회(DNS Lookup) ** 과정이 일어납니다. 브라우저는 캐시된 DNS 기록을 먼저 확인하고, 없다면 로컬 DNS 서버에 요청하여 `www.google.com`에 해당하는 IP 주소를 얻습니다.

<br />

### 2. TCP 연결

IP 주소가 확인되면, 브라우저는 서버와 TCP(Transmission Control Protocol) 연결을 수립합니다. 이 과정에서 브라우저가 SYN 패킷을 보내고, 서버가 SYN-ACK 패킷을 보내며, 다시 브라우저가 ACK 패킷을 보내는 **3-way handshake** 과정을 수행합니다.

<br />

### 3. HTTP 요청

TCP 연결이 수립되면 브라우저는 HTTP 또는 HTTPS 요청을 보냅니다. 이 요청은 `GET /HTTP/1.1` 형식으로 웹 페이지를 요청하는 메시지입니다. HTTPS를 사용할 경우 이 단계 이전에 SSL(Secure Sockets Layer))/TLS(Transport Layer Security) handshake 과정도 수행합니다. SSL/TLS handshake 과정에서 브라우저와 서버는 암호화된 연결을 설정하기 위해 보안 인증서를 교환하고 암호화 키를 협상합니다.

<br />

### 4. 서버 응답

서버는 요청을 받고 해당 요청에 대한 리소스(HTML, CSS, JavaScript 등)을 브라우저에게 응답으로 보냅니다. 이 응답은 HTTP 응답 코드(`200 OK`)와 함께 전달됩니다.

<br />

### 5. 브라우저 렌더링

마지막으로 받은 리소스를 바탕으로 브라우저 렌더링 파이프라인을 진행합니다. DOM과 CSSOM을 생성하고, 렌더트리를 구성한 후 레이아웃과 페인트 단계를 통해 웹 페이지가 화면에 표시됩니다.
