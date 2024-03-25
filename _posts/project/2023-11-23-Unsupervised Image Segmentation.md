---
title: Unsupervised Image Segmentation
author: dontempty
date: 2023-11-23 20:30:00 +0900
categories: [project, image segmentation]
tags: [image segmentation]
description: >
    Unsupervised Image Segmentation
---

# Unsupervised Image Segmentation  
Fully unsupervised learng에 대해서 다루고 있습니다. 
3가지 조건을 만족하도록 학습을 유도합니다.
1. 비슷한 특징(색이 비슷한)의 픽셀을 같은 label로 예측합니다.
2. 주변의 픽셀(거리가 가까운)을 같은 label로 예측합니다.
3. label의 개수가 많도록 유지합니다.

# 1. INTRODUCTION

CNN을 기반으로 만든 모델입니다.
전체적인 아이디어는 이미지를  clustering 해서 image segmentation을 진행합니다.

## Let 
$$x_{n}$$: p dimention feature (RGB 3차원 벡터)
$$c_{n}$$: cluster label
$$f$$: 우리 모델

# 2. METHOD

## 2.1 Constraint on feature similarity

let  
$$v_n: RGB\ vector\in\ \R^3$$  
$$I: 각\ 픽셀마다\ v_n을\ 모아놓은 것(그냥\ 이미지\ 배열)$$  
$$component: CNN(2D)-ReLU-batch\ norm $$  
$$W_c \in \R^{q \times p}$$  

M개의 component를 연결한다. 마지막에는 $(y_n = W_cx_n+b_c)$을 적용해서 마지막 label을 얻는다.  
$$W_{c}$$에서 결국 각 픽셀마다 q개의 라벨값을 예측하는데 argmax함수를 사용해서 최종적인 라벨을 결정한다.  

## 2.2 Constraint on spatial continuity

let
$$S_k: k-superpixel$$  
$$|c_n|: S_n에서\ c_n의 개수$$  

1. K(큰 수)개의 superpixel을 뽑는다.  
2. 모든 픽셀이 superpixel과 같은 cluster label을 가지도록 유도할 것이다. 각 superpxiel 안에 있는 cluster label 중에 가장 많이 등장하는 cluster label로 바꾼다. 위 내용은 SLIC라는 논문의 내용이다. 기본적으로 k-means 알고리즘 기반으로 superpixel을 update한다. 결과적으로 밑에 사진처럼 cluster을 만든다. k-means 에서 거리를 계산하는 것처럼 superpixel은 rgb_distance와 pixel_distance 합한 값을 사용한다.
자세한 설명 <https://hydragon-cv.info/entry/Superpixels-슈퍼-픽셀-알고리즘-작성중>

## 2.3 Constraint on the number of unique cluster label

