# TCP
+ 전송 계층의 신뢰성 있는 연결 지향형 프로토콜
+ 순서 보장, 패킷 손실 복구, 흐름 제어, 혼잡 제어, 오류 검출

---

## TCP 연결의 구조
+ Client <-> Server 구조
+ 3-way handshake로 시작하고, 4-way handshake로 종료

## TCP 연결 흐름
### 3-Way Handshake (연결 설정)
| 단계 | Client | Server | 설명 |
| --- | --- | --- | --- |
| 1 | SYN 보내기 | 대기 상태 | Client가 연결 요청<br>(SYN 플래그) |
| 2 | SYN + ACK 응답 | SYN 수신 | Server가 요청 수락<br>자기 SYN도 같이 보낸다 |
| 3 | ACK 보내기 | SYN + ACK 수신 | Client가 확인 응답 전송<br>연결 완성 |

### 데이터 송수신
+ TCP는 스트림 기반 -> 데이터는 바이트 단위로 전달된다
+ 패킷 순서 보장 (시퀀스 번호 사용)
+ 손실 시 재전송

### 4-Way Handshake (연결 종료)
| 단계        | 설명 |
|-----------| --- |
| 1. FIN -> | Client가 종료 요청 |
| 2. <- ACK | Server가 수락 |
| 3. <- FIN | Server도 종료 요청 |
| 4, ACK -> | Client가 수락 후 연결 종료 |

## TCP가 신뢰성을 보장하는 방법
+ 순서 보장 : Sequence Number 사용
+ 손실 복구 : ACK(응답) 없으면 재전송
+ 오류 감지 : Checksum으로 무결성 확인
+ 흐름 제어 : Window Size로 송수신 속도 조절
+ 혼잡 제어 : 네트워크 혼잡 시 전송률 조절

## TCP 패킷 구조
| 필드 | 설명 |
| --- | --- |
| Source Port / Destination Port | 출발지와 목적지 포트 |
| Sequence Number | 데이터 순서 지정 |
| Acknowledgment Number | 받은 데이터 확인 응답 |
| Flags | SYN, ACK, FIN 등 |
| Window Size | 수신 가능한 데이터 양 |
| Checksum | 오류 검출 |