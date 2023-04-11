# Parallelism

전통적인 parallelism은 3가지
1. ILP
2. Task Parallelism
3. Data Parallelism

## Task Parallelism

- 다른 종류의 task를 동시에 실행
- idle core가 생길 수 있음

## Data Parallelism

- Input을 나눠서 여러명이 처리하도록 함
- loop-level parallelism으로도 알려짐
- 같은 연산을 다른 데이터에 동시에 적용
- 데이터가 많을수록 병렬성이 증가

## SIMD
single instruction, multiple data

> 여기 다시 듣자!!

### 전통적인 SIMD

하나의 instruction 안에 데이터를 여러 개...
- vector register --> data를 여러개 담으려면 더 큰 레지스터가 필요하다
- 그리고 저런 inst를 vector instruction이라고 함
- program counter도 하나다!

9p
- core 안에서 vector inst를 써서 4배로 성능 업
- core 4개로 나눠서 4배로 성능 업

## SPMD
single program, multiple data

지금은 4개 코어가 같은 코드를 사용하고 threadId로 다르게 동작하도록 설정함

#### GPU에서의 SIMT
single instruction, multiple thread (안좋은 네이밍..)
> CUDA에서 thread라는 네이밍을 쓰지만 OpenCL의 work-item이 더 적절한 네이밍일수도

SPMD라고 봐야 함
근데 내부적으로 instruction을 실행할 때는 SIMD

# Multithreaded Processor

instruction들을
ILP를 이용하는건데...

4 issue superscalar에서 4개의 instruction들이 다 다른 thread에서 오게 하도록 한 게 multi-threaded processor다
- 하드웨어를 크게 고칠 필요가 없음
- 대신 context를 잘 유지해야 함 (register를 사용하는 set이 다 다르게 해줘야 함)

## TLP
thread-level parallelism은 data parallism이나 task parallelism을 어떻게 구현했느냐의 문제다

## Superscalar Processors

- issue width? 한번에 몇개의 inst를 동시에 이슈할 수 있는가?
- n-issue processor --> n개의 fetch, decode 유닛이 있어야 함

4-issue processor에서 어떤 때는 4개를 동시에 issue할 수 없는 때가 있다
--> 낭비되는 slot이 많음

- horizontal waste
- vertical waste

### Vertical Multithreading

- vertical waste를 줄이기 위한 멀티쓰레딩
- Switching different thread contexts each cycle
- 사실 이거는 context switching이나 마찬가지

스케줄링
- Fine-grained multithreading: 한 사이클마다 context switch
- Coarse-grained multithreading: 몇 사이클마다 context switch

하지만 아직 horizontal waste를 없앨 수 없더라

### Simultaneous Multithreading

horizontal waste도 없애보자

근데 이렇게 항상 다른 thread에서 inst를 가져올 수 있을까?
모든 thread가 똑같은 integer addition을 수행한다고 했을 때 각각의 다른 thread에서 inst를 가져와서 스케줄링이 가능한가?
- issue는 4개를 동시에 할 수 있어도
- integer add FU는 하나이기 때문에
- 그래서 많아야 30퍼의 성능향상임.. (waste가 충분히 채워지지 않아서)

한 사이클에서 놀고 있는 FU를 이용하겠다

## Homogeneous Multicores

- Uniprocessor CPU
- Multiprocessor
- Uniprocessor CPU with simultaneous multithreading
- Multicore CPU
- Multicore CPU with a shared cache
- Multicore CPU with simultaneous multithreading: 각각의 코어가 simultaneous multithreading
