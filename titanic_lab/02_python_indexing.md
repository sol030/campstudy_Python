**데이터 수집: CSV/엑셀/DB/API에서 가져오기전처리(정제): 오류/누락/중복/형식 통일탐색(EDA): 분포, 관계, 이상치 확인분석/모델링: 규칙 찾기, 예측리포트/시각화: 요약표/그래프/인사이트 전달**

- **데이터 수집**: CSV/엑셀/DB/API에서 가져오기
- **전처리(정제)**: 오류/누락/중복/형식 통일
- **탐색(EDA)**: 분포, 관계, 이상치 확인
- **분석/모델링**: 규칙 찾기, 예측
- **리포트/시각화**: 요약표/그래프/인사이트 전달

**전처리가 중요한 이유**

- “계산 가능한 숫자”가 아니라 “문자열 숫자(4,500원)”면 **합계/평균이 안 됨**
- 날짜 형식이 섞이면 **기간별 분석이 꼬임**
- 결측/중복이 있으면 **통계가 왜곡됨**


```python
import pandas as pd

#데이터 구조 확인
df = pd.DataFrame({
    "date":  ["2026-01-01","2026-01-02","2026-01-03","2026-01-04","2026-01-05"],
    "store": ["A","A","B","A","B"],
    "menu":  ["Latte","Americano","Mocha","Latte","Americano"],
    "price": [5000, 4500, 5500, 5000, 4500],
    "qty":   [1, 2, 1, 3, 1],
    "paid":  [True, True, False, True, True]
})

df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01-01</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2026-01-02</td>
      <td>A</td>
      <td>Americano</td>
      <td>4500</td>
      <td>2</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2026-01-03</td>
      <td>B</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>1</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2026-01-04</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>3</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2026-01-05</td>
      <td>B</td>
      <td>Americano</td>
      <td>4500</td>
      <td>1</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



###항상 처음은 데이터 구조를 확인

**이 단계에서 확인할 것**
* 컬럼 이름이 의미 있는지
* True / False 조건 있는지
* *인덱스가 기본 숫자인지

### **) 인덱싱이 중요한 이유**

인덱싱은 한마디로 **데이터프레임에서 필요한 부분만 정확히 꺼내는 기술**.

전처리에서 가장 많이 하는 일은 결국 아래 3가지로 반복된다.

**1) 필요한 행만 가져오기(Row 선택)**

실제 데이터는 대부분 “전체”를 분석하지 않는다.

예를 들어 카페 매출 데이터라면

- 특정 날짜(어제/이번 주)만
- 특정 지점(A점)만
- 결제 완료 건만
- 이상치(너무 큰 금액)만

즉, **질문에 해당하는 행만 정확히 뽑아야** 분석이 시작됨.

행 선택이 잘못되면(결제 실패 섞임, 기간 섞임 등) **매출/통계가 통째로 왜곡**될 수 있음.

---

**2) 필요한 열만 남기기(Column 선택)**

실무 데이터는 컬럼이 많고, 초보자일수록 불필요한 컬럼 때문에 헷갈린다。

분석에 필요한 핵심 컬럼만 남기면:

- 작업이 단순해지고
- 실수(컬럼 혼동)가 줄고
- 정제/집계/시각화가 더 빨라집니다.

또한 협업할 때도 “필요한 컬럼만 남긴 버전”을 공유하는 습관이 중요함。

---

**3) 조건에 맞는 데이터만 보기(Filter)**

분석은 보통 **비교(지점별/시간대별/메뉴별)** 가 핵심인데, 비교를 하려면 먼저 조건으로 데이터를 나눠야 한다.

- A점 vs B점
- 오전 vs 오후
- 평일 vs 주말

즉, **필터링은 비교 분석의 출발점**.

이 조건을 정확히 구현하는 능력이 곧 인덱싱 실력임.

---

**한 줄 결론**

**인덱싱은 “분석의 정확도”를 결정하는 기본기**.

행/열/조건을 제대로 못 뽑으면, 뒤에서 어떤 통계를 내도 결과가 흔들릴 수 있다.


```python
import pandas as pd

df1 = pd.DataFrame(
    {"menu": ["Latte", "Americano", "Mocha"], "price": [5000, 4500, 5500]},
    index=["A001", "A002", "A003"]
)

df1  # <- 마지막 줄이라 자동 출력`

df1.loc["A002"]  # <- 라벨로 찾기(자동 출력)`

