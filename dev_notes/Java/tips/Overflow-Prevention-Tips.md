# 오버플로우 방지 팁 모음집 (Java 기준)
---
1. 연산 전에 타입 캐스팅은 필수다
+ int * int = int 결과도 무조건 int
+ 계산 전에 하나라도 long으로 바꾸면 전체가 long으로 계산됨
```declarative
int x = 10_000_000;
int i = 1000;

// ❌ 오버플로우 발생
long result = x * i;  // int * int 계산 → 결과도 int → 잘림

// ✅ 안전한 방식
long result = (long)x * i;
```

2. 반복문 안의 곱셈도 조심하자
```declarative
for (int i = 0; i < n; i++) {
result[i] = x * (i + 1);  // ❌ x * (i+1)에서 오버플로우 가능
result[i] = (long)x * (i + 1);  // ✅ 먼저 long으로 캐스팅
}
```

3. 배열 크기 계산 시에도 long으로
```declarative
int n = 500_000;
int size = n * 4;  // ❌ 위험: 크기 초과 가능

long size = (long)n * 4;  // ✅ 안전한 크기 계산
```

4. 누적합은 int보다 long이 기본
```declarative
long sum = 0;
for (int num : arr) {
sum += num;  // ✅ int 배열이라도 누적은 long으로
}
```

5. 조건문 비교에도 long 리터럴 주의
```declarative
int value = ...;

if (value > 1000000000) { ... }  // ❌ 경우에 따라 오버플로우 판단 실패
if ((long)value > 1_000_000_000L) { ... }  // ✅ 비교 대상도 long으로
```

6. Math.pow 사용 시 double → long 변환은 주의
```declarative
long power = (long)Math.pow(10, 9);  // ✅ 정밀도 손실 가능성 고려
```

7. 초대형 정수라면 BigInteger로 가자
```declarative
import java.math.BigInteger;

BigInteger result = BigInteger.ONE;
for (int i = 1; i <= 100; i++) {
result = result.multiply(BigInteger.valueOf(i));
}
```
---
## 요약 슬로건
```
"타입이 곧 안전이다."
곱셈 전 타입 점검은 습관처럼
int * int = int! → long 결과 필요하면 캐스팅하자
오버플로우는 논리적 실수보다 더 무섭다
```
g