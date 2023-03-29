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
