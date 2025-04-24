# persistence.xml
## `persistence.xml`을 작성하는 이유?
#### 핵심 역할
+ JPA에서 `Persistence Unit`, 즉 엔티티 관리 환경의 설정을 정의하는 파일

#### 정리
+ 어떤 DB를 사용할지 (JDBC 설정)
+ 어떤 JPA 구현체를 사용할지 (Hibernate 등)
+ 어떤 엔티티 클래스를 관리할지
+ 자동 테이블 생성 여부
+ 트랜잭션 타입 (RESOURCE_LOCAL or JTA)
등을 정의하는 JPA 설정 파일

#### 위치
`src/main/resources/META-INF/persistence.xml`

#### 예제 코드
```declarative
<persistence xmlns="http://jakarta.ee/xml/ns/persistence"
             version="3.0">
  <persistence-unit name="examplePU">
    <class>com.example.Member</class>
    <properties>
      <property name="jakarta.persistence.jdbc.driver" value="org.h2.Driver"/>
      <property name="jakarta.persistence.jdbc.url" value="jdbc:h2:mem:testdb"/>
      <property name="jakarta.persistence.jdbc.user" value="sa"/>
      <property name="jakarta.persistence.jdbc.password" value=""/>

      <property name="jakarta.persistence.schema-generation.database.action" value="create"/>
      <property name="hibernate.show_sql" value="true"/>
    </properties>
  </persistence-unit>
</persistence>
```

---

## persistence.xml의 필요 여부
| 환경 | persistence.xml 필요 여부 |
| --- | --- |
| 순수 JPA(Java SE 기반) | 필수 |
| Spring Boot + JPA | `application.yml` or `.properties` |

#### 정리
+ `persistence.xml`은 JPA 환경 설정용 파일
+ Spring Boot에서는 필요 없음 (`application.yml`로 대체)
+ maven, gradle 사용 여부와 무관하다

---

#### 참고
+ JPA 구현체들은 보통 엔티티 클래스를 자동으로 인식한다
  + 환경에 따라 인식하지 못하기도 한다
+ `persistence.xml`에 다음과 같이 `<class>`를 사용하여 지정한다
```declarative
<persistence-unit name="`...`">
    
    <class>`경로`</class>
    <properties>
    ...
```
