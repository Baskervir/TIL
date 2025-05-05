# LIFO
+ 후입선출
+ Stack

## Stack 객체 생성
```declarative
Stack<E> stack = new Stack<E>();
Stack<E> stack = new Stack<>();
```

## Stack 클래스의 주요 메서드
| 리턴 타입 | 메서드 | 설명 |
| --- | --- | --- |
| E | push(E item) | 주어진 객체를 스택에 넣는다 |
| E | pop() | 스택의 맨 위 객체를 빼낸다 |

---


# FIFO
+ 선입선출
+ Queue

## Queue 객체 생성
+ Queue 인터페이스를 구현한 대표적인 클래스는 LinkedList이다
+ LinkedList 객체를 Queue 인터페이스 변수에 대입한다
```declarative
Queue<E> queue = new LinkedList<E>();
Queue<E> queue = new LinkedList<>();
```