예측한 cluster label의 개수가 만약 1개인 경우 정확한 예측이 일어날 수 없습니다. 그래서 batch normalization을 활용했습니다.   
$$y'_{n, i} = \frac{y_{n, i}-\mu_i}{\sqrt{\sigma^2_i} + \epsilon}$$  
하지만 이것이 cluster label의 개수의 minimum값이 늘어나는 것을 보장하지 않습니다.  
![3](https://github.com/dontempty/dontempty.github.io/assets/155451345/e4e40a80-31cf-441d-9ee5-f167c3cfebc1)

## 2.4 Learning network by backpropagation

어떻게 self-train을 할 것인지 설명하겠습니다.  

1. cluster label을 예측한다.  
2. 예측된 cluster label을 통해 모델을 train합니다.  

1번의 경우 2.2에서 설명했던 superpixel refinment를 사용합니다.  
2번의 경우 gradient descent를 사용합니다.  
논문에서는  momentum을 활용한 stochastic gradient descent를 사용합니다.  
Figure 1  
![4](https://github.com/dontempty/dontempty.github.io/assets/155451345/46a5da5a-fee9-4446-b405-235aabb0433a)

# 3. RESULTS

Berkeley Segmentation Dataset and Benchmark(BSDS500) 데이터를 test로 사용했습니다.   
k-means 와 graph-based segmentation method(GS) 2가지 방법을 사용했습니다.  
k-means의 경우 RGB값을 ($\alpha \times\alpha$) 범위를 가지고 합쳤습니다.  
GS의 경우 threshold를 $\beta$로 잡아서 진행했습니다.  

$$K(\alpha, k), GS(\beta)$$  
Fugure 3  
![5](https://github.com/dontempty/dontempty.github.io/assets/155451345/ffd5219b-8b48-468a-bf3f-ad0e1ced2b10)
F-measure = precision, recall의 평균  

learning rate 0.1, momentum 0.9 는 경험적으로 얻은 최적의 하이퍼파라미터입니다.  

Figure 2  
![6](https://github.com/dontempty/dontempty.github.io/assets/155451345/fade27f5-85ff-4d87-93fa-4e1f4d15df1c)
논문에서 제시하는 예시입니다.  

Figure 4  
![7](https://github.com/dontempty/dontempty.github.io/assets/155451345/7d757fd0-7a82-448c-b55d-67e33fa4ebd1)
각각의 방법들의 그래프입니다.  
### 솔직히 여기서 이 사람들이 어떻게 IOU를 계산 했는지 모르겠습니다. 모델은 object를 segment만 하고 classification을 못하는 것 같은데 어떻게 계산했는지 모르겠습니다.  

# 4. RELATED WORK
The DEC algorithm iteratively refines clusters by minimizing the KL divergence loss between softassigned data points with an auxiliary target distribution, whereas the proposed method simply minimizes the softmax loss based on the estimated clusters. Similar approaches, such as maximum margin clustering  

그냥 이런게 있다 정도만 알고있습니다.  

# 5. CONCLUSION
1. CNN과 superpixel refinement를 통해 좋은 결과를 얻었다고 합니다.  
2. 지도학습, 준지도학습, 비지도학습 3가지를 비교했을 때 성능은 순서대로 더 좋았습니다.

# 6. MY WORK
<https://github.com/dontempty/image_segmentation>
코드를 구현했고 주석도 코드 설명 및 주석을 달았습니다.  

기존 코드와 달라진 점은 minlabels라고 예측한 cluster label의 개수가 minlabels보다 낮아지면 학습을 강제로 종료하는 코드가 있습니다. 예측하는 cluster label의 개수가 작아지는 것을 방지하기 위함입니다. 여기서 이 값이 하이퍼파라미터인데 이것을 추론하는 코드를 추가 했습니다.  

infer = True 라고 지정하면 minlabel값을 계산해서 학습을 진행합니다.  

$$Var = \frac{Var_R+Var_G+Var_B}{3}\\minlabels = (Var//10)+3$$  

$$Var_{R}$$은 R색상의 Variance입니다.  
나머지는 각 색상별로의 분산입니다.  

기본적인 아이디어는 색의 분산이 클수록 다양한 색을 가지고 있을 것입니다. 즉 다양한 물체를 포함하고 있다고 생각할 수 있습니다. 그래서 색상의 분산을 이용해서 minlabels를 추론해보기로 했습니다.  

단순히 경험 상 minlabels가 7, 8 정도가 적절해 보였는데 그것에 맞춰서 계산하는 방법입니다.  

그렇게 좋은 결과는 얻지 못 했습니다.  

# 7. 잡담
2020년에 더 발전시킨 논문이 나왔고 논문 주소입니다. <https://arxiv.org/pdf/2007.09990.pdf>

성능을 평가하는 방법을 논문에서는 제시하지 않아서 문제가 있었습니다. 임의로 성능을 평가해보기는 했지만 너무 좋지 않았습니다.  
그래도 눈으로 보기에는 어느정도 성능을 보여주고는 있습니다.

