# 라이브니스 락이란?
+ 락이 걸린 것은 아닌데 시스템이 진전되지 못하는 상황
  + 무한 대기
  + 우선순위 역전
  + busy waiting

## 의심할만한 코드 패턴
+ 무한 루프에서 조건이 바뀌지 않는다
  + `while(flag)`또는`while(queue.isEmpty())`같은 곳에서 flag나 queue 상태가 바뀌지 않으면 진전 없다
+ synchronized 블록 중 긴 작업
  + 락을 잡은 채로 네트워크 호출, 디스크 작업, sleep 등은 다른 쓰레드 진행으 막고 우선순위 역전 등 발생
+ notify를 하지 않는다
  + wait 상태에 있는 쓰레드가 깨어나지 않는다
+ busy waiting
  + `while(!ready);`같은 적극적 대기를 하며 CPU 낭비와 함계 다른 작업을 막는다
+ thread starvation
  + 특정 조건(우선순위 낮은 쓰레드, 공유 자원 부족)으로 쓰레드가 계속 실행되지 못한다

---

## 예시 코드 분석 (가정 예시)
### `문제점`: `notify()`또는 `notifyAll()`호출이 없으면 영원히 깨어나지 않는다 -> **라이브니스 락** 발생
```java
synchronized(lock) {
    while(!dataReady) {
// 다른 쓰레드가 dataReady = true; 를 해주지 않으면 영원히 대기
        lock.wait();
    }
process(data);
}
```
### 해결 방법
```java
synchronized(lock) {
    while(!dataReady) {
        lock.wait();
    }
    process(data);
}

// 다른 쓰레드
synchronized(lock) {
    dataReady = true;
    lock.notifyAll(); // 또는 notify()
}
```

### `문제점`: Thread A는 빨리 실행돼야 한다, Thread B가 lock을 가지고 오래 지속
### Thread A는 대기 상태 -> 우선순위 역전 = 라이브니스 락
```java
Thread A: synchronized(lock) { // 높은 우선순위
    doWork();
}

Thread B: synchronized(lock) { // 낮은 우선순위
    // 매우 긴 작업
}
```
### 해결 방법
+ 우선순위 상속(priority inheritance) 정책 적용
  + JVM 수준에서는 제한적이다
+ 락의 범위를 최소화
  + 긴 작업은 락 바깥으로 이동
+ 동시성 제어 구조체(ReentrantLock with fairness)로 개선