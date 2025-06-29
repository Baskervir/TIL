# Repository 단위 테스트
+ Repository의 역할은 데이터베이스에 접근
+ Repository의 단위 테스트에서는 실제 데이터베이스에 접근하여 예상대로 처리되는지 확인

---

## DI 컨테이너를 생성하기 위한 어노테이션
```java
@JdbcTest    //자동으로 DI 컨테이너 생성 & JavaConfig클래스 명시 X > 자동으로 JavaConfig 클래스를 찾는다 (main 메서드를 가진 JavaConfig 클래스가 로드) + 컴포넌트 스캔 제한 > @Repository Bean 등록 X
class JdbcTrainingRepositoryTest {
    @Autowired
    //자동 설정으로 등록된 Repository 클래스의 JdbcTemplate 객체를 @Autowired로 주입
    JdbcTemplate jdbcTemplate;
    
    TrainingRepository trainingRepository;
    
    @BeforeEach
    void setUp() {
        //@BeforeEach가 달린 메서드 내에서 수동으로 Repository 객체를 생성
        //Repository 클래스는 jdbcTemplate을 생성자로 받을 수 있도록 구성되어야 한다 < JdbcTemplate 객체를 생성자 인수로 지정한다
        trainingRepository = new JdbcTrainingRepository(jdbcTemplate);
    }
    
    @Test
    void test_selectById() {
        //테스트 대상인 Repository 객체의 메서드를 테스트 메서드 내에서 호출
        Training training = trainingRepository.selectById("t01");
        //호출한 메서드의 반환 값이 예상과 같은지 assertion하고 있다
        //assertion 하는 assertThat 메서드는 AssertJ라는 라이브러리의 static 메서드를 사용한다
        assertThat(training.getTitie()).isEqualTo("비즈니스 예절 교육");
    }
}
```

---

```java
//@JdbcTest를 붙인 테스트 클래스(@Import 사용)
@JdbcTest
//테스트 대상인 Repository의 구상클래스를 @Import로 불러온다
@Import(JdbcTrainingRepository.class)
class JdbcTrainingRepositoryTest {
    
    //JdbcTrainingRepository 클래스의 객체가 Bean으로 등록되어 @Autowired로 인젝션 가능해진다
    @Autowired
    TrainingRepository trainingRepository;
    
    @Test
    void test_selectById() {
        Training training = trainingRepository.selectById("t01");
        assertThat(training.getTitle()).isEqualTo("비즈니스 예절 교육");
    }
}
```

## DI 컨테이너 캐싱
+ 지속적 통합(CI)등에서 회귀 테스트를 수행할 때 여러 테스트 클래스를 동시에 실행한다
  + 테스트 클래스가 많아져 각각의 테스트 클래스에서 DI 컨테이너를 생성 > 테스트 실행 시간이 길어진다
    + DI 컨테이너를 캐싱하여 해결
  + DI 컨테이너 캐싱 조건
    + 각 테스트 클래스가 동일한 설정 클래스를 사용
    + DI 컨테이너에 로드된 설정 내용별로 DI 컨테이너를 생성하고 캐싱한다
```java
//@Import를 지정해서 테스트 클래스를 만든다
@JdbcTest
@Import(JdbcTrainingRepository.class)
class JdbcTrainingRepositoryTest {
    ...
}

@JdbcTest
@Import(JdbcReservationRepository.class)
class JdbcReservationRepositoryTest {
    ...
}
//각각의 설정을 불러오게 되어 비효율적이다
```

---

## 데이터베이스에 데이터 준비
+ 데이터베이스에 대한 접근을 포함해 테스트 해야한다
  + 테스트용 데이터를 데이터베이스에 등록해야 한다
    + 수작업으로 준비하면 테스트마다 작업 > 자동 테스트 X
+ 테스트용 데이터 준비 = 테스트 코드에서
+ Repository 클래스의 메서드를 테스트 할 경우
  + 테스트 클래스는 INSERT 문을 실행하여 테스트 데이터를 데이터 베이스에 등록
  + Repository 객체의 메서드를 호출
  + Repository 객체의 메서드에서 데이터베이스에 대해 SELECT 문을 싱핼 > 데이터를 가져온다
  + Repository 객체는 가져온 데이터를 반환값으로 반환
    + 반환된 데이터를 테스트 클래스가 확인
+ 내장 데이터베이스를 사용하는것이 좋다

---

## @SQL 어노테이션
```java
//@Sql을 붙여 괄호아네 SQL이 작성된 파일 지정
@Test
@Sql("JdbcTrainingRepositoryTest.sql")
void test_selectById() {
    Training training = trainingRepository.selectById("t01");
    assertThat(training.getTitle()).isEqualTo("비즈니스 예절 교육");
}
```
+ 클래스 패스에 있는 동일한 패키지에서 로드
```declarative
INSERT INTO training
(id, title, start_date_time, end_date_time, reserved, capacity) VALUES
...
```
