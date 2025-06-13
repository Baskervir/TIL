# Bean 정의
## 프로파일이란?
+ 설정을 그룹화하는 DI 컨테이너의 기능이다
+ 환경에 따른 설정을 그룹화할 수 있으며, 그룹 이름이 프로파일 이름이다

## 환경별 프로파일
+ 실제 개발 프로젝트에서는 환경별 프로파일로 그룹화하는 것이 일반적이다
+ 별도의 프로파일로 속하지 않은 Bean정의는 항시 활성화 된 상태이다
```text
TrainingServiceImpl 클래스의 Bean 정의, JdbcTrainingRepository클래스의 Bean정의,
ExternalTrainingRepository 클래스의 Bean정의가 있다고 가정한다

JdbcTrainingRepository 클래스와 ExternalTrainingRepository 클래스는 공통 인터페이스 TrainingRepository를 구현한다

ExternalTrainingRepository 클래스의 Bean 정의를 production 프로파일에, JdbcTrainingRepository 클래스의 Bean 정의를 staging 프로파일에 속하게 하면
TrainingServiceImpl 클래스의 Bean 정의는 어떤 프로파일에도 속하지 않게 된다

프로덕션 환경에서 실행할 경우: production 프로파일 활성화 > DI 컨테이너 생성
 - ExternalTrainingRepository 클래스와 TrainingServiceImpl 클래스의 Bean 정의 사용
 - ExternalTrainingRepository 객체와 TraningServiceImpl 객체가 생생 > DI 컨테이너에서 관리
 
TrainingServiceImpl 객체가 TrainingRepository 객체를 인젝션 하게 되어 있다면
 - TrainingServiceImpl 객체에 ExternalTrainingRepository 객체가 인젝션된다
```

## 프로파일 사용법
+ 설정을 그룹화할 때는 `@Profile`을 사용
```java
@Repository
@Profile("production")
public class ExternalTrainingRepository implements TrainingRepository {
    ...
}
```
```java
@Repository
@Profile("staging")
public class JdbcTrainingRepository implements TrainingRepository {
    ...
}
```
```java
@Service
public class TrainingServiceImpl implements TrainingService {
    ...
}
```
+ ExternalTrainingRepository 클래스의 Bean 정의는 production 프로파일에
+ JdbcTrainingRepository 클래스의 Bean 정의는 staging 프로파일에
+ TrainingServiceImpl 클래스의 Bean 정의는 어떤 프로파일에도 속하지 않아 > `@Profile`이 붙지 않는다

