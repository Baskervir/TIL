# CQRS 도입 및 Command 구조

## 목차
1. CQRS 도입이 필요한 조건과 판단 기준
2. 게시판 시스템을 예제로 한 Command 흐름 구조 설계
3. DDD와 CQRS의 관점 차이

---

## CQRS는 언제 도입해야 하는가?

### 도입 판단 기준

| 항목           | CQRS 도입 기준 |
|----------------|----------------|
| 시스템 복잡도   | 도메인이 복잡하고 비즈니스 규칙이 많을수록 유리 |
| 트래픽 비대칭  | 조회 트래픽이 압도적으로 많을 경우, 읽기 DB 또는 캐시 최적화를 분리하기 위해 도입 |
| 성능 요구      | 실시간 피드, 검색 등 특정 API에 극한 성능이 요구될 때 |
| 단점           | 개발 복잡도 증가, 도입 학습 비용, 테스트 설계 부담 등 고려 필요 |

---

## 📁 구조 예시 (기본 폴더 구성)

```
post/
├── command/
│   ├── PostCommandController.java
│   ├── PostCommandService.java
│   ├── command/
│   │   ├── CreatePostRequest.java
│   │   └── UpdatePostRequest.java
│   └── domain/
│       ├── Post.java
│       └── PostRepository.java
```

### DDD vs CQRS 관점 차이
- DDD는 "도메인 기준 설계"
- CQRS는 "읽기/쓰기 목적에 따라 구조를 아예 나눈다"

---

## 게시글 작성 기능 흐름 (Command)

### `CreatePostRequest.java`
```java
public class CreatePostRequest {
    private String title;
    private String content;
    private String author;
    // Getter, Setter ...
}
```

### `PostCommandController.java`
```java
@PostMapping
public Long createPost(@RequestBody CreatePostRequest request) {
    return postCommandService.create(request);
}
```

### `PostCommandService.java`
```java
public Long create(CreatePostRequest request) {
    Post post = new Post(
        request.getTitle(),
        request.getContent(),
        request.getAuthor(),
        LocalDateTime.now()
    );
    return postRepository.save(post).getId();
}
```

### `Post.java`
```java
@Entity
public class Post {
    @Id @GeneratedValue
    private Long id;
    private String title;
    private String content;
    private String author;
    private LocalDateTime createdAt;
    // 생성자, Getter
}
```

### `PostRepository.java`
```java
public interface PostRepository extends JpaRepository<Post, Long> {
}
```

---

## Command vs Query 구조 차이

| 항목           | Command                         | Query                                |
|----------------|----------------------------------|--------------------------------------|
| 모델           | `Post`                          | `PostListDto`, `PostDetailDto` |
| 서비스 목적    | 유효성 검증 + 상태 변경        | 빠른 응답 + 화면 맞춤 조회 |
| 트랜잭션 설정 | `@Transactional`                | `@Transactional(readOnly = true)` |
| DB 설계 관점   | 정합성 중심, 트랜잭션 보장 필요 | Projection, Join, ReadReplica 등 고려 |
