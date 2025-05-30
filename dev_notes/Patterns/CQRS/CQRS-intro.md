# About CQRS
+ `CQRS`는 **아키텍처 레벨에서의 책임 분리**이다

```declarative
**아키텍처**
시스템을 구성하는 요소들이 어떤 방식으로 배치되고, 서로 어떤 방식으로<br>소통하는지를 정한 설계의 큰 틀
```
##### 아키텍쳐 요약
| 항목 | 설명 |
| --- | --- |
| 개념 | 시스템의 **기본 설계 틀**<br>어떻게 나누고, 어떻게 연결할 것인가 |
| 목적 | 복잡한 시스템을 **명확하게** 나누고,<br>**효율적**으로 **협력**하게 만들기 위함 |
| 예시 | 계층형 구조, 이벤트 기반 구조, CQRS, 클린 아키텍쳐 등 |
---
## 사전 이해 필요한 핵심 항목 목록
| 항목                                      | 설명 |
|-----------------------------------------| --- |
| 1. CRUD 기본 구조                           | Create, Read, Update, Delete<br>동작이 한 서비스/리포지토리에<br>섞여 있는 구조 |
| 2. 계층형 아키텍쳐                             | Controller -> Service -> Repository<br>흐름과 그 책임 분리 |
| 3. 명령 vs 조회 개념 분리<br>(Command vs Query) | "변경하는 요청" 과 "읽기만 <br> 하는 요청"의 목적 차이 |
| 4. 동기 vs 비동기<br>처리 개념                   | 명령은 느려도 가능?<br>조회는 빠르게 돼야?<br>함의 개념적 전제 |
| 5. Event 기반 설계 이해<br> 기초수준 | 명령 -> 처리 -> 조회 모델<br>갱신 식의 흐름 이해 필요 |