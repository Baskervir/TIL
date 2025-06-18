# 스프링 JDBC: 데이터베이스 접근 정리

## 스프링 JDBC란?
+ JDBC(Java Database Connectivity)를 **추상화**한  스프링의 모듈
+ 반복되는 try-catch, 리소스 해제 코드 등을 제거해 **간결하고 예외 안전한 DB 코드** 작성 가능
+ DAO 계층 구현 시 사용되며, JPA나 MyBatis보다 **낮은 수준의 제어**를 제공

---

## 주요 클래스 및 인터페이스

| 클래스 / 인터페이스 | 설명 |
| --- | --- |
| `JdbcTemplate` | 핵심 클래스.<br>SQL 실행, 쿼리 결과 처리 등 대부분 기능 포함 |
| `DataSource` | DB 연결 정보를 담는 인터페이스.<br>Connection을 생성할 때 사용 |
| `RowMapper<T>` | ResultSet을 객체로 매핑할 때 사용 |
| `NameParameterJdbcTemplate` | 이름 기반 파라미터(`:name`)를 사용할 수 있는 템플릿 |

## JdbcTemplate 기본 사용 방법
```java
public class UserRepository {
    private final JdbcTemplate jdbcTemplate;

    public UserRepository(DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource);
    }

    public User findById(Long id) {
        String sql = "SELECT * FROM users WHERE id = ?";
        return jdbcTemplate.queryForObject(sql, userRowMapper(), id);
    }

    private RowMapper<User> userRowMapper() {
        return (rs, rowNum) -> new User(
            rs.getLong("id"),
            rs.getString("name"),
            rs.getInt("age")
        );
    }
}
```

## INSERT, UPDATE, DELETE 예시
```java
//INSERT
jdbcTemplate.update("INSERT INTO users(name, age) VALUES (?, ?)", name, age);

// UPDATE
jdbcTemplate.update("UPDATE users SET age = ? WHERE id = ?", age, id);

// DELETE
jdbcTemplate.update("DELETE FROM users WHERE id = ?", id);
```

## 트랜잭션 처리
```java
@Service
@Transactional
public class UserService {
    // 모든 public 메서드는 트랜잭션 처리된다
}
```

## Tip
+ `JdbcTemplate`은 SQL 작성이 많으므로 SQL 관리 전략이 필요하다
  + SQL Mapper 분리
+ 단순한 프로젝트에서는 유용하지만, 복잡한 객체 매핑이나 연관관계 처리에는 JPA가 더 적합하다
+ 커넥션 풀은 HikariCP 사용 권장
  + `spring.datasource.hikari.*`
+ 빈 등록 없이 사용하고 싶다면 `@Autowired JdbcTemplate`으로 바로 주입 가능