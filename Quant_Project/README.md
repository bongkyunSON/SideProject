# Presentation

# Team: Finding Alphas

| 팀명 | 팀원 |
| --- | --- |
| 퀀트 전략팀 | 손봉균, 최정택 |
| 퀀트 개발팀 | 박철준, 손해창 |

---

### 프로젝트 소개

- 목적:
    1. 데이터 분석을 통한 안전하고 일정한 투자원칙 찾아보는 것을 목적으로 프로젝트를 진행
    2. 시황분석을 통해 SP500 지수보다 좋은 성과지표(샤프비율) 달성
    3. 팩터투자를 통해서 개별주식을 선별해 투자한 경우도 SP500보다 좋은 성과를 달성

- 진행 순서
    1. 프로젝트 구조 소개
    2. 시황분석의 중요성
    3. 투자자산 다양화의 중요성
    4. 팩터투자의 중요성
    

---

### 프로젝트 프레임워크

![Framework.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2b0f9f88-18dc-44cd-98a5-d0e960e73184/Framework.png)

---

### 파일스트럭처

![File_Structure.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2fcc7b2f-65c1-4b71-b2d4-4ff6f8144043/File_Structure.png)

---

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

![sp_with_rg.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/98017b5b-c9a2-41e8-b6df-829feec13e59/sp_with_rg.png)

- 시황 적용의 필요성 인지
- recovery인 경우 가장 성과지표가 좋았다.
- 각 국면 별 투자성과가 좋은 자산군을 찾아보자.

---

### 멀티에셋: 선정자산

- 주식: sp500 index
- 장기채권: TLT ETF
- 원자재: GSG ETF(tracking S&P GSCI index)
- 달러: UUP index
- 부동산: VNQ ETF

---

### 멀티에셋: Deflation 분석

![assets_defla.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/62dee6bc-59a9-4e77-872a-9149ea36a3f4/assets_defla.png)

![assets_defla_plot.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1884205f-4932-4e3d-a26c-e78ce8f09aeb/assets_defla_plot.png)

---

- deflation: 저성장/성장 하락 추세/저인플레이션
- 성장과 인플레이션이 모두 낮게 유지되는 국면
- 위축 국면이 길어질수록 급격한 수요 감소와 디레버리징이 나타나게 되고 경기침체가 발생
- 모든 위험 자산 가격이 크게 하락
- 안전사잔인 장기채권과 달러가 가장 좋은 성과를 보임

---

### 멀티에셋: Inflation 분석

![assets_infla.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b5ff7011-3b4e-48ba-a9fb-a0701513ddb4/assets_infla.png)

![assets_infla_plot.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e1d46ed2-678b-4286-af27-2cc55eb61c1d/assets_infla_plot.png)

---

- inflation: 저성장/성장 하락 추세/고인플레이션
- 높은 인플레이션 유지되고 있지만 성장이 둔화 또는 침체되는 국면
- 높은 인플레이션으로 인해 원자재 가격이 일시적으로 크게 상승할 수 있지만 경기 둔화로 인한 수요 감소 폭이 커질 경우 급격한 조정을 받는다.
- 달러 인덱스만 수익 발생 -> 회복기에서 상승, 후퇴기에 하락, 위축기에 상승. 하지만 달러는 안전자산의 역할도 하기 때문에 경제 위축기에 상승하는 모습을 보이기도 한다.

---

### 멀티에셋: Recovery 분석

![assets_recov.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c67c74ca-c2b0-4957-b1bd-52c61aa11f01/assets_recov.png)

![assets_recov_plot.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0416c210-55f1-4652-9162-bbe4483f5332/assets_recov_plot.png)

---

- recovery: 저성장/성장 상승 추세/저인플레이션, 저성장/성장 상승 추세/고인플레이션
- 경제가 회복하는 국면
- 주식과 부동산 등 위험자산이 가장 좋은 성과를 보임
- 경제와 인플레이션이 점차 회복되어 채권금리가 상승 -> 장기채권은 불리
- 저성장 -> 원자재 역시 횡보하는 경향을 보임
- 예외: 코로나 이후의 급등

---

### 멀티에셋: Expansion 분석

![assets_expan.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2525a9b5-1e9d-4c3c-a072-fc22b51ab203/assets_expan.png)

![assets_expan_plot.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c9c17bc1-5aa0-4767-b831-1c2270716ab7/assets_expan_plot.png)

---

- expansion: 고성장/성장 상승 추세/고인플레이션, 고성장/성장 하락 추세/고인플레이션 - 경기가 피크를 찍는 국면 - 연준은 과열을 막기 위해 긴축정책을 시행 - 결국 경기는 점차 둔화되기 시작 - 고성장으로 주식도 좋은 성과를 보임 - 고성장 고인플레인션 국면 -> 원자재 주식 다음으로 좋은 성과

---

### <종합>

|  | 회복국면 | 과열국면 | 인플레이션 | 위축국면 |
| --- | --- | --- | --- | --- |
| buy | 주식, 부동산 | 주식, 원자재 | 달러 | 장기채권 |
| sell | 장기채권 | 장기채권 | 주식 | 주식, 부동산, 원자재 |

---

### 투자성과 비교

![regime_port.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/76c5e979-b9cc-457a-b05d-c1eb74e47ca0/regime_port.png)

![regime_port_plot.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3d9867e0-a0b1-462c-864b-a60e3ce939ad/regime_port_plot.png)

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
        
        ![me.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c48ac964-cc3f-4944-aa33-b039346a29c6/me.png)
        
    
- 팩터의 투자성과는 대시보드를 통해 설명
