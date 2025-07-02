# `@SpringBootTest`를 사용?
+ 스프링 전체 ApplicationContext 를 로딩해서 테스트를 실행
+ 실제 애플리케이션 구동과 거의 동일한 환경에서 테스트 가능
+ 모든 `@Component`, `@Service`, `@Repository`, `@Configuration` 등을 포함한 통합 테스트 환경
+ 내장 톰캣 없이 컨텍스트만 띄우고 테스트 실행

# `@Autowires`는 어떤 역할?
+ 스프링 컨테이너에서 Bean을 주입받기 위한 어노테이션
+ 테스트 클래스 내에 필요한 객체를 자동으로 주입
+ `@SpringBootTest`로 컨텍스트가 올라가 있기 때문에, 등록된 Bean들을 자동으로 주입 가능

# DI 컨테이너에서 불러올 JavaConfig 클래스는?
+ `@SpringBootApplication`이 붙어 있는 애플리케이션 메인 클래스 기준으로 컴포넌트 스캔이 시작
+ `@SpringBootApplication`이 붙은 클래스가 기본 JavaConfig 클래스로 로딩된다
+ multi-moudle 구조라면, 해당 모듈의 main 클래스에서 구성된 설정이 기준이 된다