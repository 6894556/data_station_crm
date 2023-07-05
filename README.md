# data_station_crm

- crm:
- 각 장의 마지막 절은 실전연습이다.
- jupyter 단축키
> - tab : 자동완성
> - shift + tab : 도움말
> - esc: mode 전환
> - a : above에 add cell
> - b : below에 add cell
> - View / Toggle Line Numbers
> - In[n]에 작성하는 code도 중요하지만 Out[n]의 결과도 중요하다.
>> 선 In[n]에 code 작성, 후 Out[n] 결과 독해.

## 5. 데이터를 합쳐서 분석하면 어떻게 될까?





### 5-4 [pd.DataFrame.melt]
`df.melt(id_vars)`
> `df.melt(id_vars).rename(columns={"col1": "col1*"})`

#### 1 
[제품명, 날짜] col만 남긴 `df` 사용
- 목표: 제품명별 매출액 barplot

#### 2
[날짜] 제외한 모든 col을 `melt(id_vars)`에 집어넣은 `df` 사용
- 목표: 최대 19년도, 20년도 12월 매출액 증감률 갖는 제품명 


### 5-3 [pd.DataFrame.pivot_table](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.pivot_table.html)

#### Big Picture
pivot_tabel(index, columns, values, aggfunc)
> argument에 list를 넣을 수 있다.
- 정확한 제품별 매출 증감액 & 증감률 구하기
- 이번 시간: `pivot_table`로 데이터를 요약 & 정리
- 다음 시간: `melt`로 정리 & 요약된 데이터를 풀기
- 데이터를 뽑았을 때 정리된 형태로 뽑혔다면, 데이터를 풀어헤쳐야 한다. 
> 이 때, `melt`를 사용한다.

#### ?
01. Channel별 Amount_Month의 mean
02. Channel과 Gender별 Amount_Month의 mean
03. Gender와 Channel에 따른 Amount_Month의 sum
04. Gender와 Channel에 따른 Amount_Month와 Overdue_count의 sum
05. Gender와 Channel에 따른 Amount_Month와 Overdue_count의 sum, min, max 
06. Datetime별 Sales_Type에 따른 Amount_Month의 sum
> 577 columns 너무 많다. DateTimeIndex 처리해서 월별, 연도별 매출액 계산해보자.
07. Channel에 따른 월별 Amount_Month의 sum
08. Sales_Type에 따른 월별 Amount_Month의 mean
09. Sales_Type에 따른 월별 Amount_Month의 sum (19년, 20년 구분 없는 월별 매출의 추이)
10. Sales_Type에 따른 연도별, 월별 Amount_Month의 sum (19년, 20년 구분 없는 월별 매출의 추이)
11. Bank에 따른 월별 Amount_Month의 sum
12. Bank에 따른 월별 Amount_Month의 sum과 월별 합 (margins=True)
13. Product_Type에 따른 Amount_Month의 sum과 전체 합 (제품별합, 전체합)
14. Product_Type에 따른 연도별 Amount_Month의 sum (제품별합, 전체합) 
15. Product_Type에 따른 연도별 Amount_Month의 차이 (19년도 -> 20년도 증감액)
16. 19년도 1-8월 데이터와 20년도 전체 데이터만 추출(filtering)하기
17. 19년도 1-8월, 20년도 전체(1-8월) product type에 따른 연도별 Amount_Month의 sum
18. 1-8월 증감액 & 증감률
> - `증감률 = 증감액 / 전년도 + 이번년도`
> - 실제로 19년 대비 매출이 약 50%정도 대폭 감소했다. 


- index와 values, aggfunc에 list 형태 구조를 집어 넣을 수 있다.
- 동시에 2개의 범주형 항목으로 데이터를 나누어 2개의 숫자형 항목 데이터에 대한 통계량 계산
- aggfunc: default는 `numpy.mean`이다.
- `margins=True`: 총합 계산
> All이 index 마지막에 붙는다. 모든 은행 총합 값 (강력한 기능)
- Datetime: 영업일자, 계약일자 (csv 파일별 description 페이지가 필요함)
- erp(전사적 자원 관리): 데이터가 모여있는 공간
- 실무에서 많이 보는 항목: 전년 대비 매출 증감률, 고객 증감률
- pivot_table을 잘 사용하면, 다양하고 많은 데이터에서 데이터를 항목별로 나눠서, 특정 값에 대한 통계량을 확인하면서, 인사이트를 얻을 수 있다. 
- `df3[2020] - df3[2019]`: dt index가 아니라 col이름
> 제품별 매출 증감액 (음의 값이므로 줄었다.)
- 정확한 제품별 매출 증감액
> 19년도는 1-12월 데이터가 있지만 20년도는 1-8월 데이터만 있으므로 1-8월 증감만 계산해야 정확한 **증감액**과 **증감률**을 계산할 수 있다.
- 19년도, 20년도에 몇월부터 몇월까지의 데이터가 있는지 확인하는 방법:
> - 20년도:`cond1 = (df1["Year"] == 2020) df1.loc[cond1]["Month"].unique() # 20년도는 정확히 1-8월 데이터만 있음`
> - 19년도: `cond1 = (df1["Year"] == 2019) df1.loc[cond1]["Month"].unique() # 19년도는 정확히 1-12월 데이터가 있음`
- 19년도 1-8월 데이터와 20년도 전체 데이터만 추출(filtering)하기
> - `cond1 = df1["Year"] == 2019 cond2 = (df1["Month"] <= 8) cond3 = (df1["Year"] == 2020) df4 = df1.loc[(cond1 & cond2) | cond3]`
- 증감률이 0보다 작다는 것은 매출액이 줄었다는 것을 뜻한다.


