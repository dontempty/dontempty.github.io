---
layout: post
title: Inverse Problem in SIR model
subtitle: 파라미터 추정하기
gh-repo: dontempty/dontempty.github.io
gh-badge: [star, fork, follow]
cover-img: /assets/img/path.jpg
tags: [inverse problem]
comments: true
---

# Inverse Problem in SIR moel

SIR model의 $\beta , \gamma$ 계수를 추정하는 작업을 합니다.

## 1. SIR model

### $\frac{dS}{dt} = -\frac{\beta IS}{N}$  
### $\frac{dI}{dt} = \frac{\beta IS}{N}-\gamma I$  
### $\frac{dR}{dt} = \gamma I$
### $\frac{dS}{dt}+\frac{dI}{dt}+\frac{dR}{dt} = 0$  

전염병이 퍼지는 양상을 설명하는 모델입니다.  

S: 정상인 수
I: 감염자 수
R: 회복자 수
![1](https://github.com/dontempty/dontempty.github.io/assets/155451345/8219c186-cd89-46ab-96ed-5b07a70c1c35)

## 2. Parameter estimation (without noise)
explicit euler를 사용하여 얻은 데이터를 사용했습니다.  
iteration: 100  
$\beta$: 0.25  
$\gamma$: 0.1  
initial condition: S[0]=0.98, I[0]=0.02, R[0]=0.0  
![1](https://github.com/dontempty/dontempty.github.io/assets/155451345/14cb0c76-6a4d-45fc-865c-a5ce94a5fa12)

## (1) Analytic solution
간단히 말해서 미분이 0되는 지점의 $\beta , \gamma$를 찾아서 계수를 추정하는 것입니다.  
뒤에서 다루겠지만 앞으로 정의하는 loss function이 convex임을 증명함으로써 optimal한 solution을 얻을수 있습니다.

### 1. first-order forward difference

$L = \sum\limits_{j=1}^{n-1} \{(s_{j+1}-s_{j}+(\frac{\beta}{N}\cdot i_{j}s_{j}))^2 + (i_{j+1}-i_{j}-(\frac{\beta}{N}\cdot i_{j}s_{j}-\gamma\cdot i_{j}))^2 + (r_{j+1}-r_{j}-(\gamma\cdot i_{j}))^2 \}$  

$\beta = \frac{\begin{matrix}
-\frac{8}{N}
\sum\limits_{j=1}^{n-1}
i_{j}s_{j}(s_{j+1}-s_{j}-i_{j+1}+i_{j})
\cdot
\sum\limits_{j=1}^{n-1}
i_{j}^2\\
-\frac{4}{N}
\sum\limits_{j=1}^{n-1}
i_{j}(i_{j+1}-i_{j}-r_{j+1}+r_{j})
\cdot
\sum\limits_{j=1}^{n-1}
i_{j}^2s_{j}
\end{matrix}}{\begin{matrix}
\frac{16}{N^2}\sum\limits_{j=1}^{n-1}
i_{j}^2s_{j}^2\cdot
\sum\limits_{j=1}^{n-1}i_{j}^2-\frac{4}{N^2}(\sum\limits_{j=1}^{n-1}i_{j}^2s_{j})^2
\end{matrix}}$  

$\gamma = \frac{\begin{matrix} 
-\frac{8}{N^2}
\sum\limits_{j=1}^{n-1}
i_{j}^2s_{j}^2
\cdot
\sum\limits_{j=1}^{n-1}
i_{j}(i_{j+1}-i_{j}-r_{j+1}+r_{j})\\
-\frac{4}{N^2}
\sum\limits_{j=1}^{n-1}i_{j}^2s_{j}
\cdot
\sum\limits_{j=1}^{n-1}
i_{j}s_{j}(s_{j+1}-s_{j}-i_{j+1}+i_{j})
\end{matrix}}{\begin{matrix}
\frac{16}{N^2}
\sum\limits_{j=1}^{n-1}
i_{j}^2s_{j}^2
\cdot
\sum\limits_{j=1}^{n-1}
i_{j}^2-\frac{4}{N^2}(\sum\limits_{j=1}^{n-1}
i_{j}^2s_{j})^2
\end{matrix}}$

### 2. first-order backward difference

$L = \sum\limits_{j=1}^{n-1} \{(s_{j+1}-s_{j}+(\frac{\beta}{N}\cdot i_{j+1}s_{j+1}))^2 + (i_{j+1}-i_{j}-(\frac{\beta}{N}\cdot i_{j+1}s_{j+1}-\gamma\cdot i_{j+1}))^2 + (r_{j+1}-r_{j}-(\gamma\cdot i_{j+1}))^2 \}$

$\beta = \frac{\begin{matrix} 
-\frac{8}{N}\sum\limits_{j=2}^{n-1}(i_{j-1}i_{j}s_{j}-i_{j}s_{j-1}s_{j}-i_{j}^2s_{j}+i_{j}s_{j}^2)\cdot\sum\limits_{j=2}^{n-1}i_{j}^2
\\
+\frac{4}{N}
\sum\limits_{j=2}^{n-1}(i_{j-1}i_{j}-i_{j}^2-i_{j}r_{j-1}+i_{j}r_{j})\cdot\sum\limits_{j=2}^{n-1}i_{j}^2s_{j}
\end{matrix}}{\begin{matrix}
\frac{16}{N^2}\sum\limits_{j=2}^{n-1}i_{j}^2s_{j}^2\cdot\sum\limits_{j=2}^{n-1}i_{j}^2-\frac{4}{N^2}(\sum\limits_{j=2}^{n-1}i_{j}^2s_{j})^2
\end{matrix}}$

$\gamma = \frac{\begin{matrix} 
+\frac{8}{N^2}
\sum\limits_{j=2}^{n-1}i_{j}^2s_{j}^2
\cdot
\sum\limits_{j=2}^{n-1}(i_{j-1}i_{j}-i_{j}^2-i_{j}r_{j-1}+i_{j}r_{j})\\
-\frac{4}{N^2}
\sum\limits_{j=2}^{n-1}i_{j}^2
\cdot
\sum\limits_{j=2}^{n-1}
(i_{j-1}i_{j}s_{j}-i_{j}s_{j-1}s_{j}-i_{j}^2s_{j}+i_{j}s_{j}^2)
\end{matrix}}{\begin{matrix}
\frac{16}{N^2}\sum\limits_{j=2}^{n-1}i_{j}^2s_{j}^2\cdot\sum\limits_{j=2}^{n-1}i_{j}^2-\frac{4}{N^2}(\sum\limits_{j=2}^{n-1}i_{j}^2s_{j})^2
\end{matrix}}
$

### 3. second-order centered difference

$L = \sum\limits_{j=1}^{n-1}\{
(\frac{s_{j+1}-s_{j-1}}{2}+\frac{\beta}{N}\cdot i_{j}s_{j})^2 + 
(\frac{i_{j+1}-i_{j-1}}{2}-\frac{\beta}{N}\cdot i_{j}s_{j}+\gamma\cdot i_{j})^2+
(\frac{r_{j+1}-r_{j-1}}{2}-\gamma\cdot i_{j})^2
\}$

$\beta = \frac{\begin{matrix} 
-\frac{4}{N}
\sum\limits_{j=2}^{n-1}
(i_{j-1}i_{j}s_{j}-i_{j}s_{j-1}s_{j}-i_{j}i_{j+1}s_{j}+i_{j}s_{j}s_{j+1})
\cdot
\sum\limits_{j=2}^{n-1}
i_{j}^2\\
+\frac{2}{N}
(\sum\limits_{j=2}^{n-1}
(i_{j}r_{j+1}-i_{j}r_{j-1})-i_{1}i_{2}+i_{n-1}i_{n})
\cdot
\sum\limits_{j=2}^{n-1}i_{j}^2s_{j}
\end{matrix}}
{\begin{matrix}
\frac{16}{N^2}\sum\limits_{j=2}^{n-1}i_{j}^2s_{j}^2\cdot\sum\limits_{j=2}^{n-1}i_{j}^2-\frac{4}{N^2}(\sum\limits_{j=2}^{n-1}i_{j}^2s_{j})^2
\end{matrix}}
$

$\gamma =
\frac{\begin{matrix} 
\frac{4}{N^2}
\sum\limits_{j=2}^{n-1}i_{j}^2s_{j}^2\cdot
(\sum\limits_{j=2}^{n-1}
(i_{j}r_{j+1}-i_{j}r_{j-1})-i_{1}i_{2}+i_{n-1}i_{n})
\\
-\frac{2}{N^2}
\sum\limits_{j=2}^{n-1}
i_{j}^2s_{j}\cdot
\sum\limits_{j=2}^{n-1}
(i_{j-1}i_{j}s_{j}-i_{j}s_{j-1}s_{j}-i_{j}i_{j+1}s_{j}+i_{j}s_{j}s_{j+1})
\end{matrix}}
{\begin{matrix}
\frac{16}{N^2}\sum\limits_{j=2}^{n-1}i_{j}^2s_{j}^2\cdot\sum\limits_{j=2}^{n-1}i_{j}^2-\frac{4}{N^2}(\sum\limits_{j=2}^{n-1}i_{j}^2s_{j})^2
\end{matrix}}
$

## (2) Physics-Informed Neural Network(PINN)
PINN을 이용하여 계수를 추정합니다.  
![2](https://github.com/dontempty/dontempty.github.io/assets/155451345/2cf53ec7-28f2-4c7b-a22c-fcedf66f62d6)

## (3) solution

|    |beta|gamma|
|---|---|---|
|real|0.25|0.1|
|first-order forward differences|0.37429|0.22289|
|first-order backward differences|0.25153|0.09894|
|second-order centered differences|0.25043|0.09928|
|PINN|0.25119|0.09994|

## 3. Parameter estimation (with noise)
explicit euler와 랜덤으로 계수를 달리해서 노이즈를 만들었습니다.  
iteration: 100  
$\beta$: 0.25+np.random.randn(1)*0.1  
$\gamma$: 0.1+np.random.randn(1)*0.1  
initial condition: S[0]=0.98, I[0]=0.02, R[0]=0.0  

![3](https://github.com/dontempty/dontempty.github.io/assets/155451345/3d8852a4-bafb-4741-b1ea-b4c0143dc2d3)

|    |beta|gamma|
|---|---|---|
|real|0.25|0.1|
|first-order forward differences|0.45016|0.25932|
|first-order backward differences|0.27620|0.08942|
|second-order centered differences|0.27644|0.09408|
|PINN|0.27391|0.09346|

## 4. Proof

### 1. loss function is convex

### 2. Denominator of analytic solution bigger then 0

## 5. Conculsion

explicit euler 보다는 implicit euler 의 성능이 더 좋은것을 관찰할 수 있다.  
loss 함수가 convex임을 증명했고 parameter들의 explicit한 표현에서 분모는 항상 0이상임을 증명했다.

## 6. Reference

<https://onlinelibrary.wiley.com/share/5XERPV9MFIVGNHBSBFYT?target=10.1002/mma.6985>

## 7. Code

<https://github.com/dontempty/Inverse_problem>

## 잡담

order가 올라갈수록 정확도가 더 높아지는 것을 확인할 수 있습니다.  
다음에는 SIR model 말고도 다른 model에도 적용해볼 생각입니다.  
논문에서 오타가 너무 많아서 읽을 때 주의 깊게 보시길 바랍니다.  
