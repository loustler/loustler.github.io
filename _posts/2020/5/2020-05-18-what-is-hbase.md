---
layout: post
title:  "[Apache HBase] HBase가 무엇일까"
date:   2020-05-19 00:033:44 +0900
categories: [data_eng]
tags : [data, bigdata, hbase]
---

우리가 흔히 알고 있는 NoSQL DB하면 떠오르는 DB 중 하나이면서,

Hadoop에서 많이 사용되고 있는 HBase는 어떤 녀석일까?

<!--more-->

HBase는 우리가 흔히 알고 있는 Hadoop에서 파생된 녀석이다.

# 왜 나왔을까?

HBase는 Hadoop의 자체의 한계로 인해 나오게 되었다.

Hadoop은 우리가 알고 있다시피 MR을 이용한 분산 데이터 프로세싱을 하는 녀석이다.

그것도 매우 큰 사이즈의 데이터를 다루는, 심지어 배치로 말이다.

특히 하둡은 순차적으로 만들어진 데이터에만 액세스할 수 있었고, 만약 간단한 작업이라도 하려면

전체 데이터셋을 검색해야 되었다.

그리고 이런 작업이 혹여나 데이터 셋을 만들어내게 되면, 새로운 데이터셋이 발생하므로 이것을 또 순차적으로 처리해야되는 문제점이 있었다.

이것을 해결하기 위해서 단일 시간 단위로 데이터를 액세스하기 위해서 탄생한 녀석이 HBase다.

우리가 알고 있는 Cassandra, AWS의 Dynamo, MongoDB 와 같은 녀석들은 빅데이터를 저장하고 랜덤 액세스를 통해 데이터에 액세스하는 DB들로, HBase 또한 그렇다.

# HBase?
HBase는 [이전 포스트](/data_eng/bigdata-basic-4/)에서 언급했다시피 분산된 **Columnar DB**이며, Hadoop File System(HDFS) 위에 구축된 녀석이다.

HBase는 분산환경에서 동작하는 녀석인만큼 수평 확장이 가능하다.

수평확장은 우리가 스케일 아웃(Scale Out)으로 알고 있는 녀석이고, 수직확장은 우리가 스케일 업(Scale Up)으로 알고 있는 녀석이다.

다 알고 있겠지만 필자도 다시 한 번 되새길겸 간단하게만 작성하면,

스케일 아웃은 서버에서 서버의 Node를 늘리는 것과 같은 녀석이고, 스케일 업은 서버에서 그 스펙을 올리는 것이다.

어찌됐든 HBase로 다시 돌아오면,

HBase는 Google의 GCP에서 사용할 수 있는 BigTable과 유사한 디자인으로 되어 있는데,

거대한 양의 구조화된 데이터(Structured Data)에 빠르게 랜덤 액세스할 수 있도록 해준다.

HBase는 Hadoop ecosystem의 일부로 실시간 읽기와 쓰기를 HDFS에 가능하게 해준다.

Data Consumer는 HBase를 이용해서 HDFS에 있는 데이터에 랜덤하게 액세스와 읽기를 할 수 있다(HBase 디자인 자체가 랜덤 액세스이기 때문)

이렇게 HBase는 HDFS 위에서 HDFS에 대한 읽기/접근에 대한 기능을 제공해준다.

자꾸 HDFS와 함께 엮어서 설명하다보니 혹시라도 혼동될까봐 확실히 정리하자면

|HDFS|HBase|
|분산 파일 시스템으로 큰 파일 저장하는데 적합|HDFS 위에 구축된 **DB**|
|각 레코드들을 조회할 수 있는 빠른 방법을 제공해주지 않음|큰 테이블에 대한 빠른 조회를 제공|
|긴 시간의 배치 프로세싱을 제공해주며, 배치 프로세싱 컨셉은 없음|짧은 시간에 백만단위의 레코드로부터 각 row를 접근할 수 있음|
|데이터에 순차 액세스만 지원|내부에서 Hash Table을 사용하며, 랜덤 액세스 지원. 빠른 조회를 위해 HDFS 파일을 인덱스|

# 언제 사용? - Use Cases
HBase는 ACID 지원하는 이상적인 플랫폼으로

1. High Scale
1. real-time application

을 위해 최적의 선택이다.

또, HBase는 고정된 스키마를 요구하지 않으므로 개발자가 사전정의된 모델을 따르지 않고도 새로운 데이터를 쉽게 추가할 수 있다.

# 저장 원리?
앞에서도 말했듯이 HBase는 **Columnar DB** 즉, **Column Oriented DB**이다.

그리고 HBase의 테이블들은 row로 정렬이 되어 있다.

또한 테이블 스키마를 정의할 때는 **Column Family**라는 녀석만 정의하면 된다.

**Column Family**라는 녀석은 key와 value로 이루어진 pair다.

테이블은 이런 **Column Family**를 여러개를 가지고 있고 각 **Column Family**는 여러개의 컬럼을 가지고 있다.

다음 Column의 값은 디스크에 이어서 연속적으로 저장이 되고, 각 **Cell**의 값에는 timestamp가 포함되어 있다.

요약하자면

- 테이블은 **Row**의 집합
- **Row**는 **Column Family**의 집합
- **Column Family**는 **Column**의 집합
- **Column**은 **Key Value Pair**의 집합

아래 그림을 통해서 이런 것들을 더 직관적으로 살펴볼 수 있다.

![HBase Data Model by dwgeek](https://dwgeek.com/wp-content/uploads/2017/09/Apache-HBase-Data-Model-Explanation.jpg)


여기까지 간단하게 HBase에 대해서 공부한 것을 다시 정리해보았다.

이 포스트를 요약해보면

1. HBase는 Hadoop의 데이터 순차 액세스에 대한 한계로 인해 나왔으며 랜덤 액세스 기반이다
1. HBase는 Columnar DB로 HDFS 위에 구축되었고 분산환경에서 동작한다.
1. HBase의 테이블은 여러개의 **Row**로 이루어져 있고, **Row**는 여러개의 **Column Family**로, **Column Family**는 여러개의 **Column**으로, **Column**은 여러개의 **Key Value Pair**로 이루어져 있다.