df1.iloc[1]      # <- 위치로 찾기(자동 출력)`
```




    menu     Americano
    price         4500
    Name: A002, dtype: object



#**.loc vs .iloc** 핵심 개념

.loc : 라벨(이름표)
.iloc : 순서(위치 번호)


같은 행을 다른 방식으로 꺼내보기
df.loc[0]    # 라벨 0
df.iloc[0]   # 위치 0


##**조건 필터링**의 진짜 구조!
**조건 필터링은 항상 2단계이다**
* ① True / False 필터를 만든다
* ② 그 필터로 df에서 True만 고른다


```python
#라벨 기준 → .loc
df.loc[0]
```




    date     2026-01-01
    store             A
    menu          Latte
    price          5000
    qty               1
    paid           True
    Name: 0, dtype: object




```python
#위치 기준 → .iloc
df.iloc[0]
```




    date     2026-01-01
    store             A
    menu          Latte
    price          5000
    qty               1
    paid           True
    Name: 0, dtype: object



### 언제 .loc / .iloc을 쓰는 게 자연스러운가?

**.loc이 자연스러운 상황**

- 조건 필터링과 같이 쓸 때 (가장 흔함)
    - “결제 완료(True)인 행만 + 필요한 컬럼만”
- 인덱스/컬럼 이름이 의미가 있을 때
    - 예: 날짜가 인덱스인 경우(“2026-01-01” 같은 라벨)
    - 예: df.loc["2026-01-01":"2026-01-07"]처럼 라벨 범위로 자를 때
* .loc 슬라이싱은 “끝을 포함”하는 경우가 많다
---
**.iloc이 자연스러운 상황**

- “위에서 몇 줄만 보기”
    - 예: 첫 5행, 특정 구간(0~9행)
- 위치 기반으로 정확히 잘라야 할 때
    - 예: df.iloc[0:10] (처음 10개)
- 컬럼 이름이 헷갈릴 때 “몇 번째 열”로 빠르게 확인할 때
    - 예: df.iloc[:, 0] (첫 번째 열)
* .iloc 슬라이싱은 파이썬 규칙대로 “끝 미포함”


```python
#필터 먼저 만들기(마스크)
mask_paid = df["paid"] == True
mask_paid
# >>> 이때 결과는 True / False의 나열

#필터 적용
df[mask_paid]
# >>>  “결제 완료된 주문만” 남음
#통계 왜곡 방지의 핵심 단계
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01-01</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2026-01-02</td>
      <td>A</td>
      <td>Americano</td>
      <td>4500</td>
      <td>2</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2026-01-04</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>3</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2026-01-05</td>
      <td>B</td>
      <td>Americano</td>
      <td>4500</td>
      <td>1</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



##항상 이 순서를 기억하자

**1. 조건 -> True / False**
**2. 그걸로 df 걸러냄**


```python
##조건 + 컬럼 선택
df.loc[mask_paid, ["date", "store", "menu", "qty"]]


```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>store</th>
      <th>menu</th>
      <th>qty</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01-01</td>
      <td>A</td>
      <td>Latte</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2026-01-02</td>
      <td>A</td>
      <td>Americano</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2026-01-04</td>
      <td>A</td>
      <td>Latte</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2026-01-05</td>
      <td>B</td>
      <td>Americano</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



###loc 패턴 암기
```
df.loc[조건, [컬럼들]]
```


```python
###다중 조건 필터링
#1. 조건 분리(디버깅용/조건을 쪼갠다!)
mask_storeA = df["store"] == "A"
mask_latte  = df["menu"] == "Latte"

```

### 다중 조건에서 꼭 지켜야 하는 규칙(가장 많이 틀리는 부분)

**규칙 1) &(AND), |(OR), ~(NOT) 사용할 때는 괄호 필수**

- AND: (조건1) & (조건2)
- OR: (조건1) | (조건2)
- NOT: ~(조건)

괄호를 안 쓰면 파이썬이 조건의 우선순위를 다르게 해석해서
에러가 나거나, 의도와 다른 결과가 나오기 쉽다.

**규칙 2) and, or가 아니라 &, |를 쓴다**

- and/or는 “파이썬 단일 True/False”에 쓰는 경우가 많고
- 판다스 필터(배열)에는 **&, |** 를 쓰는 게 기본.


```python
#2. 조건 결합 (괄호 필수!!)
mask_final = mask_paid & mask_storeA & mask_latte

```


```python
#3. 적용
df.loc[mask_final]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01-01</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2026-01-04</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>3</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



### 실무에서 가장 안전한 기본 형태: df.loc[조건, 컬럼]

조건 필터링에서 실무 기본 문장은 이 형태임.

**df.loc[조건, 컬럼]**

이게 안전한 이유는 한 문장에 역할이 분명하게 나뉘기 때문.

- 조건 : “행을 고르는 기준”
- 컬럼 : “보여줄 열만 선택”


```python
####indexing을 의미 있게 쓰는 흐름(잇츠 .lic 타임~)
#인덱싱은 분석 정확도의 기본기

