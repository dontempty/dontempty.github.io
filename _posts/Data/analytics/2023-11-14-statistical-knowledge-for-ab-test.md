---
title: A/B 테스트를 위한 통계지식
author: yoongyoonge
date: 2023-11-14 21:00:00 +0900
categories: [Data, Analytics]
tags: [study, data analysis, data science, abtest, statistics, hypothesis testing]
render_with_liquid: false
---

> A/B테스트를 이해하기 위한 기초 통계 지식을 정리합니다. 기본적인 간단한 용어 설명이나 개념 설명정도의 포스트입니다.


## 표본크기(Sample Size)
- AB테스트는 통계적으로 의미있는 차이를 확인하는 테스트이기 때문에 충분한 표본 크기가 필요
- 표본 크기는 실험의 통계적 강도에 영향 
- Baseline Conversion Rate, Minimum Detectable Effect, Statistical Significance 를 기반으로 계산

- 표본이 얼마나 필요하냐라는 질문은 두 방안의 결과가 몇 % 정도 차이가 날 것이라고 기대하는가와 같음 
    - 표본이 많으면 많을수록 A안과 B안의 결과 차이가 작더라도 실험 결과가 통계적으로는 유의미할 수 있음
    - 표본이 작더라도 A안과 B안의 결과 차이가 크다면 마찬가지로 통계적으로 유의미 할 수 있음 

    - 예시 
        - 고객 100,000명 중 전환한 사람 1,000명 => 전환율 1%, 평균 객단가 100만원 => 매출액 10억원 차이 발생 
        - 고객 1,000명 중 전환한 사람 50명 => 전환율 5%, 평균 객단가 10,000원 => 매출액 50만원 차이 발생

