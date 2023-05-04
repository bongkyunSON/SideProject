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

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8b65e79b-0114-4403-b26f-c5f1db5aacbf/Untitled.png)

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

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3bff7941-73da-422b-a0e1-678d20fd9884/Untitled.png)

     - 모바일데이터의 특징처럼 자는시간대 데이터가 많다
     - 2~6시 데이터, 7~18시 데이터, 18~1시 데이터를 나눠서 피쳐를 만들자
     - 시간대별로 구매 카테고리가 다른다. 즉 시간대별로 고객이 다르다
     - 시간대별로 특징을 나뉘어 피쳐를 만들어보자

  1. event_name 요일별 데이터

     - 클릭

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bc6a3f82-8f91-43ca-b324-43a5826391bd/Untitled.png)

     - 좋아요

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d7f41fba-b2cc-46a5-83ef-e11416b580c0/Untitled.png)

     - 장바구니 담기

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9c2ef340-6c59-4359-80aa-ea60aa48c8e5/Untitled.png)

     - 구매

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cc8db6c0-e7b2-4731-a508-117d8685e3c2/Untitled.png)

     - 이벤트 별로 요일마다 특징이 다르다
     - 일요일은 전체적으로 좋은 데이터를 볼수있다
     - 장바구니 데이터 같은 경우 가을 시즌을 맞이해서 트렌드가 올라가는걸 볼수있다
     - 요일별 피쳐를 만들어보자

  1. 지역 데이터

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/821ce3f2-6124-4222-ad60-8373815823cd/Untitled.png)

     - 지역별 상관관계 데이터
     - 서울과 경기도의 상관관계가 생각보다 높았다
     - event_name별로도 상관관계를 보았지만 크게 다르지 않았다
     - 서울과 경기도 피쳐를 만들어 보자

  1. 카테고리 데이터

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/937679ef-b682-455e-9e43-e5e5bedbf492/Untitled.png)

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
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ee88c676-8303-4b41-bdae-82c3b8bc6f73/Untitled.png)
  - 베스트셀러 성능
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/4dfab7f0-12e4-4940-ac5c-593e8940f95c/Untitled.png)

---

---

## CF(Collaborative Filtering)

- CF
  1. knn-CF
     - 이웃의 크기를 미리 정해놓고 추천 대상과 가장 유사한 K명을 선택하는 KNN 방법
     - 이웃의 크기 대신, 유사도를 기준으로 이웃을 정하는 Thresholding 방법
     - KNN 방식의 정확도: 1.01200309134375(k=5)
       ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fc79838c-f697-4130-833b-2f80611228b3/Untitled.png)
     - 최적의 이웃 크기
       ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/457dc3a7-c384-43dc-b516-d48db6ba5966/Untitled.png)
     - 대략 20부근, 30이후로는 갑이 변하지 않는다
  2. knn-CF + 신뢰도 가중(significance weighting)

     - 공통 아이템이 많은 사용자와의 유사도를 공통 아이템이 적은 사용자와의 유사도보다 더 큰 가중치를 부여. 하지만, 공통 아이템의 수를 가중치로 직접 사용하면 안 좋을 수 있기 때문에, 신뢰도가 일정 이상인 사용자를 이웃으로 활용
       ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/370f6735-78c2-4038-8fa0-16fb09d0f91f/Untitled.png)

  3. Item-based CF
     - 사용자들의 평가 패턴을 바탕으로 아이템 간의 유사도를 계산해서 사용자의 특정 아이템에 대한 예측 평점을 계산. 즉, 추천 대상(사용자)이 평가한 아이템의 평점과 다른 아이템과의 유사도를 가중해서 평균한 값을 해당 아이템에 대한 예측값으로 사용
       ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a552a510-7c1f-48e7-bf5e-2cc2f9870692/Untitled.png)

---

---

## FM(**Factorization Machines**) & DeepFM

- FM
  - 사용자와 아이템의 다양한 특성을 종합해서 모델화함으로써 예측의 성능을 높이려는 방법
  1. 기존 데이터의 피쳐

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3c3cd8e9-562d-41a8-b8b5-b60a20df84ad/Untitled.png)
  ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e9de2f93-dfcf-4f0c-8b6f-26886ae3e77a/Untitled.png)
  1. 요일별 피쳐 추가

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cc3c23c3-96d2-477b-b235-ab8be23573a3/Untitled.png)

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/014da90c-7395-4bb2-a29c-b8fc619a9ed4/Untitled.png)

  2. 한국 피쳐 추가

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1df439b3-c36d-4713-85fc-bb66265908cd/Untitled.png)

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0ea0c561-748d-4821-af9b-ccc8c6d6101d/Untitled.png)

  3. 카테고리 상관관계 피쳐 추가

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d23f5475-fe91-4d71-b106-26d2770ccf7c/Untitled.png)

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6938c208-73b2-4880-b350-0201fc30a4d1/Untitled.png)

  4. 지역별 상관관계 피쳐 추가

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ea1ddcd7-8395-4b41-9c05-3b5b54fa09e7/Untitled.png)

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c5516ddc-3226-4919-b641-e3ae9c4c4ec8/Untitled.png)

  5. 시간 피쳐 추가

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/acf1d74e-e07b-4db5-a462-ed7b6bbe7ae2/Untitled.png)

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/49fc4600-c600-4fe0-be24-d7d61006e443/Untitled.png)

  6. 시간대별 피쳐 추가

     - 2~6시 데이터, 7~18시 데이터, 18~1시 데이터로 나뉘어서 피쳐 추가
       ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ddbdaf27-e5af-4d44-9361-b9a1131b6afa/Untitled.png)
       ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3db0beea-3b26-4b05-95bb-f64d9a417eb9/Untitled.png)
     - 피쳐들을 추가하면 할수록 성능이 좋아지는걸 알수있다
     - 즉, 분석을 통해서 새로운 피쳐들을 더 추가한다면 성능은 더 좋아질수도 있다

  7. DeepFM

     - FM의 장점과 Deep Learning의 장점을 모두 합친 모델이다
     - 추가 feature engineering없이 raw feature를 그대로 사용할 수 있다

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c158f742-2bf0-4588-bb04-50f929e33979/Untitled.png)

     ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bc89132c-43fa-4226-8d65-916bd50757e5/Untitled.png)

---

---

## LGBMRanker

- LGBMRanker
  - Featur Importance
  ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/363cab45-9681-40ac-bcc8-6f67c85a14c1/Untitled.png)
  - NDCG
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/80f8b886-ab40-493b-a167-206940f46dd4/Untitled.png)
