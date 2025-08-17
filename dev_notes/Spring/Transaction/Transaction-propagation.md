# 트랜잭션 전파란?
```java
:AService.java
@Transactional
public void A() {
    ...
    BService.B();
    ...
}
```
```java
:BService.java
@Transactional
public void B() {
    ...
}
```
+ `@Transactional`을 붙인 A 메서드(AService 클래스) 안에서 `@Transactional`을 붙인 B 메서드(BService 클래스)를 호출
+ A 메서드가 트랜잭션 처리되어 있으므로 A 메서드가 호출된 시점에 스프링은 자동으로 트랜잭션을 시작
  + A 메서드 안에서 호출되는 B 메서드에도 트랜잭션 처리
+ B 메서드가 호출됐을 때 A 메서드에서 시작된 트랜잭션 안에서 B 메서드의 처리를 하는 것을 `트랜잭션 전파`라고 한다
+ 트랜잭션을 전파시킬 것인지, 전파하지 않고 새롭게 다른 트랜잭션을 시작할지 선택할 수 있다

---

## 트랜잭션 전파 설정하기
+ @Transactional의 `propagation`속성으로 설정할 수 있다
```java
:AService.java
@Transactional
public void A() {
    ...
    BService.B();
    ...
}
```
```java
:BService.java
@Transactional(propagation=XXX)
public void B() {
    ...
}
```
+ 호출되는 쪽인 B 메서드의 @Transactional에서 설정한다
  + 일반적으로 `REQUIRED`와 `REQUIERS_NEW`를 많이 사용한다

## REQUIRED의 동작
+ 기본 설정
  + propagation 속성을 지정하지 않으면 자동으로 동작
+ `REQUIRED`설정 : 호출자에서 이미 트랜잭션이 시작되었다
  + 호출된 메서드도 같은 트랜잭션 안에서 처리된다 > 새로운 트랜잭션은 시작되지 않는다
+ B 메서드가 정상적으로 종료된 후에 A 메서드의 계속되는 처리 과정에서 예외 발생하여 `롤백`
  + B 메서드 안에서 업데이트한 내용도 `롤백`
+ 호출한 측에서 츠랜잭션이 시작되지 않은 경우
  + 새로 트랜잭션이 시작된다

## NESTED의 동작
+ 부모 트랜잭션이 열려있다 -> savepoint 생성
  + 자식(NESTED)이 롤백 -> savepoint까지만 롤백
  + 부모 트랜잭션은 그대로 유지
+ 부모 트랜잭션이 없다 -> 새로운 트랜잭션 시작
+ 부모 트랜잭션이 최종적으로 롤백 -> 자식에서 성공한 것까지 모두 롤백

## REQUIRES_NEW의 동작
+ `REQUIRES_NEW`를 지정하면 호출한 곳에서 이미 트랜잭션이 시작되었는지 여부는 무관계
  + 항상 새로운 트랜잭션이 시작된다
+ 호출자인 A 메서드에서 트랜잭션이 시작되었다
  + B 메서드로 트랜잭션이 전파되지 않고 새로운 트랜잭션이 시작된다
+ B 메서드가 정상적으로 종료된 시점에 트랜잭션이 커밋
  + A 메서드의 후속 처리에서 예외가 발생하여 `롤백`
    + B 메서드 내에서 갱신한 내용은 `롤백되지 않는다`
+ A 메서드에서 트랜잭션이 시작되지 않은 경우 B 메서드가 호출된 시점에 트랜잭션이 시작된다

---

## REQUIRES_NEW가 필요한 상황
+ 로그를 데이터베이스에 등록하는 경우를 생각해보자
+ 로그 관리를 추가한 로직에서 일반적으로 로직의 예외 발생시, 로그 또한 롤백 발생
  + 이 경우 로그 등록 메서드 처리가 롤백되지 않도록 `REQUIRES_NEW`를 지정

### 주의사항
```java
@Service
public class AService {
    @Transactional
    abcMethod() {
        ...
        def메서드();
        ...
    }
    
    @Transactional(propagation = REQUIRES_NEW)
    defMethod() {
        ...
    }
}
```
+ 현재 한 클래스에 트랜잭션이 지정된 메서드가 두 개 있다
+ abc 메서드에서 def 메서드를 호출하고 있다
+ abc 메서드가 호출되는 시점에 트랜잭션이 시작
  + def 메서드에서는 `REQUIRES_NEW`가 지정되어 있어 새로운 트랜잭션 시작을 예상한다
    + 실제로는 abc 메서드에서 def 메서드를 호추해도 새로운 트랜잭션은 시작되지 않는다

### 분석
+ 트랜잭션 제어는 `Proxy`객체가 수행한다
+ AService 객체가 호출될 때 Proxy 객체가 AService 객체의 호툴자에 인젝션
+ AService 객체의 메서드가 호출될 때 Proxy 객체가 중개
  + 트랜잭션 제어 수행
+ AService 객체가 자체적으로 가지고 있는 다른 메서드 (def 메서드)를 호출할 경우
  + Proxy 객체가 처리를 중개할 수 없다
    + def 메서드에 REQUIES_NEW가 지정되어 있더라도 Proxy 객체가 중개하지 않는다
      + 새로운 트랜잭션은 시작되지 않는다

### 수정
```java
:AService.java
@Service
public class AService {
    private final BService bService;
    public AService(BService bService) {
        this.bService = bService;
    }
    
    @Transactional
    abcMethod() {
        ...
        BService.defMethod();
        ...
    }
}
```
```java
:BService.java
@Service
public class BService {
    @Transactional(propagation = REQUIRES_NEW)
    defMethod() {
        ...
    }
}
```
+ 생성된 BService 객체에 대해 Proxy 객체가 생성
+ AService 객체에 인젝션
+ AService 객체에서 defMethod를 호출했을 때 Proxy 객체가 처리를 중개
  + 새로운 트랜잭션 시작