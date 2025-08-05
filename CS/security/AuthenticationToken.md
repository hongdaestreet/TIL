# Authentication Token

### 개요

인터넷상에서 당신이 누구인지를 어떻게 증명할 것인가?

1차적으로 로그인, ID/PW로 본인 확인을 확인한다.

하지만 그 이후에 서버가 계속 인증되어있다는 것을 어떻게 알까?

⇒ **Token**으로 인증 상태를 유지함

---

### 목적

서버는 이 Token을 확인해서 요청 승인/거부 처리를 한다.

ex) 로그인 후 토큰 발급 → 클라이언트는 토큰 저장 → 이후 API 호출 시 토큰을 함께 전송함으로써 인증 상태를 유지함

---

## 동작 흐름

1. 로그인 요청
2. 서버가 사용자 인증
3. 토큰 생성
4. 클라이언트에게 토큰 전달
5. 클라이언트는 API 호출할 때 토큰을 같이 보냄
6. 서버는 토큰을 검증해서 응답을 처리함

---

## Access & Refresh Token

Token은 Access Token과 Refresh Token으로 나뉜다.

쉽게 설명하자면, Access는 안심번호? 갬성, Refresh는 진짜 전번? 갬성

Refresh에서 Access를 만들어줌. 그니까 Access 털려도 위험한데, Refresh털리면 그냥 게임 끝 ㅇㅇ

### Access Token

표준 방식 → `Authorization: Bearer <access_token>`

HTTP API 인증에서 **JWT(또는 OAuth 토큰)**을 사용하는 경우:

- `GET`, `POST`, `PUT`, `DELETE` 등 어떤 메서드든 상관없이
- **인증용 access_token은 HTTP 헤더의 `Authorization` 필드에 포함**시키는 것이 표준임.

### Refresh Token

- 왜 Refresh를 쓰나? 걍 인증하면 안되나?
    1. Access 토큰 위험 완화
    2. 재로그인 방지 → 사용자 편의성 향상
- 사용방식
    1. 유지시간 짧게 유지함
    2. 탈취 의심이 되는 refresh 토큰 블랙리스트 등록 차단 기능 사용

---

### 장점

1. Stateless : 서버가 상태를 기억하지 않아도 됨 (JWT)
2. 확장성 : MicroService/SPA/Mobile 에서 유리함
3. 유연성 : 다양한 저장/전송 방식 가능함

### 단점

1. 탈취 시 위험
2. JWT는 만료 전까지 무효화가 불가능함

---

### 주의할 점

1. 인증 토큰은 항상 Header에 담아야 함
    - Header에 담아야 하는 이유
        
        
        | 이유 | 설명 |
        | --- | --- |
        | **HTTP 표준 준수** | 인증 정보를 Header에 담는 것이 RFC 및 OAuth2 권장 방식 |
        | **캐싱 방지 및 안전성** | `GET` 요청은 일반적으로 캐싱되거나 URL 공유 가능성이 있음 → Body에 인증 정보 넣는 건 보안상 위험 |
        | **RESTful 원칙에 부합** | `GET` 요청은 리소스만 요청해야 하고, body를 보내는 게 비표준임 |
        | **서드파티 도구 및 클라이언트 호환성 향상** | Postman, Swagger, axios 등은 `Authorization` 헤더를 자동으로 처리할 수 있음 |
    - Body에 넣으면 안되는 이유
        - **보안상 안전하지 않거나**, **HTTP 표준을 위반**할 수 있음
        - 특히 `GET` 메서드는 **Body를 지원하지 않는 클라이언트**도 많음 (브라우저, 프록시, CDN 등).
2. HTTPS 필수 : 모든 토큰은 암호화된 채널에서만 전달해야 함
3. 만료 시간 설정 : Access 짧게, Refresh 길게
4. 토큰 탈취 대비 : Refresh 토큰은 서버에서 검증해야 함 (DB 저장)
5. 로그아웃 처리 : JWT는 서버 상태 없음 → 블랙리스트 or 토큰 재발급 제한
    - 블랙리스트
        
        더 이상 유효하지 않은 토큰 목록
        
        - 로그아웃해도 토큰은 계속 유효함 (JWT는 무상태니까)
        - 누군가 토큰 탈취하면 로그아웃해도 사용할 수 있음
        
        ### 활용
        
        1. 사용자 로그아웃 → 서버는 그 토큰을 블랙리스트(redis)에 저장
        2. API 요청 → 블랙리스트에 있는지 확인 → 있으면 거부

---

## 종류

### 1. JWT

(자세한 건 JWT 파트에서...)

**JSON Web Token** 

### 2. Session ID

(자세한 건 Session 파트에서…)

### 3. API Key

### 4. Opaque Token (ex. OAuth 2.0)

(자세한 건 OAuth 파트에서…)