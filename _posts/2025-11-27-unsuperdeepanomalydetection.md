---
layout: single
title:  "[paper] Unsupervised Deep Anomaly Detection for Multi-Sensor Time-Series Signals"
permalink: paper/multisensor
toc: true
toc_sticky: true
categories: 
  - paper
  - anomaly
---


Anomaly detection을 공부하면서 Deep learning을 사용해서는 어떻게 연구가 진행되고 있는지 알아볼 때가 되었다. 21년도 논문이지만 연구 흐름을 보기에 좋아보여 리뷰를 하게되었다.

---

## Unsupervised Deep Anomaly Detection for Multi-Sensor Time-Series Signals

Multi-Sensor Time-Series Signals에 대한 비지도학습을 통한 이상탐지는 중요한 문제로 주목받아왔다. Multi-Sensor Time-Series Signals에서는 시공간적(spatial-temporal) 상관관계를 포착하여 일반화된 정상 패턴을 발견하는 것, 그리고 noise data 가 포함된 데이터 속에서 normal과 abnomal을 구분하는 것, 이렇게 두가지가 중요 포인트이다.

이 논문에서는 이 두가지 문제를 같이 해결하는 Deep Convolutional Autoencoding Memory network (CAE-M) 를 제안한다.

1) multi-sensor data의 spatial dependence를 찾는 Deep Convolutional Autoencoder  
2) noise, normal, abnormal을 잘 구분하기 위한 Maximum Mean Discrepancy  

---

## introduction

이전에 알아본 대로 이상탐지 분야에서는 비지도 학습을 통해 normal 영역을 추정하는 식으로 수행한다.  
주로 데이터에서 normal보다 abnormal 데이터가 현저히 적어 data imbalance 문제가 있는 경우가 많다. 또한 abnormal 데이터를 labeling하는 것도 매우 어렵기 때문에 unsupervised learning으로 문제에 접근하는 것이 일반적이다.

하지만 Multi-Sensor Time-Series Signals의 경우 기존의 비지도학습을 그대로 적용하기 어렵다.

1) PCA, k-means, OCSVM, Autoencoder와 같은 기존 기법들은 spatial, temporal dependence을 동시에 포착하지 못한다.  
2) reconstruction error 기반의 모델(CAEs, DAEs…) 은 샘플의 재구성 오차가 크다고 가정하지만, 실제로는 모델 복잡도와 데이터 잡음으로 인해 abnormal input도 잘 재구성되는 경우가 있다.  
3) dimensionality 를 줄이기 위해서 차원 축소와 이상 탐지를 2단계로 진행하는데, 이는 모델을 각각 따로 학습시켜 local optima에 빠지기 쉽다.

Deep Convolutional Autoencoding Memory network (CAE-M)은 두가지 sub network로 구성된다.  
characterization network(deep convolutional autoencoder) and memory network

1) feature extraction module로서의 deep convolutional autoencoder  
2) forecasting module 로서의 Autoregressive model  

---

## Traditional anomaly detection

- Reconstruction-based – PCA, Kernel PCA, Robust PCA  
- Clustering-based - Gaussian Mixture Models (GMM), k-means, Kenel Density Estimator (KDE)  
- one-class learning - One-Class Support Vector Machine (OCSVM), Support Vector Data Description (SVDD)  

시계열  
- Autoregression (AR)  
- Autoregressive Moving Average (ARMA)  
- Autoregressive Integrated Moving Average (ARIMA)  

---

## Deep anomaly detection

### Reconstruction model
Autoencoder, LSTM Encoder-Decoder, CAE, ConvLSTM, VAE, DAE, DBN, Robust AE  

### Forecasting models
RNN, LSTM, CNN-RNN 혼합 모델(LSTNet), GAN 기반 모델  

### Composite models
Composite LSTM model, Spatial-Temporal AutoEncoder (STAE)  

---

## The Proposed Method   

<img src="{{ '/assets/images/MT1.png' | relative_url }}" alt="Image" width="700">   

제시된 모델 CAE-M의 전체 개요를 살펴보면, 먼저 multi-sensor time-series signal을 Deep Convolutional Autoencoder (CAE)를 통해 저차원의 representation으로 인코딩해주는 과정이 Characterization Network에서 일어난다.

기존 연구들에서는 Noise의 영향을 줄이기 위한 방법으로 Memory module 혹은 Gaussian Mixture Model (GMM)을 사용해왔지만, 본 연구에서는 Maximum Mean Discrepancy (MMD)를 사용한다. 이 방법은 학습 데이터의 분포가 가우시안 분포로 근사하도록 유도할 수 있고, 이를 통해 학습 데이터에 포함된 잡음이나 이상으로 인한 overfitting위험을 줄일 수 있다.

CAE를 통해 얻은 저차원 representation과 reconstruction error를 Attention mechanism과 Bi-LSTM을 사용한 Layer, 그리고 Auto-regressive model(AR) 모델로 이루어진 Memory network 에서 temporal한 정보를 모델링하여 future feature를 예측한다.

이후 가중 계수를 포함한 compound objective function(복합 목적 함수)를 계산하여 학습을 수행한다.

