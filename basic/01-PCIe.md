**GPU 통신 인터페이스**

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

# Infiniband / Ethernet

TODO
