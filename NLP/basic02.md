# Machine Learning 개요

머신러닝 데이터
- train data: 학습용 데이터
- test data: 평가용 데이터
- validate data: 하이퍼파라미터 조정을 위한 검증용 데이터

> 하이퍼파라미터: 모델 성능에 영향을 주는 사람이 지정하는 변수
> 파라미터: 모델의 weight, bias

머신러닝 문제는 크게 regression / classification 으로 나뉜다.
classification은 또 binary / multi-class로 나뉜다.

- Linear Regression: 연속적인 값의 범위 내에서 값을 예측
- Logistic Regression (Binary Classification): 2개의 선택지 중 하나를 선택
- Softmax Regression (Multi-class Classification): 3개 이상 중 하나를 선택

## 1. Linear Regression

- linear 함수
: 주어진 독립변수에 대한 y와의 관계를 연속적인 식으로 나타냄

### Cost function (Loss function)

실제값~예측값 오차에 대한 식 --> 가장 작아야 한다

주로 `MSE`가 사용 (Mean Squared Error)

오차를 단순히 "실제값 - 예측값"으로 정의하면 음수, 양수값이 혼재
크기를 구하기 위해 제곱한 값을 사용

### Optimizer

loss가 가장 작은 parameter를 찾아 최적화
`Gradient Descent` --> 미분값이 0이 되는 쪽을 향해간다

## 2. Logistic Regression (Binary Classification)

이진분류의 경우 연속적인 방정식으로 표현할 수 없음

- `Sigmoid 함수`
: 0~1의 값을 가지는데 0.5 이상이면 true, 이하면 false를 부여하여 이진분류 해결

### Cost function

여기서는 MSE를 사용하지 않음. --> 로지스틱에서는 로컬 미니멈에 빠질 확률이 높아서

`Cross Entropy`를 사용 --> 크로스엔트로피의 평균을 사용!!

실제값이 0일 때 y값이 1에 가까워지면 오차가 커짐
실제값이 1일 때 y값이 0에 가까워지면 오차가 커짐

이 둘을 모두 표현하는 함수 --> 크로스엔트로피 함수

## 3. Softmax Regression (Multi-class Classification)

k차원의 벡터를 입력받아 각 클래스에 대한 확률을 추정
다 합쳐서 1이 나오게...

- `Softmax 함수`
: 여러 개의 연산 결과를 정규화하여 모든 클래스의 확률값의 합이 1이 됩니다

```py
y = np.exp(x) / np.sum(np.exp(x))
```

### Cost function

`Cross Entropy`를 사용

---

# Deep Learning 개요
- 퍼셉트론
- Back propagation
- Overfitting (Regularization)
- Gradient vanishing, exploding
- Multi-layer Perceptron

## 1. Perceptron

SingleLayer Perceptron
- input layer와 output layer로 구성

MultiLayer Perceptron (MLP)
- hidden layer가 1개 이상

그중에서도 hidden layer가 2개 이상인 경우 `Deep Neural Network (DNN)` 이라고 부름


## 2. Artificial Neural Network

Feed-foward NN --> 입력에서 출력으로의 단방향 NN
Recurrent NN --> 은닉층의 출력값을 다시 입력으로

Fully-connected Layer

## 3. Activation Function

퍼셉트론에서 출력값을 결정하는 함수

활성화 함수는 Non-linear function

: 활성화 함수가 linear하다면 hidden layer를 쌓는게 사실 의미가 없음 (linear를 여러 개 쌓는건 하나의 layer로 처리할 수 있으므로)

1. Step Function
  - 거의 사용되지 않음

2. Sigmoid
  - Sigmoid 함수 전체적으로 미분값(기울기)가 0.25보다 큰 값이 나오지 않음
  - back propagation 과정에서 0과 가까운 값이 누적으로 곱해지면서 앞단에는 미분값이 전달이 잘 되지 않는 현상이 발생
  - 이 현상이 `기울기 소실 (Vanishing Gradient)`
  - 그래서 sigmoid는 hidden layer에서는 잘 사용되지 않는 활성화함수임
  - 주로 output layer에서 binary classification 문제를 해결할 때 사용

3. Hyperbolic Tangent
  - sigmoid랑 비슷한데 범위가 -1~1 임
  - sigmoid보다 기울기가 전체적으로 커서 미분할 때 sigmoid보다는 전반적으로 큰 값이 나옴

4. ReLU
  - 음수는 0, 양수는 x
  - 양수일 때 미분값이 항상 1
  - 음수일 때는 0이기 때문에 이 뉴런은 다시 회생하지 못함 --> dying ReLU
  - 이를 보완하기 위해 변형 ReLU들이 등장 --> 그 중 하나가 Leaky ReLU (음수일 때 작은 기울기를 적용)

5. Softmax
  - 얘도 output layer에서 사용되는 것이 일반적
  - multi-class classification 문제를 해결할 때 사용

## 4. Foward Propagation

프로파게이션부터!! 휴
