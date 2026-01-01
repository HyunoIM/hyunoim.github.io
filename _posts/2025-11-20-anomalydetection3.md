---
layout: single
title:  "[Anomaly] Parzen window estimation"
permalink: anomaly/parzenwindow
toc: true
toc_sticky: true
categories: 
  - anomaly
---x

## Kernel Density Estimation

특정한 분포를 가정하지 않고 데이터 자체로부터 발생 확률을 측정하는 방법이다. 이전에 알아본 Gaussian Distrbution과 Mixture of Gaussian Distrbution의 경우는 데이터가 Gaussian 분포로 이루어져 있음을 가정하고 진행하였는데 이 점이 Kernel Density Estimation과는 다르다.  
Parzen window estimation은 이 Kernel Density Estimation에 속하는 방법이다.

Kernel Density Estimation의 핵심 내용은 다음과 같다.

---

<img src="{{ '/assets/images/ad31.png' | relative_url }}" alt="Image" width="250">    

어떠한 지점의 확률밀도함수는 k/NV로 표현할 수 있다.  
V는 x를 표현하는 크기, N은 전체 데이터 수, k는 영역 안에 들어있는 객체 수이다.

이 추정은 N의 크기가 클 수록 정확해진다.  
또한 V의 크기가 작아야 정확한다. 여기에는 조건이 있다. 충분한 양의 샘플이 영역 안에는 들어올 만큼의 크기는 되어야하고, p(x)가 constant 하다는 가정을 지켜줄 만큼은 작아야한다.

이러한 조건들은 간단한 p(x) 유도과정에서 살펴볼 수 있다.

---

## 확률밀도 유도 과정

영역 R에 포함될 확률 P는 확률 밀도함수로 아래와 같이 나타낼 수 있는데 영역 R이 충분이 작아서 그 안에서 p(x)가 거의 변하지 않는다고 가정하면, p(x)V로 나타낼 수 있다.

$$
P = \int_R p(x')\, dx' \approx p(x)\, V
$$

작은 구간에서는 p(x)가 일정하다고 보고 “밀도x부피= 확률” 형태로 근사한 것이다.

또, 표본이 N개이고 그 중에서 k개가 영역 R에 들어가는 경험적 확률은

$$
P \approx \frac{k}{N}
$$

이렇게 나타낼 수 있다.

위의 두 식을 이용해서

$$
p(x)\, V \approx \frac{k}{N}
$$

확률밀도를 이처럼 추정할 수 있다.

$$
p(x) \approx \frac{k}{N V}
$$

---

## Binomial distribution 기반 해석

Binomial distribution의 평균과 분산식을 이용하면

$$
E[k] = NP, \qquad Var[k] = NP(1-P)
$$

$$
E\left[ \frac{k}{N} \right] = P
$$

$$
Var\left[ \frac{k}{N} \right] = \frac{P(1-P)}{N}
$$

이때, N의 크기가 커야 분산은 줄어들고, k/N의 확률이 실제확률 P의 좋은 추정치가 될 수 있다.

따라서 N의 크기가 클 수록, V의 크기가 작아야 정확하다는 내용이 나오게 된 것이다.

---

## Kernel Density Estimation과 KNN Density Estimation

결론적으로 나온,

$$
p(x) \approx \frac{k}{N V}
$$

이 식을 봤을 때 V를 고정해두고 k를 결정해서 확률밀도를 계산하는 방법이 Kernel Density Estimation 이다.  
반대로, K를 고정해두고 V를 결정하는 방법이 KNN Density Estimation이다.

---

## Parzen Window Density Estimation

어떠한 점 x가 주어졌을 때, 주변에 영역 상자 R을 두고 그 영역에 얼만큼의 샘플이 들어오는지를 기반으로 밀도를 추정하는 방법이다.

영역이 d차원의 공간에서 한 변의 길이가 h인 hypercube 부피는

$$
V = h^d
$$

이다.

영역 상자 R에 들어오면 1, 안 들어오면 0의 가중치를 주는 Uniform kernel도 있지만 어느 정도의 가중치를 주느냐에 따라 다양한 kernel도 존재한다.

<img src="{{ '/assets/images/ad32.png' | relative_url }}" alt="Image" width="250">   

---

왼쪽은 Uniform kernal이고 오른쪽은 Gaussian distribution으로 가중치를 주는 kernel이다.  
점 x를 Gaussian Distribution의 중심으로 보고 중심으로부터 얼마나 떨어져있는지에 대한 확률값들을 계산하여 확률밀도함수를 계산하는 방식인 것이다.

위에서 말한 h의 길이가 너무 커도, 작아도 주어진 형태를 잘 표현하기 힘들다. Likelihood를 최대화하는 h(bandwidth)를 찾아나가는 것이 Kernel Density Estimation의 학습 과정이다.

<img src="{{ '/assets/images/ad33.png' | relative_url }}" alt="Image" width="250">    

---

## 정리

정리해보면, 데이터 x로부터 하나의 데이터의 거리를 bandwidth h로 나눠 준 값을 kernel 함수가 얼마나 기여할지 가중치를 계산한다. N개의 모든 데이터로부터 구한 이 값으로부터 평균을 구해주면 이것이 확률밀도이다.

<img src="{{ '/assets/images/ad34.png' | relative_url }}" alt="Image" width="250">    

---

저번 글과 이번 글에서 다룬  
Gaussian Distrbution과 Mixture of Gaussian Distrbution,  
Kernel Density Estimation의 차이를 잘 보여주는 그림이다.

Gaussian Distrbution은 하나의 중심으로부터 Gaussian Distrbution으로 정상 영역을 추정하는 것이고 Mixture of Gaussian Distrbution은 여러 중심으로부터 찾기 때문에 더 유연한 정상영역을 찾기가 가능해진다.  
이번에 본 Parzen window density estimation은 모든 점이 특정 분포의 중심이 되어 확률 밀도를 계산하는 방법이다.

이러한 방법들이 밀도 기반의 이상치 탐지이다.

---

## 참고

이 글은 강필성 교수님의 Business Analytics (anomaly detection) 유튜브 강의를 참고하여 작성한 글입니다.

https://www.youtube.com/watch?v=rddQT5vxwrg&list=PLetSlH8YjIfWMdw9AuLR5ybkVvGcoG2EW&index=16
