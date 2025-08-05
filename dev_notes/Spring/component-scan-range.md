# 컴포넌트 스캔 범위
+ 스프링이 특정 패키지 이하에 있는 클래스들 중에서 `@Component`계열 애너테이션이 붙은 클래스들을 자동으로 찾아서 스프링 빈(Bean)으로 등로하는 기능

## 대상 애너테이션
+ `@Component`
+ `@Controller`
+ `@Service`
+ `@Repository`

---

## 동작 흐름
1. `@SpringBootApplication` 또는 `@ComponentScan`의 base package 하위부터 검색
   + `@SpringBootApplicaton(scanBasePackages = "com.project")`
   + 별도 설정이 없으면 해당 클래스가 속한 패키지 기준으로 하위만 스캔
2. 스캔 중 `@Component` 계열이 붙은 클래스 발견 시
> ApplicationContext에 Bean으로 등록
3. 등록된 Bean들은 의존성 주입으로 사용 가능
   + `@Autowired`, `@Inject` 등

## 정리
+ 기본 스캔 범위는 @SpringBootApplication 위치 패키지 기준 하위로 한정
+ 다른 위치의 클래스들을 등록하고 싶다면 `@ComponentScan(basePackage = {...})`명시 필요
+ 스캔 범위 바깥에 있는 클래스는 Bean으로 등록되지 않기 때문에 의존성 주입 시 오류 발생

---

## 스캔은 하지만 Bean 등록이 안 된다?
+ 컴포넌트 스캔 범위 내에 있는 클래스만 Bean으로 등록된다
+ 스캔 범위 바깥 패키지의 클래스는 Bean으로 등록되지 않기 때문에 의존성 주입이 안된다
  + 스프링이 관리하지 않는 객체는 DI 불가능
+ Repository는 인터페이스에 @Repository 붙이고, JPA 등에서 프록시 객체를 만들어 Bean으로 등록해준다
+ 컴포넌트 스캔 범위 내에 있어야 동작한다
+ 스캔 범위를 벗어나면 repository도 주입이 안된다