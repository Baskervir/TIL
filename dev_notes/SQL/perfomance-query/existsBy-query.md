# `existsBy...`쿼리 성능 고려 사항
1. 인덱스 설계
+ 문제
  + `placeName`과 `address1`컬럼을 동시에 WHERE 절로 조회
  + 해당 조합에 대한 복합 인덱스가 없으면 풀 테이블 스캔 발생
+ 대응
  + `CREATE INDEX idx_place_name_address1 ON place(place_name, address1);`
  + `placeName`, `address1`은 단독 인덱스보다 조합 인덱스가 효율적
2. 데이터 카디널리티
3. 뭐리 자체의 의미
4. 조회 번호
5. 데이터 정규화 및 컬럼 조건