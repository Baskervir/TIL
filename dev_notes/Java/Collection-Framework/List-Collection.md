# List 컬렉션
---
## ArrayList
- Java에서 제공하는 **가변 배열** 클래스
- `List` 인터페이스를 구현
- **순서가 유지**되며, **중복 허용**
- 내부적으로 배열을 사용하여 요소를 저장

## 특징
- **검색 속도**는 빠름 (index 기반)
- **삽입/삭제**는 느림 (중간에 삽입 시 뒤 요소 이동 필요)
- 크기 자동 확장 (`ensureCapacity`)

## 주요 메서드

| 메서드 | 설명 |
|--------|------|
| `add(E e)` | 요소 추가 |
| `add(int index, E e)` | 지정 위치에 요소 추가 |
| `get(int index)` | 해당 인덱스 요소 반환 |
| `remove(int index)` | 해당 인덱스 요소 제거 |
| `set(int index, E e)` | 해당 인덱스 요소 변경 |
| `contains(Object o)` | 요소 존재 여부 확인 |
| `size()` | 요소 개수 반환 |
| `clear()` | 모든 요소 제거 |

## 예제 코드

```java
import java.util.ArrayList;

public class ArrayListExample {
    public static void main(String[] args) {
        ArrayList<String> fruits = new ArrayList<>();

        // 요소 추가
        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Grape");

        // 특정 위치에 삽입
        fruits.add(1, "Orange");

        // 출력
        System.out.println("과일 목록: " + fruits);

        // 요소 접근
        System.out.println("두 번째 과일: " + fruits.get(1));

        // 요소 삭제
        fruits.remove("Banana");
        System.out.println("삭제 후 목록: " + fruits);

        // 요소 변경
        fruits.set(0, "Green Apple");
        System.out.println("변경 후 목록: " + fruits);

        // 전체 삭제
        fruits.clear();
        System.out.println("모두 삭제 후: " + fruits);
    }
}
```

---

## Vector
+ AarrayList와 동일한 내부 구조
+ 동기화된 메서드로 구성되어 있기 때문에 멀티 스레드가 동시에 Vector() 메서드를 실행할 수 없는 차이
+ 멀티 스레드 환경에서는 안전하게 객체를 추가, 삭제할 수 있다

### Vector 컬렉션 생성
```declarative
List<E> list = new Vector<E>();     //E에 지정된 타입의 객체만 저장
List<E> list = new Vector<>();      //E에 지정된 타입의 객체만 저장
List list = new Vector();           //모든 타입의 객체를 저장
```

---

## LinkedList
+ ArrayList와 사용 방법은 동일 / 내부 구조는 완전히 다르다
+ ArrayList는 내부 배엘에 객체 저장 / LinkedList는 인접 객체를 체인처럼 연결해서 관리
+ 특정 위치에서 객체 변경이 일어나면 바로 앞뒤 링크만 변경
  + 삭제와 삽입이 빈번한 곳에서는 ArrayList보다 좋은 성능

### LinkedList 컬렉션 생성
```declarative
List<E> list = new LinkedList<E>();     //E에 지정된 타입의 객체만 저장
List<E> list = new LinkedList<>();      //E에 지정된 타입의 객체만 저장
List list = new LinkedList();           //모든 타입의 객체를 저장
```