참고자료: <br>
[A/B 테스트에 적정한 표본의 크기와 주의사항](https://brunch.co.kr/@539insight/143) <br>
[A/B 테스트 제대로 이해하기 시리즈](https://yozm.wishket.com/magazine/@platter/)<br>
[표본 크기 계산기](https://www.evanmiller.org/ab-testing/sample-size.html) <br>
[A/B 테스트 계산기 세팅 & 해석하기](https://brunch.co.kr/@539insight/140) <br>
[AB Testguide](https://abtestguide.com/calc/)


<br>

## 가설검정(Hypothesis Testing)

- 귀무 가설(Null Hypothesis, H0) 
    - 영가설
    - 효과가 없다는 가설 
    - 우연 때문이라는 가설
    - 대립 가설의 반대 가설
    - ~와 차이가 없다, ~의 효과는 없다, ~와 같다

- 대립 가설(Alternative Hypothesis, H1) 
    - 효과가 있다는 가설 
    - 귀무가설이 틀렸다는 것을 입증해서 A 그룹과 B그룹의 차이가 우연이 아니라는 사실을 보이기 위한 가설
    - 입증하여 주장하고자 하는 가설
    - ~와 차이가 있다, ~의 효과는 있다, ~와 다르다

- 귀무 가설과 대립 가설은 모든 상황을 커버해야하며 이 두 상황이 아닌 상황은 없어야 함
- 단, 두 개 이상의 실험이 필요할 경우 MAB(multi armed bandit) 등의 실험 설계가 필요 

- 1종 오류
    - 참인 귀무 가설을 기각하는 오류 
    - 우연에 의한 효과가 실제 효과라고 잘못 결론 내리는 것
    - 보수적인 의사결정을 하기 위해서는 유의 수준을 낮춰 1종 오류를 범할 확률을 낮춰야 함  
    - 귀무 가설이 참인 것을 전제로 함

- 2종 오류
    - 거짓인 귀무 가설을 기각하지 않는 오류 
    - 실제 효과를 우연에 의한 효과라고 잘못 결론 내리는 것
    - 세운 가설이 맞음에도 맞다고 결론 짓지 않는 오류 
    - 대립 가설이 참인 것을 전제로 함

- 일원검정 
    - 한 방향으로만 우연히 일어날 확률을 계산하는 가설검정 
    - 대립가설이 확실하게 증명되지 않는다면 귀무가설을 계속 고수하면 되기 때문에 이때는 방향성을 고려한 대립가설이 필요
    - B는 A보다 낫다의 상황인 경우 
    - 우연에 의한 극단적인 결과에 대해 한 방향만을 고려하여 p값을 계산한다는 의미

- 이원검정 
    - 양방향으로 우연히 일어날 확률을 계산하는 가설검정
    - 어느쪽으로도 속지 않기 위한 가설검정 
    - A는 B와 다르며 더 크거나 더 작을 수 있음 
    - 우연에 의한 극단적인 결과가 양쪽에서 나타날 p값을 계산한다는 의미 


![image](https://github.com/yoongyoonge/yoongyoonge.github.io/assets/20895661/61f19510-067f-4250-9935-e76c8c158a4a)

참고자료 <br>
[가설 검정이란?](https://wikidocs.net/196998) <br>
[P-value](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=bi1189&logNo=221513713731#)

<br>

## 통계 테스트 
- 실험 데이터를 사용하여 귀무 가설을 기각할지 또는 기각하지 않을지 결정하는 통계적 방법을 이해해야 함 
- 일반적으로 t-검정 또는 z-검정이 사용됨 

## 신뢰구간(Confidence Interval) 
- 결과를 해석할 때 신뢰 구간을 사용하여 효과의 범위를 파악할 수 있음 


## 유의수준(Significance Level)
- 실험에서 어느 정도의 통계적 신뢰 수준을 원하는지 결정하는 것
- 이를 정할 때 유의수준(알파)을 사용
- 귀무가설 모델에서 '비정상'이라고 판단할 임계값을 말함
- 일반적으로 0.05 (5%) 또는 0.01 (1%) 수준이 사용됨 
- 임의로 선택되며 적절한 값을 보장하는 방법은 없음
- 실제 결과가 통계적으로 의미 있는 것으로 간주되기 위해, 우연에 의한 기회 결과가 능가해야 하는 '비정상적인'가능성의 임계 확률
- 랜덤 모델의 적합도에 관해 역으로 추적하는 것
- 통계적 유의성을 평가할 때 1종 오류와 2종 오류가 발생할 수 있는데 보통 1종 오류를 최소화하도록 가설을 설계함

## 유의성 검정 
- 관심 있는 효과를 측정하기 위한 검정통계량을 지정하고 관찰된 효과가 정상적인 랜덤 변이의 범위 내에 있는지 판단하는 방법 
- 가장 자주 사용되는 검정 방법은 t-검정(t-test)


참고자료 <br>
책: 데이터 과학을 위한 통계 - CHAPTER 3 통계적 실험과 유의성 검정

<br>

## p-value 
- 주어진 데이터에서 특정 검정 통계량(t-값 또는 z-값)을 얻을 확률
- 귀무가설을 구체화한 모델이 있을 때 관측된 결과가 특이하거나 극단적인 결과를 얻을 확률을 말함
- 작은 p값은 귀무 가설이 거부될 가능성이 크다는 것을 의미
- p값이 0.05라는 것은 우연히 얻은 결과의 5%가 관찰한 것만큼 극단적이거나 그 이상 극단적인 결과를 얻을 것으로 기대함 
- 따라서 p값이 낮을수록 좋다는 의미로 쓰며 이는 결과가 우연에서 비롯될 확률이 낮은 것을 뜻함 
- 즉, 랜덤 모델이 주어졌을 때, 그 결과가 관찰된 결과보다 더 극단적인 확률을 뜻함
- 하지만, 이 값이 유의미 하다고 해서 바로 증거로 사용하면 안되며 의사결정을 좌우하는 도구로 생각하면 안됨
    - p값은 데이터가 특정 통계 모델과 얼마나 상반되는지 나타낼 수 있음 
    - p 값은 연구 가설이 사실일 확률이나, 데이터가 랜덤하게 생성되었을 확률을 측정하는 것이 아님
    - p값 또는 통계적 유의성은 효과의 크기나 결과의 중요성을 의미하지 않음 

- p-value 를 관찰하면서 실험 기간을 무한정 늘릴 필요가 없고, 실험을 너무 일찍 끝내서도 안됨 

#### p-value와 유의 수준의 관계 
- 유의 수준은 귀무 가설을 기각하는 기준을 설정하는 것 
- p-value는 실제로 데이터에서 얻은 결과가 우연이 아닌지를 나타내는 지표 
- A/B테스트에서는 일반적으로 p값이 유의 수준인 0.05보다 작으면 귀무가설을 기각
- 귀무가설 환경에서 실제 데이터가 그렇게 나타나기 어려운것을 나타내기 때문

![image](https://github.com/yoongyoonge/yoongyoonge.github.io/assets/20895661/b5c51450-42db-4777-b717-f70ff3a93424)

참고자료 <br>
[P-value](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=bi1189&logNo=221513713731#)

<br>

# 참고 지식 

## 과도한 검정(Multi Testing)
- 여러 가설을 동시에 테스트할 때 과도한 검정 문제에 주의해야 함 
- Bonferroni 보정과 같은 방법을 사용할 수 있음 

## MAB(Multi-Armed Bandit)
- 고객이 선택할 수 있는 손잡이가 여러 개인 가상의 슬롯머신을 말함 
- 각 손잡이는 각기 다른 수익을 가져다주며 다중 처리 실험에 대한 비유라고 생각하면 됨 
- 좀 더 빠른 의사 결정을 가능하게 하며 여러 테스트를 위해 이를 사용 
- 3가지 이상의 처리를 효율적으로 다루고 최고를 위한 최적의 선택을 하도록 도움 
- 실험 도중에 얻은 정보를 통합하고 수익이 낮은 것의 빈도를 줄이는 쪽으로 표본 추출 과정을 변경

## 분산분석(analysis of variance, ANOVA)
- 여러 그룹, A-B-C-D의 데이터를 비교해야할 때
- 여러 그룹 간의 통계적으로 유의미한 차이를 검정하는 통계적 절차
- 그룹 간 전체적인 편차가 우연히 발생할 수 있는 범위 내에 있는지를 평가하기 위해 사용 
- 그룹 처리, 상호작용 효과, 오차와 관련된 분산의 구성 요소들을 구분할 때 유용
- t-test가 표본 1개 또는 2개를 비교하는 것에 비해 2개 이상인 표본에 대해 통계적 유의성을 확인하는 분석

참고자료 <br>
[A/B 테스트에서 p-value에 휘둘리지 않기](https://datarian.io/blog/dont-be-overwhelmed-by-pvalue) <br> 
[가설 검정이란?](https://wikidocs.net/196998)

<br><br>

참고자료 <br>
[AB 테스트 알아보기 - 1. 샘플 사이즈 계산 (with alpha, beta, power, critical value)](https://jinwoo1990.github.io/statistics/ab-test-sample-size/)