# HTTP 헤더
+ HTTP 요청(request)이나 응답(reponse)에 포함되어 부가 정보를 전달하는 `key-value`형태의 데이터

---

### 요청 예시
```declarative
GET /hello HTTP/1.1
Host: www.eample.com
User-Agent: Mozilla/5.0
Accept: text/html
```

### 응답 예시
```declarative
HTTP/1.1 200 OK
Content-Type: text/heml; charset=UTF-8
Content-Length: 1024
Set-Cookie: sessionId=abc123;
```

### 분류
| 종류 | 예시 | 설명 |
| --- | --- | --- |
| 요청 헤더 | `Host`, `User-Agent`,<br>`Accept` | 클라이언트 정보와 원하는 데이터 유형 |
| 응답 헤더 | `Content-Type`, `Set-Cookie`,<br>`Location` | 서버의 응답에 대한 정보 |
| 공통 헤더 | `Cache-Control`, `Authorization` | 요청/응답 모두에 사용 가능 |

---

## 자주 쓰는 요청 헤더
| 헤더 | 설명 |
| --- | --- |
| `Host` | 요청 대상 호스트명 (핈) |
| `User-Agent` | 브라우저/클라이언트 종류 |
| `Accept` | 클라이언트라 처리 가능한 데이터 타입 |
| `Content-Type` | 요청 본문(body)의 데이터 형식 |
| `Authorization` | 인증 정보 |
| `Cookie` | 클라이언트에 저장된 쿠키 전송 |

## 자주 쓰는 응답 헤더
| 헤더 | 설명 |
| --- | --- |
| `Content-Type` | 응답 본문의 데이터 형싱 |
| `Content-Length` | 응답 본몬의 크기 |
| `Set-Cookie` | 쿠키를 클라이언트에 설정 |
| `Location` | 리다이렉트 경로 |
| `Cache-Control` | 캐싱 정책 설정 |

---

## 헤더 사용 의미
+ 요청/응답에 메타 정보를 포함시켜 통신 효율을 높인다
+ 형식 맞춤 (JSON, HTML, 파일 등)
+ 보안 처리 (쿠키, 토큰 등)
+ 클라이언트 구분 밑 분석

