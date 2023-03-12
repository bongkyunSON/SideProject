<<<<<<< HEAD
# Quant-Project
## Overview
### 1. 프로젝트 주제
- AI를 활용한 퀀트 팩터 포트폴리오 개발

### 2. 목표
- 기존 팩터 포트폴리오 전략에 Regression, Clustering 등 최신 ML/DL 모델을 접목하여 수익성과 안정성을 향상시킨다.

### 3. 팀 구성
1. 포트폴리오 전략팀
  - 손봉균
  - 최정택(Lead)
2. 포트폴리오 대시보드 개발팀
  - 박철준
  - 손해창
=======
### 프로젝트 소개

- **목적:**
    1. 데이터 분석을 통해 안전하고 일정한 투자 원칙 찾아보는 것을 목적으로 프로젝트 진행
    2. 시황 분석을 통해 S&P500 지수보다 좋은 성과 지표(샤프비율) 달성
    3. 팩터투자를 통해 개별 주식을 선별해 투자한 경우도 S&P500보다 좋은 성과 달성
    
- **진행 순서:**
    1. 프로젝트 구조 소개
    2. 시황 분석의 중요성
    3. 투자자산 다양화의 중요성
    4. 팩터투자의 중요성
    
- **담당 역할:**
    1. 전반적인 프로젝트 방향 및 전략 수립
    2. 다양한 경제지표 가운데 어떤 것을 활용할 것인지에 대한 의사 결정 담당
    3. 팩터와 자산에 오류가 없도록 선택하는 역할
    

### 활용한 기술&테크

- 팩터 알고리즘 (베타, 듀얼모멘텀, AI팩터 - Prophet)
- 종, 횡적 배분 알고리즘
- 평가지표 알고리즘(샤프비율, MDD 등)
    

---
## 1. 프로젝트 구조 소개

### 프로젝트 프레임워크

<img width="1194" alt="Framework" src="https://user-images.githubusercontent.com/114561525/220541414-90793fd0-6372-471b-824b-4c150247cb0a.png">

---

### 파일스트럭처

<img width="1023" alt="File_Structure" src="https://user-images.githubusercontent.com/114561525/220541425-4a4a0763-9ed7-42d3-819c-8e901d549bc1.png">

---

## 2. 시황 분석의 중요성

### 시황분석: 기준

```
<미국경제를 디플레이션, 인플레이션, 회복기, 확장기로 분류>

  <정의>
    OECD 미국 경기선행지수 100pt를 기준으로 고성장과 저성장으로 구분
    OECD 미국 경기선행지수를 바탕으로 성장 상승 및 하락 추세 반영
    Core PCE 물가지수 2.5%를 기준으로 고인플레이션과 저인플레이션으로 구분

  국면:
  1. deflation:
      저성장 / 성장 하락추세 / 저인플레이션

  2. inflation:
      저성장 / 성장 하락추세 / 고인플레이션

  3. recovery:
      저성장 / 성장 상승추세 / 저인플레이션
      저성장/ 성장 상승추세 / 고인플레이션

  4. expansion:
      고성장 / 성장 상승추세 / 고인플레이션
      고성장 / 성장 하락추세 / 고인플레이션

```

---

### 시황분석: SP500 지수 분석

<img width="403" alt="시황분석" src="https://user-images.githubusercontent.com/114561525/220541444-2e91a3cf-d423-4924-916b-f6d49648e27c.png">

- 시황 적용의 필요성 인지
- recovery인 경우 가장 성과지표가 좋았다.
- 각 국면 별 투자성과가 좋은 자산군을 찾아보자.

---

## 3. 투자자산 다양화의 중요성

### 멀티에셋: 선정자산

- 주식: sp500 index
- 장기채권: TLT ETF
- 원자재: GSG ETF(tracking S&P GSCI index)
- 달러: UUP index
- 부동산: VNQ ETF

---

### 멀티에셋: Deflation 분석

