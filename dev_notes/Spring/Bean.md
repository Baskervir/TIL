# Bean 정의
## 프로파일이란?
+ 설정을 그룹화하는 DI 컨테이너의 기능이다
+ 환경에 따른 설정을 그룹화할 수 있으며, 그룹 이름이 프로파일 이름이다

## 환경별 프로파일
+ 실제 개발 프로젝트에서는 환경별 프로파일로 그룹화하는 것이 일반적이다
+ 별도의 프로파일로 속하지 않은 Bean정의는 항시 활성화 된 상태이다
```text
TrainingServiceImpl 클래스의 Bean 정의, JdbcTrainingRepository클래스의 Bean정의,
ExternalTrainingRepository 클래스의 Bean정의가 있다고 가정한다

JdbcTrainingRepository 클래스와 ExternalTrainingRepository 클래스는 공통 인터페이스 TrainingRepository를 구현한다

ExternalTrainingRepository 클래스의 Bean 정의를 production 프로파일에, JdbcTrainingRepository 클래스의 Bean 정의를 staging 프로파일에 속하게 하면
TrainingServiceImpl 클래스의 Bean 정의는 어떤 프로파일에도 속하지 않게 된다

프로덕션 환경에서 실행할 경우: production 프로파일 활성화 > DI 컨테이너 생성
 - ExternalTrainingRepository 클래스와 TrainingServiceImpl 클래스의 Bean 정의 사용
 - ExternalTrainingRepository 객체와 TraningServiceImpl 객체가 생생 > DI 컨테이너에서 관리
 
TrainingServiceImpl 객체가 TrainingRepository 객체를 인젝션 하게 되어 있다면
 - TrainingServiceImpl 객체에 ExternalTrainingRepository 객체가 인젝션된다
```

## 프로파일 사용법
+ 설정을 그룹화할 때는 `@Profile`을 사용
```java
@Repository
@Profile("production")
public class ExternalTrainingRepository implements TrainingRepository {
    ...
}
```
```java
@Repository
@Profile("staging")
public class JdbcTrainingRepository implements TrainingRepository {
    ...
}
```
```java
@Service
public class TrainingServiceImpl implements TrainingService {
    ...
}
```
+ ExternalTrainingRepository 클래스의 Bean 정의는 production 프로파일에
+ JdbcTrainingRepository 클래스의 Bean 정의는 staging 프로파일에
+ TrainingServiceImpl 클래스의 Bean 정의는 어떤 프로파일에도 속하지 않아 > `@Profile`이 붙지 않는다

---

## 활성화할 프로파일 지정하기
+ 시스템 프로퍼티
  + java 커멘드를 실행할 때 `-D`옵션으로 `spring.profiles.active` 프로퍼티 지정
```text
java -Dspring.profiles.active = 프로파일 이름     main 메서드를 가진 클래스 이름
```
+ 환경 변수 사용
```text
set SPRING_PROFILES_ACTIVE = 프로파일 이름
java main 메서드를 가진 클래스 이름
```

---

## JavaConfig란?
+ DI 컨테이너에 불러올 설정을 자바의 클래스에 작성하기 위한 스프링의 기능
+ 임의의 클래스에 `@Configuration`을 붙이면 JavaConfig 클래스로 인식된다
```java
@Configuration
public class 클래스명 {
    ...
}
```

## 여러 개의 JavaConfig 클래스 불러오기
+ AnnotationConfigApplicationContext 클래스의 생성자 인수로 지정
+ @Import로 가져오기
+ 컴포넌트 스캔

### AnnotationConfigApplicationContext 클래스이 생성자 인수로 지정하기
+ DI 컨테이너를 생성할 때 AnnotationConfigApplicationContext 클래스의 생성자 인수로 여러 개의 JavaConfig 클래스를 지정
+ 생성자 인수는 가변 인수 > 원하는 수의 JavaConfig 클래스를 지정할 수 있다
```java
@Configuration
public class AConfig {
    ...
}
```

