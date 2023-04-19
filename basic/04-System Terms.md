# 32bit 64bit 머신

## Word size
- 컴퓨터의 데이터 기본 처리 단위
- 32bit, 64bit 라는 것은 word 사이즈를 의미함

## CPU와 운영체제 조합

- CPU에서 32bit, 64bit를 지원 --> 하드웨어 레벨
- 운영체제에서 32bit, 64bit를 지원 --> 소프트웨어 레벨

| | 32bit OS | 64bit OS |
| --- | --- | --- |
| **32bit CPU** | O | X |
| **64bit CPU** | O (하위호환) | O |

## 32bit 시스템 vs. 64bit 시스템

주요 차이: Memory addressing space size

- register size --> 4byte / 8byte
  - PC 사이즈도 4byte / 8byte
- 메모리 주소공간을 32bit / 64bit 씩 할당하게 됨
  - 메모리 주소 하나 당 1byte에 맵핑
- 32bit 시스템에서는 프로세스 당 최대 4GB virtual memory address space를 사용할 수 있음 (2^32byte)
- 64bit 시스템에서는 "이론적으로" 프로세스 당 최대 16EB virtual memory address space를 사용할 수 있음 (2^64byte)
  - 이는 즉, 4GB 이상의 RAM에 접근이 가능하다는 뜻 (현재 대부분의 운영체제는 이 주소공간의 일부만을 사용하고 있음)

# ISA (Instruction Set Architecture)

- 명령어 집합을 정의하는 interface
- ISA는 CPU가 인식하는 명령어와 그 명령어가 수행하는 작업, 하드웨어 리소스(register, memory)에 대한 접근 방법 등을 정의

## ISA 종류

- x86
  - 인텔과 AMD에서 개발한 ISA 중 하나로 PC 및 서버에 많이 사용되고 있음
  - 초기에는 16bit 아키텍처였으나 현재 64비트 환경도 지원 (x86_64 == x64)
  - CISC 아키텍처

- ARM
  - ARM에서 개발한 ISA 중 하나로 모바일기기나 임베디드에서 사용되고 있음
  - RISC 기반 아키텍처로 고성능 저전력

- MIPS
  - MIPS technology에서 개발한 ISA 중 하나로 고성능 컴퓨팅 시스템이나 네트워크 장비에서 사용됨
  - RISC 기반 아키텍처로 명령어가 간단해서 고성능에 적합

- PowerPC
  - IBM, Motorola, Apple 등이 공동 개발한 ISA 중 하나로 고성능 컴퓨팅 시스템이나 게임 콘솔에서 사용됨
  - RISC 기반 아키텍처로 고성능, 신뢰성이 요구되는 분야에 적합

### RISC? CISC?

컴퓨터 아키텍처에서 ISA 설계 방식을 나타내는 개념

CISC: Complex Instruction Set Computing
- 명령어의 복잡도가 높아서 하나의 명령어로 여러 동작 수행이 가능
- 명령어 실행에 필요한 cycle 수가 많음
- 명령어 실행을 위해 메모리 엑세스가 많이 발생하며 하드웨어 복잡도가 높음

RISC: Reduced Instruction Set Computing
- 복잡도를 낮추고 하나의 명령어가 하나의 동작만을 수행
- 한 cycle에 한 명령어가 수행되도록 함
- 명령어와 데이터를 별도의 캐시 메모리에 보관하고 하드웨어 복잡도를 줄임

> CISC에서는 ADD 명령어 하나에서 값을 더하고 더한 결과를 다시 메모리의 저장하는 과정을 한번에 처리할 수 있음
