Spring Boot Initializr 
    - 스프링 프로젝트의 시작 뼈대를 자동으로 만들어주는 도구
    - 거의 모든 프로젝트가 여기서 시작

@SpringBootApplication
    - @Configuration, @EnableAutoConfiguration, @ComponentScan 을 묶은 어노테이션
    - 애플리케이션의 시작점이자 구성의 중심

main() 메서드
    - 애플리케이션의 진입점 (JVM에서 실행되는 시작지점)
    - 실무에서 이 코드는 거의 손대지 않음

루트 패키지
    - @SpringBootApplication이 선언된 클래스의 패키지
    - 컴포넌트 스캔의 기준이 되므로 아주 중요

[기존 나의 방식]
Spring Initializr -> main이 있는 기본 클래스 아래 패키지에 그냥 기능들 추가 // 틀린 방식은 아님
    - Spring Boot가 제공하는 기본 구조를 그대로 따른것

com.example.demo             <- 이게 루트 패키지
ㄴ DemoApplication.java      <- 여기에 @SpringBootApplication
ㄴ controller
ㄴ service
ㄴ repository
ㄴ entity
[이 구조가 무너지면, 스프링이 컴포넌트를 못 찾는다]

*** 실무적으로 어떻게 구성하나? ***
초반에는 루트 패키지 아래로 역발별로 나누는게 기본 구조가 된다
com.exmple.myapp
ㄴ MyAppApplication.java
ㄴ controller                  < @Controller, @RestController
ㄴ service                     < @Service
ㄴ repository                  < @Repository
ㄴ domain                      < Entity, Enum 등
ㄴ config                      < 설정 파일들 (WebConfig, SecurityConfig)

*** 구조 흐름 요약 ***
MyAppApplication.java          앱의 진입점. 컴포넌트 스캔의 기준 패키지
controller                     사용자 요청을 받는 계층
service                        비즈니스 로직 처리
repository                     DB 연동
domain                         순수 객체 모델 (Entity, Vo, Enum 등)
config                         설정 (WebMvcConfigurer, Security 등)

*** 회고 ***
- 구조를 따르긴 했지만 왜 그런 구조인지 몰랐음
- 지금 구조의 원리를 알 필요성 느낌
- 스프링의 컴포넌트 스캔과 의존성 주입이 어떻게 적용하는가 이해 중