#1-1 날짜를 인덱스로
df_dt = df.set_index("date")
df_dt
#이제 date는 단순 컬럼이 아니라 행의 이름표
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2026-01-01</th>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2026-01-02</th>
      <td>A</td>
      <td>Americano</td>
      <td>4500</td>
      <td>2</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2026-01-03</th>
      <td>B</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>1</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2026-01-04</th>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>3</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2026-01-05</th>
      <td>B</td>
      <td>Americano</td>
      <td>4500</td>
      <td>1</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>




```python
#1-2 날짜 범위 슬라이싱
df_dt.loc["2026-01-01":"2026-01-04"]

## 주의 : loc은 끝 포함 가능성 있음

# loc 슬라이싱 특징
#-라벨 기준
#-끝 포함 가능성 높음
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
    </tr>
    <tr>
      <th>date</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2026-01-01</th>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2026-01-02</th>
      <td>A</td>
      <td>Americano</td>
      <td>4500</td>
      <td>2</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2026-01-03</th>
      <td>B</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>1</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2026-01-04</th>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>3</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>




```python
# iloc 슬라이싱 (개수 정확할 때 씀)
df.iloc[0:3]


#무조건 끝 미포함
#“처음 N개” 이런거 가져올 때 안전
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01-01</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
      <td>True</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2026-01-02</td>
      <td>A</td>
      <td>Americano</td>
      <td>4500</td>
      <td>2</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2026-01-03</td>
      <td>B</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>1</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>



**인덱스가 0,1,2,3,4일 때**

- df.loc[0:2] → **0,1,2 포함**(대개 3행)
- df.iloc[0:2] → **0,1만**(2행)

**“마지막 포함/미포함” 때문에 생기는 대표 실수**

- “처음 10개만 가져오자” 할 때
    - iloc[0:10]은 **정확히 10개**
    - loc[0:10]은 **11개가 될 수 있음**
---
### **기억하기 쉬운 암기 팁**

- **loc = label(이름표)**
- **iloc = integer location(정수 위치)**
- 슬라이싱은 이렇게 외우기:
    - **iloc는 파이썬 슬라이스 그대로(끝 미포함)**
    - **loc는 라벨 구간이라 끝 포함 가능성 높음**


```python
#컬럼 선택 실습
#1-1 단일 컬럼 → Series
menu_series = df["menu"]
type(menu_series)

#단일 컬럼은 Series가 될 수 있음
#문제점 : 
#columns 없음
#merge / 저장 / 확장 불편
```




    pandas.core.series.Series




```python
#1-2 단일 컬럼 + DataFrame 유지
menu_df = df[["menu"]]
type(menu_df)

#안전!
#표 형태 유지하고 싶으면 이중 대괄호
```




    pandas.core.frame.DataFrame



### 행/열 선택 패턴
판다스에서 “어떤 열을 선택했는지”에 따라 결과가 Series(1차원) 로 나오기도 하고,  DataFrame(2차원) 으로 나오기도 함

* 단일 컬럼 선택 → Series가 되는 경우가 많다

* 복수 컬럼 선택 → DataFrame으로 남는 경우가 많다

    * 단일 컬럼 선택은 Series가 되어 다음 코드가 달라질 수 있고,
    * 복수 컬럼 선택은 DataFrame으로 남아 흐름이 안정적.

---
**(1) 결과 모양이 달라져서 join/merge/저장이 꼬임**

- Series는 저장하면 “열 이름이 애매”해질 수 있고
- DataFrame은 열 이름이 유지되어 파일/리포트에 안정적.

**(2) 그룹화/집계 결과를 이어갈 때**

- 단일 열로 뽑아둔 Series는 다음에 컬럼을 추가하거나 결합할 때 불편할 수 있고
- DataFrame은 ["colA","colB"] 형태로 확장하기가 쉽다.

## 개념 요약

* 인덱싱 = 분석 정확도의 기본기
* 조건 필터링 = True / False 마스크
* `loc` = 라벨 기반 / `iloc` = 위치 기반
* 단일 컬럼 선택 시 `Series`가 될 수 있음

## 실무 습관 3가지

* 조건식은 변수로 분리해서 가독성 유지
* 표 형태 유지가 필요하면 이중 대괄호 사용
* 연산 전 `type`, `shape` 한 번씩 확인
---
**습관 A) “표 형태를 유지하고 싶으면” 항상 이중 대괄호**

- 단일 컬럼이어도 DataFrame으로 유지하고 싶으면:
    - df[["menu"]] (이중 대괄호)
        - → 이러면 **DataFrame 유지**

