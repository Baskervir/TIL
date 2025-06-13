# REST - Representational State Transfer
+ 월드 와이드 웹과 같은 분산 하이퍼미디어 시스템을 위한 소프트웨어 아키텍처의 한 형식이다
+ 네트워크 아키텍처 원리의 모음이다
  + `네트워크 아키텍처 원리` : 자원을 정의하고 자원에 대한 주소를 지정하는 방법 전반을 일컫는다
+ HTTP나 WWW가 아닌 소프트웨어 시스템을 설계하는 것도 가능하다
+ XML과 HTTP인터페이스를 이용해 설계하는 것도 가능하다

## REST 원리를 따르는 시스템은 종종 RESTful이란 용어로 지칭된다

---

## REST 아키텍처에 적용되는 6가지 제한 조건
+ 인터페이스 일관성 : 이로간적인 인터페이스로 분리되어야 한다
+ Stateless : 각 요청 간 클라이언트의 콘텍스트가 서버에 저장되어서는 안된다
+ Cacheable : WWW에서와 같이 클라이언트는 응답을 캐싱할 수 있어야 한다
  + 잘 관리되는 캐싱은 클라이언트-서버 간 상호작용을 부분적 or 완전하게 제거하여 scalability와 성능을 향상시킨다
+ Layered System : 클라이언트는 대상 서버에 직접 연결인지 or 중간 서버를 경유하는지 알 수 없다
  + 중간 서버는 `로드 밸런싱` 기능이나 `공유 캐시` 기능을 제공 > 시스템 규모 확장성을 향상시키는데 유용
+ Code on demand (optional) : Java 또는 JS 의 제공을 통해 서버가 클라이언트가 실행시킬 수 있는 로직을 전송 > 기능확장 가능
+ 클라이언트-서버 구조 : 아키텍처를 단순화시키고 작은 단위로 분리(decouple)함으로써 클라이언트-서버의 각 파트가 독립적으로 개선될 수 있도록 한다

## REST 인터페이스의 원칙에 대한 가이드
+ 자원의 식별 : 특정 자원을 지칭하는 메시지와 메타데이터 존재 > 서버 상의 해당 자원 변경 및 삭제 가능
+ 메시지 : 자신을 처리하는 방식에 대한 충분한 정보 포함해야 한다
  + MIME type과 같은 미디어 타입 전달 시 > 어떤 parser를 이용해야 하는지에 대한 정보도 포함해야 한다

## REST의 주요 목표
+ scalability of component interactions
+ Generality of interfaces
+ Independent deployment of components
+ Intermediary components to reduce latency, enforce security and encapsulate legacy systems