# 영속성 컨텍스트
+ JPA를 이해하는데 가장 중요한 용어
+ `엔티티를 영구 저장하는 환경`이라는 뜻
+ EntityManager.persist(entity);

## 영속성 컨텍스트는 논리적인 개념
+ 엔티티 매니저를 통해서 영속성 컨텍스트에 접근

## 엔티티의 생명주기
+ 비영속 (new/transient)
  + 영속 컨테그스트와 전혀 관계가 없는 `새로운` 상태
+ 영속 (managed)
  + 영속성 컨텍스트에 관리되는 상태
+ 준영속 (detached)
  + 영속성 컨텍스트에 저장되었다가 `분리`된 상태
+ 삭제 (removed)
  + `삭제`된 상태



## JPA가 영속성 컨텍스트에 저장하는 이유
+ 1차 캐시 역할
  + 동일 엔티티 반복 조회 시 DB 안 건드리고 메모리에서 해결 (효율적)
+ 변경 감지 (Dirty Checking)
  + 이후 setter 등으로 필드 변경 -> 트랜잭션 커밋 시 자동 UPDATE
+ 동일성 보장
  + 같은 ID의 엔티티는 항상 같은 객체(==)로 유지
+ 지연 로딩 가능
  + 연관된 프록시 객체들도 함께 관리된다
+ 쓰기 지연 SQL 저장소와 통합
  + persist()헤도 즉시 INSERT 안 됨 -> 트랜잭션 끝날 때 일괄 실행

## 만약 영속 컨텍스트에 저장하지 않는다면?
+ 같은 엔티티 여러 번 조회 시 객체가 다 달리짐
+ 변경 감지 안 되고, 명시적으로 merge()해야한다
+ 성능 저하, JPA의 핵심 기능인 자동 관리 의미 상실

## 정리
+ DB에서 꺼내오면 끝 이 아닌 객체 상태 관리까지 해야 한다
+ 영속 컨텍스트의 기능들로 캐시, 변경감지, 지연 로딩, 동일성 보장 등 장점 다수 존재
+ 단순 조회만 생각할 경우 @Transactional(readOnly = true) + DTO 변환 or NaviteQuery 추천

---

## 영속 엔티티의 동일성 보장
```declarative
Member a = em.find(Member.class, "member1");
Member b = em.find(Member.class, "member1");

System.out.println(a == b);     //동일성 비교 true
```
+ 1차 캐시로 반복 가능한 읽기(Repeatable Read) 등급의 트랜잭션 격리 수준을 데이터베이스가 아닌 애플리케이션 차원에서 제공

---

## 트랜잭션을 지원하는 쓰기 지연
```declarative
EntityManager em = emf.createEntityManager();
EntityTransaction transaction = em.getTransaction();
//엔티티 매니저는 데이터 변경 시 트랜잭션을 시작해야 한다
transaction.begin();    //[트랜잭션] 시작

em.persist(memberA);
em.persist(memberB);
//여기까지 INSERT SQL을 데이터베이스에 보내지 않는다

//커밋하는 순간 데이터베이스에 INSERT SQL을 보낸다
transaction.commit();   //[트랜잭션] 커밋
```
+ 트랜잭션 시작
+ em.persist로 멤버a 멤버b를 넣어둔다
+ 아직 INSERT SQL을 보내지 않는다(DB에)
+ JPA에 누적중
+ 트랜잭션 커밋하는 순간 DB에 SQL을 보낸다

---

## 엔티티 수정 - 변경 감지
```declarative
EntityManager em = emf.cerateEntityManager();
EntityTransaction transactio = em.getTransaction();
transaction.begin();    //[트랜잭션] 시작

//영속 엔티티 조회
Member memberA = em.find(Member.class, "memberA");

//영속 엔티티 데이터 수정
memberA.setUsername("hi");
memberA.setAge(10);

//em.update(member)와 같은 코드가 있어야 하지 않을까?

transaction.commit();   //[트랜잭션] 커밋
```
+ 커밋을 하면 내부적으로 flush호출
+ 엔티티와 스냅샷 비교 / 스냅샷은 1차 캐시 내에 존재 / 영속성 컨텍스트에 반영 된 상태에 스냅샷을 기록
+ flush호출 시에 비교 후 변경된 사항을 쓰기 지연 SQL 저장소에 쿼리를 저장
+ 이후 DB에 반영

---

## 플러시
+ 영속성 컨텍스트의 변경내용을 데이터베이스에 반영
+ 보통 데이터베이스 트랜잭션 될때 발생
+ 영속성 컨텍스트와 데이터베이스의 상태를 맞춰주는 역할

## 플러시 발생
+ 변경 감지
+ 수정된 엔티티 쓰기 지연 SQL 저장소에 등록
+ 쓰기 지연 SQL 저장소의 쿼리를 데이터베이스에 전송

## 영속성 컨텍스트를 플러시하는 방법
+ em.flush() - 직접 호출
+ 트랜잭션 커밋 - 플러시 자동 호출
+ JPQL 쿼리 실행 - 플러시 자동 호출

---

## 영속성 관리
| 세부 | 정리 포인트 |
| --- | --- |
| EntityManager | 하나의 작업 단위에서 동작, 직접 쿼리 대신 동작 |
| 영속성 컨텍스트 | 객체를 관리하는 1차 캐시 |
| 생명주기 | new -> managed -> detached -> removed |
| 플러시 | DB에 반영하는 시점 = flush, commit |
| 준영속 | 영속성 끊긴 상태 -> 더 이상 감지되지 않음 |

---

## 준영속
+ 영속 -> 준영속
+ 영속 상태의 엔티티가 영속성 컨텍스트에서 분리 / detached
+ 영속성 컨텍스트가 제공하는 기능 사용 불가

### 준영속 상태로 만드는 방법
+ em.detach(entity)
  + 특정 엔티티만 준영속 상태로 전환
+ em.clear()
  + 영속성 컨텍스트를 완전히 초기화
+ em.close()
  + 영속성 컨텍스트를 종료