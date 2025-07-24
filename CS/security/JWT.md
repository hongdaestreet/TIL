# JWT

**[Json Web Token]** JSON 객체에 인증에 필요한 정보들을 담은 후 비밀키로 서명한 토큰 (인터넷 표준 인증 방식)

인증 & 권한허가

## 구조

`Header`.`Payload`.`Signature`

- Header : 해시 암호화 알고리즘 + 토큰 타입
- Payload : 토큰에 담을 클레임 정보 포함
    - 등록된 클레임 : token 정보를 표현하기 위해 이미 정해진 종류의 데이터
        - iss(발급자(issuer)), sub(제목(subject)), aud(대상자(audience)), exp(만료 시간(expiration)), nbf(활성 날짜(not before)), iat(발급시간(issued at)), jti(JWT식별자(JWT ID))
    - 공개 클레임 : (사용자 정의) 공개용 정보를 위해 사용
    - 비공개 클레임 : (사용자 정의) 서버와 클라이언트 사이에 임의로 지정한 정보를 저장
- Signature : Header+Payload+Secret Key

## 동작 방식

<img width="1280" height="680" alt="Image" src="https://github.com/user-attachments/assets/3b49a329-3af2-494d-8e9b-4251571f857b" />

<img width="3744" height="1952" alt="Image" src="https://github.com/user-attachments/assets/6e84ad3d-970b-41c8-a43f-8f68af2b7c8e" />

- jwt 발급 전
    1. 사용자가 ID/PW나 소셜 로그인으로 서버에 로그인 요청
    2. 서버는 비밀키를 사용해서 json 객체 암호화한 JWT 토큰을 발급함
    3. JWT를 header에 담아 클라이언트에 보냄
- jwt 발급 후
    1. 클라이언트는 JWT를 로컬에 저장함
    2. API 호출할때마다 header에 JWT를 실어 보냄
    3. 서버는 헤더를 매번 확인하여 사용자 체크하고 인증되면 API 응답 보냄

### Access Token

- 인증된 사용자가 일정 기간동안 재인증하지 않아도 되도록 함
- 사용자의 인증 정보 포함
- 보호된 리소스에 접근하기 위해 사용됨

### Refresh Token

Access Token을 refresh하는 것을 보장해줌

Access Token이 만료되면 새로 발급해줌

## 장점

- stateless → 서버는 토큰의 유효성만 검증하므로 새션 관리가 필요X
- 토큰 자체가 인증된 정보 → 별도의 인증 저장소(ex. 세션 저장소) 필요X
- 클라이언트 상태를 서버가 저장하지 않아도 됨
- 로그인할때 한번만 인증하면 돼서 DB조회 한번만 함