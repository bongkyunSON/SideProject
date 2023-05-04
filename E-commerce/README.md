# [개인 프로젝트] 추천시스템 프로젝트

## 프로젝트 소개

- **목적:**
  1. E-commerce 데이터를 활용하여 추천시스템을 만들어보자
  2. 각종 추천 알고리즘을 사용하여 장, 단점을 살펴보고 RMSE값을 구해보자
- **진행 순서:**

  1. EDA
  2. TF-IDF
  3. Best-seller
  4. CF(knn, knn-plus)
  5. Item-based-CF
  6. FM & DeeFM
  7. LGBMRanker

- 총정리
  - 리소스 문제로 인해 16,000개 정도의 row로 만들수밖에 없었던게 너무 아쉽다
  - 추천시스템 성능을 올리기 위해 데이터를 분석하여 새로운 feature들을 만들었다
  - 더 많은 데이터와 분석에 의한 feature들을 추가한다면 성능을 더 올릴수 있을것이다

---

---

## EDA

- EDA
  1. 유저 아이디 아웃라이어

     ![유저아이디 아웃라이어](https://user-images.githubusercontent.com/114561525/236294657-7e3a94e5-d757-4519-9a14-ef0f3d993c09.png)

     - 유저 아이디별로 데이터를 1000개 밑으로 가지고있다
     - 아웃라이어 제거하여 피쳐를 만들어보자

  1. 나이대별 데이터 개수

     - 나이대 데이터 갯수
       - 30 2267563
       - 20 1992880
       - 40 997590
       - 50 208039
       - 60 35553
       - 10 31632
       - 70 8225
       - 80 963
       - 90 216
       - 0 26
     - 20~30대 데이터가 압도적으로 많다
     - 30대 이후의 데이터는 합치는 경우가 많은것같다
     - 나이대별로 나뉘는 피쳐를 만들어보자

  1. 성별 차이

     - F : 4342347, M : 834053
     - 여자 데이터가 압도적으로 많다

  1. 시간대별 데이터

     ![시간대](https://user-images.githubusercontent.com/114561525/236294957-d8bdcf47-995d-483b-a024-9b0bb9196a70.png)

     - 모바일데이터의 특징처럼 자는시간대 데이터가 많다
     - 2 ~ 6시 데이터, 7 ~ 18시 데이터, 18 ~ 1시 데이터를 나눠서 피쳐를 만들자
     - 시간대별로 구매 카테고리가 다른다. 즉 시간대별로 고객이 다르다
     - 시간대별로 특징을 나뉘어 피쳐를 만들어보자

  1. event_name 요일별 데이터

     - 클릭

     ![클릭별1](https://user-images.githubusercontent.com/114561525/236294825-c3b7741c-8cd1-4af8-92d9-5adfe8a5c300.png)

     - 좋아요

     ![좋아요](https://user-images.githubusercontent.com/114561525/236294851-5514a378-9c2e-48d3-bb4d-2a0c7ada9cad.png)

     - 장바구니 담기

     ![카트](https://user-images.githubusercontent.com/114561525/236294885-4b249fe1-4782-4bca-aa6d-78f14cfdc032.png)

     - 구매

     ![구매](https://user-images.githubusercontent.com/114561525/236294898-5e9db4a3-a883-4ce1-a73d-0d33d3994ba9.png)

     - 이벤트 별로 요일마다 특징이 다르다
     - 일요일은 전체적으로 좋은 데이터를 볼수있다
     - 장바구니 데이터 같은 경우 가을 시즌을 맞이해서 트렌드가 올라가는걸 볼수있다
     - 요일별 피쳐를 만들어보자

  1. 지역 데이터

     ![지역_전체](https://user-images.githubusercontent.com/114561525/236295105-20baee7a-1097-40d0-888a-947186b051bf.png)

     - 지역별 상관관계 데이터
     - 서울과 경기도의 상관관계가 생각보다 높았다
     - event_name별로도 상관관계를 보았지만 크게 다르지 않았다
     - 서울과 경기도 피쳐를 만들어 보자

  1. 카테고리 데이터

     ![카테고리1](https://user-images.githubusercontent.com/114561525/236295120-c054627f-801f-4c4a-b5bf-34d0442a99fb.png)

     - 카테고리별로 상관관계 데이터
     - 244100100 잡화, 243100100 의류
     - event_name별로도 상관관계를 보았지만 크게 다르지 않았다
     - 잡화, 의류 피쳐를 만들어보자

---

---

## TF-IDF

- TF-IDF
  - TF : 단어가 해당 문서(아이템)에 얼마나 자주 등장하는지를 나타냄
  - IDF : 특정 단어가 등장한 문서의 수를 나타내는 DF(document frequency)의 반비례 하는 수로, 다른 문서에서도 자주 등장하는 단어의 가중치를 낮춤
  - 단어 간 가중치 : TF \* IDF로 각 문서에 등장하는 모든 단어의 가중치 계산
  - 문서 간 가중치 : cosine similarity를 사용해 단어의 가중치가 문서 간에 얼마나 유사한지 계산
  ```python
  print(content_recommender('Stream Dress_Sky', 5))
  print('-' * 30)
  print(content_recommender('Modern Square Chain Bracelet', 10))

  output

  201214    Ann Shirring Check Dress in Khaki
  254995           V-NECK FLARE ONEPIECE_NAVY
  386                         하와이안 레트로 원피스_핑크
  92369               floral color P dress PK
  Name: item_name, dtype: object
  ------------------------------
  221835                        Basic Bangle_Beige [YA103_BG]
  205319    [Silver925] TN051 Sleek curved line simple bra...
  226605                                             실버 체인 팔찌
  25231             Half And Blue Cord Bracelet Ib61 [Silver]
  56473                                            핑퐁 블랙 원석팔찌
  76433     Smile blue green string Bracelet 실버코인 블루 그린 실팔...
  20081         [판도라 PANDORA] 587125CZ 모먼트 슬라이딩 스네이크 체인 로즈 팔찌
  208145                             Curvilinear Archives 39A
  172348                                          트로피컬 더블링 팔찌
  Name: item_name, dtype: object
  ```
  - Stream Dress_Sky 에서 5개
  - Modern Square Chain Bracelet 에서 10개

---

---

## Best-seller

- Best-seller
  - 탑 10개
    ![탑10](https://user-images.githubusercontent.com/114561525/236295196-96d44562-2c7f-4711-a0cf-43807715a55d.PNG)
  - 베스트셀러 성능
    ![2](https://user-images.githubusercontent.com/114561525/236295213-619081a0-310e-4911-813c-e19711e853fc.PNG)

---

---

## CF(Collaborative Filtering)

- CF
  1. knn-CF
     - 이웃의 크기를 미리 정해놓고 추천 대상과 가장 유사한 K명을 선택하는 KNN 방법
     - 이웃의 크기 대신, 유사도를 기준으로 이웃을 정하는 Thresholding 방법
     - KNN 방식의 정확도: 1.01200309134375(k=5)
       ![knn1](https://user-images.githubusercontent.com/114561525/236295292-4e6c0ca7-3774-4952-8c06-77303a615312.PNG)
     - 최적의 이웃 크기
       ![knn2](https://user-images.githubusercontent.com/114561525/236295315-a9d5422c-bfbf-4508-8289-9a3a9683e5b8.PNG)
     - 대략 20부근, 30이후로는 갑이 변하지 않는다
  2. knn-CF + 신뢰도 가중(significance weighting)

     - 공통 아이템이 많은 사용자와의 유사도를 공통 아이템이 적은 사용자와의 유사도보다 더 큰 가중치를 부여. 하지만, 공통 아이템의 수를 가중치로 직접 사용하면 안 좋을 수 있기 때문에, 신뢰도가 일정 이상인 사용자를 이웃으로 활용
       ![knn+](https://user-images.githubusercontent.com/114561525/236295346-ee571f0f-868b-45f3-8fd7-0900df8d837b.PNG)

  3. Item-based CF
     - 사용자들의 평가 패턴을 바탕으로 아이템 간의 유사도를 계산해서 사용자의 특정 아이템에 대한 예측 평점을 계산. 즉, 추천 대상(사용자)이 평가한 아이템의 평점과 다른 아이템과의 유사도를 가중해서 평균한 값을 해당 아이템에 대한 예측값으로 사용
       ![아이템](https://user-images.githubusercontent.com/114561525/236295371-b6b4cd96-d009-44bf-b6e6-6bd3c342069e.PNG)

---

---

## FM(**Factorization Machines**) & DeepFM

- FM
  - 사용자와 아이템의 다양한 특성을 종합해서 모델화함으로써 예측의 성능을 높이려는 방법
  1. 기존 데이터의 피쳐

     ![1-1](https://user-images.githubusercontent.com/114561525/236295435-3195d24f-3ad2-4594-8d74-f6df067ba0bf.PNG)
  !![1](https://user-images.githubusercontent.com/114561525/236295468-64c149c0-ee57-4cd1-b61c-1b3dbb481514.png)
  1. 요일별 피쳐 추가

     ![요일추가](https://user-images.githubusercontent.com/114561525/236295526-362296be-49d5-461c-9d2d-817c38456787.PNG)

     ![요일추가그래프](https://user-images.githubusercontent.com/114561525/236295550-2fa61442-3813-4033-bd3d-961eab12170f.png)

  2. 한국 피쳐 추가

     ![한국추가](https://user-images.githubusercontent.com/114561525/236295580-9cfeb4fe-ffb0-4aea-b395-a4042ab1354d.PNG)

     ![한국그래프](https://user-images.githubusercontent.com/114561525/236295599-08406812-ab1c-4323-9a84-5d952b7301ba.png)

  3. 카테고리 상관관계 피쳐 추가

     ![카테고리추가](https://user-images.githubusercontent.com/114561525/236295637-240d65b5-7552-4bc7-94f2-efef13bbfc29.PNG)

     ![카테고리그래프](https://user-images.githubusercontent.com/114561525/236295667-c7d4c2c1-b770-4fa2-acc1-e9b5efa349c9.png)

  4. 지역별 상관관계 피쳐 추가

     ![지역추가](https://user-images.githubusercontent.com/114561525/236295710-050d3518-2f64-4d84-8e1a-ae188416a00f.PNG)

     ![지역그래프](https://user-images.githubusercontent.com/114561525/236295733-4941db51-e4bf-48e8-a21f-d58877704b96.png)

  5. 시간 피쳐 추가

     ![시간추가](https://user-images.githubusercontent.com/114561525/236295774-fc4a5bdb-19eb-4921-ac49-c83ce064065c.PNG)

     ![시간추가그래프](https://user-images.githubusercontent.com/114561525/236295813-191a5d14-5ff9-48d6-86e7-8159ecde52f7.png)

  6. 시간대별 피쳐 추가

     - 2~6시 데이터, 7~18시 데이터, 18~1시 데이터로 나뉘어서 피쳐 추가
       ![시간대추가](https://user-images.githubusercontent.com/114561525/236295877-d44b4439-c254-4e34-9c1d-998662172247.PNG)
       ![시간대그래프](https://user-images.githubusercontent.com/114561525/236295909-e1c5ea5a-c62c-4d94-933b-e192b6c19845.png)
     - 피쳐들을 추가하면 할수록 성능이 좋아지는걸 알수있다
     - 즉, 분석을 통해서 새로운 피쳐들을 더 추가한다면 성능은 더 좋아질수도 있다

  7. DeepFM

     - FM의 장점과 Deep Learning의 장점을 모두 합친 모델이다
     - 추가 feature engineering없이 raw feature를 그대로 사용할 수 있다

     ![deepfm](https://user-images.githubusercontent.com/114561525/236295936-35365fc6-3b74-4bda-81b1-4c20230b0b59.PNG)

     ![deepfm2](https://user-images.githubusercontent.com/114561525/236295963-65f2c351-8bbd-4a1c-a644-b57123566e20.PNG)

---

---

## LGBMRanker

- LGBMRanker
  - Featur Importance
  ![1](https://user-images.githubusercontent.com/114561525/236296008-b6ff8d3f-c687-4e72-bc05-6282d7196164.png)
  - NDCG
    ![1](https://user-images.githubusercontent.com/114561525/236296028-9dbcf55c-29dd-4db5-a915-0b2f61120880.png)
