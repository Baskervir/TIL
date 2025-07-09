# 갱신 계열 REST API

## 갱신 계열 HTTP 메서드
+ REST API는 HTTP 메서드로 POST, PUT, DELETE를 사용

| HTTP 메서드 | 용도 |
| --- | --- |
| POST | 리소스를 새로 등록 |
| PUT | 리소스를 갱싱 |
| DELETE | 리소스를 삭제 |

## PUT의 요청과 응답
+ PUT으로 리소스를 변경할 때의 요청 데이터를 작성
```json
PUT api/trainings/t01 HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{
  "title": "비즈니스 예절 교육(개정)",
  "startDateTime": "2021-08-01T09:30:00",
  "endDateTime": "2021-08-03T17:00:00",
  "reserved": 1,
  "capacity": 10
}
```
+ 갱신하고자 하는 강의 데이터의 내용을 JSON 형식으로 작성
+ 웹 서비스에 JSON 형식이라는 것을 알리기 위해
  + `Content-Type`의 헤더 값에 `application/json`명시

+ PUT 응답
```json
HTTP/1.1 204 No Content
Content-Length: 0
```
+ 상태 코드는 204(No Content)
  + 응답 바디가 비어있음을 나타낸다
    + Content-Length 헤더의 값은 0

---

## 요청 바디 변환
+ 요청 바디에 기재된 데이터를 핸들러 메서드에서 사용
  + JSON 형식 그대로는 내용을 분석하기 어렵다
  + 스프링 MVC는 JSON, XML 등 데이터를 자동으로 자바 객체로 변환

## PUT에 대응하는 핸들러 메서드
```java
@PutMapping("/api/trainings/{id}")
@ResponseStatus(HttpStatus.No_CONTENT)
public void updateTraining(@PathVariable String id,
                           @Validated @RequestBody TrainingAdminInput trainingAdminInput) {
    trainingAdminInput.setId(id);
    trainingAdminService.update(trainingAdminInput);
}
```
+ PUT 메서드에 대응하기 위해 `@PutMapping`
+ 상태 코드로 204를 반환하고 싶다
  + `@ResponseStatus`에 지정
+ 반환 값 타입을 void로 지정
  + 응답 바디를 비워둘 수 있게 되었다
+ 갱신 대상 리소스 ID가 경로에 포함되어 있다
  + `@PathVariable`로 ID부분을 첫 번째 인수로 정의하고 요청 바디의 데이터를 보관할 TrainingAdminInput 클래스를 두 번째 인수로 정의
+ TrainingAdminInput에 붙은 `@RequestBody`는 요청 바디의 데이터를 객체로 변환하기 위한 애너테이션
  + 내부에서 HttpMessageConverter 객체가 작동 -> 요청 바디의 데이터를 TrainingAdminInput 객체로 변환 -> 핸들러 메서드의 이눗로 전달 가능
+ `@Validation`를 붙여 입력 검사 가능

---

## POST의 요청과 응답
+ POST 요청
```json
POST /api/trainings HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{
  "title": "SQL 입문",
  "startDateTime": "2021-12-01T09:30:00",
  "endDateTime": "2021-12-03T17:00:00"
  "reserved": 0,
  "capacity": 8
}
```
+ URL 경로 끝에 ID를 붙이지 않고 리솟 전체를 나타내는 `/trainings`로 설정
+ 요청 바디에는 등록할 강의 데이터
+ 데이터 형식: JSON
  + `Content-Type: application/json`

+ POST 응답
```json
HTTP/1.1 201 Created
Location: http://localhost:8080/api/trainings/t99
```
+ 상태 코드는 201 사용
+ 새로 등록한 리소스의 URL을 Location 헤더에 기재

## POST에 대응하는 핸들러 메서드
```java
@PostMapping("/api/trainings")
public ResponseEntity<void> registerTraining(
        @Validated @RequestBody TrainingAdminInput trainingAdminInput) {
    Training training = trainingAdminService.register(trainingAdminInput);

    URI location = ServletUriComponentBuilder
            .fromCurrentRequestUri()
            .path("/{id}")
            .buildAndExpand(training.getId())
            .toUri();
    
    return ResponseEntity.created(lcation).build;
}

```