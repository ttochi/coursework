# Cache

Locality: 한 번 엑세스한 데이터를 다시 엑세스할 가능성 높음

이를 이용해서 memory hierarchy를 정했다.

위로 올라갈 수록 latency가 빨라서 비싼 것

> 여기서 Main memory가 소프트웨어적으로 처리되는 애고 (virtual memory)
> cache는 하드웨어적으로 해결되는 애들

## Cache 종류

https://inyongs.tistory.com/134

### Direct-Mapped Cache

set 하나 당 하나의 cache line
(set 안의 cache line을 1로 고정)

### Set-Associative Cache

set 안에 cache line을 여러 개 두어서 conflict miss를 줄이자

기존 direct mapped cache에서 miss가 나는 케이스에도 채워넣을 공간이 있도록

단, tag를 비교하는 트렌지스터가 많아지는 것 뿐

### Fully-Associative Cache

Set이 하나고 cache line이 엄청 많은 것

conflict miss가 일어나지 않도록

단, tag comparison이 많아서 복잡해짐

주소를 보면 set index가 필요 없어서 offset과 tag로만 구성이 됨

> L1 cache에서는 direct를 주로 쓰고
> L2에서는 8-way, 16-way associative를 주로 쓰고
> TLB에서는 fully associative를 주로 쓴다

## Cache Miss 타입

-   Cold miss: 캐시가 비어서 미스나는 경우
-   Conflict miss: 여러 데이터가 같은 cache line에 맵핑되어 미스나는 경우
-   Capacity miss: working set이 cache capacity보다 큰 경우 (working set 뒷부분에 가서는 결국 앞에 찬 애들을 치워버려야 한다)

> 예를들어 32k direct mapped cache를 달고 있는 컴퓨터에서 128k array data를 접근하는 경우 캐시는 array data를 모두 저장할 수 없으므로 용량 부족에 의한 캐시 미스가 발생한다.

> working set: 명확한 데피니션 X
> 자주 엑세스가 일어나는 부분의 set --> 이걸 통째로 cache에 올려버리자

## Replacement Policies

-   LRU
-   Pseudo LRU
-   FIFO
-   Ramdom

### LRU

Least Recently Used

누가 최근에 사용? --> timestamp가 필요하다
space가 많이 필요

그래서 Pesudo LRU를 사용한다

### Pesudo LRU

-   binary decision tree를 사용해서 비트 수를 줄이던가

## Write Policies

read의 경우, tag compare할 때 동시에 읽어오면 되고 miss면 버리면 됨

그러나 write의 경우는 tag compare와 동시에 일어날 수 없다 --> policy가 필요

`hit`

-   write through: cache line과 memory에 동시에 업데이트
-   write back: cache line에만 업데이트 하고 cache line이 쫓겨날 때 memory 업데이트 (dirty bit)

`miss`

-   write allocate: cache line에 가져온 다음에 그 위에 업데이트
-   no write allocate: 그 라인을 cache에 가져오지 않고 바로 메모리에 업데이트

**각각의 장단점을 이해할 것**

## Cache Performance Metircs

-   Miss Rate
-   Hit Time
-   Miss Penalty
