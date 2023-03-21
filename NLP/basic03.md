# Recurrent Neural Networks

앞서 배운 피드 포워드 신경망은 입력의 길이가 고정되어 있어 자연어 처리를 위한 신경망으로는 한계

다양한 길이의 입력 시퀀스를 처리할 수 있는 Neural Network --> RNN

RNN -> LSTM -> GRU

## 1. RNN

RNN은 은닉층의 노드에서 활성화 함수를 통해 나온 결과값을 출력층 방향으로도 보내면서, 다시 은닉층 노드의 다음 계산의 입력으로 보내는 특징을 갖고있습니다.

hidden layer의 노드를 cell이라고 부름

hidden state
- cell이 다음 시점인 t+1의 자신에게 보내는 값
- 즉, t시점의 cell은 t-1 시점의 cell이 보낸 hidden state를 input으로 사용함

> RNN에서의 용어 정리: 입력 벡터, 출력 벡터, hidden state  

![img](https://wikidocs.net/images/page/22886/rnn_image2_ver3.PNG)
위 그림에서의 네모들은 벡터 단위를 가정함
이 네모들을 기존 FFNN에서의 뉴런단위로 표현하면 아래와 같이 펼쳐질 것임

![img](https://wikidocs.net/images/page/22886/rnn_image2.5.PNG)
time step 2개에 대해서만 표현된 그림  
입력벡터크기: 4, 출력벡터크기: 2, hidden state 크기: 2

### RNN 수식 정의

가중치 값은 하나의 RNN layer에서 모든 시점에서 동일한 값을 가질 것
물론 시점마다 출력되는 hidden state는 당연히 다르겠지만

```
ht = tanh(Wx*xt + Wh*ht-1 + b)
```

### Deep RNN

RNN도 다수의 hidden layer를 가질 수 있다

### Bidirectional RNN

2개의 cell을 사용

다른 인공 신경망 모델들도 마찬가지이지만, 은닉층을 무조건 추가한다고 해서 모델의 성능이 좋아지는 것은 아닙니다. 은닉층을 추가하면 학습할 수 있는 양이 많아지지만 반대로 훈련 데이터 또한 많은 양이 필요합니다.

## 2. LSTM

Simple RNN은 비교적 짧은 시퀀스(sequence)에 대해서만 효과를 보이는 단점
time stamp가 길어질 수록 앞의 정보가 뒤로 충분히 전달되지 못하는 현상이 발생

LSTM은 Simple RNN의 변형 (Long Short-Term Memory)
긴 시퀀스 입력을 처리하는데 탁월한 성능을 보임

### LSTM

hidden state 계산식이 복잡해지고 cell state 개념이 추가됨

ht와 ct를 계산하기 위해 3개의 게이트를 사용
- input gate
- forget gate
- output gate

#### 1) input gate

![img](https://wikidocs.net/images/page/22888/inputgate.PNG)

현재 정보를 기억하기 위해 사용되는 게이트

입력 벡터와 hidden state에 대해 sigmoid를 지난 it (0~1)
입력 벡터와 hidden state에 대해 tangent를 지난 gt (-1~1)

#### 2) forget gate

![img](https://wikidocs.net/images/page/22888/forgetgate.PNG)

기억을 삭제하기 위한 게이트

입력 벡터와 hidden state에 대해 sigmoid를 지난 ft (0~1)

ft가 0에 가까울수록 정보를 많이 삭제한 것

#### 3) cell state

![img](https://wikidocs.net/images/page/22888/cellstate2.PNG)

it와 gt의 원소별 곱을 구한 값과
ft와 이전 cell state인 Ct-1의 원소별 곱을 구한 값을 더한다

이게 무엇을 의미?

ft가 0이 된다면, Ct-1은 Ct에 대한 영향력이 0이 된다는 뜻
--> 오직 input gate의 결과에서만 Ct를 결정할 수 있다
--> 삭제게이트는 닫히고 입력게이트만 열렸다

it가 0이 된다면, Ct는 Ct-1의 값에만 의존하게 됨
--> 입력게이트는 닫히고 삭제게이트만 열렸다

- 삭제게이트: 이전 시점의 입력을 얼마나 반영할 지
- 입력게이트: 현재 시점의 입력을 얼마나 반영할 지

#### 4) output gate와 hidden state

![img](https://wikidocs.net/images/page/22888/outputgateandhiddenstate.PNG)

ht를 결정하기 위한 게이트

입력 벡터와 hidden state에 대해 sigmoid를 지난 ot (0~1)

ot를 tanh(Ct)과의 원소별 곱을 구하면 그게 ht가 된다

why? hidden state를 결정할 때 cell state에서의 값이 걸러지는 효과를 내기 위함

## 3. GRU

LSTM의 복잡한 구조를 간단화 시킴

LSTM에서 3개 게이트를 사용했으나 GRU에서는 2개 게이트로 줄였다.
- update gate
- reset gate

## 4. RNN을 이용한 언어모델

이어지는 문장에서 다음에 올 단어가 무엇인지 예측하는 모델이라고 할 때

![](https://wikidocs.net/images/page/46496/rnnlm1_final_final.PNG)

요런식의 입력과 출력이 나올거라는 것  

이전 시점에서의 출력을 현재 시점에서의 입력으로 활용한다
하지만 이것은 inference, test에서 사용될 때의 이야기이며,
training 과정에서는 `what will the fat cat sit` 시퀀스로 입력을 넣었을 때 `will the fat cat sit on` 이 출력 시퀀스로 나오도록 훈련시킨다
--> **teacher forcing**

또한 이러한 언어모델에서는
- 출력층에서 active function으로 softmax 함수를 활용
- loss function으로 cross-entropy 함수를 활용
