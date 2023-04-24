# Cache Tiling

캐시를 이용해서 어떻게하면 더 빠르게 프로그램을 실행할 수 있을까?  
그 방법 중에 하나가 tiling (혹은 blocking)

## Strip Mining

Vectorization(즉, SIMD)에서 많이 사용됨

Adjusts the granularity of an operation
--> 즉, 작업을 세분화한다!

loop를 더 작은 단위로 나누는 것

## Tiling for Matmul

```
for (i=0; i<N; i++)
  for (j=0; j<N; j++)
    for (k=0; k<N k++)
      C[i][j] = A[i][k] * B[k][j];
```

matrix를 메모리에 저장할 때 row-major로 값이 저장된다

이 때 cache에 가져올 때 cache-line 단위로 가져오는데,
A는 cache hit가 많이 나겠지만 B는 miss가 계속 발생할 것

> cache-line size가 m일 때, A는 N^2/m 만큼 miss가 발생하고
B는 N^2 element에 대해서 N번 씩 miss가 발생함 --> N^3

miss를 줄이는 게 중요하다! 어떻게?

```
for (kk = 0; kk < N; kk += B)
  for (i = 0; i < N; i++)
    for (j = 0; j < N; j++)
      for (k = kk; k < MIN(kk+B, N); k++)
        c[i][j] += a[i][k] * b[k][j];
```
--> k를 strip mining을 하자

Block으로 나눠서 Block을 캐시에 올려서 계산하겠다
--> improve spatial and temporal locality

> 피피티 내용의 플로우 확인할 것~~

# Cache Coherence

각 core마다 private cache를 가지고 있으면 문제가 생긴다!
--> Coherence Problem

왜?
사용자는 cache의 값을 읽을 때 가장 최근의 값을 읽어오고 싶은데 변수에 대해 여러 개의 copy가 존재한다면 그게 안될 수도 있다는 것..

하나의 프로세서가 여기를 고쳤을 때 저쪽 프로세서의 캐시에도 이 값을 업데이트 했다는게 보여져야 한다.

> "coherence" !== "consistenecy"
- coherence: 한 메모리 location에 엑세스할 때 발생하는 문제
- consistency: 여러 개의 메모리 location에 엑세스할 때 발생하는 문제

## Cache coherence in uniprocessor

cache coherence는 이러한 멀티프로세서 이전에 unipreocessor에서도 있던 문제다.

프로세서가 어떤 메모리에서 IO 디바이스 카피를 한다고 하자

- 시작주소와 크기를 DMA 엔진에게 알려주고 프로세서는 딴 일을 한다
- DMA가 작업을 다 끝내면 프로세서에게 interrupt 신호를 보냄
- 프로세서가 interrupt 신호를 보고 처리를 진행함

Q. 근데 Cache에 들어있는 데이터가 아직 메모리에 write back 되지 않은 상황인데,
DMA가 메모리에 있는 데이터를 IO 디바이스에 카피해버렸다면?

DMA가 접근하는 영역을 uncacheable region으로 정의하던가...
DMA IO가 cache flush를 하고서 되던가...
아무튼 uniprocessor에서의 cache coherence 문제는 비교적 쉽게 해결이 가능하다

## Cache coherence in multiprocessor

여러 개의 processor가 있고 각자 private cache를 가지고 있을 때 발생

- 가정: write-back, write-allocate

1. C0가 X:46을 읽었다
2. C2가 X:46을 읽었다
3. C2가 X:64로 썼다
  - 이 때, P0의 X를 invalidate 시키던가 (invalidate protocol)
  - 아니면, P0에도 X:64로 update 시키던가 (update protocol)
4. C3가 X를 읽어올 때는 C2로부터 가장 최근 값을 전달받음

> cache coherence 문제는 소프트웨어적으로도 풀 수 있지만, 컴파일러가 프로그램의 memory access information을 완벽하게 알고 있어야 함..
> 하드웨어로 해결하는게 일반적이며 cache coherence protocol이 다 잘 만들어져있어서 걱정없이 사용할 수 있다


#### Cache Coherence Protocol
- Snoopy protocol
  - 각 코어의 cache가 interconnect(bus)를 계속 snoop 하고 있다
  - 저짝 cache에서 변수에 업데이트가 발생하면 invalidate/update 시키는..
- Directory based protocol
  - Centralized directory에 누가 어떤 걸 가지고 있는 지 정보가 적혀있음
  - 이걸 이용해서 invalidate/update 시키는..

- MSI, MESI, MOESI protocol

#### Type of cache miss

1. cold miss
2. conflict miss
3. capacity miss
4. **coherence miss**

Cache coherence에 의해 invalidation 되어서 발생하는 cache miss를 coherence miss라고 한다. (메모리에서 다시 가져와야 함)

### Cache coherence in write-through cache

지금까지 write-back cache를 가정해서 얘기했는데,
write-through cache는 어떨까?

--> Write-through에서도 cache coherence 문제가 생긴다

특정 코어에서 write가 발생할 때 main memory에는 most up-to-date 값이 써지지만 다른 코어의 cache에는 안써진다

그래도 메모리에 most up-to-date 값이 있으니까 write back 보다는 cache coherence를 처리하는게 심플할 것

### False sharing

Cache block 단위로 cache를 가져오는데,

1. 메모리에 36,24 block이 있을 때
2. C0가 왼쪽에 46을 적었다 (46,24)
3. C2가 오른쪽에 64를 적었다 (46, 64)
  - C2는 가장 최근 값인 C0의 cache block을 가져와서 적은 것
4. 근데 C2에서 64를 적어줬기 때문에 C0의 cache block이 invalidate 된다
5. 그러고 C0가 왼쪽에 다시 56을 적었다 (56, 64)
  - C0는 가장 최근 값인 C2의 cache block을 가져와서 적은 것
6. C0에서 56을 적어줬기 때문에 C2의 cache block이 invalidate 된다

invalidation이 핑퐁한다!! --> 오버헤드가 굉장히 큼

실제로 C0가 쓰는 쪽은 왼쪽이고 C2가 쓰는 쪽은 오른쪽이라서 오버랩이 없는데,
cache line 자체를 share하기 때문에 이런 일이 발생한다

--> 이걸 false sharing이라고 함

> invalidate-based protocol에서는 발생하나 update-based protocol에서는 발생하지 않는 이슈임

#### 이걸 어떻게 해결할것이냐?

두 word가 같은 cache line에 있는 것이 문제임
--> data layout을 바꿔주면 됨
