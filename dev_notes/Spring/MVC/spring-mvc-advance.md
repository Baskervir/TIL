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
+