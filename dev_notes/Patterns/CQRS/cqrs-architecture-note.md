# CQRS 패턴 정리 및 학습 요약

## 목차
1. [CQRS란?](#cqrs란)
2. [CQRS의 출발점 - CRUD 구조의 한계](#cqrs의-출발점---crud-구조의-한계)
3. [계층형 아키텍처의 의미와 필요성](#계층형-아키텍처의-의미와-필요성)
4. [Command vs Query 분리의 철학](#command-vs-query-분리의-철학)
5. [CQRS의 구조 및 실제 적용 예시](#cqrs의-구조-및-실제-적용-예시)
6. [CQRS의 실질적인 장점](#cqrs의-실질적인-장점)

---

## CQRS란?
+ **Command and Query Responsibility Segregation**
+ 명령(Command)과 조회(Query)의 책임을 분리한다는 설계 패턴
  + 기존 CRUD 구조는 모든 로직이 하나의 모델/서비스에 몰려있다
  + CQRS는 읽기와 쓰기의 목적과 최적화 방식이 다르다는 점에서 시작

---

## CQRS의 출발점 - CRUD 구조의 한계
### 상황 가정
```java
public class UserService {
    public void createUser(...) { ... }
    public User findUser(...) { ... }
    public void updateUser(...) { ... }
    public void deleteUser(...) { ... }
}
```
#### 문제점
+ SRP 위배
+ 유지보수 및 관리 어렵다
+ 테스트/배포/확장이 어렵다
+ 조회 성능을 따로 개선한다? → 전체 구조에 영향을 미친다
  + 조회 최적화를 적용하기 어렵다

---

## 계층형 아키텍처의 의미와 필요성
### 구조
```declarative
Controller → Service → Repository
```

### 책임 분리 이유
+ SRP 기반 → 계층마다 역할이 명확해야 한다
+ 외부 노출 여부 판단 가능 //Ex. Controller가 DTO로 제한
+ 단위 테스트 가능
+ 트랜잭션 및 데이터 일관성 유지

### Controller에서 EntityManager 직접 접근할 경우
+ 계층 구조 붕괴 → 정책이 흩어지고 재사용 어려움
+ 테스트 어려움
+ 비즈니스 로직 위치 불명확
+ 트랜잭션 관리 어려움 → 데이터 일관성 문제 발생

---

## Command vs Query 분리의 철학
### 사례 코드
```java
public User getUserAndUpdateLastLoginTime(Long id) {
    User user = userRepository.findById(id);
    user.setLastLogin(LocalDateTime.now());
    userRepository.save(user);
    return user;
}
```
### 조회일까? 명령일까?
+ 내부적으로 상태를 변경하므로 **명령(Command)**에 해당한다

#### 철학적 분리
| 항목 | Command      | Query |
| --- |--------------| --- |
| 목적 | 상태 변경        | 정보 조회 |
| 부작용 | 있다           | 없다 (읽기 전용) |
| 예시 | 회원가입, 글 수정 등 | 목록 조회, 상세 보기 등 |
| 중요 요소 | 트랜잭션, 유효성 검사 | 빠른 응답, 캐싱, 최적화 |
| 설계 방식 | 별도 도메인 모델 사용 | DTO 기반 단순 구조 사용 |

#### 왜 Command 모델과 Query 모델을 나눠야 할까?
+ 조회는 화면/속도 중심, 명령은 정합성/비즈니스 중심
  + 조회와 명령은 목적이 다르다
+ 같은 모델을 사용하면 둘 중 하나를 최적화할 수 없다
+ 표현 방식과 성능 요구 조건이 다르므로 분리하는 것이 구조적으로 유리

---

## CQRS의 구조 및 실제 적용 예시
### CRUD 통합 방식
```declarative
UserService
├── createUser()
├── getUser()
├── updateUser()
├── deleteUser()
```
+ 모든 책임이 한 서비스에 집중되어 있다

### CQRS 구조
```declarative
UserCommandService         UserQueryService
┌──────────────────┐       ┌──────────────────┐
│ createUser()     │       │ getUserDetails() │
│ updateUser()     │       │ listUsers()      │
└──────────────────┘       └──────────────────┘
        ↓                            ↓
   CommandModel                QueryModel (DTO)
        ↓                            ↓
Write DB (or same DB)         Read DB / Cache
```
#### 실제 API 예시
| HTTP Method | 경로 | 서비스                                  |
| --- | --- |--------------------------------------|
| POST | `/users` | `UserCommandService.createUser(dto)` |
| GET | `/users/{id}` | `UserQueryService.getUserDetails(id)` |                                

---

## CQRS의 실질적인 장점
+ 조회와 명령의 목적과 책임, 최적화 방향이 다르다
  + 같은 모델로 처리하면 구조 변경, 테스트, 성능 튜닝에서 얽히고 충돌
+ Query 측은 캐시/읽기 전용 DB로 최적화 가능
+ Command 측은 도메인 로직과 정합성 중심으로 설계 가능
+ 유지보수, 배포, 스케일아웃 전략까지 유연하게 설계 가능

---

CQRS는 Command와 Query를 명확히 분리하여 시스템의 변경과<br>
조회를 독립적으로 최적화할 수 있게 만든 아키텍쳐 설계 패턴이다
