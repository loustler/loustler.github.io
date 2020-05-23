---
layout: post
title:  "[OLAP] OLAP의 종류 Part 1. 여러 OLAP의 소개"
date:   2020-04-02 01:05:50 +0900
categories: [data_eng]
tags : [data]
---

다른 것도 그렇듯 OLAP도 여러 종류가 있다.

거기에 대해서 알아보고자 한다.

<!--more-->

OLAP에는 다른 것과 같이 여러 종류가 있는데 그것들이 일종의 hierarchy를 이루고 있다.

아래 그림을 보자.

![Type of OLAP - guru99](https://www.guru99.com/images/1/022218_1238_WhatisOLAPO7.png)

왼쪽부터 차례대로 설명하겠다.

먼저 **ROLAP**는 Relational OLAP로 표준 관계형 연산을 실행하기 위해서 확장하여 다차원 데이터(multidimensional data)맵핑과 함께 확장한 RDBMS다.

**ROLAP**는 RDB에 존재하는 데이터와 함께 동작하는데, 요소(fact)와 차원(dimension) 테이블들이 RDB에 저장된다.

> Fact Table: 트랜잭션처럼 사실이 기록된 것(집계의 기반이 되는 숫자 데이터, 판매액 같은 것), Demension Table: Fact Table의 데이터에 참고되는 master data(주로 데이터를 분류하기 위한 속성값).

또한 다차원 데이터 분석을 할 수 있으며 빠르게 성장하는 OLAP다.

**ROLAP**의 이점은

1. 높은 데이터 효율
   - query의 성능과 액세스 언어가 특히 다차원 형태의 데이터 분석(Multidimensional data anlaysis)에 최적화되어 있음
1. 확장성(Scalability)
   - 대량 데이터를 관리하고 꾸준히 늘어나는 데이터에도 확장성을 제공

단점은

1. 높은 자원 요구
    - 사람의 힘과, 소프트웨어 그리고 하드웨어 리소스에 대해 높은 수준의 활용이 필요함
    - 즉, 유지보수하는데 사람의 리소스가 많이 들어가며, SW/ HW의 리소스가 꽤 필요하다는 말
1. 전체적인 데이터의 한계
    - 데이터에 대한 aggregate의 모든 형태를 SQL을 통해서 실행함
    - 계산처리에 대한 한계가 설정되어 있지 않음
    - 데이터에 대한 것들을 SQL로 처리를 하는데, 이 SQL을 처리하는데 있어서 limit이 없다는 말로 간주됨
1. 느린 쿼리 속도
    - MOLAP와 비교했을 때 비교적 느린 속도를 보임

**MOLAP**는 Multidimensional OLAP로 데이터의 다차원 뷰를 보여주기 위한 array 기반의 다차원 스토리지 엔진이다. 기본적으로 OLAP 큐브를 사용한다.

**HOLAP**는 Hybrid OLAP로 **ROLAP**와 **MOLAP**를 합친 것이다. MOLAP의 빠른 계산과 ROLAP의 높은 확장성을 제공한다.

**HOLAP**는 2개의 DB를 사용하는데

1. Aggregate 됐거나 계산된 데이터들을 다차원 OLAP 큐브를 저장할 DB
1. 자세한 정보를 저장할 RDB

**HOLAP**의 이점은

1. 디스크 공간을 절약하며, 액세스 속도 및 편의와 관련된 문제를 피하는데 도움이 되는 compact 상태를 유지
1. 모든 타입의 데이터에 빠른 성능을 보여주는 cube 기술을 사용
1. **ROLAP**는 즉시 업데이트 되고 **HOLAP**유저들은 업데이트된 데이터에 대해 실시간으로 접근할 수 있음. **MOLAP**는 데이터를 정리하고 변환하여 데이터 관련성을 향상시킴

단점은

1. 매우 복잡한 수준
    - **ROLAP**와 **HOLAP** 툴은 물론 Application까지 모두 지원한다는게 가장 큰 단점. 매우 복잡하기 때문
1. 잠재적인 중복
    - 기능들이 중복될 가능성이 매우 높음

**DOLAP**는 Desktop OLAP로 유저가 DB로부터 데이터를 일부 다운로드 받아서 로컬(데스크탑)에서 분석을 실행함.

이 **DOLAP**는 다른 OLAP 시스템에 비해 기능이 거의 없어 비용이 상대적으로 저렴

**WOLAP**는 Web OLAP로 OLAP system을 웹을 통해 접근하는 것을 말함.

**WOLAP**는 클라이언트, 미들웨어 그리고 DB 서버로 이루어진 3개의 레이어로 아키텍처가 구성되어 있다.

![WOLAP Architecture by Afonso Arriaga](https://www.researchgate.net/profile/Afonso_Arriaga/publication/264845178/figure/fig1/AS:669569249185797@1536649157233/Figura-1-Fluxo-de-informacao-num-sistema-WOLAP.png)

**Mobile OLAP**는 유저들이 모바일 기기를 통해 OLAP 데이터에 접근하고 분석할 수 있도록 도와주는 OLAP

**Spatial OLAP**는 지리 정보 시스템에서 공간 및 비공간 데이터를 쉽게 관리할 수 있도록 만들어졌음

자 그러면 이런 OLAP들을 사용할 때 어떤 이점이 있는지, 어떤 단점이 있는지 알아보면서 마무리하겠다.

OLAP을 사용함으로써 얻을 수 있는 이점은

1. OLAP는 계획, 예산 책정, 보고 및 분석을 포함한 모든 유형의 비즈니스를 위한 플랫폼
1. 일련의 정보와 데이터들은 모두 OLAP 큐브에서 일관되게 관리되며 중요한 장점
1. 어떤 시나리오에 대해 빠르게 시나리오를 만들고 또 분석해볼 수 있음
1. 광범위하거나 특정 용어를 OLAP DB에서 쉽게 검색할 수 있음
1. 비즈니스 모델링 툴, 퍼포먼스 리포팅 툴, 데이터 마이닝 툴을 위한 빌딩 블록을 제공함
1. 사용자가 다양한 차원, 지표 그리고 필터로 OLAP Cube를 **Slice and dice** 할 수 있게 지원해줌
1. 타임 시리즈 분석에 좋음
1. 어떤 군집과 아웃라이어들을 쉽게 찾을 수 있음
1. 빠른 응답시간을 제공하는 강력한 시각화 온라인 분석 프로세스 시스템

단점은

1. OLAP를 위한 데이터는 Star Schema나 Snowflake Schema로 구성해야 됨. 이런 Schema들은 구현과 관리가 복잡함
1. 1개의 OLAP Cube에 많은 수의 차원들을 가질 수 없음
1. 트랜잭션 데이터는 OLAP 시스템으로 접근할 수 없음
1. OLAP cube의 수정은 전체적인 cube의 업데이트를 초래함. 시간을 많이 잡아먹는 프로세스다.

이렇게 OLAP의 여러 종류에 대해 간략히 알아봤고 또 이런 OLAP 시스템들을 사용했을 때 얻을 수 있는 이점과 단점들을 알아보았다.

그러나 **MOLAP**는 다뤄야 하는 게 많아서 [별도로 다루도록 하겠다.](/data_eng/type-of-olap-part-2-molap/)

또한 Star Schema와 Snowflake Schema도 차후에 별도의 포스트에서 다루도록 하겠다.
