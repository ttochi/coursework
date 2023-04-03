# Gpipe
Easy Scaling with Micro-Batch Pipeline Parallelism

### 1. Introduction

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

### 2. The Gpipe Library

#### 2.1 Interface

user input:
- 모델 파티션 수 K
- micro-batch 수 M
- L개의 layer로 구성된 모델

`Li`은 forward function `fi`와 parameter `wi`로 구성
