# Conventional Language Model

1. Text preprocessing
  - Tokenization
  - Cleaning and Normalization

2. Conventional Language Model

3. Vector Similarity

## Preprocessing

- 토큰화(tokenization): corpus data에서 용도에 맞게 토큰을 분류
- 정제(cleaning): 노이즈 데이터를 제거
- 정규화(normalization): 표현 방법이 다른 단어들을 동일한 단어로 처리

### Tokenization

Tokenization 중에 그 기준을 어떻게 정하는 가가 중요
라이브러리들이 기준에 따라 잘 API 제공해줌ㅎ (e.g. NLTK)

> 품사 태깅, 워드 토크나이저, 문장 토크나이저, 형태소 토크나이저, 명사 추출 등등

```py
from nltk.tokenize import sent_tokenize

text = "His barber kept his word. But keeping such a huge secret to himself was driving him crazy."
sent_tokenize(text)
# ['His barber kept his word.', 'But keeping such a huge secret to himself was driving him crazy.']
```

```py
from nltk.tokenize import word_tokenize

text = "I am actively looking for Ph.D. students. and you are a Ph.D. student."
word_tokenize(text)
# ['I', 'am', 'actively', 'looking', 'for', 'Ph.D.', 'students', '.', 'and', 'you', 'are', 'a', 'Ph.D.', 'student', '.']
```

### Encoding

텍스트를 숫자로 인코딩
- One-hot Encoding
- Word Embedding

#### 단순 정수 인코딩

```py
preprocessed_sentences = [['barber', 'person'], ['barber', 'good', 'person'], ['barber', 'huge', 'person'], ['knew', 'secret'], ['secret', 'kept', 'huge', 'secret'], ['huge', 'secret'], ['barber', 'kept', 'word'], ['barber', 'kept', 'word'], ['barber', 'kept', 'secret'], ['keeping', 'keeping', 'huge', 'secret', 'driving', 'barber', 'crazy'], ['barber', 'went', 'huge', 'mountain']]
```

```py
[[1, 5], [1, 8, 5], [1, 3, 5], [9, 2], [2, 4, 3, 2], [3, 2], [1, 4, 6], [1, 4, 6], [1, 4, 2], [7, 7, 3, 2, 10, 1, 11], [1, 12, 3, 13]]
```

#### 원핫 인코딩

원-핫 벡터: 표현하고 싶은 index에 1을 부여하고 나머지 index에 0을 부여한 벡터

한계
1. 단어 갯수가 늘어날 수록 벡터를 저장하기 위해 필요한 공간이 늘어난다
2. 단어의 유사도를 표현하지 못함

이를 해결하기 위해 Word Embedding을 사용함

```py
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.utils import to_categorical

text = "나랑 점심 먹으러 갈래 점심 메뉴는 햄버거 갈래 갈래 햄버거 최고야"
tokenizer = Tokenizer()
tokenizer.fit_on_texts([text])

tokenizer.word_index
# {'갈래': 1, '점심': 2, '햄버거': 3, '나랑': 4, '먹으러': 5, '메뉴는': 6, '최고야': 7}

sub_text = "점심 먹으러 갈래 메뉴는 햄버거 최고야"
encoded = tokenizer.texts_to_sequences([sub_text])[0]
# [2, 5, 1, 6, 3, 7]

one_hot = to_categorical(encoded)
# [[0. 0. 1. 0. 0. 0. 0. 0.]
# [0. 0. 0. 0. 0. 1. 0. 0.]
# [0. 1. 0. 0. 0. 0. 0. 0.]
# [0. 0. 0. 0. 0. 0. 1. 0.]
# [0. 0. 0. 1. 0. 0. 0. 0.]
# [0. 0. 0. 0. 0. 0. 0. 1.]]
```

### Padding

문장의 길이를 동일하게 맞추어 병렬 연산을 수행하도록 한다 (행렬화)

padding 방법은 여러가지...
> zero padding, post padding ...


```py
array([[ 1,  5,  0,  0,  0,  0,  0],
       [ 1,  8,  5,  0,  0,  0,  0],
       [ 1,  3,  5,  0,  0,  0,  0],
       [ 9,  2,  0,  0,  0,  0,  0],
       [ 2,  4,  3,  2,  0,  0,  0],
       [ 3,  2,  0,  0,  0,  0,  0],
       [ 1,  4,  6,  0,  0,  0,  0],
       [ 1,  4,  6,  0,  0,  0,  0],
       [ 1,  4,  2,  0,  0,  0,  0],
       [ 7,  7,  3,  2, 10,  1, 11],
       [ 1, 12,  3, 13,  0,  0,  0]])
```

## Language Model

언어 모델이란 단어 시퀀스(문장)에 확률을 할당하는 모델


## Vector Similarity

A 문장과 가장 유사한 문장을 찾아보자

- 코사인: 두 벡터간의 코사인 값이 1에 가까울 수록 유사도가 큼
- 유클리디안 거리: 두 벡터의 거리가 가까울수록 유사도가 큼
- 자카드 유사도: 두 문장에서 쓰인 단어들의 합집합 중에서 교집합의 비율