**습관 B) “내가 지금 가진 게 Series인지 DataFrame인지” 한 번만 확인**

- type(변수) 또는 .shape로 감 잡기
    - Series: 보통 (행수,)
    - DataFrame: 보통 (행수, 열수)


### 정렬(sort_values, sort_index)이 필요한 이유
**“분석 결과를 사람이 읽기 좋은 순서로 재배치하는 작업”** 입니다.

**1) 보고서/리포트는 “정렬된 표”가 기본**

실무에서 보고서를 만들 때는 거의 항상 이런 순서를 요구한다.

- **최신순**: “최근 데이터부터 보여줘” (날짜 기준 내림차순)
- **매출 큰 순**: “어떤 메뉴/지점이 매출을 많이 만들었지?” (매출 기준 내림차순)
- **수량 많은 순**: “가장 많이 팔린 메뉴 TOP N” (판매수량 기준 내림차순)



```python
import pandas as pd

# 샘플: 카페 주문 데이터
df = pd.DataFrame({
    "date": ["2026-01-03", "2026-01-01", "2026-01-02", "2026-01-03", "2026-01-01"],
    "menu": ["Latte", "Americano", "Mocha", "Americano", "Latte"],
    "qty":  [2, 1, 3, 1, 1],
    "price":[5000, 4500, 5500, 4500, 5000]
})

# 매출 컬럼(수량 * 가격) 추가
df["revenue"] = df["qty"] * df["price"]

# 1) 최신순: 날짜 기준 내림차순
latest_first = df.sort_values(by="date", ascending=False)

# 2) 매출 큰 순: revenue 기준 내림차순
revenue_first = df.sort_values(by="revenue", ascending=False)

# 3) 수량 많은 순: qty 기준 내림차순 (TOP 3만)
top_qty = df.sort_values(by="qty", ascending=False).head(3)

# 노트북에서 print 없이 한 번에 보기
df, latest_first, revenue_first, top_qty
```




    (         date       menu  qty  price  revenue
     0  2026-01-03      Latte    2   5000    10000
     1  2026-01-01  Americano    1   4500     4500
     2  2026-01-02      Mocha    3   5500    16500
     3  2026-01-03  Americano    1   4500     4500
     4  2026-01-01      Latte    1   5000     5000,
              date       menu  qty  price  revenue
     0  2026-01-03      Latte    2   5000    10000
     3  2026-01-03  Americano    1   4500     4500
     2  2026-01-02      Mocha    3   5500    16500
     1  2026-01-01  Americano    1   4500     4500
     4  2026-01-01      Latte    1   5000     5000,
              date       menu  qty  price  revenue
     2  2026-01-02      Mocha    3   5500    16500
     0  2026-01-03      Latte    2   5000    10000
     4  2026-01-01      Latte    1   5000     5000
     1  2026-01-01  Americano    1   4500     4500
     3  2026-01-03  Americano    1   4500     4500,
              date       menu  qty  price  revenue
     2  2026-01-02      Mocha    3   5500    16500
     0  2026-01-03      Latte    2   5000    10000
     1  2026-01-01  Americano    1   4500     4500)



**2) 정렬을 하면 인사이트가 바로 보인다**

예를 들어 메뉴별 매출 집계표가 있을 때 정렬을 안 하면:

- 라떼가 1등인지 5등인지 눈으로 찾기 어려움
- 중요 메뉴/문제 메뉴가 어디인지 한 번에 안 보임

반대로 **매출 내림차순 정렬**을 하면:

- 상위 메뉴 TOP 5가 즉시 보이고
- “우리가 밀어야 할 메뉴”가 명확해지고
- 하위 메뉴도 바로 보여서 “개선 대상”을 찾기 쉬워짐.


```python
import pandas as pd

# 샘플: 카페 주문 데이터
df = pd.DataFrame({
    "menu": ["Latte","Americano","Mocha","Latte","Mocha","Americano","Tea","Tea","Latte"],
    "qty":  [2, 1, 1, 3, 2, 4, 5, 1, 1],
    "price":[5000,4500,5500,5000,5500,4500,4000,4000,5000]
})

# 매출(=수량*가격) 컬럼 추가
df["revenue"] = df["qty"] * df["price"]

# 메뉴별 매출 집계표 만들기
menu_sales = df.groupby("menu", as_index=False)["revenue"].sum()

# 1) 정렬 안 한 집계표: 순서가 애매해서 TOP 메뉴가 바로 안 보일 수 있음
menu_sales_unsorted = menu_sales

# 2) 매출 내림차순 정렬: TOP 메뉴가 즉시 보임 (TOP 5)
menu_sales_sorted = menu_sales.sort_values(by="revenue", ascending=False)
top5 = menu_sales_sorted.head(5)

# 3) 하위 메뉴(개선 대상)도 바로 보임 (BOTTOM 3)
bottom3 = menu_sales_sorted.tail(3)

# 노트북에서 한 번에 보기
menu_sales_unsorted, top5, bottom3
```




    (        menu  revenue
     0  Americano    22500
     1      Latte    30000
     2      Mocha    16500
     3        Tea    24000,
             menu  revenue
     1      Latte    30000
     3        Tea    24000
     0  Americano    22500
     2      Mocha    16500,
             menu  revenue
     3        Tea    24000
     0  Americano    22500
     2      Mocha    16500)



