*** 자바에서 new 없이 객체를 반환하는 대표적인 6가지 방식

[싱글턴 패턴]
public class Settings {
    private static final Settings INSTANCE = new Settings();
    private Settings() {} // 외부에서 new 못하게
.
    public static Settings getInstance() {
        return INSTANCE; // new 없이 동일 객체 반환
    }
}

[정적 팩토리 메서드]

[캐시 / 풀링된 객체 반환]

[JVM 내부 객체 (ex. String 리터럴)]

[Spring Bean / JPA EntityManager 관리 객체]

[Optional or Stream API 내부 동작]


*** new를 쓰지 않고 객체를 만들어야 하는 상황 ***
객체 생성을 제어하거나 제한해야 할 때 [객체 1개만 유지해야 한다]
    직접 생성하지 않고, 특정 방식으로만 생성하게 하고 싶을 때

객체 풀 / 캐시를 활용해서 재사용해야 할 때 [메모리 절약], [성능 최적화]
    성능 최적화를 위해 new 최소화

프레임워크가 객체 생명주기를 관리할 때 [생명주기 / 기능 위임 (AOP, 트랜잭션 등)]
    개발자가 new를 직접 쓰면 안된다

프록시 객체가 필요한 상황 [지연 로딩, 변경 감지 등 기능 사용]
    프록시를 통해 지연 로딩, 트랜잭션 제어 등 해야 할 때

테스트나 코드 격리를 위해 대체 객체를 주입해야 할 때 [유닛 테스트, 의존성 주입, 모킹]
    new를 사용하면 변경이 불가능한 구조가 된다

자바에서 new는 강력하지만, 언제 쓰면 안 되는지도 중요하다
스프링, JPA, DI 컨테이너, 테스트 환경에선 new 금지 가 실무 기본 룰이다
