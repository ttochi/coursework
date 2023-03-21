# Word Embedding

워드임베딩이란?
단어를 뉴럴넷 학습을 통해 벡터화하는 작업을 워드 임베딩이라고 함

> 즉, 단어를 벡터화 하는 작업에도 ML 모델을 사용한다는 것

#### 기존 원핫 인코딩과의 차이?

Sparse representation vs. Dense representation

기존 원핫벡터의 경우는
- 표현하려는 단어만 1이고 나머지는 0 --> sparse vector
- 차원의 크기가 너무 크다는 단점 (벡터 크기가 vocab size)
- 단어 벡터 간 유의미한 유사도를 계산할 수 없다는 단점

임베딩 벡터는
- 벡터의 차원을 줄이고 elem에 실수값을 갖게 함 --> dense vector
- 사용자가 차원의 크기를 지정하여 벡터로 표현
- 단어의 벡터화 작업에 뉴럴넷 모델을 사용해야 함

단어를 임베딩 벡터로 변환하는 것을 워드 임베딩이라고 하며,
임베딩 모델로는 아래와 같은 것들이 있다

- Word2Vec
- GloVe

## 1. Word2Vec

원핫벡터는 0과 1로만 표현 --> 단어 벡터간 유사도를 계산할 수 없음

단어 벡터 간 유의미한 유사도를 반영할 수 있도록 단어의 의미를 수치화하자!

### Distributed representation
- 단어의 의미를 다차원 공간에 벡터화 시킴
- 비슷한 문맥에서 등장하는 단어들은 비슷한 의미를 가지니 비슷한 벡터를 가질 것
- 이를 통해 단어 벡터 간 유사도를 계산할 수 있다

### Word2Vec의 학습방법

- CBOW
  - 주변 단어를 통해 중간 단어를 예측
  - input data: window의 주변단어 (원핫벡터s)
  - output data: window의 중심단어 (원핫벡터)

- Skip-Gram
  - 중간 단어를 통해 중간 단어를 예측
  - input data: window의 중심단어 (원핫벡터)
  - output data: window의 주변단어 (원핫벡터s)

> window란 중심 단어 앞뒤로 몇개를 볼 지

How to make training data set? --> Sliding Window

### CBOW

![](https://wikidocs.net/images/page/22660/word2vec_renew_1.PNG)

Word2Vec은 hidden layer가 2개 이상인 딥러닝 모델이 아님
즉, hidden layer 1개 짜리인 얕은 뉴럴넷

또한, hidden layer에 별도의 활성화함수도 존재하지 않음
(그래서 hidden layer라는 용어 대신 projection layer라고 부르기도 함)

이 projection layer의 사이즈가 최종적으로 임베딩 벡터의 크기가 될 것

![](https://wikidocs.net/images/page/22660/word2vec_renew_3.PNG)

학습이 완료되면 최종적으로 W 행렬의 각 행이 각 단어의 M차원 임베딩 벡터로 사용됨.
또는, W와 W'를 모두 사용해서 임베딩 벡터를 사용하기도 함

### Skip-Gram

![](https://wikidocs.net/images/page/22660/word2vec_renew_6.PNG)

## 2. GloVe
