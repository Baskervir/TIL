# DDL Data Definition Language
+ 테이블, 인덱스, 스키마 와 같은 데이터베이스 구조를 `정의`, `변경` 및 `삭제`하는데 사용할 수 있는 SQL 명령

### DDL 명령
| 명령       | 설명 | 예시 |
|----------| --- | --- |
| CREATE   | 데이터베이스 또는 해당 객체를 생성 | CREATE TABLE table_name... |
| DROP     | 데이터베이스에서 객체 삭제 | DROP TABLE table_name; |
| ALTER    | 데이터베이스 구조를 변경 | ALTER TABLE table_name ADD COLUMN column_name... |
| TRUNCATE | 테이블에서 모든 레코드 제거 | TRUNCATE TABLE table_name; |
| COMMENT  | 주석 추가 | COMMENT 'comment_text' ON TABLE table_name; |
| RENAME   | 객체의 이름 변경 | RENAME TABLE old_table_name TO new_table_name; |