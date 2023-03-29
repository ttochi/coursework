<!-- TODO -->
# Transformer

## Attention

self-attention / multi-head attention
masked는 왜하는가...

## Self Attention

Why Q, K, V?
각 벡터가 무엇을 의미하는지 가중치를 둘 것..

Q K^T --> Q와 K의 Similarity를 보겠다

한국어를 영어로 바꾸는 모델이라고 할 때,
Q "한국어"와 K "지금까지 내뱉은 영어"의 관계성을 보고 그걸 참고해서 다음단어 예측하겠다
V 한국어 토큰들 중에 어디에 중점을 두어서 관계성을 볼 것인가 (input의 가중치)