```java
@Configuration
public class BConfig {
    ...
}
```

```java
ApplicationContext context = new AnnotationConfigApplicationContext(AConfig.class, BConfig.class);
```
+ AConfig와 BConfig 두 개의 JavaConfig 클래스를 DI 컨테이너에서 불러온다

### @Import로 가져오기
+ 스프링에서 제공하는 `@Import`를 사용
+ 한 JavaConfig 클래스에서 다른 JavaConfig 클래스를 가져올 수 있다
```java
@Configuration
@Import(BConfig.class)
public class AConfig {
    ...
}
```

```java
@Configuration
public class BConfig {
    ...
}
```

```java
ApplicationContext context = new AnnotationConfigApplicationContext(AConfig.class);
```
+ ACongif 클래스 위에 `@Import`가 붙어있다
  + ()로 BConfig 클래스를 지정하고 있다
+ AnnotationConfigApplicationContext 클래스의 생성자 인수로는 AConfig 클래스만 지정
  + AConfig 클래스가 BConfig 클래스를 임포트 > AConfig 클래스와 BConfig 클래스를 불러온다

+ 여러 설정 클래스를 합쳐서 한 번에 등록할 때 사용
+ SpringBoot에서 내부적으로 많이 사용되는 방식

### 컴포넌트 스캔하기
+ `@Configuration`은 스테레오타입 애너테이션
  + @Service나 @Repository와 마찬가지로 컴포넌트 스캔을 하면 JavaConfig 클래스의 객체 생성
    + Bean으로 관리된다
```java
package A;
...
@Configuration
@ComponentScan/* (basePackages = "com.example")*/
public class AConfig {
    ...
}
```

```java
package A;
...
@Configuration
public class BConfig {
    ...
}
```

```java
ApplicationContext context = new AnnotationConfigApplicationContext(AConfig.class);
```
+ AConfig 클래스와 BConfig 클래스는 모두 A 패키디에 속한다
+ AConfig 클래스 쪽에 `@ComponentScan`이 붙어있다
  + AnnotationConfigApplicationContext 클래스의 생성자 인수로 AConfig 클래스 지정
    + A 패키지를 베이스 패키지로 기준 > 컴포넌트 스캔 진행

+ `@Component`, `@Service`, `@Repository`, `@Controller`등 사용
+ Spring이 해당 패키지 이하의 클래스들을 자동으로 찾아서 Bean으로 등록
+ 자동 등록
+ 대규모 프로젝트에서는 이 방식이 주류

---

## @Bean 메서드란?
+ Bean을 정의하는 방법 중 하나
+ JavaConfig 클래스 안에서 @Bean을 붙인 메서드를 작성
+ @Bean이 붙은 메서드를 일컫는다
```java
@Configuration
public class AConfig {
    @Bean
    public AService aService() {
        return new AService();
    }
}
```
+ AService 메서드는 AService 객체를 반환한다
+ @Bean 메서드는 DI 컨테이어가 자동을 호출해주며 반환된 객체는 Bean으로 관리된다

+ `AnnotationConfigApplicationContext(AConfig.class)`와 같이 등록된다
+ 수동 등록
+ 가장 명시적
+ 테스트에서 많이 사용된다

```text
**Bean으로 관리된다?**

Spring Container가 해당 객체(클래스의 인스턴스)를
1. 직접 생성하고,
2. 필요한 의존성을 주입하며,
3. 필요한 시점에 초기화 또는 소멸시키는 과정까지 책임진다
```

---

## 정리
| 방식                       | 특징 | 주 사용 |
|--------------------------| --- | --- |
| `@Bean`                  | 수동 등록 | 테스트, 명시적 설정 |
| `@ComponentScan` + 애너테이션 | 자동 등록 | 일반 서비스 로직 |
| `@Import`                | 설정 병합 | 복잡한 설정 구조 |

---

