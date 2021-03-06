---
layout: post
title:  "[OLAP] OLAP의 종류 Part 2. MOLAP"
date:   2020-04-08 00:16:50 +0900
categories: [data_eng]
tags : [data]
---

저번 [Part 1](/data_eng/type-of-olap-part-1/)에서 미처 다루지 못했던

MOLAP에 대해서 공부한 내용을 다루고자 한다.

<!--more-->

**MOLAP**는 저번 [Part 1](/data_eng/type-of-olap-part-1/)에서도 말했고 이전에도 말했었지만,

Multidimensional OLAP, 다차원 OLAP이다.

**MOLAP**는 classical OLAP로 다차원 데이터 큐브(multidemensional data cube)를 이용해서 데이터 분석을 수월하게 할 수 있는 녀석이다.

이 때 Data는

1. 사전에 계산되고(pre-computed)
1. 사전에 요약되며(pre-summarized)

무엇보다 **MOLAP**에 저장이 된다.

**MOLAP**에 저장이 되는 부분이 **ROLAP**와의 차이점인데, **ROLAP**는 RDB에 저장을 하기 때문이다.

**MOLAP**를 사용하면, 유저는 다양한 측면으로 다차원 뷰 데이터(multidemensional view data)를 사용할 수 있다.

이는 다양한 시각으로 다차원 뷰 데이터를 볼 수 있다는 말과 같을 것이다.

다차원 데이터 분석은 RDB 또한 사용이 가능하며 이를 위해 쿼리를 여러 테이블에 해야한다.

이와 반대로 **MOLAP**는 이미 다차원 배열(multidimensional array)에 모든 조합 데이터들이 적재되어 있으며 직접적으로 데이터에 접근할 수 있다.

이 말은,

- 다차원 데이터 분석은 RDB를 사용할 수 있다. 그러나 사용하기 위해서는 SQL 등을 이용해서 쿼리를 여러 테이블에 해야 된다.
- 다차원 데이터 분석을 **MOLAP**를 이용하면 위에 말했듯이 사전에 계산되고 요약된 데이터들이 이미 다차원 배열에 적재되어 있다

와 같다.

즉, RDB를 사용하여 다차원 데이터 분석을 하려면 데이터를 쿼리를 이용하여 데이터를 그 때마다 계산하고 요약하는 등의 행위가 동반되기 때문에,

빠르게 데이터를 분석하기에 어려움이 있다는 것이고, 그래서 **ROLAP**보다는 **MOLAP**가 빠르다고 할 수 있다.

**MOLAP**의 몇가지 키포인트를 짚고 가면,

1. **MOLAP**에서 operation은 processing이라고 함
1. MOLAP Tool들은 요약 수준에 상관없이 동일한 응답 시간으로 정보를 처리함
1. MOLAP Tool들은 분석을 위한 데이터를 RDB에 적재하기 위해 해야 하는 설계의 복잡함을 제거함
1. MOLAP 서버는 밀도가 높고 드문 데이터 셋을 관리하기 위해 2개의 레벨의 스토리지 표현을 구현했음
1. 데이터 셋이 드문경우에는 스토리지의 활용도가 떨어질 수 있음
1. 사실(fact)은 다차원 배열에 저장되고, 차원(dimension)은 그런 사실들을 쿼리하는데 사용됨

**MOLAP Architecture**

**MOLAP Architecture**는 아래와 같은 컴포넌트(component)들로 구성되어 있다.

- Database Server
- MOLAP Server
- Front-end tool

컴포넌트들을 먼저 그림을 통해 보자면 아래와 같다.

![MOLAP Architecture by guru99](https://www.guru99.com/images/1/022218_1034_MOLAPMultid1.png)

위 그림에 표시된 대로 flow를 보면

1. 유저가 인터페이스를 통해서 리포트를 요청
1. MDDB(MultiDimansional DataBase)의 어플리케이션 로직 레이어가 DB로 부터 데이터를 가지고 옴
1. 어플리케이이션 로직 레이어가 클라이언트와 유저에게 가져온 데이터 결과를 반환

**MOLAP Architecture**는 주로 미리 컴파일된 데이터들을 읽는데,

이는 **MOLAP Architecture**가 동적으로 집계를 생성하거나 사전에 계산되지 않았거나 저장되지 않은 결과를 계산하는데에는 기능이 제한적이기 때문이다.

이런 MOLAP를 구현하는데에 있어 고려해야 될 것들은 다음과 같다.

1. **MOLAP**는 큐브를 구축하기 위한 전략을 만드는데에 있어서 유지보수와 스토리지의 영향에 대해 고려가 필수적
1. **MOLAP**의 쿼리를 위해 지원되는 언어가 제한적
1. 차원이 늘어날 때, 요구되는 큐브의 숫자와 사이즈로 인해 확장의 어려움이 있음
1. API가 큐브를 probing 하기 위해 제공되어야 됨
1. 데이터 구조는 탐색되고 분석될 데이터의 여러 주제 영역을 지원해야 되며, 탐색이 변경되었을 때 데이터 구조를 물리적으로 재구성해야 될 필요가 있음
1. DBA가 DB를 구축하고 유지보수하는데 각기 다른 스킬셋과 툴이 필요함

그리고 마지막으로 MOLAP와 이점과 단점을 알아보고 마무리하겠다.

이점은

1. **MOLAP**는 상당한 양의 다차원 데이터를 관리, 분석 그리고 적재할 수 있음
1. 최적화된 스토리지, 인덱싱, 캐싱에 빠른 쿼리 성능을 보여줌
1. RDB와 비교해서 비교적 작은 사이즈의 데이터
1. 더 높은 수준의 집계 데이터의 자동 계산
1. 유저가 크고 덜 정의된 데이터를 분석할 수 있도록 도와줌
1. 사용하는데 있어서 많은 경험이 필요하지 않음
1. MOLAP 큐브는 빠른 데이터 검색을 위해 만들어졌으며 **Slice and dicing** 연산에 최적화되어 있음
1. 큐브를 만들 때 모든 계산이 미리 생성됨

단점은

1. 주요 단점으로 제한된 양의 데이터만 처리하므로 ROLAP에 비해 확장성이 떨어짐
1. 자원집약적으로 데이터 중복성이 도입됨
1. 대용량 데이터에서 오래 걸림
1. 10개 차원이 넘어갈 경우 모델을 업데이트하고 쿼리하는 동안 MOLAP 제품에 문제가 생길 수도 있음
1. 상세 데이터를 포함할 수 없음
1. 데이터 셋이 분산이 많이 되어 있을 경우 활용도가 낮을 수 있음
1. 제한된 양의 데이터만 처리할 수 있으므로 큐브 자체에 많은 양의 데이터를 포함할 수 없음

