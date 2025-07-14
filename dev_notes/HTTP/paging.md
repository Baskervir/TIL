# 페이징
+ HTTP에서 페이징 처리는 클라이언트가 서버로부터 대량의 데이터를 한번에 받는 대신, 일정 개수씩 나눠서 요청하고 응답받도록 하는 방식
+ 성능 및 사용자 경험 개선을 위해 필수적이다

### 일반적인 페이징 방식
1. Query Parameter 방식 (가장 보편적)
   + `page`: 몇 번째 페이지인지
   + `size`: 한 페이지에 몇 개의 데이터
> 보통 백엔드에서는 `limit`, `offset`등으로 변환하여 DB 쿼리를 작성한다

2. Cursor 기반 방식 (keyset pagination)
+ `cursor`: 마지막으로 본 데이터의 고유 ID
+ 장점: 페이지 중간 데이터가 추가/삭제 되더라도 일관된 결과 제공
+ 단점: 전체 페이지 수 계산 어려움

---

### Request 예시
```text
GET /api/reviews?placeId=12&page=2&size=10
```

### Response 예시
```text
{
  "status": "OK",
  "message": "리뷰 목록 조회 성공",
  "result": {
    "reviews": [
      {
        "reviewId": 101,
        "content": "좋아요!",
        "createdAt": "2025-07-14T12:00:00"
      },
      ...
    ],
    "pagination": {
      "page": 2,
      "size": 10,
      "totalElements": 56,
      "totalPages": 6,
      "hasNext": true,
      "hasPrevious": true
    }
  }
}
```

### 참고 키워드
| 키워드 | 설명 |
| --- | --- |
| `page` | 요청한 페이지 번호 |
| `size` | 페이지당 아이템 개수 |
| `offset` | (page - 1) * size |
| `limit` | 조회할 최대 개수 |
| `cursor` | keyset pagination에서 기준이 되는 ID |
| `totalPages` | 전체 페이지 수 |
| `totalElements` | 전체 데이터 개수 |
| `hasNext` | 다음 페이지 여부 |
| `hasPrevious` | 이전 페이지 여부 |