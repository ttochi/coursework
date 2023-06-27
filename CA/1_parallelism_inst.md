Parallelism은 크게 3가지로 구분됩니다.
1. Instruction
2. Data
3. Thread

# Instruction Level Parallelism

## 1. ILP에서의 Dependence

### ILP 란?
- 하나의 `superscalar 프로세서`에서 동시에 실행될 수 있는 `instruction 평균 갯수`
- 이는 inst 간의 dependence에 영향을 받는다
- Application 자체의 특성으로 응용프로그램에 든 ILP는 한정되어있음 (ILP Wall)

#### 슈퍼스칼라 프로세서란?
: 일반적 파이프라인 기법의 확장을 통해 CPU의 속도를 향상시키기 위한 컴퓨터 구조 설계 방식

![img](./img/030801.png)

### ILP에서의 제약사항
1. Resource Conflicts
2. Dependence

Dependence도 크게 3가지로 나누는데,  
그 중 Data dependence가 찐 디펜던스고 (그래서 true dependence)  
Name dependence(Anti/output)는 사실상 resource conflicts에 의해 발생

### Dependence

1. True dependence: `RAW hazard`
  - Read After Write
  - 이 depnedence에서는 반드시 선행 inst가 먼저 실행되어야 함
  - Cannot be executed simultaneously

2. Anti dependence: `WAR hazard`
  - Write After Read
  - 이 경우에도 inst의 실행순서가 지켜져야 함
  - 하지만 2번째 inst의 r1을 r6로 바꾼다면? 병렬로 실행이 가능!

3. Output dependence: `WAW hazard`
  - Write After Write
  - 이 경우에도 inst의 실행순서가 지켜져야 함 (이후에 r3를 참조할 때 마지막 inst의 결과가 참조되어야 하니까)
  - 하지만 이 또한 2번째 inst를 r3가 아닌 다른 레지스터를 사용한다면? 병렬로 실행이 가능!

Name Dependence의 솔루션:
더 많은 register를 사용한다 --> **Renaming resource**

> Renaming에 implicit/explicit 방법이 있는데, explicit한 renaming은 하드웨어 레벨의 테크닉이기 때문에 우리는 implicit에 집중할 것

## 2. Dynamic Scheduling

### Dynamic Scheduling이란?

Dynamic Scheduling이란? Rearrange order of Instruction

실행 중인 명령어들 사이에서 의존성을 파악하여 실행 순서를 재조정

- **why?** 실행시간(stall 즉, idle cycle)을 줄이기 위해!
- **how?** instruction들의 data flow를 유지하면서!

> 컴파일러는 ISA에 대한 abstraction을 보고 있고 CPU에서 insturction을 re-ordering 하는 것

> 즉, 컴파일러에서 다이나믹 스케줄링을 고려할 필요는 없다!
> 그러나 CPU에서 control overhead가 발생하는 것은 단점이긴 함

#### Static vs. Dynamic

학부 컴퓨터구조에서 배웠던 MIPS 파이프라인 구조에서는 dynamic scheduling 기법이 적용되어있지 않음

Static Scheduilng: MIPS에서는 명령어가 순차적으로 실행됨.

> 물론 MIPS에서도 dependence 이슈를 해결하기 위해 fowarding과 같은 기법을 사용하지만, 이러한 기법들은 dynamic scheduling과 함께 사용될 때 더욱 효과적으로 성능을 개선할 수 있음

Dynamic scheduling 방식을 사용하면 static 대비 명령어의 수행 순서를 최적화하고, 파이프라인 실행률을 향상시킬 수 있다

#### Dynamic Scheduling의 대표적인 방법

1. Scoreboard
2. Tomasulo Algorithm

### Scoreboard

다이나믹 스케줄링의 가장 기초적인 CPU 구조

`Register + Functional Unit + Scoreboard`

![img](./img/030802.png)

- Functional Unit
  - mutiplier
  - divider
  - adder
  - integer unit (for memory access)
- Scoreboard
  - <u>manage status of functional unit and availability of registers</u>

#### 4-stages pipeline

예시에서는 4-stage pipeline을 볼 것

**1. Issue**
  - `Structure hazard` 확인
  - 이는 곧 availability of functional unit (e.g. 하드웨어 구조적으로 지금 multiplier가 available한 지?)
  - 또한 `WAW hazard`도 확인
  - Structure, WAW hazard가 생길 가능성이 있다면 issue를 하지 않는다 (stall)

