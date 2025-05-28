# HTTP 흐름 파악하기
---
## HTTP 메시지 구조 이해
1. HTTP 요청 메시지 구조 (Client → Server)
```
요청 라인
GET /index.html HTTP/1.1

헤더
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html

(공백 한 줄)

메시지바디 (body)
- POST나 PUT일 때만 있음 (예: JSON 데이터 등)
```
### 구성 요소
+ 요청 라인 : 어떤 요청을 하는지 (메서드, 경로, HTTP 버전)
+ 헤더 : 부가 정보 전달 (브라우저 정보, 수용 데이터 형식 등)
+ 바디 : 보낼 데이터

2. HTTP 응답 메시지 구조 (Server → Client)
```
상태 라인
HTTP/1.1 200 OK

헤더
Content-Type: text/html
Content-Length: 342

(공백 한 줄)

메시지 바디
<html>...</html>
```
### 구성 요소
+ 상태 라인 : 서버의 응답 상태 (버전, 코드, 메시지)
+ 헤더 : 응답에 대한 정보 (형식, 길이, 캐시 등)
+ 바디 : 실제 데이터 (HTML, JSON, 이미지 등)

---

## HTTP 전체 흐름 기반의 기븐 분류
1. 클라이언트 요청 시작

| 구성요소 | 설명 | 키워드 |
| --- | --- | --- |
| 주소 입력 | 브라우저에 URL 입력 | Ex.https://example.com |
| DNS 조회 | 도메인을 IP로 변환 | `DNS 서버`, `A record`, `도메인 → IP` |
| TCP 연결 | 3-way handshake 수행 | `SYN`, `ACK`, `포트 80/443` |

2. HTTP 요청 전송

| 구성요소 | 설명 | 키워드 |
| --- | --- | --- |
| 요청 메시지 | 메서드, 경로, 헤더, 바디 포함 | `GET`, `POST`, `Accept`, `User-Agent` |
| 요청 메서드 구분 | 데이터 조회, 전송, 수정 등 목적 구분 | `GET`, `POST`, `PUT`, `DELETE`, `PATCH` 등 |
| 인증정보 포함 | 쿠키, 토큰 등으로 사용자 인증 | `Cookie`, `Authorizaion`, `JWT`, `Session ID` |

3. 서버 처리 & 응답

| 구성요소 | 설명 | 키워드 |
| --- | --- | --- |
| 응답 메시지 | 상태라인, 헤더, 바디 구성 | `200 OK`, `Content-Type`, HTML/JSON |
| 상태 코드 구분 | 서버의 응답 의미를 표현 | `2xx`, `3xx`, `4xx`, `5xx` |
| 캐싱/리디렉션/오류처리 | 부라 응답 전략 포함 | `Cache-Control`, `302`, `404`, `500` 등 |

4. 후속 동작 (브라우저 & 네트워크)

| 구성요소 | 설명 | 키워드 |
| --- | --- | --- |
| 렌더링 | HTML, CSS, JS 등을 브라우저가 해석 | `DOM`, `렌더 트리`, `JS 실행` |
| 보안 및 최적화 | HTTPS 암호화, CDN, 프록시 | `TLS`, `프록시`, `CDN`, `압축` |

### 흐름 요약
```
[사용자 입력]
  → [DNS 조회]
    → [TCP 연결]
      → [HTTP 요청 전송]
        → [서버 처리]
          → [HTTP 응답 수신]
            → [브라우저 처리 및 렌더링]
```