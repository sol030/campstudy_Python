##실습 목표 한 줄

원본 주문 로그 → 분석용 테이블로 정제 →
GroupBy / Pivot / Merge를 사용해 리포트용 요약표 2개 생성

##실습 흐름 전체 지도

1. 실습 환경 준비

2. 원본 로그 데이터 생성

3. 분석 가능하도록 1차 전처리

4. GroupBy 기본 (단일 기준)

5. GroupBy 심화 (다중 기준 + Pivot)

6. Merge로 정보 확장

7. Map / Apply 사용 시점 체감

8. 요약 테이블 A / B 완성

9. 저장 & 재현성 확인


```python
##1. 실습 환경 준비
import pandas as pd
import numpy as np

```


```python
#2.원본 로그 데이터 생성 (문제 상황 포함)
raw = [
    {"order_id":"O001","date":"2026-01-01 09:12","store":"광교점","menu":"Americano","price":"4,500원","qty":"2","paid":"TRUE","channel":"kiosk"},
    {"order_id":"O002","date":"2026/01/01 10:05","store":"광교점","menu":" Latte ","price":"5000","qty":1,"paid":"True","channel":"app"},
    {"order_id":"O003","date":"2026-01-02 12:20","store":"광교점","menu":"Mocha","price":None,"qty":2,"paid":"FALSE","channel":"kiosk"},
    {"order_id":"O004","date":"2026-01-03 15:40","store":"수원점","menu":"Americano","price":"4500","qty":None,"paid":True,"channel":"app"},
    {"order_id":"O005","date":"2026-01-03 18:10","store":"수원점","menu":"latte","price":"5,000원","qty":"3","paid":"TRUE","channel":"kiosk"},
    {"order_id":"O006","date":"2026-01-04 08:55","store":"수원점","menu":"Vanilla Latte","price":"5800원","qty":"1","paid":"TRUE","channel":"app"},
    {"order_id":"O007","date":"2026-01-04 09:10","store":"광교점","menu":"Mocha","price":"5500","qty":"1","paid":"FALSE","channel":"kiosk"},
    {"order_id":"O008","date":"2026-01-05 11:00","store":"광교점","menu":"Americano","price":"4500원","qty":"1","paid":"TRUE","channel":"app"},
]

df = pd.DataFrame(raw)
df.head()
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
      <th>order_id</th>
      <th>date</th>
      <th>store</th>
      <th>menu</th>
      <th>price</th>
      <th>qty</th>
      <th>paid</th>
      <th>channel</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>O001</td>
      <td>2026-01-01 09:12</td>
      <td>광교점</td>
      <td>Americano</td>
      <td>4,500원</td>
      <td>2</td>
      <td>TRUE</td>
      <td>kiosk</td>
    </tr>
    <tr>
      <th>1</th>
      <td>O002</td>
      <td>2026/01/01 10:05</td>
      <td>광교점</td>
      <td>Latte</td>
      <td>5000</td>
      <td>1</td>
      <td>True</td>
      <td>app</td>
    </tr>
    <tr>
      <th>2</th>
      <td>O003</td>
      <td>2026-01-02 12:20</td>
      <td>광교점</td>
      <td>Mocha</td>
      <td>None</td>
      <td>2</td>
      <td>FALSE</td>
      <td>kiosk</td>
    </tr>
    <tr>
      <th>3</th>
      <td>O004</td>
      <td>2026-01-03 15:40</td>
      <td>수원점</td>
      <td>Americano</td>
      <td>4500</td>
      <td>None</td>
      <td>True</td>
      <td>app</td>
    </tr>
    <tr>
      <th>4</th>
      <td>O005</td>
      <td>2026-01-03 18:10</td>
      <td>수원점</td>
      <td>latte</td>
      <td>5,000원</td>
      <td>3</td>
      <td>TRUE</td>
      <td>kiosk</td>
    </tr>
  </tbody>
</table>
</div>



## 전처리

* 날짜 데이터 전처리
    * 날짜 형식이 제각각이면 pandas가 날짜로 인식하지 못함
    * `pd.to_datetime()` 사용
    * `errors='coerce'`
        * 변환 불가능한 값은 NaT로 강제 변환
    * pandas 2.x / Python 3.13 환경에서는
        * 날짜 포맷이 섞여 있으면 변환 실패 가능
        * 이때 `format='mixed'` 옵션 사용

---

* 문자열 → 숫자 전처리 (price 컬럼)
    * 원, 콤마가 포함된 숫자는 문자열
    * 숫자로 변환하기 전 반드시 문자 정리 필요
    * 절차
        * astype(str) → 문자열로 명시적 변환
        * str.replace('원','')
        * str.replace(',','')
        * pd.to_numeric()

---

* 결측치 처리
    * `isnull()` / `notnull()` 확인
    * `fillna()` 로 기본값 채우기 가능
    * 평균, 중앙값, 고정값 등 다양한 방식


```python
##3. 1차 전처리 (분석 가능한 테이블 만들기)
#3-1 날짜 처리 + 파생 컬럼
df["date"] = pd.to_datetime(df["date"], errors="coerce")
df["ym"] = df["date"].dt.to_period("M").astype(str)
df["day_name"] = df["date"].dt.day_name()

