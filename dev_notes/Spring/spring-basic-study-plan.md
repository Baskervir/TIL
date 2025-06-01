# 스프링 큰 그림 학습 플랜 - 전반적인 흐름 중시
## 스프링은 무엇을 해결하기 위해 만들어졌는가?
| 개념 | 설명 |
| --- | --- |
| 프레임워크 | 어떤 "형식"을 강제하여 일관성과 생산성을 보장 |
| 스프링 컨테이너 | 객체(Bean)를 만들고, 필요한 객체에게 주입 해준다 |
| 빈(Bean) | 스프링이 생성/관리하는 객체<br>Ex. Controller, Servide 등 |
| IoC(제어의 역전) | 객체 새성과 관리를 개발자가 아닌 프레임워크가 담당 |
| DI(의존성 주입) | 필요한 객체를 알아서 주입해주는 방식<br> Setter, 생성자 등 |

### 구조
```
[사용자 요청]
      ↓
DispatcherServlet (프론트 컨트롤러)
      ↓
[HandlerMapping → Controller]
      ↓
Service
      ↓
Repository
      ↓
DB 접근

모든 객체는 스프링이 관리하는 Bean
→ ApplicationContext에서 생성 및 주입됨
```

### Spring 전체 요청 흐름 요약 - 계층 구조와 설명
1. 사용자 요청
   + 브라우저나 클라이언트에서 URL로 HTTP 요청 발생
2. DispatcherServlet
   + 스프림의 핵심 진입점
   + 모든 요청은 여기서 시작하고 끝난다
   + 역할 : 요청 분배 (중앙집중식 관제탑)
3. HandlerMapping
   + 어떤 Controller가 이 요청을 처리할지 결정
   + Ex. `/user`요청 > `UserController`의 `getUsers()`메서드 매핑
4. Service
+ 요청을 받아서 처리할 책임
+ 필요한 데이터나 로직은 Service에 위임
5. Repository
6. DB