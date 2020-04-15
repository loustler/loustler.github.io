---
layout: post
title:  "[BigData 기초] 3. 크로스 집계 기본"
date:   2020-04-15 21:54:47 +0900
categories: [data_eng]
tags : [data, bigdata]
---

빅데이터의 기본 크로스 집계 분석에 대해서 공부한 것을 공유한다.

<!--more-->

# 크로스 집계
## 개념
- 트랜잭션 테이블
- 크로스 테이블
- 피벗 테이블

트랜잭션 테이블 -> 크로스 테이블로 변환화는 과정을 **크로스 집계**라고 함

### 크로스 테이블(Cross table)
행과 열이 교차하는 부분에 데이터를 넣는 형태의 테이블

컬럼과 행으로 구성된 양방향 테이블을 말함

아래와 같은 형태다

|카테고리|2020-01|2020-02|2020-03|
|---|---|---|---|
|Fashion|201|228|346|
|Food|420|333|490|
|Beauty|93|232|182|

### 트랜잭션 테이블(Transaction table)
데이터가 행(row)만 증가하고 열(column)은 증가하지 않는 형태의 테이블

우리가 흔히 사용하는 RDB에서의 테이블이라고 생각하면 되지 않을까 싶다.

형태는 아래와 같다

|년월|카테고리|개수|
|---|---|---|
|2020-01|Fashion|201|
|2020-01|Food|420|
|2020-01|Beauty|93|
|2020-02|Fashion|228|
|2020-02|Food|333|
|2020-02|Beauty|232|
|2020-03|Fashion|346|
|2020-03|Food|490|
|2020-03|Beauty|182|

### 피벗 테이블(Pivot table)
엑셀에서 흔히 볼 수 있는 피벗테이블을 생각하면 됨

광범위한 테이블의 데이터를 요약하는 통계 테이블이다

보통 여기에는 `SUM`, `AVERAGE`와 같은 aggregatge된 값들이 테이블의 값이 되곤 한다

## 피벗 테이블을 이용한 크로스 집계
피벗팅을 통하여 피벗 테이블을 만들어 이를 통해 크로스 집계를 하는 것은 다음과 같이 쉽게 할 수 있다.

```sql
SELECT "category",
       SUM(CASE WHEN SUBSTRING("date"::TEXT, 1, 7) = '2020-01' THEN "count" ELSE 0 END) AS "JAN",
       SUM(CASE WHEN SUBSTRING("date"::TEXT, 1, 7) = '2020-02' THEN "count" ELSE 0 END) AS "FEB",
       SUM(CASE WHEN SUBSTRING("date"::TEXT, 1, 7) = '2020-03' THEN "count" ELSE 0 END) AS "MAR",
       SUM(CASE WHEN SUBSTRING("date"::TEXT, 1, 7) = '2020-04' THEN "count" ELSE 0 END) AS "APR",
       SUM(CASE WHEN SUBSTRING("date"::TEXT, 1, 7) = '2020-05' THEN "count" ELSE 0 END) AS "MAY"
FROM data1
GROUP BY "category";
```

피벗팅 코드들은 작성자의 GitHub [Repository](https://github.com/loustler/massemble/tree/master/data-eng/pivot)에서 볼 수 있다.