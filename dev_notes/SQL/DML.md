# DML Data Manipulation Language
+ 데이터 조작 언어

### DML 명령
| 명령 | 설명                        | 예시 |
| --- |---------------------------| --- |
| INSERT | 데이터 삽입                    | INSERT INTO table_name (c1, c2, ...) VALUES (v1, v2, ...); |
| UPDATE | 기존 데이터 업데이트               | UPDATE table_name SET c1 = v1, c2 = v2 WHERE condition; |
| DELETE | 테이블에서 레코드 삭제              | DELETE FROM table_name WHERE condition; |
| LOCK | 테이블 제어 동시성                | LOCK TABLE table_name IN lock_mode; |
| CALL | PL/SQL 또는 JAVA 하위 프로그램 호출 | CALL procedure_name(arguments); |
| EXPLAIN PLAN | 데이터 접근 경로 설명 | EXPLAIN PLAN FOR SELECT * FROM table_name; |