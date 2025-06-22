# 트랜잭션 전파란?
+ AService.java
```java
@Transactional
public void A() {
    ...
    BService.B();
    ...
}
```
+ BService.java
```java
@Transactional
public void B() {
    ...
}
```
+ `@Transactional`을 붙인 A 메서드(AService 클래스) 안에서 `@Transactional`을 붙인 B 메서드(BService 클래스)를 호출
+ A 메서드가 트랜잭션 처리되어 있으므로 A 메서드가 호출된 시점에 스프링은 자동으로 트랜잭션을 시작
+ 