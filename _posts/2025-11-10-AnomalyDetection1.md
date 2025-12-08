---
layout: single
title:  "[Anomaly] Anomaly Detection이란"
permalink: anomaly/anomalydetectionintro
toc: true
toc_sticky: true
categories: 
  - anomaly
---


# Anomaly Detection

지난 ECG 샤가스 질병 진단 프로젝트, MRI 이미지 알츠하이머 진단 프로젝트, EEG Foundation model 프로젝트들을 진행하면서 모든 프로젝트에서 공통적으로 가졌던 어려움이 있었다. 병에 대한 진단이다보니 양성(병이 있는) 데이터의 수가 현저히 적어서 클래스 간의 불균형이 심하다는 점이었다. 이러한 문제를 해결하기 위해서 양성 데이터를 Augmentation을 하거나 양성 데이터에 더 큰 가중치를 주어 Weighted Cross Entropy, Focal Loss 등을 사용하는 식의 방법을 사용해왔다. 

이런 문제에 대해서 살펴보다보니 비지도 학습을 통해서 접근하는 방법도 이 문제를 해결하기에 좋아보였다. 그리고 Anomaly Detection 분야에서 주로 비지도 학습을 통해 이상치를 탐지 한다는 사실도 알게되면서 Anomaly Detection에 대해 살펴보았는데 꽤 흥미로웠다. (물론 지도학습, 준지도학습을 통한 Anomaly Detection도 있다) 지금까지 진행해온 프로젝트와도 충분히 연관성 있고 적용해볼만 한 개념들이 많아 보였다.

Anomaly Detection에 대해서 한 번 정리해보고자 한다.

---

## Anomaly Detection이란?

Anomaly Detection은 어떠한 데이터 집합에서 예상 가능한 값이 아닌 다른 형태의 데이터 패턴을 찾아내는 것을 말한다. 쉽게 말해, 정상적인 패턴을 보이지 않는 데이터를 찾아내는 것이다.

Anomaly Detection 분야에서도 딥러닝 분야와 비슷하게 Supervised(지도), Semi-Supervised(준지도), Unsupervised(비지도) learning 전부 연구가 되고있다. One-class Support Vector Machine, Deep SVDD 등의 준지도 학습, Autoencoder를 이용한 비지도 학습 등 굉장히 다양하게 있다. 이 방법들에 관련한 논문들을 앞으로 하나씩 리뷰를 할 예정이다.

---

## Novelty vs Outlier

이 분야에서 주로 사용되는 용어 중에 Novelty와 Outlier라는 개념이 있는데 비슷한 의미를 가지지만 조금은 다르게 사용된다고 한다. 어떤 데이터 집합에서 같은 결이지만 다른 데이터와 다른 형태를 가지는 것을 Novelty라고 한다. Novelty는 정상치에 포함된다고 말하기도 하는 것 같다. 그리고 기존 데이터 집합과 완전히 다른 형태의 데이터를 Outlier, 이상치라고 한다. 이와 관련해서는 다양한 관점이 있는 것으로 보이는데, 이정도로 알고 있으면 문제 없을 것 같다.   

<img width="383" height="144" alt="Image" src="https://github.com/user-attachments/assets/b2c4de3b-be0b-493a-b6c7-2eada0c0140f" />  

---

## OOD (Out-of-Distribution Detection)

정상 샘플이 단일 class로만 구성되어 있는 경우 말고 실제로는 정상 샘플이 여러 개의 multi class로 구성된 경우가 많다. 이 때 정상 범주에 있는 샘플들은 In-distribution sample 이라고 표현한다. 이 In-distribution 데이터로 학습시키고 비정상 샘플을 찾는 문제를 Out-of-distribution Detection이라 부른다. 우리가 주로 사용하는 softmax 기반의 classification에서는 마지막 출력에서 가장 확률이 높은 class를 결과로 하는 방식이기에 비정상 sample이 입력되어도 class 중 하나로 예측을 하게 된다. 이럴 때 Out-of-distribution Detection을 이용하여 outlier를 걸러낸다고 한다. 또 이런 경우에 새로 class를 지정하여 구분하는 incremental learning 방법도 존재한다고 한다. 개인적으로 이 방법론이 흥미롭다.

---

## Anomaly, Noise, Outlier의 차이

Anomaly(이상치)는 두 가지로 볼 수 있는데, 일반적인 데이터와 다른 메커니즘에 의해 발생한 데이터, 혹은 데이터 분포 안에서 단순히 객체들의 발생 확률 밀도가 매우 낮은 데이터이다. 

또 Noise라는 개념은 데이터 수집과정에서 자연스럽게 포함되는 잡음이고 Noise가 존재함을 인지하고 있어야한다. 이와는 다르게 Outlier는 우리가 찾아내야하는 객체를 의미한다.

