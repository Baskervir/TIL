# Service 단위 테스트

## Service 단위 테스트 개요
+ Service의 역할은 업무 로직
  + 로직이 원하는 대로 작동하는지 확인한다
+ 의존 객체인 Repository 객체는 Mock 객체로 전환
```text

:테스트 클래스 -----> :Service ------> :Repository의 Mock
                  |Service 의  |
                  |단위 테스트  | = 테스트 대상
```

+ Mock의 Repository 객체는 데이터베이스에 접근하지 않는다
  + 적절하게 정해진 값을 반환하여 처리 수행
+ Mock 객체는 Mock용 라이브러리를 사용
  + `Mockito 라이브러리`
+ Service의 단위 테스트의 경우 웹이나 데이터베이스에 관련된 라이브러리 사용 X
  + DI 컨테이너를 생성하지 않고 테스트하는 경우가 많다

---

## Mockito란?
+ Mock 객체를 쉽게 만들 수 있는 오픈 소스 라이브러리
  + 스프링 부트의 테스트용 스타터에 포함
+ Mockito 외에도 Mock용 라이브러리 존재

---

## Mockito를 사용한 Service 단위 테스트
```java
//JUnit에서 제공하는 @ExtendWith 를 사용 > 지정된 클래스의 처리를 호출해준다
//@InjectMocks / @Mock 과 같은 Mockito가 제공하는 어노테이션을 탐지 > 어노테이션에 따른 처리 수행
@ExtendWith(MockitoExtension.class)
class TrainingAdminServiceImplTest {
    
    //선언된 필드 클래스의 생성자를 Mockito가 호출 > 자동으로 객체 생성 > 필드에 할당
    //Mock 객체로서 준비된 의존 객체를 인젝션
    //인젝션은 Spring이 아닌 Mockito가 수행
    @InjectMocks
    TrainingAdminServiceImpl trainingAdminService;
    
    //Mock 객체 준비
    //지정된 타입의 Mock 객체 생성 > 필드에 할당
    //Mock 객체의 구상 클래스는 Mockito가 테스트 실행 시 자동으로 생성
    @Mock
    TrainingRepository trainingRepository;
    
    @Test
    //테스트 메서드 안에서는 Mock 객체의 메서드가 호출되었을 때의 동작을 지정 (무엇을 반환할 것인가?)
    void test_findById() {
        Training training = new Training();
        training.setTitle("비즈니스 예절 교육");
        //메서드 실행 시 지정된 Title을 가진 Training 객체를 반환하는 동작을 지정
        doReturn(training).when(trainingRepository).selectById("t01");
        
        //테스트 대상인 Service 객체의 메서드를 호출
        //findById 메서드 내에서 Mock 객체의 selectById 메서드 호출
        //'비즈니스 예절 교육'이 설정된 Training 객체가 findById 메서드에서 반환
        Training actual = trainingAdminService.findById("t01");
        //반환된 Training 객체의 제목을 어설션
        assertThat(actual.getTitle()).isEqualTo("비즈니스 예절 교육");
    }
}
```
```text
              +findById("t01")                               +selectById("t01")
:테스트 클래스 <=================> :TrainingAdminServiceImpl <====================> :Mock의 TrainingRepository
              ㄴ:Training[title[비즈니스 예절 교육]]             ㄴ:Training[title[비즈니스 예절 교육]]
```

