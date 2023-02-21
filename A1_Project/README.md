# A1 PERFORMANCEFACTORY

# Team: A1 PERFORMANCEFACTORY

| 팀명 | A1 PERFORMANCEFACTORY                                      |
| ---- | ---------------------------------------------------------- |
| 팀원 | 손봉균, 손해창, 김정현, ~~임하은~~, ~~이희민~~, ~~김재승~~ |

---

### 프로젝트 소개

- 목적:
    
    log데이터와 bidding데이터를 분석하여 광고를 최적화 하기 위한 새로운 인사이트를 발견, 발전
    

- 진행 순서
    1. 프로젝트 소개
    2. log데이터
    3. bidding 데이터
    4. 질의응답
    

---

## **프로젝트 소개**

### 1. Data 소개

### ***bidding_result.csv***

| data columns   | 데이터 설명                                | 비고    |
| -------------- | ------------------------------------------ | ------- |
| Index          | 광고 고유 이름( All 숫자)                  | int64   |
| Impression     | 노출수 ***** 라벨링 값                     | int64   |
| Click          | 클릭수 ***** 라벨링 값                     | int64   |
| Cost           | 광고 집행 비용; bid * 클릭수(세금제외)     | float64 |
| Sum of AD rank | 전일 광고 평균 순위                        | float64 |
| Bid            | 입찰, 광고 단가                            | int64   |
| Price(PC)      | PC 가격 *** ; 판매중인 물건 가격           | int64   |
| Price(Mobile)  | 모바일 가격 ***; 판매중인 물건 가격        | int64   |
| Delivery Fee   | 배송비 (-1: 광고가져오지 못한것 즉 결측값) | int64   |
| Category1      | 범주1                                      | object  |
| Category2      | 범주2                                      | object  |
| Category3      | 범주3                                      | object  |
| Category4      | 범주4                                      | object  |
| date           | 날짜                                       | int64   |
|                |                                            |         |

### **log_csv.csv**

| data columns | 데이터 설명                                                                                                                   | 비고   |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------- | ------ |
| uid          | 고유의 값_유저 식별을 위한 인덱스 (동일 아이디일때 같은 유저라고 파악                                                         | object |
| action_type  | 해당 유저가 한 행동                                                                     -유저가 하는 행동중 추적 가능한 행동- |
1. View : 페이지 방문
2. Basket : 장바구니 버튼
3. Highlight : 드래그 이벤트 발생 
4. Copy:  복사 이벤트 발생
5. SINF : 광고 유입을 통한 입장 | object |
| category1 | 범주1 | object |
| category2 | 범주2 | object |
| category3 | 범주3 | object |
| collect_time | 수집 시간(단위 : ms) | object |
| geoip_city_name | 유저 ip기반으로 추측하는 대략적 위치 정보 | object |
| useragent_os | 유저가 사용하는 기기 정보 | object |
| viewrate | 유저가 페이지에서 확인한 내용(스크롤 단위 %)  | float64 |
| viewtime | 유저가 페이지에 머문 시간을 의미합니다 (단위 ms) | int64 |

---

### 2. 스토리 라인

1. EDA를 통한 인사이트 찾기
2. log데이터와, bidding데이터의 인사이트로 스토리라인 구성
3. 통계적 모델 및 AI모델 활용
4. 광고 최적화를 위한 스토리 라인 정리

---

## Log data 분석

### 1. **시간대별 log 분석**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c62ec24f-ad0a-457d-886c-1defffa4918a/Untitled.png)

전체로그데이터

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/96d278f0-d948-4a90-b4bc-fe96647fc780/Untitled.png)

Basket 로그데이터

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d3be79af-14ca-4be8-8324-533814223c18/Untitled.png)

non-basket 로그데이터

- 우리가 생각하는 근무시간대 데이터가 많은걸 볼수있다
- 만약 광고를 한다면 근무시간대 광고를 하는것이 좋겠다
- 근무시간과, 자는시간으로 나누어서 OS 비중을 살펴보자

---

### 2. 시간대별 OS비중


![통합데이터 시간대별 로그](https://user-images.githubusercontent.com/114561525/220329225-943829eb-9a9b-4a42-a14a-f447089f41ff.png)
전체 시간 os 비중

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/19ced73f-0a01-4e9b-9f6a-0e9f681653f0/Untitled.png)

