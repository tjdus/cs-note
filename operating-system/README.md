# Operating System (OS) — 핵심 CS 정리

## 목차
1. [Deadlock (교착 상태)](#1-deadlock-교착-상태)
2. [프로세스 vs 스레드](#2-프로세스process-vs-스레드thread)
3. [사용자 수준 스레드 vs 커널 스레드](#3-사용자-수준-스레드-vs-커널-스레드)
4. [메모리 구조](#4-메모리-구조-stack--heap--data--code)
5. [Kernel & System Call](#5-kernel--system-call)
6. [동기화](#6-동기화-mutex-semaphore-spin-lock)
7. [Race Condition & Critical Section](#7-race-condition--critical-section)
8. [가상 메모리](#8-가상-메모리virtual-memory)
9. [단편화](#9-단편화)
10. [Polling vs Interrupt](#10-polling-vs-interrupt)
11. [CPU 스케줄링](#11-cpu-스케줄링)
12. [Context Switching](#12-context-switching)
13. [Write-through vs Write-back](#13-write-through-vs-write-back)
14. [Kernel Panic](#14-kernel-panic)
15. [멀티프로세스 vs 멀티스레드](#15-멀티프로세스보다-멀티스레드를-사용하는-이유)
16. [volatile 키워드](#16-volatile-키워드)

---

## 1. Deadlock (교착 상태)

### 정의
두 개 이상의 프로세스가 서로가 점유한 자원을 기다리며 **무한 대기 상태**에 빠지는 현상.

### Deadlock 발생 조건
1. **상호 배제 (Mutual Exclusion)** — 자원을 동시에 공유할 수 없음
2. **점유 대기 (Hold & Wait)** — 자원을 보유한 채 다른 자원을 요청
3. **비선점 (No Preemption)** — 자원을 강제로 빼앗을 수 없음
4. **순환 대기 (Circular Wait)** — 프로세스 간 자원 요청이 순환 구조 형성

### Deadlock 해결 방법

#### 1) 예방 (Prevention)
교착 상태 발생 조건 중 하나라도 성립하지 않게 설계
- 자원 요청 순서 지정
- 모든 자원을 한 번에 요청하도록 강제 (hold&wait 방지)

#### 2) 회피 (Avoidance)
현재 상태가 안전한지를 판단하여 자원을 할당
- **은행원 알고리즘 (Banker's Algorithm)**: 최대 자원 사용량 기반 안전 상태 판단

#### 3) 발견 & 회복 (Detection & Recovery)
- 주기적으로 deadlock 검사 후
- 프로세스 종료/자원 회수로 회복

#### 4) 무시 (Ignore)
- Linux/Unix의 **Ostrich Algorithm**
- Deadlock 발생 확률이 낮으므로 무시

---

## 2. 프로세스(process) vs 스레드(thread)

### 프로세스
- 실행 중인 프로그램
- Code / Data / Heap / Stack의 **독립된 메모리 공간** 보유
- Context Switching 비용 큼

### 스레드
- 프로세스 내부 실행 흐름
- **Stack만** 독립, Code/Data/Heap은 공유
- Context switching 빠름
- 하나의 스레드 오류 → 프로세스 전체 영향

### 비교

| 항목 | 프로세스 | 스레드 |
|------|---------|--------|
| 메모리 공간 | 독립적 | Stack만 독립, 나머지 공유 |
| Context Switching | 비용 큼 | 비용 적음 |
| 통신 | IPC 필요 | 공유 메모리로 직접 통신 |
| 오류 영향 | 프로세스 단위 | 프로세스 전체 영향 |

---

## 3. 사용자 수준 스레드 vs 커널 스레드

### 사용자 수준 스레드 (User-Level Thread)

**장점**
- 커널 개입 없이 스레드 전환 → 빠름
- 구현이 상대적으로 간단

**단점**
- 하나가 커널 호출하면 전체 block
- 멀티코어 활용 불가

### 커널 수준 스레드 (Kernel-Level Thread)

**장점**
- 커널이 스레드를 직접 스케줄링
- 멀티코어 사용 가능
- 하나의 스레드 block이 다른 스레드에 영향 없음

**단점**
- 스레드 전환 시 커널 개입 → 비용 증가

### 비교

| 항목 | 사용자 수준 스레드 | 커널 수준 스레드 |
|------|-------------------|------------------|
| 스케줄링 | 사용자 라이브러리 | 커널 |
| 전환 비용 | 낮음 | 높음 |
| 멀티코어 활용 | 불가 | 가능 |
| Block 영향 | 전체 스레드 | 해당 스레드만 |

---

## 4. 메모리 구조: Stack / Heap / Data / Code

| 메모리 영역 | 설명 | 특징 |
|------------|------|------|
| **Stack** | 지역 변수, 매개변수, 리턴 주소 | LIFO, 자동 관리 |
| **Heap** | 동적 메모리 (`malloc`, `new`) | 수동 관리, 단편화 가능 |
| **Data** | 전역 변수, static 변수 | 프로그램 시작 시 할당 |
| **Code** | 기계어 명령 저장 영역 | 읽기 전용 |

---

## 5. Kernel & System Call

### Kernel
운영체제의 핵심
- 프로세스/메모리/I-O 관리
- 파일 시스템 관리
- 하드웨어와 응용 프로그램 사이의 **인터페이스**

### System Call
유저 프로그램이 커널 기능을 사용하기 위한 인터페이스

### 동작 과정
1. 사용자 프로그램이 system call 호출
2. **trap** 발생 → 커널 모드 진입
3. 커널이 요청 수행
4. 사용자 모드로 복귀

---

## 6. 동기화: Mutex, Semaphore, Spin Lock

### Mutex
- 단일 자원 보호
- 락 **소유권 존재**
- 같은 스레드만 unlock 가능

### Semaphore
- 개수 제한 있는 자원 접근 제어
- Binary Semaphore는 Mutex와 유사하나 **소유권 없음**

### Spin Lock
- 락이 풀릴 때까지 busy-wait 반복
- 짧은 임계 구역에서 유리

### 비교

| 방식 | 소유권 | 사용 시나리오 |
|------|--------|--------------|
| **Mutex** | 있음 | 단일 자원 보호 |
| **Semaphore** | 없음 | 제한된 개수의 자원 접근 제어 |
| **Spin Lock** | 있음 | 짧은 임계 구역, CPU 대기 가능 |

---

## 7. Race Condition & Critical Section

### Race Condition
여러 스레드가 공유 자원에 접근하여 **실행 순서에 따라 결과가 달라지는 문제**

### Critical Section
공유 데이터를 수정하는 코드 영역
→ Mutex/Semaphore/Lock 등으로 보호

### 해결 방법
- **상호 배제 (Mutual Exclusion)**: 한 번에 하나의 스레드만 접근
- **진행 (Progress)**: 임계 구역이 비어있으면 대기 중인 스레드 진입 허용
- **한정 대기 (Bounded Waiting)**: 무한 대기 방지

---

## 8. 가상 메모리(Virtual Memory)

### 목적
- **프로세스 격리**
- **안전성 (Protection)**
- **물리 메모리보다 큰 공간 사용 가능**
- **효율적 메모리 활용**

### Paging
- 고정 크기 페이지
- **외부 단편화 없음**
- 내부 단편화 가능

### Segmentation
- 의미 단위로 메모리 분리
- **외부 단편화 발생 가능**
- 논리적 구조 반영 용이

### 비교

| 방식 | 단위 | 단편화 | 장점 |
|------|------|--------|------|
| **Paging** | 고정 크기 | 내부 단편화 | 외부 단편화 없음 |
| **Segmentation** | 가변 크기 | 외부 단편화 | 논리적 구조 반영 |

---

## 9. 단편화

| 종류 | 설명 | 발생 원인 |
|------|------|----------|
| **내부 단편화** | 페이지/블록 크기가 커서 남는 공간 | 고정 크기 할당 |
| **외부 단편화** | 작은 조각들이 흩어져 큰 공간을 할당 못함 | 가변 크기 할당, 반복적인 할당/해제 |

---

## 10. Polling vs Interrupt

### Polling
- CPU가 지속적으로 장치 상태 확인
- 구현 간단하지만 **비효율적 (CPU 낭비)**

### Interrupt
- 장치가 이벤트 발생 시 CPU에 신호
- CPU는 다른 작업 가능 → **효율적**

### 종류
- **하드웨어 인터럽트**: 키보드, 마우스, 타이머
- **소프트웨어 인터럽트**: 시스템 콜, 예외

### 비교

| 방식 | CPU 사용 | 응답 속도 | 구현 복잡도 |
|------|----------|----------|------------|
| **Polling** | 지속적 확인 | 즉시 | 간단 |
| **Interrupt** | 이벤트 시만 | 빠름 | 복잡 |

---

## 11. CPU 스케줄링

### FCFS (First Come First Served)
- 먼저 온 프로세스 처리
- **Convoy effect** 발생 (긴 프로세스가 짧은 프로세스 대기)

### SJF (Shortest Job First)
- 실행 시간 짧은 프로세스 우선
- 평균 대기시간 최소
- Burst Time 예측 어려움

### Round Robin (RR)
- Time Quantum 기반
- 공정성 ↑
- Quantum이 짧으면 오버헤드 ↑

### Priority Scheduling
- 우선순위 높은 작업 우선
- **Starvation** 발생 → **Aging**으로 해결

### MLFQ (Multi-Level Feedback Queue)
- 우선순위를 **동적으로 변경**
- I/O-bound → 상위 큐
- CPU-bound → 하위 큐
- Starvation → Periodic Priority Boost

### 비교

| 알고리즘 | 특징 | 단점 |
|---------|------|------|
| **FCFS** | 단순, 공정 | Convoy effect |
| **SJF** | 평균 대기시간 최소 | 예측 어려움 |
| **RR** | 공정성, 응답성 | Quantum 설정 중요 |
| **Priority** | 우선순위 반영 | Starvation |
| **MLFQ** | 동적 우선순위 | 복잡도 높음 |

---

## 12. Context Switching

CPU가 실행 중인 프로세스의 상태(레지스터, PC, 스택 포인터)를 PCB에 저장하고 다른 프로세스의 상태를 복원하는 작업

### 특징
- 비용 큼
- 가능한 최소화가 중요

### 비용 요소
- 레지스터 저장/복원
- 캐시 미스
- TLB 플러시

---

## 13. Write-through vs Write-back

| 방식 | 특징 | 장점 | 단점 |
|------|------|------|------|
| **Write-through** | 변경 시 즉시 메모리에 반영 | 안정성 ↑, 일관성 | 속도 ↓ |
| **Write-back** | 캐시에 쓰고 나중에 메모리에 반영 | 속도 ↑ | 데이터 손실 위험 |

---

## 14. Kernel Panic

커널이 복구 불가능한 오류를 감지하여 시스템 전체를 멈추는 현상

### 주요 원인
- 잘못된 드라이버
- 하드웨어 오류
- 메모리 손상
- 커널 버그

---

## 15. 멀티프로세스보다 멀티스레드를 사용하는 이유

### 장점
- 스레드 생성/전환 비용이 적음
- IPC보다 스레드 간 통신이 빠름
- 메모리 사용량 감소
- CPU 사용량 효율적

### 단점 및 고려 사항
- 공유 자원 동기화 필요
- 캐시 일관성 문제
- 스레드 오류는 프로세스 전체에 영향

### 비교

| 항목 | 멀티프로세스 | 멀티스레드 |
|------|-------------|-----------|
| 메모리 | 독립적 | 공유 |
| 통신 | IPC 필요 | 공유 메모리 |
| 생성 비용 | 높음 | 낮음 |
| 안정성 | 높음 (격리) | 낮음 (영향 전파) |

---

## 16. volatile 키워드

### 특징
- 변수를 **항상 메모리에서 읽도록 함**
- 컴파일러 최적화 방지
- 멀티스레드에서 값이 즉시 반영됨

### 주의사항
- **원자성 보장 X** → Mutex/Lock 필요
- 성능 오버헤드 존재

---

[← 메인으로 돌아가기](../README.md)
