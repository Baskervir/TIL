# Set 컬렉션
---
## HashSet
+ Set 컬렉션 중에서 가장 많이 사용된다
```declarative
Set<E> set = new HashSet<E>();      //E에 지정돈 타입의 객체만 저장
Set<E> set = new HashSet<>();       //E에 지정된 타입의 객체만 저장
Set set = new HashSet();            //모든 타입의 객체를 저장
```

+ `HashSet`은 동일한 객체는 중복 저장하지 않는다
  + 동일한 객체란 동등 객체
+ 다른 객체라도 hashCode() 메서드의 리턴값이 같다
+ equals() 메서드가 true를 리턴하면 동일한 객체라 판단 > 중복 저장하지 않는다
+ 문자열을 저장할 경우, 같은 문자열을 갖는 String 객체는 동등한 객체로 간주한다
  + 같은 문자열이면 hashCode() 리턴값이 같고 equals()의 리턴값이 true다

### Set 컬렉션은 인덱스로 객체를 검색해서 가져오는 메서드가 없다
+ 객체를 한 개씩 반복해서 가져와야 한다
  1. for문 이용
```declarative
Set<E> set = new HashSet<>();
for (E e : set) {
...
}
```
  2. Set 컬렉션의 iterator() 메서드로 반복자를 얻어 객체를 하나씩 가져오기
```declarative
Set<E> set = new HashSet<>();
Iterator<E> iterator = set.iterator();
```

| 리턴 타입 | 메서드명 | 설명 |
| --- | --- | --- |
| boolean | hashNext() | 가져올 객체가 있으면 true를 리턴 / 없으면 false |
| E | next() | 컬렉션에서 하나의 객체를 가져온다 |
| void | remove() | next()로 가져온 객테를 Set 컬렉션에서 제거한다 |
```declarative
while(iterator.hashNext()) {
    E e = iterator.next();
}
```

## TreeSet
+ 이진 트리를 기반으로 하는 Set 컬렉션
+ 여러 개의 노드가 트리 형태로 연결된 구조
+ `루트 노드`라고 불리는 하나의 노드에서 시작해 각 노드에 최대 2개의 노드 연결

### TreeSet 컬렉션 생성
```declarative
TreeSet<E> treeSet = new TreeSet<E>();
TreeSet<E> treeSet = new TreeSet<>();
```

