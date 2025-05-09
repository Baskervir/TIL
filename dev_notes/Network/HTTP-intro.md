# HTTP 개요

- **HyperText Transfer Protocol**
- 웹 상에서 클라이언트(Client)와 서버(Server)가 **문서, 리소스**를 교환하기 위한 프로토콜
- **무상태(stateless)** 방식: 각 요청은 독립적이며, 이전 요청 정보를 서버가 저장하지 않음

---

## HTTP 요청-응답 흐름

1. 클라이언트 (브라우저, 앱 등)
    - URL(예: `https://api.example.com/users`)을 기반으로
    - HTTP 요청(Request)을 생성

2. TCP 연결
    - HTTP/1.1 : 요청마다 또는 keep-alive로 연결 재사용
    - HTTP/2 : 하나의 연결(Multiplexed Stream)으로 다수의 요청 처리

3. 서버 수신 → 요청 파싱
    - Start-Line : `METHOD URI VERSION`
    - Headers : 메타정보 (콘텐츠 타입, 인증, 캐시 등)
    - Body (선택) : POST/PUT 등에서 데이터 전달

4. 서버 처리
    - 요청 경로에 맞는 비즈니스 로직 수행
    - DB 조회, 연산, 외부 API 호출 등

5. HTTP 응답(Response) 생성
    - Status-Line : `VERSION STATUS_CODE REASON_PHRASE`
    - Headers
    - Body : HTML, JSON, 이미지 등

6. 클라이언트 수신 → 처리
    - 상태 코드에 따라 화면 렌더링, 에러 처리 등 수행

---

## 주요 HTTP 메서드

| 메서드  | 용도               | Idempotent |
|---------|--------------------|---------------|
| GET     | 리소스 조회        | O             |
| POST    | 리소스 생성/행위   | X             |
| PUT     | 리소스 전체 수정   | O             |
| PATCH   | 리소스 부분 수정   | X (사실상 비멱등) |
| DELETE  | 리소스 삭제        | O             |
| HEAD    | GET과 동일하나 본문 없음 | O          |

- `Idempotent` : 여러 번 호출해도 결과가 동일해야 함

---

## 상태 코드(Status Code)

| 그룹  | 범위  | 의미                            |
|-------|-------|---------------------------------|
| 1xx   | 100–199 | 정보 제공                       |
| 2xx   | 200–299 | 성공 → 주로 `200 OK` 사용       |
| 3xx   | 300–399 | 리다이렉션 → `301`, `302` 등    |
| 4xx   | 400–499 | 클라이언트 오류 → `404`, `401` 등 |
| 5xx   | 500–599 | 서버 오류 → `500 Internal Server Error` 등 |

---

## 핵심 헤더(Header)

- 요청 헤더
    - `Host` : 요청 대상 서버(도메인)
    - `Accept` : 클라이언트가 처리 가능한 미디어 타입
    - `Authorization` : 인증 토큰/크리덴셜
    - `Cookie` : 세션 식별 등

- 응답 헤더
    - `Content-Type` : 본문 미디어 타입 (`application/json`)
    - `Set-Cookie` : 클라이언트에 쿠키 전달
    - `Cache-Control` : 캐싱 정책 제어
    - `Location` : 리다이렉트 대상 URL

---

## HTTP 버전 차이

- HTTP/1.1
    - 텍스트 기반, 요청마다 헤더 재전송
    - Keep-Alive 지원

- HTTP/2
    - 바이너리 프레임, 멀티플렉싱 & 헤더 압축
    - 하나의 TCP 연결에서 동시 다중 요청 처리

- HTTP/3 (QUIC 기반)
    - UDP 위에서 동작, 연결 복구 및 지연 시간 개선

---

## HTTP의 특징 & 한계

- 무상태 : 서버가 요청 간 문맥을 저장하지 않음  
  → 세션, JWT, 쿠키 등 별도 기술로 상태 유지
- 캐싱 : `Cache-Control`, `ETag` 등을 통해 성능 최적화
- 보안 : HTTPS(TLS)를 통해 암호화 & 신원 확인

---

> TIP:
> - RESTful API 설계 시 HTTP 메서드와 상태 코드의 의미를 정확히 지키면,  
    >   API의 가독성과 일관성을 크게 높일 수 있어.
> - 실제 트래픽 분석 시 HTTP 헤더를 잘 활용하면, 문제 원인 파악이나 최적화에 도움이 됨.
