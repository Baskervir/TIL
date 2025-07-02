# Stream이란?
+ 자바의 컬렉션을 일련의 연산(필터링, 매핑, 정렬 등)을 거쳐 간결하고 효율적으로 처리할 수 있게 도와주는 기능

---

## for문 방식
```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public void original_for() {

    List<Integer> list = Arrays(1, 2, 3, 4, 5);
    List<Integer> evenSquares = new ArrayList<>();

    for (int n : list) {
        if (n % 2 == 0) {
            evenSquares.add(n * n);
        }
    }
}
```
## Stream 방식

```java
import java.util.List;
import java.util.stream.Collectors;

public void sample_stream() {
    List<Integer> evenSquares = list.stream()
            .filter(n -> n % 2 == 0)
            .map(n -> n * n)
            .collecter(Collectors.toList());
}
```

## 정리
| 메서드 | 의미 |
| --- | --- |
| `.stream()` | 리스트를 스트림으로 변환 |
| `.filter()` | 조건에 맞는 값만 통과 (`if`) |
| `.map()` | 값을 변환 (`n -> n * n)` |
| `.collect()` | 결과를 다시 컬렉션(List 등)으로 변환 |