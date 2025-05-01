# 트랜잭션 종료 후 LAZY 연관객체 접근 시 발생하는 문제
```declarative
트랜잭션이 끝나면 영속성 컨텍스트(EntityManager)가 닫히고,<br>LAZY로 설정된 연관 객체는 아직 DB에서 가져오지 않은 상태이기<br>때문에, 연관 객체를 접근하려 하면 예외가 터진다.
```

###### 터지는 예외 이름
```declarative
org.hibernate.LazyInitializationException
```
+ "could not initialize proxy = no Session"
+ 세션이 없어서 프록시 초기화하지 못했다

## 문제 발생 원인
+ LAZY 로딩은 `필요로 할 때` DB 쿼리를 날려서 데이터를 채우려 한다
+ 트랜잭션이 끝나면 `DB 연결(세션)` 자체가 끊긴다
+ 끊긴 세션 상태에서는 쿼리를 날릴 수 없다
+ 그래서, 프록시 객체만 남아 있고 진짜 데이터는 없는 상태가 돼버린다
`결국 터진다`

---

