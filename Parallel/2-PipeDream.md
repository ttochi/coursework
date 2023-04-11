# PipeDream
[SOSP 2019](https://cs.stanford.edu/~matei/papers/2019/sosp_pipedream.pdf)
[PipeDream_2BW PipeDream-Flush](https://arxiv.org/pdf/2006.09503.pdf)

[논문발표1](https://www.youtube.com/watch?v=ORPuUVv2_ng)
[논문발표2](https://www.youtube.com/watch?v=ILBPCi6Il1U)
[발표자료](https://www.slideshare.net/databricks/generalized-pipeline-parallelism-for-dnn-training)

## 간단정리
참조: https://www.slideshare.net/gohyunwoong/large-scalelmpart1

- Pipe Dream은 MS에서 개발한 파이프라인 병렬화 솔루션.
- Google GPipe와 약간 다른 파이프라이닝 구조를 가지고 있음.
- Pipeline Parallelism은 다음과 같은 챌린지들이 존재한다.
  - (1) Weight Version Managing
  - (2) Work Partitioning

### 1. Weight Version Managing

- GPipe의 경우 하나의 Weight 버전만 운용하지만 주기적으로 Pipeline Flush가 일어난다.
- Pipeline Flush는 계산된 Gradients를 이용해 파라미터를 업데이트하는 과정임
- 이러한 Flush 과정에서 Forward, Backward 연산도 하지 않기 때문에 처리효율이 떨어진다.
- Pipe Dream은 이러한 Flush 과정 없이 파라미터 상태를 지속적으로 관리한다.
- 만약 최신버전의 파라미터만 저장하면 이전 Layer의 출력이 다음 Layer로 전송될 때, 다음 Layer의 파라미터 부분이 이미 업데이트 되어버렸을 수도 있다.

5번 micro-batch 부터는 Forward & Backward 과정 중에 계속 파라미터 업데이트가 일어남.
결과적으로 매 스텝마다 다른 파라미터를 가지고 Forward하게 됨. => 계산 미스가 발생

- 이러한 문제를 막기 위해 PipeDream은 여러 버전의 Weight를 저장한다.
- 그러면 이미 변해버린 파라미터에 Forward & Backward 하는 문제를 막을 수 있다.
- 여러 버전을 저장하면 문제가 해결되긴 하지만 메모리 공간을 많이 차지하게 된다.
- 여기에서 Trade Off가 발생함:
  - GPipe: 메모리 효율적, 프로세싱 비효율적
  - PipeDream: 메모리 비효율적, 프로세싱 효율적

### 2. Work Partitioning

- 두번째는 Neural Net을 어떻게 쪼개는 것이 현명한지에 대한 문제.
- 단순히 Layer 별로 쪼개는 것이 별로 현명하지 않을 수 있음.
- 일단 각 파티션의 Running Time이 비슷해야 Idle Time을 최소화 할 수 있을 것임.
- 그 이외에도 Parameter Size, Activation Memory 등 다양한 요소를 고려해야 함.


- Profiling과 Optimizing을 통해 최적의 Partition을 찾아냄.
- Activation Size, Parameter Size, Compute Time 등을 고려.

### Experiments

- 단순 Data Parallelism에 비해 훨씬 빠르게 수렴하는 것을 볼 수 있음.

### PipeDream 2BW
PipeDream 2BW (2-buffered weight update): Memory Efficient PipeDream

- PipeDream이 프로세싱 효율성이 좋지만, 메모리 효율성이 떨어졌음.
- PipeDream 2BW는 이전 PipeDream에 비해 메모리 효율성을 끌어올렸음.
- 핵심 아이디어는 파이프라이닝 중에 Gradient Accumulation을 수행하는 것임.
- 여러개의 Gradient들을 모아두다가 한번에 업데이트를 수행하는 방식으로 해결.
- 단 두개의 Weight Version만 유지해도 계산에 문제가 없으며 Flush도 필요 없음.

### PipeDream Flush
PipeDream Flush: Memory Efficient PipeDream

- PipeDream Flush는 GPipe처럼 Pipeline Flush가 일어남.
- PipeDream의 1F1B (1-Forward, 1-Backward) 파이프라인과 Flush를 결함한 파이프라인.
- GPipe와 비교해서 Bubble Time은 비슷하나 Forward & Backward 중에 유지해야하는 activation 메모리가 줄어서 효율적.
- 단일 가중치만 유지하면 되기 때문에 PipeDream 2BW보다도 메모리 효율적임. (즉, 가장 메모리 효율적임)

### Interleaved Scheduling
얘는 아마 megatron 2번 논문 내용일거야..

---

1. Introduction
2. Background
  - 2.1 Intra-batch Parallelism
    - Data parallelism
    - Other DP Optimizations
    - Model Parallelism
    - Hybrid Intra-batch Parallelism
  - 2.2 Inter-batch Parallelism
  - 2.3 DNN Model and Hardware Diversity
3. Pipeline Parallelism
  - 3.1 Challenge 1: Work Partitioning
    - Solution / Profiler / Partitioning algorithm
  - 3.2 Challenge 2: Work Scheduling
    - Solution
  - 3.3 Challenge 3: Effective Learning
    - Solution / Vertical Sync / Staleness / Memory Overhead
4. Implementation
5. Evaluation
  - 5.1 Experimental Setup
  - 5.2 Comparison to Data Parallelism
  - 5.3 Comparison to Other Intra-batch Parallelism Schemes (Model, Hybrid)
  - 5.4 Comparison to Inter-batch Parallelism
  - 5.5 Microbenchmarks
6. Conclusion

---

## 1. Introduction

### 배경
DNN have become more computationally expensive to train --> require parallel execution

> DNN은 forward, backward computation의 iteration이 반복되면서 학습된다
(각 iteration에서는 minibatch를 프로세싱하고 모델 파라미터를 업데이트)

Current approach --> data parallelism, model parallelism, hybrid (`intra-batch parallelization`)

Unfortunately, intra-batch parallelization can suffer from high communication costs at large scale.
- `all_reduce` 통신 비용이 크다 (NVLink 같은 전용 인터커넥트를 쓴 서버에서도!)

### 파이프드림이란?

PipeDream --> pipeline parallelism to enable faster DNN training by combining intra-batch parallelism with inter-batch parallelization.

- divides the model among available workers
- assigning layers in the operator graph to each worker
- overlaps the computation and communication of different inputs in a pipelined fashion.

> layer는 a group of consecutive operators in DNN

### 효과

PipeDream reduce inter-worker communication!

왜?
- it limits the communication to `layer inputs and outputs` (전체 모델 사이즈보다 훨 작은)
- all-to-all이 아닌 peer-to-peer 커뮤니케이션임

> layer inputs and outputs --> activations in the forward pass and gradients in the backward pass

### 챌린지

DNN의 파이프라이닝은 traditional pipelining에는 없던 챌린지들이 있음

- DNN training은 bi-directional
  - forward pass랑 backward pass가 동일한 레이어에서 반대의 순서로 일어남
  - forward pass의 state, intermediate result를 들고 있어야 계산이 가능

Naive scheduling mechanism:
- 한 에폭의 모든 미니배치를 파이프라인에 주입
- 모든 input mini-batch에 대한 forward pass들을 완료한 후에 backward pass들을 완료한다
- 하지만, 데이터셋을 훑는 pass 수가 증가해야 모델의 퀄리티가 높아지는데, 이 방법으로는 statistical low efficiency
- gradient가 모든 학습샘플에 대해 평균화되므로 원하는 accuracy를 얻지 못할 수 있다

> **statistical efficiency?** number of iteration needed to reach a particular target accuracy  
statistical efficiency가 높다는 것은 적은 양의 데이터로도 높은 성능을 달성한다는 뜻

> **hardware efficinecy?** time needed per iteration

개선(Gpipe):
- 위의 statistical efficiency를 높이기 위해 mini-batch의 subset을 파이프라인에 주입
- mini-batch마다 weight update
- This reduces hardware efficiency due to more frequent pipeline flushes

PipeDream:
- high hardware efficiency with no pipeline stalls in steady state
- high statistical efficiency comparable to data parallelism using the same number of workers

### 1F1B scheduling algorithm

1F1B scheduling algorithm을 사용하여 하드웨어 유틸라이제이션을 data parallelism과 유사한 수준으로 유지한다

1F1B steady state
- 각 워커에서 forward, backward pass들을 번갈아 수행
- high resource util을 보장 (무시할 수 있는 stall, no pipeline flush)
- backward가 forward보다 오래걸리는 일반적인 케이스에도 잘 보장함

### weight version managing

- statistical efficiency를 위해 model weight의 버전을 관리함
- 각 스테이지의 backward pass 후 weight update
- forward pass에서 latest weight를 사용하고 corresponding backward pass가 이 weight를 사용할 때 까지 숨김
- bounded staleness가 training speed에 효과적이라고 밝혀졌으나(??), backward pass에서 정확한 gradient를 계산하려면 반드시 forward pass에서 사용한 weight version을 사용해야 함
- pipedream은 in-pipeline minibatch 수를 파이프라인을 가득 채우는 데 필요한 최소값으로 제한하여 메모리 오버헤드를 줄이려고 함

> **staleness?** 다른 노드에서 계산된 weight와 로컬 노드에서 계산된 weight 사이의 시간 차이를 의미합니다
staleness가 높을수록 weight 간의 불일치가 발생하여 성능에 악영향을 미침 --> staleness를 제한시키는 방법을 사용

### pipeline partitioning

파이프라인의 각 스테이지는 대략적으로 유사한 시간을 소요해야 한다 (파이프라이닝의 바틀넥이 slowest stage에서 생기므로)

PipeDream은 자동으로 DNN operator의 파티셔닝을 결정한다
- short profiling run on single GPU
- balancing computational load among stages
- model diversity와 platform diversity에도 효과적으로 동작한다
- 심지어 어떤 stage에서는 data parallelism을 사용할 수 있도록 결정하기도 함 (즉, 특정 stage에 worker가 여러 개 할당되어 서로 다른 minibatch를 프로세싱할 수 있음)

1F1B-RR:
- PipeDream의 1F1B 스케줄링은 data parallel stage에서 round-robin 스케줄링과 통합되도록 확장하는 동시에 backward pass gradient가 correspoing한 forward pass가 있는 worker에 할당되도록 한다
- 그러므로 pipeline-parallel training can be thought of as a principled combination of inter-batch pipelining with intra-batch parallelism

### Evaluation

multi-GPU machine에서,

data-parallel training과 비교했을 때,
- image classification 5.3배
- translation 3.1배
- language model 4.3배
- video captioning 3배

model-parallelism과 비교했을 때 2.6~15배

hybrid와 비교했을 때 최대 1.9배

Gpipe와 같이 단순한 접근법보다는 1.7배

---

## 2. Background

2. Background
  - 2.1 Intra-batch Parallelism
    - Data parallelism
    - Other DP Optimizations
    - Model Parallelism
    - Hybrid Intra-batch Parallelism
  - 2.2 Inter-batch Parallelism
  - 2.3 DNN Model and Hardware Diversity

### 2.1 Intra-batch Parallelism

Intra-batch parallelism이란?
: a single iteration of training is split across available workers.

#### Data parallelism
inputs are partitioned across workers

- Each worker maintains a local copy of the model weights
- trains on its own partition of inputs
- periodically synchronizing weights with other workers (collective communication like `all_reduce`)

가장 common한 DP는 BSP임 (bulk synchronous parallel)
- each worker should wait for gradients from other workers
- 이에 대한 최적화가 많이 이뤄지고는 있지만 large model에서 communicaiton stall은 피할 수 없음 (worker 간 gradient 싱크를 맞추기 위한 시간이 전체 computation time에서 지배적이기 때문)

Figure 1: DP에서 training 시간 중 communicaiton stall이 차지하는 비율을 보여줌 (엄청나다는겨)

Figure 1에서 4가지 확인한 점:
1. The communication overhead is high despite using multi-GPU servers and sota communication libraries like NCCL.
2. Distributed app are bottlenecked by slower inter-server links.
3. Num of workers increases, communication overheads increase.
4. GPU compute speeds increase (1080Tis to V100s), communication overheads also increase.

> 근데 DP만 이럴거같진 않은디,,ㅎㅎ

#### Other DP Optimizations

Asynchronous parallel training (ASP)
- worker에서 이전 미니배치로부터 gradient를 받기 전에 다음 미니배치를 실행시킨다
- BSP보다 하드웨어 efficiency를 높일 수 있음 (overlapping!)
- 하지만 staleness를 초래하고 statistical efficiency가 낮아짐

Quantization
- communicate해야 할 data 양 자체를 줄이자는 것
- generalize하기 어려운 방법 (effective for limited scenarios)

Using large mini-batch + LARS(Layer-wise Adaptive Rate Scaling) 이게 ResNet50에서 효과적이다
- large mini-batch --> reduce communication overhead
- 하지만 ResNet50 외에서는 generality가 낮고 pipeline parallelism이 더 좋은 실험결과를 보임ㅎ

#### Model Parallelism

The operators in a DNN model are partitioned across the available workers  
즉, 각 worker는 all input에 대해 model parameter의 subset에 대해서만 evaluation, update를 수행함  
Communicate data 사이즈는 intermediate output size(+ gradients)에 의해 결정

model parallel은 거의 사용되지 않는데, 그 이유는
1. under utilize compute resources --> Fig2에서 at most 1 GPU is active at a time
2. burden of partitioning a model

> 최근 강화학습을 통해 자동으로 model parallelism place를 찾으려는 시도가 보이지만 별로다.
우리는 DNN training을 연속적인 레이어 간의 computational pipeline으로 가정했으며 이는 최적화를 더 쉽게할 수 있게 함 (3.1에서 설명 예정)

#### Hybrid Intra-batch Parallelism

최근엔 single iteration을 multiple dimension으로 쪼개는 방법도 나옴

OWT라는 논문은
- 알렉사넷을 손으로 쪼갬
- convolutional layer에는 DP를 쓰고 (small weight parameter, large output)
- FC layer에는 replication 쓰지 않고 (large weight parameter, small output)
- 파이프라이닝을 쓴 건 아님

FlexFlow라는 논문은
- single iteration을 여로 요소에 따라 쪼개는데..
- 이 split을 어떻게 자동화된 방식으로 할지 결정하는 알고리즘을 설명하고 있음
- 얘도 파이프라이닝을 쓰진 않음

### 2.2 Inter-batch Parallelism

- 이전에 training에 파이프라이닝을 적용한 논문 있었음 --> 이건 statistical efficiency, scale 등이 고려되지 않음
- 이전에 backward pass에 대해 파이프라인 적용한 논문 있었음 --> 우리껀 forward backward 둘 다 해

**Gpipe**
- minibatch를 m개의 micro-batch로 쪼개고 forward, backward를 수행
- memory efficiency를 위해 gradient aggregation, rematerialization (foward, backward 사이의 activation stash들을 날리고 backward에서 필요할 때 re-compute)
- hardware efficiency 이슈 --> re-computation overhead, frequent pipeline flush

파이프드림은 이전 work들에서 무시된 이슈들을 처리했다

### 2.3 DNN Model and Hardware Diversity

세상에 모델 종류가 대따 많다.  
optimal parallelization --> model-dependent!

하드웨어도 다양해서 hardware dependent!
interconnect linking도 다양한 토폴로지를 가짐 --> 이거도 병렬화에 영향을 줌!

PipeDream은 이러한 다양성에서도 병렬화 작업을 자동화시켰다

---

## 3. Pipeline Parallelism

PipeDream combines intra-batch parallelism with inter-batch parallelism

**Main Idea of PipeDream:**
1. partitioning the layers of a DNN model into multiple stages (각 stage는 GPU worker에 맵핑)
2. we inject multiple minibatches into the pipeline one after the other
3. On completing its forward pass for a minibatch, each stage asynchronously sends the output activations to the next stage, while simultaneously starting to process another minibatch.
4. The last stage starts the backward pass on a minibatch immediately after the forward pass completes.
5. On completing its backward pass, each stage asynchronously sends the gradient to the previous stage while starting computation for the next minibatch

**Why Pipelining is Good?**

왜 이러한 파이프라인 병렬화가 outperforming 하는가?

1. Pipelining communicates less
  - DP보다 훨 적게 커뮤니케이션
  - No need to aggregate and broadcast the gradients
  - DP랑 달리, gradient 또는 output activation의 subset만 전달
  - DP랑 달리, all-to-all이 아니라 peer-to-peer
  - VGG16, AWD LM에서 85% 이상의 reduction in communication

2. Pipelining overlaps computation and communication
  - Figure 5: communication과 forward, background computation이 오버랩핑
  - Because communication and computation have no dependency

하지만 PP를 구현하기 위해서 PipeDream은 3가지 챌린지를 극복해야 했다!

### 3.1 Work Paritioning

- 파이프라인의 각 스테이지는 미니배치에 대해 유사한 throughput을 가져야 한다 (파이프라이닝의 바틀넥이 slowest stage에서 생기므로)
- stage allocation이 model-aware, hardware-aware 해야 함

PipeDream은 자동으로 DNN operator의 파티셔닝을 결정한다
- short profiling run on single GPU
- balancing computational load among stages
- model diversity와 platform diversity에도 효과적으로 동작한다
- 심지어 어떤 stage에서는 data parallelism을 사용할 수 있도록 결정하기도 함 (즉, 특정 stage에 worker가 여러 개 할당되어 서로 다른 minibatch를 프로세싱할 수 있음)

`PipeDream's Optimizer`
1. each stage completes at roughly the same rate
2. minimize communication across workers in a topology-aware way (large output should be sent over higher bandwidth links)
3. beyond straight pipelines, allowing a stage to be replicated (data-parallelism is used on the stage)

`Partitioning Algorithm`
- This partitioning problem has optimal sub-problem property --> dynamic programming 알고리즘을 사용한다

**Profiler**
- DNN training shows little variance in computation time across inputs
- PipeDream records 3 quantities for each layer
  1. Tl: total computation time across the forward and backward passes for layer
  2. al: the size of the output activations of layer
  3. wl: the size of weight parameters for layer
- short profiling run of 1000 minibatches on a single GPU

위 profiler에서 얻은 3가지는 optimizer의 Partitioning Algorithm의 input으로 쓰임
> + hardware topology and bandwidth, number of workers, and memory capacity of the compute devices.

```
communication time = amount of transferred data / Bi (bandwidth)

// the time for weight synchronization for layer l when using data parallelism with m workers
(m−1/m · |wl|)
```

**Partitioning Algorithm**
1. a partitioning of layers into stages
2. the replication factor (number of workers) for each stage
3. optimal number of in-flight minibatches to keep the training pipeline busy

Goal: Get partitioning of layers, replication factor, optimal num of in-flight mini-batches

자세한 알고리즘은 반드시 이해하되, 간략하게 설명할 것
+ 네트워크 토폴로지를 고려한 DP 문제라는 것을 언급해야 함!!! (초기화 값과 함께..)

### 3.2 Work Scheduling

DNN은 input minibatch가 forward를 수행하고 backward를 수행해야하는 bi-directional pipeline이다.
- -> 각 worker는 foward를 수행할 지, backward를 수행할 지 결정해야 함
- -> 플러스, replicated stage에서는 미니배치가 어떻게 route 되어야할지도 정해야 함

**startup state**
- input stage에서 steady state의 파이프라인이 꽉 채워질 수 있는 충분한 양의 미니배치를 받아야 함
- partitioning algorithm을 통해, input stage replica 당 받을 수 있는 optimal minibatch 수는 다음과 같다
  - NOAM = (worker 수) / (input stage의 replica 수) (올림)
  - Fig 4: NOAM은 4

**1F1B steady state**
- 각 워커에서 forward, backward pass들을 번갈아 수행
  - output stage가 first mini-batch에 대한 forward pass를 끝내면 바로 동일 미니배치에 대한 backward pass를 수행
  - 그다음에 이어지는 minibatch에 대한 foward, backward를 번갈아 수행
  - every stage가 backward pass propagation을 받기 시작한 다음부터는 forward, backward를 번갈아 수행
- high resource util을 보장 (무시할 수 있는 stall, no pipeline flush)
  - 파티셔닝을 통해 각 stage에서 forward, backward를 거의 동일한 시간동안 수행하기 때문
- backward가 forward보다 오래걸리는 일반적인 케이스에도 잘 보장함 (Fig 4에서 하이라이팅 되어있음)

**1F1B-RR**
- handle stage replications with deterministic round-robin load balancing
  - static policy
  - Fig 8에서 홀수 ID는 worker 1에, 짝수 ID는 worke 2에 할당 (worker 3은 모든 input을 처리)
- ensures that each mini-batch is routed to the same worker for both the forward and backward passes
  - parameters and intermediate outputs from the forward pass are needed for the backward pass.

### 3.3 Effective Learning (Weight Stashing)

naively pipelined system
- forward 때 사용한 파라미터 버전과 backward 때 사용한 파라미터 버전이 다르다
- Figure 4 (stage 1)
  - minibatch 5 forward pass is performed after minibatch 1 updated
  - backward pass for minibatch 5 is performed after updates from minibatches 2, 3, and 4 are applied
  - the gradient is computed using a different weights than forward pass
  - --> invalid gradients & model convergence X

**Weight Stashing**
- 이러한 weight 불일치를 방지하기 위해 weight version을 유지하는 stashing 기법을 사용함
- Each stage processes a minibatch using the latest version of weights available in the forward pass
- After forward pass, store the weights used for that minibatch
- The same weight version is then used to compute in backward pass

**Vertical Sync**
- Weight Stashing only ensure the weights used in forward/backward pass are the same within a stage!
  - 즉, cannot ensure the weights are the same across stages (workers)
  - Figure 9: 5번 minibatch는 1번 worker에서는 1번 가중치 업데이트를 사용하고, 2번 worker에서는 2번 가중치 업데이트를 사용한 것
- Vertical sync is an optional technique in PipeDream that eliminates the potential inconsistency across stages.
  - 즉, vertical sync를 쓰면 Fig 4에서 5번 미니배치가 모든 worker에서 1번 가중치 업데이트를 사용한다는 것
  - 5번 미니배치가 5-x weight와 associated 되어있다는 사실을 activation, gradient 정보와 함께 propagation
  - 5번 backward pass가 5-x weight를 이용해서 잘 끝났다면, 5번 weight를 만들어주고 5-x번 weight는 이만 삭제한다
- Vertical sync ensures that the same weight versions are used to compute gradients across different workers
- (이건 혼자적은거!) 그래서 pipeDream은 flush를 안하고 동일한 weight 버전으로 gradient 계산이 가능한거구나

> PipeDream default semantic: weight stashing but no vertical sync

**Staleness 측면에서의 효과**
```
Staleness에 나온 weight 계산식이 아래와 같다.
1. valina minibatch SGD
2. weight stashing 적용했을 때
3. weight stashing 적용 안했을 때 --> 이건 계산 자체가 잘못된거라고 함
4. vertical sync 까지 적용했을 때
```

**Memory Overhead 측면에서의 효과**
- Doesn’t increase memory overhead significantly compared to data-parallel training (weight stashing을 사용하더라도!)
  - 파이프라이닝 안된 model parallel --> 각 worker는 1/n 메모리를 사용 (worker가 n일 때)
  - pipeline이 n개 입력을 허용하면, weights 버전을 activation 마다 가지고 있기 때문에 최대 n배까지 증가함 (NOAM이 여기서 n/1 임.. no-replica라서)
  - 즉, PipeDream’s peak per-worker memory usage <= data parallelism

> memory footprint can be further reduced by using existing techniques: encoding or compression


---

## 4. Experiment