```


```python
#3-2 메뉴 표기 통일
df["menu"] = df["menu"].astype(str).str.strip().str.title()
```


```python
#3-3. 가격 정리 (문자 → 숫자)
df["price"] = (
    df["price"]
    .astype(str)
    .str.replace(",", "", regex=False)
    .str.replace("원", "", regex=False)
)

df["price"] = pd.to_numeric(df["price"], errors="coerce")

```


```python
#3-4. 수량 정리
df["qty"] = pd.to_numeric(df["qty"], errors="coerce")
df["qty"] = df["qty"].fillna(1).astype(int)
```


```python
#3-5. 결제 여부(bool) 통일
def to_bool(x):
    if isinstance(x, bool):
        return x
    x = str(x).strip().lower()
    return x in ["true", "1", "yes", "y"]

df["paid"] = df["paid"].apply(to_bool)

```


```python
#3-6. 매출 컬럼 생성
df["sales"] = np.where(df["paid"], df["price"] * df["qty"], 0)

#이제 분석용 테이블 완성!
```

* 그룹핑 & 집계
    * `groupby()` + `agg()` 사용
    * 집계 함수: `sum`, `mean`, `count`, `nunique`, `max`, `min`
    * 그룹핑 후 인덱스 초기화: `reset_index()`


```python
##4. GroupBy 기본 (단일 기준)
#매장별 요약
store_summary = df.groupby("store").agg(
    total_sales=("sales","sum"),
    orders=("order_id","count"),
    paid_rate=("paid","mean")
).reset_index()

store_summary

#True / False → mean() → 비율
#reset_index()는 실무 필수
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
      <th>total_sales</th>
      <th>orders</th>
      <th>paid_rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>광교점</td>
      <td>18500.0</td>
      <td>5</td>
      <td>0.6</td>
    </tr>
    <tr>
      <th>1</th>
      <td>수원점</td>
      <td>25300.0</td>
      <td>3</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
</div>



* 파생 컬럼 생성
    * 날짜 → 연, 월, 요일 컬럼 생성
        * `.dt.year`, `.dt.month`, `.dt.day_name()`
    * 수치 계산: `sales = unit_price * qty`

---

* 정렬 & 소팅
    * `sort_values()` 사용
    * 오름차순 / 내림차순 선택 가능

---

* 피보팅
    * `pivot_table()`로 가독성 좋은 테이블 생성
    * index, columns, values, aggfunc 지정 가능


```python
##5. GroupBy 심화 (다중 기준 + Pivot)
#월 × 요일 요약
ym_day = df.groupby(["ym","day_name"]).agg(
    total_sales=("sales","sum"),
    orders=("order_id","count"),
    paid_rate=("paid","mean")
).reset_index()

```


```python
#리포트용 피벗
pivot_sales = (
    ym_day
    .pivot(index="ym", columns="day_name", values="total_sales")
    .fillna(0)
)

pivot_sales
#요약 테이블 A 후보
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
      <th>day_name</th>
      <th>Friday</th>
      <th>Monday</th>
      <th>Saturday</th>
      <th>Sunday</th>
      <th>Thursday</th>
    </tr>
    <tr>
      <th>ym</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2026-01</th>
      <td>0.0</td>
      <td>4500.0</td>
      <td>19500.0</td>
      <td>5800.0</td>
      <td>9000.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
##6. Merge (카테고리 붙이기)
#카테고리 테이블
menu_map = pd.DataFrame([
    {"menu":"Americano", "category":"Coffee"},
    {"menu":"Latte", "category":"Coffee"},
    {"menu":"Mocha", "category":"Coffee"},
    {"menu":"Vanilla Latte", "category":"Latte Variations"},
])

```


```python
#merge
df2 = df.merge(menu_map, on="menu", how="left")
#how="left" → 원본 보존
#NaN = 매핑 누락 탐지 포인트
```


```python
##7. Map / Apply 사용
#map: 단순 치환
channel_map = {"kiosk":"키오스크", "app":"앱"}
df2["channel_kr"] = df2["channel"].map(channel_map).fillna("기타")

```


```python
#apply: 행 단위 규칙
def sales_grade(row):
    if row["sales"] >= 10000:
        return "A"
    elif row["sales"] > 0:
        return "B"
    else:
        return "C"

df2["grade"] = df2.apply(sales_grade, axis=1)

```


```python
##8. 최종 요약 테이블 2개
#요약 테이블 A (월 × 요일)
summary_A = df.groupby(["ym","day_name"]).agg(
    total_sales=("sales","sum"),
    paid_rate=("paid","mean")
).reset_index()

summary_A_pivot = (
    summary_A
    .pivot(index="ym", columns="day_name", values="total_sales")
    .fillna(0)
)

```


