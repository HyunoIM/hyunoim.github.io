---
layout: single
title:  "[Anomaly] Gaussian Distrbution, Mixture of Gaussian Distribution"
permalink: anomaly/gaussiandist
toc: true
toc_sticky: true
categories: 
  - anomaly
---

# Gaussian Distrbution, Mixture of Gaussian Distribution

## Density based Novelty detection

Density based Novelty detection  
정상 상태의 normal data의 분포를 추정한다음, 새로 주어진 데이터의 확률 분포의 값이 높으면 normal, 낮으면 abnormal로 판단하는 식의 이상탐지 방법이다. 

예를 들어, 정상 데이터로 Gaussian Distrbution을 추정하고 새로운 데이터의 확률을 파악하여 이상치인지 아닌지를 구분하는 것이다.  
여기서 사용하는 분포는 기본적으로 정규분포 Gaussian Distrbution, 그리고 mixture of Gaussian Distrbution을 사용한다고 한다.

---

## Gaussian Distrbution

정상 데이터가 하나의 Gaussian Distrbution으로 부터 생성되었음을 가정하고 사용한다. 정규분포를 추정하기 위한 파라미터는 평균(μ)과 표준편차(σ)이다. 당연하게도 Multivariate(다변량) 상태에서 주로 쓰이니 mean vector와 covariance matrix가 정규분포를 추정하기 위한 미지수가 되겠다. 

- 데이터 변수의 범위에 민감하지 않다.

Gaussian Distrbution 기반의 이상탐지에서는 scale 에 영향을 받지 않고 standardized 된 거리로 판단하기 때문이다.   

<img src="{{ '/assets/images/ad23.png' | relative_url }}" alt="Image" width="500">    

다음 식에서 볼 수 있듯이 공분산의 역행렬이 함께 계산되는데, 변수의 variance 마다 기여도가 달라지는 것이다. 변수의 범위는 covariance matrix가 자동으로 보정해준다.

- Optimal threshold를 계산할 수 있다.

이렇게 특정 분포를 가정하게 되면 우리는 신뢰구간만 설정하면 신뢰 구간에 해당하는 임계값을 통계적으로 계산이 가능하다. 

Gaussian Distrbution에서 likelihood를 최대화하는 파라미터(평균, 공분산)을 계산하게 되면 정상 데이터들의 평균과 공분산이라는 점을 계산 가능하다. Log-likelihood를 미분하여 최대가 되는 파라미터 값을 찾아내는 과정으로 계산이 가능하다. (MLE) Maximum Likelihood Estimation 라는 개념으로 배워온 개념이다. 

하지만 모든 데이터가 이 하나의 Gaussian Distrbution로 추정된다는 가정은 매우 엄격한 가정이어서 실제로 적용하기 어렵다. 따라서 multi modal distribution으로 완화한 가정에서 쓰이는 분포가 Mixture of Gaussian Distribution이다.

---

## Mixture of Gaussian Distribution

여러 개의 Gaussian Distribution의 선형결합으로 표현된다. 더 많은 데이터가 필요하지만 unimodal 가정일 때 보다 정확한 추정이 가능하다. K개의 Gaussian Distribution이 사용되는 경우 k개의 μ와 σ 그리고 각각의 분포가 기여되는 정도인 가중치 w값이 파라미터로 사용된다. 총 3*k개의 미지수가 필요함을 직관적으로 볼 수 있다.

<img src="{{ '/assets/images/ad24.png' | relative_url }}" alt="Image" width="700">   

여기서 expectaion maximization(EM) algorithm 이라는 개념이 쓰인다. Likelihood를 최대화 시키는 MLE 방법 중에 Gradient descent 말고도 EM algorithm도 존재한다. 보이지 않는 정보가 있어 Log-likelihood를 직접 최대화 하기 힘들 때 주로 쓰인다.

보이지 않는 정보를 추측하는 E-Step, 이 추측으로 파라미터를 업데이트하는 M-Step이 있다.   

<img src="{{ '/assets/images/ad25.png' | relative_url }}" alt="Image" width="700">   

이 그림을 예시로 보면 초기에는 어떤 점이 분포A에서 생성된 건지 분포B에서 생성된 건지 알 수 없다. E-Step에서는 각 데이터가 A와 B 중 어느 Gaussian에서 나왔을지를 확률로 계산 한다. M-Step에서는 이 확률을 기반으로 분포의 파라미터를 수정한다. 이 과정을 반복하다 보면 수렴하는 순간이 올텐데 이때까지 반복하는 것이다.   

<img src="{{ '/assets/images/ad26.png' | relative_url }}" alt="Image" width="700">   

위의 과정을 수식으로 표현한 것이다.  
Expectation은 각 데이터가 m번째 Gaussian에서 나왔을 확률을 의미하고, M-Step에서 사용하는 mixture weight는 m번째 가우시안에 속할 확률을 모두 더한 뒤, 전체 데이터 개수로 나눈 것이다.

m번째 가우시안의 평균은 그 가우시안에 속할 확률로 가중치를 준 평균이고 분산은 그 집단에 속할 확률로 가중된 분산으로 이해하면 될 것 같다. 이렇게 S-Step과 M-Step을 반복해서 최적의 값을 찾는다.

Mixture of Gaussian Distribution 에서 몇개의 Gaussian Distribution을 사용할지도 중요한 문제인데, likelihood를 최대화하는 분포의 개수를 찾는 과정도 필요하다.

---

## 참고

이 글은 강필성 교수님의 Business Analytics (anomaly detection) 유튜브 강의를 참고하여 작성한 글입니다.

https://www.youtube.com/watch?v=kKZM8bxwQbA&list=PLetSlH8YjIfWMdw9AuLR5ybkVvGcoG2EW&index=15
