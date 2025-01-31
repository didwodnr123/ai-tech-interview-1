<div align='center'>
  <h1>🖥️ Operating System 🖥️</h1>
</div>

> 질문은 `<strong>`[WeareSoft님의 tech-interview](https://github.com/WeareSoft/tech-interview)`</strong>`를 참고하였습니다.

---

## Table of Contents

- [프로세스와 스레드의 차이(Process vs Thread)를 알려주세요.](#1)
- [멀티 프로세스 대신 멀티 스레드를 사용하는 이유를 설명해주세요.](#2)
- [캐시의 지역성에 대해 설명해주세요.](#3)
- [Thread-safe에 대해 설명해주세요. (hint: critical section)](#4)
- [뮤텍스와 세마포어의 차이를 설명해주세요.](#5)
- [스케줄러가 무엇이고, 단기/중기/장기로 나누는 기준에 대해 설명해주세요.](#6)
- [CPU 스케줄러인 FCFS, SJF, SRTF, RR, Priority Scheduling에 대해 간략히 설명해주세요.](#7)
- [동기와 비동기의 차이를 설명해주세요.](#8)
- [메모리 관리 전략에는 무엇이 있는지 간략히 설명해주세요.](#9)
- [가상 메모리에 대해 설명해주세요.](#10)
- [교착상태(데드락, Deadlock)의 개념과 조건을 설명해주세요.](#11)
- [사용자 수준 스레드와 커널 수준 스레드의 차이를 설명해주세요.](#12)
- [외부 단편화와 내부 단편화에 대해 설명해주세요.](#13)
- [Context Switching이 무엇인지 설명하고 과정을 나열해주세요.](#14)
- [Swapping에 대해 설명해주세요.](#15)

---

## #1

### 프로세스와 스레드의 차이(Process vs Thread)를 알려주세요.

#### 개념 정의

`한줄요약: 프로그램 -> 프로세스 -> 스레드`

- **프로그램(Program)**: 파일이 저장 장치에 저장되어 있지만 메모리에는 올라가 있지 않은`정적`인 상태.
  - 아직 실행되지 않은 파일 그 자체를 가르킴.
- **프로세스(Process)**: 운영체제로부터 자원을 할당받은`작업`의 단위.
  - 프로그램을 실행 -> 메모리에 올라감 -> 동적인 상태 -> 이 상태의 프로그램을 프로세스라고 함.
- **스레드(Thread)**: 프로세스가 할당받은 자원을 이용하는`실행 흐름`의 단위.
  - 프로세스 내에서 실행되는 여러 흐름의 단위
    - 즉 프로세스는 최소 1개의 스레드를 갖고 있음

#### 프로세스와 스레드의 차이

가장 큰 특징은 메모리 공유.
OS가 **프로세스**에게 code/data/stack/heap 메모리 영역을 할당해 최소 작업 단위로 삼지만
**스레드**는 프로세스 내에서 stack 메모리 영역을 제외한 다른 메모리 영역을 같은 프로세스 내 다른 스레드와 공유.
이로 인한 각각의 장단점은 #2에서 다룸!

### Reference

- [프로세스와 스레드의 차이](https://velog.io/@raejoonee/%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EC%99%80-%EC%8A%A4%EB%A0%88%EB%93%9C%EC%9D%98-%EC%B0%A8%EC%9D%B4)

## #2

#### 멀티 프로세스 대신 멀티 스레드를 사용하는 이유를 설명해주세요.

**멀티 프로세스**란 하나의 응용프로그램을 여러 개의 프로세스로 구성하여 각 프로세스가 하나의 작업을 처리하도록 하는 것이다.

![Screen Shot 2021-12-26 at 9.23.26 PM](images/os_jw_01.png)

[이미지 출처](https://velog.io/@nnnyeong/OS-%EB%A9%80%ED%8B%B0%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EB%A9%80%ED%8B%B0%EC%8A%A4%EB%A0%88%EB%93%9C-%EB%A9%80%ED%8B%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EB%A9%80%ED%8B%B0%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EB%A9%80%ED%8B%B0%EC%8A%A4%EB%A0%88%EB%93%9C%EC%97%90%EC%84%9C%EC%9D%98-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%86%B5%EC%8B%A0)

- 장점
  - 여러 개의 자식 프로세스 중 하나에 문제가 발생하면 그 자식 프로세스만 죽는 것 이상으로 다른 영향이 확산되지 않는다.
- 단점
  - Context Switching에서의 오버헤드
    - Context Switching 과정에서 캐시 메모리 초기화 등 무거운 작업이 진행되고 많은 시간이 소모되는 등의 오버헤드가 발생하게 된다.
    - 프로세스는 각각의 독립된 메모리 영역을 할당받았기 때문에 프로세스 사이에서 공유하는 메모리가 없어, Context Switching가 발생하면 캐시에 있는 모든 데이터를 모두 리셋하고 다시 캐시 정보를 불러와야 한다.
  - 프로세스 사이의 어렵고 복잡한 통신 기법(IPC)
    - 프로세스는 각각의 독립된 메모리 영역을 할당받았기 때문에 하나의 프로그램에 속하는 프로세스들 사이의 변수를 공유할 수 없다.

**멀티 스레드**란 하나의 응용프로그램을 여러 개의 스레드로 구성하고 각 스레드로 하여금 하나의 작업을 처리하도록 하는 것이다. 윈도우, 리눅스 등 많은 운영체제들이 멀티 프로세싱을 지원하고 있지만 멀티 스레드를 기본으로 하고 있다. 웹 서버는 대표적인 멀티 스레드 응용 프로그램이다.

![Screen Shot 2021-12-26 at 9.24.31 PM](images/os-jw-multi-thread.png)

[이미지 출처](https://velog.io/@nnnyeong/OS-%EB%A9%80%ED%8B%B0%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EB%A9%80%ED%8B%B0%EC%8A%A4%EB%A0%88%EB%93%9C-%EB%A9%80%ED%8B%B0-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%EB%A9%80%ED%8B%B0%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EB%A9%80%ED%8B%B0%EC%8A%A4%EB%A0%88%EB%93%9C%EC%97%90%EC%84%9C%EC%9D%98-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%86%B5%EC%8B%A0)

- 장점
  - 시스템 자원 소모 감소(자원의 효율성 증대)
    - 프로세스를 생성하여 자원을 할당하는 시스템 콜이 줄어들어 자원을 효율적으로 관리할 수 있다.
  - 시스템 처리량 증가(처리 비용 감소)
    - 스레드 간 데이터를 주고받는 것이 간단해지고 시스템 자원 소모가 줄어들게 된다.
    - 스레드 사이의 작업량이 작아 Context Switching이 빠르다.
  - 간단한 통신 방법으로 인한 프로그램 응답 시간 단축
    - 스레드는 프로세스 내의 Stack 영역을 제외한 모든 메모리를 공유하기 때문에 통신의 부담이 적다.
- 단점
  - 주의 깊은 설계가 필요하다.
  - 디버깅이 까다롭다.
  - 단일 프로세스 시스템의 경우 효과를 기대하기 어렵다.
  - 다른 프로세스에서 스레드를 제어할 수 없다.(프로세스 밖에서 스레드 각각을 제어할 수 없다.)
  - 멀티 스레드의 경우 자원 공유의 문제가 발생한다.(동기화 문제)
  - 하나의 스레드에 문제가 발생하면 전체 프로세스가 영향을 받는다.

> **멀티 프로세스 대신 멀티 스레드를 사용하는 이유**

1. 자원의 효율성 증대

프로세스 간의 Context Switching 시 단순히 CPU 레지스터 교체뿐만 아니라 RAM과 CPU 사이의 캐시 메모리에 대한 데이터까지 초기화되므로 오버헤드가 발생한다. 멀티 프로세스로 실행되는 작업을 멀티 스레드로 실행할 경우, **프로세스를 생성하여 자원을 할당하는 시스템 콜이 줄어들어** 자원을 효율적으로 관리할 수 있다.

스레드는 프로세스 내의 메모리를 공유하기 때문에 독립적인 프로세스와 달리 스레드 간 데이터를 주고받는 것이 간단해지고 시스템 자원 소모가 줄어들게 된다.

1. 처리 비용 감소 및 응답 시간 단축

스레드는 Stack 영역을 제외한 모든 메모리를 공유하기 때문에 프로세스 간의 통신(IPC)보다 스레드 간의 통신의 비용이 적으므로 작업들 간의 통신의 부담이 줄어든다.

Context Switching 시 스레드는 Stack 영역만 처리하기 때문에 프로세스 간의 전환 속도보다 스레드 간의 전환 속도가 빠르다.

> **Context Switching**

CPU에서 여러 프로세스를 돌아가면서 작업을 처리하는 데 이 과정을 Context Switching라 한다. 동작 중인 프로세스가 대기를 하면서 해당 프로세스의 상태(Context)를 보관하고, 대기하고 있던 다음 순서의 프로세스가 동작하면서 이전에 보관했던 프로세스의 상태를 복구하는 작업을 말한다.

> **동기화 문제(Synchronization Issue)**

멀티 스레드를 사용하면 각각의 스레드 중 어떤 것이 어떤 순서로 실행될지 그 순서를 알 수 없다. 만약 A 스레드가 어떤 자원을 사용하다가 B 스레드로 제어권이 넘어간 후 B 스레드가 해당 자원을 수정했을 때, 다시 제어권을 받은 A 스레드가 해당 자원에 접근하지 못하거나 바뀐 자원에 접근하게 되는 오류가 발생할 수 있다.

이처럼 여러 스레드가 함께 전역 변수를 사용할 경우 발생할 수 있는 충돌을 동기화 문제라고 한다.

> Context Switching에 대한 자세한 내용은 [#14. Context Switching이 무엇인지 설명하고 과정을 나열해주세요.](https://github.com/didwodnr123/ai-tech-interview/blob/main/answers/6-operating-system.md#14) 참고

#### References

- [[OS\] 프로세스와 스레드의 차이 - Heee&#39;s Development Blog](https://gmlwjd9405.github.io/2018/09/14/process-vs-thread.html)
- [프로세스와 스레드의 차이 - 개발장](https://velog.io/@raejoonee/프로세스와-스레드의-차이)

## #3

### 캐시의 지역성에 대해 설명해주세요.

![OS-3-(2)](images/OS_3-1.jpg)

![OS-3-(2)](images/OS_3.jpg)

**캐시 메모리(Cash Memory)**

- CPU의 처리 속도와 메모리의 속도 차이로 인한 병목현상을 완화하기 위해 사용하는 고속 버퍼 메모리
- 주기억장치에서 자주 사용하는 프로그램과 데이터를 저장행두어 속도를 빠르게 하는 메모리

  - 주기억장치 보다 크기가 작다
- 주기억장치와 CPU사이에 위치
- 캐시 메모리에 먼저 접근후에 없으면 메인메모리로 접근

![OS-3-(2)](images/OS-3-2.png)

**지역성(Locality)**

- 어떻게  "자주 사용하는 기준"을 판단하여 정보를 캐시 메모리에 저장할까? 이때 사용하는 개념이 지역성(Locality)의 원리
- `지역성(Locality)`이란 기억 장치 내의 정보를 균일하게 액세스하는 것이 아닌 어느 한순간에 특정 부분을 집중적으로 참조하는 특성
- 지역성은 크게 2가지 시간적 지역성(Temporal Locality)`, `공간적 지역성(Spatial Locality) 존재
- 시간적 지역성(Temporal Locality): 현재 사용되는 데이터는 곧 다시 쓰일것이다.
- 공간적 지역성(Spatial Locality): 현재 사용되는 메모리 근처의 데이터는 곧 사용될 것이다.
- 그러나 지역성은 어디까지나 경향에 대한 것이므로 항상 캐시의 높은 적중률(캐시 메모리가 쓰이냐 안 쓰이냐)을 보장해 주지는 않는다.

#### Reference

- [[OS] 캐시 메모리(Cache Memory)란? 캐시의 지역성(Locality)이란?](https://chelseashin.tistory.com/43)
- [ 캐시가 동작하는 아주 구체적인 원리](https://parksb.github.io/article/29.html)
- [Locality of Reference and Cache Operation in Cache Memory](https://www.geeksforgeeks.org/locality-of-reference-and-cache-operation-in-cache-memory/)

## #4

### Thread-safe에 대해 설명해주세요. (hint: critical section)

#### 정의

멀티 스레드 프로그래밍에서 일반적으로 어떤 함수나 변수, 혹은 객체가 여러 스레드로부터 동시에 접속이 이루어져도 프로그램의 실행에 문제가 없음을 뜻함

즉, 하나의 함수가 한 스레드로부터 호출되어 실행 중일 때, 다른 스레드가 그 함수를 호출하여 동시에 함께 실행되더라도 각 스레드에서 함수의 수행 결과가 올바르게 나오는 것

#### critical section

thread-safe하게 만들기 위해 사용되는 개념, 연산들을 serialize 시켜준다.

thread-safe 달성을 위해 사용하는 방법들

1. **Mutual Exclusion** : 공유 자원을 꼭 사용해야 하는 경우 자원의 접근을 세마포어 등의 lock으로 통제한다. 통상적으로 사용되는 방법이다.
2. **Thread Local Storage** : 각각의 스레드에서만 접근 가능한 저장소들을 사용함으로써 동시 접근을 막는다.
3. **Re-entrancy** : 어떤 함수가 한 스레드에 의해 호출되어 실행 중일 때, 다른 스레드가 그 함수를 호출하더라도 그 결과가 각각에게 바르게 주어져야 한다.
4. **Atomic Operation** : 공유 자원에 접근할 때 원자적으로 정의된 접근 방법을 사용한다.
5. **Immutable Object** 등의 방법이 제안된다.

#### Reference

- [[OS] Thread Safe란?](https://gompangs.tistory.com/entry/OS-Thread-Safe란)
- [[운영체제] 스레드 안전 : Thread-safety (C++과 JAVA)](https://eun-jeong.tistory.com/21)

## #5

### 뮤텍스와 세마포어의 차이를 설명해주세요.

Mutex = Mutual + Exclusion (상호 배제)

여러 스레드를 실행하는 환경에서 자원에 대한 접근에 제한을 강제하기 위한 메커니즘

공유자원을 사용하는 스레드가 자원에 lock을 건다. 이후 접근하려는 스레드를  blocking해 sleep상태로 만든다. lock을 건 스레드만 lock을 해제할 수 있다.

Semaphore

멀티프로그래밍 환경에서 다수의 프로세스나 스레드가  n개의 공유 자원에 대한 접근을 제한하는 방법

#### Reference

- [Mutex vs Semaphore](https://www.youtube.com/watch?v=oazGbhBCOfU)

## #7

### CPU 스케줄러인 FCFS, SJF, SRTF, RR, Priority Scheduling에 대해 간략히 설명해주세요.

> Scheduling 정책

- Preemptive vs Non-preemptive

  - Preemptive(선점) : CPU가 프로세스를 실행 중인데, 우선순위가 더 높은 프로세스가 들어오면 CPU를 뺏음
    - Context Switching 부하가 큼
    - 응답성 높음
    - real-time system, time-sharing system에 적합 ex) 응급실
  - Non-preemptive(비선점) : 우선순위가 더 높은 프로세스가 들어와도 CPU가 실행중인 프로세스가 끝날 때까지 기다려야 함
    - Context Switching 부하 적음
    - 응답성 낮음
    - 평균 응답시간이 증가 ex) 은행 창구
- Static Priority vs Dynamic Priority

  - Static Priority(정적 우선순위) : 프로세스 생성 시 결정된 우선순위가 유지
    - 구현 쉬움
    - Context Switching 부하 적음
    - 시스템 환경 변화 대응이 어려움
  - Dynamic Priority(동적 우선순위) : 프로세스 상태 변화에 따라 우선순위 변경
    - 구현 복잡
    - 시스템 환경변화에 유연

> Scheduling criteria : 스케쥴링 척도

- CPU Utilization (CPU 이용률) : CPU가 얼마나 놀지않고 부지런히 일하는가
- Throughput (처리율) : 시간당 몇 개의 작업을 처리하는가
- Turnaround time (반환시간) : 작업이 레디큐에 들어가서 나오는 시간의 차이(병원에서 진료 받을 때..대기하고 CT 찍고, … 나오는 시간 차) 짧아야 좋음
- Waiting time (대기시간) : CPU가 서비스를 받기 위해 Ready Queue에서 얼마나 기다렸는가
- Response time (응답시간) : Interactive system에서 중요. 클릭-답, 타이핑-답. 첫 응답이 나올 때 까지 걸리는 시간

> 대표적인 CPU Scheduling Algorithms

| Preemptive | Non-preemptive |
| ---------- | -------------- |
| RR         | FCFS           |
| SRTF       | SJF            |
|            | Priority       |

Static or Dynamic Priority는 각 스케쥴링 알고리즘에 자유롭게 넣으면 됨.

- `First-Come, First-Served (FCFS)` : 먼저 온 놈 먼저 처리
  - Convoy effect
    - 수행시간 긴 놈이 먼저 들어오면 다른 프로세스의 대기 시간이 길어짐
- `Round-Robin (RR)` : 빙빙 돌면서 순서대로
  - FCFS에 time quantum 개념을 더함
    - Convoy effect 어느정도 해소
  - 지정한 time quantum이 지나면 자원 반납
- `Shortest-Job-First (SJF)` : 작업 시간이 짧은 놈 먼저 처리
  - 작업시간이 긴 프로세스는 계속 뒤로 밀림
  - 하지만 작업시간을 예측할 수 있다는 것이 비현실적
- `Shortest-Remaining-Time-First (SRTF)` : 잔여 작업시간이 더 적은 프로세스 먼저 처리
  - 잔여 작업 시간을 계속 추적해야 함 -> overhead 큼
  - 잔여 작업 시간을
- `Priority` : 우선 순위가 높은 놈부터
  - starvation
    - 우선순위가 낮은 프로세스는 계속 뒤로 밀림
    - 일정 시간 이상 기다리면 프로세스의 우선순위를 높여주는 aging 방식으로 해결

#### Reference

- [성윤님의 CPU Scheduling 정리](https://zzsza.github.io/development/2018/07/29/cpu-scheduling-and-process/)

## #8

### 동기와 비동기의 차이를 설명해주세요.

**동기(sync), 비동기(async)**에 대해 알아보기전, **Blocking, Non-Blocking** 부터 알아보자.

**Blocking** : 자신의 작업을 진행하다가 다른 주체의 작업이 시작되면 다른 작업이 끝날 때까지 **기다렸다**가 자신의 작업을 시작하는 것.

**Non-Blocking** : 다른 주체의 작업에 **관련없이** 자신의 작업을 하는 것.

> Note: 두 가지 방식의 차이점은 다른 주체가 작업할 때 자신의 **제어권**이 있는지 없는지로 볼 수 있다.

**Synchronous(동기)** : 작업을 동시에 수행하거나, 동시에 끝나거나, **끝나는 동시에 시작함을 의미**

**Asynchronous(비동기)** : 시작, 종료가 일치하지 않으며, **끝나는 동시에 시작을 하지 않음**을 의미

> Note: 결과를 돌려주었을 때 **순서와 결과에 관심**이 있는지 아닌지로 판단할 수 있다.

![Screen Shot 2022-01-02 at 8.33.22 PM](images/sync-async.png)

요약하자면 동기/비동기와 블락/넌블락은 어떤 관점에서 보는지에 따라 차이가 있다.

- **동기/비동기** : 결과에 관심이 있냐 없냐
- **블락/넌블락** : 제어권 관점

![Screen Shot 2022-01-02 at 8.38.31 PM](images/block-sync-async.png)

![Screen Shot 2022-01-02 at 8.38.55 PM](images/nonblock-sync-async.png)

[이미지출처](https://musma.github.io/2019/04/17/blocking-and-synchronous.html)

**Reference**

- https://www.youtube.com/watch?v=oEIoqGd-Sns
- https://musma.github.io/2019/04/17/blocking-and-synchronous.html

## #9

### 메모리 관리 전략에는 무엇이 있는지 간략히 설명해주세요.

메모리 관리는 메모리 location을 추적하여 얼마나 많은 메모리가 할당되었는지를 확인하고, 어느 시정에 어떤 process가 메모리가 필요한지를 결정한다. unallocated 되거나 자유로운 메모리가 발생하면 그것의 상태를 업데이트한다.

#### Swapping

Swapping은 Process를 일시적으로 main memory에서 second storage(disk)로 swap(swap out)하는 방법으로 일정 시간이 지난 후에(보냈던 프로세스가 요청이 들어 오면) OS는 해당 process를 secondary storage로 부터 main memeory로 swap back(swap in) 합니다.  Performance(성능?)은 보통 swapping process에 영향을 받지만 다양하고 큰 process를 병렬적으로 처리할 수 있습니다.

<div align='center'>
     <img src=".\images\OS_9_1.jpg">
   </div>

- **스와핑 프로세스**에 걸리는 총 시간에는 전체 프로세스를 보조 디스크로 이동한 다음 프로세스를 다시 메모리로 복사하는 데 걸리는 시간과 프로세스가 주 메모리를 다시 얻는 데 걸리는 시간이 포함

#### Fragmentation(단편화)

프로세스들이 메모리에 적재되고 제거되는 일이 반복되다보면 프로세스들이 차지하는 메모리 틈 사이에 사용하지 못할 만큼의 작은 자유공간들이 늘어가게 되는데, 이것이 **단편화**이다. 단편화는 2가지 종류로 나뉜다.

- **External fragmentation(외부 단편화)**

  메모리 공간 중 사용하지 못하게 되는 일부분. 물리 메모리(RAM)에서 사이사이 남는 공간들을 모두 합치면 충분히 공간이 되는 부분들이 분상되어 있을 때 발생한다고 볼 수 있다.
- **Internal fragmentation(내부 단편화)**
  프로세스가 사용하는 메모리 공간에 포함된 남는 부분. 예를 들어 메모리 분할 자유 공간이 10,000B 있고 Process A가 9,998B 사용하게 되면 2B 라는 차이가 존재하고, 이를 내부 단편화라 칭한다.

<div align='center'>
     <img src=".\images\OS_9_2.jpg">
   </div>

외부 단편화는 compaction(압축) 또는 shuffle memory 내용을 통해 모든 여유 메모리를 하나의 큰 블록에 함께 배치함으로써 줄일 수 있습니다. ~~압축이 가능하도록 하려면 dynamic relocation 이어야 합니다.~~

내부 단편화는 프로세스를 처리기에는 충분히 큰 것중에 가장 작은 파티션을 효율적으로 할당함으로써 줄일 수 있습니다.

#### paging(페이징)

paging은 process address space을 page라고 하는 동일한 크기의 블록으로 나누는 메모리 관리 기술입니다.(크기는 2의 거듭제곱, 512바이트에서 8192바이트 사이). 프로세스의 크기는 페이지 수로 측정됩니다.

마찬가지로 main memory는 frame이라는 작은 고정 크기의 (물리적) 메모리 블록으로 나뉘며 frame의 크기는 page의 크기와 동일하게 유지되어 main memory를 최적으로 활용하고 external fragmentation(외부 단편화)를 방지합니다.

<div align='center'>
     <img src=".\images\OS_9_3.jpg">
   </div>

- Frame에서 page로 system을 할당할 때 address translation과정(logical -> physical) 추가적으로 있는데 참고 자료 첨부합니다

**장점**

- paging 은 구현하기 쉽고 효율적인 메모리 관리 기술
- page와 frame의 크기가 동일하기 때문에 swapping이 매우 쉬움
- 논리 메모리는 물리 메모리에 저장될 때, 연속되어 저장될 필요가 없음

**단점**

- 물리 메모리의 남는 프레임에 적절히 배치됨으로 외부 단편화를 해결할 수 있으나 내부 단편화 문제점 여전히 존재.
- Page table(Address translation 과정에서 사용됨)이 추가적인 메모리 공간을 필요로 하기 때문에 작은 크기의 RAM에서는 좋은 방법이 아닐 수 있다.

#### Segmentation(세그멘테이션)

한 Process를 페이징에서처럼 논리메모리와 물리메모리를 같은 크기의 블록이 아닌 서로 다른 크기의 **논리적 단위**인 세그먼트(Segment)로 분할. 각 segemetntaion은 **연관된 기능**을 수행하고, 프로그램에 **다른 logical address(논리적 주소)**를 갖고있다.

Segmentation Memory management는 paging과 매우 유사하만 segmentation은 가변 길이를 갖고 있고 paging page는 고정된 크기를 갖고있다.

<div align='center'>
     <img src=".\images\OS_9_4.jpg">
   </div>

#### Reference

- [Operating System - Memory Management](!https://www.tutorialspoint.com/operating_system/os_memory_management.htm)
- [[CS 기초 - 운영체제] 메모리 관리 전략](!https://velog.io/@deannn/CS-%EA%B8%B0%EC%B4%88-%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%AC-%EC%A0%84%EB%9E%B5)

## #10

### 가상메모리에 대해 설명해주세요

#### 가상 메모리란 ?

메모리 관리 기법의 하나로, 기계에 실제로 이용 가능한 Memory 자원을 이상적으로 추상화 하여 사용자들에게 매우 큰 (주)메모리(RAM)로 보이게 만드는 것을 말한다.

각 프로그램에 실제 메모리 주소가 아닌 가상의 메모리 주소를 주는 방식

#### 사용 효과

이런 방식은 멀티태스킹 운영체제에 흔히 사용되고, 실제 주기억장치(RAM)보다 큰 메모리 영역 제공

다른 프로그램이 수용 될 때는 가상 메모리의 크기에 맞춰 수용되지만 실행 될 때는 실제 메모리를 필요로 함

#### 예시를 통한 설명

공부를 위해 필요한 서적이 수백권이 있을 때 책상에 모두 올리는 것은 힘들 것이다. 그래서 책장에 서적을 넣어 놓고 당장 필요한 것들만 책상으로 가져와 사용하면 해결 될 것이다. 여기서 책들은 프로그램, 책장이 가상메모리, 책상은 실제 메모리가 된다.

#### 가상 메모리 <-> 실제 메모리 변환 방법

책장에 있는 수백 권의 책들 중에서 현재 읽어야 할 책들을 보다 빠르게 보다 정확하게 옮겨줄 무언가가 필요할 것인데 이러한 mapping을 해주는 하드웨어는 MMU(Memory Management Unit)이라 부른다. 이는 CPU와 Memory 사이에 존재하여 프로그램에서 사용하는 가상주소를 메모리에 해당하는 물리적 주소로 변환하는 작업을 한다.

mapping의 방식에는 paging과 segmentation 그리고 이 둘 을 혼합한 방법 이렇게 3가지로 존재한다. 이 중 paging에서 ***page table*** 을 사용하게 되는데 이는 아래 챕터에서 확인 !

#### Page Table

paging을 하게되면 메모리 공간을 분할 하게 되고 페이지 끼리 매핑이 필요한데 이러한 가상 메모리의 page와 실제 메모리의 page를 연결 시켜주기 위해 page table(mapping table)이 필요하다.

page table은 가상메모리의 페이지 넘버와 실제 메모리의 * **페이지 프레임** 이 하나의 순서쌍으로 저장하고 있는 도표.

이러한 도표를 사용해 프로그램에서 사용되는 가상 주소 -> 실제 주소로 변환

> 도서관에서 몇 번 책장에 어떤 책이 꽃혀있다는 열람자료가 없다면 책을 찾는데 한참 걸릴 것이다 ! 여기서 열람자료가 바로 page table!

***페이지 프레임*** : 가상 메모리에서 사용되는 하나의 페이지에 대응하는 실제 메모리 영역

#### Reference

- [위키백과 - 가상메모리](https://ko.wikipedia.org/wiki/가상_메모리)
- [운영체제 가상메모리란?](https://vmilsh.tistory.com/384)

## #11

### 교착상태(데드락, Deadlock)의 개념과 조건을 설명해주세요.

교착상태  -  두 가지 이상의 작업이 서로 상대방의 작업이 끝나기만을 하염없이 기다리는 상태. 서로 자원을 놓아줄 생각은 없고 자원 요청을 무한정 대기하고 있는 상태

네 가지 조건

상호 배제 - 프로세스들이 필요로 하는 자원에 대해 배타적인 통제권을 요구하는 것. 하나의 프로세스가 공유 자원을 사용할 때, 다른 프로세스가 동일한 공유자원에 접근할 수 없도록 통제하는 것.

점유대기 - 최소 하나의 자원을 점유하고 있으면서, 다른 프로세스에 할당되어 사용되고 있는 자원을 추가적으로 사용하기 위해서 대기하는 프로세스가 필요하다.

비선점 - 다른 프로세스에 할당된 자원은 사용이 끝날 때까지 강제로 빼앗을 수 없다.

환형대기 - 공유자원과 공유자원을 사용하기 위해 대기하는 프로세스들이 원형으로 구성되어 있어 자신에게 할당된 자원을 점유하면서 앞이나 뒤에 있는 프로세스의 자원을 요구해야 한다.

#### Reference

- [교착상태](https://yabmoons.tistory.com/662)

## #12

### 사용자 수준 스레드와 커널 수준 스레드의 차이를 설명해주세요.

#### 커널 모드 vs 유저 모드

* 메모리 영역은 사용자에 의해서 할당되는 메모리 공간인**유저 영역**과 운영체제라는
  하나의 소프트웨어를 실행시키기 위해서 필요한 메모리 공간인**커널 영역**으로 나뉜다.
* 사용자가 사용하는 메모리 영역은 유저 영역이지만 C언어는 메모리 참조가 용이하기 때문에안정성 제공 측면에서 커널 모드와 유저 모드가 사용된다.
* 기본적으로 유저 모드로 동작하다가 Windows 커널이 실행되어야 하는 경우에 커널 모드로의 전환이 일어난다.

#### 커널 수준 스레드 vs 사용자 수준 스레드

* 커널 레벨 쓰레드와 유저 레벨 쓰레드는 생성 주체가 누구냐에 따라 구분된다.
* 프로그래머 요청에 따라 쓰레드를 생성하고 스케줄링하는 주체가 커널이면 커널 레벨 스레드라고 한다.
* 커널에 의존적이지 않은 형태로 쓰레드의 기능을 제공하는 라이브러리를 활용할 수 있는데 이러한 방식으로 제공되는 쓰레드가 유저 레벨 스레드이다.

#### 장, 단점

* 커널 레벨 쓰레드의 장점은 커널이 직접 제공해 주기 때문에 안정성과 다양한 기능이 제공된다.
* 커널 레벨 쓰레드의 단점은 유저 모드에서 커널 모드로의 전환이 빈번하게 이뤄져 성능 저하가 발생한다.
* 유저 레벨 쓰레드의 장점은 커널은 쓰레드의 존재조차 모르기 때문에 모드 간의 전환이 없고 성능 이득이 발생한다.

#### Reference

- [사용자 수준 Thead와 커널 수준 Thred 의 차이점은?]()

## #13

### 외부 단편화와 내부 단편화에 대해 설명해주세요.

#### 메모리 단편화(Memory Fragmentation)

- RAM애 사용가능한 메모리가 충분히 존재하지만 작은 조각으로 나뉘어져 할당이 불가능한 상태
- 메모리 단편화는`내부 단편화`와`외부 단편화`로 구분됨
  - 내부 단편화(Internal Fragmentation) : 메모리를 할당할 때 프로세스가 필요한 양보다 더 큰 메모리가 할당되어서 메모리 공간이 낭비되는 상황
  - 외부 단편화(External Fragmentation) : 메모리가 조각나 있어서 총 메모리 공간은 충분한데 실제로 할당할 수 없는 상황
    - 메모리의 할당/해제가 반복되면서 작은 메모리가 중간중간 존재

#### 메모리 단편화 해결 방법

> 외부 단편화 해결방법

1. `압축(Storeage Compaction)`

- 주기적으로 삭제 공간을 회수하여 메모리 공간을 정리
- 비용이 많이 들어 자주 쓸수 없음 -> 주기를 설정해 실행

2. `통합(Coalescing)`

- 쪼개진 메모리 공간들 중 인접한 공간들을 합쳐서 더 크게 만듬

3. `배치 전략(Placement Strategy)`

- 단편화의 발생 가능성을 최대한 줄이도록 배치를 잘하자
- 하지만 시간이 지나면... 결국 다 쪼개짐

4. `Paging 기법`

- 고정 길이 방식의 대표 유형
- 프로세스는 어차피 스케쥴링 될텐데 굳이 메모리에 연속되어 올라가야 할까?
  - 프로세스를 일정한 단위로 잘라서 메모리에 올리자!
- 가상 메모리(page table)의 단위는 page, 물리 메모리(physical memory)의 단위는 frame이라고 부름
- 외부 단편화 해결! 하지만 내부 단편화 문제 여전히 존재
  - page 단위를 더 작게 하면 내부 단편화 해결할 수 있지만 대신 page mapping 과정이 많아져서 효율 낮아짐

<div align='center'>
    <img src=".\images\os_13_paging.png", style="zoom:40%;"\>
  </div>

> 내부 단편화 해결방법

1. `Segmentation`

- 가변 길이 방식의 대표 유형
- 논리적 단위(=Segmentation)로 잘라서 메모리에 배치
  - 하나의 프로세스는 code, heap, data, stack 등의 의미있는 단위들로 구성
  - 메모리 공간을 고정길이로 하지 말고 의미있는 단위(=논리적 단위 : Segmentation)으로 자르자!
    - 보통 code, heap, stack, data 등의 기능단위로 Segmentation을 정의함
    - 그래서 각 Segmentation의 크기는 서로 같지 않음
- 내부 단편화 해결, 외부 단편화는 여전히 존재
  - 하나의 Segmentation은 메모리 공간에 연속해서 올림
  - Sharing : 메모리 낭비 방지
    - 같은 프로그램을 쓰는 복수의 프로세스가 있다면, code같은 경우는 공유 가능!

<div align='center'>
    <img src=".\images\os_13_segmentation.png", style="zoom:40%;"\>
  </div>

2. `Memory Pull`

- 필요한 메모리 공간을 필요한 크기, 개수 만큼 사용자가 직접 지정하여 미리 할당받아 놓고 필요할 때마다 사용하고 반납하는 기법

> 결론
>
>> Segmentation을 Paging하면 내외부 단편화 문제를 모두 해결할 수 있음 -> Paged segmentation
>>
>> ex)Intel 80x86
>>

#### Reference

- [메모리 단편화](https://jeong-pro.tistory.com/91)
- [다나단아님의 메모리 단편화 해결 방법](https://nevertheless-intheworld.tistory.com/8)
- [변성윤님의 메모리 이해하기](https://zzsza.github.io/development/2018/07/31/memory/)
- [정아마추어님의 메모리 단편화 해결 방법](https://jeong-pro.tistory.com/91)

## #15

#### Swapping에 대해 설명해주세요.

(Memory) Swapping은 운영 체제가 주 메모리(Main memory)에서 사용할 수 있는 것보다 더 많은 메모리를 실행 중인 응용 프로그램이나 프로세스에 제공할 수 있도록 하는 기술입니다. 주 메모리(Physical system memory?)가 다 사용되고 추가적인 resource가 필요할 때,  운영체제는 이벤트가 발생하기까지 기다리고 있는 프로세스를 Secondary Storage에 저장(swap out)을 하고 새로운 프로세스를 불러옴으로써 메모리를 효율적으로 관리할 수 있다. 이후에 보넀던 프로세스로 부터 이벤트 요청이 들어온다면 이 프로세스를 다시 수행하기 Secondary memory로 부터 main memory로 해당 프로세스를 다시 불러온다(SWAP in).

Secondary Sotrage로 보내고 다시 불러오는 과정에서 성능 저하가 좀 있으나 부족한 메모리에 더 많은 프로세스를 실행할 수 있다는 장점이 크다.

<div align='center'>
     <img src=".\images\OS_9_1.jpg">
   </div>

> swapping: 주기억장치에 적재한 하나의 프로세스와 보조기억장치에 적재한 다른 프로세스의  메모리를 교체하는 방법

#### Reference

- [스와핑이란?](https://jhnyang.tistory.com/103)
