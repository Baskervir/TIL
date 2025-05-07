# 엔티티 매핑
+ 객체와 테이블 매핑 : `@Entity`, `@Table`
+ 필드와 컬럼 매핑 : `@Column`
+ 기본 키 매핑 : `@Id`
+ 연관관계 매핑 : `@ManyToOne`, `@JoinColumn`

---

## @Entity
+ @Entity가 붙은 클래스는 JPA가 관리한다
+ JPA를 사용해서 테이블과 매핑할 클래스에 필수
+ 주의 사항
  + 기본 생성자 필수
  + final 클래스, enum, interface, inner 클래스 사용 불가
  + 데이터베이스에 저장할 필드에 final 사용 X

---

## 데이터베이스 스키마 자동 생성
+ DDL을 애플리케이션 실행 시점에 자동 생성
+ 테이블 중심 -> 객체 중심
+ 데이터베이스 방언을 활용해서 데이텁이스에 맞는 적절한 DDL 생성
+ 이렇게 생성된 DDL은 장비에서만 사용
+ 생성된 DDL은 운영서버에서는 사용하지 않거나, 적절히 다듬은 후 사용

### 데이터베이스 스키마 자동 생성 - 주의
+ 운영 장비에는 절대 create, create-drop, update 사용하면 안된다
+ 개발 초기 단계는 create 또는 update
+ 테스트 서버는 update 또는 validate
+ 스테이징과 운영 서버는 validate 또는 none

---

## DDL 생성 기능
+ 제약조건 추가 : 필수 조건, 글자 수 조건
  + @Column(nullable = false, length = 10)
+ 유니크 제약조건 추가
  + @Table(uniqueConstraints = {@UniqueConstraint(name = "NAME_AGE_UNIQUE", columnNames = {"NAME", "AGE})})
+ DDL 생성 기능은 DDL을 자동 생성할 때문 사용된다
  + JPA의 실행 로직에는 영향을 주지 않는다

---

## 매핑 어노테이션 정리
| 어노테이션 | 설명 |
| --- | --- |
| @Column | 컬럼 매핑 |
| @Temporal | 날짜 타입 매핑 |
| @Enumerated | enum 타입 매핑 |
| @Lob | BLOB, CLOB 매핑 |
| @Transient | 특정 필드를 컬럼에 매핑하지 않는다 |

### @Column
| 속성 | 설명 | 기본값 |
| --- | --- | --- |
| name | 필드와 매핑할 테이블의 컬럼 이름 | 객체의 필드 이름 |
| insertable,<br>updatable | 등록, 변경 가능 여부 | TRUE |
| nullable(DDL) | null 값의 허용 여부를 설정한다.<br>false로 설정하면 DDL 생성 시에<br>not null 제약조건이 붙는다 | |
| unique(DDL) | @Table의 uniqueConstraints와 같지만<br>한 컬럼에 간단히 유니크 제약조건을<br>걸 때 사용한다 | |
| columnDefinition(DDL) | 데이터베이스 컬럼 정보를 직접 줄 수 있다<br>ex) varchar(100) default 'EMPTY' | 필드의 자바 타입과<br>방언 정보를 사용 |
| length(DDL) | 문자 길이 제약조건, String 타입에만 사용한다 | 255 |

### @Enumerated
+ EnumType.ORDINAL : enum 순서를 데이터베이스에 저장
+ EnumType.STRING : enum 이름을 데이터베이스에 저장
`주의! ORDINAL 사용 금지`

### @Temporal
+ 날짜 타입을 매핑할 때 사용
+ `LocalDate`, `LocalDateTime`을 사용할 때는 생략 가능

### @Lob
+ 데이터베이스 `BLOB`, `CLOB` 타입과 매핑
+ @Lob에는 지정할 수 있는 속성이 없다
+ 매핑하는 필드 타입이 문자면 CLOB
+ 나머지는 BLOB

### @Transient
+ 필드 매핑 X
+ 데이터베이스에 저장 X, 조회 X
+ 주로 메모리 상에서만 임시로 값을 보관하고 싶을 때 사용

---

### @TableGenerator - 속성
| 속성              | 설명 | 기본값 |
|-----------------| --- | --- |
| name            | 식별자 생성기 이름 | 필수 |
| table           | 키생성 테이블명 | hibernate_sequences |
| pkColumnName    | 시퀀스 컬럼명 | sequence_name |
| valueColumnName | 시퀀스 값 컬럼명 | next_val |
| pkColumnValue | 키로 사용할 값 이름 | 엔티티 이름 |
| initialValue | 초기 값, 마지막으로 생성된 값이 기준이다 | 0 |
| allocationSize | 시퀀스 한 번 호출에 증가하는 수 <br>(성능 촤적화에 사용된다) | 50 |
| catalog, schema | 데이터베이스 catalog, schema 이름 | |
| uniqueConstraints(DDL) | 유니크 제약 조건을 지정할 수 있다 | | 

---

### IDENTITY 전략 - 특징
+ 기본 키 생성을 데이터베이스에 위임
+ JPA는 트랜잭션 커밋 시점에 INSERT SQL 실행
+ AUTO_INCREMENT는 데이터베이스에 INSERT SQL을 실행 한 이후에 ID값을 알 수 있다
+ IDENTITY 전략은 em.persist() 시점에 즉시 INSERT SQL을 실행하고 데이터베이스에서 식별자를 조회한다

---

### 권장하는 식별자 전략
+ 기본 키 제약 조건 : null 아님, 유일, 변하면 안된다
+ 미래까지 이 조건을 만족하는 자연키는 찾기 어렵다
+ 대리키(대체키)를 사용하자
+ `권장`: Long형 + 대체키 + 키 생성전략 사용

