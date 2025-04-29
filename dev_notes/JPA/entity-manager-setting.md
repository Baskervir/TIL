# 엔티티 매니저 설정
## 엔티티 매니저 생성 과정
1. `Persistence`로부터 설정 정보 조회
2. `Persistence`로부터 `EntityManagerFactory`생성
3. `EntityManagerFactory`로부터 `EntityManager`생성

## 엔티티 매니저 팩토리
+ JPA를 시작하기 위해 persistence.xml의 설정 정보 사용
+ `엔티티 매니저 팩토리` 생성
+ JPA 사용 준비 완료
```declarative
EntityManagerFActory emf = Persistence.createEntityManagerFactory("sample");
```
+ persistence.xml에서 이름이 **sample**인 영속성 유닛을 찾아 엔티티 매니저 팩토리 생성
+ 엔티티 매니저 팩토리는 애플리케이션 전체에서 딱 한번만 생성한고 공유해서 사용

## 엔티티 매니저
```declarative
EntityManager em = emf.createEntitiyManager();
```
+ `emf`를 통해 엔티티 매니저 생성
+ JPA의 기능 대부분을 제공한다
+ 엔티티를 데이터베이스에 **등록**/**수정**/**삭제**/**조회**할 숭 ㅣㅆ다
+ 데이터소스(데이터베이스 커넥션)를 유지하면서 데이터베이스와 통신

## 주의
+ 사용이 끝난 엔티티 매니저와 엔티티 매니저 팩토리는 반드시 종료해야 한다
+ 스레드간에 공유하거나 재사용 금지

---

## 트랜잭션 관리
+ JPA를 사용하면 항상 트랜잭션 안에서 데이터를 변경해야 한다
+ 트랜잭션 없이 데이터 변경 시 예외 발생
+ `em`에서 트랜잭션 API를 받아와야 한다
```declarative
EntityTranscation tx = em.getTransaction();
try {
    tx.begin();     //트랜잭션 시작
    login();        //비즈니스 로직 실행
    tx.commit();    //트랜잭션 커밋
} catch (Exception e) {
    e.rollback();   //예외 발생 시트랜잭션 롤백
}
```
+ 트랜잭션 API를 사용 -> 비즈니스 로직 정상 동작 -> 트랜잭션 커밋
+ 예외 발생 시 트랜잭션을 롤백

---