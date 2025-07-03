# 어노테이션 차이점 정리

## `@RestController` vs `@Controller`
| 항목 | `@Controller` | `@RestController` |
| --- | --- | --- |
| 역할 | 뷰 반환용 (HTML, JSP 등) | 데이터 반환용 (JSON, XML 등) |
| 반환값 처리 | 뷰 이름으로 인식 > 템플릿 렌더링<br>`return "home"` | 반환값이 HTTP Body에 직렬화된다<br>`return 객체` -> JSON |
| @ResponseBody 필요 | 반드시 붙여야 JSON 응답 가능 | 자동 포함되어 있다 (생략 가능) |
| 사용 목적 | 웹사이트처럼 화면 출력용 | REST API처럼 데이터 처리용 |
| 내부 구성 | `DispatcherServlet -> ViewResolver` 사용 | `HttpMessageConverter`로 변환 (Jackson 등) |

## 포인트
+ `@RestController = @Controller + @ResponseBody`
+ 웹 페이지 개발이면 `@Controller`, API 서버 개발이면 `@RestController` 사용

---

### @Controller 예시
```java
@Controller
public class PageController {

    @GetMapping("/hello")
    public String helloPage(Model model) {
        model.addAttribute("name", "브로");
        return "hello"; // templates/hello.mustache 렌더링
    }
}
```

### @RestController 예시
```java
@RestController
public class ApiController {

    @GetMapping("/hello")
    public Map<String, String> helloJson() {
        return Map.of("name", "브로"); // JSON 반환
    }
}
```

---

## `@ModelAttribute` vs `@RequestBody`
| 항목 | `@ModelAttribute` | `@RequestBody` |
| --- | --- | --- |
| 바인딩 대상 | 폼 데이터, 쿼리 스트링 등<br>`x-www-form-urlencoded` | 요청 본문 (JSON, XML 등) |
| 데이터 소스 | 요청 파라미터 (URL, form 필드) | HTTP body |
| 사용 시점 | `GET`, `POST` 모두 가능<br>(HTML Form 기반) | 주로 `POST`, `PUT`, `PATCH`<br>(API에서 JSON 수신 시) |
| 유효성 검사 | 가능<br>`@Valid`, `BindingResult` | 가능<br>`@Valid`, `BindingResult` |
| 자동 적용 여부 | 파라미터가 객체면 자동으로 적용 (생략 가능) | 명시적 `@RequestBody` 필요 |
| 내부 처리 | `WebDataBinder` | `HttpMessageConverter`<br> (Jackson 등) |

### @ModelAttribute 예시
```java
@PostMapping("/register")
public String register(@ModelAttribute UserForm form) {
    // form.name, form.email 등 자동 바인딩됨
    return "success";
}
```
+ 요청
```html
POST /register
Content-Type: application/x-www-form-urlencoded

name=브로&email=bro@example.com
```

### @RequestBody 예시
```java
@PostMapping("/register")
public ResponseEntity<?> register(@RequestBody UserDto dto) {
    // JSON body → UserDto로 매핑
    return ResponseEntity.ok().build();
}
```
+ 요청
```html
POST /register
Content-Type: application/json

{
"name": "브로",
"email": "bro@example.com"
}
```

---

## 정리 요약
+ 화면(view)을 반환하고 싶다?
  + `@Controller`
+ JSON, XML 응답만 주는 API 서버?
  + `@RestController`
+ URL/form 파라미터를 객체로 받고 싶다?
  + `@ModelAttribute`
+ JSON 요청 바디를 객체로 받고 싶다?
  + `@RequestBody`