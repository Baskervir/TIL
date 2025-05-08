# JPA 값 타입
+ JPA에서 값 타입은 엔티티와는 다르게 `식별자가 없고`, `생명주기를 엔티티에 의존`하며, `불변 객체`로 다루는 것이 권장된다

## 값 타입 종류
1. 기본 값 타입
2. 임베디드 타입 (복합 값 타입)
3. 값 타입 컬렉션

---

## 기본 값 타입
+ `int`, `Integer`, `String`, `Boolean` 등 자바의 기본 타입 및 래퍼 클래스
+ 엔티티 필드에 직접 선언하여 사용
+ 엔티티가 삭제되면 함께 삭제된다
+ 식별자가 없고, 공유되지 않으며, 엔티티에 종속적이다
+ 참조형 타입은 불변이 아니므로 주의 필요
```declarative
@Entity
public class Member {
    @Id @GeneratedValue
    private Long id;

    private String name;    // 기본 값 타입
}
```

## 임베디드 타입 (복합 값 타입)
+ 하나 이상의 필드를 묶어서 새롭게 정의한 타입
+ `@Embeddable`로 정의하고, 사용하는 곳에서는 `@Embedded`로 참조
+ 데이터베이스에는 그 구성 필드들이 동일한 테이블에 속한 컬럼들로 저장된다
+ `기본 생성자 필수`
```declarative
@Embeddable
public class Address {
    private String city;
    private String street;
    private String zipcode;
}

@Entity
public class Member {
    @Id @GeneratedValue
    private Long id;

    @Embedded
    private Address address;
}
```

### 속성 재정의 : `@AttributeOverride`
+ 같은 임베디드 타입을 여러 필드에서 사용할 때 컬럼명을 바꾸기 위해 사용
```declarative
@Embedded
@AttributeOverride ({
    @AttributeOverride(name = "city", column = @Column(name = "home_city")),
    @AttributeOverride(name = "street", column = @Column(name = "home_street")),
})
private Address homeAddress;
```
#### 특징
+ 임베디드 타입 자체가 `null`이면 모든 컬럼 값이 `null`로 저장
+ 엔티티와 생명주기 완전 공유
+ 엔티티 참조는 가능하지만, 값 타입 안에 엔티티를 넣는 건 권장되지 않는다

## 값 타입 컬렉션
+ 값 타입을 컬렉션 형태로 저장할 때 사용
+ `List<Address>`,`Set<String>` 등
+ `@ElementCollection` -> 값 타입 컬렉션 선언
+ `@CollectionTable` -> 별도 테이블 매핑
```declarative
@ElementCollection
@CollectionTable(name = "favorite_food", joinColumns = @JoinColumn(name = "member_id"))
@Column(name = "food_name")
private Set<String> favoriteFoods = new HashSet<>();
```

#### 단점
+ 지연 로딩만 지원된다. `LAZY`
+ 영속성 전이(cascade) 및 고아 객체 제거 안된다
+ 수정 시 전체 삭제 후 재삽입 -> 실무에서는 대체로 `OneToMany + Entity` 사용 권장

## 값 타입 사용 시 주의사항
+ 객체는 참조이므로, 값 공유로 인해 부작용이 발생할 수 있다
```declarative
Address a = new Address("city", "street", "zipcode");
Member m1 = new Member();
m1.setAddress(a);

Member m2 = new Member();
m2.setAddress(a);           // 같은 Address 인스턴스를 공유하면 위험하다
```

## 해결법
+ 모든 필드를 `final`로 하고, 생성자만 사용해서 초기화
+ setter 제거
```declarative
@Embeddable
public class Address {
    private final String city;
    private final String street;
    private final String zipcode;

    protected Address() {}      // JPA용 기본 생성자

    public Address(String city, String street, String zipcode) {
        this.city = city;
        this.street = street;
        this.zipcode = zipcode;
    }

    // getter만 제공
}
```

---

## 정리
| 구분 | 특징 | 주의사항 |
| --- | --- | --- |
| 기본 값 타입 | int, String 등 | 참조형은 공유 주의 |
| 임베디드 타입 | 복합 필드 구성, 재사용 가능 | null 전체 처리, `@AttributeOverride` |
| 값 타입 컬렉션 | 컬렉션으로 여러 값 저장 | 변경 시 전체 삭제/삽입 |