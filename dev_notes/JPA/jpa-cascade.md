# 영속성 전이
---
## 영속성 전이 : CASCADE
+ 특정 엔티티를 영속 상태로 만들 때, 여관된 엔티티도 영속 상태로 만들기 위해 사용한다
+ JPA는 `CASCADE` 옵션으로 영속성 전이를 제공한다
+ 부모 엔티티를 저장할 때 자식 엔티티도 함께 저장
`JPA에서 엔티티를 저장할 때 연관된 모든 엔티티는 영속 상태여야 한다`

## 영속성 전이 : 저장
+ `cascade = CascadeType.PERSIST`옵션을 설정
+ 부모만 영속화 하면 설정한 자식 엔티티까지 함께 영속화해서 저장한다
+ 양방향 연관관계를 추가한 다음 영속 상태로 만들어야 한다

## 영속성 전이 : 삭제
+ `cascade = CascadeType.REMOVE`로 설정
+ `DELETE SQL`을 3번 실행 -> 부모 & 연관된 자식 모드 삭제
+ 위의 설정 없이 엔티티 삭제 시
  + 자식 테이블에 걸려있는 외래 키 제약조건에 의해 데이터베이스에서 외래 키 무결성 예외 발생

## CASCADE의 종류
```declarative
public enum CascadeType {
    ALL,            //모두 적용
    PERSIST,        //영속
    MERGE,          //병합
    REMOVE,         //삭제
    REFRESH,        //REFRESH
    DETACH          //DETACH
}
```

---

## 고아 객체
+ JPA에서 부모 엔티티와 연관관계가 끊어진 자식 엔티티를 자동으로 삭제하는 기능
+ `orphanRemoval = true`옵션 설정
+ 부모 엔티티의 컬렉션에서 자식 엔티티의 참조만 제거하면 자식 엔티티가 자동으로 삭제된다
`삭제한 엔티티를 다른 곳에서도 참조한다면 문제 발생`

## 영속성 전이 + 고아 객체, 생명주기
+ `CascadeType.ALL + orphanRemoval = true`
+ 두 옵션의 활성화
  + 엔티티 스스로 생명주기를 관리 -> 부모 엔티티를 통해서 자식의 생명주기를 관리
+ 자식을 저장하려면 부모에 등록만 하면된다 (cascade)
+ 자식을 삭제하려면 부모에서 제거하면 된다 (orphanRemoval)ㅎ