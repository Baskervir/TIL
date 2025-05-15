# OAuth
+ 웹 및 애플리케이션 인증 및 권한 부여를 위한 개방형 표준 프로토콜
+ third-party 애플리케니션이 사용자의 리소스에 접근하기 위한 절차를 정의
+ 서비스 제공자의 API를 사용할 수 있는 권한을 부여
  + Ex. 네이버 로그인, 구글 로그인

---

## OAuth 역할
+ 리소스 소유자 (Resouce Owner)
  + 리소스에 대한 엑세스 권한을 부여하는 사용자(엔티티)
  + 클라이언트 인증(Authorize)하는 역할을 수행
  + 네이버 로그인에서 네이버 아이디를 소유하고 third-party 애플리케이션(클라이언트)에<br> 네이버 아이디로 소셜 고르인 인증을 하는 사용자를 의미한다
+ 클라이언트 (Client)
  + OAuth를 사용하여 리소스에 접근하려는 third-party 애플리케이션이나 서비스
+ 권한 서버 (Authorization Server)
  + 권한 서버는 클라이언트가 리소스 소유자의 권한을 얻을 수 있도록 도와주는 서버
  + 권한 서버는 사용자 인증, 권한 부여 및 토큰 발급을 관리한다
+ 리소스 서버 (Resource Server)
  + 보호되는 리소스를 호스팅하는 서버
  + 엑세스를 허용하거나 거부한다
  + OAuth 토큰을 사용하여 클라이언트에게 리소스에 엑세스할 권한을 부여하고 실제 데이터를 제공한다

---

## OAuth 용어
+ 엑세스 토큰 (Access Token)
  + 클라이언트가 리소스 서버의 리소스에 접근하기 위한 권한을 부여받는 토큰
  + 엑세스 토큰은 권한 서버로부터 발급된다
  + 제한된 유효 기간을 가지고 있다
+ 리프레시 토큰 (Refresh Token)
  + 엑세스 토큰의 유효 기간이 만료된 후 새로운 엑세스 토큰을 받기 위한 토큰
  + 사용자는 다시 로그인할 필요 없이 토큰 유효 시간 갱신만으로 계속 애플리케이션 사용 가능
+ 범위 (Scope)
  + 클라이언트가 리소스에 대한 특정 작업을 수행할 수 있는지를 저으이하는 문자열
  + 권한 서버에 의해 정의된다
  + 클라이언트는 특정 범위의 엑세스 권한을 요청할 수 있다
+ 인증 코드 (Authorization Code)
  + 클라이언트가 엑세스 토큰을 얻기 위한 중간 단계로 사용되는 코드
  + 인증 코드 부여 (Authorization Code Grant) 방식을 통해 권한 서버로부터 발급
  + 엑세스 토큰과 리프레시 토큰을 얻을 수 있다

---

## OAuth 권한 부여 방식
### 권한 부여 승인 코드 방식 (Authorization Code)
+ OAuth에서 가장 많이 사용되는 인증방식
+ 권한 서버. 인증/인가를 수행하는 서버
+ 클라이언트의 접근 자격을 확인하고 Access Token을 발급하여 권한을 부여하는 역할을 수행
+ Refresh Token 사용 가능 방식
+ 주로 타사의 클라이언트에게 보호된 자원을 제공하기 위한 인증에 사용

![img.png](Authorization_Code.png)
+ 권한 부여 승인 요청 시 `response_type=code`로 지정하여 요청
+ 클라이언트는 권한 서버에서 제공하는 로그인 페이지를 브라우저에 띄워 출력
+ 사용자 로그인 시
  + 권한 서버는 권한 부여 승인 코드 요청
    + 전달받은 `redirect_url`로 `Authorization Code`를 전달
    + `Authorization Code`는 권한 서버에서 제공하는 API를 통해 `Access Token`으로 교환

#### Authorization Code Grant 
```declarative
# Step 1. Authorization
Request (GET)/authorize?response_type=code&client_id=s6BhdRkqt3&state=xyz&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fc
Response: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA&state=xyz

# Step 2. Access Token
Request (POST) /token
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded

Response
{
           "access_token":"2YotnFZFEjr1zCsicMWpAA",
           "token_type":"example",
           "expires_in":3600,
           "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
           "example_parameter":"example_value"

}
```
`Authorization Code 획득 후 해당 Code로 Access Token 획득`

---
### 암묵적 승인 방식 (Implicit)
+ 자격증명을 안전하게 저장하기 힘든 클라이언트에게 최적화된 방식
+ Refresh Token 사용이 불가능항 방식
+ 권한 서버는 `client_secret`를 사용해서 클라이언트를 인증하지 않는다
+ `Access Token`을 획득하기 위한 절차가 간소화된다
  + 응답성과 효율성은 높아지지만, `Access Token`이 URL로 전달된다 //`토큰 탈취 위험 증가`

