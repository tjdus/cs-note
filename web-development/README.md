# Web Development (웹 개발) — 핵심 CS 정리

## 목차
1. [CSR vs SSR](#1-csr-vs-ssr)
2. [RESTful API](#2-restful-api)
3. [Over-fetching vs Under-fetching](#3-over-fetching-vs-under-fetching)
4. [인증 방식: 쿠키/세션 vs JWT](#4-인증-방식-쿠키세션-vs-jwt)
5. [로그인에 POST를 사용하는 이유](#5-로그인에-post를-사용하는-이유)

---

## 1. CSR vs SSR

### CSR (Client-Side Rendering)

**특징**
- 클라이언트에서 렌더링
- React, Vue 등
- 필요한 부분만 렌더링

**장점**
- 초기 로드 후 빠른 전환
- 서버 부하 감소

**단점**
- 초기 로딩 느림
- SEO 어려움

### SSR (Server-Side Rendering)

**특징**
- 서버에서 HTML 직접 생성
- 서버가 완전한 HTML 전송

**장점**
- 초기 렌더링 빠름
- 검색 엔진 최적화 (SEO)
- 정적 파일이므로 비즈니스 로직 거치지 않아 빠름
- 캐싱 효율

**단점**
- 서버 부하 증가
- 페이지 전환 시 서버 요청 필요

### 비교

| 항목 | CSR | SSR |
|------|-----|-----|
| 렌더링 위치 | 클라이언트 | 서버 |
| 초기 로딩 | 느림 | 빠름 |
| SEO | 어려움 | 용이 |
| 서버 부하 | 적음 | 많음 |

---

## 2. RESTful API

### REST (Representational State Transfer)

**원칙**
- **Stateless**: 서버는 클라이언트 상태를 유지하지 않음
- **Cache**: 캐시 가능한 응답
- **Client-Server**: 독립적으로 동작
- **Resource-oriented**: 리소스 중심 설계
- **Layered**: 계층 구조

### Stateless의 의미

**특징**
- 서버는 클라이언트의 상태(Session)을 유지하지 않음
- 각 요청은 **독립적**이어야 함
- 필요한 모든 정보를 요청 안에 담아서 전송

**장점**
- 확장성 향상
- 서버 부하 감소

### RESTful API의 장점

- **Cache**: 서버 부하 감소, 클라이언트 성능 향상
- **Client-Server**: 독립적 개발, 유지보수성, 확장성
- **Layered**: 보안 강화, 로드 분산, 확장성

### RESTful API의 단점

- **Over-fetching / Under-fetching** 문제
- 실시간 통신 어려움
- 복잡한 쿼리 처리 어려움

### 해결 방법

**GraphQL**
- 필요한 데이터만 요청
- Over-fetching / Under-fetching 문제 해결

**WebSocket**
- 실시간 통신 필요 시 사용
- 채팅, 실시간 알림 등

---

## 3. Over-fetching vs Under-fetching

### Over-fetching
- **불필요한 정보**까지 가져옴
- 네트워크 대역폭 낭비

### Under-fetching
- **부족한 정보**만 가져옴
- 여러 번의 요청 필요
- 네트워크 요청 증가

### 해결 방법

**GraphQL**
- 필요한 데이터만 요청
- Over-fetching / Under-fetching 문제 해결

**WebSocket**
- 실시간 통신 필요 시 사용
- 채팅, 실시간 알림 등

---

## 4. 인증 방식: 쿠키/세션 vs JWT

### 쿠키/세션 방식

**동작**
- 서버가 세션 정보를 저장
- 클라이언트에 **Session ID**를 쿠키로 전송
- 브라우저가 자동으로 헤더에 포함

**특징**
- 서버 의존적 (서버의 세션 저장소 필요)
- 서버 확장 시 세션 공유 필요

**보안**
- 쿠키 탈취 가능
- 세션 키를 쿠키에 담아 저장

**단점**
- 사용자 수 증가 시 메모리/디스크 사용량 급증

### JWT (JSON Web Token)

**구조**
- **Header**: 알고리즘 정보
- **Payload**: 실제 데이터 (Base64 인코딩)
- **Signature**: 무결성 검증

**특징**
- **무상태 (Stateless)**: 토큰 자체에 정보 포함
- 서버 간 상태 공유 불필요
- 확장 용이

**사용 환경**
- 마이크로서비스 등 분산 환경

### 비교

| 구분 | 쿠키/세션 (Session ID) | JWT |
|------|----------------------|-----|
| 저장 정보 | Session ID (서버 데이터 접근 키) | 사용자 정보/권한 (토큰 자체에 포함) |
| 인증 방식 | 서버 의존적 | 자체 검증 (무상태) |
| 요청 전송 | 브라우저 자동 포함 | 수동으로 Authorization 헤더에 포함 |
| 확장성 | 세션 저장소 확장 필요 | 서버 간 상태 공유 불필요 → 확장 용이 |
| 사용 환경 | 단일 서버 또는 세션 공유 환경 | 마이크로서비스 등 분산 환경 |

---

## 5. 로그인에 POST를 사용하는 이유

### GET의 문제점
- 쿼리 파라미터로 데이터 전송
- URL에 노출되어 보안 위험
- 브라우저 히스토리, 로그에 남음

### POST의 장점
- **Message Body**에 데이터 포함
- URL에 노출되지 않음
- 보안성 향상
- **멱등성 보장 X** → 캐시 불가 (로그인은 캐시하면 안 됨)

### 추가 보안
- JWT 등의 인증 방식 사용
- HTTPS 사용

---

[← 메인으로 돌아가기](../README.md)