**2. Read operand**
  - `Data(RAW) hazard`가 없는 지 확인하고 수행된다 (RAW hazard는 이 단계에서 resolved)
  - We read operands only when they are ready
  - 참고로 Scoreboard에서는 pipeline fowarding이 적용되지 않음

**3. Execution**

**4. Write result**
  - `WAR hazard`를 확인
  - WAR hazard가 생길 가능성이 있다면 write하지 않는다 (stall)

#### Scoreboard component

**Notation**
- Fi:	Dest register
- Fj,Fk: Source-register numbers
- Rj,Rk: status of register (Yes means ready)
- Qj,Qk: Which FU waiting for it

**FU latency**
- Add: 2 clock
- Mul: 10 clock
- Div: 40 clock

#### Scoreboard example

```
PPT 15p~41p 예제 반드시 따라가기
```

![img](./img/042201.png)

#### Out-of-order execute

In-order issue; Out-of-order execute  
이슈는 program 순서대로 되었지만 실제 실행은 순서대로 진행되지 않음  
**By doing out-of-order execute and commit, we can reduce the total cycle of this program**

#### Limitation in Scoreboard

Better solution: Tomasulo

1. 포워딩을 하지 않았음 --> Common Data Bus를 통해 해결!
2. Wait for WAR hazard in write --> additional register를 통해 해결!
3. Prevent WAW hazard in issue --> additional register를 통해 해결!
4. Prevent structual hazard in issue --> 얘는 토마슐로도 똑같음

### Tomasulo

다이나믹 스케줄링에서 엄청 유명한 알고리즘

특히 WAR, WAW hazard와 같은 resource conflict를 해결하기 위해 Register Renaming한 부분을 집중해서 볼 것

`6 Load unit + Reservation station(3 Add + 2 Mult) + Common Data Bus`

![img](./img/030803.png)

#### Reservation station

Reservation station은 토마슐로에서 사용되는 Additional register를 의미함 (`renaming`)

Reservation station에서 제공하는 additional register를 사용하므로써 WAR/WAW hazard를 resolve 시킬 수 있음  
--> 이제 RAW hazard만 남게 됨

**Reservation Station Components**
- Op: operation type
- Vj,Vk: operand들의 "값" --> content copy 개념!
- Qj,Qk: Which RS waiting for it
- Busy: RS 또는 FU가 busy한 지 여부

#### Common Data Bus

Common Data Bus는 Reservation Station과 연결됨  
Mem에 있는 데이터를 "directly" adder와 multiplier에 보낸다

즉, CDB `foward` the result of FU directly to waiting FU

#### 3-stages pipeline

토마슐로에서는 3-stage pipeline로 simplified

**1. Issue**
  - Reservation Station이 free한지 확인
  - 이는 곧 `Structure hazard`를 방지

**2. Execution**
  - 모든 operand가 ready가 될 때 까지 Common Data Bus를 watch

**3. Write result**
  - Common Data Bus에 write하고
  - reservation station이 available하다고 마킹

#### Tomasulo example 1

```
PPT 51p~72p
```

> additional register에 content가 바로 저장되는 것과 operator 결과가 broadcasting 되는 것에 주목!

> Assume: Load 실행에 2 cycle 소요

![img](./img/042202.png)

#### Tomasulo vs. Scoreboard

Key difference:
1. Reservation station의 additional register를 통해 `Renaming`을 했다는 것
2. Common Data Bus의 브로드캐스트를 통해 `Fowarding`을 구현한 것

물론 다른 아키텍처적인 차이도 있음 (e.g. 슬롯 갯수의 차이)  
그런데 가장 key diff는 저 2개라는 것

#### Tomasulo example 2

```
PPT 74p~96p
```

---

<!-- TODO -->

## 3. Explicit Renaming

토마슐로 솔루션에서는 Additional register를 이용해서 Implicit register renaming을 사용

다이나미컬리 physical register를 logical register에 맵핑하겠다
--> 맵핑 테이블을 관리해서

![img](./img/042203.png)

#### Basic principle
- 데이터를 레지스터에 write할 때 새로운 physical register를 allocate 시키고 맵핑테이블을 업데이트한다.
- 데이터를 읽을 때는 맵핑테이블에서 logical register에 맵핑되는 physical register를 확인한다