**3) 정렬은 “커뮤니케이션 비용”을 줄인다**

분석 결과는 보통 **다른 사람(팀장/클라이언트/동료)** 에게 보여줘야 함.

이때 정렬이 되어 있으면 보는 사람이:

- “뭘 먼저 봐야 하는지” 바로 이해하고
- 질문이 줄고
- 보고서 설득력이 올라간다.

정렬이 안 되어 있으면 반대로

“이게 중요한 결과 맞나요?” 같은 질문이 늘어남.


```python
import pandas as pd

# 예시: "팀장에게 메뉴별 매출 리포트"를 보여주는 상황
# - 정렬이 안 된 표(unsorted)는 핵심이 바로 안 보여서 질문이 늘 수 있고
# - 정렬된 표(sorted)는 TOP 메뉴가 바로 보여서 커뮤니케이션이 쉬워집니다.

df = pd.DataFrame({
    "menu": ["Latte", "Americano", "Mocha", "Tea", "Cake"],
    "revenue": [32000, 15000, 27000, 9000, 12000]  # 메뉴별 총매출(이미 집계된 값이라고 가정)
})

# 1) 정렬 안 한 리포트: 보는 사람이 "뭐가 1등이지?"를 눈으로 찾아야 함
report_unsorted = df

# 2) 매출 내림차순 정렬된 리포트: TOP 메뉴가 위에 고정되어 바로 이해됨
report_sorted = df.sort_values(by="revenue", ascending=False)

# 3) 보통 보고서에는 TOP N을 같이 제시하면 설득력이 더 좋아짐
top3 = report_sorted.head(3)

# 노트북에서는 print 없이 마지막 줄에 두면 자동 출력
report_unsorted, report_sorted, top3
```




    (        menu  revenue
     0      Latte    32000
     1  Americano    15000
     2      Mocha    27000
     3        Tea     9000
     4       Cake    12000,
             menu  revenue
     0      Latte    32000
     2      Mocha    27000
     1  Americano    15000
     4       Cake    12000
     3        Tea     9000,
             menu  revenue
     0      Latte    32000
     2      Mocha    27000
     1  Americano    15000)



**4) sort_values vs sort_index 차이(감 잡기)**

- **sort_values**: “값” 기준 정렬
- 예: 매출, 수량, 평점, 가격 순서로 정렬할 때
- **sort_index**: “인덱스(행 이름표)” 기준 정렬
- 예: 날짜를 인덱스로 두었을 때 날짜순으로 정렬하고 싶을 때
- 예: 그룹화 결과가 인덱스가 되어 있을 때 정리할 때

리포트/랭킹표는 대부분 **sort_values**가 더 자주 쓰이고,

시간축 데이터/인덱스 기반 표 정리는 **sort_index**가 자주 쓰인다.


