Interleaved Scheduling:
- Bubble을 줄이기 위해서 각각의 device가 pipeline model parallel의 여러 stage에 해당 되도록 할당
- 이전에는 하나의 device가 연속된 레이어를 한번에 계산해서 결과 값을 뽑았었음.
  - 예를 들면 1번 device가 1~4번 레이어, 2번 device가 5~8번 레이어를 맡는다고 가정.
  - 그러면 1번 device는 1~4번 레이어에 대한 Forward & Backward 연산을 한번에 진행해서 출력했음.

- Interleaved Scheduling은 **한개 device 안에서 파이프라인을 더 분할하는 작업**
  - 1번 device가 1~2번 레이어를 연산하는 것과 3~4번 레이어를 연산하는 것을 중첩시킴.
  - 이렇게 되면 버블타임은 줄어드는대신 통신시간이 늘어나기 때문에 잘 조절해야함. (Trade Off 존재)

## Motivation
