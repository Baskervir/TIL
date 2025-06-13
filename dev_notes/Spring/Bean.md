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
| 방식 | 특징 | 주 사용 |
| --- | --- | --- |
| `@Bean` | 수동 등록 | 테스트, 명시적 설정 |
| `@ComponentScan` + 어노테이션 | 자동 등록 | 일반 서비스 로직 |
| `@Import` | 설정 병합 | 복잡한 설정 구조 |