# Scalable Recommendation System Study Intro
+ 서비스 확장을 고려한 추천 시스템 설계를 위한 핵심 개념 및 실습 로드맵
+ 무상태 서버 기반, 싱글 사인온 등 포함합니다

---

## Step 1: 스케일아웃 & 무상태 개념 이해
+ 목표 : 서버 확장 전략과 무상태 시스템이 왜 중요한지 이해하자

#### Stateless vs Stateful
+ Stateless : 요청 간 상태 저장하지 않음
+ Stateful : 요청마다 서버가 상태를 기억한다 / Ex. Session

#### 예시
+ 세션 로그인 -> stateful
+ JWT 로그인 -> stateless

---

## Step 2: SSO(Single Sign-On) 개념 및 흐름
+ 목표 : SSO의 기본 구조와 왜 필요한지 이해하자

#### 구성 요소
+ `IdP`(Identity Probider) : 사용자 인증 책임 (Keycloak, Okta)
+ `SP` (Service Provider) : 실제 서비스들 (게시판, 추천, 마이페이지 등)

#### 사용 프로토콜
+ OAuth2.0
+ OpenID Connect (OIDC)
+ SAML

---

## Step 3: 스케일아웃을 고려한 추천 시스템 API 구조 이해
+ 목표 : 추천 시스템 API가 무상태 기반일 때 어떤 장점이 있는지 이해하자

+ REST API 설계 개념 이해
+ 무상태 서버와 JWT 인증
+ 캐시 / 최적화 개요 

---

## 전체 목표
+ 무상태 서버와 인증의 개념 이해 필요
+ SSO가 필요한 이유와 동작 방식 설명 가능 필요
+ 추후 확장을 위한 개념적 기반 확보 필요