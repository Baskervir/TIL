# 배열
### 개념
+ 배열(Array) : 같은 타입의 데이터를 고정된 크기로 연속 저장하는 자료구조
+ 특징
  + 타입 동일
  + 크기 고정
  + 인덱스 (0부터 시작)
+ 메모리 : 메모리상 연속 공간 확보
```declarative
int[] arr = {1, 2, 3, 4, 5};
System.out.println(arr[2]); // 3
```

### 동작 흐름 (추상화)
+ 배열 이름 = 첫 번째 요소의 참조값
+ 인덱스 계산 = (기준 주소) + (요소 크기 x 인덱스)
+ 접근 빠름 (O(1))

---

# 큐
### 개념
+ 큐(Queue) : 선입선출 구조
+ 먼저 들어온 데이터가 먼저 나간다
+ 배열과 달리 입력과 출력 위치가 다르다

### 기초 연산
| 연산 | 설명 |
| --- | --- |
| enqueue | 데이터 추가 (뒤로) |
| dequeue | 데이터 제거 (앞에서) |
| peek | 가장 앞 데이터 조회 (제거하지 않음) |

### 큐 흐름 요약
```declarative
[1]  [2]  [3]
 ↑             ↑
출구           입구
```
```declarative
import java.util.LinkedLidt;
import java.util.Queue;

Queue<Integer> queue = new LinkedList<>();
queue.offer(1);
queue.offer(2);
queue.offer(3);

System.out.println(queue.poll()); // 1 출력
System.out.println(queue.peek()); // 2 출력 (삭제X)
```

---

# 스택
### 개념
+ 스택(Stack) : 후입선출 구조
+ 나중에 들어온 데이터가 먼저 나간다

### 기초 연산
| 연산 | 설명 |
| --- | --- |
| push | 데이터 추가 (맨 위로) |
| pop | 데이터 제거 (맨 위에서) |
| peek | 가장 위 데이터 조회 (제거하지 않음) |

### 스택 흐름 요약
```declarative
맨 위 ->  [3]
         [2]
         [1]  <- 맨 아래
```
```declarative
import java.util.Stack;

Stack<Integer> stack = new Stack<>();
stack.push(1);
stack.push(2);
stack.push(3);

System.out.println(stack.pop()); // 3 출력
System.out.println(stack.peek()); // 2 출력 (삭제X)
```

---

# 덱
### 개념
+ 덱(Deque, Double-Ended Queue) : 양쪽 끝 모두에서 삽입/삭제 가능한 자료구조
+ *큐*와 *스택*을 모두 일반화한 구조

### 기초 연산
| 연산       | 설명 |
|----------| --- |
| addFirst | 앞에 추가 |
| addLast | 뒤에 추가 |
| removeFirst | 앞에서 제거 |
| removeLast | 뒤에서 제거 |
| peekFirst | 앞 조회 |
| peekLast | 뒤 조회 |

### 덱 흐름 요약
```declarative
입구 ↔ [1] [2] [3] ↔ 출구
```
```declarative
import java.util.Deque;
import java.util.LinkedList;

Deque<Integer> deque = new LinkedList<>();
deque.addFirst(1);
deque.addLast(2);
deque.addLast(3);

System.out.println(deque.removeFirst()); // 1 출력
System.out.println(deque.removeLast()); // 3 출력
```

---

# 정리
### 비교
| 비교항목   | 배열 | 큐 | 스택 | 덱 |
|--------| --- | --- | --- | --- |
| 데이터 접근 | 인덱스 직접 접근 | 선입선출 | 후입선출 | 양쪽 접근 가능 |
| 삽입/삭제 | 임의 위치 삽입/삭제 가능 | 안쪽 입구/출구 | 한쪽 입구/출구 | 양쪽 모두 삽입/삭제 가능 |
| 사용 예 | 점수 목록, 통계 | 대기열, BFS 탐색 | 실행취소, DFS 탐색 | 양방향 탐색, 슬라이딩 윈도우 |

