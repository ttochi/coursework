# 부동소수점

왜 중요? 퀀타이제이션!

딥러닝 모델의 경량화
- 퀀타이제이션
- 푸르닝
- 날리지디스머시기...?

32bit representation을 16bit로 줄인다
--> 모델이 반으로 줄어듦

근데 어떻게 정보를 안 잃어버리고 16bit로 줄이는가?
이걸하려면 floating point representation을 잘 이해해야

이건 고전 HPC에서 연구해왔던건데
딥러닝에서 새로 추가된 개념이, `bf16` `tensorflow16` 이런게 등장하고 있음

이걸 이해하는데 기본이 floating point representation


### 과학적 표기법

정규화된 과학적 표기법...
1 <= m < b

얘랑 비슷한게 floating point representation

### 부동소수점 표현

과학적 표기법을 사용하여 수를 표현하는 부동소수점

같은 갯수 숫자를 사용했을 때 수의 range는 부동소수점 표현이 더 큼

하지만 표현할 수 있는 수의 갯수는 부동소수점과 고정소수점이 동일

> 실수가 주어짐. 이를 부동소수점으로 나타내려면 정확하게 값이 떨어지지 않을 수 있다 --> 이는 어림잡아서 표현해야 함

64bit로 나타낼 수 있는 정보의 갯수는 2^64로 한정되어있음

그래서 어림잡아서 표현한다 --> 정확도 문제가 발생

### 표준

IEEE 754

CPU 벤더마다 부동소수점 표현 방식이 다르면 값이 달라질 수 있다.
--> 이슈! 표준이 필요!
--> IEEE에서 표준화

> 흔히 딥러닝에서 사용하는건 FP32와 FP16

우리는 FP32를 배우고 이걸 알면 다른 표현도 쉽게 이해할 수 있다

### 32비트 IEEE 754 이진 부동소수점 표현

s: 1bit
E: 8bit (exponent)
F: 23bit (fractional)

우리가 알고 있는 수를 표현하는 방법은 6p 표에서 위에 3개

### 라운딩

아까 실수는 어림잡아 표현한다고 했음

- 라운딩: 더 "적은" 수의 자릿수를 가진 값으로 주어진 수를 어림잡는 작업

그러다보니 실제값과의 오차가 발생할 수 밖에 없다

### 0의 표현

### 정규값

꼭 계산해볼 것

### 서브노멀값

아주 작은 값을 나타내기 위해서 만들었다

얘로 나타낼 수 있는 가장 작은 값이 2^(-149)

### 오버플로우와 언더플로우

서브노말 값은 exponent가 0으로 고정되어있어서 간격이 일정

언더플로우는 값이 너무 작아서 나타낼 수 없는 경우

### 무한대

IEEE 표준에 무한대에 대한 사칙연산이 정의되어있음

### NaN

허수, 복소수 (얘는 갑자기 왜 얘기가 나온거지)

### x86 80bit extended precision

96bit를 사용

왜? 80bit라며

alignmet requirement
: int를 표현하려면 주소값은 4byte의 배수여야 함
(하드웨어에서 쉽게 접근하기 위해)

그래서 80비트인데 실제로는 96비트에 저장된다

80비트는 10byte --> not 4의 배수
96비트는 12byte --> 4의 배수

그래서 96bit로 사용하고 앞에는 버린다

> 저장공간은 96bit를 사용하지만 하드웨어는 80bit를 사용하도록 설계되어있기 때문...
계산하는 하드웨어 디자인이 80bit를 쓰도록 설계가 되어있는데... 저장하려다보니 96bit
돈이 없어가지고ㅠ

####

QNaN --> 에러 발생 시키지 않고 일단 계산처리
SNaN --> NaN 발생하면 바로 익셉션을 발생

### 부동소수점 연산









.