<img width="374" alt="멀티에셋" src="https://user-images.githubusercontent.com/114561525/220541475-4516b351-fd86-4d8b-811d-6f34253e4d21.png">


---

- deflation: 저성장/성장 하락 추세/저인플레이션
- 성장과 인플레이션이 모두 낮게 유지되는 국면
- 위축 국면이 길어질수록 급격한 수요 감소와 디레버리징이 나타나게 되고 경기침체가 발생
- 모든 위험 자산 가격이 크게 하락
- 안전사잔인 장기채권과 달러가 가장 좋은 성과를 보임

---

### 멀티에셋: Inflation 분석

<img width="371" alt="assets_infla" src="https://user-images.githubusercontent.com/114561525/220541575-865229f9-7d66-474e-95aa-0a23b297c68b.png">

![assets_infla_plot](https://user-images.githubusercontent.com/114561525/220542380-e7fa26ad-3b89-4289-832c-50c9849b5692.png)

---

- inflation: 저성장/성장 하락 추세/고인플레이션
- 높은 인플레이션 유지되고 있지만 성장이 둔화 또는 침체되는 국면
- 높은 인플레이션으로 인해 원자재 가격이 일시적으로 크게 상승할 수 있지만 경기 둔화로 인한 수요 감소 폭이 커질 경우 급격한 조정을 받는다.
- 달러 인덱스만 수익 발생 -> 회복기에서 상승, 후퇴기에 하락, 위축기에 상승. 하지만 달러는 안전자산의 역할도 하기 때문에 경제 위축기에 상승하는 모습을 보이기도 한다.

---

### 멀티에셋: Recovery 분석

<img width="373" alt="assets_recov" src="https://user-images.githubusercontent.com/114561525/220541795-987d152c-9ace-4f8c-af25-a958f6f932f4.png">

![assets_recov_plot_1](https://user-images.githubusercontent.com/114561525/220541905-c35ba7d4-90ed-43f5-b59a-89c8938eb11a.png)

---

- recovery: 저성장/성장 상승 추세/저인플레이션, 저성장/성장 상승 추세/고인플레이션
- 경제가 회복하는 국면
- 주식과 부동산 등 위험자산이 가장 좋은 성과를 보임
- 경제와 인플레이션이 점차 회복되어 채권금리가 상승 -> 장기채권은 불리
- 저성장 -> 원자재 역시 횡보하는 경향을 보임
- 예외: 코로나 이후의 급등

---

### 멀티에셋: Expansion 분석

<img width="379" alt="assets_expan" src="https://user-images.githubusercontent.com/114561525/220541999-21c116cb-011a-49ba-99da-8e07bf0466b7.png">

![assets_expan_plot](https://user-images.githubusercontent.com/114561525/220542018-e382d27d-9a9a-4b1b-a56d-9b1667b77044.png)

---

- expansion: 고성장/성장 상승 추세/고인플레이션, 고성장/성장 하락 추세/고인플레이션 - 경기가 피크를 찍는 국면 - 연준은 과열을 막기 위해 긴축정책을 시행 - 결국 경기는 점차 둔화되기 시작 - 고성장으로 주식도 좋은 성과를 보임 - 고성장 고인플레인션 국면 -> 원자재 주식 다음으로 좋은 성과

---

### <종합>

|  | 회복국면 | 과열국면 | 인플레이션 | 위축국면 |
| --- | --- | --- | --- | --- |
| buy | 주식, 부동산 | 주식, 원자재 | 달러 | 장기채권 |
| sell | 장기채권 | 장기채권 | 주식 | 주식, 부동산, 원자재 |

---

## 4. 팩터투자의 중요성

### 투자성과 비교

<img width="119" alt="regime_port" src="https://user-images.githubusercontent.com/114561525/220542102-4963878e-f08e-417a-aab4-216db9b03881.png">

<img width="619" alt="regime_port_plot" src="https://user-images.githubusercontent.com/114561525/220542117-09a4bca0-4601-4380-b460-93c1c4c5529a.png">

- 시황별 성과가 좋은 대체자산에 투자한 경우 SP500 지수를 바이앤홀드한 경우의 샤프비율인 0.38 대비 1.31로 약 4배 가까이 높았다.
- 다음 스탭: 시황별 어떤 자산군에 투자할지 대략적 감을 잡았다면, 개별 주식을 선택함으로써 SP500 지수를 이길 수 있는 방법론으로 팩터투자를 실시했다.

---

### 목표 설정

- 목표 설정전략유형:
    - 인헨스드 베타: SP500 지수 대비 성과지표의 개선된 포트폴리오 개발
    - 자산군: SP500을 구성하는 미국주식

---

### 팩터 선택

- 팩터: 개별 주식을 선별하는 방법론
- 팩터 스타일
    - 듀얼모멘텀: 절대모멘텀과 상대모멘텀이 동시에 존재하는 자산을 선택
    - 베타: 딘순회귀의 기울기값이 큰 자산을 선택
    - 저변동성: 낮은 변동성을 선택
    - AI forecasting: Prophet의 예측에 따라 자산을 선택
        
        ![me](https://user-images.githubusercontent.com/114561525/220542194-a2b294a9-ef61-4e4d-9edb-f82079721848.png)
        
    
---
### 팩터 투자 성과

![베타_보수적](https://user-images.githubusercontent.com/114561525/224532651-7affd5c5-5836-4624-a517-95d784b8bc1c.PNG)

### **Beta Conservative**

- 포트폴리오
    - Total Return : 410.24%
    - CAGR : 13.69
    - MDD : -0.19
    - Sharpe Ratio : 0.65
- S&P 500
    - Total Return : 359.36%
    - CAGR : 12.33%
    - MDD : -0.24
    - Sharpe Ratio : 0.58

![베타 중간](https://user-images.githubusercontent.com/114561525/224532700-2cd572ed-f514-431a-bcff-9b3a57c268ea.PNG)

### Beta Moderate

- 포트폴리오
    - Total Return : 592.29%
    - CAGR : 17.55%
    - MDD : - 0.26
    - Sharpe Ratio : 0.65
- S&P 500
    - Total Return : 359.36%
    - CAGR : 12.33%
    - MDD : -0.24
    - Sharpe Ratio : 0.58

![베타공격적](https://user-images.githubusercontent.com/114561525/224532703-2dacb08d-d472-4609-80c2-52a6ed9056c3.PNG)

### Beta Aggressive

- 포트폴리오
    - Total Return : 958.11%
    - CAGR : 22.81%
    - MDD : -0.36
    - Sharpe Ratio : 0.63
- S&P 500
    - Total Return : 359.36%
    - CAGR : 12.33%
    - MDD : -0.24
    - Sharpe Ratio : 0.58

---

![모멘텀_보수적](https://user-images.githubusercontent.com/114561525/224532708-bb774101-234c-416a-989e-050b4790b205.PNG)

### Momentum Conservative

- 포트폴리오
    - Total Return : 405.85%
    - CAGR : 13.58%
    - MDD : -0.14
    - Sharpe Ratio : 1.01
- S&P 500
    - Total Return : 359.36%
    - CAGR : 12.33%
    - MDD : -0.24
    - Sharpe Ratio : 0.58

![모멘텀_중간](https://user-images.githubusercontent.com/114561525/224532712-cbfddd6a-df5f-42e0-a3ce-b90ac24729fe.PNG)

### Momentum Moderate

- 포트폴리오
    - Total Return : 605.28%
    - CAGR : 17.78%
    - MDD : -0.19
    - Sharpe Ratio : 1.03
- S&P 500
    - Total Return : 359.36%
    - CAGR : 12.33%
    - MDD : -0.24
    - Sharpe Ratio : 0.58

![모멘텀_공격적](https://user-images.githubusercontent.com/114561525/224532713-7803ae8a-b21e-42cc-b464-d2699baf3870.PNG)

### Momentum Aggressive

- 포트폴리오
    - Total Return : 1067.88%
    - CAGR : 24.02%
    - MDD : -0.27
    - Sharpe Ratio : 1.05
- S&P 500
    - Total Return : 359.36%
    - CAGR : 12.33%
    - MDD : -0.24
    - Sharpe Ratio : 0.58

---

![변동성_보수적](https://user-images.githubusercontent.com/114561525/224532717-a66c2229-e8f1-4e1a-aaf2-0ec19bbd9cbc.PNG)
### Volatility Conservative

- 포트폴리오
    - Total Return : 226.39%
    - CAGR : 7.71%
    - MDD : -0.09
    - Sharpe Ratio : 0.61
- S&P 500
    - Total Return : 359.36%
    - CAGR : 12.33%
    - MDD : -0.24
    - Sharpe Ratio : 0.58

![변동성_중간](https://user-images.githubusercontent.com/114561525/224532721-95c943f3-13c6-4ccd-9bf2-308ae70c194d.PNG)
### Volatility Moderate

- 포트폴리오
    - Total Return : 272.18%
    - CAGR : 9.53%
    - MDD : -0.13
    - Sharpe Ratio : 0.67
- S&P 500
    - Total Return : 359.36%
    - CAGR : 12.33%
    - MDD : -0.24
    - Sharpe Ratio : 0.58

![변동성_공격적](https://user-images.githubusercontent.com/114561525/224532726-87fe0885-921e-40bc-bdea-6431cee23e99.PNG)

### Volatility Aggressive

- 포트폴리오
    - Total Return : 354.33%
    - CAGR : 12.19%
    - MDD : -0.19
    - Sharpe Ratio : 0.65
- S&P 500
    - Total Return : 359.36%
    - CAGR : 12.33%
    - MDD : -0.24
    - Sharpe Ratio : 0.58

---

![ai_보수적](https://user-images.githubusercontent.com/114561525/224532732-bc23575e-57b0-4edf-910b-962f527610ef.PNG)

### AI Forecasting Conservative

- 포트폴리오
    - Total Return : 354.33%
    - CAGR : 12.19%
    - MDD : -0.19
    - Sharpe Ratio : 0.65
- S&P 500
    - Total Return : 362.86%
    - CAGR : 12.43%
    - MDD : -0.22
    - Sharpe Ratio : 0.77

![ai_중간](https://user-images.githubusercontent.com/114561525/224532734-76b6a6a0-6b79-47dd-b1db-3ea2852ef2f3.PNG)

### AI Forecasting Moderate

- 포트폴리오
    - Total Return : 512.84%
    - CAGR : 16.02%
    - MDD : -0.30
    - Sharpe Ratio : 0.79
- S&P 500
    - Total Return : 362.86%
    - CAGR : 12.43%
    - MDD : -0.22
    - Sharpe Ratio : 0.77

![ai_공격적](https://user-images.githubusercontent.com/114561525/224532739-ecf0ff5a-4bf8-4296-9cf8-63d46433826f.PNG)
### AI Forecasting Moderate

- 포트폴리오
    - Total Return : 825.59%
    - CAGR : 21.16%
    - MDD : -0.42
    - Sharpe Ratio : 0.79
- S&P 500
    - Total Return : 362.86%
    - CAGR : 12.43%
    - MDD : -0.22
    - Sharpe Ratio : 0.77

---

### 프로젝트를 통해 배운 점

- 파이프라인을 만들어 대시보드를 웹페이지의 구축
- 웹페이지의 지연시간을 줄이기 위해 미리 결과물을 제이슨 파일로 만들어 파이프라인 구축
- AI모델로 팩터를 만들어봄
    - 리밸런싱 날짜를 기준으로 1년을 학습하고 다음 리밸런싱 날짜에 예상수익률을 뽑아 상위 20개의 종목을 찾아서 시그널을 보내줌

>>>>>>> d3d8697f862ab53daaea971ba68a89c8d088169a
