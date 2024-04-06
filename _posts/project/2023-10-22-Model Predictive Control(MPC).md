---
title: MPC
author: dontempty
date: 2023-11-10 20:30:00 +0900
categories: [project, MPC]
tags: [mpc]
use_math: true
description: >
    Inverse Problem in SIR model
---

# Model Predictive Control(MPC)

[MPC 란? (Model Predictive Control) 1. 기본 컨셉](https://sunggoo.tistory.com/65)

[제어조교 〈Ctrl튜브〉](https://www.youtube.com/@ctrl3283)

[[MPC] 모델예측제어 개요](https://pasus.tistory.com/228)

[MPC_handout.pdf](Model%20Predictive%20Control(MPC)%203cbc523c33994289b3f872c4c46c67c3/MPC_handout.pdf)

위 4개를 보면서 공부했습니다.

Notation은 위 내용을 혼용하여 사용하겠습니다.

드론의 동역학과 같이 구현하려고 한 것입니다.

[Drone ](https://www.notion.so/Drone-456301252bd947b4abfeed019cfda82a?pvs=21) 

### 코드

[https://github.com/donstrave/MPC.git](https://github.com/donstrave/MPC.git)

MPC는 현재 상태에서 제어 명령을 미리 설정하고 실행해봅니다. 그런 다음 상태를 평가하고 다시 계획하기를 반복합니다.

![Untitled](Model%20Predictive%20Control(MPC)%203cbc523c33994289b3f872c4c46c67c3/Untitled.png)

t 시점에서 t+N 시점까지 정보를 가져와서 어떻게 제어를 할 것인지 계획합니다.

# 2. MPC 상태공간 방정식

상태방정식: $\dot x = Ax + Bu$

출력방정식: $y=Cx$

MPC는 Discrete-time에서 구현한 모델이기 때문에 위 방정식을 discrete하게 만들어 줘야합니다.

DT 상태방정식: $x(k+1) = A_dx(k) + B_du(k)$

DT 출력방정식: $y(k) = C_dx(k)$

정확한 이유는 모르겟지만 $\Delta x$를 사용합니다.

# 3. prediction of state and output

Difference state-space model

$x(k+1)-x(k) = A_d(x(k)-x(k-1))+B_d(u(k)-u(k-1))$

$\Rightarrow \Delta x(k+1) = A_d \Delta x(k)+B_d \Delta u(k)$

Output

$y(k+1)-y(k) = C_d\Delta x(k+1)=C_dA_d\Delta x(k)+C_dB_d\Delta u(k)$

$\Rightarrow y(k+1) = y(k)+C_dA_d\Delta x(k) + C_dB_d\Delta u(k)$

이를 행렬 형태로 변환하여 쓰면 다음과 같습니다.

$x_a(k) = \begin{bmatrix} \Delta{x(k)} \\ y(k) \end{bmatrix}$

$$\begin{bmatrix} \Delta{x(k+1)} \\ y(k+1) \end{bmatrix} = \begin{bmatrix} \ A_d \quad \ 0 \\ C_dA_d \ 1 \end{bmatrix} \begin{bmatrix} \ \Delta{x(k)} \\ y(k)\end{bmatrix}+\begin{bmatrix} B_d \\ C_dB_d\end{bmatrix} \Delta{u(k)}$$

$$y(k) = \begin{bmatrix} 0 \quad I \end{bmatrix} \begin{bmatrix} \Delta{x(k+1)} \\ y(k) \end{bmatrix}$$

상태 변수$\Delta x$와 관측 변수$y$를 같은 벡터에 넣어서 동시에 설명하고 있다.

위 내용을 간단히 작성하면 다음과 같습니다.

$x_a(k+1) = A_ex_a(k)+B_e \Delta{u(k)}$

$y_a(k) = C_ex_a(k)$

$N_p$ : 예측하고 싶은 미래의 출력 수

$N_c$: 제어 입력의 수

$(N_c < N_p)$

미래에 행하게 될 제어값을 다음과 정리합니다.

$Future\ control\ trajectroy$  
$\Delta{u(k)},\ \Delta{u(k+1)}\ ... \ \Delta{u(k+N_p-1)}$

미래의 상태 변수를 다음과 같이 정리합니다.

$Future\ state\ variable$  
$x_a(k+1|k),\ x_a(k+2|k), \ ... \ x_a(k+N_p|k)$

다음 상태를 현재 상태 변수$x_a(k)$를 이용하여 다음과 같이 정리할 수 있습니다.

$$
\begin{aligned}
x_a(k+1|k) = & A_e x_a(k)_a+B_eu(k) \\
x_a(k+2|k) = & A_e x(k+1|k)_a + B_eu(k+1) \\
& A_e^2 x_a(k) + A_e B_e\Delta{u(k)} + B_e \Delta{u}(k+1)\\
& .\\
& . \\
& x_a(k+N_p|k) = A_e^{N_p}x_a(k)+A_e^{N_p-1}B_e\Delta{u(k)} + ... + A_e^{N_p-N_c}B_e\Delta{u(k+N_c-1)}
\end{aligned}
$$

출력 변수 $y(k)$에 대해서도 미래 시점의 관측 값을 다음과 같이 정리할 수 있습니다.

$y(k) = C_ex(k)$

$y(k+N_p|k) = C_e A_e^{N_p}x_a(k) + C_e A_e^{N_p-1}B_e\Delta{u(k)} + ... + C_e A_e^{N_p-N_c}B_e\Delta{u(k+N_c-1)}$

$$Y = \begin{bmatrix}
y(k+1|k)\\
y(k+2|k)\\
..\\
y(K+N_p|k)
\end{bmatrix}\quad 
\Delta{U} = \begin{bmatrix}
\Delta{u(k)}\\
\Delta{u(k+1)}\\
..\\
\Delta{u(k+N_c-1)}
\end{bmatrix}$$

$$F = \begin{bmatrix}
  C_eA_e \\
  C_eA_e^2 \\
  .. \\
  C_eA^{N_p}
\end{bmatrix} \quad
\Phi = 
\begin{bmatrix}
C_eB_e & 0 & ... &0\\
C_eA_eB_e & C_eB_e& ... & 0\\
\vdots & \ddots \\
C_eA_e^{N_p-1}B_e & ... & & C_eA_e^{N_p-N_c}B_e
\end{bmatrix}$$

$Y = Fx_a(k) + \Phi \Delta{U}$

위 내용에서 설명하는 것은 현재 상태 $x(k)$만 알고 있더라도 미래에 어떻게 움직일 것인지 알 수 있다. 

블로그의 말을 빌리자면 현재 상태를 알고 있으면 $N_c$개의 입력을 잘 만들어서 $N_p$개의 출력 즉 미래의 상태를 확인 할 수 있다는 것이다.

이를 이용해서 미래에 원하는 상태가 있다면 그렇게 되도록 control을 만들 수 있다는 것이다.

# 4. Controller Design

현재 하고 있는 일의 목적은 reference가 주어졌을 경우 이를 따라가도록 하려면 어떤 control을 주어야 하는가 입니다.

즉 reference와 예측된 미래의 상태($Y$) 차이를 minimize하는 방향으로  control을 만드는 것과 같습니다.

$R_s = reference$

$J =(R_s-Y)^TQ(R_s-Y) +\Delta{U}^T\bar{R}\Delta{U}$

로스함수를 위와 같이 둡니다.

$R_s = 
\begin{bmatrix}
1,\ 1, \ ...\ 1
\end{bmatrix} r(k)$  : $N_p$개

k시점의 reference부터 $k+N_P-1$시점 까지의 reference를 가져와서 $Y$와 비교합니다.

우리가 제어하는 것은 $\Delta{U}$이기 때문에 $J$를 $\Delta{U}$로 미분하여 그라디언트를 계산합니다. 

로스함수에 $Y$를 대입하여 $\Delta{U}$에 대해 정리합니다.

$J=(Rs−Y)^TQ(Rs−Y)+ΔU^T\bar{R}ΔU \: \leftarrow \; Y = Fx_a(k) + \Phi \Delta{U}$

$J=(R_s−Fx_a(k))^TQ(R_s−Fx_a(k))−2ΔU^TΦ^T(R_s−Fx_a(k))+ΔU^T(Φ^TΦ+\bar{R})ΔU$

위 식을 $\Delta{U}$로 편미분합니다.

$\frac{\partial{J}}{\partial{\Delta{U}}} = 
-(R_s-Fx_a-\Phi\Delta{U})^TQ\Phi+\Delta{U}^T\bar{R}$ 

최적의 $\Delta{U}$는 미분값이 0이 되는 지점이므로 다음과 같이 쓸 수 있다.

$\frac{\partial{J}}{\partial{\Delta{U}}} = -(R_s-Fx_a-\Phi\Delta{U})^TQ\Phi+\Delta{U}^T\bar{R}=0$

$\Delta{U}^* = (\bar{R}+\Phi^TQ\Phi)^{-1}\Phi^TQ(R_s-Fx_a)$

$\frac{\partial^2{J}}{\partial{\Delta{U}^2}} = R+\Phi^TQ\Phi>0$ 이므로 convex임을 알 수 있다. 
즉 위의 $\Delta{U}$가 최적의 값이다.

우리가 원하는 값은 현재사용할 $\Delta u$이기 때문에 제일 앞의 값만 사용한다.

$\Delta{u} = \begin{bmatrix}
I_m,\, O\, ...\, O
\end{bmatrix}(\bar{R}+\Phi^TQ\Phi)^{-1}\Phi^TQ(R_s-Fx_a)$

# 5. MPC with Constraints

handout에서는 $\Delta{u}$에 관한 제약 조건을 주었지만 여기서는 $Y$즉 위치에 대한 제약 조건을 주었다.

기존의 로스함수에 다음을 추가한다.

$J_{c} = (L-Y)^T\Omega(L-Y) + (Y-U)^T\Omega(Y-U)$

$L$: 위치의 lower bound

$U$: 위치의 upper bound

$\frac{\partial{J_c}}{\partial{\Delta{U}}} = 
-(L-Fx_a-\Phi\Delta{U})^T\Omega\Phi + (Fx_a+\Phi\Delta{U}-U)^T\Omega\Phi$

$\frac{\partial{(J_c+J)}}{\partial{\Delta{U}}} = 
-(R_s-Fx_a-\Phi\Delta{U})^TQ\Phi+\Delta{U}^T\bar{R} \\
-(L-Fx_a-\Phi\Delta{U})^T\Omega\Phi + (Fx_a+\Phi\Delta{U}-U)^T\Omega\Phi$

![KakaoTalk_20231114_190745967.jpg](Model%20Predictive%20Control(MPC)%203cbc523c33994289b3f872c4c46c67c3/KakaoTalk_20231114_190745967.jpg)

$\Delta{U}^* = (\Phi^TQ\Phi+2\Phi^T\Omega\Phi)^{-1}((R_s-Fx_a)^TQ\Phi+(L-Fx_a)^T\Omega\Phi +(U-Fx_a)^T\Omega\Phi)$

# 6. 적용

## 1. 사용한 모델

$\dot{x} = Ax + Bu\\
Y = Cx$ 

$x$: 상태변수

밑에서 사용한 모델

위치: $\begin{bmatrix}
x\\
y\\
z
\end{bmatrix} \in \R^3$ , 위치별 속도: $\begin{bmatrix}
u\\
v\\
w
\end{bmatrix} \in \R^3$ , 위치별 가속도 제어입력: $\begin{bmatrix}
u_1\\
u_2\\
u_3
\end{bmatrix} \in \R^3$

상태변수 $x = \begin{bmatrix}
x\\
y\\
z\\
u\\
v\\
w
\end{bmatrix}$

ODE system

$\dot{\begin{bmatrix}
x\\
y\\
z\\
u\\
v\\
w
\end{bmatrix}} = 
\begin{bmatrix}
x\\
y\\
z\\
u\\
v\\
w
\end{bmatrix} +
\begin{bmatrix}
0\\
0\\
0\\
u_1\\
u_2\\
u_3
\end{bmatrix}$

상태방정식 $\dot{x} = Ax+Bu$

$\dot{\begin{bmatrix}
x\\
y\\
z\\
u\\
v\\
w
\end{bmatrix}} = 
\begin{bmatrix}
0 & 0 & 0 & 1 & 0 & 0\\
0 & 0 & 0 & 0 & 1 & 0\\
0 & 0 & 0 & 0 & 0 & 1\\
0 & 0 & 0 & 0 & 0 & 0\\
0 & 0 & 0 & 0 & 0 & 0\\
0 & 0 & 0 & 0 & 0 & 0
\end{bmatrix}
\begin{bmatrix}
x\\
y\\
z\\
u\\
v\\
w
\end{bmatrix} + 
\begin{bmatrix}
0 & 0 & 0 \\
0 & 0 & 0 \\
0 & 0 & 0 \\
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1 
\end{bmatrix}
\begin{bmatrix}
u_1\\
u_2\\
u_3
\end{bmatrix}$

## 2. 결과

### $N_p$ 크기 변화에 따른 변화 관찰

![reference](Model%20Predictive%20Control(MPC)%203cbc523c33994289b3f872c4c46c67c3/Untitled%201.png)

reference

![Np = 10](Model%20Predictive%20Control(MPC)%203cbc523c33994289b3f872c4c46c67c3/Untitled%202.png)

Np = 10

![Np = 20](Model%20Predictive%20Control(MPC)%203cbc523c33994289b3f872c4c46c67c3/Untitled%203.png)

Np = 20

$N_p$크기를 늘리면 참고하는 미래의 상태를 더 많아지는 것이기 때문에 성능이 더 좋아 진 것을 확인할 수 있다.

### **가중치(Weight Matrix) 선택에 따른 결과 비교**

![R=50, Q=120](Model%20Predictive%20Control(MPC)%203cbc523c33994289b3f872c4c46c67c3/Untitled%204.png)

R=50, Q=120

![R=25, Q=120](Model%20Predictive%20Control(MPC)%203cbc523c33994289b3f872c4c46c67c3/Untitled%205.png)

R=25, Q=120

![R=25, Q=360](Model%20Predictive%20Control(MPC)%203cbc523c33994289b3f872c4c46c67c3/Untitled%206.png)

R=25, Q=360

R은 $\Delta{U}$에 대한 가중치로 R의 값이 높을 수록 $\Delta{U}$를 크게 바꾸겠다는 뜻이다. 그래서 왼쪽의 사진에서 보면 위치가 크게 바뀌는 것을 확인할 수 있다.

Q는 $Y-R_s$에 대한 가중치로 reference와 현재 상태의 사이를 줄이는 방향으로 control을 만들게 된다. 그래서 오차가 빨리 줄어드는 것을 확인할 수 있다.

### **State Variable에 대한 제약 조건 추가 및 결과 비교**

![제약 조건 없음](Model%20Predictive%20Control(MPC)%203cbc523c33994289b3f872c4c46c67c3/Untitled%207.png)

제약 조건 없음

![제약 조건 있음](Model%20Predictive%20Control(MPC)%203cbc523c33994289b3f872c4c46c67c3/Untitled%208.png)

제약 조건 있음

왼쪽은 제약 조건 없이 reference를 따라 가도록 한 것입니다. 초기 조건을 일부러 reference랑 차이를 두어서 얼마나 빨리 수렴하는지 관찰 했습니다.  

오른쪽은 옆에 있는 bound를 건드리지 않는 제약 조건을 추가한 것입니다.  
왼쪽과 차이점은 bound가 있어서 진동이 빨리 잡히는 것을 확인할 수 있습니다.

# 7. 후속 연구

1. 드론의 동역학을 기반으로 한 MPC 적용
2. N개의 드론을 동시에 control하면서 충돌을 방지하는 것
3. 실시간 데이터를 바탕으로 장애물 회피 등을 구현하는 것

# 8. 잡답

1. 원래는 드론에 MPC를 적용하려고 했는데 nonlinearlity가 너무 강해서 실패했다. 드론에서는 body_frame 기준으로 z축에서만 힘을 받고 회전을 통해 x, y축으로 이동하는데 초기 rotation matrix가 diag[111]이라서 회전에 대한 update가 이루어지지 않았다. (z축 은 잘 맞았다.) 
결론은 linear한 모델은 안되고 nonlinear한 방법론을 추후에 적용해서 해보겠다.
2. 지금은 refernce를 만들고 한 축으로만 bound를 주었는데 일반적으로 bound를 원통으로 주는게 합리적으로 보여서 나중에 구현해 보겠다.
3. 드론 여러 개를 update하는 방법이 같은 행렬에 넣어서 동시에 돌리면 되는 걸로 아는데 일단 실패했다. 이것도 추후에 해볼 예정
4. R이 $\Delta{U}$에 관한 가중치인데 이것에 매우 민감하게 반응한다. 하이퍼파라미터 $dt, N_p, \bar{R}, Q$를 이상하게 주면 폭발하게 된다. 즉 발산하다는 것입니다. 안전성에 문제가 있는데 안전성에 대한 조건을 추후에 공부해 볼 예정입니다.
