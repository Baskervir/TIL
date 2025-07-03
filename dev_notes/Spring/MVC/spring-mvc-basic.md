# MVC란?
+ Model, View, Controller 3가지 역할로 나눈다
+ Model
  + 업무 로직(Service), 업무 데이터 보유(Entity), 데이터베이스 접근(Repository)
+ View
  + 화면을 표시하기 위한 HTML 생성(JSP 등의 템플릿 파일)
+ Controller
  + Request ~ Response 까지의 전체 흐름 제어

---

## 스프링 MVC란?
+ 주로 Controller 프로그램을 만들기 위한 기능을 제공
+ 작성한 Controller는 Bean으로 관리되므로 Service 등의 의존 객체를 인젝션하여 사용

## Thymeleaf란?
+ 오픈 소스 템플릿 엔진
+ HTML 문법에 따라 템플릿 파일을 작성할 수 있다

---

## 전체 처리 흐름
1. 브라우저가 요청을 보내면 스프링 MVC의 내부 프로그램이 수락한다
2. 스프링 MVC의 내부 프로그램은 요청 내용(URL / HTTP 메서드)을 확인 > 일치하는 Controller 객체의 메서드를 호출
3. Controller 객체는 View 객체에 데이터를 전달하기 위해 Model 객체에 데이터를 저장
4. Controller 객체는 반환 값으로 View 이름인 .html을 반환
5. .html을 받은 스프링 MVC의 내부 프로그램은 대응하는 템플릿 파일에 연결된 View 객체를 준히 > View 객체에 처리를 요청
6. View 객체는 Model에서 데이터를 가져와 HTML을 생성하고 브라우저에 응답을 보낸다

## 스프링 MVC의 내부 동잗
+ 스프링 MVC의 내부에서 핵심이 되는 것은 `DispatcherServlet`클래스이다
  + DispatcherServlet 클래스는 자바 표준 서블릿의 메커니즘으로 작동
### 흐름
1. 브라우저의 요청은 DispatcherServlet 객체가 모두 받는다
2. DispatcherServlet 객체는 요청에 대응하는 Controller 객체를 지정하고자 HandlerMapping으로 불리는 역할의 객체에 지정을 요청
3. HandlerMapping 객체가 지정한 Controller 객체의 정보를 DispatcherServlet 객체가 받아 HandlerAdapter 객체에 Controller 객체의 호출을 요청
4. HandlerAdapter 객체는 Controller 객체에 전달할 인수(Model 등)를 준비 > Controller 객체의 메서드를 호출
5. Controller 객체는 Service 객체의 업무 로직을 호출
6. View 객체에 전달할 데이터를 Model 객체에 저장
7. Controller 객체가 View 이름을 반환 > DispatcherServlet 객체가 받아 View 객체를 준비하는 ViewResolver 객체를 호출
8. ViewResolver 객체는 View 이름에 대응하는 템플릿 파일에 연결된 View 객체를 준비 > DispatcherServlet 객체에 반환
   + DispatcherServlet 객체는 ViewResolver 객체가 준비한 View 객체에 처리를 요청
9. View 객체는 Model 객체 안의 데이터를 이용 > HTML 생성
10. 생성된 HTML은 응답으로 브라우저에 전송

---

## Service 객체 인젝션
+ Controller 객체는 Service 객체의 메서드를 호출해야 한다
  + Service 객체가 필요하다
+ Controller 객체와 Service 객체는 모두 DI 컨테이너에서 관리된다
  + Controller 객체는 Service 객체를 인젝션할 수 있다
+ Service 객체에 Repository 객체를 인젝션하는 것과 동일하다

---

## 핸들러 메서드와 @XxxMapping
+ 핸들러 메서드(컨트롤러 메서드)는 요청과 연결된 Controller 클래스의 메서드
+ 핸들러 메서드를 만들기 위해서는 먼저 Controller 클래스 안에 적절한 메서드를 만든다 > `@XxxMapping`형태의 어노테이션을 붙인다

### @XxxMapping의 형태 및 종류
| 어노테이션 | 설명 |
| --- | --- |
| `@RequestMapping` | 가장 기본적인 매핑 어노테이션<br>모든 HTTP 메서드 지원(GET, POST 등) |
| `@GetMapping` | `@RequestMapping(method = RequestMethod.GET)` |
| `@PostMapping` | `@RequestMapping(method = RequestMethod.POST)` |
| `@PutMapping` | `@RequestMapping(method = RequestMethod.PUT` |
| `@DeleteMapping` | `@RequestMapping(method = RequestMethod.DELETE)` |
| `@PatchMapping` | `@RequestMapping(method = RequestMethod.PATCH)` |

#### 사용 예시
```java
@RestController
@RequestMapping("/users") // 공통 경로
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return userService.findById(id);
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.save(user);
    }

    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        return userService.update(id, user);
    }

    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userService.delete(id);
    }
}
```

## `@XxxMpaiing`기능의 속성
| 속성 | 설명 |
| --- | --- |
| `path`, `value` | 매핑할 URL 경로<br>`@GetMapping("/hello")`와 같다 |
| `method` | HTTP 메서드 지정<br> `@RequestMapping`에서 사용 |
| `params` | 특정 요청 파라미터가 있을 때만 매핑된다 |
| `headers` | 특정 HTTP 헤더가 있을 때만 매핑된다 |
| `consumes` | 허용하는 요청의 Content-Type 지정 |
| `produces` | 반환하는 응답의 Contene-Type 지정 |


---

### `@XxxMapping`과 함께 자주 사용하는 어노테이션 정리
| 어노테이션 | 용도 및 설명 |
| --- | --- |
| `@PathVariable` | URL 경로에 포함된 값을 변수로 바인딩할 때 사용 |
| `@RequestParam` | 쿼리 파라미터 또는 HTML form 파라미터 값을 가져올 때 사용 |
| `@RequestBody` | HTTP 요청 본문(JSON, XML 등)을 객체로 매핑할 때 사용 |
| `@RequestBody` | 메서드의 반환값을 HTTP 응답의 Body로 직렬화<br>`@RestController` 쓰면 생략 |
| `@RequestHeader` | HTTP 요청 헤더 값을 가져올 때 사용 |
| `@CookieValue` | 쿠키 값을 가져올 때 사용 |
| `@ModelAttribute` | 폼 데이터 or 쿼리 파라미터를 객체에 바인딩<br>주로 HTML form 처리용 |
| `@RequestPart` | multipart 요청에서 특정 part를 가져올 때 사용 |
| `@VAlid`, `Validated` | 입력값 유효성 검증을 위해 DTO에 붙인다 |