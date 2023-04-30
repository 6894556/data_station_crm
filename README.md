# Pandas 


## dtype별 확인할 사항들


### 숫자 데이터
- 평균: column별 대표값은 몇인가?
> df["col"].mean(axis=0)
- 합: column별 합은 얼마인가?
> df["col"].sum(axis=0)
- 요약 통계량으로 한번에
> [df.describe()](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.describe.html)
- df.select_dtypes(include=np.number).mean(axis=0), df.select_dtypes(include=np.number).sum(axis=0)


### 문자 데이터
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



##  structured data 
- 직접 입력
- 불러오기:
> - read_csv() : comma seperated value
> - read_excel()
> - keras.datasets
> - seaborn 팽귄



## Pandas data structure
- Series: index-value(dtype 통일)
- DataFrame: ser의 집합, index-value를 갖는 ser를 axis=1로 concat한 것 (vct, mtx)
structured data이므로 index-column-value를 확인한다.
> - df.info(): - df.columns와 df.index를 한번에 (+항목별 dtype) 확인, 구조와 타입을 확인
> - 





## [데이터의  형태 분류](https://deep-jin.tistory.com/entry/%EC%A0%95%ED%98%95-%EB%B0%98%EC%A0%95%ED%98%95-%EB%B9%84%EC%A0%95%ED%98%95-%EB%8D%B0%EC%9D%B4%ED%84%B0)
- 정형 데이터(Structured Data) : index-column-value e.g. csv, excel
- 반정형 데이터(Semi-Structured Data): e.g. Schema, Meta Data, json, xml, html
- 비정형 데이터(Unstructured Data): e.g. 영상, 이미지, 텍스트..
- 정형 비정형 차이: index-column-value 구조의 유무




## IT 관점에서 데이터를 처리하는 4단계
1. 데이터 수집: 장치를 통한 또는 직접 데이터를 입력
2. 데이터 저장: 수집된 데이터를 데이터베이스(DB)에 저장
3. 데이터 처리: 데이터 분석 목적에 맞게 / 사용 목적에 맞게 처리
4. 데이터 분석: 비즈니스 목적/목표 달성을 위한 분석 실시 (통계적 데이터 분석)
> 1. 기술적 데이터 분석(DDA)
> 2. 탐색적 데이터 분석(EDA)
> 3. 확증적 데이터 분석(CDA)
> 4. 예측적 데이터 분석(PDA)
 



