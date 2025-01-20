---
title: TT Decompostion
author: dontempty
date: 2024-04-15 20:30:00 +0900
categories: [study, Tensor Decomposition]
tags: [TT Decompostion]
use_math: true
description: >
    Tensor Train Decomposition
---


# TENSOR-TRAIN DECOMPOSITION

## CP Decomposition(canonical decomposition)

<https://dontempty.github.io/CP-Decomposition/>

Approximate a given tensor B by a tensor A $\approx$ B with elements  

$A(i_1, i_2, ..., i_d) = G_{1}(i_1)G_{2}(i_2)...G_{d}(i_d)$  

$G_k(i_k)$ is an $r_{k-1} \times r_{k}$ matrix

## Tensor Train Decomposition

### 1. Introduction
$$A(i_1, i_2, ..., i_d) = G_{1}(i_1)G_{2}(i_2)...G_{d}(i_d)\tag{1.2}$$  

$G_k(i_k)$ is an $r_{k-1} \times n_{k} \times r_{k}$ array  

$$A(i_1, i_2, ..., i_d) = \sum\limits_{\alpha_{0}, ..., \alpha_{d}} G_{1}(\alpha_0, i_1, \alpha_1)G_{2}(\alpha_1, i_2, \alpha_2)...G_{d}(\alpha_{d-1}, i_d, \alpha_d)\tag{1.3}$$  
boundary conditions : $r_0$ = $r_d$ = 1  
TT ranks $r_k$ : $\alpha_{k}$ 의 원소 개수