```python
import pandas as pd

# ------------------------------------------------------------
# sort_values vs sort_index 차이 한 번에 감 잡기
# - sort_values: "값" 기준 정렬 (리포트/랭킹표에서 가장 자주)
# - sort_index : "인덱스(행 이름표)" 기준 정렬 (시간축/인덱스 기반 표 정리)
# ------------------------------------------------------------

# 1) sort_values 예시: 메뉴별 매출(값)로 정렬
sales = pd.DataFrame({
    "menu": ["Latte", "Americano", "Mocha", "Tea"],
    "revenue": [32000, 15000, 27000, 9000]
})

# 값(revenue) 기준 내림차순 정렬 -> "매출 TOP" 랭킹표 만들 때
sales_sorted_by_value = sales.sort_values(by="revenue", ascending=False)


# 2) sort_index 예시: 날짜를 인덱스로 둔 뒤 인덱스(날짜 라벨)로 정렬
daily = pd.DataFrame({
    "date": ["2026-01-03", "2026-01-01", "2026-01-02"],
    "revenue": [12000, 8000, 15000]
}).set_index("date")   # date가 인덱스(행 이름표)가 됨

# 인덱스(날짜) 기준 오름차순 정렬 -> 시간 흐름대로 정리할 때
daily_sorted_by_index = daily.sort_index(ascending=True)


# 3) sort_index 예시(그룹화 결과 정리): groupby 결과는 인덱스가 menu가 되는 경우가 많음
orders = pd.DataFrame({
    "menu": ["Latte","Americano","Latte","Mocha","Mocha","Tea"],
    "qty":  [2, 1, 3, 1, 2, 4]
})

qty_sum = orders.groupby("menu")["qty"].sum()  # 결과: 인덱스가 menu인 Series
qty_sum_sorted_by_index = qty_sum.sort_index() # 인덱스(메뉴 이름) 알파벳/가나다 순 정리

# 노트북에서 print 없이 한 번에 보기
sales, sales_sorted_by_value, daily, daily_sorted_by_index, qty_sum, qty_sum_sorted_by_index
```




    (        menu  revenue
     0      Latte    32000
     1  Americano    15000
     2      Mocha    27000
     3        Tea     9000,
             menu  revenue
     0      Latte    32000
     2      Mocha    27000
     1  Americano    15000
     3        Tea     9000,
                 revenue
     date               
     2026-01-03    12000
     2026-01-01     8000
     2026-01-02    15000,
                 revenue
     date               
     2026-01-01     8000
     2026-01-02    15000
     2026-01-03    12000,
     menu
     Americano    1
     Latte        5
     Mocha        3
     Tea          4
     Name: qty, dtype: int64,
     menu
     Americano    1
     Latte        5
     Mocha        3
     Tea          4
     Name: qty, dtype: int64)



### 클리닝(정제)의 4대 문제

**컬럼명/구조 문제: rename, drop문자열 문제: 공백, 대소문자, 불필요 문자(“원”, “,”)결측치 문제: NaN(비어 있음)중복 문제: 같은 행이 여러 번 있음**

- 컬럼명/구조 문제: rename, drop
- 문자열 문제: 공백, 대소문자, 불필요 문자(“원”, “,”)
- 결측치 문제: NaN(비어 있음)
- 중복 문제: 같은 행이 여러 번 있음

**결측치 전략 2가지**

- dropna: “신뢰할 수 없는 행은 버린다”(단순)
- fillna: “합리적 값으로 채운다”(실무형)
- 평균/중앙값/그룹별 평균 등

**중복 처리에서 중요한 옵션**

- subset: 무엇을 기준으로 중복인지 판단할지
- keep: 첫 번째를 남길지, 마지막을 남길지

# 실습편


```python
import pandas as pd

raw = [
    {"date":"2026-01-01", "time":"09:10", "store":"A", "menu":"Americano", "price":"4,500원", "qty":"2", "paid":"TRUE"},
    {"date":"2026/01/01", "time":"09:12", "store":"A", "menu":"Latte",     "price":"5000",   "qty":1,   "paid":"True"}, # /
    {"date":"2026-01-02", "time":"12:30", "store":"A", "menu":"Latte",     "price":None,     "qty":2,   "paid":"FALSE"},
    {"date":"2026-01-03", "time":"18:05", "store":"B", "menu":"Mocha",     "price":"5500",   "qty":None,"paid":True},
    {"date":"2026-01-03", "time":"18:05", "store":"B", "menu":"Mocha",     "price":"5500",   "qty":None,"paid":True},  # 중복
    {"date":"2026-01-04", "time":"08:55", "store":"B", "menu":"Americano ", "price":"4500",  "qty":"1", "paid":"TRUE"}, # 공백
    {"date":"2026-01-04", "time":"08:58", "store":"A", "menu":"latte",     "price":"5,000",  "qty":"3", "paid":"TRUE"}, # 소문자
]
df = pd.DataFrame(raw)

df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>time</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01-01</td>
      <td>09:10</td>
      <td>A</td>
      <td>Americano</td>
      <td>4,500원</td>
      <td>2</td>
      <td>TRUE</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2026/01/01</td>
      <td>09:12</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2026-01-02</td>
      <td>12:30</td>
      <td>A</td>
      <td>Latte</td>
      <td>None</td>
      <td>2</td>
      <td>FALSE</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2026-01-03</td>
      <td>18:05</td>
      <td>B</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>None</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2026-01-03</td>
      <td>18:05</td>
      <td>B</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>None</td>
      <td>True</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2026-01-04</td>
      <td>08:55</td>
      <td>B</td>
      <td>Americano</td>
      <td>4500</td>
      <td>1</td>
      <td>TRUE</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2026-01-04</td>
      <td>08:58</td>
      <td>A</td>
      <td>latte</td>
      <td>5,000</td>
      <td>3</td>
      <td>TRUE</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.shape
df.info()
df.describe(include="all")
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 7 entries, 0 to 6
    Data columns (total 7 columns):
     #   Column  Non-Null Count  Dtype 
    ---  ------  --------------  ----- 
     0   date    7 non-null      object
     1   time    7 non-null      object
     2   store   7 non-null      object
     3   menu    7 non-null      object
     4   price   6 non-null      object
     5   qty     5 non-null      object
     6   paid    7 non-null      object
    dtypes: object(7)
    memory usage: 524.0+ bytes
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>time</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>7</td>
      <td>7</td>
      <td>7</td>
      <td>7</td>
      <td>6</td>
      <td>5</td>
      <td>7</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>5</td>
      <td>6</td>
      <td>2</td>
      <td>5</td>
      <td>5</td>
      <td>5</td>
      <td>4</td>
    </tr>
    <tr>
      <th>top</th>
      <td>2026-01-04</td>
      <td>18:05</td>
      <td>A</td>
      <td>Latte</td>
      <td>5500</td>
      <td>2</td>
      <td>TRUE</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>2</td>
      <td>2</td>
      <td>4</td>
      <td>2</td>
      <td>2</td>
      <td>1</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
#실습 1: “한 행” 뽑기
df.loc[0]      # 0번 인덱스 라벨
df.iloc[0]     # 0번째 위치
```




    date     2026-01-01
    time          09:10
    store             A
    menu      Americano
    price        4,500원
    qty               2
    paid           TRUE
    Name: 0, dtype: object




