## SPA와 웹 서비스
+ Single Page Application
+ 첫 페이제를 불러온다 -> 페이지에 함께 삽입된 JS.Application이 화면을 갱신
+ 이때 필요한 데이터를 웹 서비스로부터 가져와 화면을 갱신한다
+ 화면 전체를 새로고침 하지 않는다

### 마이크로서비스와 웹 서비스
+ 큰 애플리케이션을 만드는 것이 아니라 여러 개의 작은 애플리케이션으로 분할해 개발하는 개념
+ 각각의 애플리케이션을 독립적으로 보수 할 수 있다
+ 개별 애플리케이션에 적절한 기술(프로그래밍 언어, 프레임워크, 데이터베이스 등)을 사용할 수 있다

```text

클라이언트 - (html) - JS.App <-(데이터)-> 웹 서비스 < <-(데이터)-> 웹 서비스
                                                | <-(데이터)-> 웹 서비스
```

---

## REST란?
+ REpresentational State Transfer
+ 서비스를 만들 때 적용하는 가이드라인
+ REST를 적용한 웹 서비스: RESTful 웹 서비스
+ 웹 서비스가 제공하는 HTTP 통신의 창구: 웹 API = REST API
  + URL, HTTP 메서드, 요청 파라미터, 응답 내용 등과 같은 API 사양을 명시

`{웹 서비스 { API }} <-> 클라이언트`

### REST의 규칙
+ URL로 리소스 식별
+ HTTP 메서드로 리소스 조작
+ response body의 데이터 형식
+ request body의 데이터 형식
+ 상태 코드 활용
+ 헤더 활용
+ 서버 측을 무상태로 만들기

### URL로 리소스 식별
+ 처리할 리소스를 URL로 식별

ex. https:/shopping.example.com/products
+ 도메인명 뒤에 `/products`경로가 상품 데이터 전체를 나타낸다

`ex.) https://shpooing.example.com/products/p01`
+ /products 뒤에 붙은 `/p01`이 ID
+ 리소스를 계층적으로 나타낼 수 있다

`ex) https://shopping.example.com/shops/s01/products/p01`
+ `/shops`가 매장을 나타내고 /products가 매장에서 취급하는 상품을 나타낸다
+ `s01`이라는 ID를 가진 매장에서 취급하는 p01 상품을 식별하는 URL

### HTTP 메서드로 리소스 조작
+ URL로 리소스 시별 != 무엇을 하고싶은가!?
+ REST에서는 리소스에 대한 조작을 HTTP 메서드로 표현한다
#### 자주 사용되는 주요 HTTP 메서드
| HTTP 메서드 | 용도 |
| --- | --- |
| GET | 리소스를 가져온다 |
| POST | 리소스를 새로 등록 |
| PUT | 리소스 갱신 |
| DELETE | 리소스 삭제 |

`ex) https://shopping.example.com/products/p01`
+ HTTP 메서드가 PUT인 p01 상품을 갱신하는 요청

### response body의 데이터 형식
+ 서버에서 가져오는 데이터는 응답 바디에 작성된다
+ response body의 데이터 형식은 클라이언트 측에서 선택할 수 있도록 한다
+ HTTP의 요청 헤더 중 하나인 `Accept`헤더를 사용해 가져올 데이터의 형식을 지정

### request body의 데이터 형식
+ 클라이언트 측에서 request body의 데이터 형식을 결정할 수 있다
+ `Content-Type`헤더에 데이터 형식을 기재

### 상태 코드 활용
+ status code: 요청에 대한 처리를 나타내는 상태 코드
+ REST에서는 status code를 적극적으로 활용

### 헤더 활용
+ HTTP 요청과 응답에서는 헤더를 이용해 부가 정보를 지정할 수 있다
+ REST에서는 헤더 활용을 권장
+ 
#### 주요 요청 헤더
| 요청 헤더 | 용도 |
| --- | --- |
| Accept | 응답으로 받고 싶은 데이터 형식 지정 |
| Authorization | 인증 정보를 지정 |
| Content-Type | request body의 데이터 형식 지정 |

#### 주요 응답 헤더
| 응답 헤더 | 용도 |
| --- | --- |
| Content-Type | request body의 데이터 형식 지정 |
| Location | 새로 등록한 리소스의 URL을 지정 |

### 서버 측을 무상태로 만들기
+ REST에서는 서버 측을 `stateless`로 만들 것을 권장
  + `state`: 여러 요청에 걸쳐 관리되는 클라이언트 고유의 데이터 (=세션 데이터)
+ REST에서는 서버 측을 무상태로 + 클라이언트 측에서 상태를 보관

#### 스케일아웃의 문제
+ 일반적으로 서버의 메모리상에 상태를 보관
+ 서버가 한 대라면 클라이언트가 접속하는 서버 동일
  + 항상 상태가 존재
+ 서버가 여러 대의 경우 서버마다 상태가 다르다
  + 새로 증설한 서버로 접속 -> 상태 변경 (불일치)

---