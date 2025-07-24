# Review - CRUD
## <?> 에서 ? 로 선언한 이유 및 이점
+ `ResponseEntity<?>`에서 `?`는 와일드카드 타입
  + `<?>`는 `<? extends Object>`와 같은 의미
+ 불특정 타입의 응답을 다룰 때 쓰는 관용적 표현
+ 깔끔한 코드 유지에 도움
+ 실무에선 `ResponseEntity<Map<String, Object>>`처럼 명확한 타입으로 바꿔주는 게 유지보수에 유리

---

## Optional<Review>로 지정한 이유 및 이점
+ `Optional<Review>`는 null이 될 수 있는 Review 객체를 안전하게 감싸는 컨테이너
+ `findById()`는 해당 ID가 없을 수도 있다
  + null을 직접 반환하기 보다 Optional로 감싸서 처리
+ null 체크를 강제하여 NullPointerException 방지
+ `.map()`, `.orElse()`, `ifPresent()`같은 체이닝 가능
  + 코드 가독성과 함수형 스타일 증가
+ 실수로 null 그대로 넘겨 생기는 문제를 줄여준다

---

## noContent()란?
+ `ResponseEntity.noContent()`는 HTTP 상태 코드 204 No Content를 의미
  + 요청은 잘 처리되었으나 반환할 내용이 없다는 뜻
+ 클라이언트에게 `정상 처리되었으나 결과 없음`을 명확하게 전달 가능
+ `null`이나 빈 body를 억지로 보내는 대신
  + HTTP 표준을 지키며 깔끔한 응답 처리 가능

---

## 