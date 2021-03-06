# [2019 서울시 빅데이터 캠퍼스](https://bigdata.seoul.go.kr/cntst/selectCntst.do?r_id=P610&use_type_code=30&cntst_seq=45&cntst_se_code=&file_id=&sch_type=&sch_text=&currentPage=1) 공모전 (2019.09.02 ~ 11.29)
  - 서울시 빅데이터캠퍼스의 데이터를 주요 데이터로 활용하여 분석한 내용으로 자유주제
  - 데이터 마이닝 수업시간에 진행한 Final Project 발전시켜 참가

## 분석 주제 및 목적 
- 분석 주제 : 카페 최적입지 선정을 위한 조건 
    - 서울시 카페 폐업률 예측을 통한 상권분석
- 분석 배경 : 한국인의 1인당 커피 소비량이 증가하며 커피 시장은 커지고 있으며, 창업자들 사이에서도 '카페'는 가장 선호하는 업종이 되었지만 카페는 이미 포화상태, 카페 창업자에게 좀 더 나은 환경을 제공해보자 
- 분석 목적 
1. 카페 창업자에게 오래 장사할 수 있는 적절한 위치 및 입지 조건 추천해주기
2. 기존 카페 점주에게는 폐업 위험률 알려주기 

### 우리 '임앤김스'의 세부 목적을 정리하면, 
- "어떤 변수가 폐업률에 영향을 많이 줄까?" 를 탐색하기 위해 최대한 많은 변수를 모델에 반영했다.
     - 그만큼 사용한 데이터 셋이 13개 정도, 총 변수는 약 41개 정도로 변수 생성 및 데이터 핸들링에 공을 많이 들였다.
- 분석 시 크고 작은 선택을 할 때마다 분석 결과를 사용할 사람의 입장에서 고려하여 선택했다.
-----------------------------------

## 분석 과정 
### 1.분석 사용 변수
#### 1. 카페 개별 특성 변수

| 변수명      | 생성 기준    | type  |
| :-------------: |:-------------:| :-----:|
| 층 구분 | 지하, 지하+지상, 1층, 1층+2층 이상 | 범주형 |
| 총 층수 | 매장 총 층수    | 연속형 |
| 총 면적 | 단위:𝑚^2      | 연속형 |
| 프랜차이즈 여부 |   | 범주형 |
| 월,계절 | 개업시점  | 범주형 |

#### 2. SNS 변수
- 입소문 효과를 나타내는 SNS 변수를 생성하기 위해 네이버 블로그 건수를 크롤링

| 변수설명      | 변수 생성 기준    | type  |
| :-------------: |:-------------:| :-----:|
| 블로그 건수 | 월 단위, 지하철역 위도, 경도 1000m 이내 | 연속형|

#### 3. 주변 상권 변수

| 변수설명      | 변수 생성 기준    | type  |
| :-------------: |:-------------:| :-----:|
| 주변 음식점 수 | 개업시점 이전 달, 도로명별 | 연속형|
| 주변 카페 수 | 개업시점 이전 달, 도로명별 | 연속형 |
| 주변 프랜차이즈 카페 수 | 개업시점 이전 달, 도로명별 | 연속형 |
| 주변 스타벅스 카페 수 | 개업시점 이전 달, 도로명별 | 연속형 |
| 이전 달 새로 생긴 카페 수 | 개업시점 이전 달, 500m 이내  | 연속형 |

#### 4. 지역특성 

| 변수설명      | 변수 생성 기준    | type  |
| :-------------: |:-------------:| :-----:|
| 행정구 | 총 25개 | 범주형 |
| 유동인구, 직장인구, 주거인구| 행정동별, 개업시점 이전 분기 | 연속형 |
| 여성인구 비율| 행정동별, 개업시점 이전 분기 | 연속형 |
| 가구 수, 소득분위| 행정동별, 개업시점 이전 분기 | 연속형 |
| 임대 시세 | 행정동별,개업시점 이전 분기,층별, 3.3𝑚^2당 월환산 임대료|연속형 |
| 월 평균 매출액 | 행정동별, 개업시점 이전 분기|연속형 |
| 횡단보도 수 | 50m 이내 | 연속형 |
| 주차장 수  | 500m 이내 | 연속형 |
| 지하철 수 | 1000m 이내 | 연속형 |
| 집객 시설(은행,병원,학교,버스정류장 등) | 행정동별 | 연속형 |

#### 5. 경제 상황

| 변수설명      | 변수 생성 기준    | type  |
| :-------------: |:-------------:| :-----:|
| KOSPI 주간 변화율 | 개업시점 기준, 1/2/3주 전 | 연속형 |
| KOSPI 월간 변화율 | 개업시점 기준, 1주 전| 연속형 |
| KOSPI 연속감소주 | 개업시점 기준연속형 |연속형 |