### 5-2: [pd.merge](https://pandas.pydata.org/docs/reference/api/pandas.merge.html?highlight=merge#pandas.merge)

pd.merge(df1, df2, on, how)
> - on: key col
> - how:

- df1에는 고객 4396명 정보가 있다.
- df3에는 고객 1383명 정보가 있다. 
- df3 `회원번호`는 df1 `회원번호`의 subset 인가? 
- `구매금액`과 `구매수량` col은 고객 1383명만 있다.
- df3을 구매특성 df라고 하자.
- index는 key col로 사용할 수 없다.
> index를 key col로 사용하기 위해 `reset_index` 사용한다.
- 어차피 분석하려면 missing val 지워야 한다. 
> 따라서 inner join으로 병합한 df 사용하자.
- 그냥 붙이면 중복되거나, 누락되는 일이 발생할 수 있다.
> 데이터 구조, 타입에 맞는 적절한 병합 방법을 우리가 찾아서 사용해야 한다.

### 5-1: [pd.concat](https://pandas.pydata.org/docs/reference/api/pandas.concat.html)

- 모든 df의 행이 같을 때 사용 가능.
- ppt 테이블 screenshot 필요
- 집합 {df_0, df_1, ..., df_n}는 for every n in index에 대해서 same columns를 갖는다고 가정
- csv 4개가 있다.
- df 4개로 불러올 수 있다.
- df 4개를 df 1개로 묶고 싶다.
- df 4개 col이 같다.
- df 4개 파일명이 비슷하다. 
- `pd.DataFrame.shape`으로 어떻게 concat 되었는지 쉽게 확인할 수 있다.


```python
df_raw = pd.DataFrame()
for i in [str(i) for i in range(1, 5)]:
    df_ = pd.read_csv("dataset/2016_health_checkup_" + i +".csv")
    print(f'{i}: {df_.shape}')
    df_raw = pd.concat([df_raw, df_])
    
# loop 이해
# empty, 1
# empty1, 2
# empty12, 3
# empty123, 4
```




### 4-4: 유통 데이터 실습

#### ?
- pd.Series.replace({'기존val': '대체할val'}) (단, dtype=object 전제? (since `replace`))
- sns.barplot(estimator, hue) 
> - estimator : pivot_table(aggfunc)와 유사 
> - hue: 문자 col을 지정하면 unique값별로 나눠줌 
> - 정확하게는 모르겠음
- pd.Series.apply(func)
> - ser의 value가 func1에 하나씩 들어간다. 
> - val 조작을 위한 함수를 만들어야 함
> - 따라서 ser의 dtype이 중요.
> - str.split(' ') : ' ' 공백 기준으로 split해서 list로 return
- pd.Timedelta
> - **timedelta**: 격차, 양을 나타내는 타입, 데이터 간의 간격을 구하는 타입, 시간 간의 차이를 계산
> - str "7 days"가 Timedelta로 자동 변환되서 df1.loc[df1["배송기간"] > "7 days"]도 가능
- pd.Series.astype(int)
> ser의 val을 int 타입으로 변환



#### 주중/주말 col
> - df1["주중/주말"].unique()에서 요일 값을 긁어 오면 편하다
> - `df1["주중/주말"] = df1["주문요일"].replace({'Thursday': '주중', 'Saturday': '주말', 'Sunday': '주말', 'Monday': '주중', 'Tuesday': '주중', 'Wednesday': '주중', 'Friday': '주중'})`
##### pivot_table
sum의 차이는, 일주일 중 주중이 5일, 주말이 2일이라는 점에 의한 차이일 수 있다. 따라서 건당 mean을 살펴보자.
- (주문 당) 상품구매금액의 평균: 비슷하다. 
##### sns.barplot
`estimator`와 `hue`가 핵심
- 매출액비교
> - `sns.barplot(data=df1, x="주중/주말", y="상품구매금액", **estimator="sum"**, errorbar=None)`
- 주문경로별 매출액 비교
> - `sns.barplot(data=df1, x="주중/주말", y="상품구매금액", estimator="sum", errorbar=None, **hue="주문경로"**)` 
> - **Insight**: 주중/주말에 따라서 주문경로에 차이가 있다. 주말에는 모바일웹 매출액이 가장 높다.

#### 배송기간 col
col 만들기: apply(적절한 함수 만들기) -> to_datetime
- 배송시작일(dt) 
> - 오전 8:56 format 이다. 따라서 pd.to_datetime 사용 못한다. 
> - 그래서 pd.Series.apply(func) 사용한다.
- 배송완료일(dt): 배송시작일과 동일한 방법 적용
- 배송기간(td)
> - '배송시작일'과 '배송완료일' col이 datetime 타입이라서 subtract 가능
> - subtract후 return 되는 pd.Series의 val이 pd.Timedelta 타입이다.

##### filtering
- 배송기간이 4days 보다 오래 걸린 데이터 filtering
> string "4 days"는 pd.Timedelta(days=4)로 pandas에 의해 자동변환 된다.
- 배송기간 > 7일인 데이터 filtering
> 7일 이상 걸린 배송만 to_excel

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
- `to_excel`: 파일로 저장해서 갖고 있으면, 나중에 처리 작업을 반복하지 않아도 된다. 매번 처리 작업을 반복하지 않아도 된다.

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
