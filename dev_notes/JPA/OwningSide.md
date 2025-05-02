[연관 관계의 주인]
    JPA에서 "연관관계의 주인"은, 누가 외래키(FK)를 관리하느냐로 결정된다

@OneToMany(mappedBy = "team")
private List<Member> member;
```
@ManyToOne
@JoinColumn(name = "team_id")
private Team team;
```
Member가 Team을 가리키고 있다
DB 테이블의 외래키 team_id는 Member 테이블에 있다
    > Member가 관계의 주인이다

객체적으로 보면 Team이 중심처럼 보인다 멤버는 그 안에 속한 존재로 보인다
DB 기준 (외래키) Team은 외래키를 안가진다. 외래키는 Member에 (team_id) 존재한다
JPA 기준으로 보면 Team이 아닌 Member가 주인이 된다

