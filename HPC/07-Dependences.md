# Dependences and Pipelineing

## Compile Process

GCC

1. preprocessing
  - `#define` pragma 혹은 compiler directive 라고 하는데, 이걸 젤 먼저 처리한다
  - directive가 없는 c 소스코드를 뱉어냄
2. Compilation
  - object를 만듦
  - 하지만 printf같은게 여기 들어가있지 않아 --> 워낙 많이 쓰는 라이브러리니까
3. Linking
  - shared library를 object file에 합치는 것
  - 최종적으로 c 프로그램이 나옴

### Compilation vs. Interpretation

인터프리터는 한줄한줄 실행
인터프리터는 안에 이미 generation된 function(머신코드)를 이용해서 코드를 실행
인터프리터는 펑션콜이 일어나기 때문에 오버헤드가 있음

> 인터프리팅 언어를 컴파일 언어로 바꾸면 500~1000배 정도 빨라짐

> 인터프리트 언어는 프로토타이핑할 때는 좋은데, 소프트웨어 프로덕션할 때는 좋은 언어는 아님

## Dependence

### 싱글코어 퍼포먼스

1. instrucion pipeline
2. on-chip cache

이 2가지 하드웨어 테크닉이 엄청 성능을 높였다.
이걸 설명하려면 dependence에 대한 이해가 필요함

### Dependence

두 컴퓨테이션 간의 실행 순서에 대한 관계

1. Flow dependence
  - True dependence
  - Read after write

2. Anti dependence
  - Write after read

3. Output dependence
  - Write after write

4. Input dependence
  - Read after read
  - 얘는 진짜 디펜던스가 있는 건 아님

Parallel에서 가장 중요한 원리가 바로 이 디펜던스를 지키는 것!

### Control Dependences

#### Basic Block?

그림에 나와있는 코드는 basic block 단위로 나뉜 것
statement들의 집합으로 중간에 빠져나갈 수 없다

만약 2번블락 + 3번블락이라면?
중간에 goto L0가 있어서 중간으로 들어가기 때문에 basic block을 나눠놔야 함

2개의 연속적인 inst가 하나의 basic block에 들어가있으면 앞에께 실행되면 뒤에꺼가 실행되는게 보장이 된다는 것

그럼 베이직 블락을 나누는 알고리즘은?
1. leader를 identify하는 것 (제일 첫 inst)
2. 어떤 inst가 branch의 타겟이 되면 리더다
3. if문 다음에 나오는 conditional jump / goto로 나오는 uncoditional jump 도 리더다
4. 리더부터 시작해서 다음 리더 앞까지가 베이직 블락이다!

베이직블락은 컴파일러에서 자동으로 처리

#### Control-Flow Graphs

그림처럼 베이직 블락을 나눈 다음에 control flow graph를 생성 (with dummy entry, exit node)

#### Control Dependences

control flow graph를 이용해서..

Q의 실행은 P의 실행에 대해 컨트롤 디펜던트 하다!
즉, Q의 실행을 P보다 앞에 둘 수 없다!

디펜던스가 있으면 실행순서를 못바꾼다 --> 그말인 즉슨 병렬처리를 못한다는 것!!!
이게 이번 학기 가장 새겨들어야 할 근본적인 원리다

---

## Instruction Pipeline

frequency가 높다 --> 깊은 pipeline을 구현할 수 있다 (pipeline stage를 높인다?)

typical 5-stage pipeline

데이터 헤저드는 데이터 디펜던스랑,
컨트롤 헤저드는 컨트롤 디펜던스랑 관련이 있는거고
스트럭쳐 헤저드는 이름 그대로 리소스와 연관된 것

### Data Hazard

1. stall (bubble)
: 하드웨어적으로도, 소프트웨어적으로도 가능 (`no-ops` 추가)

2. Data Fowarding
: 데이터를 미리 넘기는거 (WB 전에)

3. Transparent register file
: 사이클을 반으로 쪼개서
: WB 단계에서 값을 바로 읽어올 수 있도록

### Structural Hazard

Resource conflict 때문에...

### Control Hazard

PC
- fetch 다음에 업데이트
- branch inst에서 값을 수정

컨트롤헤저드는 브랜치 때문에 발생하는데,
파이프라인이 branch 명령에서 MEM 단계까지 가기 전까지는 다음 inst가 뭔지 몰라

그러면 branch 이후에 수행했던 파이프라인 명령을 다 flush 해야 해

가장 간단한 방법 --> Stall

1. stalling (3 사이클정도 손해)
: 하드웨어적으로..?
: branch predictor를 추가 --> 미리 예측 (cpu에서 굉장히 많은 면적을 차지하는 복잡한 트렌지스터임)

> GPU 프로그래밍할 때 가능한 branch를 없애는 게 좋다

2. 컴파일러
: instruction scheduling
: no-ops를 추가하거나
: 앞에 실행할 명령을 뒤로 넣어주거나 (디펜던스가 없어야지 가능)

> loop unrolling (질문 나와서)
브랜치의 수를 줄이므로서 성능을 높인다는 건데
근데 하드웨어 브랜치 프레딕터 들어가있으면 큰 효과는 없음

### 결론
최적화를 위해 파이프라이닝
--> 하지만 그러다보니 디펜던스에 의해 이런 hazard 문제가 생기더라


## In-order Execution

instruction 들어온 순서대로 fetch-decode-execute를 실행하는 것

하드웨어가 명령별로 나뉘어져 있고,

> 그림이 뭔가 병렬적으로 수행할거같지 생겼지만~~ 병렬적으로 수행하고싶게 보이라고 그려진 그림이고 execution 자체는 들어온 순서대로 수행됨 (그래서 OoO로 가자...)

## Out of Order Execution

`reservation station`이라는 queue에 "issue" 한다

> 앞서 FU에 던지는걸 dispatch 한다고 하는데, reservation station에는 issue한다고 함. 그리고 reservation station에서 execute state로 가는 걸 dispatch라고 함.

`reorder buffer` --> 실행은 순서를 바꿔서 했어도 실제 프로그램에 나온 순서대로 state가 바뀔 수 있도록 정리

- Tomasulo Algorithm

- Retirement (graduation)
: exception이 발생했거나 해서 commit하면 안될 때 --> retire
: retire? reorder buffer slot is freed

### superscalar

OoO도 많은 종류의 FU를 써서 parallel하게 만든건데...

아까봤던 OoO 파이프라인 그림에서 fetch decode를 동시에 실행하는 수를 늘린다.

동시에 늘리면서 반드시 디펜던스는 지켜야한다!

execution도 동시에 4개? ㄴㄴ...

> 현대의 CPU processor는 전부 파이프라이닝이 되어있고 수퍼스칼라다

슈퍼스칼라는 `다이나믹`하게 `OoO execution` + `동시에 fetch decode` 를 수행한다.

### VLIW Processor

Use a long instruction word
that contains a fixed number of instructions that are fetched, decoded, issued, and executed synchronously

> 프린터에서 많이 씀
pdf 점을 어디에 찍어야하는가에 대한 언어라고 봄
