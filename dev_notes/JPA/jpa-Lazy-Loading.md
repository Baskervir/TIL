# 지연 로딩
+ 데이터베이스 성능 최적화의 핵심 기술 중 하나
+ 연관된 엔티티를 즉시 로딩하지 않는다
  + 해당 엔티티를 사용할 때 조회하는 방식

## 예시
```declarative
@Entity
public class Order {
    @ManyToOne(fetch = FetchType.LAZY)
    private Member member;
}
```
+ `Order`를 조회해도 `Member`는 아직 조회되지 않음
+ `order.getMember().getName()`을 호출하는 순간 DB 쿼리가 날아간다

## 지연 로딩 동작 방식
| 단계 | 설명 |
| --- | --- |
| 객체 조회 시 | 실제 연관 객체는 `proxy`로 대체된다 |
| 프록시 사용 전 | 데이터 조회 X |
| 프록시 메서드 호출 시 | 쿼리 실행 -> DB 접근 |
| 세션 닫힌 상태에서 접근 | `LazyInitializationException` 발생 |

## 장점
+ 연관 객체를 매번 사용하지 않을 경우 성능
+ 초기 로딩 속도가 중요할 경우
+ 대용량 데이터에 대해 최적화가 필요한 경우

### `LazyInitializationException`에 대해
+ 트랜잭션(영속성 컨텍스트)이 닫힌 상태에서 프록시 초기화시도 시 발생
+ 컨트롤러 or 뷰 레이어에서 발생
  + 해결 방법
    + 트랜잭션 내에서만 지연 로딩된 객체를 접근하도록 해야 한다
    + DTO로 미리 필요한 정보만 뽑아서 반환

`항상 연관관계는 LAZY로 설정` - (기본전략) / 
`필요한 경우에만 JOIN FETCH로 명시적으로 로딩`

---

# 즉시 로딩
+ `지연 로딩`과 반대되는 개념
```declarative
@ManyToOne(fetch = FetchType.EAGER)
```
+ `Order`를 조회하면 바로 `Member`도 함께 조회한다
  + `Join` 쿼리 발생
`연관된 모든 엔티티를 즉시 로딩하면 성능 저하가 발생할 수 있다 (N+1 문제 발생 위험)`

---