#### Explicit Renaming Example

additional physical register를 사용했고
Rename table을 통해 맵핑했다

```
PPT 104p~122p
```

## 4. Branch Prediction

1. simple한 방법

- branch 주소에 대한 branch history table을 가지고 이전의 결과값을 그대로 따른다
- 여기서 1bit만으로 history를 관리하지 않고 2bit를 사용하면 예측 정확도를 높일 수 있다

2. 문제점

- 근데 branch history table이 무작정 클 수 없으므로 여러 branch 주소가 같은 table entry를 바라볼 수 있다
- 특정 branch A의 결과에 따라 branch B의 결과가 결정되는 경우가 있음

3. two-level branch predictor

- two-level branch predictor를 사용하여 이전 브랜치의 결과와 현재 브랜치 주소를 함께 사용하여 결과를 예측한다
- 방식이 GAg, GAs, GAp와 같은 스키마가 있음
- Gselect는 BHR(branch history register)의 LSB와 PC의 LSB를 조합하여 branch history table에 접근
- Gshare는 BHR과 PC를 XOR 시킨 값으로 branch history에 접근

## 5. 토마슐로 + Branch Prediction

이전에 본 dynamic scheduling은 out-of-order completion 임
- post-interrupt, mis-prediction이 발생하면 write한 걸 다 취소해야 한다.
- register, memory에 data를 쓰기 전에 branch prediction이 맞는 지 확신을 가져야 한다.

**Key idea**
- in-order completion while doing out-of-order execution
- in-order completion을 실현하기 위해서 reorder buffer가 필요함

### Reorder buffer

Reorder buffer를 가진 토마슐로
- Register file이나 reservation station에 데이터를 적고,
- Common data bus로부터 데이터를 읽어옴

Register file에 결과를 바로 저장하는게 아니라 temporal한 공간에 먼저 저장해두자!

### 4 Stage Pipeline for Speculative Tomasulo

1. Issue: issue된 instruction에 reorder buffer entry를 할당

2. Execution: operands가 ready가 되면 execute, not ready라면 common data bus를 모니터링

3. Write result: common data bus를 통해 FU와 reorder buffer에 temporal result를 저장

4. Commit: reorder buffer에 저장된 result를 register file에 write

### Speculative Tomasulo Example

```
160p~
```

![img](./img/042204.png)


#### Reorder buffer that store information
- currently running instructions
- destination register
- temporal value of instruction
  - branch prediction 때문에 우리는 몇몇 Speculative한 instruction을 실행하고 있을 거다.
  - 그래서 temporal result가 reorder buffer에 적히게 됨

#### Reorder number
- index of instruction in reorder buffer
- 이 값이 있으면 아직 이 instruction의 execution이 끝나지 않았다는 것

그래서 160p의 reorder number의 의미는
- current result of F6 can be found in 1st entry of reorder buffer
- temporary result는 reorder buffer에 저장되고, 실제 값이 써질 때 commit할 것
- F6는 1st entry가 commit될 때 까지 기다린다

#### Head, tail pointer

- 1번 inst가 끝났다
- #1의 Vaule에 값이 씌여졌다
- head 포인터를 옮겼다
- 1번 inst는 cancel할 수 없음!!

그렇기에 head 포인터가 중요하다

중간에 브랜치 명령에서 mis-prediction이 발생했을 때, 모든 execution들을 flush 시키고 correct path에서부터 다시 fetch한다.

### Summary

![img](./img/042205.png)

- Out-of-order execution
- In-order issue, commit

한계점:
- Too many data copies
- Common data bus가 reorder buffer에 붙으면서 복잡해짐
- reservation station에 data와 tag가 함께 섞이면서 복잡해짐

## 6. Multi Issue

Dynamic scheduling + multiple issue + speculation

### Multi issue without speculation
![img](./img/042206.png)

- 각 cycle에서 2개의 instruction을 issue할 수 있다 (branch는 하나만)
- 가능한 많은 inst를 issue해야 병렬화의 가능성이 높아짐
- Speculation이 없다면, 2nd iteration 이후의 명령은 7 cycle이 끝나기 전까지 실행할 수 없다

### Multi issue with speculation
![img](./img/042207.png)

- Speculative에서는 7 cycle의 branch 명령이 실행되기 전에 2nd iteration도 execution 시켜둘 수 있음
