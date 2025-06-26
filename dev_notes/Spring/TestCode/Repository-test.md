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