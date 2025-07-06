# Repository 단위 테스트
+ Repository의 역할은 데이터베이스에 접근
+ Repository의 단위 테스트에서는 실제 데이터베이스에 접근하여 예상대로 처리되는지 확인

---

## DI 컨테이너를 생성하기 위한 애너테이션
```java
@JdbcTest    //자동으로 DI 컨테이너 생성 & JavaConfig클래스 명시 X > 자동으로 JavaConfig 클래스를 찾는다 (main 메서드를 가진 JavaConfig 클래스가 로드) + 컴포넌트 스캔 제한 > @Repository Bean 등록 X
class JdbcTrainingRepositoryTest {
    @Autowired
    JdbcTemplate jdbcTemplate;    //
    
    TrainingRepository trainingRepository;
    
    @BeforeEach
    void setUp() {
        trainingRepository = new JdbcTrainingRepository(jdbcTemplate);
    }
    
    @Test
    void test_selectById() {
        Training training = trainingRepository.selectById("t01");
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

## @SQL 애너테이션
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
('t01', '비즈니스 예절 교육', '2023-08-01 09:30', '2023-08-03 17:00', 1, 10),
('t02', '자바 교육', '2023-09-01 09:30', '2023-08-03 17:00', 1, 5),
('t03', '마케팅 교육', '2023-10-01 09:30', '2023-10-03 17:00', 5, 5);
```
+ 이미 데이터가 등록되어 있다는 전제하에 테스트 메서드 작성 가능

```java
@Test
@Sql({"JdbcTrainingRepositoryTest_1.sql", "JdbcTrainingRepositoryTest_2.sql"})
void test_selectById() {
  ...
}
```
+ `@Sql`애너테이션으로 SQL 파일 여러 개를 지정할 수 있다
  + 지정된 순서대로 SQL 파일에 기재된 SQL이 실행된다
  + 메서드뿐만 아니라 클래스에도 적용할 수 있다
    + 클래스에 적용한 경우, 테스트 클래스에 속한 모든 테스트 메서드에서 지정된 SQL 파일이 실행

---

## 데이터 정리
+ 테스트 메서드가 실행되면 해당 테스트 메서드에 필요한 데이터가 등록된다
  + 다음 테스트 메서드를 실행할 때 필요한 데이터를 다시 등록하기 위해선 기존 데이터를 정리 해야 한다
+ `@JdbcTest`를 사용하면 자동으로 트랜잭션이 시작 > 테스트 메서드 종료시 자동으로 롤백
  + 트랜잭션을 시작하는 시점은 @Sql에서 지정한 SQL 파일을 실행하기 전
    + @Sql에서 등록한 데이터 롤백
+ @Transactional은 @JdbcTest에 포함된 기능

### 정리
+ 위의 테스트는 Repository 클래스의 메서드를 호출하여 예상대로 데이터를 가져올 수 있는지 확인하는 참조 계열 테스트

## 갱신 개열 처리 테스트
+ Repository 클래스의 메서드를 호출하여 예상대로 데이터가 업데이트되었는지를 테스트 한다

```text
     +----------테스트 데이터 등록-------------------------->+
:테스트 클래스 -------------> :Repository -------------> 데이터페이스
     +           ㄴ처리 호출                 ㄴ데이터베이스가 갱신된다.
     +----------데이터 확인-------------------------------->+
```
+ 테스트 데이터를 등록하고 Repository 객체 처리를 호출하는 부분까지는 참조 계열 테스트와 동일
+ 이번에는 Repository 객체의 처리 과정에서 업데이트 처리 발생
  + 데이터베이스의 데이터 갱신 (업데이트는 등록, 변경, 삭제 포함)
+ Repository 객체의 처리가 끝나면 테스트 클래스에서 데이터베이스의 내용을 확인

---

## JdbcTemplate으로 데이터 확인하기
+ 테스트 클래스에 JdbcTemplate 객체를 인젝션
  + 임의의 SQL로 데이터를 가져와 예상대로 데이터가 나오는지 확인
```java
...
@Autowired
JdbcTemplate jdbcTemplate;  //JdbcTemplate 객체를 인젝션
...

@Test
void test_update() {
    Training training = new Training();
    training.setId("t01");
    training.setTitle("SQL 입문");
  ...
    //강의 데이터를 업데이트하는 메서드를 호출 > training 테이블의 데이터 갱신
    boolean result = trainingRepository.update(training);
    assertThat(result).isEqualTo(true);
    //JdbcTemplate 객체를 사용하여 갱신된 레코드를 SELECT 문으로 가져온다
    Map<String, Object> trainingMap = jdbcTemplate.queryForMap(
            "SELECT * FROM training WHERE id=?", "t01");
    //가져온 레코드의 컬럼 값을 어선셜
    assertThat(trainingMap.get("title")).isEqualTo("SQL 입문");
  ...
}
...
```

---
### 기본 CRUD 동작 확인
```java
@Test
void test_insert_and_select() {
    Training newTraining = new Training("t99", "신입 사원 교육", ...);
    trainingRepository.insert(newTraining);

    Training loaded = trainingRepository.selectById("t99");
    assertThat(loaded).isNotNull();
    assertThat(loaded.getTitle()).isEqualTo("신입 사원 교육");
}
```

### 예외 상황 처리 확인
```java
@Test
void test_selectById_whenNotExists() {
    Training training = trainingRepository.selectById("not_found_id");
    assertThat(training).isNull();
}
```

### UPDATE 로직 테스트
```java
@Test
void test_update() {
    trainingRepository.updateTitle("t01", "새로운 제목");
    Training updated = trainingRepository.selectById("t01");
    assertThat(updated.getTitle()).isEqualTo("새로운 제목");
}
```

### DELETE 로직 테스트
```java
@Test
void test_delete() {
    trainingRepository.deleteById("t01");
    Training deleted = trainingRepository.selectById("t01");
    assertThat(deleted).isNull();
}
```

---

### 테스트 설정 팁
+ H2 데이터베이스를 테스트용으로 활용
  + `@JdbcTest`는 기본적으로 내장 H2 DB를 사용
  + `src/test/resources/schema.sql`, `data.sql`을 통해 테스트용 스키마와 초기 데이터 자동 설정 가능



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