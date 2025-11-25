# Network (네트워크) — 핵심 CS 정리

## 목차
1. [브라우저 주소 입력 시 동작 과정](#1-브라우저-주소-입력-시-동작-과정)
2. [OSI 7계층 vs TCP/IP 4계층](#2-osi-7계층-vs-tcpip-4계층)
3. [TCP vs UDP](#3-tcp-vs-udp)
4. [3-way Handshake & 4-way Handshake](#4-3-way-handshake--4-way-handshake)
5. [TCP 흐름제어 vs 혼잡제어](#5-tcp-흐름제어-vs-혼잡제어)
6. [HTTP vs HTTPS](#6-http-vs-https)
7. [HTTP 1.1 vs HTTP 2.0](#7-http-11-vs-http-20)
8. [DNS (Domain Name System)](#8-dns-domain-name-system)
9. [ARP (Address Resolution Protocol)](#9-arp-address-resolution-protocol)
10. [MAC Address](#10-mac-address)
11. [NAT (Network Address Translation)](#11-nat-network-address-translation)
12. [CORS (Cross-Origin Resource Sharing)](#12-cors-cross-origin-resource-sharing)
13. [TLS/SSL Handshake](#13-tlsssl-handshake)
14. [대칭키 vs 공개키 암호화](#14-대칭키-vs-공개키-암호화)

## 관련 주제
- [RESTful API](../web-development/#2-restful-api) - 웹 개발
- [Over-fetching vs Under-fetching](../web-development/#3-over-fetching-vs-under-fetching) - 웹 개발
- [인증 방식: 쿠키/세션 vs JWT](../web-development/#4-인증-방식-쿠키세션-vs-jwt) - 웹 개발
- [CSR vs SSR](../web-development/#1-csr-vs-ssr) - 웹 개발
- [로그인에 POST를 사용하는 이유](../web-development/#5-로그인에-post를-사용하는-이유) - 웹 개발
- [로드 밸런싱](../system-design/#1-로드-밸런싱) - 시스템 설계
- [트래픽 폭주 대응](../system-design/#2-트래픽-폭주-대응) - 시스템 설계
- [웹 서버 vs 웹 애플리케이션 서버](../system-design/#3-웹-서버-vs-웹-애플리케이션-서버) - 시스템 설계
- [Proxy (Forward vs Reverse)](../system-design/#4-proxy-forward-vs-reverse) - 시스템 설계

---

## 1. 브라우저 주소 입력 시 동작 과정

### 과정
1. **DNS 조회**
   - 브라우저가 도메인을 IP 주소로 변환
   - 로컬 캐시 확인 → 없으면 DNS 서버에 요청

2. **TCP 연결**
   - IP 주소를 이용해 TCP 연결
   - **3-way handshake** 수행

3. **HTTP 요청/응답**
   - HTTP 요청 전송
   - 서버가 요청 처리 후 HTTP 응답 반환

4. **렌더링**
   - 브라우저가 응답 받아 페이지 렌더링

---

## 2. OSI 7계층 vs TCP/IP 4계층

### OSI 7계층

| 계층 | 이름 | 역할 | 주요 프로토콜/기능 |
|------|------|------|-------------------|
| 7 | 응용(Application) | 사용자 애플리케이션과 네트워크 인터페이스 제공 | HTTP, FTP, SMTP, DNS |
| 6 | 표현(Presentation) | 데이터 형식, 암호화/압축, 인코딩 처리 | SSL/TLS, JPEG, ASCII |
| 5 | 세션(Session) | 통신 세션 관리, 연결 유지 및 동기화 | RPC, NetBIOS |
| 4 | 전송(Transport) | 호스트 간 신뢰성 있는 데이터 전송, 흐름/혼잡 제어 | TCP, UDP |
| 3 | 네트워크(Network) | 패킷 전달, 주소 지정, 라우팅 | IP, ICMP, IPsec |
| 2 | 데이터링크(Data Link) | 물리적 전송 오류 검출, 프레임 전송 | Ethernet, PPP, MAC |
| 1 | 물리(Physical) | 실제 비트 전송, 전송 매체 정의 | 케이블, 광섬유, 전기신호 |

### TCP/IP 4계층

| 계층 | 이름 | 역할 | 주요 프로토콜/기능 |
|------|------|------|-------------------|
| 4 | 응용(Application) | 애플리케이션과 직접 통신 | HTTP, FTP, SMTP, DNS |
| 3 | 전송(Transport) | 호스트 간 신뢰성 전송, 포트 기반 통신 | TCP, UDP |
| 2 | 인터넷(Internet) | 패킷 전달 및 주소 지정, 라우팅 | IP, ICMP |
| 1 | 네트워크 인터페이스(Network Interface) | 물리적 전송 + 데이터 링크 기능 | Ethernet, Wi-Fi, PPP |

---

## 3. TCP vs UDP

### TCP (Transmission Control Protocol)

**특징**
- **연결 지향적 (Connection-oriented)**
- **3-way handshake**로 연결 설정
- 높은 신뢰성 보장
- 순서 보장, 재전송, 흐름제어, 혼잡제어

**사용 프로토콜**
- HTTP, SMTP, SSH, FTP

**단점**
- UDP보다 느림
- 오버헤드 큼

### UDP (User Datagram Protocol)

**특징**
- **비연결 지향적 (Connectionless)**
- 신뢰성 낮음 (순서 보장 X, 재전송 X)
- **빠른 전송 속도**
- 오버헤드 적음

**사용 프로토콜**
- DNS (연결 상태 유지 불필요)
- 실시간 스트리밍
- 게임

### 비교

| 항목 | TCP | UDP |
|------|-----|-----|
| 연결 방식 | 연결 지향 | 비연결 |
| 신뢰성 | 높음 | 낮음 |
| 속도 | 느림 | 빠름 |
| 순서 보장 | 있음 | 없음 |
| 오버헤드 | 큼 | 적음 |
| 사용 예시 | HTTP, SMTP | DNS, 스트리밍 |

---

## 4. 3-way Handshake & 4-way Handshake

### 3-way Handshake (연결 설정)

1. **SYN (Synchronize)**
   - 클라이언트 → 서버
   - 클라이언트가 서버에 연결 요청

2. **SYN-ACK (Synchronize + Acknowledge)**
   - 서버 → 클라이언트
   - 서버가 클라이언트 요청 수락 및 응답 준비

3. **ACK (Acknowledge)**
   - 클라이언트 → 서버
   - 클라이언트가 서버 응답 확인 및 최종 승인
   - 연결 성립

### 4-way Handshake (연결 종료)

1. **FIN**
   - 종료 요청하는 쪽이 보냄 (예: 클라이언트)

2. **ACK**
   - 상대방이 요청 확인

3. **FIN**
   - 상대방도 연결 종료 준비 완료

4. **ACK**
   - 최초 종료 요청자가 확인
   - 연결 종료 완료

---

## 5. TCP 흐름제어 vs 혼잡제어

### 흐름제어 (Flow Control)

**목적**
- 수신자 버퍼 초과 방지
- 수신자의 처리 능력에 맞춰 송신 속도 조절

**기준**
- 수신자 처리 능력 (버퍼 크기)

**제어 대상**
- 송신자 ↔ 수신자

**대표 메커니즘**
- Sliding Window

### 혼잡제어 (Congestion Control)

**목적**
- 네트워크 혼잡 방지
- 네트워크 상태에 맞춰 송신 속도 조절

**기준**
- 네트워크 상태 (손실, 지연)

**제어 대상**
- 송신자 ↔ 네트워크 전체

**대표 메커니즘**
- Slow Start
- Congestion Avoidance

### 비교

| 구분 | 흐름제어 | 혼잡제어 |
|------|---------|---------|
| 목적 | 수신자 버퍼 초과 방지 | 네트워크 혼잡 방지 |
| 기준 | 수신자 처리 능력 | 네트워크 상태 |
| 제어 대상 | 송신자 ↔ 수신자 | 송신자 ↔ 네트워크 전체 |
| 메커니즘 | Sliding Window | Slow Start, Congestion Avoidance |

---

## 6. HTTP vs HTTPS

### HTTP (HyperText Transfer Protocol)
- 평문 전송
- 보안 취약

### HTTPS (HTTP Secure)
- HTTP + SSL/TLS
- 암호화된 안전한 통신
- 인증서 필요

### HTTPS 전환 과정
1. **인증서 발급** (CA에서)
2. **서버 설정** (인증서 설치)
3. **HTTP 리다이렉트** (HTTP → HTTPS 자동 전환)
4. **내부 리소스 수정** (모든 리소스를 HTTPS로)

---

## 7. HTTP 1.1 vs HTTP 2.0

### HTTP 1.1의 문제점

**HOL (Head-of-Line) Blocking**
- 연결 한 개당 하나의 요청을 순차적으로 처리
- 앞의 요청이 지연되면 전체 지연 발생

**오버헤드**
- 중복된 정보(쿠키, 헤더)로 인한 오버헤드
- 텍스트 기반 프로토콜

### HTTP 2.0의 개선사항

**멀티플렉싱 (Multiplexing)**
- 여러 요청과 응답을 비순차적으로 처리
- Stream 고유 번호로 원래 메시지 복구 가능

**헤더 압축 (HPACK)**
- 중복된 헤더를 테이블로 관리하여 헤더 크기 축소

**서버 푸시 (Server Push)**
- 리소스를 미리 전송 가능
- 요청/응답 반복 횟수 감소

**이진 프레이밍 (Binary Framing)**
- 텍스트 기반이 아닌 이진 프로토콜로 전송
- 성능 향상

### 비교

| 항목 | HTTP 1.1 | HTTP 2.0 |
|------|----------|----------|
| 요청 처리 | 순차적 | 병렬 (멀티플렉싱) |
| HOL Blocking | 발생 | 해결 |
| 헤더 | 중복 전송 | 압축 (HPACK) |
| 프로토콜 | 텍스트 | 이진 |
| 서버 푸시 | 불가 | 가능 |

---

## 8. DNS (Domain Name System)

### 정의
도메인 이름을 IP 주소로 변환하는 시스템

### 동작 과정
1. **로컬 캐시 확인**
2. **호스트 파일 확인**
3. **DNS 서버에 요청**
   - Root DNS → TLD DNS → Authoritative DNS

### 목적
- 사람이 기억하기 쉬운 도메인 이름 사용
- IP 주소 변경 시 도메인만 수정하면 됨

---

## 9. ARP (Address Resolution Protocol)

### 정의
IP 주소를 MAC 주소로 변환하는 프로토콜

### 동작
1. 같은 네트워크 내에서 IP 주소로 MAC 주소 조회
2. 브로드캐스트로 ARP 요청
3. 해당 IP를 가진 장치가 MAC 주소 응답

### 목적
- 같은 네트워크 내에서 기기 식별
- 브로드캐스트 → 유니캐스트 변환

---

## 10. MAC Address

### 정의
**Media Access Control Address**의 약자
- 네트워크 장치(네트워크 인터페이스 카드, NIC)에 **하드웨어 수준에서 부여된 고유 식별자**

### 구조
- **OUI (Organizationally Unique Identifier)**: 상위 24비트
  - 제조사 식별
- **NIC 식별자**: 하위 24비트
  - 제조사가 부여하는 고유 번호

### 특징
- 하드웨어 고유 식별 번호
- 같은 네트워크 내 기기 식별에 사용

---

## 11. NAT (Network Address Translation)

### 정의
네트워크 장비(주로 **라우터**)가 **패킷의 IP 주소를 변환**하는 기술

### 역할
- 내부 네트워크의 **사설 IP** ↔ 외부 네트워크의 **공인 IP** 간 변환

### 목적
- 공인 IP 주소 부족 문제 해결
- 내부 네트워크 보안 강화

---

## 12. CORS (Cross-Origin Resource Sharing)

### SOP (Same Origin Policy)
같은 사이트 내에서만 데이터 공유

### CORS
다른 도메인 간 리소스 공유를 허용하는 메커니즘

### 해결 방법

**서버 측**
- 허용할 도메인을 헤더에 추가
- `Access-Control-Allow-Origin` 설정

**클라이언트 측**
- Proxy Server (Forward Proxy) 사용

---

## 13. TLS/SSL Handshake

### TLS (Transport Layer Security)
네트워크에서 데이터를 안전하게 주고받기 위한 **보안 프로토콜**

### Handshake 과정

1. **Client Hello**
   - 클라이언트가 TLS 버전, 지원 암호화 알고리즘 전송

2. **Server Hello**
   - 서버가 사용할 알고리즘 선택, 인증서 전송

3. **키 교환 (Key Exchange)**
   - 세션 키 생성

4. **Finished**
   - 서로 세션 키로 암호화된 메시지 교환
   - 안전한 통신 시작

---

## 14. 대칭키 vs 공개키 암호화

### 대칭키 암호화 (Symmetric Key)

**특징**
- 송수신자가 **같은 키** 사용
- 빠른 연산 속도
- 대량 데이터 처리 가능

**사용**
- 실제 데이터 전송 시 (세션 키)

### 공개키 암호화 (Public Key Cryptography, 비대칭 암호화)

**특징**
- **공개키 (Public Key)**: 모두에게 공개, 데이터 암호화
- **비밀키 (Private Key)**: 소유자만 보유, 데이터 복호화
- 느린 연산 속도

**사용**
- **키 교환**: 안전하게 대칭키(세션 키) 전달
- **디지털 서명**: 메시지 무결성 검증

### HTTPS에서의 사용

**공개키 사용**
1. 서버가 공개키와 인증서를 브라우저에 제공
2. 브라우저는 서버 공개키로 **세션키(대칭키) 생성**
3. 세션키를 공개키로 암호화해서 서버에 전송
4. 서버는 비밀키로 세션키 복호화 → 안전하게 공유 완료

**대칭키 사용**
- 세션키를 공유한 후, **실제 데이터 전송**에는 대칭키 사용
- 이유: 연산이 빠르고, 대량 데이터 처리 가능

### 비교

| 항목 | 대칭키 | 공개키 |
|------|--------|--------|
| 키 개수 | 1개 (공유) | 2개 (공개키/비밀키) |
| 속도 | 빠름 | 느림 |
| 사용 | 데이터 전송 | 키 교환, 디지털 서명 |

---

[← 메인으로 돌아가기](../README.md)
