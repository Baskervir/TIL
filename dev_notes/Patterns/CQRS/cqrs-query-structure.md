# CQRS Query 흐름 설계 및 최적화 실전 예제

## DTO 반환의 이유
- Entity를 직접 반환하지 않고 DTO로 가공하여 반환하는 이유:
  - 보안성: DB 모델에는 외부에 노출하면 안 되는 내부 상태가 있을 수 있음
  - 성능: 불필요한 Lazy 로딩이나 JOIN 최소화 가능
  - 구조 안정성: API와 DB 간 결합도 낮춤 (유연성↑)

---

## Query 전용 폴더 구조

```
post/
└── query/
    ├── PostQueryController.java
    ├── PostQueryService.java
    ├── dto/
    │   ├── PostDetailDto.java
    │   └── PostListDto.java
    └── domain/
        └── PostQueryRepository.java
```

---

## 게시글 단건 조회 흐름

### `PostDetailDto.java`
```java
public class PostDetailDto {
    private Long id;
    private String title;
    private String content;
    private String author;
    private LocalDateTime createdAt;
}
```

### `PostQueryService.java`
```java
public PostDetailDto findPostById(Long id) {
    Post post = postQueryRepository.findById(id)
        .orElseThrow(...);
    return new PostDetailDto(post.getId(), post.getTitle(), ...);
}
```

---

## 게시글 목록 조회 흐름

### `PostListDto.java`
```java
public class PostListDto {
    private Long id;
    private String title;
    private String author;
    private LocalDateTime createdAt;
}
```

### 기본 방식 (Entity 조회 후 DTO 매핑)
```java
public List<PostListDto> findAllPosts() {
    return postQueryRepository.findAll().stream()
        .map(p -> new PostListDto(p.getId(), p.getTitle(), p.getAuthor(), p.getCreatedAt()))
        .collect(Collectors.toList());
}
```

### 최적화 방식 (JPA Projection)
```java
@Query("SELECT new post.query.dto.PostListDto(p.id, p.title, p.author, p.createdAt) FROM Post p")
List<PostListDto> findAllSummary();
```

- Entity를 조회하지 않고 바로 DTO 생성 → **속도 + 메모리 최적화**

---

## DTO 반환의 실질적 이점 요약

| 항목             | 설명 |
|------------------|------|
| 속도             | 필요한 필드만 SELECT (JOIN 최소화) |
| 메모리           | 전체 Entity 불필요, 객체 최소화 |
| Lazy 로딩 회피   | 연관 객체 접근 없이 DTO만 반환 가능 |
| 직렬화 안정성    | 프록시, 순환 참조 등 이슈 제거 |
