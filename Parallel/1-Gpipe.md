# Gpipe
Easy Scaling with Micro-Batch Pipeline Parallelism

## 1. Introduction

larger model --> accuracy improvement

Large model challenges: Hardware constraints (e.g. memory limitation, communication bandwidth)

--> 모델을 여러 accelerator에 쪼개자 (Model Parallelism)
--> 하지만 모델패러렐 알고리즘이 architecture, task-specific 함

다양한 ml task에 보편화시킬 수 있는 scale 방법?

Gpipe: 모델을 여러 엑셀러레이터에 분산시키고 엑셀러레이터에 re-materialization을 지원하는 라이브러리

- model partitioning
- batch spliting: split `mini-batch` into `micro-batch`
- sync mini-batch gradient descent
  - gradients accumulated across all micro-batches in mini-batch
  - gradients applied at the end of a mini-batch

> iteration이 mini batch 단위로 돈다 --> mini batch 단위로 gradient update가 발생함

실험
- image classification
- machine translation

## 2. The Gpipe Library

### 2.1 Interface

User input:
- 모델 파티션 수 K
- micro-batch 수 M
- L개의 layer로 구성된 모델

DNN은 Layer의 시퀀스로 구성
레이어 `L[i]`은 forward function `f[i]`와 parameter `w[i]`로 구성
cost function `c[i]`

L개의 레이어를 K개의 복합 레이어로 분할

`p[k]`는 `L[i]`와 `L[j]` 사이의 연속적인 레이어라고 하자
- `p[k]`의 parameter는 `w[i] + w[i+1] + ... + w[j]`과 동일함
- `p[k]`의 forward function `F[k]`는 `f[j] ◦ ... ◦ f[i+1] ◦ f[i]`과 동일함
- `p[k]`의 back-propagation function `B[k]`는 `F[k]`에서 계산이 가능하다
- `p[k]`의 cost function `C[k]`는 `c[i] + c[i+1] + ... + c[j]`

### Pipeline Parallelism

Micro batch spliting

- 파이프라이닝을 해도 Bubble Time은 존재할 수 밖에 없으며, Bubble Time은 M(micro-batch)와 K(partition)과 관계가 있음.
- 정량적 분석 결과, M >= 4*K 정도가 되면 Bubble Time을 거의 무시할 수 있다고 함

### Rematerialization

- 대부분의 Layer는 내부에 Forward 입력을 저장하고 있음 (Checkpointing) --> `Activation Memory`
- Foward pass의 입력값을 가지고 있어야 Backward pass 계산이 가능하기 때문

- Rematerialization은 Layer 안에 입력값들을 저장하지 않게 해서 메모리 사용량을 줄인다
- Backward할 때 forward pass를 다시 태워서 compute하여 값을 구한다 (forward 계산을 2번 해야하는 단점)

메모리 사용량을 줄이는 대신 compute time이 늘어남. 이를 해결하기 위해서...

- (1) 중간 Layer(파티션) 들의 결과값은 저장시켜서 완전 처음부터 foward 계산하는건 방지
- (2) bubble time 동안 미리 foward 연산을 시켜둠

시간복잡도: `O(K-1/M+K-1)`


## 3. Performance Analyses