```python
#요약 테이블 B (메뉴 × 카테고리)
menu_summary = df2.groupby(["category","menu"]).agg(
    total_sales=("sales","sum"),
    total_qty=("qty","sum"),
    orders=("order_id","count"),
    paid_rate=("paid","mean")
).reset_index()

menu_summary = menu_summary.sort_values("total_sales", ascending=False)

```


```python
##9. 저장 & 재현성 확인
import os
os.makedirs("data", exist_ok=True)

summary_A.to_csv("data/summary_A_long.csv", index=False)
summary_A_pivot.to_csv("data/summary_A_pivot.csv")
menu_summary.to_csv("data/summary_B_menu.csv", index=False)

```


```python
check = pd.read_csv("data/summary_B_menu.csv")
check.head()

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
      <th>category</th>
      <th>menu</th>
      <th>total_sales</th>
      <th>total_qty</th>
      <th>orders</th>
      <th>paid_rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Coffee</td>
      <td>Latte</td>
      <td>20000.0</td>
      <td>4</td>
      <td>2</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Coffee</td>
      <td>Americano</td>
      <td>18000.0</td>
      <td>4</td>
      <td>3</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Latte Variations</td>
      <td>Vanilla Latte</td>
      <td>5800.0</td>
      <td>1</td>
      <td>1</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Coffee</td>
      <td>Mocha</td>
      <td>0.0</td>
      <td>3</td>
      <td>2</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
</div>



###오늘 실습 핵심 요약 (진짜 중요)

* 전처리의 목적은 분석이 아니라 “요약 가능 상태” 만들기

* GroupBy = 기준 변경
    * 데이터를 요약하거나 집계할 때 사용. 기준 컬럼을 정하고, 집계 함수(sum, mean, count 등)를 적용해 데이터 프레임으로 결과를 받는다. 멀티 컬럼 기준으로도 그룹핑 가능하며, 인덱스를 리셋하여 디폴트 인덱스를 붙일 수 있다.

* agg() = 리포트용 지표 한 번에 생성

* Pivot = 사람 눈에 보이게 표 펼치기 / 데이터를 요약하여 시각화나 보고서에 바로 활용할 수 있는 형태로 변환함. 원본 데이터는 그대로 두고, 가독성을 위해 피보팅 테이블을 만든다.

* Merge / join = 정보 확장 / 여러 테이블을 연결할 때 사용. 데이터 분석에서 테이블을 합쳐서 분석하거나 시각화할 때 필수적

* reset_index() 안 하면 다음 단계에서 계속 걸림

* 문자열, 숫자, 날짜 데이터 전처리

    * 날짜는 형식이 제각각이면 pandas가 인식하지 못하므로 pd.to_datetime()을 사용합니다. 변환 불가 값은 errors='coerce'로 NaT 처리합니다.

    * 문자열 → 숫자 변환 시, 원화 기호나 콤마를 제거한 뒤 pd.to_numeric()으로 변환합니다.

    * 데이터 타입 통일, 대소문자 정리, 날짜 포맷 통일, 파생 컬럼 생성 등을 통해 분석 가능한 데이터 프레임을 만듭니다.

* Boolean/정규화
    * True/False 값을 활용해 비율 계산 가능
    * 0/1로 변환 후 평균 계산 가능

# 추가 실습


```python
# 정제한 df_final(카테고리 결합 데이터) 활용
category_report = df2.groupby('category').agg(
    total_sales=('sales', 'sum'),
    avg_price=('price', 'mean'),
    total_qty=('qty', 'sum')
).sort_values(by='total_sales', ascending=False)

category_report
# 결과: Coffee 카테고리는 매출이 높지만, Latte Variations는 단가가 높음을 확인
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
      <th>total_sales</th>
      <th>avg_price</th>
      <th>total_qty</th>
    </tr>
    <tr>
      <th>category</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Coffee</th>
      <td>38000.0</td>
      <td>4833.333333</td>
      <td>11</td>
    </tr>
    <tr>
      <th>Latte Variations</th>
      <td>5800.0</td>
      <td>5800.000000</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 채널별 결제율 분석
channel_analysis = df.groupby('channel').agg(
    total_orders=('order_id', 'count'),
    paid_count=('paid', 'sum')
).reset_index()

channel_analysis['conversion_rate'] = (channel_analysis['paid_count'] / channel_analysis['total_orders']) * 100
print(channel_analysis)
# 만약 앱에서 하는 결제율이 현저히 낮다면? -> 앱 결제 UI/UX에 문제가 있다고 판단할 수 있음
```

      channel  total_orders  paid_count  conversion_rate
    0     app             4           4            100.0
    1   kiosk             4           2             50.0
    


```python
!python -m jupyter nbconvert --to markdown 03_cafe_data.ipynb
```
