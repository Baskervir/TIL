# 테스트
+ 수동 테스트 : 직접 프로그램을 실행하여 화면을 조작하고 결과를 눈으로 확인
+ 자동 테스트 : 테스트 코드가 프로그램을 실행하고 결과를 확인

---

## 자동 테스트
+ 단위 테스트
  + 하나의 처리를 독립적으로 테스트
+ 통합 테스트
  + 여러 처리를 연결하여 테스트
+ E2E 테스트
  + 애플리케이션 전체(end-to-end)를 연결하여 테스트

## 테스트 범위에 따른 패턴
+ E2E 테스트
  + Controller-View-Service-Repository 처리를 연결하여 테스트
  + UI 테스트
+ Controller-Service-Repository 통합 테스트
  + 테스트 코드는 Controller에 직접 요청 > View가 생성한 HTML을 브라우저에 표시하지 않고 태스트 클래스가 내용을 확인
+ Service-Repository 통합 테스트
  + 테스트 코드는 Service 객체의 메서드를 직접 호출하여 프로그램 실행
+ Controller 단위 테스트
  + 의존 객체인 Service는 `Mock`객체로 전환
+ Service 단위 테스트
  + 의존 객체인 Repository는 `Mock`객체로 전환
+ Repository 단위 테스트
  + 실제로 데이터베이스에 접근

---

## 단위 테스트와 Mock
```java
:테스트 클래스 -> :Service /*Service의 단위테스트*/ -> :Repository의 Mock
```
+ 테스트 클래스를 생성해 Service 객체의 메서드를 직접 호출
+ 의존 객체인 Repository를 Mock 객체로 전환
  + Mock 객체는 실제 데이터베이스에 접근할 필요 없이 Service 객체의 동작을 확인하기 쉽게 미리 정해둔 데이터만 반환
+ Service 객체는 Mock 객체에서 반환된 데이터로 비즈니스 로직 수행

---

