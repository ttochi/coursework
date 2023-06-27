# Large-scale training

- Reducing activation memory in training
- Data vs. model parallelism to understand large-model training
- MicroSoft ZERO, DeepSpeed, and ZERO Infinity
- Training recommender systems with large embedding tables



## Reducing Activation Memory 이슈

Memory capacity 문제

- 배치 사이즈가 커지믄서 activation 크기도 커지고
- Adam같이 메모리 많이 쓰는 method들 생기면서 모델 크기도 커지고

> back propagation 하면서 weight update할 때 activation이 필요해서 들고있어야 해!

그래서 어떻게 해결?

- Activation checkpointing
  - forward pass에서 모든 레이어가 아닌 일부 레이어에만 activation 저장
  - backward pass에서 re-calculation
  - 그래서 computation overhead가 생기지만 그래도 베네핏이 큼

- Activation compression
  - 이게 바로 퀀타이제이션!

- Model partitioning
  - 모델 패러렐리즘 사용

- CPU-GPU activation transfer
  - 메인메모리에서 디스크로 스왑핑하듯이
  - GPU 메모리에서 CPU 메모리에 잠시 올려두고
  - PCIe latency를 감추기 위해 backward pass 중에 CPU 메모리에서 프리패칭해오기
  - Zero-offload 같은 경우는 아예 weight update를 CPU에서 해버리기도 해



## Large-model training 이슈

모델이 점점 커진다 1.6T 까지!!
이걸 GPU 64개로 학습이 가능할까?
개당 32G라고 해도 모델이 겨우 들어가는 사이즈임. 우리는 computation을 위한 메모리 공간이 더 필요함

> 왜? Adam과 같은 방식은 momentum v와 variance s를 각각 parameter w마다 모두 들고 있기 때문에
model state가 트리플로 증가!

- 그래서 나온게 Zero와 DeepSpeed
- MP / PP / DP

### Data parallelism

- DP는 각 GPU가 같은 model parameter를 들고 있고 서로 다른 data batch를 입력받아서 학습시킴
- training을 하면 각 GPU의 모델은 서로 다른 weight update를 가지고 있음
- We need to calculate their average value! --> **All Reduce**

### Model parallelism

- 각 노드가 activation을 copy해서 모아온다 --> **All gather**
- MP는 GPU 노드 (DGX) 내부에서만 가능함ㅠ (GPU 노드 안에서 NVLINK를 사용해서 bandwidth를 높임)
- activation transfer에 있어 노드 간 통신(Infiniband)은 느림

### Pipeline parallelism

- PP can amortize the cost of large activation transfer across layers
- 왜냐면 N개의 layer가 하나의 stage로 묶여서 activation transfer가 N번 중 한번씩 발생하므로



## ZERO / DeepSpeed

### ZERO-DP

- model state
  - parameter, adam optimizer state
- residual state
  - activation, temporal buffer

그냥 DP는 각 디바이스가 모델을 다 들고있는디
Zero-DP는 모델 state를 파티션해서 가지고 있음 --> dynamic communication

DP에서는 각 디바이스에서
- model shard --> forward --> backward --> all-reduce(grad) --> update weight

Zero-DP에서는 각 디바이스에서
- model shard --> all-gather(weight) --> forward --> all-gather(weight) --> backward --> reduce-scatter(grad) --> update weight

MP랑 비교하면 어때?
- MP는 activation을 all-gather 시키고
- Zero-DP는 model state를 all-gather 시킴
- MP만 쓰는거는 activation이 크기 때문에 오버헤드가 큼
- ZeRO는 activation 대신 parameter를 transfer하기 때문에 오버헤드를 줄여줌 (`140GB vs. 7GB * 2`)

### DeepSpeed

- 3D parallelism
  - MP는 GPU 노드 내에서 파티셔닝하여 activation을 GPU 노드 (DGX) 내부에서 빠른 communication을 사용하여 transfer (NVLINK)
  - PP는 GPU 노드들에 N-layer 씩 파티셔닝하여 GPU 노드 간 통신을 통해 activation을 transfer (activation 크기가 크지만 N layer 중 한번씩만 발생)
  - ZeRO-DP는 GPU 노드들에 parameter를 파티셔닝하여 GPU 노드 간 통신을 통해 parameter를 transfer

4096 A100 GPU를 써서
2TB 모델 --> 8-way MP `256G` --> 64-way PP `4G` --> 8-way ZeRO-DP `0.5G`

- ZeRO-Offload

### ZeRO-infinity





## 라지 추천 시스템

모델은 각 gpu에서 들고있고…
임베딩 테이블 사이즈가 너무 커서 distribute

User input --> embedding table에 접근 (all-to-all communication)

가져온 embedding vector로 training한 MLP는 all-reduce



## 정리

현재 라지 모델 트레이닝에 있어서 바틀넥은 스몰 HBM size다

낮은 cpu-gpu bandwidth때문에 CPU의 메모리를 잘 활용하지 못했다

노드간 커뮤니케이션이 느렸다

### Scale up trend (intra-node)

- Larger DRAM for GPU
- Pooled Memory (CXL)

### Scale out trend (inter-node)

- faster inter-node network