```python
#“행 범위” 슬라이싱 실수 포인트
df.loc[0:2]    # 끝 포함
df.iloc[0:2]   # 끝 미포함
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>time</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01-01</td>
      <td>09:10</td>
      <td>A</td>
      <td>Americano</td>
      <td>4,500원</td>
      <td>2</td>
      <td>TRUE</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2026/01/01</td>
      <td>09:12</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>




```python
#열 선택 실전: 필요한 컬럼만 남기기
#오늘 회의용 테이블은 이 컬럼만 필요

cols = ["date","time","store","menu","price","qty","paid"]
df2 = df[cols].copy()
df2.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>time</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01-01</td>
      <td>09:10</td>
      <td>A</td>
      <td>Americano</td>
      <td>4,500원</td>
      <td>2</td>
      <td>TRUE</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2026/01/01</td>
      <td>09:12</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2026-01-02</td>
      <td>12:30</td>
      <td>A</td>
      <td>Latte</td>
      <td>None</td>
      <td>2</td>
      <td>FALSE</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2026-01-03</td>
      <td>18:05</td>
      <td>B</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>None</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2026-01-03</td>
      <td>18:05</td>
      <td>B</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>None</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



### 조건 필터링(불리언 인덱싱): “결제 완료 + A매장 + 오전”

**규칙 1) & | ~ 는 괄호 필수**

**규칙 2) 실무 패턴은 이거 하나: df.loc[조건, 컬럼]**


```python
#**Step 1) 먼저 paid 값을 통일(소문자/대문자/True 섞임)**

df2["paid"] = df2["paid"].astype(str).str.strip().str.lower()
df2["paid"].value_counts()

#**Step 2) 결제 완료만 추출**

cond_paid = df2["paid"].isin(["true"])
paid_df = df2.loc[cond_paid]
paid_df

#**Step 3) A매장 + 오전(09시대)만 더 좁히기**

cond_storeA = (df2["store"] == "A")
cond_morning = df2["time"].str.startswith("09")

df_morning_A = df2.loc[cond_paid & cond_storeA & cond_morning, ["date","time","store","menu","price","qty"]]
df_morning_A
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>time</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01-01</td>
      <td>09:10</td>
      <td>A</td>
      <td>Americano</td>
      <td>4,500원</td>
      <td>2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2026/01/01</td>
      <td>09:12</td>
      <td>A</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
#정렬: “메뉴별 판매수량 TOP”을 뽑을 준비
df2.sort_values(by=["date","time"], ascending=[True, True]).head()
#- sort_values = 값 기준 정렬
#- sort_index = 인덱스 기준 정렬
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>time</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01-01</td>
      <td>09:10</td>
      <td>A</td>
      <td>Americano</td>
      <td>4,500원</td>
      <td>2</td>
      <td>true</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2026-01-02</td>
      <td>12:30</td>
      <td>A</td>
      <td>Latte</td>
      <td>None</td>
      <td>2</td>
      <td>false</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2026-01-03</td>
      <td>18:05</td>
      <td>B</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>None</td>
      <td>true</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2026-01-03</td>
      <td>18:05</td>
      <td>B</td>
      <td>Mocha</td>
      <td>5500</td>
      <td>None</td>
      <td>true</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2026-01-04</td>
      <td>08:55</td>
      <td>B</td>
      <td>Americano</td>
      <td>4500</td>
      <td>1</td>
      <td>true</td>
    </tr>
  </tbody>
</table>
</div>




```python
#클리닝 기본기 4종 세트 (오늘의 하이라이트)

