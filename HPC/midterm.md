## 1. Current Trends

### 1.1 멀티코어의 등장 배경

기존 Moore의 법칙은 죽었다

- ILP wall
  - 하나의 프로세서에서 동시에 실행할 수 있는 instruction 평균 갯수는 instruction 간의 dependence에 영향을 받는다.
  - single CPU의 core 수가 증가할 수록 프로세서가 동시에 실행할 수 있는 instruction 수는 그에 비례하여 증가하지만, ILP가 제한적이라면
- Power wall
  - CPU frequency를 높이면 발열량도 높아지기 때문에 이를 무한히 높일 수 없다

이를 해결하는 게 멀티코어

### 1.2 Amdal의 법칙

![](./img/042001.png)

- 프로그램에서 병렬화할 수 없는 부분이 존재하면 Amdal의 법칙에 의해 아무리 core 수가 증가하더라도 speedup은 어느 순간 병목이 생긴다.

> 추가로 core 간 통신 시간이나 cache coherence, memory consistency에 의해 성능향상이 항상 선형적으로 증가하진 않는다

## 2. Number System

2. floating point conversion (2회)
- 127을 8bit 2의 보수표현으로
- 3.14를 IEEE 754 single precision floating point binary representation으로
- 2^(-143)을 IEEE 754 single precision floating point binary representation으로
- 주어진 바이너리 표현을 decimal 로

## Dependences

4. loop dependence 코드에서 100배가 아닌 10000배 느려진 이유
- 디펜던스에 대한 설명이 필요
- 디펜던스가 성능에 어떤 영향을 주는 지 설명이 필요

11. 주어진 코드에서 루프디펜던시에서 어떤 종류의 디펜던시가 있는 지 설명하고 이를 병렬화해바라 (2회)

## Parallelism

9. SMT 프로세서가 data parallelism에 적절한지?

3. simultaneous multi-treading vs superscalar and OoO

## Cache

7. directed mapped cache가 fully associatvie cache보다 hit rate가 큰 상황은 언제인가? (2회)
- 프로그램이 접근하는 spatial data가 cache size보다 클 때?

## Virtual Memory

5. trashing에 대한 설명..?

6. VIPT의 장점에 대해 설명

8. virtual memory는 전체 사이즈가 physical memory보다 작을 때에도 유용함. 왜?

4. virtual memory
- virtual-to-physical address translation이 캐시 전에 발생하였을 때의 pro and cons
- virtual-to-physical address translation이 캐시 후에 발생하였을 때의 pro and cons

## Cache Coherence

7. false sharing에 대해 설명하고 예시 상황을 설명하라

## Memory Consistency

8. 메모리 컨시스턴시 모델 별로 변수 값을 써라 (2회)
