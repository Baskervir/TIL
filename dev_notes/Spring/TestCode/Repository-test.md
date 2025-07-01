# Repository 단위 테스트
+ Repository의 역할은 데이터베이스에 접근
+ Repository의 단위 테스트에서는 실제 데이터베이스에 접근하여 예상대로 처리되는지 확인

---

## DI 컨테이너를 생성하기 위한 어노테이션
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