![img.png](Implicit.png)
+ 권한 부여 승인 요청 시 `reponse_type`을 `token`으로 설정하여 요청
  + 클라이언트는 권한 서버에서 제공하는 로그인 페이지를 브라우저에 출력
  + 로그인이 완료되면
    + 권한 서버는 `Authorization Code`가 아닌 `Access Token`을 redirect_url로 전달

#### Implicit
```declarative
Request (GET)/authorize?response_type=token&client_id=s6BhdRkqt3&state=xyz&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
Response http://example.com/cb#access_token=2YotnFZFEjr1zCsicMWpAA&state=xyz&token_type=example&expires_in=3600
```
`Authorize 요청 시 url로 Access Token이 바로 전달됨`
---
### 자원 소유자 자격증명 승인 방식 (Resource Owner Password Credentials)
+ username, password로 Access Token을 받는 방식
+ 사용자의 ID와 비밀번호를 클라이언트에 직접 제공하게 되므로 보안 위험이 크다
+ 클라이언트가 타사의 외부 프로그램일 경우 이 방식 적용 금지!
  + 자신의 서비스에서 제공하는 애플리케이션일 경우에만 사용되는 인증 방식 `Refresh Token`사용 가능

![img.png](Resource_Owner_Password_Credentials.png)
+ 제공하는 API를 통해 username, password를 전달하여 Access Token을 받는다
+ 권한 서버, 리소스 서버, 클라이언트가 모두 같은 시스템에 속해 있을 때 사용되어여 한다!

#### Resource Owner Password Credentials
```declarative
Request (POST) /token
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded
grant_type=password&username=johndoe&password=A3ddj3w

Response 
{
           "access_token":"2YotnFZFEjr1zCsicMWpAA",
           "token_type":"example",
           "expires_in":3600,
           "refresh_token":"tGzv3JOkF0XG5Qx2TlKWIA",
           "example_parameter":"example_value"
}
```
`Username, Password로 Access Token 획득`
---
### 클라이언트 자격증명 승인 방식 (Client Credentials)
+ 클라이언트의 자격증명 만으로 `Access Token`을 획득하는 방식
  + 클라이언트 자체가 사용자를 대신하여 `Access Token`을 획득한다
+ OAuth의 권한 부여 방식 중 가장 간단한 방식
+ 클라이언트 자신이 관리하는 리소스 혹은 권한 서버에 해당 클라이언트를 위한 제한된 리소스 접근 권한이 설정되어 있는 경우 사용

![img.png](Client_Credentials.png)
+ 백엔드 서비스 간 두 통신
  + 두 서비스가 사용자의 개입 없이 서로 통신할 필요가 있을 때 사용
+ 제한된 리소스에 대한 엑세스
  + 사용자와 관련이 없는 리소스에 대한 엑세스가 필요할 때 사용
+ 애플리케이션 수준의 권한
  + 특정 애플리케이션 또는 서비스에 권한을 부여할 때 사용자 개입이 필요없는 때 사용

#### Client Credentials
```declarative
Request (POST) /token
Authorization: Basic czZCaGRSa3F0MzpnWDFmQmF0M2JW
Content-Type: application/x-www-form-urlencoded
grant_type=client_credentials

Response
{
           "access_token":"2YotnFZFEjr1zCsicMWpAA",
           "token_type":"example",
           "expires_in":3600,
           "example_parameter":"example_value"

}
```
`클라이언트 자격증명만으로 Access Token 획득`

---
### Request and Response Examples
| API Parameter | 설명                                                                                                              | 값 |
| --- |-----------------------------------------------------------------------------------------------------------------| --- |
| client_id<br>client_secret | 클라이언트의 자격증명 클라이언트가 권한 서버에 등록<br>하면 발급받을 수 있으며 권한 서버 연동 시 클라이언트<br> 검증에 시용된다                                     | |
| redirect_url | 권한 서버가 요청에 대한 응답을 보낼 url을 설정                                                                                    | |
| response_type | 권한 부여 동의 요청 시 포함되는 값<br>권한 부여 방식에 대한 설정                                                                         | code: Authorization Code Grant<br>token: Ilplicit Grant |
| state | CSRF 공격에 대비하기 위해 클라이언트가 권한서버에<br>요청 시 포함하는 임의의 문자열<br>클라이언트가 요청 시 state를 포함 시켰다면<br>권한 서버는 동일 값을 클라이언트에게 보내야 한다 | |
| grant_type | Access Token 획득 요청 시 포함되는 값<br>권한 부여 방식에 대한 설정 | authorization code : Authorization Code Grant<br>password: Resource Owner Password Credentials Grant<br> client credentials: Client Credentials Grant |
| code | Authorization Code Grant 방식에서 Access Token요청 시<br>사용된다<br>권한 서버에서 획득한 Authorization Code를 입력 | |
| token_type | 발행된 Token의 타입<br>Bearer, MAC(Message Authorization Code) | |
| expires_in | 토큰 만료 시간 (단위: 초) | |
| example_parameter | Token 타입에 따른 추가 파라미터 | |
g