---
title: CP Decompostion
author: dontempty
date: 2024-03-12 20:30:00 +0900
categories: [study, Tensor Decomposition]
tags: [CP Decompostion]
use_math: true
description: >
    Canonical Tensor Decomposition
---

# CP decompostion
<https://github.com/dontempty/CP-decompostion>

## SVD
svd는 행렬을 3개의 행렬 $U, \Sigma, V$로 분해하는 작업을 합니다.  

$A = U\Sigma V^T$  
$\quad = \sigma_1 \vec u_1 \vec v_1^T + \sigma_2 \vec u_2 \vec v_2^T +\cdots+ \sigma_m \vec u_m \vec v_m^T $

시각적으로 표현하면 다음과 같습니다.  
![1](https://github.com/dontempty/tensor-decompostion/assets/155451345/2c256996-2bac-4f43-8a51-a529fe8b8d81)

여기가 정말 잘 설명해 줍니다.
<https://angeloyeo.github.io/2019/08/01/SVD.html>

## CP decompostion 
CP decomposition은 SVD와 유사하게 생각하시면 됩니다.   
2차원 배열인 행렬에 대해서 했던것 처럼 3차원 배열인 텐서에 대해서 유사한 방식으로 분해합니다.   
![2](https://github.com/dontempty/tensor-decompostion/assets/155451345/4380f47d-7bdd-4a5b-a3fc-753f2d8f7c6d)


### 계산하는 방법
우선 텐서를 reshape합니다.  
![3](https://github.com/dontempty/tensor-decompostion/assets/155451345/a6472ae1-c9e6-437d-a5cf-18f9d9005489)

위 그림에서 본대로 input_a $\cdot$ a = target_a 방정식을 새울 수 있습니다.  
least square문제이고 이를 a, b, c에 대해서 반복적으로 실행하여 최종적인 a, b, c를 구합니다.  

여기서 의문인 점은 (input_a)$^{T}$(input_a)의 역행렬의 존재성이 확실치 않습니다.  
그래서 찾아본 결과 이를 방지할 수 있는 방법이 아래 논문에 기제되어 있습니다.  
<https://iopscience.iop.org/article/10.1088/2632-2153/ab8240/pdf>  
정확히 읽어보지는 않았습니다. 그냥 방법이 있다 정도로만 알고 있습니다.

### 실험 결과 
이미지에 대해서 실험을 해본 경과 성능이 좋지는 않았습니다.
자세한 결과는 cp.ipynb를 참고하세요
svd를 이미지에 적용하면 RGB 각각의 배열에 svd를 적용하여 다시 합치는 것으로 알고 있습니다.
그런데 코드를 실행한 결과 조금 이상해서 이 부분에 대해서도 추후에 다시 작성하겠습니다.

