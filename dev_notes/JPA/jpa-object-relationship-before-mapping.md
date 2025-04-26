# 객체 연관관계 기초 정리 (JPA 적용 이전)
### 기본 개념
+ 연관관계 : 객체와 객체 사이의 관계
+ JPA 적용 전에도 객체 참조(Reference)로 관계를 맺는다
+ JPA 적용 시 이 관계는 데이터베이스 외래 키(Foreign Key)로 매핑된다

### 연관관계 종류
1. 일대일
   + 하나의 객체가 하나의 객체를 참조

#### 주 테이블에 외래 키
`단방향`
```declarative
@Entity
public class Member {
    @Id @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;

    private String username;

    @OneToOne
    @JoinColumn(name = "LOCKER_ID")
    private Locker locker;
    ...
}

@Entity
public class Locker {
    @Id @GeneratedValue
    @Column(name = "LOCKER_ID")
    private Long id;

    private String name;
    ...
}
```
`양방향`
```declarative
@Entity
public class Member {
    @Id @GeneratedValue
    @Column(name = "MEMBER_ID")
    private Long id;
    
    private String username;
    
    @OneToOne
    @JoinColumn(name = "LOCKER_ID")
    private Locker locker;
    ...
}

@Entity
public class Locker {
    @Id @GeneratedValue
    @Column(name = "LOCKER_ID")
    private Long id;
    
    private String name;
    
    @OneToOne(mappedBy = "locker")
    private Member member;
    ...
}
```

2. 일대다
    + 하나의 객체가 여러 객체를 참조
```declarative

```