![1](https://github.com/dontempty/dontempty.github.io/assets/155451345/36bd4f8a-b31f-436a-a559-cc43f065c1b4)

#### Main goal

1. Represnt Tensor in the TT format  
2. Approsimate given tensor with prescribed $\epsilon$  
     $||A-B||_F \leq \epsilon ||B||_F$  
     F : frobenius norm

### 2. Definition of the format and compression from full array to the TT-format

1. TT-format : 주어진 tensor A core tensor $G_k$들로 분해한 형태  
2. Unfolding matrix : tensor A 를 reshape 해서 matrix로 변환한 형태  
    $A_{k} = A_{k}(i_1, .., i_k : i_{k+1} .., i_d)$  
    $A_k = reshape(A, \prod\limits_{s=1}^{k}n_s, \prod\limits_{s=k+1}^{d}n_s)$ A tensor를 저 형태로 변환합니다.

#### Theorem 2.1
If for each unfolding matrix $A_k$ of tensor $A$  
$rank A_k \leq r_k$,  
then there exists a decomposition (1.3) with TT-ranks not higher than $r_k$

중간다리를 담당하는 rank가 $r_k$로 정해져 있을 때 랭크를 줄여서 TT format으로 만들수 있다는 뜻입니다. 즉 데이터를 적게 사용한다는 뜻입니다.

* proof. 쓸게 너무 많아서 생략

#### Theorem 2.2
Suppose that unfolding matrix $A_k$ of the tensor $A$  
Then  $TT-SVD$ computes a tensor $B$ in the TT-format with TT-ranks $r_k$ and  
$$||A-B||_F \leq \sqrt{\sum\limits_{k=1}^{d-1}\epsilon_{k}^{2}}$$ 

$ A_k = R_k + E_k$  
$rank R_k = r_k$  
$||E_k||_F = \varepsilon_k\, k = 1, ..., d-1$

TT-SVD가 SVD를 활용하여 분해하는 방법인데 SVD에서 모든 singular vector를 사용하는 것이 아니고 일부분만 사용하여 근사하면 다음과 같은 estimatation을 얻을 수 있습니다. 

* proof. 간단하게만 작성합니다.  

우선 1번째에 대해서 unfolding을 한 다음 SVD를 적용합니다.
$$A_1 = U_1 \Sigma_1 V_1 + E_1 = U_1 B_1 + E_1$$
여기서 $U_1: n_1 \times r_1, orthogonal\ columns$  
$B_1$ 은 $(d-1)\  dimensional$ tensor 라고 생각하시면 됩니다. 

$A$ : given tensor  
$B$ : TT-SVD 로 근사한 tensor

$$
\begin{align}
||A-B||_F^2 & = ||A_1-U_1\hat{B_1}||_F^2 = ||A_1-U_1(\hat{B_1+B_1-B_1})||_F^2 \\
 & = ||A_1-U_1B_1||_F^2 + ||U_1(B_1-\hat{B_1})||_F^2
\end{align}$$

$B_1 = \Sigma V$ 인데 이를 SVD로 근사하는 $\hat{B_1}$ 고려합니다.  

(1) 에서 (2)으로 넘어가는 과정입니다.  
let $\square = A_1-U_1B_1$ , $\bigcirc = U_1(B_1-\hat{B_1})$

$$
\begin{align*}||A_1-U_1(\hat{B_1+B_1-B_1})||_F & = trace((\square+\bigcirc)^T(\square+\bigcirc)) \\
& = trace(\square^T\square + \square^T\bigcirc + \bigcirc^T\square + \bigcirc^T\bigcirc) \\
& = trace(\square^T\square + \bigcirc^T\bigcirc)
\end{align*}$$  
$$since\ trace(\square^T\bigcirc) = trace(\bigcirc^T\square) = 0$$  
$$
\begin{align*}\square^T\bigcirc & = (A_1-U_1B_1)^T(U_1(B_1-\hat{B_1})) \\
& = A_1^TU_1(B_1-\hat{B_1}) - B_1^TU_1^TU_1(B_1-\hat{B_1}) \\
& = B_1^T(B_1-\hat{B_1}) - B_1^T(B_1-\hat{B_1}) 
\end{align*}$$
$$since \ U_1\ has\ orthonormal\ columns \\  and \ B_1 = U_1^TA_1$$

$$||A_1-U_1(\hat{B_1+B_1-B_1})||_F \leq \varepsilon_1^2 + ||B_1-\hat{B_1}||_F^2$$

위에서 $B_1$을 근사하는 $\hat{B_1}$에서도 위와 같은 과정을 반복하면 다음과 같은 결과를 얻을 수 있습니다.  
$$||B_1 - \hat{B_1}||_F \leq \sum\limits_{k=2}^{d-1} \varepsilon_k^2$$

즉 다음과 같은 결론을 얻을 수 있다.  
$$||A - \hat{A}||_F \leq \sum\limits_{k=1}^{d-1} \varepsilon_k^2$$

뒤에서도 설명하곘지만 정리를 해보자면 Tensor를 unfolding하여 matrix로 바꾼 뒤 SVD를 이용하여 $U_k$들을 뽑아서 tensor core $G_k$들을 추출한다.

#### Corollary 2.3
If a tensor $A$ admits a cannoical approximation with R terms and accuracy $\varepsilon$, then there exists a TT-approximation with TT-ranks $r_k \leq R$ and accuracy $\sqrt{d-1}\varepsilon$

#### Corollary 2.4
Given a tensor $A$ and rank bounds $r_k$, the best approximation to $A$ in the Frobenius norm with TT-ranks bounded by $r_k$ always exsits (denoted by $A^{best}$), and the TT-approximation $B$ computed bythe TT-SVD algorithm is quasioptimal  

여기서 tensor의 rank는 위에서 했던 각 $B_k$ 마다 SVD에서 사용한 vector의 개수입니다.  
* proof. 간단하게만 작성합니다.

$\varepsilon = \inf\limits_{C}||A-C||$,  where the over all tensor trains with TT-ranks bounded by $r_k$  
infimum정의에 따라서 $A$로 수렴하는 $B^{(s)}$ 수열이 존재하고, 각각의 성분들이 bounded하기 때문에 수렴하는 부분 수열이 존재하고 이것을 $B^{min}$이라고 합니다.  
Check : Since the set of matrices of rank not higher then $r_k$ is closed set   
 결국 부분수열 또한 $A$로 수렴하고 closed set의 limit point이기 때문에 rank $B^{min} \leq r_k$ 랭크들도 유계이다.

 check : the set of matrices of rank not higher then $r_k$ is closed set

아직 증명을 완변하게 이해하지 못 했습니다.

<https://math.stackexchange.com/questions/43192/how-to-prove-that-the-collection-of-rank-k-matrices-forms-a-closed-subset-of-the>  

간단하게 설명하자면 continunous function을 잘 만들어서 closed set의 preimage가 우리가 원하는 set이기 때문에 closed set임을 보일 수 있습니다.

### TT-SVD algorithm
![2](https://github.com/dontempty/dontempty.github.io/assets/155451345/ccc4148f-45e9-47b2-95d0-1b8dbfc5f150)

간단히 설명하자면 tensor를 unfolding하여 SVD를 통해 core tensor를 추출합니다.

### 3. Rounding in TT-format

잘 모르겠습니다.
