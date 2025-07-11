# 인증과 인가란?

<br />

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/64b906e5-1b57-4c52-9516-e6a40c771cc5" />

<br />

## 🪪 인증

인증(Authentication)은 **사용자가 누구인지 신원을** 확인하는 과정입니다. 예를 들어, 웹사이트에 로그인할 때 아이디와 비밀번호를 입력받아 해당 사용자가 누구인지 검증하는 것이 이에 해당합니다.

<img src="https://blog.kakaocdn.net/dna/lJnbC/btrUTJ4e6RK/AAAAAAAAAAAAAAAAAAAAAIb6UwbgkjWQ9p4erg_N7WOAbG46obs35af0x6b8Qaj3/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1753973999&allow_ip=&allow_referer=&signature=L6qf97EWSvJ%2F9N2WpX2fr%2BWEK%2Fg%3D" />

인증 방식에는 비밀번호 인증, 생체 인증, OTP 인증 등이 있습니다. 또한 최근에는 보안 강화를 위해 MFA(다중 요소 인증)도 널리 사용되고 있습니다.

<br />

## 🛂 인가

인가(Authorization)은 **사용자가 특정 자원에 접근할 권한이 있는지** 확인하는 과정입니다. 예를 들어, 회사 내부 시스템에서 일반 직원은 고객 데이터를 조회할 수 없지만 관리자 계정은 조회할 수 있도록 설정하는 것이 이에 해당합니다.

<img src="https://blog.kakaocdn.net/dna/bzYKs4/btrUP0y1fRM/AAAAAAAAAAAAAAAAAAAAALG01GO0An3AjvoLtY4gYm9qHVhSlHljjvukdNAnkUcK/img.png?credential=yqXZFxpELC7KVnFOS48ylbz2pIh7yKj8&expires=1753973999&allow_ip=&allow_referer=&signature=GO28ibkKISEy20xBcU%2BNZ%2BC%2FrVk%3D" />

<br />

## ↔️ 인증과 인가의 차이점

인증과 인가는 비슷해 보이지만 엄연한 차이가 존재합니다.

||**인증(Authentication)**|**인가(Authorization)**|
|---|---|---|
|**기능**|자격 증명 확인|권한 허가/거부|
|**방식**|비밀번호, 생체 인식, 일회용 PIN 등|보안 팀에서 관리하는 설정 사용|
|**사용자가 볼 수 있는가?**|가능|불가능|
|**사용자가 직접 변경할 수 있는가?**|부분적으로 가능|불가능|
|**데이터 전송**|ID 토큰 사용|액세스 토큰 사용|

<br />

## :book: 참고

- [Authentication vs. Authorization](https://www.okta.com/ko-kr/identity-101/authentication-vs-authorization/)
- [Inpa Dev - 쉽게 이해하는 Authentication vs Authorization 차이](https://inpa.tistory.com/entry/CS-%F0%9F%91%A8%E2%80%8D%F0%9F%92%BB-Authentication-vs-Authorization-%EC%B0%A8%EC%9D%B4-%EC%97%84%EC%B2%AD-%EC%89%BD%EA%B2%8C-%EC%84%A4%EB%AA%85)
