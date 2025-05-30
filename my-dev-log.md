# 🧠 전체 학습 항목 정리
## 🚩 기초 프로그래밍
+ 자바(JAVA)
  + 컬렉션 프레임워크 선택 기준
  + 추상 클래스와 오버라이딩
  + 테스트 코드의 역할과 구조
  + `@Controller`, `@RestController` 차이

+ JavaScript
  + 기본 문법
  + `fetch` 활용과 DOM 조작
  + SPA 방식의 HTML fragment 교체

## 💾 백엔드 구조
+ 계층 분리
  + Controller → Service → UseCase → Repository
  + 각 계층의 책임 구분과 데이터 흐름
+ DTO vs Entity
  + 컨트롤러는 DTO만 알게 설계
  + Entity 직접 노출의 위험성
+ 회원가입, 로그인 설계
  + 이메일 인증 로직 분리
  + 세션 방식 vs JWT 방식 비교
+ 예외 처리 설계
  + 단일 책임 원칙(SRP) 기반의 예외 관리 전략

## 🗃️ 데이터베이스 및 JPA
+ Entity 설계
  + 단방향/양방향 매핑
  + mappedBy의 의미
+ 관계
  + 1:1, 1:N, N:1, N:M의 구조적 목적
+ JPA 주요 개념
  + EntityManager, flush, detach
  + 프록시와 지연 로딩
  + 고급 매핑 전략 (상속, 식별자 관계 등)
+ SQL 개념 정리
  + JOIN의 작동 방식
  + 정규화와 스키마 개념

## 🔧 개발 도구 및 설정
+ Gradle 멀티 모듈 구성
+ Git 브랜치 전략과 충돌 해결
+ H2 데이터베이스 테스트 구성
+ Redis 캐싱 실습 기획

## 📊 알고리즘 & 수학
+ 완전탐색 (브루트 포스)
+ 순열과 조합의 차이
+ 유클리디안 거리, 코사인 유사도
+ 베이지안 추론 (추천 알고리즘 연계)
+ 선형대수학 기초
+ 수학적 개념을 실제 코드에 적용하는 방식

## 🌐 웹 & 네트워크
+ HTTP 구조 및 요청/응답 구조
+ GET/POST 차이
+ 쿠키 vs 세션 vs JWT
+ CSRF 개념과 방어
+ 비동기 처리(AJAX, fetch)
+ 무상태 서버와 스케일 아웃 개념

## 🔐 보안 및 인증
+ OAuth 2.0 흐름 정리
+ SSO 시스템 구성 요소 (IdP, SP)
+ 이메일 인증 URL 설계와 토큰 저장 전략

## 📦 쿠버네티스 및 컨테이너
+ 컨테이너 vs 가상머신
+ Docker의 역할과 한계
+ Kubernetes의 등장 배경과 필요성
+ Pod, Node, Cluster 개념
+ 스케줄러 역할
+ YAML 설정 파일 구조 및 image 의미

## 💡 기획 및 프로젝트 구조 설계
+ 음식점 검색 서비스 포트폴리오 설계
  + SSR 기반, Mustache 템플릿 사용
  + 사용자, 관리자, Root 권한 구분
  + 예약 시스템, 리뷰, 알러지 필터링
+ 감성 기반 SNS 설계 (내향적 성향에 맞춘 힐링 플랫폼)
  + MBTI 기반 유저 추천
  + 폐쇄형 소셜 피드 설계
+ 공공 API를 활용한 관광 정보 프로젝트
  + 카카오 API, 국토부 관광 API 활용

