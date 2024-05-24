<aside>
💡 데이터 전처리 및 자세한 분석 내용을 보시려면 블로그를 참고해 주세요.

</aside>

['E-commerce Data 분석' 카테고리의 글 목록](https://datapractice0815.tistory.com/category/E-commerce%20Data%20%EB%B6%84%EC%84%9D)

# 1. 데이터 분석을 통한 마케팅 전략 수립

## 성별 및 나이대별 이벤트 특징

![image](https://github.com/bongkyunSON/SideProject/assets/114561525/9e9c3c23-f44b-460d-a560-b300391f6ccf)


- 가장 많은 양의 결제가 일어난 구간: 여성 25~35세(합계=2334298)
- 결제 완료의 45% 이상 해당 구간에서 발생합니다.
- 전환율 높은 그룹(결제 완료 10000회 이상)
    - 남성 25~35
    - 남성 36~44
    - 여성 25~35세

## 시간 및 요일별 이벤트 특징

- 시간별 구매

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9e55a6a3-4ef6-4949-9539-2b8e91461c5c/74b23f70-be2b-4105-bd1c-d401dee9bee5/Untitled.png)

- 요일별 구매

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9e55a6a3-4ef6-4949-9539-2b8e91461c5c/c9a0cbda-492e-4f6a-b441-ed82364d16b6/Untitled.png)

- 구매 빈도가 높은 요일: 일요일 ~ 화요일
- 구매 빈도가 높은 시간: 10시~12시, 20시~24시

## 데이터 분석을 통한 매출증대 아이디어

<aside>
💡 고객 획득 비용(Customer Acquision Cost) = CAC

</aside>

- 전환율이 가장 높은 **남성 25~44세** 그룹에 마케팅 예산을 집중하는 것은 좋은 전략으로 보입니다.
- 주로 **10시~12시나 20시~24시**에 구매량이 대폭 늘어나므로, 광고 예산을 이 시간대에 집중적으로 집행하거나 전환을 유도하는 모바일 알림을 보내는 것은 시도해 볼 만합니다.
- **일요일부터 화요일**까지 구매가 많이 이루어지는 현상이 발견되었습니다. 이 시기에 광고 예산을 크게 집행하거나 구매를 유도하는 모바일 알림을 보내는 것은 좋은 아이디어가 될 것입니다.
- 카테고리별 전환율이 높은 **브랜드들과 협력하여 프로모션 전략**을 세운다면 **좋은 효과**를 기대할 수 있을 것입니다.

# 2. LTV(Life Time Value)분석

<aside>
💡 “고객 라이프 사이클 기간 동안 기업에 얼마만큼의 이익을 가져왔는가” 를 정량적으로 합한 것입니다.

</aside>

구매 기록 데이터를 분석 **RFMT** 지표를 계산하고 이를 바탕으로 **BG/NBD** (Beta-Geometric/Negative Binomial Distribution) 모형과 **Gamma-Gamma** 모형을 적합하여 고객 생애 가치를 (LTV, Lifetime Value) 산출합니다.

### **RFMT**

| Recency | R | 고객별 첫 구매~마지막 구매까지의 시간 |
| --- | --- | --- |
| Frequency | F | 고객별 구매 일수  |
| Monetary Value | M | 고객별 평균 구매 금액 |
| T | T | 고객별 첫 구매~집 계일까지의 시간 |

### L2 penalty

LTV 분석을 할 때 L2 penalty(규제, regularization)를 사용하는 이유는 모델의 과적합(overfitting)을 방지하고, 더 일반화된 모델을 만들기 위해서입니다. 

- 과적합 방지
- 모델의 안정성 증가

## BG/NBD

**L2 penalty**: 0.0013637185037294174

**MSE**: 0.57 → (구매 일수에 대한 평균 제곱 오차가 +- 0.5)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9e55a6a3-4ef6-4949-9539-2b8e91461c5c/ebd0e188-00a2-4e02-b1a3-af619e2a8af0/Untitled.png)

- 고객이 활동하는 동안 일정한 기간 T 동안의 구매 횟수는 푸아송 분포(Pois(λT))를 따릅니다. 예를 들어, 1일 동안의 구매 횟수가 푸아송 분포(Pois(1/12))를 따른다면, T가 1년인 경우에는 푸아송 분포(Pois(30))를 따르게 됩니다. 여기서 λT는 예상 구매 횟수를 의미합니다.
- 각 고객이 일정한 기간 동안 구매하는 횟수는 서로 다를 수 있으며, 이는 감마 분포(Gamma(r, α))를 따릅니다.
- 고객이 j번째 구매를 마지막으로 더 이상 구매하지 않을 확률(이탈률)은 p입니다. 이탈할 때까지의 총 구매 횟수는 기하 분포(Geo(p))를 따릅니다.
- 또한, 각 고객의 이탈률 p는 서로 다르며, 이는 베타 분포(Beta(a, b))를 따릅니다.
- 마지막으로, 각 고객의 일정 기간 동안의 구매 횟수와 이탈할 확률은 서로 독립적입니다.

## Gamma-Gamma

**L2 penalty:** 0.0004892556801810725

**MSE:** 9,513,426 → (평균 구매 금액의 평균 제곱 오차는 9,513,426원)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9e55a6a3-4ef6-4949-9539-2b8e91461c5c/3a3c0bc9-a1ed-49be-af70-df94e2a398d3/Untitled.png)

파란색 그래프: 실제값

