# 2강 Computers

## 1. 컴퓨터의 구성

**컴퓨터공학에서의 Abstraction**
- 내부 구현은 신경쓰지 않고 외적 기능만 고려하는 것
- ENIAC의 진공관의 원리를 이해할 필요 없이 그 기능만 고려하면 된다는 것
- 현재의 컴퓨터는 애니악보다 abstraction의 단계가 높아짐
- 하드웨어와 소프트웨어를 아우르는 최적화를 통해 발전
- 우리는 하드웨어를 이해할 줄 아는 sw 엔지니어가 되어야 함

### ENIAC

- Stored-program 컨셉이 아직 적용되지 않은 최초의 범용 컴퓨터
  - Stored-program 컨셉은 폰노이만 아키텍처에서 도입됨
- 프로그램을 물리적으로 작성해서 컴퓨터에 실행시킴
- ENIAC에서는 프로그래밍 언어가 부재하였고 전선과 스위치 연결을 통해 프로그래밍을 하였음

### 폰노이만 아키텍처

- Input devices
- Output devices
- Main memory
- CPU

#### Main memory

- RAM --> SRAM / DRAM (DDR)
- volatile: 휘발성. 전기 끄면 날라간다

#### Machine Code

- 컴퓨터가 이해할 수 있는 representation으로 표현한 코드
- 머신코드는 일련의 instruction으로 구성되며,
- Instruction은 sequence of bit

#### CPU

- Instruction을 실행하는 유닛
- ALU (Arithmetic Logic Unit)
  - Instruction 실행에 사용
- CU (Control Unit)
  - Instruction Cycle을 제어

> Instruction Cycle
- 메인메모리에 들어간 instruction을 CPU에 `fetch`
- instruction을 `decode`
- ALU에서 `execute`

#### Secondary Storage

- For storing programs and data
- 하드디스크, 플래시 메모리 등
- Non-volatile

### Stored Program Concept

- 폰노이만 구조의 가장 메인 컨셉
- Program과 Data는 완전히 다른 개념이지만, program을 마치 data처럼 메인 메모리에 저장했다가 읽음
  - 프로그램이 sencondary storage에서 main memory로 로드
  - 프로그램의 instruction이 실행됨
- 다른 task를 수행할 때 쉽게 프로그램 교체가 가능

### 용어 정의 (Processor, CPU, Core)

- Core
  - instruction을 실행하는 유닛 (fetch-decode-execute)
  - ALU, FPU, L1/L2 cache (private)
- Processor / CPU
  - 하나 이상의 코어로 구성됨
- Uncore
  - 인텔에서 쓰는 용어
  - CPU 안에서 core가 아닌 부분

### File

- 컴퓨터가 핸들링하는 데이터의 collection
- 모든 파일은 다 bit로 인코딩되어 구성
  - ASCII: 영문에 대한 인코딩 스키마로 7bit를 사용 (128 symbol)
  - Unicode: 모든 언어에 대한 인코딩 스키마로 16bit 사용 (65536 symbol)
- 파일의 헤더를 통해 이게 이미지인지 다른 건 지 알 수 있음

## 2. Number System

수 vs 숫자: 숫자는 추상적인 수를 표현하는 방법

### 고정소수점 vs 부동소수점

- 고정소수점(fixed point): 소수점이 특정 위치에 고정되어있다고 가정한 것
- 부동소수점(floating point): 소수점이 어디든 위치할 수 있다

### 기수

기수법: 기수는 밑
- MSD: Most Significant Digit
- LSD: Least Significant Digit

![](./img/042004.png)

#### 이진수 변환
![](./img/042005.png)

- 2진수 --> 8진수: 3개씩 끊어 (소수점 앞뒤로)
- 2진수 --> 16진수: 4개씩 끊어 (소수점 앞뒤로)

#### 십진수 변환
![](./img/042006.png)

- 십진수 --> 이진수(정수): 2를 계속 나눠준 나머지
- 십진수 --> 이진수(정수): 2를 계속 곱해준 결과 bit (소수점 몇째짜리까지 할건지 정해야 함)
