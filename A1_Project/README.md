## 프로젝트 소개

- 목적 : log데이터와 bidding데이터 분석을 통해, 광고 최적화를 위한 인사이트 발견 및 솔루션 제안
- 진행 순서:
    1. 프로젝트 구조 소개
    2. log데이터 분석
    3. bidding 데이터 분석
    4. 솔루션 제안
- 담당 역할:
    1. log데이터 분석
    2. Bidding 분석
    3. 솔루션 제안

---

## 1. 프로젝트 구조 소개

### 1) Data 소개

### ***bidding_result.csv***

| data columns | 데이터 설명 | 비고 |
| --- | --- | --- |
| 데이터 열 | 데이터 설명 | 비고 |
| uid | 고유의 값_유저 식별을 위한 인덱스 (동일 아이디일때 같은 유저라고 파악 | object |
| action_type | 해당 유저가 한 행동                                                                     -유저가 하는 행동중 추적 가능한 행동- |  |
| Cost | 광고 집행 비용; bid * 클릭수(세금제외) | float64 |
| Sum of AD rank | 전일 광고 평균 순위 | float64 |
| Bid | 입찰, 광고 단가 | int64 |
| Price(PC) | PC 가격 *** ; 판매중인 물건 가격 | int64 |
| Price(Mobile) | 모바일 가격 ***; 판매중인 물건 가격 | int64 |
| Delivery Fee | 배송비 (-1: 광고가져오지 못한것 즉 결측값) | int64 |
| Category1 | 범주1 | object |
| Category2 | 범주2 | object |
| Category3 | 범주3 | object |
| Category4 | 범주4 | object |
| date | 날짜 | int64 |

### **log_csv.csv**

| uid | 고유의 값_유저 식별을 위한 인덱스  | object |
| --- | --- | --- |
| action_type | 1. View : 페이지 방문
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

## 2. Log data 분석

### 1) **시간대별 log 분석**

1) 전체로그데이터

![통합데이터 시간대별 로그](https://user-images.githubusercontent.com/114561525/220329463-a19db4ad-177f-4864-a4bd-60380628c8f3.png)

2) Basket 로그데이터

![바스켓 시간](https://user-images.githubusercontent.com/114561525/220329515-66f0f389-efe9-48ae-b2af-5bae7f8b7500.png)


3) non-basket 로그데이터

![노바스켓시간](https://user-images.githubusercontent.com/114561525/220329549-72991aa1-68e9-4b2a-ac3a-2becd564256e.png)


- 우리가 생각하는 근무시간대 데이터가 많은걸 볼수있다
- 만약 광고를 한다면 근무시간대 광고를 하는것이 좋겠다
- 근무시간과, 자는시간으로 나누어서 OS 비중을 살펴보자

---

### 2) 시간대별 OS비중

1) 전체 시간 os 비중


![os_1](https://user-images.githubusercontent.com/114561525/220329604-f5f40ec8-8275-4eb7-b8ee-74a8e5ca9a2c.png)
2) 근무시간대 os비중

![os_2](https://user-images.githubusercontent.com/114561525/220329657-95630f2e-0468-46f5-a819-73882f36961e.png)

3) 자는시간대 os비중

![os_3](https://user-images.githubusercontent.com/114561525/220329672-e344b4a5-fe34-4852-b62b-9eb7c8935f62.png)


- 가설 :  근무시간대에는 데스크탑을 많이 쓰니 윈도우 & 맥 비중이 높을 것으로 예상됨
- 가설을 데이터로 확인
- 전체 데이터가 아닌 basket데이터와 non 바스켓 데이터로 나누어서 보자

---

### 3) 시간대별 Basket data

1) 전체시간

![basket_os1](https://user-images.githubusercontent.com/114561525/220329739-6f7f5c7f-f3bb-4b23-8785-1bbb23a89e07.png)

2) 근무시간대

![basket_os2](https://user-images.githubusercontent.com/114561525/220329751-0ba06315-fcf4-4209-bd80-961c71b48e18.png)

3) 자는시간대