정상 데이터의 경우 reconstruction 된 값과 원래의 입력 시퀀스가 유사하고, 모델 예측 값 또한 시계열의 미래 값과 유사하다. 하지만 이상 데이터는 값들이 크게 달라지게 된다.

---

## Characterization Network

입력된 multi-sensor time-series signal을 가지고 요약된 feature와 reconstruction error를 구하는 과정이다. Auto encoder가 이상데이터도 잘 복원해버리는 상황을 막기 위해서 optimization function에 reconstruction loss를 결합하는 것이다.

### Deep feature extraction   

<img src="{{ '/assets/images/MT2.png' | relative_url }}" alt="Image" width="200">   

CAE는 Encoder 와 Decoder로 이루어져 있는데, Encoder는 입력 행렬 𝑥를 다수의 covolutional과 max-pooling을 통해 은닉 표현 𝑧𝑓로 표현한다.

Decoder는 반대로 zf 를 앞에서 거친 Encoder계층의 반대로 이루어진 계층을 통해 공간으로 다시 매핑하여 reconstruction을 수행한다.

원래의 입력 벡터와 reconstruction 결과와의 MSE값을 reconstruction loss로 설정한다.

---

### Handling noisy data   

<img src="{{ '/assets/images/MT3.png' | relative_url }}" alt="Image" width="500">   

Autoencoder가 noise 데이터와 이상 데이터에 대해서도 지나치게 잘 일반화되는 것을 방지하기 위해 저차원 representaion에 잠재되어 있는 이상을 탐지한다. Maximum Mean Discrepancy (MMD)를사용한 loss를 사용하는데, 이는 두 분포의 샘플 간 거리를 측정하는 척도이다.

잠재공간에서 정상 데이터의 분포를 가우시안 분포에 맞추게 함으로써, Autoencoder가 noise나 이상데이터가 가우시안 분포 속 저밀도 영역에 위치하도록 유도하여 이상 데이터까지 과도하게 일반화하는 문제를 완화하는 것이다.

---

## Memory Network

제안된 모델에서는 spatial, temporal pattern 을 잘 찾아내기 위해 reconstruction과 prediction analysis를 수행한다. 앞의 Characterization network를 통해 저차원 feature와 reconstruction error를 학습했다.  
Memory Network에서는 temporal pattern을 찾기 위해 linear, non-linear 함수 기반의 예측을 실행한다.

---

### Non-linear prediction   

<img src="{{ '/assets/images/MT4.png' | relative_url }}" alt="Image" width="500">   

RNN은 장기 의존성 학습에 한계가 있어 이 연구에서는 전체, local 문맥을 모두 고려할 수 있는 Attention mechanism을 포함한 BiLSTM을 사용한다.

BiLSTM은 입력을 두 방향으로 처리하는데, 하나는 과거에서 미래로, 다른 하나는 미래에서 과거로 처리한다. 단방향 모델과 달리, 두 개의 은닉 상태를 결합함으로써 어느 시점에서도 과거와 미래의 정보를 동시에 유지할 수 있다는 장점이 있다.

---

### Linear prediction   

<img src="{{ '/assets/images/MT5.png' | relative_url }}" alt="Image" width="250">   

Linear prediction을 위한 모델로는 Autoregressive (AR) 모델을 사용하는데, 이는 현재 관측값과 과거 관측값 간의 의존성을 사용하는 회귀 모델이다. 앞서 설명한 non-linear prediction 방법이 더욱 효과적이고 강력하지만 short term modeling에 있어서는 AR model도 뛰어나다.   

<img src="{{ '/assets/images/MT6.png' | relative_url }}" alt="Image" width="500">   

다음과 같이 prediction error는 non-linear 방식과 linear 방식을 결합하여 사용한다.

---

## Joint optimization

위처럼 여러 단계로 이루어진 접근법은 각 모델별로 optimization을 진행하기 때문에 local optima에 빠질 위험이 존재한다. 따라서 본 논문에서는 compound objective function을 minimize하는 end-to-end hybrid model을 제안한다.

위에서 모두 설명한 바와 같이 CAE-M 목적함수는 MSE, MMD, Prediction error (non-linear, linear) 4가지로 구성되어 있다.   

<img src="{{ '/assets/images/MT7.png' | relative_url }}" alt="Image" width="500">   

M 은 배치 크기, h 는 현재 시간 스텝, λ1,λ2,λ3 는 각 손실 항의 중요도를 조절하는 하이퍼터이다. 실험적으로 λ1=10−4,λ2=0.5,λ3=0.5가 좋은 성능을 보인다.

---

## Inference   

<img src="{{ '/assets/images/MT8.png' | relative_url }}" alt="Image" width="500">   

Threshold는 위와 같고 Err(xi) 는 샘플 xi 에 대해 계산된 손실 함수 값의 합을 의미한다. Err(x) 값이 Threshold를 넘으면 abnormal 그렇지 않으면 normal로 판단하는 것이다.

---

Y. Zhang, Y. Chen, J. Wang, and Z. Pan, “Unsupervised Deep Anomaly Detection for Multi-Sensor Time-Series Signals,” IEEE Transactions on Knowledge and Data Engineering

