---
layout: post
title:  "[OLAP]OLAP의 4가지 기본적인 Operation - Part 2"
date:   2020-03-31 23:59:00 +0900
categories: [data_eng]
tags : [data]
---

이전 포스트들인 [OLAP란?](/data_eng/what-is-olap-1/), [OLAP의 기본 분석 operation part 1](/data_eng/basic-analytical-operations-of-olap-part-1/)에 이어서

Part 1에서 소개하지 않았던 나머지 2개를 먼저 소개하겠다.

<!--more-->

지난 [Part 1](/data_eng/basic-analytical-operations-of-olap-part-1/)에서 말했듯이

OLAP의 기본적인 분석 operation이 4가지 있다.

1. **Roll-up**
1. **Drill-down**
1. **Slice and dice**
1. **Pivot**

나머지 2개인 **Slice and dice**과 **Pivot**을 설명하겠다.

먼저 **Slice and dice**는

- slice: 1개의 차원(dimension)을 고른 뒤에 새로운 서브 큐브(sub cube) 만들기
- dice: slice와 유사하지만 2개 이상의 차원(dimensions)를 골라 새로운 서브 큐브(sub cube)를 만들기

로 나눌 수 있다.

먼저 slice 부터 살펴보자

이전 포스트와 동일한 형태의 데이터를 이용한다.

아래와 같은 데이터가 있다고 해보자

|종류|기간|도시|개수|종류|기간|도시|개수|
|---|---|---|---|---|---|---|---|
|컴퓨터|1Q|서울|350|옷|1Q|서울|1000|
|컴퓨터|1Q|부산|100|옷|1Q|부산|800|
|컴퓨터|1Q|광주|250|옷|1Q|광주|600|
|컴퓨터|1Q|수원|300|옷|1Q|수원|900|
|컴퓨터|1Q|성남|500|옷|1Q|성남|700|
|컴퓨터|2Q|서울|450|옷|2Q|서울|2000|
|컴퓨터|2Q|부산|50|옷|2Q|부산|600|
|컴퓨터|2Q|광주|150|옷|2Q|광주|300|
|컴퓨터|2Q|수원|200|옷|2Q|수원|800|
|컴퓨터|2Q|성남|1000|옷|2Q|성남|900|

이렇게 있을 때 **Slice and dice**의 **slice**를 기간 중 일부인 1Q에 적용을 해보면

아래의 표와 같이 바뀐다

|종류|도|개수|종류|도|개수|
|---|---|---|---|---|---|
|컴퓨터|서울|350|옷|서울|1000|
|컴퓨터|부산|100|옷|부산|800|
|컴퓨터|광주|250|옷|광주|600|
|컴퓨터|경기|800|옷|경기|1600|

이렇게 보면 **slice**는 우리가 흔히 아는 filter와 같은 느낌이다.

혹시 **slice**에 대해 감이 안 온다면 아래 그림을 참고하면 조금 더 직관적으로 이해를 하는데에 도움을 줄 것이라 생각한다.

![Slice](https://www.guru99.com/images/1/022218_1238_WhatisOLAPO4.png)

다음은 **dice**이다. **dice**는 **slice**의 발전된 형태? 라고 이해하면 되지 않을까 싶다.

**slice**는 1개의 차원을 선택해서 필터해서 보는 거라면, **dice**는 2개 이상의 차원을 선택해서 필터를 해서 보는 거라고 생각하면 될 거 같다.

위와 같이 간단한 예시와 함께 보자

|종류|기간|도시|개수|종류|기간|도시|개수|
|---|---|---|---|---|---|---|---|
|컴퓨터|1Q|서울|350|옷|1Q|서울|1000|
|컴퓨터|1Q|부산|100|옷|1Q|부산|800|
|컴퓨터|1Q|광주|250|옷|1Q|광주|600|
|컴퓨터|1Q|수원|300|옷|1Q|수원|900|
|컴퓨터|1Q|성남|500|옷|1Q|성남|700|
|컴퓨터|2Q|서울|450|옷|2Q|서울|2000|
|컴퓨터|2Q|부산|50|옷|2Q|부산|600|
|컴퓨터|2Q|광주|150|옷|2Q|광주|300|
|컴퓨터|2Q|수원|200|옷|2Q|수원|800|
|컴퓨터|2Q|성남|1000|옷|2Q|성남|900|

자 이런 데이터가 있다고 했을 때, 여러개의 차원 즉, 여러 개를 골라보자

글로 설명하기엔 조금 길어질테니 우리가 친숙한 코드로 표현하겠다.

```scala
data.filter(x =>
  (x.city == "서울" || x.city == "성남") &&
    (x.time == "1Q" || x.time == "2Q" )&&
    x.item == "컴퓨터"
)
```

|종류|기간|도시|개수|종류|기간|도시|개수|
|---|---|---|---|---|---|---|---|
|컴퓨터|1Q|서울|350|옷|1Q|서울|1000|
|컴퓨터|1Q|성남|500|옷|1Q|성남|700|
|컴퓨터|2Q|서울|450|옷|2Q|서울|2000|
|컴퓨터|2Q|성남|1000|옷|2Q|성남|900|

이런식으로 나온다.

그림을 통해서 **dice**과정을 보자면 아래 그림과 같다.

![dice](https://www.guru99.com/images/1/022218_1238_WhatisOLAPO5.png)

**slice**나 **dice**나 동일하게 filter라고 생각하면 될 것 같다.

다만 차이라면 **slice**는 필터하는 필드(차원)가 1개, **dice**는 필터하는 필드(차원)이 2개이상 이라는 차이다.

마지막은 **Pivot**이다

**Pivot**은 데이터의 축을 돌려서 데이터를 표현하는 것이다.

어떤 말인지는 표보다는 그림으로 보는 것이 더 이해하는 데 도움이 될 것 같아 그림을 통해서만 살펴본다.

![Pivot](https://www.guru99.com/images/1/022218_1238_WhatisOLAPO6.png)

그림을 보니 이해가 되었을거라 생각하지만 설명을 하겠다.

잘 보면 PC, Book 등이 있는 X 축과 Sydney, Perth 등 도시들이 나열되어 있는 Y축이 서로 바뀌면서(회전함으로써) 데이터의 형태가 변경되었다.

이렇게 **Pivot**을 통해 얻을 수 있는 데이터를 보면 그림을 통해서도 알 수 있다시피 데이터를 바라보는 view 자체가 바뀌어 버린다.

이번 Part 2에서 알아본 **Slice and dice**와 **Pivot** 를 정리하면 다음과 같이 정리할 수 있겠다.

**Slice and dice**은

> 다차원의 데이터를 1개(Slice) 또는 2개 이상(Dice) 필터를 하는 것

**Pivot**은

> 데이터의 차원을 회전시켜 다른 관점으로 데이터를 보는 것

라고 정리하려고 한다.