## 인젝션
+ @Bean 메서드로 Bean을 정의할 때 인젝션하는 부분도 @Bean 메서드 안에 작성 가능
  + Bean으로 관리하고 싶은 구상 클래스가 의존 객체를 인젝션하기 위한 생성자를 정의한 경우
    + 생성자 호출할 때 의존 객체를 전달할 수 있다

```java
import java.beans.BeanProperty;

@Bean
public TrainingService trainingService(
        TrainingReposiroty trainingReposiroty) {
    TrainingServiceImpl trainingService = new TrainingServiceImpl(trainingReposiroty);
    return trainingServiceImpl;
}

@Bean
public TrainingRepository trainingRepository() {
    return new JdbcTrainingRepository();
}
```
+ TrainingServiceImpl 객체와 JdbcTrainingRepository 객체를 Bean으로 정의하는 @Bean 메서드가 각각 정의되어 있다
+ TrainingServiceImpl 객체에는 의존 객체로 TrainingRepository 객체를 인젝션
+ trainingRepository 메서드에서는 JdbcTrainingRepository 객체를 Bean으로 정의
  + JdbcTrainingRepository 객체가 DI 컨테이너에서 관리된다
+ trainingService 메서드에서는 TrainingServiceImpl 객체를 Bean으로 정의
  + trainingService 메서드의 인수로는 TrainingRepository 타입 인수 지정
  + @Bean 메서드의 인수로 TrainingRepository 타입의 인수 지정해두면
    + DI 컨테이너가 이 @Bean 메서드를 호출할 때 컨테이너 내에서 TrainingRepository 객체를 찾아 인수로 넘겨준다
    + 이 경우 JdbcTrainingRepository 객체가 전달된다
+ trainingService 메서드에서는 TrainingServiceImpl 클래스의 생성자를 호출해 객체를 생성
  + 생성자의 인자로 TrainingRepository 객체를 전달한다
  + TrainingServiceImpl 객체를 반환값으로 돌려준다
+ TrainingServiceImpl 객체에 JdbcTrainingRepository 객체가 인젝션 된다

---

## 라이브러리의 클래스를 Bean으로 정의하기
+ @Bean 메서드이 용도는 라이브러리가 제공하는 클래스의 Bean 정의다

---

## Bean으로 등록한다?
스프링 컨테이너가 해당 객체의 생성, 관리, 주입을 책임진다는 뜻

**빈(Bean)이란?** = 스프링 컨테이너에 의해 관리되는 객체
스프링이 생성하고, 생명주기를 관리하고, 필요할 때 꺼내쓸 수 있게 해주는 객체

## 왜 빈으로 등록해야 하나?
1. 의존성 주입을 위해
+ 어떤 객체 A가 B를 필요로 하면, 직접 `new B()`해서 만드는게 아니라, 스프링이 만들어 둔 B를 주입받는 방식으로 설계
+ 이게 가능하기 위해서는 스프링이 B객체를 먼저 만들어서 보관하고 있어야 한다
  + 빈으로 등록되어 있어야 한다
2. 재사용성과 효율성 확보
+ 객체를 매번 새로 만들지 않고, 필요한 시점에 스프링이 꺼내준다
+ 대부분 싱글통 범위로 관리되므로 성능에도 이점
3. 관심사의 분리
+ 객체를 생성하는 책임과 사용하는 책임을 분리
+ 개발자는 비즈니스 로직에 집중하고, 스프링이 객체 생성과 주입은 알아서 처리
4. 테스트와 확장에 유리
+ 빈으로 등록해두면 테스트 시 다른 구현체로 쉽게 교체 가능
+ DI 컨테이너에 의해 유연하게 모듈 대체 가능

## 정리
+ 빈 등록의 의미 : 스프링이 객체를 관리하도록 등록하는 것
+ 왜 필요한가? : DI를 위해, 재사용성, 유지보수, 테스트 유리
+ 언제 하는가? : 객체가 다른 객체에 주입되거나, 설정이 필요할 때