근무시간대 os비중

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1fc18b14-1d55-4541-bca8-729027c40cd1/Untitled.png)

자는시간대 os비중

- 가설 :  근무시간대에는 데스크탑을 많이 쓰니 윈도우 & 맥 비중이 높을 것으로 예상됨
- 가설을 데이터로 확인
- 전체 데이터가 아닌 basket데이터와 non 바스켓 데이터로 나누어서 보자

---

### 3. 시간대별 Basket data

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/07d67625-ff4a-4aab-a51a-5307e42295c0/Untitled.png)

전체시간

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/989e41d8-6230-42ea-b29c-009701c0a28f/Untitled.png)

근무시간대

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c7f6dc92-9c4b-41cf-ae75-445c7e1b5446/Untitled.png)

자는시간대

- 자는 시간대에도 데스크탑 비중이 줄어들지 않는다
- 가설 : Basket 하는데 있어서 모바일 쪽 고객들에게 불편한 시스템이 아닐까?
- 모바일쪽 바스켓 버튼을 가지고 A/B test를 해보자

---

### Uid Basket data 이탈율 중간값

![바스켓데이터.PNG](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/763887b9-f613-4a02-b08e-c0eb093b52d3/%EB%B0%94%EC%8A%A4%EC%BC%93%EB%8D%B0%EC%9D%B4%ED%84%B0.png)

- 바스켓을 한 uid 별로 데이터를 본다
- 데스크탑은 18초, 모바일은 14초 이탈율이 나온다
- 상품 소개글 작성시 데스크탑은 18초, 모바일은 14초 가독 시간안에 중요한 정보들을 입력해야한다

---

### Uid non - Basket data 이탈율 중간값

![non바스켓.PNG](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ad1eecdf-d7f5-4bfd-95a8-b3b213326ab0/non%EB%B0%94%EC%8A%A4%EC%BC%93.png)

- non바스켓을 한 uid 별로 데이터를 본다
- 데스크탑은 17초, 모바일은 18초 이탈율이 나온다
- 데스크탑과 모바일간의 차이가 많이 나지 않는다
- 모바일 쪽을 보면 바스켓과 non 바스켓간의 차이가 4초가 난다
- 모바일 쪽 가독 평균 14초가 넘어가면 문제가 있다

---

### 카테고리별 바스켓 비율

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/91d36557-dcfe-4eb3-88d1-6eb904705195/Untitled.png)

- 특히 데코/식물, 가전/디지털 같은경우 모바일, 데스크탑의 비율차이가 많다
- 데코/식물 카테고리는 모바일쪽에 불친절하지 않는지 체크할 필요가있다
- 가전/디지털 같은 경우 데스크탑쪽에 불친절하지 않는지 체크할 필요가 있다

---

### Prophet으로 클릭데이터 보기(bidding data)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b223e21a-8672-4c9a-801c-b35a1f7bfa15/Untitled.png)

- 클릭 추세가 점점 줄어든다
- 일, 월, 화에 클릭수가 높다
- 클릭수가 높은 일, 월, 화에 광고를 하면 좀더 효율적이다

 

---

### 카이제곱검정

```jsx
# 카이제곱검정

# 모바일과 데스크탑중 바스켓의 비율
log_drop_null = log_drop_null[log_drop_null['useragent_os'].str.contains('Windows|Android|iOS|Mac OS X')]
# 윈도우, 안드로이드, ios, mac 만 남기고 나머지 drop

log_drop_null["useragent_os"] = np.where((log_drop_null['useragent_os'] == 'Android')|
                                         (log_drop_null["useragent_os"] == 'iOS') , "1","0" )

# 모바일 (안드로이드, ios)은 1, 나머지는 0

log_drop_null["viewrate"] = np.where((log_drop_null['viewrate'] != 0), "1","0" )

# 뷰레이트는 혹시나해서?

log_basket = log_drop_null[log_drop_null['useragent_os']=='0'][['viewrate']]
log_non_basket = log_drop_null[log_drop_null['useragent_os']=='1'][['viewrate']]
# log_basket = log_drop_null[log_drop_null['useragent_os']=='0']
# log_non_basket = log_drop_null[log_drop_null['useragent_os']=='1']

total_basket = pd.concat([pd.get_dummies(log_basket),
                          log_drop_null[log_drop_null['useragent_os']=='0']
                          [['action_type']]],axis=1)

total_non_basket = pd.concat([pd.get_dummies(log_non_basket),
                              log_drop_null[log_drop_null['useragent_os']=='1']
                              [['action_type']]],axis=1)

a_0 = total_basket[total_basket['action_type']=='Basket'].shape[0]
a_1 = total_basket[total_basket['action_type']!='Basket'].shape[0]
b_0 = total_non_basket[total_non_basket['action_type']=='Basket'].shape[0]
b_1 = total_non_basket[total_non_basket['action_type']!='Basket'].shape[0]

dataset_chi = [[a_0, a_1], [b_0, b_1]]
pval = stats.chi2_contingency(dataset_chi)[1]
print('p-value : ',pval)

#output
p-value : 0.0
```