---

## Anomaly Detection 알고리즘의 분류

Anomaly Detection의 분류에 다양한 분류가 있다.

- 데이터 간의 **밀도**를 기반으로 이상치를 탐지하는 밀도 기반 알고리즘  
- **통계적**으로 이례적인 행동을 보이는 데이터를 식별하는 통계 기반 알고리즘  
- 데이터 간의 **거리**를 기반으로 이상치를 탐지하는 거리 기반 알고리즘  
- 여러 개의 의사결정 기반 모델을 조합하는 **앙상블 알고리즘**

대부분 통계학 전공, 머신러닝, 딥러닝 수업에서 다룬 개념들이 보인다. 저차원으로 차원을 축소시켜 데이터 구조를 바라보는 주성분분석(PCA)도 통계 기반 알고리즘으로써 이상탐지에 사용되고 KNN, k-means 알고리즘도 거리 기반 알고리즘으로써 이상탐지에 사용된다.

---

## Anomaly Detection의 학습 관점

Anomaly Detection의 목적은 이상치인지, 아닌지 판단하는 지도학습에 가깝지만 실제 수행 방식은 비지도 학습에 가깝다. 지도학습 관점의 Classification을 예를 들면, 어떠한 데이터를 잘 구별하는 분류 경계면을 찾아내는 과정이다. 하지만 Anomaly Detection에서는 normal data의 normal 영역을 추정하고 이 이외의 데이터는 normal이 아닌 데이터이다. 이때의 데이터는 단순히 abnormal이 아니라 normal이 아닌 다른 class에 속할 수 있는 데이터라고 본다. Anomaly Detection은 대체로 정상 데이터가 비정상 데이터보다 훨씬 많고 정상데이터만 사용한다는 가정을 한다.   

<img width="208" height="133" alt="Image" src="https://github.com/user-attachments/assets/bd32ec4b-d250-421e-b5be-83f9c99904a9" />

---


이전에 진행했던 ECG 샤가스 병 진단 프로젝트에서는 정상 심전도 데이터와 샤가스 질병을 가진 심전도 데이터를 가지고 단순 Classification을 진행했기 때문에 엄밀히 말하면 Anomaly Detection이라고는 할 수 없어 보인다. 만약 정상데이터만을 가지고 normal 영역을 추정하여 abnormal을 찾아내는 방식으로 진행했다면 이 때는 Anomaly Detection이 될 수 있다.

---

## Tradeoff

Anomaly Detection에서는 Generalization과 Specialization 사이의 Tradeoff가 존재한다. 위에서 언급한 바와 같이 normal의 영역을 추정할 때 normal의 개념을 너무 넓게 일반화하면 이상치를 정상으로 분류할 수 있고 반대로 normal의 개념을 너무 좁게하면 정상을 비정상으로 판단되는 오류가 생길 수 있다. 이 개념은 기초통계에서 배운 1종오류, 2종오류와 같은 개념이다. 이 기술이 사용되는 현장 상황에 따라 정도가 조절되어야 할 것으로 보인다.

결론적으로 Anomaly Detection은 클래스 불균형이 심각할 때, 그리고 소수 클래스의 데이터 수가 적을 때 사용하면 된다. 만약 이에 해당하지 않는다면 Classification 문제로 접근하는 것이 더 좋을 수도 있다고 한다. 이상치는 생각한 것 보다 정말 적고 이상한 데이터를 의미한다. 비지도 학습을 쓸 수 밖에 없는 상황이다.

---

## Time Series Anomaly Detection

Anomaly Detection에서도 Time Series data를 깊게 다루는 분야도 있다고 한다. 이전에 진행했던ECG 샤가스 질병 진단 프로젝트, EEG Foundation model 프로젝트도 시계열 1D signal 데이터이다. 시계열 데이터에 적합한 Anomaly Detection 모델들에 대한 연구도 활발하다. Transformer 기반 시계열 이상탐지 모델인 **Anomaly Transformer**, 시계열의 다변량 특성을 그래프로 모델링한 **Graph-Augmented Normalizing Flow (GANF)** 등 다양한 논문이 있는데 이 논문들도 꼭 리뷰할 예정이다.

---

## 참고

또한 이 글을 쓰는데 참고한 강필성 교수님의 비즈니스 애널리틱스(anomaly detection) 유튜브 강의도 쭉 리뷰해 볼 예정이다.

참고)  
https://www.youtube.com/watch?v=ECgI1YVQpY8  
https://hoya012.github.io/blog/anomaly-detection-overview-1/  
https://github.com/hoya012/awesome-anomaly-detection
