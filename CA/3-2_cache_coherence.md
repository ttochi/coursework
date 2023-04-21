# Cache Coherence

지금까지는 single core cache에 대해서만 알아봄

multi-core design에서:
- 각 cpu는 자신만의 캐시를 가짐 --> coherence 중요
- large shared 캐시도 가짐 --> partitioning 중요

- Cache coherency problem
- Snoopy protocols
- Directory-based protocols

## Cache coherency problem

**문제상황**

- Write back policy에서
- P3는 write-back policy에 맞게 업데이트했는데
- 그 사이에 P1, P2에서 잘못된 값을 읽어오는거야

## Snoopy Protocol

### 1. Write through invalidated protocol

심플솔루션!

Writes invalidate all other caches
- 즉, u 변수에 write가 발생하면 모든 캐시에 들어있는 u 변수를 invalidate 시켜라

Basic Bus-based protocol
- All transaction over bus snooped
- P1, P2, P3의 캐시가 모두 bus에서 발생하는 event를 모니터링하고 있어야 함
- Snooped? 모니터링되고 있다 (Snoopy protocol)

2 states per block in each cache
- 각 cache block에 state bit이 존재함 (마치라잌 valid bit)

Write-through protocol is simple
- Every write is observable
- However, it use a lot of bandwidth!
- Functionality 잘 동작하지만 퍼포먼스가 안좋아ㅠ

#### Invalidate vs. Update

아까는 각 캐시 안의 변수들을 invalidate만 했다면,
이제는 update까지 한다

- invalidate는 다른 캐시가 다시 read하려 할 때 miss가 발생함
- update까지 하면 miss를 피할 수 있음
- 하지만 더이상 사용하지 않을 dead data까지 업데이트 한다

> 대부분은 invalidate protocol

### 2. MSI protocol

아까 write-through는 bandwidth를 많이 잡아먹는다ㅠ
--> 우리의 goal은 write-back policy를 쓰는거야

아까는 하나의 valid state를 썼는데
이제 state bit를 하나 더 쓰자구

### 3. MESI protocol

### 4. MOESI protocol

### Snoopy 프로토콜 단점

A major limitation of snoopy protocols --> Scalability

Ex. BusRdx to 100 cores on chip?

cache가 많은 상황에서 스누피를 적용하면 에너지 컨숨싱이 큰디


## Directory-based protocols
