# Map
+ `키`와 `값`으로 구성된 엔트리 객체를 저장한다
+ `키`는 중복 불가
+ `값`은 중복 저장 가능
+ 기존에 저장된 키와 동일한 키로 값을 저장하면 기존의 값은 없어지고 새로운 값으로 대체된다

+ `HashMap`, `Hashtable`, `LinkedHashMap`, `Properties`, `TreeMap`

---

## HashMap
+ 키로 사용할 객체가 hashCode() 메서드의 리턴값이 같고 equals() 메서드를 리턴할 경우 동일 키로 보고 중복 저장 허용 X
+ HashMap 컬렉션 생성 방법 / K와 V는 각각 키와 값의 타입을 지정할 수 있는 타입 파리미터다
```declarative
Map<K, V> map = new HashMap<K, V>();
```

---

## Hashtable
+ HashMap과 동일한 내부 구조를 가진다
+ 동기화된 메서드로 그성되어, 멀티 스레드가 동시에 Hashtable의 메서드 실행할 수 없는 차이 존재
    + 멀티 스레드 환경에서도 안전하게 객체를 추가, 삭제할 수 있다

+ 키 타입 : String / 값 타입 : Integer를 갖는 Hashtable 생성하기
```declarative
Map<String, Integer> map = new Hashtable<String, Integer>();
Map<String, Integer> map = new Hashtable<>();
```

