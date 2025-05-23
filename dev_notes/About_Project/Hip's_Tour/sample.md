# 쿠버네티스란?
+ 컨테이너를 자동으로 배포하고 관리해주는 오케스트레이션 도구
+ 컨테이너를 다수 운영할 때 생기는 문제를 자동으로 처리해주는 운영 관리자

## 왜 필요한가?
```
Docker로 만든 컨테이너가 있다.
한두 개는 직접 띄우가 종료하고 모니터링이 가능하다
수십 개, 수백 개 일 경우에는?
```
+ 컨테이너 죽었을 때 재시작해야 한다
+ 트래픽이 몰리면 컨테이너 수 늘려야 한다
+ 무중단 배포도 해야 한다
+ 서버 장애 시 다른 서버로 옮겨야 한다
```
-> 이걸 사람이 다 하면 비효율적이다
-> Kubernetes
```

## 구성요소 정리
| 구성 요소 | 설명 |
| --- | --- |
| Pod | 컨테이너 1개 이상을 묶는 단위 |
| Node | 실제 컨테이너가 실행되는 서버 |
| Cluster | 여러 Node가 모인 것 |
| Deployment | 배포 전략 및 상태관리 정의 |
| Service | 외부 또는 내부 접근 라우팅 |
| Master (Control Plane) | 클러스터 전체를 관리하는 두뇌 |

---

## 컨테이너란?
+ 실행에 필요한 코드 + 라이브러리 + 설정을 묶은 독립 실행 환경
+ 운영체제 위에서 격리되어 돌아가는 가벼운 가상 머신

### 컨테이너 vs 가상머신
| 구분 | 컨테이너 | 가상머신 |
| --- | --- | --- |
| OS 포함 여부 | X (호스트 OS 공유) | O (게스트 OS 포함) |
| 실행 속도 | 빠름 | 느림 |
| 용량 | 작음 (수 MB) | 큼 (수 GB) |
| 실행 목적 | 프로세스 단위 실행 | 전체 OS 가상화 |
+ 컨테이너는 더 가볍고 빠르며, 앱 실행에 최적화된 기술

### 컨테이너를 '운영한다'는 의미
컨테이너들이 잘 실행되도록, 문제 발생 시 자동 복구, 필요할 때 늘리고 줄이는 것까지 모든 흐름을 자동으로 관리한다
+ 컨테이너가 죽으면 → 자동 재시작
+ 트래픽이 늘어나면 → 컨테이너 개수 늘림
+ 코드를 바꾸면 → 무중단 배포
+ 서버가 죽으면 → 다른 서버로 자동 이동
+ 요청이 많으면 → 로드밸런싱 자동 분산
`사람이 일일이 수동으로 안 해도 되게 만드는 쿠버네티스`

---

## Docker란?
+ 애플리케이션을 컨테이너 형태로 만들고 실행하기 위한 플랫폼/도구

---

❓Q. 이 정도까지 자동으로 해주려면 쿠버네티스는 컨테이너를 직접 실행할까?
아니면 컨테이너를 묶어서 뭔가로 관리할까?

✅ 컨테이너 묶음 단위(Pod 단위)
+ 쿠버네티스는 컨테이너를 직접 관리하지 않는다
+ 대신 여러 컨테이너를 하나로 묶은 Pod라는 단위로 관리한다
+ Pod 안에는 보통 하나의 컨테이너가 있지만, 필요하면 여러 개도 들어갈 수 있다 (Ex. 로그 수집용 사이드카 컨테이너 등)
`쿠버네티스는 컨테이너를 관리하지 않는다. Pod를 관리한다`

❓Q. Pod는 왜 필요할까?
왜 그냥 컨테이너를 직접 안 쓰고 굳이 Pod라는 껍데기(?)를 만들었을까?

✅ 왜 컨테이너를 직접 안 쓰고 Pod를 쓰는가?
1. 논리적 묶음
   + 서로 강하게 연결된 컨테이너들을 하나로 묶어야 할 때가 있다
   + 예: 하나는 앱 서버, 다른 하나는 로그 수집기 → 서로 같은 IP 공간에서 통신 필요
   + Pod 안에 넣으면 같은 네트워크, 같은 볼륨을 공유한다