주황색 그래프: 예측값

## LTV분석

이제 LTV를 구할 준비가 완료되었습니다. 지금까지의 과정을 요약하자면, 우리는 고객별 **RFMT**를 구했고, calibration/holdout 데이터를 나눠 **L2 penalt**y를 최적화했습니다. 이 최적화된 값을 **BG/NBD** 모형에 넣어 예상 구매 일수를, **Gamma-Gamma** 모형에 넣어 예상 구매 금액을 구했습니다. 이제 이 두 모형을 합쳐 LTV를 구하겠습니다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9e55a6a3-4ef6-4949-9539-2b8e91461c5c/b55f44fe-4cd5-49dc-9021-9a4a09b1b29b/Untitled.png)

- 1등 고객의 평균 구매 금액(monetary value)은 약 675,467원입니다. 한 번에 67만 원 정도를 쓰는 분이죠. 이 고객은 63일 동안 43번 구매했고, 어제도 구매했습니다.
- 단순히 과거 데이터를 기반으로 계산하면 다음 해에도 67만 원씩 43번 구매할 것이라 예상할 수 있습니다. 이 경우 **예상 총 구매 금액은 약 2,800만 원**이 됩니다.
- 그러나, BG/NBD 모형과 Gamma-Gamma 모형을 사용하여 계산한 **LTV는 66,855,590원으로, 약 4천만 원 정도 더 높게 예측되었습니다.**

## VIP, non-VIP 비교

- frequency의 평균과 표준편차를 이용하여 VIP와 non-VIP 를 나누었습니다.
- 나이별 분석, 시간별 분석, 요일별 분석, 카테고리별 분석, 브랜드별 분석, 체류시간 분석을 했습니다
- 특징을 찾은 분석
    - **요일별 분석:** 기존 분석에서는 일요일~화요일까지의 데이터가 중요했지만 VIP에서는 수요일에도 구매를 많이 하는 특징을 찾을 수 있었습니다.
    - **브랜드별 분석**: BIARRITZ**,** maatila**,** 락앤락 브랜드는 높은 고객 만족도와 재구매율을 유지하며, 꾸준한 품질 관리와 신뢰를 통해 고객을 지속적으로 유지하고 있습니다. 이에 비해 UNLIMIT**,** MARITHE ****FRANCOIS ****GIRBAUD**,** EMIS 브랜드는 주로 프로모션과 이벤트를 통해 고객의 관심을 끌며, 1회 성 구매가 주를 이루고 있습니다.

# 3. 추천시스템 모델 성능 평가

<aside>
💡 모델: Factorization Machines

</aside>

FM(Factorization Machines) 모델을 선택한 이유는 여러 분석을 통해 모델의 성능 변화를 관찰하고 싶었기 때문입니다. 분석을 진행하다 보면 데이터가 점점 Sparse(희소) 해질 가능성이 큽니다. 이럴 때, Sparse 한 데이터와 고차원 데이터에서 강점을 보이는 FM 모델이 적합하다고 판단했습니다.

## 데이터 분석 결과 적용

- 데이터 분석했던 결과들을 **Feature engineering** 하여 모델의 성능을 관찰합니다.

기본 모델 성능

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9e55a6a3-4ef6-4949-9539-2b8e91461c5c/2df7f3b3-b943-4c3b-ae72-03baabdfcc15/Untitled.png)

요일별 분석 결과 **Feature engineering**

- 마케팅 전략시 일요일~화요일, VIP분석시 수요일에 대한 특징도 발견
- 일요일~수요일 가중치 부여

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9e55a6a3-4ef6-4949-9539-2b8e91461c5c/05f2df7d-38cf-42bc-9aab-395fdb32a7af/Untitled.png)

시간별 분석 결과 **Feature engineering**

- 10~12시, 20~24시 가중치 부여

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9e55a6a3-4ef6-4949-9539-2b8e91461c5c/43ce9a8d-738e-4c2c-9e0d-6571f959c130/Untitled.png)

전환율이 높은 남자 나이대 결과 **Feature engineering**

- 남성 25~35 가중치 부여
- 남성 36~44 가중치 부여

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9e55a6a3-4ef6-4949-9539-2b8e91461c5c/afe98020-7824-4c61-a59c-11fc63b94034/Untitled.png)

LTV가 높은 브랜드 결과 **Feature engineering**

- LTV가 높은 브랜드 TOP10 가중치 부여

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/9e55a6a3-4ef6-4949-9539-2b8e91461c5c/323fb006-1023-44bf-a84a-4d985daed08c/Untitled.png)

# 프로젝트 결과

- 이번 프로젝트에서 분석 결과에 가중치를 부여하여 Feature Engineering을 수행한 결과, 모델의 성능이 향상되는 것을 확인할 수 있었습니다.
- 또한, LTV 분석 결과가 모델 성능을 저하시킬 것이라고 예상했지만, 실제로는 반대의 결과가 나와 매우 놀랐습니다. 이러한 예상을 뒤집는 결과는 저에게 좋은 경험이 되었습니다.
- 다만, 로컬 리소스 문제로 인해 충분한 에폭을 돌리지 못한 점은 아쉬웠고, 시계열 데이터가 짧아 더 디테일한 시계열 분석을 수행하지 못한 것도 아쉬움으로 남았습니다.
- 이번 프로젝트를 통해 분석 및 추천 모델링에 대해 한층 더 레벨 업할 수 있었던 소중한 경험이었습니다.