### 2. 행정구별 Clustering
- 전체 행정구를 대상으로 모델링을 진행해 본 결과, Random Forest가 가장 우수했고 변수 중요도 그래프를 그렸을 때, '행정구' 영향이 가장 컸음
- 따라서 행정구별 폐업률에 차이가 커 행정구별 특성을 상쇄하기 위해 같은 특성의 행정구를 군집화함
- Clustering을 진행하기 전 기준 변수에 대한 고민을 했고, 소상공인의 입장에서 창업 전 가장 중요하게 여기는 '임대료', '매출액'을 기준으로 군집화함
- ![](https://user-images.githubusercontent.com/45617225/89878687-d6310600-dbfc-11ea-9d1a-04090aeb61d7.png)



### 3. Modeling
#### 1. 1년 내 폐업할 카페 예측 및 폐업 원인 분석
- 2015년 4월 ~ 2018년 5월에 폐업한 8312개 카페에 대해서 총 영업 일수 그래프를 그려본 결과 1년, 2년 단위로 다수 폐업하는 것을 알 수 있었음
<p align="center">
  <img src="https://user-images.githubusercontent.com/45617225/89879249-9b7b9d80-dbfd-11ea-81a9-005fc452afc4.png">
</p>
- 이는 임대차 계약이 1년 단위로 이루어지기 때문이라고 짐작했으며, 1년 영업 후 폐업한 곳이 가장 높았기 때문에 우리의 반응변수(Y)를 '1년 내 폐업 여부' 로 설정함
- Logistic Regression(Ridge, Lasso), SVM, Random Forest 등 분류 모형 이용해 적합 후 해석

#### 2.생존분석을 통한 생존확률 제시 
- 2015년에 개업한 카페 대상으로 Cox-PHM 모형을 통한 위험률 및 생존확률의 변화 예측
- 아래는 시뮬레이션을 통한 생존 확률의 변화
<p align="center">
  <img src="https://user-images.githubusercontent.com/45617225/89879416-e3022980-dbfd-11ea-9c9d-2aec622a81a1.png">
</p>

### 4. 서비스 활용 방안 제시 
#### 1. 카페 창업자에게 오래 장사할 수 있는 적절한 위치 및 입지 조건 추천해주기
- Random Forest 모형 적합 후 Partial Dependence Plot(머신러닝 모형에서 개별적인 설명변수가 예측 결과에 미치는 영향을 나타냄)을 바탕으로 입지 추천해주기
- 서울시에서 창업 교육 시 입지 추천 컨설팅을 통해 폐업률이 낮은 좋은 위치를 추천해줘서 오래 장사할 수 있는 환경 제공하기
<p align="center">
  <img src="https://user-images.githubusercontent.com/45617225/89878723-e0eb9b00-dbfc-11ea-9f78-f7ac585ee594.png">
</p>


#### 2. 기존 카페 점주에게는 폐업 위험률 알려주기

- 기존 점주에게는 폐업률을 알려주며, 서울시에서 폐업 위험도가 높은 카페를 대상으로 추첨을 통해 지역 인테리어 업체와 제휴하여 무료 리모델링 행사를 진행하기
- 카페 소상공인, 지역 인테리어 업체 모두 윈윈하는 효과를 얻을 수 있고, 지역 경제 활성화에 기여할 수 있음
<p align="center">
  <img src="https://user-images.githubusercontent.com/45617225/89878753-eba63000-dbfc-11ea-902c-98e41a7ecc0c.png">
</p>

##### 분석 도구 
- R : dplyr, caret, randomForest, range, h20, ROSE, RANN, xgboost, e1071, glmnet, geosphere 등
- Python : Numpy , Python, re, Selenium, Beautifulsoup 등

-----------------------------------

### 멘토링 데이(2019.11.09)
- 본선 진출자 대상으로 데이터 현직 분석가 등으로 이루어진 멘토분들의 피드백
- 기억에 남는 피드백 : 만약 이 분석 결과를 실제 회사에서 자영업자를 대상으로 실제 컨설팅에 이용된다고 가정해봐라. 임앤김스 팀이라면 실제로 그 위치에 창업을 할 수 있겠는가? 구별 클러스터링을 진행했는데 단위가 너무 크다고 생각한다. 
- 그 외 피드백 : 직관적으로 이해 가능한 해석을 제공한다면 좋을 것 같다. (가령 횡단보도 수에 따른 PDP를 제시했는데 1~3개일 때 생존율이 높고 4개 이상일 때는 생존율이 낮은데 원인을 함께 제시해주면 좋을 것 같다.)

##### 느낀점 
> 이후에 이 멘토링 받았던 부분에 대해서 생각해보니 실제 상황에서 활용도를 많이 평가해주셨더 것 같다. 실제로 구별로 모델링 했는데 그 부분도 약간 현실성이 부족했다고 생각한다. 차라리 유동인구 기준이나, 매출액 임대료 기준으로 최대한 작은 행정동 단위로 묶어서 각각을 모델링했더라면 더 나은 의미가 있었을 수도 있겠다.!! 


### 본선 발표(2019.11.29) - 결과 : 장려상 수상
<p align="center">
  <img width="450" height="350" src="https://user-images.githubusercontent.com/45617225/89878476-8ce0b680-dbfc-11ea-8055-708932120a6e.png">
</p>