2. 쿠버네티스의 기본 단위
   + Kubernetes는 컨테이너가 아니라 Pod를 기준으로 스케일링, 배포, 복구를 한다
3. 표준화된 제어
   + Pod 단위로 관리하면 모든 컨테이너 관리 로직을 통일시킬 수 있다
   + 마치 자바에서 패키지 단위로 모듈을 정리하는 것과 같다
`Pod는 쿠버네티스에서 컨테이너를 관리하기 위한 최소 단위이자, 같은 목적을 가진 컨테이너들을 묶는 논리적 단위`

❓Q. Pod가 여러 개라면 어디에 배치될까?
→ 쿠버네티스에서 Pod들이 실제로 실행되는 물리적 장소는 뭐라고 할까?

✅ Node 위에서 실행
Node는 쿠버네티스 클러스터에서 실제 컨테이너(Pod)가 돌아가는 물리 서버 또는 가상 머신이다

### 구조 비유
```
클러스터 (전체 시스템)
 └─ Node (서버)
     └─ Pod (묶음)
         └─ 컨테이너 (실행 단위)
```

❓Q. 여러 Node가 있다고 할 때, Pod를 어디에 어떻게 배치할지 누가 결정할까?

✅ 쿠버네티스 = 자동 배치 관리자
+ Node: 실제 Pod가 실행되는 서버
+ Pod: 컨테이너들을 묶은 단위
+ Cluster: 여러 Node를 하나로 묶은 전체 시스템
+ 쿠버네티스: Pod가 어느 Node에 들어갈지 자동 결정 (스케줄링)

❓Q. 쿠버네티스가 Pod를 배포하고 관리하기 위해 사용하는 설정 파일의 형식은?
+ `.zip`, `.json`, `.xml`, `.yaml`

✅ `.yaml`

### YAML이란?
+ YAML Ain't Markup Language
+ 사람이 읽기 쉬운 설정 파일 형식
+ 들여쓰기 기반으로 구조화된 데이터 표현 가능
```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
    - name: my-container
      image: nginx
```

❓Q. 위 YAML 파일에서 "nginx"는 어떤 의미일까?

✅ `nginx`는 실행할 컨테이너 이미지 이름
즉, "nginx라는 컨테이너를 띄워줘" 라는 뜻
`Docker Hub에서 nginx이미지를 찾아서 Pod안에서 실행`하라는 의미

### nginx란?
+ 웹서버 소프트웨어
+ 자주 사용되는 컨테이너 이미지 중 하나

---

# 정리
+ 컨테이너 개념
  + 컨테이너란?
  → 코드 + 실행 환경을 묶은 독립 실행 단위
  + 가상머신과의 차이
  → 컨테이너는 OS를 공유, 훨씬 가볍고 빠름

+ Docker
  + 도커는 컨테이너를 만들고 실행하는 도구
  + Docker는 컨테이너 런타임 중 하나일 뿐, 쿠버네티스에 필수는 아님

+ 쿠버네티스(Kubernetes)
  + 컨테이너를 자동으로 배포/운영/복구/확장하는 오케스트레이션 시스템
  + 도커 없이도 동작 가능 (containerd, CRI-O 등으로 대체)

+ 쿠버네티스 구조
  + Cluster: 전체 시스템
  + Node: Pod가 실행되는 서버 (가상/물리 머신)
  + Pod: 쿠버네티스에서 컨테이너를 담는 최소 실행 단위
  + Container: 실제로 앱이 돌아가는 단위

+ Pod 개념
  + Pod는 논리적으로 연결된 컨테이너들을 하나로 묶은 단위
  + 하나의 Pod는 IP, 볼륨, 네트워크 등을 공유

+ 스케줄링
  + Pod를 어떤 Node에 배치할지는 쿠버네티스가 자동으로 결정

+ YAML 파일
  + 쿠버네티스의 설정은 .yaml 형식의 선언형 파일로 정의
  + image: nginx → nginx 컨테이너를 실행하라는 의미

+ 용어 계층 정리
  + 클러스터 > 노드 > Pod > 컨테이너
  → 컨테이너는 Pod 안에, Pod는 Node 위에, Node는 클러스터에 포함