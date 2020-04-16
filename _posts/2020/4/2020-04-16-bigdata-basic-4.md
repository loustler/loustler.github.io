---
layout: post
title:  "[BigData 기초] 4. DB의 지연을 줄이기 위한 노력"
date:   2020-04-16 22:32:35 +0900
categories: [data_eng]
tags : [data, bigdata]
---

데이터양이 나날이 증가함에 따라서 데이터의 집계와 분석에 걸리는 시간은 오래 걸린다.

이런 문제를 해결하기 위한 노력에 대한 것 중 DB와 관련된 것의 일부를 공부했고

그것에 대해 정리해본다.

<!--more-->

# 문제
데이터 양이 증가하면서 매번 집계와 분석에 대한 시간이 늘어가고 있음

이로 인해 대기 시간이 길어짐에 따라 하고 있던 집계나 분석 작업에 병목이 발생하게 됨

이 문제는 작업의 효율성을 비롯하여 다양한 문제를 일으킨다.

그러나 보통 데이터 수집 단계에서는 이런 부분을 고려하지 않기 때문에 3계층(3 layers)로 구성을 하게 된다.

Data Lake(DW) -> Data Mart -> Visualization(BI Tools)

# 해결
1. **모든 데이터를 메모리에 적재**
1. **압축과 분산**
1. **MPP(Massive Parallel Processing) DB**

## 모든 데이터를 메모리에 적재
가장 간단한 방법이다

그런데 데이터가 만약 5GB~10GB 정도의 작은 데이터양이라면 RDB를 Data Mart로 활용하는것이 더 낫다.

왜냐하면 RDB는 지연이 적게끔 설계가 되어 있고, 많은 수의 클라이언트가 접속을 해도 성능이 나빠지지 않게끔 되어 있으므로 적합할 것이다.

> 그러나 RDB는 메모리가 부족해지면 성능이 급격히 저하된다

## 압축과 분산
고속화를 사용되는 기법

데이터를 가능한 작게 압축하고 그것을 여러개의 디스크에 분산시켜 데이터 로드의 지연을 줄인다.

이런 분산된 데이터를 읽으려면 멀티코어를 활용해 디스크 I/O를 병렬처리하는 것이 효과적이다.

이런 아키텍처를 **MPP(Massive Parallel Processing)**이라고 함

**MPP**는 데이터 집계에 최적화되어 있고, DW와 데이터 분석용 DB에서 특히 많이 사용됨

[AWS Redshift](https://aws.amazon.com/redshift/)와 [Google Cloud BigQuery](https://cloud.google.com/bigquery)가 MPP를 지원한다.

### Column Oriented Database(Columnar Database)
컬럼 단위의 데이터 압축을 하고 컬럼 단위의 데이터 처리 및 집계를 할 때 사용하는 DB

기존 우리가 일반적으로 사용하는 RDB는 Record, 즉 Row Oriented Database

Columnar DB는 기존에 우리가 알고 있던 RDB와는 다르게 Column단위로 데이터를 저장한다

[Teradata](https://www.teradata.com/)와 [AWS Redshift](https://aws.amazon.com/redshift/) 등이 있다.

[HBase](https://hbase.apache.org/) 또한 Column oriented database(Columnar Database)로 볼 수 있다([HBase in Practice by Lars George](https://www.slideshare.net/larsgeorge/hbase-in-practice) 참고).


> [Cassandra](http://cassandra.apache.org/)는 **Columnar DB**가 아니라고 한다.

[Cassandra Github](https://github.com/apache/cassandra)의 소개만 봐도 **Columnar DB**라고 하지 않고

> Apache Cassandra is a highly-scalable partitioned row store

라고 소개하고 있다.

데이터를 분석할 때는 데이터들의 일부만, 몇개의 column들만 aggregate의 대상이 되는 경우가 많다. 이럴 경우 **Columnar DB**는 그 column들만 load하면 되므로 필요없는 column까지 로드해야되는 **Row Oriented DB**와는 다르게 빠르게 데이터를 로드할 수 있다(**Columnar DB**는 column별로 데이터를 모아놓기 때문).

데이터 압축률 또한 우수한데, 종종 1개의 column에 같은 데이터가 들어가는 경우가 있다.이 때 같은 문자열의 반복 등은 매우 작게 압축할 수 있는데, 경우에 따라서 **Row Oriented DB**에 비해 1/10까지 데이터를 압축할 수 있다고 한다.

### Row Oriented Database
우리가 가장 쉽게 접하고 있는 방식의 DB가 아닐까 한다

[PostgreSQL](https://www.postgresql.org/)처럼 말이다

이 DB는 row단위를 하나의 덩어리로 디스크에 저장을 하는데, 이는 새 레코드가 추가될 때마다 파일의 끝에 데이터를 append하여 빠르게 추가한다.

매일 발생하는 혹은 자주 발생하는 대량의 트랜잭션을 지연없이 처리하기 위해서 데이터가 추가를 효율적으로 할 수 있도록 하는 것이 Row Oriented Database의 목적이다.

우리는 이런 DB의 형태인 RDB를 사용할 때 주로 검색을 컬럼 단위로 하게 된다

```sql
SELECT *
FROM user
WHERE id = 'user-id'
```

같은 형태로 말이다

그래서 Row Oriented DB들은 **Index**를 통해 검색을 빠르게 할 수 있게 해준다.

**Columnar Database**와 **Row Oriented Database**의 차이점은 아래 그림을 통해서도 한눈에 알 수 있다.

![row-store vs column-store by w3resource](https://www.w3resource.com/w3r_images/key-value-store.png)

### MPP DB
**Row Oriented DB**는 보통 1개의 Query는 1개의 thread에서 실행된다.

물론 여러개의 CPU 코어를 이용하여 많은 query를 동시에 실행할 수 있지만 그렇다고 분산처리가 되는 것은 아니다.

하지만 **Row Oriented DB**는 보통 각 Query가 충분히 짧은 시간에 끝나는 것을 가정하여 분산처리하는 과정을 가정하지 않는다.

**Columnar DB**는 대량의 데이터를 읽다보니 1개의 query의 실행시간이 오래 걸린다. 또한 데이터를 압축해놓으므로 CPU의 리소스를 추가적으로 필요로 한다. 그래서 이를 빠르게 처리하기 위해서 멀티 코어를 사용해야 한다.

**MPP**는 하나의 query를 다수의 작은 Task로 나누고 가능한 병렬로 실행한다.

예를 들면 1억개의 레코드를 가진 테이블의 aggregate를 한다면, 1K개의 Task로 나누고 각각 aggregate를 한뒤에, 그것들을 다시 aggregate 하는 형태다.

만약 Query의 병렬화가 잘 될 수 있다면, CPU 코어수에 비례해서 그 속도가 빨라진다. 단 디스크로부터 로드가 병목이 되지 않게 데이터의 분산이 적절히 고르게 이루어져 있어야 한다.

**MPP**는 퍼포먼스를 위해서 CPU와 Disk 모두를 균형있게 늘려주어야 한다.

> **MPP** 아키텍처는 Hadoop과 함께 사용하는 대화형 쿼리 엔진으로도 채택된다, 이때 데이터를 저장하는 건 분산 스토리지의 역할

