# Parallelism Study Overview

1. Tensor parallelism: Megatron paper #1
2. Sequence parallelism: Megatron paper #3
3. Activation recomputation(or activation checkpointing) (+ selective version): Megatron paper #3
4. ZeRO-DP (or fully-sharded DP): ZeRO
5. Pipeline parallelism + Gradient accumulation (micro-batch concept): Gpipe
6. Pipeline parallelism - better schedule: PipeDream series
7. Pipeline parallelism - interleaving stages: Megatron paper #2
8. Mixture of Experts: Switch transformer
9. CPU offloading: ZeRO-offload
10. NVMe offloading: ZeRO-infinity

Day 1
1. Gpipe (https://arxiv.org/pdf/1811.06965.pdf)
2. PipeDream (https://arxiv.org/abs/1806.03377)
3. Megatron paper #1 (https://arxiv.org/pdf/1909.08053.pdf)

Day 2
4. ZeRO (https://arxiv.org/pdf/1910.02054.pdf)
5. Megatron paper #2 (https://arxiv.org/pdf/2104.04473.pdf)

Day 3
6. Switch transformer(https://arxiv.org/abs/2101.03961)
7. Megatron paper #3 (https://arxiv.org/pdf/2205.05198.pdf)

Day 4
8. ZeRO-offload (https://arxiv.org/pdf/2101.06840.pdf)
9. ZeRO-infinity (https://arxiv.org/pdf/2104.07857.pdf)
