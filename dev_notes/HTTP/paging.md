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

---

## 페이징 처리 방식
### URL(Query Param)에 담는 방식 - 권장
+ `GET` 방식에서 자주 사용
+ RESTful하고, 캐싱, 북마크, 공유, 디버깅에 유리
+ 브라우저에서 쉽게 확인 가능

#### URL 예시
> GET /api/place/1/review?page=0&size=10 HTTP/1.1

#### 장점
+ 표준적이고 RESTful 스타일에 적합
+ Swagger나 Postman에서도 테스트 편리
+ 클라이언트에서 명확하게 사용 가능

#### 단점
+ 너무 많은 파라미터가 필요할 경우 URL이 길어질 수 있다
+ 복잡한 검색 조건이 많은 경우 구조가 지저분해 질 수 있다


### Request Body에 담는 방식 (비 표준, 일부 사용)
+ 주로 `POST`방섹이서 사용
+ `GET`은 Body를 가지지 않는 것이 HTTP 표준
  + Body를 써도 되는 클라이언트도 있지만, 대부분 서버/프록시에서 무시

#### 요청 예시
```text
POST /api/place/review/list HTTP/1.1
Content-Type: application/json

{
    "placeId": 1,
    "page": 0,
    "size": 10
}
```

#### 장점
+ 파라미터가 많거나, 복합 조건이 많을 때 깔끔하게 처리 가능
+ 보안상 노출되면 안 되는 데이터가 있을 경우 Query 대신 Body로 숨길 수 있다

#### 단점
+ REST 원칙을 위반 (POST 조회 요청)
+ 클라이언트/서버 에서 `GET + body` 지원이 불안정
+ 브라우저 주소창에서 확인이 불가 (디버깅 어려움)