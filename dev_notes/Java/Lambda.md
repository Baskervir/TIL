# Lambda란?
+ 람다식 = 함수형 표현
+ 함수 자체를 간단히 표현하기 위한 문법
  + (n) -> n * n
  + **n**을 받아서 **n * n**을 반환하는 의미
  + 함수처럼 쓰이지만 익명 클래스보다 간결

## 전체 구조 흐름
```text
list.stream()                   //스트림으로 시작
    .filter(조건)                 //조건 걸고
    .map(값 변환)                  //가공하고
    .collect(결과로 반환);           //리스트나 셋으로 다시 모으기
```