#7-1) rename / drop: 컬럼명 정리(선택)
df2 = df2.rename(columns={"qty":"quantity"})

#7-2) 문자열 정리: menu 공백/대소문자 통일
df2["menu"] = df2["menu"].astype(str).str.strip().str.title()
df2["menu"].value_counts()

#7-3) price 정리: “원”, “,” 제거하고 숫자로 만들기
df2["price"] = (
    df2["price"]
    .astype(str)
    .str.replace(",", "", regex=False)
    .str.replace("원", "", regex=False)
    .str.strip()
)

df2["price"] = pd.to_numeric(df2["price"], errors="coerce")
df2["price"].isnull().sum()

        #문자열 패턴에 따라 정규식(Regular Expression)을 사용할 수 있음
df2["price"] = (
    df2["price"]
    .astype(str)
    .str.replace(r"[^0-9]", "", regex=True)  # 숫자(0-9) 제외 전부 제거
)

df2["price"] = pd.to_numeric(df2["price"], errors="coerce")
df2["price"].isnull().sum()

#7-4) 결측치 처리: dropna vs fillna (전략 선택)
#- 가격(price)이 비어 있으면 매출 계산이 안됨
# 실무에서는 보통 2가지 선택:
#    - **삭제(drop)**: 데이터가 적어도 괜찮고, 결측이 중요 변수면 제거
#    - **대체(fill)**: 평균/중앙값/메뉴별 평균 등으로 대체

# quantity도 숫자로 정리
df2["quantity"] = pd.to_numeric(df2["quantity"], errors="coerce")

# 핵심 변수 결측은 삭제(간단 버전)
clean = df2.dropna(subset=["price","quantity"]).copy()
clean.shape
```




    (4, 7)




```python
#중복 처리: duplicated / drop_duplicates
clean.duplicated().sum()
clean = clean.drop_duplicates(keep="first")
clean.duplicated().sum()
```




    np.int64(0)




```python
#미니 결과물 만들기: “매출 컬럼 추가 + 회의용 테이블 정리”
clean["sales"] = clean["price"] * clean["quantity"]

meeting_table = clean.loc[:, ["date","time","store","menu","price","quantity","sales"]].sort_values(
    by=["date","store","time"],
    ascending=[True, True, True]
)

meeting_table
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>time</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>quantity</th>
      <th>sales</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2026-01-01</td>
      <td>09:10</td>
      <td>A</td>
      <td>Americano</td>
      <td>45000.0</td>
      <td>2.0</td>
      <td>90000.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2026-01-04</td>
      <td>08:58</td>
      <td>A</td>
      <td>Latte</td>
      <td>50000.0</td>
      <td>3.0</td>
      <td>150000.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2026-01-04</td>
      <td>08:55</td>
      <td>B</td>
      <td>Americano</td>
      <td>45000.0</td>
      <td>1.0</td>
      <td>45000.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2026/01/01</td>
      <td>09:12</td>
      <td>A</td>
      <td>Latte</td>
      <td>50000.0</td>
      <td>1.0</td>
      <td>50000.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#저장: 2회차 결과물을 파일로 남기기 (3회차를 위해 필수)
meeting_table.to_csv("cafe_sales_clean_v1.csv", index=False, encoding="utf-8-sig")
```

**오늘 실습 팁**

- 중요 TOP3 지점
    
    **& | 괄호 누락loc 슬라이싱 끝 포함 vs iloc 끝 미포함to_numeric(errors="coerce")로 결측 생기는 이유 이해하기**
    
    - & | 괄호 누락
    - loc 슬라이싱 끝 포함 vs iloc 끝 미포함
    - to_numeric(errors="coerce")로 결측 생기는 이유 이해 못함
- 해결 예시
    - “조건은 무조건 괄호로 감싸고 &로 묶자”
    - “loc은 이름이라 끝 포함, iloc은 순서라 끝 미포함”
    - “숫자로 바꿀 수 없는 값은 NaN으로 바뀌는 게 정상”


```python
!python -m jupyter nbconvert --to markdown 02_python_indexing.ipynb
```

    [NbConvertApp] Converting notebook 02_python_indexing.ipynb to markdown
    [NbConvertApp] Writing 35542 bytes to 02_python_indexing.md
    
