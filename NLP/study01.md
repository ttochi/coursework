# Word embeddings

Sparse representation vs. Dense representation

기존 원핫벡터의 경우 차원의 크기가 너무 크다는 단점 + 단어 벡터 간 유의미한 유사도를 계산할 수 없다는 단점

--> 사용자가 차원의 크기를 지정하여 벡터로 표현하는 방법이 워드임베딩 --> 임베딩 벡터

word를 word embedding vector로 변환하는 모델
- Word2Vec --> 인접한 단어는 비슷한 벡터값을 바꾼다
- GloVe -->

## Word2Vec

원핫벡터는 sparse representation --> 0과 1로만 표현 --> 단어 벡터간 유사도를 계산할 수 없음

단어 벡터 간 유의미한 유사도를 반영할 수 있도록 단어의 의미를 수치화 --> 단어의 의미를 다차원 공간에 벡터화 시킴 --> distributed representation

distributed representation: 비슷한 문맥에서 등장하는 단어들은 비슷한 의미

- CBOW: 주변 단어를 통해 중간 단어를 예측 --> window에서 주변단어가 입력 / 중심단어가 출력
- Skip-Gram: 중간 단어를 통해 중간 단어를 예측 --> window에서 중심단어가 입력 / 주변단어가 출력

`window`: 중심 단어 앞뒤로 몇개를 볼 지

## GloVe

----

# Transformer


## Attention

self-attention / multi-head attention

masked는 왜하는가...

---

Why Q, K, V?
각 벡터가 무엇을 의미하는지 가중치를 둘 것..

Q K^T --> Q와 K의 Similarity를 보겠다

한국어를 영어로 바꾸는 모델이라고 할 때

Q "한국어"와 K "지금까지 내뱉은 영어"의 관계성을 보고 그걸 참고해서 다음단어 예측하겠다

V는 한국어 토큰들 중에 어디에 중점을 두어서 관계성을 볼 것인가 (input의 가중치)
