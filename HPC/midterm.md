# Midterm

## 2021

2. core 수 대비 성능 향상에 대한 설명
- 왜 core 수에 비례해서 성능이 향상되지 않는 지?

3. floating point to decimal representation

4. loop dependency 코드에서 100배가 아닌 10000배 느려진 이유
- 디펜던시에 대한 설명이 필요
- 디펜던시가 성능에 어떤 영향을 주는 지 설명이 필요

5. trashing에 대한 설명..?

6. VIPT의 장점에 대해 설명

7. directed mapped cache가 fully associatvie cache보다 hit rate가 큰 상황은 언제인가?
- 프로그램이 접근하는 spatial data가 cache size보다 클 때?

8. virtual memory는 전체 사이즈가 physical memory보다 작을 때에도 유용함. 왜?

9. SMT 프로세서가 data parallelism에 적절한지?

10. 이건 왜...?

11. 주어진 코드에서 루프디펜던시에서 어떤 종류의 디펜던시가 있는 지 설명하고 이를 병렬화해바라

## 2022

2. floating point conversion
- 127을 8bit 2의 보수표현으로
- 3.14를 IEEE 754 single precision floating point binary representation으로
- 2^(-143)을 IEEE 754 single precision floating point binary representation으로
- 주어진 바이너리 표현을 decimal 로

3. simultaneous multi-treading vs superscalar and OoO

4. virtual memory
- virtual-to-physical address translation이 캐시 전에 발생하였을 때의 pro and cons
- virtual-to-physical address translation이 캐시 후에 발생하였을 때의 pro and cons

5. 21년 7번이랑 동일

6. 21년 11번이랑 거의 동일 (코드 살짝 다름)

7. false sharing에 대해 설명하고 예시 상황을 설명하라

8. 메모리 컨시스턴시 문제같지?

---

## 1. Current Trends

### core 수 대비 성능 향상에 대한 설명
