# 선언적 트랜잭션

## 트랜잭션이란?
+ 데이터베이스에서 **하나의 작업 단위**를 의미
+ 반드시 모든 작업이 *성공*해야만 **커밋**되고, 하나라도 *실패*시 **롤백**

### 트랜잭션의 ACID 특성
| 속성 | 설명 |
| --- | --- |
| Atomicity | 원자성 - 모두 성공 or 모두 실패 |
| Consistency | 일관성 - 틀랜잭션 전후 데이터 무결성 보장 |
| Isolation | 독립성 - 동시에 실행되는 트랜잭션 간 간섭 방지 |
| Durability | 지속성 - 커밋된 데이터는 영구적으로 보존 |

---

## 선언적 트랜잭션이란?
+ 코드에서 **직접 트랜잭션을 제어하지 않고**, `@Transactional`을 통해 선언만 하면 스프링이 관리
```java
@Transactional
public void updateUser() {
    userRepository.save(...); // 트랜잭션 시작 → 성공 시 커밋, 예외 시 롤백
}
```

---

## 트랜잭션 추상화의 이점
1. 데이터베이스에 대한 독립성 확보
   + 스프링의 `PlatformTransactionManager`인터페이스 덕분에 어떤 DB든 맞는 구현체만 연결하면 된다
2. 비즈니스 로직과 트랜잭션 제어 코드 분리
   + 비즈니스 로직은 그대로 유지
   + 트랜잭션 처리는 스프링이 관리

---

## 프록시 기반 AOP - 작동 원리
+ 프록시 객체를 통한 트른잭션 처리
  + `@Transactional`이 붙은 메서드를 스프링이 프록시 객체로 감싸서 실행 - DI 컨테이너 생성 시
  + 프록시가 메서드 호출을 가로채고, 아래 작업 수행
```text
[프록시] 
 → 트랜잭션 시작
 → 실제 메서드 실행
 → 예외 발생 여부 확인
   - 예외 없으면 commit
   - 예외 발생하면 rollback
```
  + 내부적으로 AOP 사용

---

## 주요 예외 처리 기준
+ `RuntimeException`or`Error` - 롤백된다(기본 동작)
+ `Checked Exception` - 기본적으로 커밋된다 / 롤백하려면 명시 필요 `@Transactional(rollbackFor = ...)`

---

## 선언적 트랜잭션을 사용하기 위한 조건
+ Spring Boot 사용 시
  + `@Transactional`만 붙이면 가능
+ Spring Framework만 사용할 경우
  + 설정 필요
```java
import javax.sql.DataSource;

@Configuration
@EnableTransactionManagement
public class AppConfig {
    @Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource) {
        return new JdbcTransactionManager(dataSource);
    }
}
```

---

## 로깅 및 디버깅
+ `logging.level.org.springframework.transaction=DEBUG` 설정 시 
  + 트랜잭션 시작/종료 로그를 콘솔에서 확인 가능
+ 로그를 통해 커밋/롤백 여부를 확인 > 디버깅에 활용