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

## 스프링의 테스트 지원 기능
+ DI 컨테이너 자동 생성
  + JUnit 테스트를 수행하면 자동으로 DI 컨테이너가 생성된가
+ 테스트 클래스에 임의의 Bean 인젝션
  + 테스트 대상 메서드를 가진 Bean을 인젝션하여 테스트 메서드 안에서 호출할 수 있다
+ 지정한 SQL 파일을 테스트 시 불러와 실행
  + 임의의 SQL 파일에 SQL을 작성하고 테스트 시 자동으로 불러와 실행할 수 있다
+ 데이터베이스 자동 롤백
  + 테스트 메서드가 종료되는 타이밍에 데이터베이스 트랜잭션의 롤백을 자동으로 수행
  + 테스트로 갱신된 데이터를 테스트 전 상태로 되돌려 다음 테스트에 영향을 주지 않는다

---

## DI 컨테이너를 생성하기 위한 애너테이션
+ @SpringBootTest
  + 일반 DI 컨테이너와 기본적으로 동일하게 동작하는 DI 컨테이너를 생성
  + E2E 테스트 및 통합 테스트에 사용
+ @WebMvcTest
  + 웹 관련 설정만 하도록 자동 설정이나 컴포넌트 스캔을 제한한 DI 컨테이너를 생성
  + Controller 단위 테스트를 수행할 때 사용
+ @JdbcTest, @DataJpaTest, @MybatisTest 등
  + 데이터베이스 접근 관련 설정만 하도록 자동 설정 및 컴포넌트 스캔을 제한한 DI 컨테이너를 생성
  + 데이터베이스 접근 방식(JDBC, JPA, MyBatis 등)마다 별도의 애너테이션 존재
  + Repository 단위 테스트를 수행할 때 사용

## 테스트 클래스
```java
@SpringBootTest    //DI 컨테이너를 생성하기 위한 애너테이션
class SampleTest {
    @Autowired
    SomeBean someBean;    //테스트 대상인 SomeBean 객체를 주입
  
    @Test
    void test() {
        someBean.A();    //인젝션한 객체의 메서드를 호출하여 테스트 수행
      ...
    }
}

//구체적 명시 및 접근 제한자를 제외한 단순화된 외형
```

### JavaConfig 클래스를 자동으로 찾게 하기
+ 자동으로 찾아주기를 원하는 JavaConfig 클래스에 @SpringBootConfiguration을 붙여야 한다
  + main 메서드를 가진 클래스에 붙이던 @SpringBootApplication에 포함되어 있다
  + @SpringBootConfiguration은 @Configuration을 포함 > JavaConfig 클래스로 인식

#### @SpringBootConfiguration이 붙은 JavaConfig 클래스를 찾는 순서
1. 테스트 클래스와 동일한 패키지 안에서 찾는다
2. 찾지 못하면 그 상위 패키지에서 찾는다
3. 그래도 찾지 못하면 더 상위 패키지를 찾는다

```java
@SpringBootApplication
public class TrainingApplication {
    public static void main(String[] args) {
        SpringApplication.run(TrainingApplication.class, args);
    }
}
```
+ main 메서드를 가진 클래스에 @SpringBootApplication을 붙이고
  + main 메서드 안에 자신으ㅢ 클래스를 지정하여 DI 컨테이너를 생성
+ `@SpringBootApplication` 안에는
  + 자동 구성을 활성화 하는 `@EnableAutoConfiguration`
  + 컴포넌트 스캔을 지시하는 `@ComponentScan`
  + `@SpringBootConfiguration`이 포함되어 있다
+ main 메서드를 가진 클래스를 스프링에 불러오기 위해서는 테스트 클래스를 main 클래스가 포함된 패키지보다 하위 패키지에 배치해야 한다

### 테스트를 실행할 때만 불러오는 application.properties
+ 자동 테스트를 실행할 때만 스프링 설정을 변경하고 싶은 경우에 사용
  + 예를 들어 테스트를 실행할 때만 상세 로그를 출력하도록 로그 레벨 조정
+ `src/main/resources`아래의 `application.properties`에 설정 작성 > 테스트 & main 메서드로 app 실행 시 설정 반영
+ main 메서드로 실행했을 때 설정 반영하지 않도록 하기
  + `application.properties`를 `src/test/resources`에 배치
    + 테스트 클래스가 실행될 때만 로드
  + 테스트 클래스를 실행 : `src/test/resoutces`아래의 `application.properties`
  + `src/main/java`아래 main 메서드 실행 : `src/main/resources`아래의 `application.properties`

### src/main/resources 아래의 application..properties파일에 테스트 클래스 적용 부분 존재 시
+ `src/test/resources`아래에 `application.properties`가 아닌 `application-default.properties`를 배치
```declarative
src/main/java
         ㄴ com.example.training
                  ㄴ TrainingApplication.java(main 메서드를 가진다)
                  ㄴ ... ...
src/main/resources
         ㄴ application.properties    //TrainingApplication 실행 & 테스트 실행 공통되는 설정 작성
src/test/java
         ㄴ com.example.training
                  ㄴ service
                        ㄴ ReservationServiceTest.java
                  ㄴ ... ...
src/test/resources
         ㄴ application-default.properties    //(프로파일을 지정하지 않고)테스트를 실행했을 때만 활성회될 설정을 작성
```
+ 하이픈 뒤의 default는 프로파일 이름을 나타낸다
+ DI 컨테이너 생성 시 활성화할 프로파일을 지정하지 않으면 자동으로 default 프로파일이 활성화
  + application-default.properties 파일이 로드
+ application-default.properties를 src/test/resources 아래에 패지
  + 테스트 클래스를 실행할 때만 application-default.properties 가 로드
    + 프로파일을 지정하지 않았다고 가정 > 프로파일 이름이 없으므로 항상 로드된다
