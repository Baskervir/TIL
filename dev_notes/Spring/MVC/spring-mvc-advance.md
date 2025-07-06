# spring-mvc-advanced

## 요청 파라미터 가져오기

+ 핸들러 메서드에서는 요청 파라미터의 값을 인수로 받을 수 있다
+ 요청 파라미터 예시
    + `pageNo=1&maxCount=10`
+ 요청 파라미터 값을 받는 핸들러 메서드 예시

```java
@XxxMapping("/display-list")
public String displayList(@RequestParam("pageNo") int pNo, @RequestParam("maxCount") int max) {
    ...
}
```
+ 인수가 두 개 선언되어 있으며, 각각 @RequestParam이 붙어있다
  + @RequestParam은 요청 파라미터의 값을 가져오기 위한 애너테이션
+ ( ) 안에 파라미터명을 지정하면 인수의 변수에 파라미터의 값이 전달된다
+ 위 의 경우 pNo = 1, max = 10 이 전달된다
+ 인수명과 파라미터명을 일치시키면 @RequestParam의 ( ) 생략 가능

### 요청 파라미터의 기본값
```java
@XxxMapping("/display-list")
public String displayList(
        @RequestParam(defaultValue = "1") int pageNo,
        @RequestParam(defaultValue = "10") int maxCount) {
  ...
}
```
+ @RequestParam의 defaultValue 속성에서 임의의 기본값 지정 가능
+ 임의의 클래스를 생성하고 프로퍼티명을 요청 파라미터명에 맞춘다
  + getXxx(Getter 메서드)로 취득하거나 setXxx(Setter 메서드)로 갱신할 수 있는 데이터를 프로퍼티라고 부른다
  + xxx(첫 글자는 소문자)는 프로퍼티 이름이 된다

### 요청 파라미터 값을 저장하는 클래스
```java
public class PageInput {
    private Integer pageNo;
    private Integer maxCount;
  ... //Getter, Setter 메서드
}
```
+ 위 클래스의 타입을 핸들러 메서드의 인수로 정의한다

### 값을 저장하는 클래스를 인수로 정의한 핸들러 메서드
```java
@XxxMapping("/display-list")
public String displayList(PageInput pageInput) {
  ...
}
```
+ 요청 파라미터가 전송되면 핸들러 메서드가 호출되기 전에 PageInput 클래스의 객체가 자동으로 생성
  + 파라미터의 값이 프로퍼티에 설정된다
+ 파라미터 값이 프로퍼티에 설정된 PageInput 객체가 displayList 메서드의 인수로 전달된다

---

## View에 데이터 전달하기
+ View 객체에 데이터를 전달하기 위해서는 Model 객체 안에 데이터를 저장해야 한다
### Model에 데이터를 저장
```java
@GetMapping("/display-details")
public String displayDetails(@RequestParam String trainingId, Model model) {
    Training training = trainingService.findById(trainingId);
    model.addAttribute("training", training);
    return "training/trainingDetails";
}
```
+ 핸들러 메서드에 Model의 인자를 지정 -> 스프링 MVC가 Model 객체를 인자로 전달
+ 인젝션된 Service 객체(trainingService)에 대해 ID 지정 검색 처리를 호출 -> 얻은 Entity 객체를 Model 객체 내에 저장
  + 저장할 때는 Model 객체가 가진 addAttribute 메서드 사용

### 속성명 지정을 생략한다?
```java
@GetMapping("/display-details")
public String displayDetails(@RequestParam String trainingId, Model model) {
    Training training = trainingService.findById(trainingId);
    model.addAttribute(training);       //속성명 지정을 생략
    return "training/trainingDetails";
}
```

---

## Thymeleaf로 데이터 참조하기
+ Thymeleaf 템플릿 파일에서 Model 객체의 데이터를 참조할 수 있다
+ 예시
```text
Model 객체 안의 Training 객체
------------------------------------
|       :Model                      |
|             -----------------------
|             |     :Training       |
| 속성명       |       title         |
| "training"  |       startDateTime |
|             |       reserved      |
-------------------------------------
```
+ Training 객체가 title, startDateTime, reserved 프로퍼티를 가지고 있다

+ Model 객체의 데이터를 참조?
```html
<table>
  <tr>
    <th>강의명</th>
    <td><span th:text="${training.title}"></span></td>
  </tr>
  <tr>
    <th>시작일자</th>
    <td><span th:text="${#temporals.format(training.startDateTime, 'yyyy/MM/dd HH:mm')}"></span></td>
  </tr>
  <tr>
    <th>신청인원</th>
    <td><span th:text="${training.reserved}"></span></td>
  </tr>
</table>
```
+ Model 객체의 데이터를 참조할 때는 `${ }`안에서 Model 객체의 데이터 속성명을 지정
  + 예시에서는 `training`이므로 `${training}`이라고 작성
+ `#temporals.format`
  + `#`: Thymeleaf가 가진 범용 객체에 접근하기 위한 기호
  + `temporals`: 날짜 및 시간 데이터를 편리하게 다룰 수 있는 객체

---

## List 객체 참조하기
+ List 객체를 View 객체로 전달하여 표시
+ List 객체를 Model 객체에 저장한다?
```java
@GetMapping("/display-list")
public String displayList(Model model) {
    List<Training> trainings = trainingService.findAll();
    model.addAttribute("trainingList", trainings);
    return "training/trainingList";
}
```
+ addAttribute 메서드를 사용해 데이터를 저장