---

## Bidding data 분석

### 상품 가격대별 평균 순위

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8293a479-89b0-487b-a6b4-55cf5863574b/Untitled.png)

- 가설 : 상품가격이 높으면 높을수록 코스트 민감도가 낮아질것이다
- 가설을 데이터로 확인
- 미미하지만 가설을 확인했다

---

### 순위별 데이터 분석

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9dd728a5-d5a0-4eaa-98fc-02d27a49510b/Untitled.png)

광고

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/021645bd-3ff5-4dce-b1c1-520f9331ca67/Untitled.png)

클릭

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3724422d-6346-444d-926f-2d610f812646/Untitled.png)

코스트

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/320885a6-11d7-422a-b760-a8c96544db7d/Untitled.png)

노출

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/32ad72c0-14be-4806-bb99-5b4d946484e4/Untitled.png)

상품가격

- 등수를 나눈 기준 : 네이버 쇼핑 홈페이지 기준 20개보기, 40개보기, 60개보기, 80개보기가 있음, 거기에다가 광고6개가 붙어서(상단3개, 중간에3개) 등수를 나눴음
- 상식적으로 생각할수있는 데이터들이고 특별한 인사이트를 찾지 못함

---

### Prophet model을 이용한 분석

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/64095a26-1e60-4f2d-93b8-39b9716fb92b/Untitled.png)

클릭

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/f7c3bb63-1c49-4999-aceb-ce573c07ed63/Untitled.png)

코스트

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/33075358-c93c-4eb9-a965-15ea042a42d1/Untitled.png)

노출

- 시계열 데이터(3달)가 많이 없어서 시계열 분석은 어려웠다
- 요일별로 특징은 살표볼수있었다
- 전체적으로 추세가 점점 줄어드는걸 볼수있다
- 월별로 데이터를 분석해 볼 필요가 있다

---

### 카테고리 월별 분석

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ebdbcb4e-13c2-4ceb-a5b0-c6be212382a5/Untitled.png)

클릭

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/343cc154-32a1-4739-960f-66e1e9a89e79/Untitled.png)

코스트

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1b3bbcb3-356e-410e-bbd4-d22bb1741ddd/Untitled.png)

노출

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/55f2eed0-0977-44c6-ba1f-fbeca5b96f10/Untitled.png)

순위

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5afd96b3-be63-4120-8775-781ef2d0df7a/Untitled.png)

광고효율(클릭/코스트)

- 패션의류 같은 경우 12월에 코스트를 많이써서 클릭율을 많이 늘렸지만 효율적이지 않았다
- 디지털/가전 같은경우 10월에 12월보다 10배 코스트를 썼지만 효율적으로는 큰 차이가 나지 않았다
- 코스트를 많이 쓰면 데이터가 좋아지는건 당연하지만 그것을 효율적으로 썼다고는 볼수없다

---

## 결론

### Log data

- 모바일 바스켓 버튼 A/B test 진행
- 광고 최적화시 클릭률이 높은 요일 선택
- 카테고리별 바스켓 비중이 높은 OS를 선택하고 자는 시간과 근무시간을 선택해서 광고한다
    - e.g. 가전/디지털 카테고리 상품 → 자는시간대 → 모바일 → 일, 월, 화 요일중 선택 광고
- 카테고리별 OS 불편사항 개선
- 모바일에서 가독 평균시 14 ~18초 사이 문제 해결

### Bidding data

- 미미 하지만 상품가격이 높으면 코스트 효율이 줄어듬
- 코스트를 효율적으로 쓰기위해 매주, 매달 모니터링 필요
