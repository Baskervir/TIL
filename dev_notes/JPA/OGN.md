[객체 그래프 탐색] Object Graph Navigation
    하나의 객체를 통해 연결된 다른 객체들에 접근하는 것

public class Team {
    private String name;
    private List<Member> members;
}

public class Member {
    private String name;
    private Team team;
}

Member member = ...; 상태에서 
member.getTeam().getName() : member -> team -> name

객체 간의 연결 관계(연관관계)를 따라가며 필요한 값으 꺼내오는 것

[JPA와 같은 ORM을 사용할 때, 객체 그래프 탐색이 실제 쿼리 실행과 연결되어 있다]

[지연로딩]
    객체가 실제로 필요한 시점까지 데이터를 불러오지 않고, 요청이 있을 때 그때 DB에서 로딩하는 방식
연관된 엔티티를 당장 불러오지 않고, getter로 접근하는 순간 쿼리를 날려서 불러오는 것

@Entity
public class Member {
    @Id
    @GeneratedValue
    private Long id;
    private String name;
    @ManyToOne(fetch = FetchType.LAZY)
    private Team team;
}

Member member = memberRepository.findById(1L).get();
Team team = member.getTeam();
String teamName = team.getName();
    memberRepository.findById(...) -> 이때 member만 가져온다
    getTeam() 호출 -> 이제서야 Team을 DB에 불러온다 (지연 로딩 발생)

[즉시로딩]
    @ManyToOne(fetch = FetchType.EAGER)
    Member를 조회할 때 바로 Team까지 함께 가져온다
    불필요한 상황에도 무조건 쿼리 발생 -> 성능 낭비, N+1 문제의 원인이 될 수 있다

[N+1 문제]
    하나의 쿼리(N=1)로 시작했지만, 연관된 데이터를 가져오느라 추가 쿼리(N개)가 발생하는 비효율적인 쿼리 문제

쿼리가 많아지고, 속도가 느려지며, DB부하가 커진다
해결방법 : Fetch Join, EntityGeaph