---
layout: single
title:  "[Anomaly] Local outlier factor (LOF)"
permalink: anomaly/LOF
toc: true
toc_sticky: true
categories: 
  - anomaly
---

# Local outlier factor (LOF)

LOF는 지금까지 알아봤던 밀도 기반의 이상탐지와 비슷하다.  
어떠한 객체가 주어졌을 때 해당하는 객체의 Local density를 계산하는 것이 LOF의 주요 개념이다.

어떠한 밀도가 높은 영역 C1, 밀도가 낮은 영역 C2가 있다고 가정하자, 이 때 각각의 영역에 대한 outlier를 선정할 때 단순히 거리를 기준으로 outlier를 선정하기에는 무리가 있다. 따라서 local density를 계산한다는 개념이다.

밀도가 높은 영역 C1에 대해서는 거리가 조금 멀게 위치한 객체도 어느정도 고려한다.   

<img src="{{ '/assets/images/AD41.png' | relative_url }}" alt="Image" width="400">   

밀도가 다른 영역에서 같은 기준으로 outlier를 선정할 수 없다는 것은 당연해 보인다.

---

## LOF 알고리즘의 구성 요소

LOF 알고리즘은 크게 5가지의 정의로 이루어진다.

---

### 1. k-distance of an object p

객체 p의 k-distance는 p로부터 거리가 가까운 순으로 k 번째 위치한 점 사이의 거리를 의미한다.

d(p,q)   

<img src="{{ '/assets/images/AD42.png' | relative_url }}" alt="Image" width="500">   

이 두가지 조건이 존재하는데,

- 객체 k-distance 와 같거나 작은 점의 개수가 o’이고, 이는 적어도 k보다는 커야한다.
- 객체 k-distance 보다 작은 점의 개수가 o’이고, 이는 최대 k-1 개여야 한다.

---

### 2. k-distance Neighborhood of an object p   

<img src="{{ '/assets/images/AD43.png' | relative_url }}" alt="Image" width="500">   

즉, Nk(p)는 p와의 거리가 p의 k-distance보다 작거나 같은 q의 집합이다.

---

### 3. Reachability Distance (도달 가능 거리)   



<img src="{{ '/assets/images/AD44.png' | relative_url }}" alt="Image" width="500">   

p에서 o 까지의 reachability distance는 o의 k-distance와 p와 o사이의 거리 중 큰 값을 의미한다.

이것은 k-distance 안쪽에 위치한 점들의 거리를 k-distance 의 거리로 치환하는 역할을 하게된다.   

<img src="{{ '/assets/images/AD45.png' | relative_url }}" alt="Image" width="500">   

---

### 4. Local Reachability Density of an object p (LRD)   

<img src="{{ '/assets/images/AD46.png' | relative_url }}" alt="Image" width="500">   

p의 k-distance neighborhood 속 객체들 o에서, p와 o 사이의 reachability distance가 분모이다.   

<img src="{{ '/assets/images/AD47png' | relative_url }}" alt="Image" width="550">   

<img src="{{ '/assets/images/AD48png' | relative_url }}" alt="Image" width="550">   

Case를 두개로 나누어 볼 수 있다.

첫 번째는 o의 k-distance neighborhood안에 p가 속해있는 경우이다. 이 때는 분모가 o의 k-distance가 될 것이다.

두 번째는 o의 입장에서 k-distance neighborhood에는 p 가 속하지 않는다. 이 경우는 분모가 그림에서 처럼 p의 k-distance들의 합이 된다.

즉, 밀도가 높은 한 가운데에 p가 존재한다면, LRD의 분모가 작기 때문에 LRD(p)는 커진다.  
반면에 밀도가 낮은 지역에 p가 위치한다면, LRD(p)는 작아진다.

---

### 5. Local Outlier Factor   

<img src="{{ '/assets/images/AD49png' | relative_url }}" alt="Image" width="550">   

마지막으로 Local Outlier Factor는 위와 같이 정의된다.  
결과적으로 밀도가 높은 영역 속에 밀도가 낮은 객체에 대해서는 LOF 값을 크게 해주는 것이다.   

<img src="{{ '/assets/images/AD410png' | relative_url }}" alt="Image" width="550">   

<img src="{{ '/assets/images/AD411png' | relative_url }}" alt="Image" width="550">   

---

## LOF의 장단점

LOF의 장점은 주변 객체들의 밀도를 고려하여 이상치를 판단하기 때문에 global한 이상치 뿐만 아니라 local적인 이상치도 검출할 수 있다.

LOF의 단점은 LOF값 자체가 가지는 뚜렷한 기준이 없기 때문에 LOF값만으로는 이상치 여부를 확정짓기는 어렵다. 또한 서로 다른 데이터셋에서의 임의의 객체의 LOF 값만을 비교해서 어떠한 객체가 더 이상치에 가까운지를 판단하기 어렵다.

---

## 참고

Breunig, M. M., Kriegel, H. P., Ng, R. T., & Sander, J. (2000, May).  
LOF: identifying density-based local outliers.  
In *Proceedings of the 2000 ACM SIGMOD international conference on Management of data* (pp. 93–104)

https://velog.io/@vvakki_/LOFLocal-Outlier-Factor  

강필성 교수님 Business Analytics (anomaly detection) (https://youtu.be/ODNAyt1h6Eg?si=JWqspxHTAw88bkZT)

