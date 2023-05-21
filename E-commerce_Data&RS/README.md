## 프로젝트 소개

- **목적:**

  1. E-commerce 데이터를 통계적 분석 해보자
  2. 통계적 분석을 통해 새로운 feature를 만들어 보자
  3. FM(factorization machine) 추천시스템의 적용해보자

- **진행 순서:**
  1. 데이터를 불러오기 및 데이터 전처리
  2. 데이터 분석
  3. 추천시스템
  4. 프로젝트를 마치며

---

---

# 1. 데이터를 불러오기 및 데이터 전처리

- 1-1
  ### 1. 데이터 불러오기 및 전반적인 정보 확인
  Python의 import를 활용해 데이터 분석용 패키지인 판다스(Pandas)를 읽어옵니다.
  ```python
  import pandas as pd
  ```
  판다스 데이터프레임(DataFrame)을 출력할 때, 여러컬럼을 편하게 보기 위해 최대 출력 컬럼을 50개로 늘려줍니다.
  ```python
  pd.options.display.max_columns = 50
  ```
  데이터 로딩하기 - 데이터를 읽어올때는 판다스(Pandas)의 read_csv 라는 기능을 사용합니다.
  ```python
  FILES_DIR = './files/'
  events = pd.read_csv(FILES_DIR + 'sampled_events.csv')
  product = pd.read_csv(FILES_DIR + 'sampled_products.csv')
  users = pd.read_csv(FILES_DIR + 'sampled_users.csv')
  ```
  copy()를 이용하여 불러온 데이터들을 \_df로 assign 한다
  ```python
  events_df = events.copy()
  product_df = product.copy()
  users_df = users.copy()
  ```
  행렬 사이즈를 출력합니다. 결과는 row & column 으로 표시됩니다.
  ```python
  print(events_df.shape)
  print(product_df.shape)
  print(users_df.shape)
  ```
  ![https://blog.kakaocdn.net/dn/c0qllx/btsfpdDipJr/64MGInOLzP13N5QvNmmVXk/img.png](https://blog.kakaocdn.net/dn/c0qllx/btsfpdDipJr/64MGInOLzP13N5QvNmmVXk/img.png)
  컬럼 리스트를 출력합니다.
  ```python
  print(events_df.columns)
  print(product_df.columns)
  print(users_df.columns)
  ```
  ![https://blog.kakaocdn.net/dn/c2HvUo/btsfdrislMN/ia8KU2zfg1Ad2x0235CqTK/img.png](https://blog.kakaocdn.net/dn/c2HvUo/btsfdrislMN/ia8KU2zfg1Ad2x0235CqTK/img.png)
  ###
  ### event_df
  다음은 간편하게 데이터를 훑기 위해 head()로 event_df 데이터의 상위 5개를 띄웁니다.
  - head는 괄호() 안에 숫자를 넣어 원하는 만큼 raw를 출력할 수 있습니다. 기본은 5개입니다.
  ```python
  events_df.head()
  ```
  ![https://blog.kakaocdn.net/dn/nDFBW/btsfdaurhTG/peKejcO4PSSE8Xu6GKazZ0/img.png](https://blog.kakaocdn.net/dn/nDFBW/btsfdaurhTG/peKejcO4PSSE8Xu6GKazZ0/img.png)
  ### product_df
  ```python
  product_df.head()
  ```
  ### users_df
  ```python
  users_df.head()
  ```
  ### 2. 불러온 데이터들을 전처리
  다음으로 Data 과학자들이 일을 하면서 가장 많은 시간을 소모하는 전처리 과정으로 들어가겠습니다.(참고 내용 From Forbes)
  ![https://blog.kakaocdn.net/dn/l9fok/btsffvYRiy9/VgDpA7ck3BFVbn9b6PdKHk/img.png](https://blog.kakaocdn.net/dn/l9fok/btsffvYRiy9/VgDpA7ck3BFVbn9b6PdKHk/img.png)
  [https://www.forbes.com/sites/gilpress/2016/03/23/data-preparation-most-time-consuming-least-enjoyable-data-science-task-survey-says/#437fbfc66f63](https://www.forbes.com/sites/gilpress/2016/03/23/data-preparation-most-time-consuming-least-enjoyable-data-science-task-survey-says/#437fbfc66f63)
  결측치를 확인합니다.
  ```python
  events_df.isnull().sum()
  ```
  ![https://blog.kakaocdn.net/dn/s6ybq/btsfpdpM04o/VuSrblWY0c5xK3KkhjdwEk/img.png](https://blog.kakaocdn.net/dn/s6ybq/btsfpdpM04o/VuSrblWY0c5xK3KkhjdwEk/img.png)
  결측치가 있는 데이터 컬럼을 확인해 보자
  ```python
  events_df[['mobile_brand_name', 'mobile_model_name', 'mobile_marketing_name', 'country', 'region']]
  ```
  ![https://blog.kakaocdn.net/dn/0SkGh/btsfdausYb5/DPBpQTzRViYnXjFUHQvZXK/img.png](https://blog.kakaocdn.net/dn/0SkGh/btsfdausYb5/DPBpQTzRViYnXjFUHQvZXK/img.png)
  플랫폼 컬럼이 있기 때문에 모바일 컬럼들을 drop한다. 플랫폼 컬럼은 결측치 데이터가 없다
  ```python
  events_df['platform']
  ```
  ![https://blog.kakaocdn.net/dn/sSfzK/btsfpeWw6M6/gxSJUICnzKPM2KJvsfVcS1/img.png](https://blog.kakaocdn.net/dn/sSfzK/btsfpeWw6M6/gxSJUICnzKPM2KJvsfVcS1/img.png)
  'country', 'region' 컬럼은  un\_컬럼네임을 붙여 결측치를 처리하자
  ```python
  events_df.drop('mobile_marketing_name', axis=1, inplace=True)
  events_df.drop('mobile_brand_name', axis=1, inplace=True)
  events_df.drop('mobile_model_name', axis=1, inplace=True)
  events_df['country'].fillna('un_country', inplace=True)
  events_df['region'].fillna('un_region', inplace=True)
  ```
  product_df 결측치를 확인합니다.
  ```python
  product_df.isnull().sum()
  ```
  ![https://blog.kakaocdn.net/dn/URk1i/btsfe74Qjec/Uukn4MuKl6JS4Su9iSaby0/img.png](https://blog.kakaocdn.net/dn/URk1i/btsfe74Qjec/Uukn4MuKl6JS4Su9iSaby0/img.png)
  결측치가 너무 적어서 dropna를 하자
  ```python
  product_df.dropna(axis=0, inplace=True)
  ```
  users_df 결측치를 확인합니다.
  ```python
  users_df.isnull().sum()
  ```
  ![https://blog.kakaocdn.net/dn/BREHD/btsfvokevTR/UmcSxY4YJ6kcUKdjv2q0LK/img.png](https://blog.kakaocdn.net/dn/BREHD/btsfvokevTR/UmcSxY4YJ6kcUKdjv2q0LK/img.png)
  'birth*date', 'gender' 컬럼은  un*컬럼네임을 붙여 결측치를 처리하자
  ```python
  users_df['birth_date'].fillna('un_birth_date', inplace=True)
  users_df['gender'].fillna('un_gender', inplace=True)
  ```
  'event_timestamp' 컬럼은 보기에는 날짜 시간 형식으로 보이지만 아래처럼 현재 dtype이 object로 되어있습니다.
  ```python
  events_df['event_timestamp']
  ```
  ![https://blog.kakaocdn.net/dn/b7IkOx/btsfbz88GAB/3Pf3jDclN8RWGz5vBCY0E1/img.png](https://blog.kakaocdn.net/dn/b7IkOx/btsfbz88GAB/3Pf3jDclN8RWGz5vBCY0E1/img.png)
  'event_timestamp' 컬럼에 있는 값을 판다스의 to_datetime이라는 함수를 사용해서 날짜와 시간을 표현할 수 있는 datetime 형태로 변경합니다.
  ```python
  events_df['event_timestamp'] = pd.to_datetime(events_df['event_timestamp'])
  ```
  ![https://blog.kakaocdn.net/dn/8EMfs/btsfe7qfB0w/bRicHvVRnWvdJ8MraVr1K1/img.png](https://blog.kakaocdn.net/dn/8EMfs/btsfe7qfB0w/bRicHvVRnWvdJ8MraVr1K1/img.png)
  새로운 컬럼을 생성하며 연, 월, 일, 시, 분  단위로 나누는 작업을 진행합니다.
  마지막줄은 새로 생성한 6개 컬럼의 결과를 보여줍니다.
  ```python
  events_df["event_timestamp_year"] = events_df["event_timestamp"].dt.year
  events_df["event_timestamp_month"] = events_df["event_timestamp"].dt.month
  events_df["event_timestamp_day"] = events_df["event_timestamp"].dt.day
  events_df["event_timestamp_hour"] = events_df["event_timestamp"].dt.hour
  events_df["event_timestamp_minute"] = events_df["event_timestamp"].dt.minute

  print(events_df.shape)# 내용 변경 후 row와 컬럼 형태 확인

  events_df[["event_timestamp", "event_timestamp_year", "event_timestamp_month",\
              "event_timestamp_day", "event_timestamp_hour", "event_timestamp_minute"]].head()
  ```
  ![https://blog.kakaocdn.net/dn/EviRd/btsfvo5Ctnl/hsjyQDw1nI26CRFM0DAks1/img.png](https://blog.kakaocdn.net/dn/EviRd/btsfvo5Ctnl/hsjyQDw1nI26CRFM0DAks1/img.png)
- 1-2
  event_df의 OS 정보를 보면 아래와 같은 결과가 나옵니다.
  ```python
  events_df['operating_system_version']
  ```
  ![https://blog.kakaocdn.net/dn/BKl0b/btsfvokfIGn/WYX5NUq7qqrsTVftr0xcE0/img.png](https://blog.kakaocdn.net/dn/BKl0b/btsfvokfIGn/WYX5NUq7qqrsTVftr0xcE0/img.png)
  컬럼 내역을 보면 모바일 OS 타입은 크게 iOS와 Android로 나눠지는걸 확인할 수 있습니다.
  OS 타입과 버전을 따로 볼 수 있도록 2개 컬럼을 만들어보겠습니다.
  ```python
  events_df['operating_system_version'].unique()
  ```
  ![https://blog.kakaocdn.net/dn/lDnFK/btsfjIwIRJ8/WL5ax30BnbCLync76nntEK/img.png](https://blog.kakaocdn.net/dn/lDnFK/btsfjIwIRJ8/WL5ax30BnbCLync76nntEK/img.png)
  먼저 OS 타입부터 구해보겠습니다.
  ```python
  def find_ostype(OS):# find_ostype이라는 함수 지정if 'iOS' in OS:# 만약 'iOS'라는 string이 OS에 있으면return "iOS"# 'iOS'라는 string만 출력한다else:# 그게 아니라면return 'Android'# 'Android'라는 string만 출력한다
  ```
  위 함수를 아래 첫줄 코딩을 통해 적용 후 새로 생성한 'ostype' 컬럼이 결과에 맞게 도출되었는지 확인.
  ```python
  events_df['ostype'] = events_df['operating_system_version'].apply(find_ostype)# find_ostype 적용print(events_df['ostype'].unique())# ostype의 값들 확인
  events_df['ostype'].head()
  ```
  'ostype'은 잘 생성되었고 값들도 정리된걸 볼 수 있습니다. 다음으로 OS 버전만을 보기 위한 컬럼을 생성해보겠습니다.
  아래를 보시면 replace를 사용해 iOS와 Android 라는 내용이 있을시 공백으로 변환하여 숫자만 남기는 작업을 진행 했습니다.
  ```python
  events_df['osversion(number)'] = events_df['operating_system_version']\
                                      .str.replace('iOS', '').str.replace('Android', '')

  print(events_df['osversion(number)'].unique())# 해당 컬럼의 값들 확인
  events_df['osversion(number)'].head()
  ```
  ![https://blog.kakaocdn.net/dn/diPLWU/btsftGywgE4/Q0KE1w0lmDeWaz6dNFuF31/img.png](https://blog.kakaocdn.net/dn/diPLWU/btsftGywgE4/Q0KE1w0lmDeWaz6dNFuF31/img.png)
  Android와 iOS의 버전별로 몇명의 유저들이 있는지 확인해보겠습니다.
  ```python
  os_data = (events_df
                  .groupby(['ostype', 'osversion(number)'])# 여러 컬럼들 중 연관된 컬럼을 그룹화 합니다
                  .size()# 그룹별 속한 row 수를 반환합니다.
                  .rename('count_values')# 컬럼이름을 count_values로 변경합니다.
                  )
  os_data.to_frame()# to_frame()을 통해 series형식을 dataframe형식으로 보여줍니다.
  ```
  ![https://blog.kakaocdn.net/dn/dYLj4a/btsfaHGANdW/WgTzCQmY1bB6fcNCUmljf0/img.png](https://blog.kakaocdn.net/dn/dYLj4a/btsfaHGANdW/WgTzCQmY1bB6fcNCUmljf0/img.png)
  Android만 따로 보겠습니다.
  ```python
  os_data = os_data.to_frame()# DataFrame으로 변경된 os_data을 다시 덮어씁니다.
  os_data.T['Android']# .T 는 현재 보고있는 DataFrame을 반전시키는 기능입니다.
  ```
  ![https://blog.kakaocdn.net/dn/dNrkVs/btsfcj6iK6a/qaxZvLyQDB6HMdNs344Ok1/img.png](https://blog.kakaocdn.net/dn/dNrkVs/btsfcj6iK6a/qaxZvLyQDB6HMdNs344Ok1/img.png)
  iOS만 따로 보겠습니다.
  ![https://blog.kakaocdn.net/dn/benwUh/btsfbzahVup/NLQxzRTYRe81JDh8QbvyR1/img.png](https://blog.kakaocdn.net/dn/benwUh/btsfbzahVup/NLQxzRTYRe81JDh8QbvyR1/img.png)
- 1-3
  이번에는 product_df 을 정리해보도록 하겠습니다.
  ```python
  product_df.columns
  ```
  ![https://blog.kakaocdn.net/dn/kZVmi/btsfckc7kVm/msCKWcGNkSkM6VBpUkPHIk/img.png](https://blog.kakaocdn.net/dn/kZVmi/btsfckc7kVm/msCKWcGNkSkM6VBpUkPHIk/img.png)
  category1_name 컬럼의 비율을 확인해 보겠습니다.
  ```python
  product_df['category1_name'].value_counts()
  ```
  ![https://blog.kakaocdn.net/dn/3WGon/btsfbzH9bUP/KaEaj05mJbSg4CX69CygL0/img.png](https://blog.kakaocdn.net/dn/3WGon/btsfbzH9bUP/KaEaj05mJbSg4CX69CygL0/img.png)
  ```python
  product_df['category1_name'].value_counts(normalize=True)
  ```
  ![https://blog.kakaocdn.net/dn/cSTZq8/btsfeAsH6RU/Zfx1IAJWUvgFIYlztoFxc0/img.png](https://blog.kakaocdn.net/dn/cSTZq8/btsfeAsH6RU/Zfx1IAJWUvgFIYlztoFxc0/img.png)
  ```python
  cat1_name = product_df['category1_name'].value_counts()
  print(cat1_name)# 내용 확인print('총 개수 = ',cat1_name.sum())# 총합 확인print('Others의 비율은 = ', cat1_name['의류'] / cat1_name.sum())# 의류비율
  ```
  ![https://blog.kakaocdn.net/dn/lRus2/btsfaJxHC5j/YhVQwjx2VbynKdyveORmDk/img.png](https://blog.kakaocdn.net/dn/lRus2/btsfaJxHC5j/YhVQwjx2VbynKdyveORmDk/img.png)
  시각화시 한글이 깨지기 때문에 한글을 안깨지게 합니다
  ```python
  from matplotlib import font_manager, rc
  font_path = "C:/Windows/Fonts/malgun.TTF"
  font = font_manager.FontProperties(fname=font_path).get_name()
  rc('font', family=font)
  ```
  시각화를 합니다.
  ```python
  plt.figure(figsize=[20, 5])# 그래프의 크기 지정
  sns.countplot(data = product_df, x='category1_name'\
                , palette=sns.color_palette('coolwarm', 4))# palette를 사용하여 그래프 bar 색 지정
  ```
  ![https://blog.kakaocdn.net/dn/MlK7H/btsftIQHfqh/cxBpyyb8llAsu44WORWt00/img.png](https://blog.kakaocdn.net/dn/MlK7H/btsftIQHfqh/cxBpyyb8llAsu44WORWt00/img.png)
  category2_name 컬럼의 비율을 확인해 보겠습니다.
  ```python
  product_df['category2_name'].value_counts()
  ```
  ![https://blog.kakaocdn.net/dn/AHv4p/btsfdbtrRWe/FCVLNrUHMvaEXsiIXss3pK/img.png](https://blog.kakaocdn.net/dn/AHv4p/btsfdbtrRWe/FCVLNrUHMvaEXsiIXss3pK/img.png)
  category3_name 컬럼의 비율을 확인해 보겠습니다.
  ```python
  product_df['category3_name'].value_counts()
  ```
  ![https://blog.kakaocdn.net/dn/E49ju/btsfghMZ2fm/vkXi4jB7xkCsUKAiHZ0g81/img.png](https://blog.kakaocdn.net/dn/E49ju/btsfghMZ2fm/vkXi4jB7xkCsUKAiHZ0g81/img.png)
  brand_name 컬럼의 비율을 확인해 보겠습니다.
  ```python
  product_df['brand_name'].value_counts()
  ```
  ![https://blog.kakaocdn.net/dn/cW5BAN/btsfeh7QWK3/CAv5CmbSDSJnpQ1j2pRONk/img.png](https://blog.kakaocdn.net/dn/cW5BAN/btsfeh7QWK3/CAv5CmbSDSJnpQ1j2pRONk/img.png)
  보통 가격의 100원 이하 데이터는 없기 때문에 100원이하 데이터를 확인해 본다
  ```python
  print(len(product_df[product_df['price'] <= 100]))
  product_df[product_df['price'] <= 100].head()
  ```
  ![https://blog.kakaocdn.net/dn/dHycxf/btsfeAM2jkh/0IfpUxo4xP3hGYhONUlmD0/img.png](https://blog.kakaocdn.net/dn/dHycxf/btsfeAM2jkh/0IfpUxo4xP3hGYhONUlmD0/img.png)
  price데이터에 100원 이하는 0원밖에 없다. 0원인 데이터는 큰 의미가 없고 15개의 데이터밖에 없기 때문에 drop한다
  ```python
  print(product_df[product_df['price'] <= 100].price.unique())
  product_df = product_df[product_df['price'] != 0]
  product_df.head()
  ```
  ![https://blog.kakaocdn.net/dn/b6Gdll/btsfbkRZhwj/8A4xXcKy3WvkTXv0Y8AYb1/img.png](https://blog.kakaocdn.net/dn/b6Gdll/btsfbkRZhwj/8A4xXcKy3WvkTXv0Y8AYb1/img.png)
- 1-4
  이번에는 users_df 을 정리해보도록 하겠습니다.
  데이터에 대한 정보를 보겠습니다.
  ```python
  print(users_df.info())
  print('\n')
  print(users_df.columns)
  ```
  ![https://blog.kakaocdn.net/dn/d63O85/btsffvSfEzH/DsPjwE5AgbKN4RUP9IZk40/img.png](https://blog.kakaocdn.net/dn/d63O85/btsffvSfEzH/DsPjwE5AgbKN4RUP9IZk40/img.png)
  이번에는 성별 데이터를 다뤄보겠습니다.
  ```python
  users_df['gender'].unique()#un_gender는 결측치 처리할때 nan값을 처리한 값입니다
  ```
  ![https://blog.kakaocdn.net/dn/lDNsi/btsftI4gbIw/ST0CHIcwe2Fvgb2YtJUepK/img.png](https://blog.kakaocdn.net/dn/lDNsi/btsftI4gbIw/ST0CHIcwe2Fvgb2YtJUepK/img.png)
  이번엔 성별 비율을 확인해보겠습니다.
  ```python
  print(users_df['gender'].value_counts())
  print('\n')
  print(users_df['gender'].value_counts(normalize=True))
  ```
  ![https://blog.kakaocdn.net/dn/biydft/btsfdriA1h3/hP8QGjZcvnN9VKXY9KRpjK/img.png](https://blog.kakaocdn.net/dn/biydft/btsfdriA1h3/hP8QGjZcvnN9VKXY9KRpjK/img.png)
  상대적으로 여성분들의 비율이 높은걸 확인하실 수 있습니다.
  이번에는 birth_date 컬럼의 데이터를 이용해서 age를 구해보도록 하겠습니다.
  ```python
  today = pd.Timestamp.now().floor('D')
  users_df['birth_date'] = pd.to_datetime(users_df['birth_date'], errors='coerce')
  users_df = users_df[~pd.isnull(users_df['birth_date'])]
  users_df['age'] = ((today - users_df['birth_date']).dt.days / 365.25).astype(int)

  users_df.head()
  ```
  ![https://blog.kakaocdn.net/dn/dHqDYJ/btsftJIScrn/4sLjwflxUGHEpk3gTkQ8jK/img.png](https://blog.kakaocdn.net/dn/dHqDYJ/btsftJIScrn/4sLjwflxUGHEpk3gTkQ8jK/img.png)
- 1-5
  이번에는 처음부터 정리했던 데이터들(수정한 events_df, poduct_df, users_df)을 병합해보도록 하겠습니다.
  - - 병합 과정에 대해서 자세한 내용을 확인하시려면 링크를 참조해주세요.
  (link : [https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html](https://pandas.pydata.org/pandas-docs/stable/user_guide/merging.html) )
  우선 데이터의 형태를 먼저 파악해보도록 하겠습니다.
  ```python
  print(events_df.shape)
  print(events_df.columns)
  events_df.head(3)
  ```
  ![https://blog.kakaocdn.net/dn/cyW80w/btsfbjMknzU/NKNYxgKnTG7NS7cQesuWe0/img.png](https://blog.kakaocdn.net/dn/cyW80w/btsfbjMknzU/NKNYxgKnTG7NS7cQesuWe0/img.png)
  ```python
  print(product_df.shape)
  print(product_df.columns)
  product_df.head(3)
  ```
  ![https://blog.kakaocdn.net/dn/b0Myw7/btsfcliOAF3/94esNJYkScMhMMTiefsKF1/img.png](https://blog.kakaocdn.net/dn/b0Myw7/btsfcliOAF3/94esNJYkScMhMMTiefsKF1/img.png)
  ```python
  print(users_df.shape)
  print(users_df.columns)
  users_df.head(3)
  ```
  ![https://blog.kakaocdn.net/dn/PTnoz/btsfcN0to4L/xSQhJ0DUs1W7oIJhXnOdak/img.png](https://blog.kakaocdn.net/dn/PTnoz/btsfcN0to4L/xSQhJ0DUs1W7oIJhXnOdak/img.png)
  데이터를 merge를 이용하여 병합합니다
  ```python
  total = pd.merge(events_df, product_df)

  print(total.shape)# total 변수에 할당된 데이터의 행렬 사이즈를 출력합니다print(total.columns)# total 컬럼들을 출력합니다
  total.head()# total 데이터의 상위 5개를 띄웁니다
  ```
  다음으로 위에서 병합한 total와 users_df데이터를 병합해보도록 하겠습니다.
  ```python
  total = pd.merge(total, users_df)

  print(total.shape)# total 변수에 할당된 데이터의 행렬 사이즈를 출력합니다print(total.columns)# total 컬럼들을 출력합니다
  total.head()# total 데이터의 상위 5개를 띄웁니다
  ```
  ![https://blog.kakaocdn.net/dn/bBuiKo/btsfpdwHa8O/k6MTYAzjkvwKyYqLcUWeek/img.png](https://blog.kakaocdn.net/dn/bBuiKo/btsfpdwHa8O/k6MTYAzjkvwKyYqLcUWeek/img.png)
  데이터 병합이 완료되었습니다.
  total 데이터 csv 파일로 저장하기
  ```python
  # total 데이터 csv파일로 저장하기
  total.to_csv('total.csv')
  ```

---

---

# 2. 데이터 분석

- 2-1
  첫번째로 데이터를 불러온 후 전반적인 부분을 확인해보겠습니다.
  ```python
  print(total_df.shape)# total_df 변수에 할당된 데이터의 행렬 사이즈를 출력합니다print(total_df.columns)# total_df 컬럼들을 출력합니다
  total_df.head()# total_df 데이터의 상위 5개를 띄웁니다
  ```
  ![https://blog.kakaocdn.net/dn/bgnyRb/btsfe6SQlvL/vHLSKuG30oClhZ8qwN8NDK/img.png](https://blog.kakaocdn.net/dn/bgnyRb/btsfe6SQlvL/vHLSKuG30oClhZ8qwN8NDK/img.png)
  데이터에 대한 정보를 보겠습니다.
  ```python
  total_df.info()
  ```
  ![https://blog.kakaocdn.net/dn/d1kZbT/btsfegOYMZH/qNkwq5ZWYPeJWKjVOhrqo1/img.png](https://blog.kakaocdn.net/dn/d1kZbT/btsfegOYMZH/qNkwq5ZWYPeJWKjVOhrqo1/img.png)
  ```python
  total_df.describe()
  ```
  ![https://blog.kakaocdn.net/dn/LafJV/btsfdYgkXqb/LGo9qy1c01VLk1AK6YYLek/img.png](https://blog.kakaocdn.net/dn/LafJV/btsfdYgkXqb/LGo9qy1c01VLk1AK6YYLek/img.png)
  차후 편한 분석을 위해 필요한 컬럼만 골라내고 'session_id'를 인덱스로 지정해주도록 하겠습니다.
  ```python
  # 필요한 컬럼들만 리스트화 해서 변수에 저장합니다
  col_list = ['session_id', 'event_timestamp', 'event_name', 'user_no', 'item_no',
                   'country', 'region', 'event_timestamp_year',
                   'event_timestamp_month', 'event_timestamp_day', 'event_timestamp_hour', 'event_timestamp_minute',
                   'category1_name', 'category2_name', 'category3_name', 'brand_name', 'gender', 'age'
                   ]

  raw_data = total_df[col_list]# 저장된 변수를 활용하여 raw_data에 저장합니다print(raw_data.shape)# raw_data의 shape을 확인합니다
  raw_data.columns
  ```
  ![https://blog.kakaocdn.net/dn/bbJaGD/btsfbzaF38a/KvpIO7v6WCWywzM7Wj5u30/img.png](https://blog.kakaocdn.net/dn/bbJaGD/btsfbzaF38a/KvpIO7v6WCWywzM7Wj5u30/img.png)
  컬럼들이 잘 들어간것을 확인했습니다. 이제 index를 지정하겠습니다.
  ```python
  raw_data = raw_data.set_index('session_id')# set_index를 통해 인덱스를 지정합니다
  raw_data.head(3)
  ```
  ![https://blog.kakaocdn.net/dn/TuDiB/btsffv57anz/8QOrTQjrd3fCavNHOLOBIK/img.png](https://blog.kakaocdn.net/dn/TuDiB/btsffv57anz/8QOrTQjrd3fCavNHOLOBIK/img.png)
- 2-2
  한 명의 고객을 데려왔을 때, 고객이 회사에게 제공해주는 수익(Customer Lifetime Value, 이하 LTV)
  LTV에 대해서는 LTV분석시 자세히 설명드리겠습니다.
  LTV가 높은 고객군의 인구통계학적 정보. 이커머스 같은 경우 남성고객보다 여성 고객들이 많습니다 .
  가령 요일/시간별 결제 비율. 가령 주중보다 주말에 결제할 확률이 높다면, 상품을 결제할 의사가 있는 고객들에게 주말에 결제를 유도하는 메일을 보낼 수 있습니다.
  이러한 요청을 종합하며, 마케팅팀의 의사결정에 도움이 될 수 있는 정보를 뽑아보도록 하겠습니다.
  클릭/ 좋아여 / 장바구니/ 구매의 총 인원 수와 비율을 구해보겠습니다.먼저 가장 기본적인 정보는 클릭/ 좋아여 / 장바구니/ 구매 비율입니다.
  성별 데이터를 살펴 보겠습니다.
  ```python
  raw_data['gender'].value_counts()
  ```
  ![https://blog.kakaocdn.net/dn/dIGjw2/btsfda2LEwW/kcWtXwyP0K4uyirnI0qT4k/img.png](https://blog.kakaocdn.net/dn/dIGjw2/btsfda2LEwW/kcWtXwyP0K4uyirnI0qT4k/img.png)
  여성의 비율이 많다는걸 알 수 있습니다.
  다만 결측치 처리로 인해 un_gender값이 있습니다. 나중에 추천시스템을 만들어야하는데 un_gender가 시스템에 악영향을 미칠수있습니다. 카테고리쪽을 살펴보아도 남자, 여자 데이터를 확실히 나누기가 어렵습니다. 데이터가 많아서 샘플링을 하더나 스케일링을 해야하는데 우선 un_gender같은 경우 drop을 하도록 하겠습니다.
  un_gender를 날리고 전체 shape 수 5544530 - 366506 = 5178024 인지 확인하기
  ```python
  raw_data = raw_data[raw_data['gender'] != 'un_gender']
  raw_data.shape
  ```
  ![https://blog.kakaocdn.net/dn/cduXlC/btsfcNNiRH4/XFXdXUyyTMKpfYCc664hbK/img.png](https://blog.kakaocdn.net/dn/cduXlC/btsfcNNiRH4/XFXdXUyyTMKpfYCc664hbK/img.png)
  성별로 나이의 평균을 구해보겠습니다.
  ```python
  print(raw_data['age'].min())# 해당 컬럼의 최소값print(raw_data['age'].max())# 해당 컬럼의 최대값

  pd.pivot_table(raw_data, index = 'gender',\
                 values='age', aggfunc='mean')
  ```
  ![https://blog.kakaocdn.net/dn/sy4sV/btsfdYgEqC9/1Yqma20cz8zLAFVkiKbzg0/img.png](https://blog.kakaocdn.net/dn/sy4sV/btsfdYgEqC9/1Yqma20cz8zLAFVkiKbzg0/img.png)
  성별로 나이의 평균에는 큰 차이를 보이지 않습니다.
  나이의 최대값이 조금 이상한점이 있습니다.
  이번에는
  1) 성별(남자/여자)
  2. 나이에 따른 클릭/좋아요/장바구니/구매 비율을 알고 싶습니다. 나이의 경우 아래 기준으로 그룹을 나누고 진행하겠습니다.
  2-1) 17세 이하
  2-2) 18세 이상, 24세 이하
  2-3) 25세 이상, 35세 이하
  2-4) 36세 이상, 44세 이하
  2-5) 45세 이상, 54세 이하
  2-6) 55세 이상
  피벗 테이블을 활용해 성별 그리고 event_name기준으로 간단히 데이터를 확인하고 컬럼들을 세분화 해보겠습니다.
  ```python
  raw_data.pivot_table(index = 'gender', columns = 'event_name',\
                         values='age' ,aggfunc='count')
  ```
  ![https://blog.kakaocdn.net/dn/bQ3Wyk/btsfcj6KvII/rxSJIcqecMK9YdE2wHeBzK/img.png](https://blog.kakaocdn.net/dn/bQ3Wyk/btsfcj6KvII/rxSJIcqecMK9YdE2wHeBzK/img.png)
  이제 컬럼들을 나이대에 맞춰 세분화 해보겠습니다.
  ```python
  # 나이(age)가 17세 이하인 사용자를 찾아서 age(Group) 컬럼에 "00 ~ 17"이라는 값을 넣어줍니다
  raw_data.loc[raw_data['age'] <= 17, 'age(Group)'] = "00 ~ 17"

  # 위 방식에 따라 나이 설정 값으로 지정해줍니다
  raw_data.loc[(raw_data['age'] >= 18) &\
                 (raw_data['age'] <= 24), 'age(Group)'] = "18 ~ 24"

  raw_data.loc[(raw_data['age'] >= 25) &\
                 (raw_data['age'] <= 35), 'age(Group)'] = "25 ~ 35"

  raw_data.loc[(raw_data['age'] >= 36) &\
                 (raw_data['age'] <= 44), 'age(Group)'] = "36 ~ 44"

  raw_data.loc[(raw_data['age'] >= 45) &\
                 (raw_data['age'] <= 54), 'age(Group)'] = "45 ~ 54"

  raw_data.loc[raw_data['age'] >= 55, 'age(Group)'] = "55 ~ 99"

  # 데이터를 정리 후 아래 2개 컬럼 내용을 확인합니다
  raw_data[['age','age(Group)']]
  ```
  ![https://blog.kakaocdn.net/dn/77vwS/btsfA4faw04/9pk16ry18ugWnZvxHIBfHK/img.png](https://blog.kakaocdn.net/dn/77vwS/btsfA4faw04/9pk16ry18ugWnZvxHIBfHK/img.png)
  위 age(Group) 컬럼을 활용하여 성별과 나이대에 따른 클릭/좋아요/장바구니/구매 결과를 확인해보겠습니다.
  ![https://blog.kakaocdn.net/dn/bRm3H8/btsfehf1xGM/szfQEV8kQd9lWAMfeyt14K/img.png](https://blog.kakaocdn.net/dn/bRm3H8/btsfehf1xGM/szfQEV8kQd9lWAMfeyt14K/img.png)
  결과가 잘 확인 되었습니다. 이번에는 추가적으로 age(Group)에 따른 분포 상태와 구매 전환율을 확인해보겠습니다.
  ```python
  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  table['total'] = table['add_to_cart'] + table['click_item'] + table['like_item'] + table['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 purchase_success(구매) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  table['conversion'] = table['purchase_success'] / table['total']

  # 이 데이터프레임의 결과를 출력합니다
  table
  ```
  ![https://blog.kakaocdn.net/dn/JJHg0/btsfjIKEYeM/feZeZWndhK5sRiGbVVQNt1/img.png](https://blog.kakaocdn.net/dn/JJHg0/btsfjIKEYeM/feZeZWndhK5sRiGbVVQNt1/img.png)
  결과가 잘 확인되었습니다.
  분석 결과는 다음과 같습니다.
  - 가장 많은 양의 결제가 일어난 구간은 여성 25 ~ 35세입니다. 총 2334298개로, 결제 완료의 45% 이상이 이 구간에서 발생했습니다.
  - 또한 어느 정도 모수가 받쳐주는(결제 완료 10000회 이상) 그룹 중 이보다 전환율이 높은 그룹은 1) 남성 25 ~ 35세, 2) 남성 36 ~ 44세, 3) 여성 25 ~ 35세 입니다. 남성 25 ~35와 남성 36 ~ 44세 전환율 차이가 별로 나지 않기 때문에 1, 2등의 큰 의미가 없어 남성 25 ~ 44세까지 같은 그룹로 볼 수 있다.
  이런 상황에서, 데이터분석가는 퍼포먼스 마케터와 함께 다음의 아이디어를 제시하여 회사의 매출을 증대할 수 있습니다.
  (한 명의 고객을 데려오는데 필요한 비용, 줄여서 고객 획득 비용(Customer Acquision Cost, 이하 CAC))
  - 마케팅 예산을 남성 25 ~ 44세쪽에 집중한다. 이 그룹이 전환율이 높기 때문에, CAC가 여성 25 ~ 35세와 동일하다면 남성 25 ~ 44세에 마케팅 예산을 늘리는 것은 좋은 전략입니다.
  - 남성 25 ~ 44세 그룹의 CAC가 상대적으로 높다면, 이 CAC을 낮추는 시도를 합니다. 이 전략이 성공하면 그 후에 마케팅 예산을 집중하는 것도 방법입니다.
  - 현재 이용하고 있는 광고 채널을 다각화하여, 남성 25 ~ 44세가 활동하는 곳에 집중적으로 마케팅 예산을 투입하는 것도 시도해볼만 합니다.
- 2-3
  이번에는 시간 정보를 기준으로 클릭/ 좋아여 / 장바구니/ 구매 비율을 알아보겠습니다. 크게 다음의 상황에 따른 구매 여부를 알고 싶습니다.
  1. 시간별 구매 현황(0시 ~ 23시)
  2. 요일별 구매 현황(월요일 ~ 일요일)
  이제 시간대별 비율을 확인해보겠습니다.
  ```python
  # pivot_table을 사용하여 index(세로)에는 event_timestamp_hour를 넣어주고,# Columns(가로)에는 event_name 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 를 넣어주면 click_item, add_to_cart, like_item, purchase_success 누적 개수가 나옵니다
  table_by_hours = pd.pivot_table(raw_data,
                                  index='event_timestamp_hour',
                                  columns='event_name',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  table_by_hours['total'] = table_by_hours['add_to_cart'] + table_by_hours['click_item'] + table_by_hours['like_item'] + table_by_hours['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 completed(결제 완료) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  table_by_hours['purchase_success'] = table_by_hours['purchase_success'] / table_by_hours['total']

  # 결과를 확인합니다
  table_by_hours
  ```
  ![https://blog.kakaocdn.net/dn/u8nPB/btsfA3HkEx3/Vy8LS8IgHFMLm9RghIO1R1/img.png](https://blog.kakaocdn.net/dn/u8nPB/btsfA3HkEx3/Vy8LS8IgHFMLm9RghIO1R1/img.png)
  위 데이터를 기반으로 전환율을 시각화 해보겠습니다.
  ```python
  # 그래프 전체 사이즈 설정합니다
  plt.figure(figsize=[20,5])

  # y축의 최소/최대값을 0/0.2로 고정합니다
  plt.ylim(0, 0.2)

  # 시간대별 전환율(conversion)을 시각화 합니다
  sns.pointplot(data = table_by_hours.reset_index(), x='event_timestamp_hour', y='conversion')
  ```
  ![https://blog.kakaocdn.net/dn/WBNxU/btsfdbOaku6/1W2hmLDEPbgJoG50yknze0/img.png](https://blog.kakaocdn.net/dn/WBNxU/btsfdbOaku6/1W2hmLDEPbgJoG50yknze0/img.png)
  ```python
  # 그래프 전체 사이즈 설정합니다
  plt.figure(figsize=[20,5])

  # 시간대별 구매(purchase_success)를 시각화 합니다
  sns.pointplot(data = table_by_hours.reset_index(), x='event_timestamp_hour', y='purchase_success')
  ```
  ![https://blog.kakaocdn.net/dn/bfUfPy/btsfbz2SHze/x6tQvz5rizcAdrLhKfjn61/img.png](https://blog.kakaocdn.net/dn/bfUfPy/btsfbz2SHze/x6tQvz5rizcAdrLhKfjn61/img.png)
  분석 결과는 다음과 같습니다.
  - 아쉽게도, 구매 시간별 전환율(conversion)은 큰 차이가 없어 보입니다, 그 의미는 특정 시간대에 구매한 사용자들이 서비스를 이탈할 확률이 높아지거나 낮아지는 현상은 없다고 볼 수 있습니다.
  - 다만 전환율과는 별개로, 주로 점심시간(10시 ~ 12시)나 저녁(20시 ~ 24시)에 구매량이 대폭 늘어난다는 것을 알 수 있습니다. 만일 광고 예산을 집행한다면 이 시기에 집중적으로 집행하거나, 전환을 유도하는 모바일 노티피케이션을 보냄으로써 전환율을 높이는 것은 시도해볼만 합니다.
  이번에는 요일별 클릭/ 좋아여 / 장바구니/ 구매 비율을 확인해보겠습니다.
  요일 컬럼을 생성해줍니다.
  ```python
  raw_data['event_timestamp'] = pd.to_datetime(raw_data['event_timestamp'])
  raw_data['event_timestamp(weekday)'] = raw_data['event_timestamp'].dt.day_name()# 요일 확인용 컬럼 생성
  raw_data[['event_timestamp(weekday)', 'event_timestamp']].head()
  ```
  ![https://blog.kakaocdn.net/dn/oPrxl/btsfegg93fJ/w9rOeYb7LYVoKYLO6kM7kk/img.png](https://blog.kakaocdn.net/dn/oPrxl/btsfegg93fJ/w9rOeYb7LYVoKYLO6kM7kk/img.png)
  요일을 확인할 수 있는 컬럼은 생성되었고 이제 요일별 비율을 확인해보겠습니다.
  ```python
  # pivot_table을 사용하여 index(세로)에는 event_timestamp(weekday)를 넣어주고,# Columns(가로)에는 event_name 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 를 넣어주면 click_item, add_to_cart, like_item, purchase_success 누적 개수가 나옵니다
  table_by_week = pd.pivot_table(raw_data,
                                  index='event_timestamp(weekday)',
                                  columns='event_name',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  table_by_week['total'] = table_by_week['add_to_cart'] + table_by_week['click_item'] + table_by_week['like_item'] + table_by_week['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 completed(결제 완료) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  table_by_week['conversion'] = table_by_week['purchase_success'] / table_by_week['total']

  # 컬럼 정렬을 위해 새로운 변수를 생성합니다
  week_columns = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']

  # 결과를 확인합니다
  table_by_week.loc[week_columns]
  ```
  ![https://blog.kakaocdn.net/dn/vXhiS/btsfdWwugZN/sE4ndT9abgtKdDw4QODmfk/img.png](https://blog.kakaocdn.net/dn/vXhiS/btsfdWwugZN/sE4ndT9abgtKdDw4QODmfk/img.png)
  위 데이터를 기반으로 전환율을 시각화 해보겠습니다.
  ```python
  # 가로를 20인치, 세로를 5인치로 조절합니다
  plt.figure(figsize=[20,5])

  # y축의 최소/최대값을 0/0.2로 고정합니다
  plt.ylim(0, 0.2)

  # 이전에 피벗테이블로 분석한 결과를 pointplot으로 시각화합니다# X축은 구매 요일(event_timestamp(weekday)))을, y축은 전환율(conversion)을 측정합니다
  sns.pointplot(data=table_by_week.reset_index(), x='event_timestamp(weekday)',\
                y='conversion', order = week_columns)
  ```
  ![https://blog.kakaocdn.net/dn/CZZfD/btsfCtlIkHy/90O4Xgdrc2JCUxemmNR76k/img.png](https://blog.kakaocdn.net/dn/CZZfD/btsfCtlIkHy/90O4Xgdrc2JCUxemmNR76k/img.png)
  ```python
  # 가로를 20인치, 세로를 5인치로 조절합니다.
  plt.figure(figsize=[20,5])

  # 이전에 피벗테이블로 분석한 결과를 pointplot으로 시각화합니다# X축은 구매 요일(event_timestamp(weekday))을, y축은 구매 완료 (purchase_success)를 측정합니다
  sns.pointplot(data=table_by_week.reset_index(), x='event_timestamp(weekday)',\
                y='purchase_success', order = week_columns)
  ```
  ![https://blog.kakaocdn.net/dn/VOAtN/btsfpdYaDb1/OGkyxHcn6YeQ54WFKON6A0/img.png](https://blog.kakaocdn.net/dn/VOAtN/btsfpdYaDb1/OGkyxHcn6YeQ54WFKON6A0/img.png)
  분석 결과는 다음과 같습니다.
  - 구매 시간과 마찬가지로, 구매 요일별 전환율(conversion)은 큰 차이가 없어 보입니다. 어느 요일이나 마찬가지로, 구매한 사람이 서비스를 이탈하거나 남을 확률은 거의 동일합니다.
  - 하지만 사용자들은 전반적으로 수-토요일 이 다가올수록 구매를 덜 하게되고, 일-화요일이 다가올수록 구매를 많이 하게 되는 현상을 발견할 수 있습니다. 이 시기에 광고 예산을 크게 집행하거나, 구매를 유도하는 모바일 노티피케이션을 보내는 것은 좋은 아이디어입니다.
- 2-4
  이번에는 카테고리별 클릭/ 좋아여 / 장바구니/ 구매 비율을 알아 보겠습니다.
  ```python
  raw_data['category1_name'].value_counts()
  ```
  ![https://blog.kakaocdn.net/dn/pW478/btsfA3tO4dy/aEDYrjkOvqUOf3buJ1mfm1/img.png](https://blog.kakaocdn.net/dn/pW478/btsfA3tO4dy/aEDYrjkOvqUOf3buJ1mfm1/img.png)
  ```python
  raw_data['category2_name'].value_counts()
  ```
  ![https://blog.kakaocdn.net/dn/3M5ko/btsfCtsuNzQ/fy0o6wqgv3pxAilmhO4BW0/img.png](https://blog.kakaocdn.net/dn/3M5ko/btsfCtsuNzQ/fy0o6wqgv3pxAilmhO4BW0/img.png)
  ```python
  raw_data['category3_name'].value_counts()
  ```
  ![https://blog.kakaocdn.net/dn/sLbdg/btsfckq5ALZ/IdA2VZKTlEdJasXtEpd1zk/img.png](https://blog.kakaocdn.net/dn/sLbdg/btsfckq5ALZ/IdA2VZKTlEdJasXtEpd1zk/img.png)
  카테고리3은 726개로 너무 많은 카테고리를 가지고있다. 카테고리1과 카테고리2중에서 좀더 세분화가 가능한 2로 진행하겠습니다.
  카테고리2으로 클릭/ 좋아여 / 장바구니/ 구매 비율을 알아 보겠습니다.
  ```python
  # pivot_table을 사용하여 index(세로)에는 category2_name 넣어주고,# Columns(가로)에는 event_name 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 를 넣어주면 click_item, add_to_cart, like_item, purchase_success 누적 개수가 나옵니다
  table_by_cat2 = pd.pivot_table(raw_data,
                                  index='category2_name',
                                  columns='event_name',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  table_by_cat2['total'] = table_by_cat2['add_to_cart'] + table_by_cat2['click_item'] + table_by_cat2['like_item'] + table_by_cat2['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 completed(결제 완료) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  table_by_cat2['conversion'] = table_by_cat2['purchase_success'] / table_by_cat2['total']

  # 결과를 확인합니다
  table_by_cat2.sort_values(by='conversion', ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/dM2gXM/btsfeAUfw10/zQR0OGIeaun8dxK9M1nPkk/img.png](https://blog.kakaocdn.net/dn/dM2gXM/btsfeAUfw10/zQR0OGIeaun8dxK9M1nPkk/img.png)
  데이터 자체가 낮은 값들이 많아 purchase_success 4000 이상으로 설정하여 전체 데이터의 비해 얼마나 되는지 확인해보겠습니다.
  ```python
  table_suc_4000 = table_by_cat2[table_by_cat2['purchase_success'] >= 4000]
  table_suc_4000['purchase_success'].sum() / table_by_cat2['purchase_success'].sum()
  ```
  ![https://blog.kakaocdn.net/dn/p8h1J/btsfpevZz4n/Aql1yUSKDaQyG4H8F1L2AK/img.png](https://blog.kakaocdn.net/dn/p8h1J/btsfpevZz4n/Aql1yUSKDaQyG4H8F1L2AK/img.png)
  purchase_success 4000 이상으로 해도 84%의 데이터 이기 때문에 전처리 하여 데이터를 사용하겠습니다.
  conversion 높은것들 부터 순서대로 보겠습니다.
  ```python
  table_suc_4000.sort_values(by='conversion', ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/ZtHBE/btsfOKUUd5s/DxzgSfGHjuhwjIWqWlhO4K/img.png](https://blog.kakaocdn.net/dn/ZtHBE/btsfOKUUd5s/DxzgSfGHjuhwjIWqWlhO4K/img.png)
  이 결과를 통해 알 수 있는 정보는 다음과 같습니다.
  - 현재 가장 많은 구매가 일어나는 카테고리는 상의 입니다. 전체 구매중 약 35%입니다
  - 구매량이 10000회 이상인 카테고리 중 가장 전환율이 높은 카테고리는 언더웨어 입니다.
  - 아직 구매량등 모수가 많지는 않지만 전환율이 가장높은 티켓 카테고리를 살펴보면 전시/행사, 상품권, 콘서트로 티켓을 구매하는 고객들은 확실한 목적으로 앱을 사용한 고객들로 알 수 있습니다. 그러므로 확실한 티켓에 대한 이벤트가 있을때 모바일 노티피케이션을 한다면 효과가 클것으로 예상됩니다.
  - 상의, 하의, 원피스/점프슈트 등 의류 카테고리의 있는 상품들은 상품 전체 개수가 많아 전환율 자체가 높지 않다는건 이상하지 않습니다. 다만 이러한 카테고리를 가진 브랜드별로 전환율을 본다면 조금더 좋은 인사이트를 얻을수 있을것 같습니다.
  ###
  다음은 브랜드별 클릭/ 좋아여 / 장바구니/ 구매 비율을 알아 보겠습니다.
  ```python
  # pivot_table을 사용하여 index(세로)에는 brand_name 넣어주고,# Columns(가로)에는 event_name 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 를 넣어주면 click_item, add_to_cart, like_item, purchase_success 누적 개수가 나옵니다
  table_by_brand = pd.pivot_table(raw_data,
                                  index='brand_name',
                                  columns='event_name',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  table_by_brand['total'] = table_by_brand['add_to_cart'] + table_by_brand['click_item'] + table_by_brand['like_item'] + table_by_brand['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 completed(결제 완료) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  table_by_brand['conversion'] = table_by_brand['purchase_success'] / table_by_brand['total']

  # 결과를 확인합니다
  table_by_brand.sort_values(by='total', ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/xwV9X/btsfCt0pAo2/rewhnKpWI2tJlGlKlrkg1k/img.png](https://blog.kakaocdn.net/dn/xwV9X/btsfCt0pAo2/rewhnKpWI2tJlGlKlrkg1k/img.png)
  작은 데이터들이 너무 많아 기준을 잡기위래 total에 히스토그램을 그려봅니다
  ```python
  plt.figure(figsize=[20, 5])
  sns.distplot(table_by_brand['total'])
  ```
  ![https://blog.kakaocdn.net/dn/bF1JEY/btsfeByUU3K/nZy4L4tyLlZl71q4YCfHS0/img.png](https://blog.kakaocdn.net/dn/bF1JEY/btsfeByUU3K/nZy4L4tyLlZl71q4YCfHS0/img.png)
  한쪽으로 너무 몰려있는 데이터를 확인했습니다.
  표준편차, 중위값, 평균을 구합니다.
  ```python
  print(table_by_brand['total'].std())
  print(table_by_brand['total'].median())
  print(table_by_brand['total'].mean())
  ```
  ![https://blog.kakaocdn.net/dn/dd4qRr/btsfghtcnGQ/6vmKAmRK2JnO5RaUJ2eyhK/img.png](https://blog.kakaocdn.net/dn/dd4qRr/btsfghtcnGQ/6vmKAmRK2JnO5RaUJ2eyhK/img.png)
  평균과 표준편차를 이용하여 total > 3500 초과 데이터만 사용하겠습니다.
  ```python
  brand_total_3500 = table_by_brand[table_by_brand['total'] > 3500].sort_values(by='conversion', ascending=False)
  brand_total_3500
  ```
  ![https://blog.kakaocdn.net/dn/bvubKw/btsfcN0TSv0/Ekg1RCTES9pLKdUjHwckYk/img.png](https://blog.kakaocdn.net/dn/bvubKw/btsfcN0TSv0/Ekg1RCTES9pLKdUjHwckYk/img.png)
  여기서 저희에게 중요한것은 구매량이기 때문에 다시한번 구매량 데이터를 가지고 통계적 접근을 해보겠습니다.
  표준편차, 중위값, 평균을 구합니다.
  ```python
  print(brand_total_3500['purchase_success'].std())
  print(brand_total_3500['purchase_success'].median())
  print(brand_total_3500['purchase_success'].mean())
  ```
  ![https://blog.kakaocdn.net/dn/HfGgL/btsftKnXf2A/65hhzRbU5pY98IG8Xwv9q0/img.png](https://blog.kakaocdn.net/dn/HfGgL/btsftKnXf2A/65hhzRbU5pY98IG8Xwv9q0/img.png)
  평균과 표준편차를 이용하여 purchase_success > 1500 초과 데이터만 사용하겠습니다.
  purchase_success > 1500 데이터중 구매 전환율이 20%가 넘는 브랜드만 보겠습니다.
  ```python
  brand_total_3500[brand_total_3500['purchase_success'] > 1500].sort_values(by='conversion', ascending=False)[:11]
  ```
  ![https://blog.kakaocdn.net/dn/KAROs/btsfda2VrNe/6zKxz90TKAvx3kg6afRcQ0/img.png](https://blog.kakaocdn.net/dn/KAROs/btsfda2VrNe/6zKxz90TKAvx3kg6afRcQ0/img.png)
  분석 결과는 다음과 같습니다.
  - 의류 같은 경우 카테고리별 전환율이 10%미만인걸로 봐서 UNLIMIT, FLUKE, MINIMAL PROJECT, ontz, ALVINCLO, Mardi Mercredi 브랜드들의 브랜드파워는 엄청난것으로 보입니다.
  - 그라운드 시소, The Ordinary, 1507, TAW&TOE\_이누, 락앤락 같은 경우 각 카테고리에서 최고의 브랜드 파워를 가지고있는것으로 파악됩니다.
  - 해당 브랜드들과 프로모션 같은 이벤트들을 진행한다면 매출을 높여줄것으로 기대됩니다.
  현재까지 데이터들을 종합한 결과는 다음과 같습니다
  - 전환율이 가장 높은 남성 25 ~ 44 그룹에 마케팅 예산을 집중하는것은 좋은 전략으로 보입니다
  - 주로 점심시간(10시 ~ 12시)나 저녁(20시 ~ 24시)에 구매량이 대폭 늘어나니 광고 예산을 집행한다면 이 시기에 집중적으로 집행하거나, 전환을 유도하는 모바일 노티피케이션을 보냄으로써 전환율을 높이는 것은 시도해볼만 합니다.
  - 일-화요일이 다가올수록 구매를 많이 하게 되는 현상을 발견 됩니다. 이 시기에 광고 예산을 크게 집행하거나, 구매를 유도하는 모바일 노티피케이션을 보내는 것은 좋은 아이디어 인것 같습니다.
  - 각 카태고리별 전환율이 높은 브랜드들과 프로모션 전략을 한다면 좋은 효과를 기대할 수 있을것같습니다.
  ※ 25 ~ 44세 그룹에게 일~화요일, 20~24시에, 관심을 가지는 카테고리의 파워를 가진 브랜드의 이벤트를 모바일 노티피케이션을 보낸다
- 2-5
  본격적으로 데이터 분석을 하기 전에 LTV가 무엇이고 어떻게 계산하는지 세 줄로 말씀드리겠습니다.
  1. LTV는 고객이 평생동안 기업에게 어느 정도의 금전적 가치를 가져다 주는지를 정량화한 지표로, **고객별 미래의 예상 구매 횟수 \* 예상 평균 수익**으로 계산됩니다.
  2. **미래의 예상 구매 횟수**는 BG/NBD 모형을 통해 산출되며, 이를 위해 R (Recency), F (Frequency), T (Time) 정보가 필요합니다.
  3. **미래의 예상 평균 수익**은 Gamma-Gamma 모형을 통해 산출되며, 이를 위해 F (Frequency), M (Monetary Value) 정보가 필요합니다.
  그래서 저희는 구매 기록 데이터의 R, F, M, T를 구해 BG/NBD 모형과 Gamma-Gamma 모형을 각각 적합해 LTV를 구할 것입니다.
  구매 데이터만 필요하므로 구매 데이터만을 불러오고 시간데이터 타입을 datetime형태로 바꿔주겠습니다.
  또한 필요한 컬럼만 불러와서 사용하겠습니다.
  ```python
  suc_data = raw_data[raw_data['event_name'] == 'purchase_success']# 전체고객
  ltv_data = suc_data[['user_no', 'event_timestamp', 'price']]
  ltv_data['event_timestamp'] = pd.to_datetime(ltv_data['event_timestamp']).dt.date
  ltv_data
  ```
  ![https://blog.kakaocdn.net/dn/QQjj9/btsglAkClEw/Aavkq45GY2KLHgiivqM650/img.png](https://blog.kakaocdn.net/dn/QQjj9/btsglAkClEw/Aavkq45GY2KLHgiivqM650/img.png)
  이제 다음에 할 일은 BG/NBD 모형과 Gamma-Gamma 모형의 INPUT이 되는 R, F, M, T를 만드는 일입니다.
  이는 lifetimes의 summary_data_from_transaction_data 함수를 이용해 쉽게 만들 수 있습니다.
  ```python
  current_date = ltv_data['event_timestamp'].max()

  metrics_df = summary_data_from_transaction_data(ltv_data
                                            , customer_id_col = 'user_no'
                                            , datetime_col = 'event_timestamp'
                                            , monetary_value_col='price'
                                            , observation_period_end=current_date)
  metrics_df.head()
  ```
  ![https://blog.kakaocdn.net/dn/bj90LF/btsgjl3bEIH/7YKlvTJMYxzOIoZlrH9Sgk/img.png](https://blog.kakaocdn.net/dn/bj90LF/btsgjl3bEIH/7YKlvTJMYxzOIoZlrH9Sgk/img.png)
  각 컬럼에 대한 설명은 다음과 같습니다.
  | frequency      | F   | 고객별 구매 일수                        |
  | -------------- | --- | --------------------------------------- |
  | recency        | R   | 고객별 첫 구매 ~ 마지막 구매까지의 시간 |
  | T              | T   | 고객별 첫 구매 ~ 집계일까지의 시간      |
  | monetary_value | M   | 고객별 평균 구매 금액                   |
  # [최적의 L2 penalty 파라미터 찾기](https://playinpap.github.io/ltv-practice/#%EC%B5%9C%EC%A0%81%EC%9D%98-l2-penalty-%ED%8C%8C%EB%9D%BC%EB%AF%B8%ED%84%B0-%EC%B0%BE%EA%B8%B0)
  이제 고객 각각의 RFMT를 계산했으니 이 데이터를 BG/NBD 모형과 Gamma-Gamma 모형의 INPUT으로 넣어주면 됩니다.
  그런데 모형을 적합시킬 때 다음과 같이 penalizer_coef 인자에 **L2 penalty**를 넣어준다면 모형을 좀 더 강건하게 만들어줄 수 있습니다.
  ```python
  l2_reg = 0.01# lifetime 공식 홈페이지에서 0.1 ~ 0.001사이값을 넣어주는게 효과적이라고 한다
  model = BetaGeoFitter(penalizer_coef=l2_reg)
  model = GammaGammaFitter(penalizer_coef=l2_reg)
  ```
  L2 penalty의 계수를 어떻게 넣어줘야 최적인지 파라미터를 찾는 과정이 필요합니다.
  lifetimes 패키지에서는 훈련 데이터를 **calibration data** / 테스트 데이터를 **holdout data**라 부릅니다.
  그럼 어느 시점을 전후로 calibration과 holdout으로 나눌 것이냐? 에 대해선 정해진게 없습니다.
  그러므로 저희는 63일 데이터이기 때문에 15일로 나누어 계산하겠습니다.
  ```python
  holdout_days = 15# 총일수 63일
  calibration_end_date = current_date - timedelta(days = holdout_days)

  metrics_cal_df = calibration_and_holdout_data(ltv_data
                                            ,customer_id_col = 'user_no'
                                            ,datetime_col = 'event_timestamp'
                                            ,calibration_period_end=calibration_end_date# train 데이터 기간
                                            ,observation_period_end=current_date# 끝 기간
                                            ,monetary_value_col='price')

  metrics_cal_df.head()
  ```
  ![https://blog.kakaocdn.net/dn/bogJEy/btsgC2H7ali/oYyaOMz2Ww5XVxkzXmjZ7k/img.png](https://blog.kakaocdn.net/dn/bogJEy/btsgC2H7ali/oYyaOMz2Ww5XVxkzXmjZ7k/img.png)
  - calibration 기간 동안의 RFMT를 계산한 값은 _cal이 붙고, holdout 기간 동안의 F와 M을 계산한 값은 _holdout이 붙어 있는걸 볼수있습니다.
  - duration_holdout은 holdout data이 며칠인지 나타내는 컬럼입니다.
  이렇게 하면 BG/NBD 모형과 Gamma-Gamma 모형의 INPUT을 정리할 수 있습니다.
  frequency가 0인 것은 제외하고 가져와야 합니다. 이 고객들은 전체 기간 동안 구매 일수가 1일인 유저입니다. (frequency는 총 구매일수 - 1) 이들은 **“반복적인” 구매를 한 고객들이 아니라서 BG/NBD 모형 가정에서 벗어난 고객들이기 때문에** 제외하고 적합해야 합니다.
  ```python
  ## frequency가 0인 것은 제외하기
  whole_filtered_df = metrics_df[metrics_df.frequency > 0]
  filtered_df       = metrics_cal_df[metrics_cal_df.frequency_cal > 0]
  ```
  - whole_filtered_df는 L2 페널티를 최적화한 후에 제일 마지막에 LTV를 계산할 때 쓸 데이터이고 (calibration / holdout을 나누지 않은 데이터)
  - filtered_df는 L2 페널티를 최적화하기 위해 calibration / holdout을 나눈 데이터입니다.
  이제 훈련과 테스트 데이터를 나눴으니 L2 페널티를 최적화할 차례입니다. 이를 위해 세 가지 함수를 정의합니다.
  - score_model: 실제값과 예측값의 차이에 대한 지표 (MSE / RMSE/ MAE)를 계산하는 함수
  - evaluate_bgnbd_model: calibration data와 l2_reg를 넣어 **BG/NBD 모형**을 적합시키고, holdout data의 구매 일수 (frequency)에 대한 실제값과 예측값에 대한 MSE를 계산하는 함수
  - evaluate_gg_model: calibration data와 l2_reg를 넣어 **Gamma-Gamma 모형**을 적합시키고, holdout data의 평균 구매 금액 (monetary value)에 대한 실제값과 예측값에 대한 MSE를 계산하는 함수
  ```python
  # 평가 지표: default는 MSEdef score_model(actuals, predicted, metric='mse'):

      metric = metric.lower()

  # MSE / RMSEif metric=='mse' or metric=='rmse':
          val = np.sum(np.square(actuals-predicted))/actuals.shape[0]
      elif metric=='rmse':
          val = np.sqrt(val)
  # MAEelif metric=='mae':
          val = np.sum(np.abs(actuals-predicted))/actuals.shape[0]
      else:
          val = None

      return val

  # BG/NBD 모형 평가def evaluate_bgnbd_model(param):

      data   = inputs
      l2_reg = param

  # 모형 적합
      model = BetaGeoFitter(penalizer_coef=l2_reg)
      model.fit(data['frequency_cal'], data['recency_cal'], data['T_cal'])

  # 모형 평가
      frequency_actual = data['frequency_holdout']
      frequency_predicted = model.predict(data['duration_holdout']
                                          , data['frequency_cal']
                                          , data['recency_cal']
                                          , data['T_cal']
                                         )
      mse = score_model(frequency_actual, frequency_predicted, 'mse')

      return {'loss': mse, 'status': STATUS_OK}

  # Gamma/Gamma 모델 평가def evaluate_gg_model(param):

      data   = inputs
      l2_reg = param

  # GammaGamma 모형 적합
      model = GammaGammaFitter(penalizer_coef=l2_reg)
      model.fit(data['frequency_cal'], data['monetary_value_cal'])

  # 모형 평가
      monetary_actual = data['monetary_value_holdout']
      monetary_predicted = model.conditional_expected_average_profit(data['frequency_holdout'], data['monetary_value_holdout'])
      mse = score_model(monetary_actual, monetary_predicted, 'mse')

  # return score and statusreturn {'loss': mse, 'status': STATUS_OK}
  ```
  이 후 hyperopt 모듈의 fmin 함수를 이용해 BG/NBD 모형과 Gamma-Gamma 모형 각각의 최적의 L2 penalty를 찾습니다.
  ```python
  search_space = hp.uniform('l2', 0.0, 1.0)
  algo = tpe.suggest
  trials = Trials()
  inputs = filtered_df

  argmin = fmin(
    fn = evaluate_bgnbd_model,# 목적함수
    space = search_space,# 파라미터 공간
    algo = algo,# 최적화 알고리즘: Tree of Parzen Estimators (TPE)
    max_evals=100,# 반복수
    trials=trials
    )

  l2_bgnbd = space_eval(search_space,argmin)
  print(l2_bgnbd)
  ```
  ![https://blog.kakaocdn.net/dn/bObYEA/btsgCpRprmM/St6i4fFVvAKjU0nKMrLxJK/img.png](https://blog.kakaocdn.net/dn/bObYEA/btsgCpRprmM/St6i4fFVvAKjU0nKMrLxJK/img.png)
  BG/NBD의 penalizer_coef = 0.0013637185037294174
  ```python
  search_space = hp.uniform('l2', 0.0, 1.0)
  algo = tpe.suggest
  trials = Trials()
  inputs = filtered_df

  # GammaGamma
  argmin = fmin(
    fn = evaluate_gg_model,
    space = search_space,
    algo = algo,
    max_evals=100,
    trials=trials
    )

  l2_gg = space_eval(search_space,argmin)
  print(l2_gg)
  ```
  ![https://blog.kakaocdn.net/dn/bA1H63/btsgCo58i4j/uMyfaMMIbKJLU5Ha2zHeNK/img.png](https://blog.kakaocdn.net/dn/bA1H63/btsgCo58i4j/uMyfaMMIbKJLU5Ha2zHeNK/img.png)
  Gamma-Gamma의 penalizer_coef = 0.0004892556801810725
  이제 최적의 L2 penalty를 찾았으니 이를 이용해 BG/NBD 모형과 Gamma-Gamma 모형을 적합할 차례입니다! 최적의 L2 penalty를 넣었을 때 holdout 데이터로 각 모형의 MSE를 구해보겠습니다.
  - 최적의 L2 penalty l2_bgnbd를 넣고, calibration data로 모형을 적합시킨 후
  - holdout data로 frequency의 실제값과 예측값을 비교해 MSE를 계산합니다.
  ```python
  lifetimes_model = BetaGeoFitter(penalizer_coef=l2_bgnbd)#l2_bgnbd = hyperopt로 나온 결과# calibration 데이터의 R,F,T로 모형 적합
  lifetimes_model.fit(filtered_df['frequency_cal'], filtered_df['recency_cal'], filtered_df['T_cal'])

  # holdout 데이터로 모델 평가: F의 실제값과 예측값의 MSE
  frequency_actual = filtered_df['frequency_holdout']
  frequency_predicted = lifetimes_model.predict(filtered_df['duration_holdout']
                                      ,filtered_df['frequency_cal']
                                      , filtered_df['recency_cal']
                                      , filtered_df['T_cal'])
  mse = score_model(frequency_actual, frequency_predicted, 'mse')
  print('MSE: {0}'.format(mse))
  ```
  ![https://blog.kakaocdn.net/dn/WdNJO/btsgDK8gqNk/uIGw3GIfPY060zFJOVtTrK/img.png](https://blog.kakaocdn.net/dn/WdNJO/btsgDK8gqNk/uIGw3GIfPY060zFJOVtTrK/img.png)
  MSE는 0.57 정도네요. 구매 일수에 대한 평균 제곱 오차가 +- 0.5일 정도 됨을 알 수 있습니다.
  lifetimes_model을 통해 BG/NBD 모형에서 추정된 파라미터들을 확인할 수 있습니다.
  ```python
  lifetimes_model.summary
  ```
  ![https://blog.kakaocdn.net/dn/bYL5J9/btsgmHDDvJ4/vVKkTyWgyGO52dSVo6vKrK/img.png](https://blog.kakaocdn.net/dn/bYL5J9/btsgmHDDvJ4/vVKkTyWgyGO52dSVo6vKrK/img.png)
  BG/NBD 모형은 다음과 같은 가정을 하고 있습니다.
  1. 고객이 남아있는 동안, 일정한 기간 T 동안의 구매 횟수는 Pois(λT)를 따릅니다. 1일 간 Pois (1/12)를 따른다면 T= 1년일 경우 Pois (30)을 따르게 됩니다 (포아송의 모수인 λT는 **예상 구매 횟수** 를 의미합니다)
  2. 고객마다 일정한 기간 동안 구매하는 횟수는 다릅니다. 이는 λ ~ Gamma (r,α)을 따릅니다.
  3. j번째 구매가 마지막이고 더 이상 구매를 하지 않을 확률 (이탈률)은 p입니다. 이탈할 때까지의 구매 횟수는 Geo (p)를 따릅니다.
  4. 고객마다 더 이상 구매를 하지 않을 확률 (이탈률)은 다릅니다. 이탈률 p는 p ~ Beta (a,b)를 따릅니다.
  5. 고객별 일정 기간 동안의 구매 횟수와 구매를 하지 않을 확률은 서로 영향을 주지 않습니다.
  이를 활용해 **고객마다 일정한 기간 동안 구매하는 횟수의 분포**와 **고객별 더 이상 구매하지 않을 확률의 분포**도 그려 보겠습니다.
  먼저, 고객마다 일정한 기간 동안 구매하는 횟수 λ는 Gamma(r,alpha)을 따릅니다.
  ```python
  # 고객별 lambda (구매율) 의 분포from scipy.stats import gamma
  coefs = lifetimes_model.summary['coef']
  x = np.linspace (0, 0.5, 100)
  y = gamma.pdf(x, a=coefs['r'], scale=1/coefs['alpha'])# BG/NBD에서의 모수 alpha는 scale 모수가 아닌 rate 모수이므로 역수!

  plt.plot(x, y)
  ```
  ![https://blog.kakaocdn.net/dn/EDIf6/btsgiBSvz1a/qBXUaFMJNyB59qFtZe01g0/img.png](https://blog.kakaocdn.net/dn/EDIf6/btsgiBSvz1a/qBXUaFMJNyB59qFtZe01g0/img.png)
  여기서 T 기간동안의 평균 구매 횟수가 λT 이므로 λ에 대한 분포는 1일 단위입니다.
  위 그림에서 0.25쯤부터 확률이 0으로 수렴하고 있는데 대부분 고객들이 1일의 평균 구매 횟수는 최대 0.25 정도임을 의미합니다. 즉, 최대 4일에 한 번 꼴로 구매한다라 볼 수도 있겠습니다.
  두 번째로, 고객이 더 이상 구매를 하지 않을 확률은 Beta(a,b)를 따릅니다.
  ```python
  # 고객별 구매하지 않을 분포from scipy.stats import gamma
  from scipy.stats import beta
  coefs = lifetimes_model.summary['coef']
  x = np.linspace (0, 2, 100)
  y = beta.pdf(x, a=coefs['a'], b=coefs['b'])

  plt.plot(x, y)
  ```
  ![https://blog.kakaocdn.net/dn/xL527/btsgFegsHdo/vkWKTD7KKTMtuSfU3unq80/img.png](https://blog.kakaocdn.net/dn/xL527/btsgFegsHdo/vkWKTD7KKTMtuSfU3unq80/img.png)
  분포가 0이 1보다 많음을 알 수 있습니다. 즉 더 이상 구매를 하지 않을 확률이 0인 active한 유저가 확률이 1 근처인 inactive한 유저 보다 많음을 알 수 있습니다.
  이제 “평균 구매 금액”을 모델링하기 위한 Gamma-Gamma 모형을 적합해봅시다.
  이를 위해 BG/NBD 모형때와 마찬가지로
  - 최적의 L2 penalty l2_gg를 넣고, calibration data로 모형을 적합시킨 후
  - holdout data로 monetary value의 실제값과 예측값을 비교해 MSE를 계산합니다.
  ```python
  spend_model = GammaGammaFitter(penalizer_coef=l2_gg)
  spend_model.fit(filtered_df['frequency_cal'], filtered_df['monetary_value_cal'])

  # conditional_expected_average_profit: 고객별 평균 구매 금액 예측
  monetary_actual = filtered_df['monetary_value_holdout']
  monetary_predicted = spend_model.conditional_expected_average_profit(filtered_df['frequency_holdout']
                                                                      ,filtered_df['monetary_value_holdout'])

  mse = score_model(monetary_actual, monetary_predicted, 'mse')

  print('MSE: {0}'.format(mse))
  ```
  ![https://blog.kakaocdn.net/dn/8GnS4/btsgE8gkPbk/wvf8UyyN31vkB6jpAXZf8K/img.png](https://blog.kakaocdn.net/dn/8GnS4/btsgE8gkPbk/wvf8UyyN31vkB6jpAXZf8K/img.png)
  평균 구매 금액의 평균 제곱 오차는 9,513,426원 정도 된다고 나옵니다.
  히스토그램을 통해 실제와 예측된 평균 구매 금액이 어떻게 차이가 나는지 확인해봅시다.
  ```python
  bins = 100
  plt.figure(figsize=(15, 5))

  plt.hist(monetary_actual, bins, label='actual', histtype='bar', color='STEELBLUE', rwidth=0.99)
  plt.hist(monetary_predicted, bins, label='predict', histtype='step', color='ORANGE',  rwidth=0.99)
  plt.legend(loc='upper right')
  ```
  ![https://blog.kakaocdn.net/dn/EiOPR/btsgEDnlSsC/k9scZ6hNBG3MGprjWuIJiK/img.png](https://blog.kakaocdn.net/dn/EiOPR/btsgEDnlSsC/k9scZ6hNBG3MGprjWuIJiK/img.png)
  파란 막대가 실제값, 주황 투명 막대가 예측값입니다. 어느 정도 분포가 비슷함을 볼 수 있습니다.
  만약 penalizer_coef를 주지 않고 예측할 경우 어떤지 살펴 보겠습니다.
  ```python
  # penalizer_coef 없이 했을 때의 결과
  spend_model = GammaGammaFitter(penalizer_coef=0)
  spend_model.fit(filtered_df['frequency_cal'], filtered_df['monetary_value_cal'])

  # 평가
  monetary_actual = filtered_df['monetary_value_holdout']
  monetary_predicted = spend_model.conditional_expected_average_profit(filtered_df['frequency_holdout']
                                                                      ,filtered_df['monetary_value_holdout'])

  mse = score_model(monetary_actual, monetary_predicted, 'mse')

  bins = 100
  plt.figure(figsize=(15, 5))

  plt.hist(monetary_actual, bins, label='actual', histtype='bar', color='STEELBLUE', rwidth=0.99)
  plt.hist( monetary_predicted, bins, label='predict', histtype='step', color='ORANGE',  rwidth=0.99)
  plt.legend(loc='upper right')
  ```
  ![https://blog.kakaocdn.net/dn/bqmThd/btsgEBC4qmZ/4IjfKM1581TBKPctl7j6kK/img.png](https://blog.kakaocdn.net/dn/bqmThd/btsgEBC4qmZ/4IjfKM1581TBKPctl7j6kK/img.png)
  penalized_coef=0으로 두었을 땐 파란 막대와 주황 막대의 분포가 전혀 다름을 확인할 수 있습니다.
  이제 LTV를 구할 준비는 끝났습니다. 지금까지는
  - 고객별 RFMT를 구하였고
  - calibration/holdout 데이터를 나눠 L2 penalty 최적화를 하였고
  - 최적화된 값을 BG/NBD 모형에 넣어 예상 구매 일수를
  - 최적화된 값을 Gamma-Gamma 모형에 넣어 예상 구매 금액을 구하였습니다.
  이제 BG/NBD 모형과 Gamma-Gamma 모형을 합쳐 LTV를 구할 차례입니다.
  - lifetimes_model은 BetaGeoFitter를 통해 나온 모형이고
  - spend_model은 GammaGammaFitter를 통해 나온 모형입니다.
  이를 통해 아래와 같이 final_df에 ltv 컬럼을 추가할 수 있습니다.
  ```python
  final_df = whole_filtered_df.copy()
  final_df['ltv'] = spend_model.customer_lifetime_value(lifetimes_model,
                                                       final_df['frequency'],
                                                       final_df['recency'],
                                                       final_df['T'],
                                                       final_df['monetary_value'],
                                                       time=12,
                                                       discount_rate=0.01
                                                       )
  ```
  - 여기서 왜 filtered_df 대신 whole_filtered_df를 쓰냐면, calibration/holdout로 나뉘어진 데이터가 아닌 전체 데이터를 대상으로 LTV를 구해야하기 때문입니다.
  또한 BG/NBD 모형의 OUTPUT인 정해진 기간만큼의 **예상 구매 횟수**도 구할 수 있습니다. 63일 동안의 예상 구매 횟수를 구하려면 다음과 같습니다.
  ```python
  t=63
  final_df['predicted_purchases'] = lifetimes_model.conditional_expected_number_of_purchases_up_to_time(t
                                                                                        , final_df['frequency']
                                                                                       , final_df['recency']
                                                                                       , final_df['T'])
  ```
  마지막으로 Gamma-Gamma 모형의 OUTPUT인 **예상 평균 구매 금액**은 다음과 같이 구할 수 있습니다.
  ```python
  final_df['predicted_monetary_value'] = spend_model.conditional_expected_average_profit(final_df['frequency']
                                                                      ,final_df['monetary_value'])
  ```
  그럼 누가 LTV가 가장 많이 높은지 확인해보겠습니다.
  ```python
  final_df.sort_values(by='ltv', ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/bWyzLl/btsgCGeoELZ/0KoeNDKSuUWX2zTaFdY0IK/img.png](https://blog.kakaocdn.net/dn/bWyzLl/btsgCGeoELZ/0KoeNDKSuUWX2zTaFdY0IK/img.png)
  - 1등의 평균 구매 금액 (monetary_value)은 675,467 정도로 한 번에 67만원 정도 쓰시는 분입니다.
  - 63일 동안 43번 구매했습니다.
  - 어제도 구매하셨네요. (T - recency = 집계일 - 마지막 구매 일자가 1이므로)
  LTV 모형을 고려하지 않고 계산하면 다음 해에도 **67만 원씩 \* 43번 = 2천8백만원** 정도 구매할 거라 예상하겠지만
  BG/NBD 모형과 Gamma-Gamma 모형을 통해 나온 **LTV는 66,855,590원**으로 4천만 원 정도 더 많게 예측했습니다.
- 2-6
  본격적으로 데이터 분석을 하기 전에 funnel 분석을 설명드리겠습니다.
  1. funnel은 깔때기란 뜻으로 funnel 분석은 깔때기의 원리와 유사합니다.
  2. 깔때기가 단계가 진행될 수록 좁아지면서 걸러지듯이 funnel 분석에서도 funnel 스탭에 따라 고객에 대한 분석이 가능합니다.
  3. funnel 분석은 단계에 따라 고객의 이탈률을 확인하여 그에 대한 조치를 가능하도록 도와주는 분석 방법이라 할 수 있습니다.
  ### **일별 활성 사용자(Active User) 수 구하기**
  활성 사용자 수는 방문수, 방문자수의 개념과는 달리 '정해진 기간동안 접속한 사용자의 수'입니다.
  흔히 방문수를 구할때 사용하는 세션을 이용해 방문 횟수를 구하지만, 이는 실제 웹에 방문한 사용자 수와는 다른 개념입니다.
  예를 들어 한명의 사용자가 한 사이트에 3번 접속했다면, 방문수는 3회이지만 활성 사용자수는 1명인 것입니다.
  ![https://blog.kakaocdn.net/dn/bhy18K/btsgECCEbfc/eK1qHVNG2zxr5trpnz9Kbk/img.png](https://blog.kakaocdn.net/dn/bhy18K/btsgECCEbfc/eK1qHVNG2zxr5trpnz9Kbk/img.png)
  ![https://blog.kakaocdn.net/dn/c6lMn6/btsgEBKvLMb/PSVkFlJvkkruJrVjxXWfvk/img.png](https://blog.kakaocdn.net/dn/c6lMn6/btsgEBKvLMb/PSVkFlJvkkruJrVjxXWfvk/img.png)
  이런 활성 사용자는 DAU, WAU, MAU 등의 종류가 있는데, DAU는 Daily Active User로 일일 활성 사용자 수를 의미합니다.
  WAU는 Weekly Active User, MAU는 Monthly Active User입니다.
  ### **우선 DAU 구해보겠습니다.**
  (전체적으로 데이터가 WAU, MAU를 구하기는 부족하다고 판단하였습니다)
  데이터를 처음부터 불러와서 시작하겠습니다.
  ```python
  FILES_DIR = './files/'
  total = pd.read_csv(FILES_DIR + 'total.csv')

  total_df = total.copy()
  ```
  본격적으로 DAU를 구해보겠습니다.
  ```python
  table_DAU = pd.pivot_table(total_df,
                                  index='event_timestamp_month',
                                  columns='event_timestamp_day',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  table_DAU.T.sort_values(by=6, ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/bNFaEm/btsgEfAVC1W/vPJSyGRSkpF6FOsQTKreQk/img.png](https://blog.kakaocdn.net/dn/bNFaEm/btsgEfAVC1W/vPJSyGRSkpF6FOsQTKreQk/img.png)
  월별로 총 DAU를 구해보겠습니다.
  ```python
  table_DAU['total'] = table_DAU.sum(axis='columns')
  table_DAU.T
  ```
  ![https://blog.kakaocdn.net/dn/bG2HbW/btsgJ1BpD0q/5KXb4yCdKFcZHVt1qzjSzK/img.png](https://blog.kakaocdn.net/dn/bG2HbW/btsgJ1BpD0q/5KXb4yCdKFcZHVt1qzjSzK/img.png)
  6월 평균 171646, 7월 평균 184097, 8월 평균 143981 을 확인했습니다.
  ### **사이트 체류 시간과 페이지 체류 시간**
  사이트 체류 시간은 말 그대로 사용자가 해당 사이트에 접속하여 머무른 시간을 얘기합니다. 그리고 페이지 체류 시간은 해당 사이트에 접속하여 하나의 페이지에 머무른 시간입니다.
  페이지 체류 시간을 계산하는 방법은 간단합니다. 가장 최근의 페이지 방문 시간에서 이전 페이지 방문시간을 빼면 됩니다.
  사이트 체류 시간은 페이지 체류 시간의 총합과 같습니다.
  ![https://blog.kakaocdn.net/dn/dngVAp/btsgCCQZF4z/DoBCkRZKejryDEdQecxt5k/img.png](https://blog.kakaocdn.net/dn/dngVAp/btsgCCQZF4z/DoBCkRZKejryDEdQecxt5k/img.png)
  ![https://blog.kakaocdn.net/dn/TYn0Z/btsgGlUroAC/lBkFYvO2aiGhLmGKU72dLk/img.png](https://blog.kakaocdn.net/dn/TYn0Z/btsgGlUroAC/lBkFYvO2aiGhLmGKU72dLk/img.png)
  위의 사진과 같이 사용자가 페이지를 이동했다고 가정했을때 첫번째 페이지의 체류 시간은 3분, 두번째 페이지의 체류 시간은 4분입니다.
  그러나 사이트 체류 시간은 10분이 아닌 7분으로 계산 된다는 점입니다. 사용자가 이탈하는 경우 세션 로그가 남지 않기 때문에 세번째 페이지의 체류 시간을 알 수 없고, 사이트 체류 시간 계산에 포함되지 않습니다.
  그렇다고 해서 사이트/페이지 체류 시간이 유의미 하지 않은 지표는 아닙니다. 수많은 사용자들이 수많은 페이지를 빈번하게 오고가는 상황 속에 모든 로그에서 동일하게 마지막 페이지의 체류 시간을 계산하지 않기 때문에 모두가 일관된 기준으로 측정하기에 큰 문제가 되지 않습니다.
  그리고 그 양이 상당하기 때문에 한개 페이지의 시간 차이는 크게 사용자들의 트렌드를 반영하지 못한다고 보는 것이 옳을 것입니다.
  ### **사이트 체류 시간 계산하기**
  위의 사이트 체류 시간의 개념을 적용하여  데이터셋을 이용하여 실제 사용자들의 사이트 체류 시간을 구해보겠습니다.
  사용자들의 페이지 접속 시간을 확인하기 위해 세션 ID를 기준으로 합니다. 사용자의 경우 동일한 ID여도 세션이 다르면은 다른 경우로 간주해야 합니다.
  왜냐하면 세션은 일정 시간동안 같은 사용자(정확하게는 브라우저)로 부터 들어오는 일련의 요구를 하나의 상태로 보기 때문에 세션이 달라졌다면은 다른 시간대에 접속한 것으로 보는 것이 맞기 때문입니다.
  본격적으로 체류시간을 계산해보겠습니다.
  동일한 세션에 대해 가장 마지막(최대)의 시간을 값으로 하는 그룹과 가장 처음(최소)의 시간을 값으로 하는 그룹 2개의 그룹을 만들고 이를 각각 데이터 프레임으로 생성해줍니다.
  ```python
  total_df['event_timestamp'] = pd.to_datetime(total_df['event_timestamp'])
  total_df['event_timestamp'] = total_df['event_timestamp'].dt.strftime('%Y-%m-%d %H:%M:%S')
  total_df['event_timestamp'] = pd.to_datetime(total_df['event_timestamp'])

  max_group = total_df.groupby(['session_id', 'user_no'])['event_timestamp'].max()
  min_group = total_df.groupby(['session_id', 'user_no'])['event_timestamp'].min()

  max_time = pd.DataFrame(max_group)
  max_time.columns = ['latest']
  max_time
  ```
  ![https://blog.kakaocdn.net/dn/cQm22q/btsgJ1nSrUm/TqJHJ2nYP09KAU2iR82gqk/img.png](https://blog.kakaocdn.net/dn/cQm22q/btsgJ1nSrUm/TqJHJ2nYP09KAU2iR82gqk/img.png)
  ```python
  min_time = pd.DataFrame(min_group)
  min_time.columns = ['start']
  min_time
  ```
  ![https://blog.kakaocdn.net/dn/12mW1/btsgEevhxYV/SSau4O5LmkUetDQKXvENPk/img.png](https://blog.kakaocdn.net/dn/12mW1/btsgEevhxYV/SSau4O5LmkUetDQKXvENPk/img.png)
  이후 2개의 데이터 프레임을 연결하여 준 뒤, 컬럼 간의 연산을 통해 세션별 사이트 체류 시간을 구해줍니다.
  ```python
  time_max_min = pd.concat([max_time, min_time], axis=1)
  time_max_min['session_time'] = time_max_min['latest'] - time_max_min['start']
  time_max_min.sort_values(by='session_time', ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/sFgO8/btsgFuRFX5h/KRXK3cx46Laa0x1niy2Kkk/img.png](https://blog.kakaocdn.net/dn/sFgO8/btsgFuRFX5h/KRXK3cx46Laa0x1niy2Kkk/img.png)
  세션별 사이트 체류 시간의 히스토그램을 구해봅니다.
  ```python
  plt.figure(figsize=(12, 5))
  sns.distplot(time_max_min.session_time)
  ```
  ![https://blog.kakaocdn.net/dn/mY2Xn/btsgEK1uG21/gfAChqLQJ2kK28HiDOttf0/img.png](https://blog.kakaocdn.net/dn/mY2Xn/btsgEK1uG21/gfAChqLQJ2kK28HiDOttf0/img.png)
  평균, 표준편차, 중위값을 구해봅니다.
  ```python
  print(time_max_min.session_time.mean())
  print(time_max_min.session_time.std())
  print(time_max_min.session_time.median())
  ```
  ![https://blog.kakaocdn.net/dn/wfPLF/btsgFdCk4GS/GJL3ePWxKOIeW9gPNVeMI1/img.png](https://blog.kakaocdn.net/dn/wfPLF/btsgFdCk4GS/GJL3ePWxKOIeW9gPNVeMI1/img.png)
  구매 데이터와 구매가 아닌데이터만 나뉘어서 체류시간을 비교해보겠습니다.
  먼저 구매데이터 부터 보겠습니다.
  ```python
  suc_data = total_df[total_df['event_name'] == 'purchase_success']

  suc_data['event_timestamp'] = pd.to_datetime(suc_data['event_timestamp'])
  suc_data['event_timestamp'] = suc_data['event_timestamp'].dt.strftime('%Y-%m-%d %H:%M:%S')
  suc_data['event_timestamp'] = pd.to_datetime(suc_data['event_timestamp'])

  max_group = suc_data.groupby(['session_id', 'user_no'])['event_timestamp'].max()
  min_group = suc_data.groupby(['session_id', 'user_no'])['event_timestamp'].min()

  max_time = pd.DataFrame(max_group)
  max_time.columns = ['latest']

  min_time = pd.DataFrame(min_group)
  min_time.columns = ['start']

  suc_max_min = pd.concat([max_time, min_time], axis=1)

  suc_max_min['session_time'] = suc_max_min['latest'] - suc_max_min['start']

  suc_max_min.sort_values(by='session_time', ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/bZm71H/btsgCkwffDx/uKiOjz8UjJD6O6NKr4Bg91/img.png](https://blog.kakaocdn.net/dn/bZm71H/btsgCkwffDx/uKiOjz8UjJD6O6NKr4Bg91/img.png)
  ```python
  print(suc_max_min['session_time'].mean())
  print(suc_max_min['session_time'].std())
  print(suc_max_min['session_time'].median())
  ```
  ![https://blog.kakaocdn.net/dn/rq5RN/btsgELTCmTh/jknEKLF7o9h1HMaX8gOIYk/img.png](https://blog.kakaocdn.net/dn/rq5RN/btsgELTCmTh/jknEKLF7o9h1HMaX8gOIYk/img.png)
  다른 이벤트 데이터 보겠습니다.
  ```python
  non_suc_data = total_df[total_df['event_name'] != 'purchase_success']

  non_suc_data['event_timestamp'] = pd.to_datetime(non_suc_data['event_timestamp'])
  non_suc_data['event_timestamp'] = non_suc_data['event_timestamp'].dt.strftime('%Y-%m-%d %H:%M:%S')
  non_suc_data['event_timestamp'] = pd.to_datetime(non_suc_data['event_timestamp'])

  max_group = non_suc_data.groupby(['session_id', 'user_no'])['event_timestamp'].max()
  min_group = non_suc_data.groupby(['session_id', 'user_no'])['event_timestamp'].min()

  max_time = pd.DataFrame(max_group)
  max_time.columns = ['latest']

  min_time = pd.DataFrame(min_group)
  min_time.columns = ['start']

  non_suc_max_min = pd.concat([max_time, min_time], axis=1)

  non_suc_max_min['session_time'] = non_suc_max_min['latest'] - non_suc_max_min['start']

  non_suc_max_min.sort_values(by='session_time', ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/bIDnYf/btsgEhS5zZv/L1AHygbQ3VoqghWcTOZkk0/img.png](https://blog.kakaocdn.net/dn/bIDnYf/btsgEhS5zZv/L1AHygbQ3VoqghWcTOZkk0/img.png)
  ```python
  print(non_suc_max_min['session_time'].mean())
  print(non_suc_max_min['session_time'].std())
  print(non_suc_max_min['session_time'].median())
  ```
  ![https://blog.kakaocdn.net/dn/dKCDwR/btsgDNSgPJl/WXc1ae5V0hm3tLz7kKFB8K/img.png](https://blog.kakaocdn.net/dn/dKCDwR/btsgDNSgPJl/WXc1ae5V0hm3tLz7kKFB8K/img.png)
  평균의 차이가 5분정도 차이가 납니다.
  이유를 생각해보면 구매하기전에는 고민을 하고 구매하겠다고 확신을 하면 바로 구매하기 때문에 차이는 당연히 난다고 생각합니다.
  보다 중요한것은 구매를 확신했더라도 평균적으로 40초 정도 걸리고 많게는 5분정도 걸린다는것은 구매를 하는 과정이 생각보다 길다고 판단할수도있습니다.
  또한 구매하지 않은 데이터들을 보면 평균적으로 고민을 5분30초정도 하고 많게는 16~17분정도 하는것을 볼수있습니다.
  고민은 길게할수있지만 중위값으로 봤을때는 1분정도 안에 제품에대한 임펙트를 주는것이 좀더 좋은 해석인것같습니다.
  무언가 제품의 설명이나 임펙트를 주고싶을때는 가독성 1분이상 가지 않게 하는것이 중요해 보입니다.
  ###
  ### **본격적으로 Funnel 분석을 하겠습니다.**
  분석에 필요한 컬럼만 불러와서 사용하겠습니다.
  ```python
  funnel_df = total_df[['user_no', 'event_name', 'event_timestamp']]
  funnel_df
  ```
  ![https://blog.kakaocdn.net/dn/CG17w/btsgEDInhSi/FrNSXJKEMXVKmM6n7cyfr1/img.png](https://blog.kakaocdn.net/dn/CG17w/btsgEDInhSi/FrNSXJKEMXVKmM6n7cyfr1/img.png)
  그리고 이 데이터 프레임에서 세션id와 이벤트 타임을 가지고 시간을 기준으로 하여 groupby를 진행해주겠습니다.
  시간에 대해 최초 접속을 기준으로 하기위해 min으로 정렬하겠습니다.
  ```python
  funnel_group = funnel_df.groupby(['user_no', 'event_name'])['event_timestamp'].min()
  funnel_group
  ```
  ![https://blog.kakaocdn.net/dn/IKTAz/btsgJ1g8LUN/W4Xe6QjBhLLKezG42Zd2kK/img.png](https://blog.kakaocdn.net/dn/IKTAz/btsgJ1g8LUN/W4Xe6QjBhLLKezG42Zd2kK/img.png)
  다음 단계로는 funnel 스탭을 위한 별도의 데이터 프레임을 생성했습니다. 스탭은 순서대로 클릭, 좋아요, 장바구니, 구매 순으로 이어지도록 하였다.
  ```python
  funnel_steps = pd.DataFrame({'steps' : [1,2,3,4]}, index=['click_item', 'like_item', 'add_to_cart', 'purchase_success'])
  funnel_steps
  ```
  ![https://blog.kakaocdn.net/dn/et6zcj/btsgDLT8AZ3/8tboyKIP7BfEeHRqnujdHk/img.png](https://blog.kakaocdn.net/dn/et6zcj/btsgDLT8AZ3/8tboyKIP7BfEeHRqnujdHk/img.png)
  기존에 생성한 그룹을 데이터 프레임화 시켜준 후, funnel 스탭 데이터 프레임과 머지 시켜줍니다.
  ```python
  funnel_group = pd.DataFrame(funnel_group).merge(funnel_steps, left_on='event_name', right_index=True)
  funnel_group
  ```
  ![https://blog.kakaocdn.net/dn/ot7Su/btsgDOwS31u/3IykdFpkzhz49k5JKGuAP1/img.png](https://blog.kakaocdn.net/dn/ot7Su/btsgDOwS31u/3IykdFpkzhz49k5JKGuAP1/img.png)
  그 다음에는 피벗 테이블을 이용하여 세션 id별로 다음 단계로의 진행 여부를 확인할 수 있도록 구성해줍니다.
  ```python
  funnel = funnel_group.reset_index().pivot(index='user_no', columns='steps', values='event_timestamp')
  funnel.columns = funnel_steps.index
  funnel
  ```
  ![https://blog.kakaocdn.net/dn/bgojHv/btsgELlLApT/ozeYw9wy3Jv3ieLVgSztPK/img.png](https://blog.kakaocdn.net/dn/bgojHv/btsgELlLApT/ozeYw9wy3Jv3ieLVgSztPK/img.png)
  이제 남은 것은 위의 데이터 프레임을 이용하여 단계별로 카운트하여 funnel 수치를 구해줍니다.
  ```python
  step_values = [funnel[column].notnull().sum() for column in funnel.columns]
  step_values
  ```
  ![https://blog.kakaocdn.net/dn/GhP82/btsgCkbXsd4/OWqrvx0OODCTHNP6oE2jhK/img.png](https://blog.kakaocdn.net/dn/GhP82/btsgCkbXsd4/OWqrvx0OODCTHNP6oE2jhK/img.png)
  그중에서도 plotly express를 이용하면 funnel 수치를 시각화합니다.
  ```python
  import plotly.express as px
  data = dict(
          number = [180605, 22599, 35383, 134741],
          stage = ['click_item', 'like_item', 'add_to_cart', 'purchase_success'])
  fig = px.funnel(data, x='number', y='stage')
  fig.show()
  ```
  ![https://blog.kakaocdn.net/dn/Ws72L/btsgEcqqfjZ/MgaLWveJX906I16gXJwcQk/img.png](https://blog.kakaocdn.net/dn/Ws72L/btsgEcqqfjZ/MgaLWveJX906I16gXJwcQk/img.png)
  클릭 대비 구매는 74%정도 이며 전체데이터 대비 구매율은 36%정도 됩니다.
  여기서 DAU와 구매데이터가 연관성이 있는지 살펴보겠습니다
  DAU가 가장 높은 날은 7월 11일입니다.
  ```python
  # 7월 11일
  funnel_df_0711 = total_df[['user_no', 'event_name', 'event_timestamp','event_timestamp_month', 'event_timestamp_day']]

  funnel_df_0711 = funnel_df_0711[funnel_df_0711['event_timestamp_month'] == 7]
  funnel_df_0711 = funnel_df_0711[funnel_df_0711['event_timestamp_day'] == 11]

  funnel_group_0711 = funnel_df_0711.groupby(['user_no', 'event_name'])['event_timestamp'].min()

  funnel_steps_0711 = pd.DataFrame({'steps' : [1,2,3,4]}, index=['click_item', 'like_item', 'add_to_cart', 'purchase_success'])

  funnel_group_0711 = pd.DataFrame(funnel_group_0711).merge(funnel_steps_0711, left_on='event_name', right_index=True)

  funnel_0711 = funnel_group_0711.reset_index().pivot(index='user_no', columns='steps', values='event_timestamp')
  funnel_0711.columns = funnel_steps_0711.index

  step_values_0711 = [funnel_0711[column].notnull().sum() for column in funnel_0711.columns]
  step_values_0711
  ```
  [18592, 1422, 2435, 4169]
  ```python
  import plotly.express as px
  data = dict(
          number = [18592, 1422, 2435, 4169],
          stage = ['click_item', 'like_item', 'add_to_cart', 'purchase_success'])
  fig = px.funnel(data, x='number', y='stage')
  fig.show()
  ```
  ![https://blog.kakaocdn.net/dn/bap8Qq/btsgCkJOsaC/9y2plcGd6d5wraWBLPD0u0/img.png](https://blog.kakaocdn.net/dn/bap8Qq/btsgCkJOsaC/9y2plcGd6d5wraWBLPD0u0/img.png)
  DAU가 가장 낮은 날은 8월 4일입니다.
  ```python
  # 8월 4일
  funnel_df_0804 = total_df[['user_no', 'event_name', 'event_timestamp','event_timestamp_month', 'event_timestamp_day']]

  funnel_df_0804 = funnel_df_0804[funnel_df_0804['event_timestamp_month'] == 8]
  funnel_df_0804 = funnel_df_0804[funnel_df_0804['event_timestamp_day'] == 4]

  funnel_group_0804 = funnel_df_0804.groupby(['user_no', 'event_name'])['event_timestamp'].min()

  funnel_steps_0804 = pd.DataFrame({'steps' : [1,2,3,4]}, index=['click_item', 'like_item', 'add_to_cart', 'purchase_success'])

  funnel_group_0804 = pd.DataFrame(funnel_group_0804).merge(funnel_steps_0804, left_on='event_name', right_index=True)

  funnel_0804 = funnel_group_0804.reset_index().pivot(index='user_no', columns='steps', values='event_timestamp')
  funnel_0804.columns = funnel_steps_0804.index

  step_values_0804 = [funnel_0804[column].notnull().sum() for column in funnel_0804.columns]
  step_values_0804
  ```
  [6157, 521, 837, 1339]
  ```python
  import plotly.express as px
  data = dict(
          number = [6157, 521, 837, 1339],
          stage = ['click_item', 'like_item', 'add_to_cart', 'purchase_success'])
  fig = px.funnel(data, x='number', y='stage')
  fig.show()
  ```
  ![https://blog.kakaocdn.net/dn/uiQww/btsgE7PXP8b/vc5FmjNhZjGWfI83273QH1/img.png](https://blog.kakaocdn.net/dn/uiQww/btsgE7PXP8b/vc5FmjNhZjGWfI83273QH1/img.png)
  7월 11일 데이터는 구매율 15%, 8월4일 구매율은 15% 즉 DAU와 구매율은 큰 연관성이 없다고 볼수있습니다.
- 2-7
  ### **LTV를 구한 데이터로 VIP구하기 (2-5)**
  우선 데이터를 보겠습니다
  ```python
  final_df.sort_values(by='ltv', ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/deQ6NS/btsgE6wLrBS/ENrChAaR7d9XqY8aye0Fw0/img.png](https://blog.kakaocdn.net/dn/deQ6NS/btsgE6wLrBS/ENrChAaR7d9XqY8aye0Fw0/img.png)
  어떤 기준으로 vip를 나눌까 고민하던중 추천시스템을 만들어야 하니 ltv보다는 frequency를 기준으로 하는게 낫다고 판단하여 frequency를 기준으로 vip를 나누었습니다.
  ```python
  print(final_df.frequency.mean())
  print(final_df.frequency.std())
  print(final_df.frequency.median())
  ```
  2.0629158449101666
  1.8180830543953739
  1.0
  평균과 표준편차를 이용하여 분리하였습니다.
  ```python
  # 4번 이상 고객들 분리
  vip_customer = final_df[final_df['frequency'] >= 4].sort_values(by='frequency', ascending=False)
  non_vip_customer = final_df[final_df['frequency'] < 4].sort_values(by='frequency', ascending=False)
  ```
  vip고객들 frequency 살펴보았습니다.
  ```python
  print(vip_customer.frequency.mean())
  print(vip_customer.frequency.std())
  print(vip_customer.frequency.median())
  ```
  5.6239123428939735
  2.5862779096603608
  5.0
  non_vip고객들 frequency 살펴보았습니다.
  ```python
  print(non_vip_customer.frequency.mean())
  print(non_vip_customer.frequency.std())
  print(non_vip_customer.frequency.median())
  ```
  1.50952798297233
  0.7091423494617679
  1.0
  vip고객과 non_vip고객들의 특징을 살펴보겠습니다.
  ### **vip와 non_vip 나이대 분석**
  vip부터 보겠습니다.
  ```python
  # pivot_table을 사용하여 index(세로)에는 성별(gender)과 나이(age(Group))를 넣어주고,# Columns(가로)에는 event_name를 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 혹은 count를 넣어주면 add_to_cart, click_item, like_item, purchase_success의 누적 개수가 나옵니다
  vip_table = pd.pivot_table(vip_data,
                         index=['gender', 'age(Group)'],
                         columns='event_name',
                         values='user_no',
                         fill_value=0,
                         aggfunc='count')

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  vip_table['total'] = vip_table['add_to_cart'] + vip_table['click_item'] + vip_table['like_item'] + vip_table['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 purchase_success(구매) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  vip_table['conversion'] = vip_table['purchase_success'] / vip_table['total']

  # 이 데이터프레임의 결과를 출력합니다
  vip_table#
  ```
  ![https://blog.kakaocdn.net/dn/bDhbIY/btsgEDIo7eW/ReMDzsrz0zyUmKQAr5KCI1/img.png](https://blog.kakaocdn.net/dn/bDhbIY/btsgEDIo7eW/ReMDzsrz0zyUmKQAr5KCI1/img.png)
  non_vip도 보겠습니다.
  ```python
  # pivot_table을 사용하여 index(세로)에는 성별(gender)과 나이(age(Group))를 넣어주고,# Columns(가로)에는 event_name를 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 혹은 count를 넣어주면 add_to_cart, click_item, like_item, purchase_success의 누적 개수가 나옵니다
  non_vip_table = pd.pivot_table(non_vip_data,
                         index=['gender', 'age(Group)'],
                         columns='event_name',
                         values='user_no',
                         fill_value=0,
                         aggfunc='count')

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  non_vip_table['total'] = non_vip_table['add_to_cart'] + non_vip_table['click_item'] + non_vip_table['like_item'] + non_vip_table['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 purchase_success(구매) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  non_vip_table['conversion'] = non_vip_table['purchase_success'] / non_vip_table['total']

  # 이 데이터프레임의 결과를 출력합니다
  non_vip_table#
  ```
  ![https://blog.kakaocdn.net/dn/cGrNBB/btsgJZDEfjh/UQiFDVlpEUDmyTlOSPSczK/img.png](https://blog.kakaocdn.net/dn/cGrNBB/btsgJZDEfjh/UQiFDVlpEUDmyTlOSPSczK/img.png)
  2-2 분석과 크게 다르지 않습니다.
  vip와 non_vip의 큰 특징을 찾기는 어려울것같습니다.
  ### **vip와 non_vip 시간대 분석**
  ```python
  # pivot_table을 사용하여 index(세로)에는 event_timestamp_hour를 넣어주고,# Columns(가로)에는 event_name 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 를 넣어주면 click_item, add_to_cart, like_item, purchase_success 누적 개수가 나옵니다
  vip_by_hours = pd.pivot_table(vip_data,
                                  index='event_timestamp_hour',
                                  columns='event_name',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  vip_by_hours['total'] = vip_by_hours['add_to_cart'] + vip_by_hours['click_item'] + vip_by_hours['like_item'] + vip_by_hours['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 completed(결제 완료) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  vip_by_hours['conversion'] = vip_by_hours['purchase_success'] / vip_by_hours['total']

  # 결과를 확인합니다
  vip_by_hours
  ```
  ![https://blog.kakaocdn.net/dn/bgy2LB/btsgE64Csa9/PcIPyKeKUCE4ZKXQAaWxz1/img.png](https://blog.kakaocdn.net/dn/bgy2LB/btsgE64Csa9/PcIPyKeKUCE4ZKXQAaWxz1/img.png)
  ```
  # pivot_table을 사용하여 index(세로)에는 event_timestamp_hour를 넣어주고,# Columns(가로)에는 event_name 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 를 넣어주면 click_item, add_to_cart, like_item, purchase_success 누적 개수가 나옵니다
  non_vip_by_hours = pd.pivot_table(non_vip_data,
                                  index='event_timestamp_hour',
                                  columns='event_name',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  non_vip_by_hours['total'] = non_vip_by_hours['add_to_cart'] + non_vip_by_hours['click_item'] + non_vip_by_hours['like_item'] + non_vip_by_hours['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 completed(결제 완료) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  non_vip_by_hours['conversion'] = non_vip_by_hours['purchase_success'] / non_vip_by_hours['total']

  # 결과를 확인합니다
  non_vip_by_hours
  ```
  ![https://blog.kakaocdn.net/dn/bybYgi/btsgECvWuJ6/VtaeQLkAO6WOmHmTHVlZR0/img.png](https://blog.kakaocdn.net/dn/bybYgi/btsgECvWuJ6/VtaeQLkAO6WOmHmTHVlZR0/img.png)
  ```python
  # 그래프 전체 사이즈 설정합니다
  plt.figure(figsize=[20,5])

  # y축의 최소/최대값을 0/0.2로 고정합니다
  plt.ylim(0, 0.2)

  # 시간대별 전환율(conversion)을 시각화 합니다
  sns.pointplot(data = vip_by_hours.reset_index(), x='event_timestamp_hour', y='conversion')
  ```
  ![https://blog.kakaocdn.net/dn/DExrB/btsgGmy5bYv/qsqfNNhh1mjjs4NoRaIUbk/img.png](https://blog.kakaocdn.net/dn/DExrB/btsgGmy5bYv/qsqfNNhh1mjjs4NoRaIUbk/img.png)
  ```
  # 그래프 전체 사이즈 설정합니다plt.figure(figsize=[20,5])

  # y축의 최소/최대값을 0/0.2로 고정합니다plt.ylim(0, 0.2)

  # 시간대별 전환율(conversion)을 시각화 합니다sns.pointplot(data = non_vip_by_hours.reset_index(), x='event_timestamp_hour', y='conversion')
  ```
  ![https://blog.kakaocdn.net/dn/d3fiXm/btsgGiXK6UU/OfHmJSI88tsYD0AaVrgQOk/img.png](https://blog.kakaocdn.net/dn/d3fiXm/btsgGiXK6UU/OfHmJSI88tsYD0AaVrgQOk/img.png)
  ```python
  # 그래프 전체 사이즈 설정합니다
  plt.figure(figsize=[20,5])

  # 시간대별 구매(purchase_success)를 시각화 합니다
  sns.pointplot(data = vip_by_hours.reset_index(), x='event_timestamp_hour', y='purchase_success')
  ```
  ![https://blog.kakaocdn.net/dn/daRxVf/btsgC3HBIc7/FKXviMdAARKqWQOti05unk/img.png](https://blog.kakaocdn.net/dn/daRxVf/btsgC3HBIc7/FKXviMdAARKqWQOti05unk/img.png)
  ```python
  # 그래프 전체 사이즈 설정합니다
  plt.figure(figsize=[20,5])

  # 시간대별 구매(purchase_success)를 시각화 합니다
  sns.pointplot(data = non_vip_by_hours.reset_index(), x='event_timestamp_hour', y='purchase_success')
  ```
  ![https://blog.kakaocdn.net/dn/XkMK2/btsgJZRcKvM/OYl1XaNSQydvZRs4okzsz1/img.png](https://blog.kakaocdn.net/dn/XkMK2/btsgJZRcKvM/OYl1XaNSQydvZRs4okzsz1/img.png)
  2-3 분석과 크게 다르지 않습니다.
  vip와 non_vip의 큰 특징을 찾기는 어려울것같습니다.
  ### **vip와 non_vip 요일대 분석**
  ```python
  # pivot_table을 사용하여 index(세로)에는 event_timestamp(weekday)를 넣어주고,# Columns(가로)에는 event_name 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 를 넣어주면 click_item, add_to_cart, like_item, purchase_success 누적 개수가 나옵니다
  vip_by_week = pd.pivot_table(vip_data,
                                  index='event_timestamp(weekday)',
                                  columns='event_name',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  vip_by_week['total'] = vip_by_week['add_to_cart'] + vip_by_week['click_item'] + vip_by_week['like_item'] + vip_by_week['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 completed(결제 완료) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  vip_by_week['conversion'] = vip_by_week['purchase_success'] / vip_by_week['total']

  # 컬럼 정렬을 위해 새로운 변수를 생성합니다
  week_columns = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']

  # 결과를 확인합니다
  vip_by_week.loc[week_columns]
  ```
  ![https://blog.kakaocdn.net/dn/bq5U1j/btsgDhF93oz/eAWRRhTGzvHk2mX8La88U1/img.png](https://blog.kakaocdn.net/dn/bq5U1j/btsgDhF93oz/eAWRRhTGzvHk2mX8La88U1/img.png)
  ```python
  # pivot_table을 사용하여 index(세로)에는 event_timestamp(weekday)를 넣어주고,# Columns(가로)에는 event_name 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 를 넣어주면 click_item, add_to_cart, like_item, purchase_success 누적 개수가 나옵니다
  non_vip_by_week = pd.pivot_table(non_vip_data,
                                  index='event_timestamp(weekday)',
                                  columns='event_name',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  non_vip_by_week['total'] = non_vip_by_week['add_to_cart'] + non_vip_by_week['click_item'] + non_vip_by_week['like_item'] + non_vip_by_week['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 completed(결제 완료) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  non_vip_by_week['conversion'] = non_vip_by_week['purchase_success'] / non_vip_by_week['total']

  # 컬럼 정렬을 위해 새로운 변수를 생성합니다
  week_columns = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']

  # 결과를 확인합니다
  non_vip_by_week.loc[week_columns]
  ```
  ![https://blog.kakaocdn.net/dn/YgELt/btsgCiSL11p/EPufYB3KJJG0sT1wcizTeK/img.png](https://blog.kakaocdn.net/dn/YgELt/btsgCiSL11p/EPufYB3KJJG0sT1wcizTeK/img.png)
  ```python
  # 가로를 20인치, 세로를 5인치로 조절합니다
  plt.figure(figsize=[20,5])

  # y축의 최소/최대값을 0/0.2로 고정합니다
  plt.ylim(0, 0.2)

  # 이전에 피벗테이블로 분석한 결과를 pointplot으로 시각화합니다# X축은 구매 요일(event_timestamp(weekday)))을, y축은 전환율(conversion)을 측정합니다
  sns.pointplot(data=vip_by_week.reset_index(), x='event_timestamp(weekday)',\
                y='conversion', order = week_columns)
  ```
  ![https://blog.kakaocdn.net/dn/N5JUk/btsgEgtb2LO/8WSDqt4FeGwOwvXgbX2u90/img.png](https://blog.kakaocdn.net/dn/N5JUk/btsgEgtb2LO/8WSDqt4FeGwOwvXgbX2u90/img.png)
  ```python
  # 가로를 20인치, 세로를 5인치로 조절합니다
  plt.figure(figsize=[20,5])

  # y축의 최소/최대값을 0/0.2로 고정합니다
  plt.ylim(0, 0.2)

  # 이전에 피벗테이블로 분석한 결과를 pointplot으로 시각화합니다# X축은 구매 요일(event_timestamp(weekday)))을, y축은 전환율(conversion)을 측정합니다
  sns.pointplot(data=non_vip_by_week.reset_index(), x='event_timestamp(weekday)',\
                y='conversion', order = week_columns)
  ```
  ![https://blog.kakaocdn.net/dn/qhMXP/btsgJ1BvGtP/rocKvNcKBXba63JF3WHGY0/img.png](https://blog.kakaocdn.net/dn/qhMXP/btsgJ1BvGtP/rocKvNcKBXba63JF3WHGY0/img.png)
  ```python
  # 가로를 20인치, 세로를 5인치로 조절합니다.
  plt.figure(figsize=[20,5])

  # 이전에 피벗테이블로 분석한 결과를 pointplot으로 시각화합니다# X축은 구매 요일(event_timestamp(weekday))을, y축은 구매 완료 (purchase_success)를 측정합니다
  sns.pointplot(data=vip_by_week.reset_index(), x='event_timestamp(weekday)',\
                y='purchase_success', order = week_columns)
  ```
  ![https://blog.kakaocdn.net/dn/dqcrW5/btsgClINYKB/g6EITKmMNm1HzAICQ7NE11/img.png](https://blog.kakaocdn.net/dn/dqcrW5/btsgClINYKB/g6EITKmMNm1HzAICQ7NE11/img.png)
  ```python
  # 가로를 20인치, 세로를 5인치로 조절합니다.
  plt.figure(figsize=[20,5])

  # 이전에 피벗테이블로 분석한 결과를 pointplot으로 시각화합니다# X축은 구매 요일(event_timestamp(weekday))을, y축은 구매 완료 (purchase_success)를 측정합니다
  sns.pointplot(data=non_vip_by_week.reset_index(), x='event_timestamp(weekday)',\
                y='purchase_success', order = week_columns)
  ```
  ![https://blog.kakaocdn.net/dn/bKWkg4/btsgGlNItsF/jxYk6zQKqUABzkvfR26tOK/img.png](https://blog.kakaocdn.net/dn/bKWkg4/btsgGlNItsF/jxYk6zQKqUABzkvfR26tOK/img.png)
  2-3 분석만 봤다면 일~화까지의 데이터가 중요한것이지만 vip분석으로 vip고객들은 수요일에도 구매를 많이 하기 때문에 수요일까지도 중요한 요일이라고 판단하면 좋을것같다.
  non_vip들은 수요일에 확 떨어진다.
  ### **vip와 non_vip 카테고리 분석**
  vip
  ```python
  # pivot_table을 사용하여 index(세로)에는 category2_name 넣어주고,# Columns(가로)에는 event_name 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 를 넣어주면 click_item, add_to_cart, like_item, purchase_success 누적 개수가 나옵니다
  vip_by_cat2 = pd.pivot_table(vip_data,
                                  index='category2_name',
                                  columns='event_name',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  vip_by_cat2['total'] = vip_by_cat2['add_to_cart'] + vip_by_cat2['click_item'] + vip_by_cat2['like_item'] + vip_by_cat2['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 completed(결제 완료) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  vip_by_cat2['conversion'] = vip_by_cat2['purchase_success'] / vip_by_cat2['total']

  # 결과를 확인합니다
  vip_by_cat2.sort_values(by='conversion', ascending=False)
  ```
  ```python
  print(vip_by_cat2.purchase_success.mean())
  print(vip_by_cat2.purchase_success.std())
  print(vip_by_cat2.purchase_success.median())
  ```
  869.1979166666666
  2566.0585037421088
  137.0
  ```python
  vip_suc_3000 = vip_by_cat2[vip_by_cat2['purchase_success'] >= 3000]
  vip_suc_3000['purchase_success'].sum() / vip_by_cat2['purchase_success'].sum()
  ```
  0.615557925769687
  ```python
  vip_suc_3000.sort_values(by='conversion', ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/Jsi1E/btsgEhFBHFt/PLNydM46jLgTeps0MRwkf0/img.png](https://blog.kakaocdn.net/dn/Jsi1E/btsgEhFBHFt/PLNydM46jLgTeps0MRwkf0/img.png)
  non_vip
  ```python
  # pivot_table을 사용하여 index(세로)에는 category2_name 넣어주고,# Columns(가로)에는 event_name 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 를 넣어주면 click_item, add_to_cart, like_item, purchase_success 누적 개수가 나옵니다
  non_vip_by_cat2 = pd.pivot_table(non_vip_data,
                                  index='category2_name',
                                  columns='event_name',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  non_vip_by_cat2['total'] = non_vip_by_cat2['add_to_cart'] + non_vip_by_cat2['click_item'] + non_vip_by_cat2['like_item'] + non_vip_by_cat2['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 completed(결제 완료) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  non_vip_by_cat2['conversion'] = non_vip_by_cat2['purchase_success'] / non_vip_by_cat2['total']

  # 결과를 확인합니다
  non_vip_by_cat2.sort_values(by='conversion', ascending=False)
  ```
  ```python
  print(non_vip_by_cat2.purchase_success.mean())
  print(non_vip_by_cat2.purchase_success.std())
  print(non_vip_by_cat2.purchase_success.median())
  ```
  1790.3663366336634
  5970.471014455678
  205.0
  ```python
  non_vip_suc_6000 = non_vip_by_cat2[non_vip_by_cat2['purchase_success'] >= 6000]
  non_vip_suc_6000['purchase_success'].sum() / non_vip_by_cat2['purchase_success'].sum()
  ```
  0.6255592361760135
  ```python
  non_vip_suc_6000.sort_values(by='conversion', ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/SvlV2/btsgEfOyY3w/Qk3NgKEN7rztzVhuWsgYdK/img.png](https://blog.kakaocdn.net/dn/SvlV2/btsgEfOyY3w/Qk3NgKEN7rztzVhuWsgYdK/img.png)
  2-4 분석과 크게 다르지 않습니다.
  다만 원피스/점프슈트 카테고리는 특정 이벤트나 프로모션 때문에 구매하기 보다는 꾸준히 소비되는 카테고리이며 카테고리를 좀더 세분화 했을때 원피스, 점프슈트 2가지 카테고리가있으며 브랜드는 MONGDOL(9%) 브랜드가 가장 많이 팔리는 브랜드이다. 다만 이 브랜드가 전체 구매율을 봤을때는 큰 메리트가 있지는 않다.
  브랜드가 중요한것이 아니라 카테고리가 중요하다고 볼수있다.
  ### **vip와 non_vip 브랜드별 분석**
  vip
  ```python
  # pivot_table을 사용하여 index(세로)에는 brand_name 넣어주고,# Columns(가로)에는 event_name 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 를 넣어주면 click_item, add_to_cart, like_item, purchase_success 누적 개수가 나옵니다
  vip_by_brand = pd.pivot_table(vip_data,
                                  index='brand_name',
                                  columns='event_name',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  vip_by_brand['total'] = vip_by_brand['add_to_cart'] + vip_by_brand['click_item'] + vip_by_brand['like_item'] + vip_by_brand['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 completed(결제 완료) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  vip_by_brand['conversion'] = vip_by_brand['purchase_success'] / vip_by_brand['total']

  # 결과를 확인합니다
  vip_by_brand.sort_values(by='total', ascending=False)
  ```
  ```python
  print(vip_by_brand['total'].mean())
  print(vip_by_brand['total'].std())
  print(vip_by_brand['total'].median())
  ```
  149.65341759521039
  579.5227861218661
  15.0
  ```python
  brand_vip_600 = vip_by_brand[vip_by_brand['total'] > 600].sort_values(by='conversion', ascending=False)

  print(brand_vip_600['purchase_success'].mean())
  print(brand_vip_600['purchase_success'].std())
  print(brand_vip_600['purchase_success'].median())
  ```
  147.83183183183183
  175.40085400616533
  94.0
  ```python
  # top 15
  brand_vip_600[brand_vip_600['purchase_success'] > 300].sort_values(by='conversion', ascending=False)[:10]
  ```
  ![https://blog.kakaocdn.net/dn/KOasG/btsgE7oWlNG/j8ZFCYTpVDdv80etxuKWnK/img.png](https://blog.kakaocdn.net/dn/KOasG/btsgE7oWlNG/j8ZFCYTpVDdv80etxuKWnK/img.png)
  non_vip
  ```python
  # pivot_table을 사용하여 index(세로)에는 brand_name 넣어주고,# Columns(가로)에는 event_name 넣어줍니다# 여기서 fill_value에 0을 넣어줘야, 데이터가 없을 경우 NaN이 아닌 0이 나옵니다# 그리고 aggfunc에 len 를 넣어주면 click_item, add_to_cart, like_item, purchase_success 누적 개수가 나옵니다
  non_vip_by_brand = pd.pivot_table(non_vip_data,
                                  index='brand_name',
                                  columns='event_name',
                                  values='user_no',
                                  fill_value=0,
                                  aggfunc=len)

  # add_to_cart, click_item, like_item, purchase_success 총 인원 수를 더해서 total이라는 새로운 컬럼을 추가합니다
  non_vip_by_brand['total'] = non_vip_by_brand['add_to_cart'] + non_vip_by_brand['click_item'] + non_vip_by_brand['like_item'] + non_vip_by_brand['purchase_success']

  # 위에서 추가한 total이라는 컬럼으로 completed(결제 완료) 컬럼을 나누면 결제 확률, 전환율(Conversion)이 나옵니다
  non_vip_by_brand['conversion'] = non_vip_by_brand['purchase_success'] / non_vip_by_brand['total']

  # 결과를 확인합니다
  non_vip_by_brand.sort_values(by='total', ascending=False)
  ```
  ```python
  print(non_vip_by_brand['total'].mean())
  print(non_vip_by_brand['total'].std())
  print(non_vip_by_brand['total'].median())
  ```
  279.31304609822234
  1141.0134850269749
  20.0
  ```python
  non_vip_by_brand_1300 = non_vip_by_brand[non_vip_by_brand['total'] > 1300].sort_values(by='conversion', ascending=False)

  print(non_vip_by_brand_1300['purchase_success'].mean())
  print(non_vip_by_brand_1300['purchase_success'].std())
  print(non_vip_by_brand_1300['purchase_success'].median())
  ```
  356.42088607594934
  459.78811207645606
  218.5
  ```python
  # top15
  non_vip_by_brand_1300[non_vip_by_brand_1300['purchase_success'] > 800].sort_values(by='conversion', ascending=False)[:11]
  ```
  ![https://blog.kakaocdn.net/dn/ch5Qjd/btsgFcKhjzf/kFGhxP44Y1RdJ1viszpgok/img.png](https://blog.kakaocdn.net/dn/ch5Qjd/btsgFcKhjzf/kFGhxP44Y1RdJ1viszpgok/img.png)
  BIARRITZ, maatila, 락앤락 브랜드들은 프로모션이나 이벤트 보다는 평소 고객들의 만족도가 높고 재구매율이 높은 브랜드이며
  UNLIMIT, MARITHE FRANCOIS GIRBAUD, EMIS 프로모션이나 이벤트로 1회용 구매 브랜드 인것 같습니다.
  ### **vip와 non_vip 체류시간 분석**
  vip
  ```python
  vip_data['event_timestamp'] = pd.to_datetime(vip_data['event_timestamp'])
  vip_data['event_timestamp'] = vip_data['event_timestamp'].dt.strftime('%Y-%m-%d %H:%M:%S')
  vip_data['event_timestamp'] = pd.to_datetime(vip_data['event_timestamp'])

  max_group = vip_data.groupby(['session_id', 'user_no'])['event_timestamp'].max()
  min_group = vip_data.groupby(['session_id', 'user_no'])['event_timestamp'].min()

  max_time = pd.DataFrame(max_group)
  max_time.columns = ['latest']

  min_time = pd.DataFrame(min_group)
  min_time.columns = ['start']

  vip_max_min = pd.concat([max_time, min_time], axis=1)

  vip_max_min['session_time'] = vip_max_min['latest'] - vip_max_min['start']

  vip_max_min.sort_values(by='session_time', ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/bYvE2D/btsgEgtesew/gHh9OoZFAwN7Fl1vTnI1X1/img.png](https://blog.kakaocdn.net/dn/bYvE2D/btsgEgtesew/gHh9OoZFAwN7Fl1vTnI1X1/img.png)
  ```python
  print(vip_max_min['session_time'].mean())
  print(vip_max_min['session_time'].std())
  print(vip_max_min['session_time'].median())
  ```
  00:06:51
  00:13:19
  00:01:15
  non_vip
  ```python
  non_vip_data['event_timestamp'] = pd.to_datetime(non_vip_data['event_timestamp'])
  non_vip_data['event_timestamp'] = non_vip_data['event_timestamp'].dt.strftime('%Y-%m-%d %H:%M:%S')
  non_vip_data['event_timestamp'] = pd.to_datetime(non_vip_data['event_timestamp'])

  max_group = non_vip_data.groupby(['session_id', 'user_no'])['event_timestamp'].max()
  min_group = non_vip_data.groupby(['session_id', 'user_no'])['event_timestamp'].min()

  max_time = pd.DataFrame(max_group)
  max_time.columns = ['latest']

  min_time = pd.DataFrame(min_group)
  min_time.columns = ['start']

  non_vip_max_min = pd.concat([max_time, min_time], axis=1)

  non_vip_max_min['session_time'] = non_vip_max_min['latest'] - non_vip_max_min['start']

  non_vip_max_min.sort_values(by='session_time', ascending=False)
  ```
  ![https://blog.kakaocdn.net/dn/cK3TKo/btsgCjYuCxC/Sq0EymZSKQRpeWGlPIMxS1/img.png](https://blog.kakaocdn.net/dn/cK3TKo/btsgCjYuCxC/Sq0EymZSKQRpeWGlPIMxS1/img.png)
  ```python
  print(non_vip_max_min['session_time'].mean())
  print(non_vip_max_min['session_time'].std())
  print(non_vip_max_min['session_time'].median())
  ```
  00:05:53
  00:11:42
  00:00:54
  체류시간이 꼭 구매에 영향을 주는것은 아닌것같습니다.
  분석은 2-6과 크게 다르지 않은것 같습니다.

---

---

# 3. 추천시스템

- 3-1
  ### **Factorization Machine**
  사용하여 추천시스템을 만들어 보겠습니다.
  FM에 대한 설명은**[https://datapractice0815.tistory.com/208](https://datapractice0815.tistory.com/208)**이곳에서 확인해주세요.
  우선 리소스 문제로인하여 1/100 이로 샘플링 하여 데이터를 사용하겠습니다.
  ```python
  # sample_df = total.sample(frac=0.01, random_state=42)# sample_df.to_csv('sample_df.csv')# 리소스 문제로 total data를 1/100로 샘플링

  FILES_DIR = './files/'
  sample = pd.read_csv(FILES_DIR + 'sample_df.csv')
  ```
  분석과정에서 나온 데이터들을 전처리하겠습니다.
  ```python
  # binary로 문제를 풀어볼거여서 구매 말고는 -1로
  sample_df['rating'] = sample_df['event_name']
  re_name = {'rating' : {'click_item':-1, 'like_item':-1, 'add_to_cart':-1, 'purchase_success':1}}
  sample_df = sample_df.replace(re_name)
  ########################################################################
  sample_df = sample_df[sample_df['gender'] != 'un_gender']
  ########################################################################
  sample_df.loc[sample_df['age'] <= 17, 'age(Group)'] = '00 ~ 17'

  sample_df.loc[(sample_df['age'] >= 18) &\
                 (sample_df['age'] <= 24), 'age(Group)'] = '18 ~ 24'

  sample_df.loc[(sample_df['age'] >= 25) &\
                 (sample_df['age'] <= 35), 'age(Group)'] = '25 ~ 35'

  sample_df.loc[(sample_df['age'] >= 36) &\
                 (sample_df['age'] <= 44), 'age(Group)'] = '36 ~ 44'

  sample_df.loc[(sample_df['age'] >= 45) &\
                 (sample_df['age'] <= 54), 'age(Group)'] = '45 ~ 54'

  sample_df.loc[sample_df['age'] >= 55, 'age(Group)'] = '55 ~ 99'
  ########################################################################
  sample_df['event_timestamp'] = pd.to_datetime(sample_df['event_timestamp'])
  sample_df['event_timestamp(weekday)'] = sample_df['event_timestamp'].dt.day_name()
  ```
  우선 기존 데이터에서 필요한 컬럼만 추출하여 사용하겠습니다.
  ```python
  # 필요한 컬럼만 추출
  col_list_1 = ['event_name', 'user_no', 'item_no', 'country', 'region', 'platform',
  'event_timestamp_month', 'event_timestamp_day', 'event_timestamp_hour',
  'category1_name', 'category2_name', 'gender', 'age', 'age(Group)', 'event_timestamp(weekday)']

  raw_data_1 = sample_df[col_list_1]
  ```
  X와 y데이터를 만들겠습니다.
  ```python
  X_data = pd.get_dummies(raw_data_1, columns=col_list_1)
  y_data = sample_df['rating']
  ```
  X데이터를 csr_matrix로 만들어 주겠습니다.
  csr_matrix 자세한 설명은 [https://rfriend.tistory.com/551](https://rfriend.tistory.com/551) 참고해주세요
  ```python
  import scipy
  X_sparse = scipy.sparse.csr_matrix(X_data.values)
  ```
  Model Equation
  ![https://blog.kakaocdn.net/dn/SriCt/btsgEfnFMBC/ggECYaaizyD41xgHMEJ1PK/img.png](https://blog.kakaocdn.net/dn/SriCt/btsgEfnFMBC/ggECYaaizyD41xgHMEJ1PK/img.png)
  Pairwise Interaction to be computed
  - Linear Complexity
  ![https://blog.kakaocdn.net/dn/bSisdr/btsgDNdv5no/yhJoKrQzPdU2DYZQAMfTTK/img.png](https://blog.kakaocdn.net/dn/bSisdr/btsgDNdv5no/yhJoKrQzPdU2DYZQAMfTTK/img.png)
  로스를 정의하는 부분입니다.
  negative likelihood 이며 binary classification할때 사용하는 것입니다.
  ```python
  # Compute negative log likelihood between prediction and labeldef log_loss(pred, y):
      return np.log(np.exp(-pred * y) + 1.0)
  ```
  gradient를 업데이트 해주겠습니다.
  SGD를 사용하겠습니다
  ```python
  # Update gradientsdef sgd(X, y, n_samples, n_features,
                  w0, w, v, n_factors, learning_rate, reg_w, reg_v):
      data = X.data
      indptr = X.indptr
      indices = X.indices
      loss = 0.0

      for i in range(n_samples):
          pred, summed = predict(X, w0, w, v, n_factors, i)

  # calculate loss and its gradient
          loss += log_loss(pred, y[i])
          loss_gradient = -y[i] / (np.exp(y[i] * pred) + 1.0)

  # update bias/intercept term
          w0 -= learning_rate * loss_gradient

  # update weightfor index in range(indptr[i], indptr[i + 1]):
              feature = indices[index]
              w[feature] -= learning_rate * (loss_gradient * data[index] + 2 * reg_w * w[feature])

  # update factorfor factor in range(n_factors):
              for index in range(indptr[i], indptr[i + 1]):
                  feature = indices[index]
                  term = summed[factor] - v[factor, feature] * data[index]
                  v_gradient = loss_gradient * data[index] * term
                  v[factor, feature] -= learning_rate * (v_gradient + 2 * reg_v * v[factor, feature])

      loss /= n_samples
      return loss
  ```
  y예측값을 얻기위한 과정입니다.
  ```python
  def predict(X, w0, w, v, n_factors, i):
      data = X.data
      indptr = X.indptr
      indices = X.indices
      """predicting a single instance"""
      summed = np.zeros(n_factors)
      summed_squared = np.zeros(n_factors)

  # linear output w * x
      pred = w0
      for index in range(indptr[i], indptr[i + 1]):
          feature = indices[index]
          pred += w[feature] * data[index]

  # factor outputfor factor in range(n_factors):
          for index in range(indptr[i], indptr[i + 1]):
              feature = indices[index]
              term = v[factor, feature] * data[index]
              summed[factor] += term
              summed_squared[factor] += term * term

          pred += 0.5 * (summed[factor] * summed[factor] - summed_squared[factor])

  # gradient update할 때, summed는 독립이므로 re-use 가능return pred, summed
  ```
  학습하는 과정입니다.
  ```python
  # Train Factorization Machine# X -> sparse csr_matrix, y -> labeldef fit(X, y, config):
      epochs = config['num_epochs']
      num_factors = config['num_factors']
      learning_rate = config['learning_rate']
      reg_weights = config['reg_weights']
      reg_features = config['reg_features']

      num_samples, num_features = X.shape
      weights = np.zeros(num_features)# -> w
      global_bias = 0.0# -> w0

  # latent factors for all features -> v
      feature_factors = np.random.normal(size = (num_factors, num_features))

      epoch_loss = []
      for epoch in range(epochs):
          loss = sgd(X, y, num_samples, num_features,
                              global_bias, weights,
                              feature_factors, num_factors,
                              learning_rate, reg_weights, reg_features)
          print(f'[epoch: {epoch+1}], loss: {loss}')

          epoch_loss.append(loss)

      return epoch_loss
  ```
  ```python
  config = {
      "num_epochs": 10,
      "num_factors": 10,
      "learning_rate": 0.01,
      "reg_weights": 0.01,
      "reg_features": 0.01
  }
  ```
  ```python
  epoch_loss = fit(X_sparse_1, y_data.values, config)
  ```
  ![https://blog.kakaocdn.net/dn/bA6pJz/btsgEevLD55/rsbtbqDWcfe1UrkHXbBONk/img.png](https://blog.kakaocdn.net/dn/bA6pJz/btsgEevLD55/rsbtbqDWcfe1UrkHXbBONk/img.png)
  ```python
  import plotly.graph_objects as go
  fig = go.Figure()
  fig.add_trace(go.Scatter(x=epoch_loss, mode='lines+markers'))
  fig.show()
  ```
  ![https://blog.kakaocdn.net/dn/bmdkZh/btsgEfnWcrO/JM3REekBKmc4yJPBBnbA7k/img.png](https://blog.kakaocdn.net/dn/bmdkZh/btsgEfnWcrO/JM3REekBKmc4yJPBBnbA7k/img.png)
  기본 데이터에서 feature들을 점점 추가하여 FM의 성능을 확인해 보겠습니다.
- 3-2
  3-1을 기본으로 하여 feature들을 추가해 보겠습니다.
  2-3분석에서  일~화요일이 다가올수록 구매를 많이 하게 되는 현상을 발견할 수 있습니다.
  추가로 2~7에서 vip고객들은 수요일도 구매를 많이 한다는걸 발견했습니다.
  일~수요일 데이터들의 feature를 추가하여 FM성능을 확인해 보겠습니다.
  ```python
  sample_df['dayname_feature'] = np.where((sample_df['event_timestamp(weekday)'] == 'Sunday')
                                          |(sample_df['event_timestamp(weekday)'] == 'Monday')
                                          |(sample_df['event_timestamp(weekday)'] == 'Tuesday')
                                          |(sample_df['event_timestamp(weekday)'] == 'Wednesday') , 1, 0)
  ```
  ```python
  epoch_loss = fit(X_sparse, y_data.values, config)
  ```
  ![https://blog.kakaocdn.net/dn/dsunxC/btsgECC99r4/y3tt4p1OETpgqevAiKJmpk/img.png](https://blog.kakaocdn.net/dn/dsunxC/btsgECC99r4/y3tt4p1OETpgqevAiKJmpk/img.png)
  ```python
  plt.plot(epoch_loss)
  plt.title('Loss per epoch')
  plt.show()
  ```
  ![https://blog.kakaocdn.net/dn/bVFkss/btsgEasXmUI/N0y3T9fzahCpkKMFcZ6iZ0/img.png](https://blog.kakaocdn.net/dn/bVFkss/btsgEasXmUI/N0y3T9fzahCpkKMFcZ6iZ0/img.png)
  3-1보다 성능이 3배정도 좋아진걸 확인할수있습니다.
  다른 feature도 추가하여 성능의 변화를 확인해 보겠습니다.
- 3-3
  기존 3-2 데이터를 이어서 하겠습니다.
  2-3 데이터 분석을 기반으로 10~12시, 20~24(0)시 feature를 만들어 추가하겠습니다.
  ```python
  sample_df['event_timestamp_hour'] = sample_df['event_timestamp_hour'].replace(0, 24)
  sample_df['time_1'] = np.where((sample_df['event_timestamp_hour'] >= 10)&(sample_df["event_timestamp_hour"].values <= 12) , 1, 0)
  sample_df['time_2'] = np.where((sample_df["event_timestamp_hour"].values >= 20), 1, 0)
  ```
  ```python
  epoch_loss = fit(X_sparse, y_data.values, config)
  ```
  ![https://blog.kakaocdn.net/dn/zgY4P/btsgC4NDdOU/gBOF78UPmvqjt1nZe72ABK/img.png](https://blog.kakaocdn.net/dn/zgY4P/btsgC4NDdOU/gBOF78UPmvqjt1nZe72ABK/img.png)
  ```python
  plt.plot(epoch_loss)
  plt.title('Loss per epoch')
  plt.show()
  ```
  ![https://blog.kakaocdn.net/dn/GrGIO/btsgEbSU68J/EgQkd1Bw5aTRzKumjv2iHk/img.png](https://blog.kakaocdn.net/dn/GrGIO/btsgEbSU68J/EgQkd1Bw5aTRzKumjv2iHk/img.png)
  3-2와 성능의 차이를 보이지 못했다.
  리소스 문제로 하이퍼 파라미터 튜닝이 어렵고 데이터를 샘플링하는 과정에서 문제가 생겨 그럴수있지만
  그렇다고 한들 성능의 차이가 보이지 못한것은 아쉬운 부분이다.
  다른 추가적인 부분도 feature를 추가해보자
- 3-4
  기존 3-3 데이터를 이어서 하겠습니다.
  2-7분석을 기반으로 새로운 카테고리 feature를 만들어 봅니다.
  ```python
  sample_df['cat2_feature'] = np.where((sample_df['category2_name'] == '상의')
                                          |(sample_df['category2_name'] == '하의')
                                          |(sample_df['category2_name'] == '가방')
                                          |(sample_df['category2_name'] == '신발')
                                          |(sample_df['category2_name'] == '주방용품')
                                          |(sample_df['category2_name'] == '언더웨어')
                                          |(sample_df['category2_name'] == '원피스/점프슈트') , 1, 0)
  ```
  ```python
  epoch_loss = fit(X_sparse, y_data.values, config)
  ```
  ![https://blog.kakaocdn.net/dn/cfDla2/btsgFuxRUSs/SODBYoQzCa7eIpzZikQvGK/img.png](https://blog.kakaocdn.net/dn/cfDla2/btsgFuxRUSs/SODBYoQzCa7eIpzZikQvGK/img.png)
  ```python
  plt.plot(epoch_loss)
  plt.title('Loss per epoch')
  plt.show()
  ```
  ![https://blog.kakaocdn.net/dn/lbMRG/btsgKoRfZ14/LhugYpYAqxBGckxxPyKkr0/img.png](https://blog.kakaocdn.net/dn/lbMRG/btsgKoRfZ14/LhugYpYAqxBGckxxPyKkr0/img.png)
  3-3보다 미세하지만 성능이 올라갔습니다.
  하이퍼 파라미터 튜닝과 리소스 문제가 해결됐다면 좀더 성능을 올렸을수 있을것같습니다.
  다른  feature를 추가하여 FM의 성능을 관찰해 보겠습니다.
- 3-5
  기존 3-4 데이터를 이어서 하겠습니다.
  2-7분석을 기반으로 새로운 브랜드 feature를 만들어 봅니다.
  ```python
  sample_df['brand_feature'] = np.where((sample_df['brand_name'] == '그라운드시소')
                                          |(sample_df['brand_name'] == 'The Ordinary')
                                          |(sample_df['brand_name'] == '1507')
                                          |(sample_df['brand_name'] == 'ontz')
                                          |(sample_df['brand_name'] == 'TOPTEN')
                                          |(sample_df['brand_name'] == 'comfortlab')
                                          |(sample_df['brand_name'] == 'FLUKE')
                                          |(sample_df['brand_name'] == 'TAW&TOE_이누')
                                          |(sample_df['brand_name'] == 'maatila')
                                          |(sample_df['brand_name'] == 'BIARRITZ') , 1, 0)
  ```
  ```python
  epoch_loss = fit(X_sparse, y_data.values, config)
  ```
  ![https://blog.kakaocdn.net/dn/tRdZf/btsgE8aLK1E/ioLvXb9JzKGyRHKYXrrUY0/img.png](https://blog.kakaocdn.net/dn/tRdZf/btsgE8aLK1E/ioLvXb9JzKGyRHKYXrrUY0/img.png)
  ```python
  plt.plot(epoch_loss)
  plt.title('Loss per epoch')
  plt.show()
  ```
  ![https://blog.kakaocdn.net/dn/buGKes/btsgEC4hr1G/XRqBVDSRGITDykEeYNtI2K/img.png](https://blog.kakaocdn.net/dn/buGKes/btsgEC4hr1G/XRqBVDSRGITDykEeYNtI2K/img.png)
  정말 미세하게 3-4 보다 성능이 올라갔습니다.
  미비 하지만 분석이 틀리지 않다는걸 증명해주는것 같습니다.
- 3-6
  기존 3-5 데이터를 이어서 하겠습니다.
  2-2분석을 기반으로 남자 나이대 feature를 만들어 봅니다.
  ```python
  sample_df.loc[(sample_df['gender'] == 'M') &\
                 (sample_df['age(Group)'] == '25 ~ 35'), 'M_age_25_35'] = 1
  sample_df['M_age_25_35'] = sample_df['M_age_25_35'].fillna(0)

  sample_df.loc[(sample_df['gender'] == 'M') &\
                 (sample_df['age(Group)'] == '36 ~ 44'), 'M_age_36_44'] = 1
  sample_df['M_age_36_44'] = sample_df['M_age_36_44'].fillna(0)
  ```
  ```python
  epoch_loss = fit(X_sparse, y_data.values, config)
  ```
  ![https://blog.kakaocdn.net/dn/cP4t7N/btsgNWGWPRp/ugYGV2NkoKjUPKKPvs3kV0/img.png](https://blog.kakaocdn.net/dn/cP4t7N/btsgNWGWPRp/ugYGV2NkoKjUPKKPvs3kV0/img.png)
  ```python
  plt.plot(epoch_loss)
  plt.title('Loss per epoch')
  plt.show()
  ```
  ![https://blog.kakaocdn.net/dn/dxjipY/btsgMaeivfE/skW0u8FbxX5slrVoftWF60/img.png](https://blog.kakaocdn.net/dn/dxjipY/btsgMaeivfE/skW0u8FbxX5slrVoftWF60/img.png)
  성능이 미세하게 내려갔습니다.
  아마도 구매가 가장많은 여자 25~35가 아닌 구매율이 높은 남자 25~44 feature를 만들어서 그런가 추측됩니다.
  하이퍼 파라미터 튜닝이 안돼서 그런거 일수도 있고, 샘플링 과정 때문에 데이터의 오류가 생겼을수도 있습니다.
  추후에 다시한번 수정해 보겠습니다.

---

---

# 4. 프로젝트를 마치며

- 기존 추천시스템을 만들기 위해 E-commerce데이터를 활용한 프로젝트 보다 통계적 분석을 통해 추천시스템을 만들어 보니 데이터에 대한 이해도와 분석능력이 향상되어 제가 좀더 발전할수 프로젝트였던것 같습니다.
- 아쉬운점은 리소스 문제가 아니였다면 하이퍼 파라미터 튜닝과 샘플링을 하지않아서 좀더 신뢰도 높고 모델의 성능을 높이는 프로젝트가 되지 않았을까 생각됩니다.
- LTV분석에서 frequency를 기준으로 고객군을 분류했는데, 이것이 맞는것인지 검색해 보았지만 답을 찾을수 없었습니다. 그리하여 제가 생각하기 frequency가 맞다고 판단하였는데 잘 나눈것인지 아직도 궁금증에 있습니다.