---

## Bean Validation을 이용한 입력 검사
+ 자바 표준 기술
+ 스프링 MVC는 Bean Validation과 원활하게 연동 가능

| 애너테이션         | 용도 |
|---------------| --- |
| `@NotNull`    | 값이 null이 아닌지 확인 |
| `@NotBlank`   | 문자열이 null, 빈 문자(""), 공백 문자(" ")가 아닌지 확인 |
| `@NotEmpty`   | null, "" 체크 & " " 허용<br>List나 Map이 null이거나 크기가 0이 아닌지 확인 |
| `@Max`        | 지정 값 이하의 수치인지 확인 |
| `@Min`        | 지정 값 이상의 수치인지 확인 |
| `@Size`       | 문자열 길이나 List, Map의 요소 수가 최소 및 최대 범위인지 확인<br>`@Size(min=5, max=10)`은 5이상이고 10이하인지 확인 |
| `@Email`      | 이메일 주소 형식 확인 |
| `@Pattern`    | 지정한 정규 표현에 맞는지 확인 |
| `@AssertTrue` | Boolean 타입인 필드 혹은 메서드의 반환 값이 True인지 확인 |

---

## 요청 파라미터를 사용한 핸들러 메서드 호출
+ `<form>` 태그에 요청을 보낼 때 `action` 속성에 보낼 경로 하나 지정
  + 이 때, 처리할 작업이 여러 개인 경우 존재

### <form> 태그와 조작
```html
<form th:action="@{reservation/reserve}" method="post">
  ...
  <input type="submit" value="신청 내용 확정"/>
  <input type="submit" value="신청 내용 변경"/>
</form>
```
+ 동일한 경로(reservation/reserve)로 동일한 HTTP 메서드(POST) 요청이 전송된다
  + 핸들러 메서드를 나눌 수 없다
+ 요청 파라미터를 할당하여 문제를 해결한다
#### 요청 파라미터 할당
```html
<form th:action="@{reservation/reserve}" method="post">
  ...
  <input name="reserve" type="submit" value="신청 내용 확정"/>
  <input name="correct" type="submit" value="신청 내용 변경"/>
</form>
```
+ '신청 내용 확정'에는 reserve, '신청 내용 변경'에는 correct 파라미터명 할당
  + 핸들러 메서드를 나눌 수 있게 된다
#### 요청 파라미터로 나눈 핸들러 메서드
```java
@PostMapping(value = "/reserve", params = "correct")     //params 속성 지정
public String correctInput(@Validated ReservationInput reservationInput, Model model) {
    List<StudentType> studentTypeList = reservationService.findAllStudentType();
    model.addAttribute("studentTypeList", studentTypeList);
    return "reservation/reservationForm";
}

@PostMapping(value = "/reserve", params = "reserve")     //params 속성 지정
public String reserve(@Validated ReservationInput reservationInput, Model model) {
    Reservation reservation = reservationService.reserve(reservationInput);
    model.addAttribute("reservation", reservation);
    return "reservation/reservationCompletion";
}
```
+ `param 속성 지정` -> 요청과 핸들러 메서들르 연결하는 조건
  + `@PostMapping(value = "/reserve", params = "correct")`의 경우
    + 요청: POST | 경로: /reserve | 요청 파라미터명: correct(임의의 값)

---

## 예외 핸들링
+ 핸들러 메서드에서 따로 예외를 잡아주지 않으면 -> 호출자(스프링 MVC 내부 프로그램)로 예외가 전파된다
  + 사용자에게 표기하기에 부적절 -> 예외 처리 필요

### 예외를 처리하는 방법
1. `@ExceptionHandler`: 컨트롤러 내 or 전역
```java
@RestController
public class Mycontroller {
    
    @GetMapping("/place")
    public Place find() {
        throw new PlaceNotFoundException("해당 장소 없음");
    }
    
    @ExceptionHandler(PlaceNotFoundException.class)
    public ResponseEntity<String> handlePlaceNotFound(PlaceNotFoundException e) {
        return ReponseEntity.status(HttpStatus.NOT_FOUND).body(e.getMessage());
    }
}
```
+ 장점: 해당 컨트롤러 범위 내에서 세밀하게 예외 처리 가능
+ 단점: 모든 컨트롤러마다 중복될 수 있다

2. `@ControllerAdvice` + `@ExceptionHandler`: 전역 예외 처리
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(PlaceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handlePlaceNotFound(PlaceNotFoundException e) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(new ErrorResponse("PLACE_NOT_FOUND", e.getMessage()));
    }
    
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleDefault(Exception e) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(new ErrorResponse("INTERNAL_ERROR", "알 수 없는 오류 발생"));
    }
}
```
+ 장점: 예외를 한 곳에서 일괄적으로 처리 가능
+ 단점: 일부 특정 컨트롤러에서 다른 응답 형식을 요구할 경우 충돌 가능

3. `ResponseStatusException`사용
```java
@GetMapping("/place")
public Place find() {
    throw new ResponseStatusException(HttpStatus.NOT_FOUND, "장소를 찾을 수 없습니다.");
}
```
+ 장점: 간단, 별도 핸들러 없이 HTTP 상태 코드 응답 가능
+ 단점: 구조화된 에러 응답을 제공하기 어렵다