![basket_os3](https://user-images.githubusercontent.com/114561525/220329759-781a2175-5a29-45f3-ae4c-c1cf49007516.png)


- 자는 시간대에도 데스크탑 비중이 줄어들지 않는다
- 가설 : Basket 하는데 있어서 모바일 쪽 고객들에게 불편한 시스템이 아닐까?
- 모바일쪽 바스켓 버튼을 가지고 A/B test를 해보자

---

### 4) Uid Basket data 이탈율 중간값

![바스켓데이터](https://user-images.githubusercontent.com/114561525/220329839-b4ca2423-42d1-4991-b22e-d1cfe4d312a6.PNG)

- 바스켓을 한 uid 별로 데이터를 본다
- 데스크탑은 18초, 모바일은 14초 이탈율이 나온다
- 상품 소개글 작성시 데스크탑은 18초, 모바일은 14초 가독 시간안에 중요한 정보들을 입력해야한다

---

### 5) Uid non - Basket data 이탈율 중간값

![non바스켓](https://user-images.githubusercontent.com/114561525/220329899-8c92727e-9a47-4c68-99b1-a6d7537823bb.PNG)

- non바스켓을 한 uid 별로 데이터를 본다
- 데스크탑은 17초, 모바일은 18초 이탈율이 나온다
- 데스크탑과 모바일간의 차이가 많이 나지 않는다
- 모바일 쪽을 보면 바스켓과 non 바스켓간의 차이가 4초가 난다
- 모바일 쪽 가독 평균 14초가 넘어가면 문제가 있다

---

### 6) 카테고리별 바스켓 비율

![카테고리별 바스켓 비율](https://user-images.githubusercontent.com/114561525/220330051-e2f2f2c9-6f7c-4773-93f1-0a5b64dff262.PNG)

- 특히 데코/식물, 가전/디지털 같은경우 모바일, 데스크탑의 비율차이가 많다
- 데코/식물 카테고리는 모바일쪽에 불친절하지 않는지 체크할 필요가있다
- 가전/디지털 같은 경우 데스크탑쪽에 불친절하지 않는지 체크할 필요가 있다

---

### 7) Prophet으로 클릭데이터 보기(bidding data)

![클릭프로펫](https://user-images.githubusercontent.com/114561525/220330381-98b048ae-fcf0-4bd9-93a6-c61dc7d4a518.png)

- 클릭 추세가 점점 줄어든다
- 일, 월, 화에 클릭수가 높다
- 클릭수가 높은 일, 월, 화에 광고를 하면 좀더 효율적이다

 

---

### 8) 카이제곱검정

```jsx
# 카이제곱검정

# 모바일과 데스크탑중 바스켓의 비율
log_drop_null = log_drop_null[log_drop_null['useragent_os'].str.contains('Windows|Android|iOS|Mac OS X')]
# 윈도우, 안드로이드, ios, mac 만 남기고 나머지 drop

log_drop_null["useragent_os"] = np.where((log_drop_null['useragent_os'] == 'Android')|
                                         (log_drop_null["useragent_os"] == 'iOS') , "1","0" )

# 모바일 (안드로이드, ios)은 1, 나머지는 0

log_drop_null["viewrate"] = np.where((log_drop_null['viewrate'] != 0), "1","0" )

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

## 3. Bidding data 분석

### 1) 상품 가격대별 평균 순위

![상품가격대별 평균순위](https://user-images.githubusercontent.com/114561525/220330166-ae5ef5d7-96d7-48c3-8f11-f55a9f7ff139.PNG)

- 가설 : 상품가격이 높으면 높을수록 코스트 민감도가 낮아질것이다
- 가설을 데이터로 확인
- 미미하지만 가설을 확인했다

---

### 2) 순위별 데이터 분석

1) 광고

![순위별광고](https://user-images.githubusercontent.com/114561525/220330205-3bf006ca-0ca3-4d11-9420-af42d75ca133.PNG)

2) 클릭

![순위별클릭](https://user-images.githubusercontent.com/114561525/220330224-efee75a2-d90c-42ea-aee7-44ece5fca070.PNG)

3) 코스트

![순위별코스트](https://user-images.githubusercontent.com/114561525/220330251-9a5d5b5b-943e-45df-8d29-091d37dbd6ed.PNG)

4) 노출

![순위별노출](https://user-images.githubusercontent.com/114561525/220330276-d0cb07e9-8d75-4ae5-9198-4613d8868542.PNG)

5) 상품가격

![순위별상품가격](https://user-images.githubusercontent.com/114561525/220330304-fbd7ff20-f3f3-435d-8cbf-9c8f56cb77af.PNG)


- 등수를 나눈 기준 : 네이버 쇼핑 홈페이지 기준 20개보기, 40개보기, 60개보기, 80개보기가 있음, 거기에다가 광고6개가 붙어서(상단3개, 중간에3개) 등수를 나눴음
- 상식적으로 생각할수있는 데이터들이고 특별한 인사이트를 찾지 못함

---

### 3) Prophet model을 이용한 분석

1) 클릭

![클릭프로펫](https://user-images.githubusercontent.com/114561525/220330752-c4d6b335-db6b-4995-b14c-9efe899c2c3a.png)

2) 코스트

![코스트프로펫](https://user-images.githubusercontent.com/114561525/220330366-a5fab13e-66ae-433d-ac72-08228d20696f.png)

3) 노출

![노출프로펫](https://user-images.githubusercontent.com/114561525/220330340-9b26728a-ff54-41a1-8023-311a4a5935e8.png)


- 시계열 데이터(3달)가 많이 없어서 시계열 분석은 어려웠다
- 요일별로 특징은 살표볼수있었다
- 전체적으로 추세가 점점 줄어드는걸 볼수있다
- 월별로 데이터를 분석해 볼 필요가 있다

---

### 4) 카테고리 월별 분석

1) 클릭

![카테고리클릭](https://user-images.githubusercontent.com/114561525/220330419-3b196328-44e6-411e-b7f8-2ab706e253f2.PNG)

2) 코스트

![카테고리코스트](https://user-images.githubusercontent.com/114561525/220330438-9e4ef70d-8db3-4ab1-aad8-6e7d5da9ec05.PNG)

3) 노출

![카테고리노출](https://user-images.githubusercontent.com/114561525/220330457-e2aa37cf-d932-49d0-a8ab-bae1a33975a3.PNG)

4) 순위

![카테고리순위](https://user-images.githubusercontent.com/114561525/220330484-edaa8472-0e29-4059-a404-2280859e5c5b.PNG)

5) 광고효율(클릭/코스트)

![카테고리효율](https://user-images.githubusercontent.com/114561525/220330498-6daa512d-268c-4c49-8756-eba100f9e041.PNG)


- 패션의류 같은 경우 12월에 코스트를 많이써서 클릭율을 많이 늘렸지만 효율적이지 않았다
- 디지털/가전 같은경우 10월에 12월보다 10배 코스트를 썼지만 효율적으로는 큰 차이가 나지 않았다
- 코스트를 많이 쓰면 데이터가 좋아지는건 당연하지만 그것을 효율적으로 썼다고는 볼수없다

---

## [결론] 솔루션 제안

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
