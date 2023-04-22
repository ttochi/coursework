# ZeRO

- Activation checkpointing
- all-reduce = reduce-scatter + all-gather

## Motivation

: 모델이 커지고있다
: DP와 MP는 각각 문제가 있다

32GB 메모리에서 왜 안도는가?
메모리가 어느부분에서 소요되는지? (Where did all the memory go?)

- Model state --> ZeRO-DP
- Residual state --> ZeRO-R

### ZeRO-DP

DP + MP
