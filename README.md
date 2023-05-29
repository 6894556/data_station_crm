# data_station_crm

- crm:

### 4-4: 유통 데이터
#### 

### 4-3: Datetime

날짜 데이터
- 배송시작일
- 배송완료일
- 주문일
이 있다. 형식이 제각각이다.


- 변환
> df1["시간col1(datetime)"] = pd.to_datetime(df1["시간col1"], format="문자열 형식")
> - format: 날짜 데이터 string의 [format](https://docs.python.org/3/library/datetime.html#strftime-and-strptime-behavior)
> - %Y: Year with century as a decimal number.
> - %m: Month as a **zero-padded** decimal number.
> - %d:Day of the month as a **zero-padded** decimal number

- df1["주문일(dt)"] = pd.to_datetime(df1["주문일"], format="%Y%m%d")
- df1["주문연도"] = df1["주문일(dt)"].dt.year
- df1["주문월"] = df1["주문일(dt)"].dt.month
- 1년 52주 중 몇주차에 주문이 되었는가?
- df1["주문주차"] = df1["주문일(dt)"].dt.isocalendar().week
- df1["주문요일"] = df1["주문일(dt)"].dt.day_name()



#### 예제
- 요일별 주문량
> `sns.countplot(data=df1, x="주문요일")`
- 주문일별 상품구매금액의 총합
> `sns.lineplot(data=df1, x="주문일(dt)", y="상품구매금액", estimator="sum")`
- 일자별 주문수량
> `df1.pivot_table(index="주문일(dt)", values="수량", aggfunc="sum")`
- 주문수량이 가장 많았던 일자 확인
> - `df2 = df1.pivot_table(index="주문일(dt)", values="수량", aggfunc="sum")`
> - `df2.sort_values(by="수량", ascending=False).head(1)`





#### pivot table 
df.pivot_table(index="시간col1(datetime)", values="col2", aggfunc="sum")
- index: datetimeInde
- values: numeric
- aggfunc: sum, mean
- 주문일별 물품 주문 수량의 평균
> df1.pivot_table(index="주문일(datetime)", values="수량")
- 주문일별 물품 주문 수량의 총합
> df1.pivot_table(index="주문일(datetime)", values="수량", aggfunc="sum")




#### 예제
> - 요일별 주문량
> - 일자별 수량합계

- 배송시작일
- 배송완료일

### 4-2: Outliers
 
- 확인 방법
> - df.describe의 col별 min, max 값 확인 (이상치 조건 찾기)
> - cond1 = (df["col1"] 이상치 조건)
> - df.loc[cond1] <br>

처리 방법(col 단위)
- 제거
> - cond1 = (df["col1"] 이상치 조건(연산자, 값))
> - df2 = df.loc[~cond1]

- 다른 값으로 대체
> - cond1 = (df["col1"] 이상치 조건)
> - df["col1(clean)"] = df["col1"] <br>
> - df.loc[cond1, "col1(clean)"] = 대체값
> - df.loc[cond1] 

- NaN으로 대체
> - cond1 = (df1["col1"] 이상치 조건)
> - df1.loc[cond1, "col1"] = np.nan
> - cond2 = (df1["col1"].isnull())
> - df1.loc[cond2]



### 4-1: Missing Values 

결측치 확인 -> 결측치 처리(dropna or new col) -> 처리된 df 사용 & 분석

- 확인 방법
> - **df.info()** : Non-Null Count는 col별 null이 아닌 val의 개수
> - **df.isnull()** : df의 val별 null 여부 확인 (if val is null, returns True)
>> **df.loc[df["col1"].isnull()]** : 특정 col에 결측값이 있는 sample만 filtering
>  **df.isnull().sum()** : df의 col별 null-count 확인
>> **[df.sum()]**(https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.sum.html)


- 처리 방법
> - **df.dropna()** : any row with null val을 drop (axis=0으로)
>> 데이터 loss가 너무 크다는 단점 존재
> - **df.fillna()** : df의 모든 null val을 대체할 값 지정
>> - **df["col1"].fillna()** : ser의 모든 null val을 대체할 값 지정
>> - df["col1(clean)"] = df["col1"].fillna(대체값)
>> - df["col1"].describe()의 mean 값을 긁어서 df["col1"].fillna(여기에 넣는다.)
>> - df2 = df.drop(columns=["col1"]): col1은 df2에 필요 없으므로 drop


### 3-4: VIP실습 
filtering -> 새 df -> to_excel
1. VIP이고 모바일 알람 수신하는 회원 filtering
2. VIP이고 총구매금액 상위 10명 filtering
3. VIP이고 입력 받은 지역 filtering
4. VIP이고 입력 받은 연령대 filtering 하고 총구매금액이 높은 고객 순으로 sorting해서 저장
5. 총구매수량이 100건 이상이고, 총구매금액 상위 50명 filtering
6.  Mission
> 1. Address항목에서 '수지구'에 사는 고객들의 VIP와 Member 수를 확인해 보세요!
> 2. Gender항목에 '남성' 고객들의 VIP와 Member수를 확인해 보세요!
> 3. 배송서비스신청여부를 '신청' 또는 '미신청' 값을 입력하여, '총구매수량'이 높은 상위 100명의 명단을 엑셀 파일로 추출하는 코드를 구성해보세요!

**ch3 핵심: indexing, sorting, filtering**



- Data Cleansing : 데이터를 깔끔하게 다듬는 작업
> Data Cleaning의 첫 단계가 결측값 처리이다.
- 머신러닝 기법, 통계적 가설검정 기법을 원활하게 사용하기 위해서는 결측치를 반드시 처리해야 한다. 
- 결측값 종류: - NaN (Not a Number) / None / NA



### 3-3: Filtering
cond for condition(조건)
- cond1  = (df["col"] 논리연산자1 col type의 값1)
> df.loc[cond1]
- cond2  = (df["col"] 논리연산자2 col type의 값2)
> df.loc[cond1 & cond2]
- cond3  = (df["col"] 논리연산자1 col type의 값1) & (df["col"] 논리연산자2 col type의 값2)
> df.loc[cond3]
> df.loc[~cond3]

- 예제
> 1. Contract_Type이 교체 계약인 고객 데이터만 filtering
> 2. Overdue_count가 5회 이상인 고객 데이터만 filtering
> 3.  Address1이 경기도이면서 Overdue_count가 5회 이상인  고객 데이터 filtering
> 4. Age가 30 미만이면서 Overdue_count가 5회 이상인 고객 데이터 filtering
> 5. Address1이 경기도이거나 충청도인 고객 데이터만 filtering
> 6. Sales_Type이 렌탈이고, Overdue_count가 5회 이상인 고객 데이터만 filtering 하고 Overdue_count 기준으로 descending sort했을 때 상위 10명
> 7. Sales_Type이 렌탈인 고객 들 중에서 Overdue_count 상위 10명 고객 데이터 filtering



### 3-2: Sorting

#### 숫자 데이터 Sorting 
- df.sort_values(by="col") : col 한개에 대해서 ascending sort(정렬)
> - df.sort_values(by="col", ascending=False) : descending sort (내림차순) 
- df.sort_values(by=["col1", "col2"]) : 선 col1 기준으로 ascending 정렬, 후 col2 기준으로 ascending 정렬
> - df.sort_values(by=["col1", "col2"], ascending=False) : 선 col1 기준으로 descending 정렬, 후 col2 기준으로 descending 정렬
- df.sort_values(by=["col1", "col2"], ascending=[False, True]) : 
- \ (back slash) 넣으면 줄바꿔도 코드 작동한다.
- 함수를 조합해서 우리가 원하는 형태로 데이터를 추출하는 방법
> - 선 indexing, 후 sorting
> - 선 sorting, 후 indexing



### 3-1: indexing
#### 행 단위 indexing 
df로만 return
> - df.head(n): 상위 n개 행 추출
> - df.tail(m): 하위 m개 행 추출
> - df.iloc[row_idx_n : row_idx_m]: m행부터 n행까지 행 추출 (iloc : index location) (list sclicing과 같은 logic)
### 열 단위 indexing 
ser 또는 df를 return
> - df["col"]: col열만 ser 형태로 추출
> - df[["col"]]: col열만 df 형태로 추출
> - df[["col1", "col2"]]: col1, col2 열을 df 형태로 추출

- 실무 조언: 
> 1. 공장에서 data를 받아오면, 열 개수만 50개가 넘는 경우가 많다. 
이런 상황에서, 내가 분석할 수 있는 데이터만 indexing으로 가져와서 분석하는 것이 가장 효과적이다. 
그래야 보기도 쉽고 다루기도 어렵지 않다. 실무에서는 indexing해서 분석해야 한다.
> 2. 여러 함수를 조합해서 사용할 수 있다. (선후관계)
>> - df1[["Product_Type", "Bank"]].head(1000) : 선 지정 열 2개, 후 상위 행 1천개
>> - df1.head(1000)[["Product_Type", "Bank"]] : 선 상위 행 1천개, 후 지정 열 2 개 



### dtype별 확인할 사항들


#### 숫자 데이터
- 평균: column별 대표값은 몇인가?
> df["col"].mean(axis=0)
- 합: column별 합은 얼마인가?
> df["col"].sum(axis=0)
- 요약 통계량으로 한번에
> [df.describe()](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.describe.html)
- df.select_dtypes(include=np.number).mean(axis=0)


#### 문자 데이터
- 항목 : 어떤 value가 있는가? 
> df["col"].unique()
- 빈도수: value별 출현 frequency
> df["col"].value_counts()
- 요약 통계량으로 한번에
> df.describe(inclue="object")
>> - count: 개수
>> - unique: unique value의 개수
>> - top : unique value 중 가장(top) 빈도 수가 높은 object type의 value(최고 빈도수를 갖는 value)
>> - freq : 가장 빈도수가 많은 unique value의 frequency(만 출력)



###  structured data 
- 직접 입력
> - Series : pd.Series([a1, a2, ..., an]), 단 {an}은 같은 dtype (**Python List**)
> - DataFrame : pd.DataFrame({"key1": [value11, value12, ..., value1N], "key2":[value21, value22, ..., value2N]}) (**Python Dictionary**)
- 불러오기:
> - read_csv() : comma seperated value
> - read_excel()
> - keras.datasets
> - seaborn 팽귄



### Pandas data structure
- Series: index-value (value의 dtype 1개)
- DataFrame: ser의 집합, index-value를 갖는 ser를 axis=1로 concat한 것 (vct, mtx)
structured data이므로 index-column-value를 확인한다.
> - df.info(): - df.columns와 df.index를 한번에 (+항목별 dtype) 확인, 구조와 타입을 확인
> - 





### 데이터의  형태 분류
- 정형 데이터(Structured Data) : index-column-value e.g. csv, excel
- 반정형 데이터(Semi-Structured Data): e.g. Schema, Meta Data, json, xml, html
- 비정형 데이터(Unstructured Data): e.g. 영상, 이미지, 텍스트..
- 정형 비정형 차이: index-column-value 구조의 유무




### IT 관점에서 데이터를 처리하는 4단계
1. 데이터 수집: 장치를 통한 또는 직접 데이터를 입력
2. 데이터 저장: 수집된 데이터를 데이터베이스(DB)에 저장
3. 데이터 처리: 데이터 분석 목적에 맞게 / 사용 목적에 맞게 처리
4. 데이터 분석: 비즈니스 목적/목표 달성을 위한 분석 실시 (통계적 데이터 분석)
> 1. 기술적 데이터 분석(DDA)
> 2. 탐색적 데이터 분석(EDA)
> 3. 확증적 데이터 분석(CDA)
> 4. 예측적 데이터 분석(PDA)
 

### Data Sation Class 101 
- Numpy : 2010 이전 (2008) 
- Pandas: 2010 이후
