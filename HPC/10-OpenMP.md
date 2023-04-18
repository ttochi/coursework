# OpenMP

An API for shared-memory parallelism in C, C++ and Fortran

Thread: An execution entity with a stack and associated `static memory`, called `thread-private memory`

Parallel region


**OpenMP 사용 포맷**
```
#pragma omp directive_name [ clause [ clause ] ... ] new-line
```

**include**
```
#include <omp.h>
```

**compilation**
```
#ifdef _OPENMP
#endif
```
_OPENMP가 환경변수나 #define으로 정의가 안되어있다 --> 컴파일하지 않음

## Setting number of thread

## if/private/shared 절

## Work sharing constructs

work-sharing 리전에는 임의의 barrier가 설정되어있음

work-sharing의 모든 쓰레드가 다 barrier에 도착해야 빠져나갈 수 있다

barrier는 기본으로 붙어있는데 없어도 된다고 판단이 된다면 nowait 을 사용하면 됨

## Sections Constructs

## Single Constructs
single이 달리면 하나의 쓰레드에서만 실행됨
