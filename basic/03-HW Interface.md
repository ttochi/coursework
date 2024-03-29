잘 몰랐던 하드웨어 인터페이스들에 대해 정리해보자

---

# PCIe

## 메인보드란?
컴퓨터를 구성하는 CPU, 메모리(DRAM, SSD, HDD), GPU 등의 부품들을 하나로 연결해주는 역할
메인보드에 그래픽카드가 장착되는 슬롯을 PCIe 슬롯이라고 말한다

## PCI?
Peripheral Component Interconnect
컴퓨터(메인보드)에 하드웨어 장치(SSD, GPU ...)를 연결하기 위한 인터페이스의 규격

## PCIe?
PCI Express
과거 다양한 통신 규격이 있었는데 PCIe(PCI Express)로 통합됨
- PCIe 배속(레인): 데이터가 지나가는 길
- PCIe 버전: 3.0, 4.0 ... 버전별로 전송 속도와 대역폭이 다름

PCIe는 시리얼 통신 기술을 사용하여 여러 개의 데이터 전송 레인(레인: 독립적인 데이터 전송 경로)을 병렬로 사용하여 데이터를 전송하므로, 더 높은 대역폭을 지원

#### SSD에서의 PCIe
SSD는 SATA와 NVMe로 주로 구분
NVMe가 SATA보다 더 빠르고 비쌈

NVMe가 현재는 2종류가 있는데...
- PCIe 3.0 SSD (SATA 대비 6.25배)
- PCIe 4.0 SSD (PCIe 대비 1.4배)

# NVLink
오늘날의 서버에서 GPU는 PCIe로 연결되어 초당 12GB의 통신 대역폭을 제공

NVIDIA의 새로운 NVLink 상호 연결을 통해 GPU는 5-12배 더 많은 대역폭으로 인해 훨씬 ​​더 빠른 속도로 서로 직접 통신할 수 있습니다. 또한 NVLink 상호 연결은 GPU와 CPU 간의 고속 통신을 가능하게 하여 둘 다 동일한 속도로 시스템 메모리에 액세스할 수 있도록 하여 가속화된 컴퓨팅의 이점을 더욱 높입니다.

---

# 네트워크 카드

Network Interface Card

- 유선 네트워크 카드: 이더넷 카드, 인피니밴드 카드
- 무선 네트워크 카드: Wifi, 블루투스, 셀룰러 네트워크

## 이더넷 카드

메인보드의 슬롯 규격이 ISA --> PCI --> PCIe로 발전하면서 해당 슬롯에 맞는 이더넷 카드가 등장하고 있음

## 인피니밴드 카드

서버 간 통신을 위한 네트워크 카드 (최소 10Gbps~수백Gbps)
HPC에서는 멜라녹스 사의 장비를 주로 사용

---

# 메모리

- RAM (Random Access Memory)
  - 휘발성 메모리 (volatile)
  - 운영체제와 응용 프로그램이 동작하는데 필요한 데이터를 저장

- ROM (Read Only Memory)
  - 읽기 전용 메모리
  - BIOS나 부트로더와 같은 중요한 초기화 및 설정 데이터를 저장

> RAM과 ROM은 컴퓨터에서 메인메모리 외에도 캐시메모리, 디스크에서도 사용할 수 있음!

## RAM

### SRAM
- Static Random Access Memory
- Flip-flop으로 작동하는 방식
  - 전류 신호가 오기 전에는 상태가 변화하지 않음
- 속도가 빠르고 비싸다
- 주로 캐시 메모리에서 사용

### DRAM
- Dynamic Random Access Memory
- Capacitor로 작동하는 방식
  - 시간이 지나면 capacitor는 방전됨
  - 일정 주기로 refresh 신호를 통해 충전시켜줘야 함
- 메모리 용량이 크고 저렴하다
- 주로 메인 메모리에서 사용
