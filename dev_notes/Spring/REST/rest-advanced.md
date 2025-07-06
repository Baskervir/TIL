# spring-advanced

## 스프링 MVC의 REST 지원
+ HTTP 메서드별로 핸들러 메서드 전환
+ 송수신하는 데이터를 적절한 형식으로 자동 변환
+ 헤더 참조 및 지정
+ 상태 코드 지정

---

## HttpMessageConverter로 요청/응답 데이터 변환하기
+ REST에서는 response body와 request body의 데이터 형식을 클라이언트가 결정할 수 있다
    + 변환이 필요 (직접 작성시 프로그램이 복잡해진다)
+ 스프링 MVC는 클라이언트가 지정한 데이터 형식에 따라 자동으로 데이터를 변환

#### 요청 시 HttpMessageConverter
```text
클라이언트 -> :DispatcherServlet -> :Controller
                     V
           :HttpMessageConverter
```
+ 변환을 하는 것은 `HttpMessageConverter`객체다
    + 변환하는 데이터 형식(JSON, XML)에 따라 HttpMessaveConverter 인터페이스를 구현한 구상 클래스가 여러 개 제공된다
    + DispatcherServlet 객체가 HttpMessageConverter 객체를 사용하여 JSON or XML 형식의 데이터를 객체로 변환
    + 변환된 객체가 Controller 객체의 핸들러 메서드에 전달
+ response body도 같은 방식으로 자동한다
    + 스피링 MVC는 HttpMessaveConverter 객체를 사용하여 자바 객체를 JSON or XML 형식으로 변환
    + > ex)Accept 헤더에서 JSON 형식을 지정하면 대상 객체를 JSON으로 변환하여 response body에 작성

#### 응답 시 HttpMessageConverter
```text
클라이언트 <- :DispatcherServlet <- :Controller
                     V
            :HttpMessageConverter
```
+ Controller 객체의 핸들러 메서드는 대상 객체를 반환 값으로 반환하기만하면 된다
+ DispatcherServlet 객체는 핸들러 메서드가 반환한 반환 값의 객체를 받아 HttpMessageConverter 객체에 전달
    + 이후 JSON or XML 등의 형식으로 변환
    + 변환된 데이터는 response body에 작성되어 클라이언트에게 전송된다

## 상태 코드 지정
+ 상태 코드를 별도로 지정하지 않는 경우 핸들러 메서드는 200(OK)를 반환한다
+ 명시적으로 지정하려면 `@ResponseStatus`에서 지정 가능
```java
@PutMapping("/products/...")
@ResponseStatus(HttpStatus.NO_CONTENT)
public void update(...) {
  ...
}
```
+ `HttpStatus`: @ResponseStatus의 ( )안에 지정하는 상태 코그 값
    + enum 타입으로 정의되어 있다

---

## 요청 헤더 참조하기
+ `@RequestHeader`를 사용하여 핸들러 메서드 인수에서 요청 헤더 값을 얻을 수 있다
### 요청 헤더 참조
```java
@GetMapping("/a")
public A getA(
        @RequestHeader(HttpHeaders.USER_AGENT) String userAgent) {
    ...
}
//User-Agent: 클라이언트 프로그램 종류(브라우저의 제품 등)가 저장되어 있는 헤더
```
+ @RequestHeader의 ( )안에 지정하는 헤더명은 `HttpHeaders`라는 클래스 상수(문자열 타입)를 사용할 수 있다

## 응답 헤더 지정하기
+ 응답 헤더를 지정하는 경우 핸들러 메스드는 `ResponseEntity` 객체를 반환해야 한다
  + RespnseEntity는 HTTP의 응답을 나타내는 타입
+ >[:ResponseEntity [상태 코드] [응답 헤더] [응답 바디]]
+ 스프링 MVC는 핸들러 메섣의 반환 값으로 ResponseEntity 객체가 반환되면 ResponseEntity 객체의 내용에 따라 응답 데이터를 생성
```java
@GetMapping("/a")
public ResponseEntity<A> getA() {
    A a = ...
    return ResponseEntity.ok()                      //200(ok)에 해당하는 ok메서드 사용
            .eTag(a.getVersion())                   //응답 헤더의 ETag 헤더 지정
            .header(HeepHeaders.DATE, ...)          //header 메서드로 임의의 헤더를 지정: (헤더명, 값)
            .header("abc", "def")                   //임의의 헤더명 지정
            .body(a);                               //response body에 작성할 객체를 지정
}
```
+ 핸들러 메서드의 반환 값 타입을 ResponseEntity로 설정
+ < >안의 type 파라미터는 응답 바디에 작성할 객체의 타입 지정
+ A 객체의 내용을 response body에 작성한다고 가정하므로 `ResponseEntity<A>` 라는 반환 값 타입으로 되어 있다
+ ResponseEntity 객체를 새성하기 위해 상태 코드마다 제공되는 ResponseEntity 클래스의 static 메서드를 사용할 수 있다

---

## 참조 계열 REST API 만들기
+ 핸들러 메서드 만들기
#### GET 요청
```text
GET /api/trainings/t01
Host: localhost:8080
Accept: application/json
```

#### GET 응답
```text
HTTP/1.1 200 OK
Content-Type: application/json
{
    "id": "t01",
    "title": "비즈니스 예절 교육",
    "startDateTime": "2021-08-01T09:30:33",
    "endDateTime": "2021-08-03T17:00:00",
    "reserved": 1,
    "capacity": 10
}
```
+ JSON 형식으로 작성된 Response Body
+ GET 메서드로 데이터를 가져올 때의 상태 코드는 REST에서는 200(OK)를 사용한다

#### GET 메서드에 대응하는 핸들러 메서드
```java
@Controller
public class TrainingAdminRestController {
  ...
  @GetMapping("api/trainings/{id}")                             //GET 메서드를 받기 위한 @GetMapping
  @ResponseBody                                                 //@ResponseBody: 반환한 객체를 HttpMessageConverter를 사용해 JSON, XML 등의 형식으로 변환하라는 지시 사항을 나타내는 애너테이션
  public Training getTraining(@PathVariable String id) {        //핸들러 메서드의 인수를 정의하고 @PathVariable을 붙인다, { }안의 작성한 문자열과 동일하게 한다
      return trainingAdminService.findById(id);
  }
}
```
+ REST API를 만들 때 핸들러 메서드에 `@ResponseBody`를 붙이는 경우가 많다
  + 매번 붙이는 것은 번거롭기 때문에 스프링 MVC는 `@RestController`라는 애너테이션 제공
  + `@RestController`: `@Controller` + `@ResponseBody`
#### @RestController를 붙인 소스 코드
```java
@RestController
public class TrainingAdminRestController {
  ...
  @GetMapping("/api/training")
  public List<Training> getTrainings() {
      return trainingAdminService.findAll();
  }
  
  @GetMapping("/api/training/{id}")
  public Training getTraining(@PathVariable String id) {
      return trainingAdminService.findById(id);
  }
}
```
+ GET 요청을 받는 햔들러 메서드가 두 개 있지만 모두 @ResponseBody가 붙어있지 않다
+ 클래스에 @RestController 붙어있다 -> @ResponseBody 생략 가능
