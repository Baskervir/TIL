## Java Collection Framework 정리
#### Collection Framework란?
+ 자료구조를 보다 쉽게 사용할 수 있게 자바에서 제공하는 클래스들의 집합
+ List, Set, Map 계열로 분류된다

#### Collection 계층 구조
```declarative
                Collection
                /        \
             List        Set
               |          |
           ArrayList    HashSet
           LinkedList   TreeSet
```
+ `Collection`은 인터페이스, `List`와 `Set`은 하위 인터페이스
+ `Map`은 `Collection`의 하위가 아니다 (별도)

#### 주요 인터페이스 특징 요약
+ List : 순서 보장, 중복 허용 // ArrayList, LinkedList, Vector
+ Set : 순서 없음, 중복 불가 // HashSet, LinkedHashSet, TreeSet
+ Map : key-value 쌍, key 중복 불가 // HashMap, TreeMap, LinkedHashMap