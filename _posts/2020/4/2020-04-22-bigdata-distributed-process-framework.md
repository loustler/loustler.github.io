---
layout: post
title:  "[BigData] 대규모 분산 처리"
date:   2020-04-22 00:47:44 +0900
categories: [data_eng]
tags : [data, bigdata]
---

빅데이터를 다룰 때 대규모 분산 처리를 위해서 프레임워크를 사용한다.

대표적으로 Apache Hadoop과 Apache Spark를 사용한다.

<!--more-->

이전에 [[BigData 기초] 1. 빅데이터의 특성과 그 기술](/data_eng/bigdata-basic-1/)에서 **3V** 중 하나인 **다양성(Variety)**을 설명할 때,

**Structured Data**를 비롯해 **Semi-Structured Data**와 **Unstructured Data**에 대해서 언급했었다.

또, **Semi-Structured Data**가 **Schemaless Data**라고도 불린다고도 말했었다.

빅데이터의 데이터는 이런 형식으로 이루어져 있고, 파이프라인을 구성할 때 데이터를 분산 스토리지에 저장한다.

이렇게 분산스토리지에 저장될 때에는, 명확한 스키마를 가지고 있지 않아 SQL을 이용하여 집계를 할 수 없다.

그래서 SQL을 이용하여 집계를 하기 위해 **Structured Data**로 변환하는 것을 진행하게 된다.

보통 이런 **Structured Data**로 변환할 때는 **Columnar DB**를 이용하여 저장을 하게 된다. 왜냐하면 데이터 압축률을 높여 효율적으로 저장할 수 있기 때문이다.

그래서 **MPP DB**에 전송하거나 **Hadoop**에서 Columnar 형식으로 변환한다.

이런 **Structured Data** 중 시간에 따라 증가하게 되는 데이터들을 **Fact Table**, 이것의 부속 데이터를 **Dimension Table**으로 취급한다.

**MPP DB**의 경우 내부에서 어떻게 저장을 하는지에 대한 스토리지 형식이 정해져 있어서 자세히 알 필요는 없지만, **Hadoop**은 이를 선택하여 저장할 수 있다.

[Apache ORC](https://orc.apache.org/)나 [Apache Parquet](https://parquet.apache.org/), [Apache Kudu](https://kudu.apache.org/) 등을 이용하면 된다.

분산시스템을 구성하는데에 **Hadoop**에 100% 의존하지는 않고 경우에 따라,

**HDFS**를 사용하면서 **YARN** 대신 [Mesos](https://mesos.apache.org/)를 사용하거나

MR대신 Spark를 사용할 수도 있다.

**Hadoop**의 **HDFS**를 비롯한 **YARN**과 같은 Ecosystem은 차후에 Hadoop에 대해서 공부하고 정리하면서 다룰 예정이다.
(**Spark**도 마찬가지)

앞에서도 말했다시피 분산 파일 시스템은 보통 **HDFS**를 사용하고, 리소스 매니저로는 YARN이나 Mesos를 사용한다.

분산 파일 시스템은 우리가 그 이름에서 예상할 수 있다시피 파일을 여러곳으로 분산시키고 또 이를 분산된 파일을 보관하고 있는 서버가 죽더라도 문제가 없도록 여러개로 복사하여 여러 서버로 분산한다.

**HDFS**는 기본적으로 3개의 복사본을 가지고 분산시키기 때문에 서버 1대가 죽더라도 2대의 서버에 의해서 안정성을 확보할 수 있다.

**YARN**이나 **Mesos** 같은 리소스 매니저는 이름 그대로 리소스들을 관리해주는 녀석들이다.

만약 이런 분산 시스템에 저장된 데이터들을 SQL을 이용하여 데이터 집계 등을 하려고 한다면 쿼리 엔진이 필요한데 [Apache Hive](https://hive.apache.org/)가 대표적인 쿼리 엔진이다.

**Apache Hive**를 실행시키면 Hadoop MR이 실행되는 것을 확인할 수 있다. 이는 쿼리 엔진이 쿼리를 분석하여 MR을 통하여 실행을 시키기 때문이다.

현재 **Apache Hive**는 Hadoop의 MR뿐만(Hive on MR) 아니라 **Spark**를 이용하여 쿼리를 실행시킬 수 있게 지원해준다.(Hive on Spark, [Hive Jira Issue](https://issues.apache.org/jira/browse/HIVE-7292) 참고)

**Apache Hive**는 배치형 쿼리 엔진으로 대량의 처리하기에 적합하다. 기존에 MR을 사용한 것을 봐도 이런 느낌이 올 것이다. 왜냐면 Hadoop의 MR은 대표적인 대용량 데이터를 위한 배치 처리 시스템이지 않은가

이런 배치형 엔진이 있다면 다른 형태의 엔진도 있다. 대화형 쿼리 엔진으로 분류되는 [Presto](https://prestodb.io/)와 [Apache Impala](https://impala.apache.org/), Spark에 기본으로 내장되어 있는 [Spark SQL](https://spark.apache.org/sql/), [Apache Drill](https://drill.apache.org/) 등이 있다.

이 엔진은 순간 최대 속도를 높이기 위해서 현재 사용할 수 있는 리소스를 최대료 땡겨서 사용하여 빠르게 처리하는 것이 목표다. 그래서 **MPP DB**와 비교해도 나쁘지 않은 응답속도를 자랑한다.


앞에서 **Apache Hive**를 말할 때 Spark를 지원한다고 하였다. 그 이유는 퍼포먼스를 올리기 위해서인데, Spark가 Hadoop MR에 비해 월등하게 속도가 월등하게 빠르기 때문이다(in-memory computing).

그 이유로 Hadoop이 디스크를 활용하기 때문에 IO처리가 많은 반면, Spark는 메모리에 이런 데이터들을 다 적재해서 빠르게 처리하기 때문이다.

IO보다 메모리가 더 빠른 건 누구나 알 수 있는 사실이기 때문에 직감적으로 그 속도의 차이를 느꼈으리라 생각한다.

[Spark](https://spark.apache.org/)에 접속하면 메인 페이지에 바로 보이는 것이 기존의 Hadoop MR에 비해서 100배나 빠르다고 한 것이다.

Spark가 아무래도 퍼포먼스를 비롯해 다양한 부분에서 우수하다보니 MR을 대체하고 Spark로 넘어가는 움직임이 뚜렷하다.

본인이 재직했던 회사 또한 Hadoop MR을 주로 사용하다가 Spark로 넘어갔었다.

이런 Spark가 주류가 됨에 따라 Spark가 꼭 Hadoop을 사용하지 않기도 한다.

또, 분산스토리지로 [AWS S3](https://aws.amazon.com/s3/)를 사용하거나 분산 DB인 [Apache Cassandra](https://cassandra.apache.org/)를 사용하기도 한다.
