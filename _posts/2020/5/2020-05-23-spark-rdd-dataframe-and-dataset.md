---
layout: post
title:  "[Apache Spark] Spark RDD, DataFrame and DataSet"
date:   2020-05-23 16:43:14 +0900
categories: [data_eng]
tags : [data, bigdata, spark]
---
Spark API 중에 중요하면서 유사한 RDD, DataFrame 그리고 DataSet에 대해서 공부한 것을 정리했다.

<!--more-->

# RDD(Resilient Distributed DataSet)
## 지난 포스트에서 설명한 것들
**RDD**는 주된 스파크 프로그래밍 추상화의 구조이며, 여러 노드에 흩어져 있으면서 병렬처리 될 수 있는 아이템들의 모음을 말한다.

또한 모든 스파크 어플리케이션의 빌딩 블록이다.

**RDD**의 각 의미를 알아보면

- **Resilient**: 내결함성 및 장애시 데이터를 재구성할 수 있다.
- **Distributed**: 클러스터의 여러 노드에 분산된 데이터
- **DataSet**: 값과 함께 파티션된 데이터 모음

또한 **RDD**는 기본적으로 lazy transformation을 따르며 본질적으로 불변이며,

**RDD**의 데이터는 키를 기준으로 chunk로 분할된다.

**RDD**는 동일한 데이터 chunk가 여러개의 노드에 복제되므로 모든 문제에서 빠르게 복구할 수 있기 때문에 복원력이 뛰어나다.

따라서 만약 1개의 노드가 실패하더라도 다른 노드에서 데이터 처리를 진행한다.

이로 인해 빠르게 데이터 셋에 대한 계산을 처리할 수 있다.

분산환경에서 **RDD**에 있는 각 데이터셋은 논리적 파티션으로 분할되며, 클러스터의 다른 노드에서 계산될 수도 있다.

이로 인해서 데이터의 변환(transformation)이나 실행(action)을 병렬적으로 실행할 수 있다.

이것들에 대해선 **Spark**가 책임지고 관리하므로 신경쓸 필요는 없다.

**RDD**가 어떻게 처리되는지 아래 그림을 통해 알아보자.

![RDD Workflow](/files/rdd-workflow.png)

참고로 **RDD**를 생성하는 방법으로는

1. 외부 데이터셋 로드(HDFS, HBase 등)
1. 드라이버 프로그램에서 객체 컬렉션을 분산

2가지의 방법을 할 수 있다.

그리고 위에서 **Transformation**과 **Action**에 대해서 이야기 했었다.

이에 대해서 잠깐 알아보자면

- **Transformation**: 이미 존재하는 **RDD**에서 새로운 **RDD**를 만들어내는 연산
- **Action**: **RDD**를 기초로 결과 값을 계산하여 그 값을 **Driver Program**에 되돌려주거나 외부 스토리지(HDFS 등)에 저장하는 연산

**Spark**는 **RDD**가 처음에 **Action**을 할 때 처리를 시작하기 때문에 *lazy evalution* 방식이다.

## 이번 포스트에서는
### 캐싱과 수동 파티셔닝
**RDD**는 캐싱과 수동 파티셔닝 할 수 있다.

- 캐싱(Caching)
  - **RDD**를 여러번 사용할 때 이점을 얻을 수 있음
  - **RDD**는 action이 발생할 때마다 실행을 하기 때문에 한 번 캐싱을 하면 캐싱한 것을 사용하게 됨
  - `RDD.persist()`를 이용하여 캐싱이 가능하며, 메모리 캐싱이 기본이며 메모리와 디스크에서 양자택일 혹은 모두 사용이 가능(디스크만 사용한다던지, 메모리와 디스크를 같이 써서 메모리로 충분하지 않다면 디스크에도 같이 한다던지)
  - 캐싱은 LRU 기법을 사용하여 최근에 사용한 것들만 남게 됨
- 수동 파티션(Manually partitioned)
  - 정확하게 균형이 맞게 파티션을 해야 됨
  - **RDD**가 `(Key, Value)` 형태라면 `RDD.partitionBy`와 같은 메서드의 사용으로 손쉬운 파티셔닝이 가능
  - `HasPartitionor`와 `RangePartitionor` 등 기본적으로 제공해주는 것들이 있고 커스터마이징 또한 가능

### RDD가 필요한 이유
**RDD**는 **Spark**의 주된 추상화 프로그래밍 구조라고 말했었다.

그렇다면 **RDD**가 필요한 이유는 무엇일까?

**RDD**의 컨셉이 이렇게 된 핵심 동기는 아래와 같다

1. 반복 알고리즘
1. 대화식 데이터 마이닝 도구
1. DSM(Distributed Shared Memory, 분산 공유 메모리)은 정말 일반적인 추상화이지만 이 일반성이 상용 클러스터에 효율적이고 내결함성(fault-tolerant)이 있는 방식으로 구현하기에 어렵다
1. 분산 컴퓨팅 시스템에서 데이터는 HDFS나 AWS S3와 같은 안정적인 중간 분산저장소에 저장이 되게 된다. 이것은 많은 IO 작업과, 복제 및 직렬화를 유발하므로 작업의 계산 속도가 느려지게 된다

이런 동기들로 인하여 **RDD**가 필요하게 된다.

사실 첫번째와 두번째는 데이터를 메모리에 넣어버리면 성능을 크게 향상시킬 수 있다.

**RDD**를 설계하는데 있어 주요 과제는 바로 내결함성(fault-tolerant)을 효율적으로 제공하는 인터페이스를 정의하는 것이다. 그래서 **RDD**는 이런 내결함성을 효율적으로 달성하기 위해서 공유상태에 대해서 *fine-grained* 업데이트보다 *coarse-grained transformation*에 기반해서 제한된 형태의 공유 메모리를 제공한다.

> fine-grained: 더 세밀한 요소로 나누는 것, coarse-grained transformation: 큰 덩어리로 나누어 변환

### RDD과 DSM의 비교

||RDD|DSM|
|Read|*coarse-grained*과 *fine-grained* 중 선택해서 진행. *coarse-grained*는 전체 데이터셋의 변환은 가능하지만 데이터셋의 개별요소는 변환 불가능. *fine-grained*는 개별 요소의 변환이 가능|*fine-grained*로 동작|
|Write|*coarse-grained*|*fine-grained*|
|Consistency|**RDD**는 계속 말해왔듯 불변이다.|규칙을 준수하면 메모리가 일관되므로 작동결과의 예측을 보증|
|Fault-Recovery|*lineage graph*로 쉽게 복구 가능. **RDD**는 불변이고 **Transformation**마다 새로운 **RDD**가 생성되기 때문에 쉬운 복구가 가능|*checkpoint*을 이용하여 롤백하여 그 지점에서부터 다시 시작|
|Straggler Mitigation|backup task를 이용|마이그레이션의 달성이 어려움|
|RAM이 충분하지 않을때|disk로 이동|성능 저하|

# DataFrame
**DataFrame**은 이름이 있는 *column*으로 구성된 RDB의 테이블과 유사한 테이블 형식이다.

혹시 R이나 Python을 해본 분이라면 익숙할 수도 있다.

**DataFrame**은 많은 양의 구조화된 데이터를 처리할 수 있다는 것이다.

스키마를 가졌다는 것인데, 이 스키마는 데이터의 구조를 보여주기 때문이다.

## DataFrame, RDD
**RDD**보다 우세하지만 **RDD**의 기능도 포함되어 있다.

**RDD**와의 공통점으로는

1. 불변성
1. 인메모리
1. 탄력성
1. 분산 컴퓨팅

이다.

이런 것들로 인해 유저는 분산된 데이터 컬렉션에 구조를 입힐수가 있다.

## Data Source
**RDD**처럼 다양한 data source를 사용할 수 있다.

Hive 테이블이라던지, RDB, CSV, TSV 처럼 구조화가 되었거나 JSON, XML 같은 반구조화 데이터도 가능하다

## Why DataFrame?
**DataFrame**은 기존 **RDD**보다 한 발 더 앞서나가있다고 볼 수 있다.

메모리 관리측면과 실행 계획의 최적화 때문이다.

### 메모리 관리
*Thungsten 프로젝트*라고도 알려져 있다. *Thungsten 프로젝트*에 관해서는 DataBricks사의 [Tungsten 포스트](https://databricks.com/glossary/tungsten)와 [Project Tungsten 포스트](https://databricks.com/blog/2015/04/28/project-tungsten-bringing-spark-closer-to-bare-metal.html)에서 설명해주고 있다.

간략하게 설명하자면

- Data를 binary format으로 off-heap memory에 저장하면 메모리를 많이 아낄 수 있다
- GC의 overhead도 발생하지 않는다는 것이다.
- 비용이 비싼 Java Serialization도 피할 수 있다.

이것이 가능한 것은 데이터가 binary format으로 저장되고 스키마를 알고 있기 때문이다.

### 실행계획 최적화
*Query Optimizer*라고도 알려져 있다.

*Query Optimizer*를 통해 실행된 query로부터 최적화된 실행 계획을 만들게 된다.

결국 이렇게 최적화된 실행 계획이 생성되면 **RDD**에서 실행된다.

## 기능
**RDD**의 한계는

1. 내장된 최적화 엔진이 없다
1. 정형 데이터를 정의하고 처리할 수가 없었다

으로 명확했다. 이를 극복하기 위해서 나온 것이 **DataFrame**이다.

**DataFrame**은

1. *column*이 이름을 가지며 RDB의 테이블과 유사하다
1. 구조화/비구조화 데이터를 모두 처리할 수 있다.
1. **Catalyst Optimizer**가 있다
1. 다양한 언어 제공(Java, Scala, Python, R)
1. Hive 호환성 제공. Hive Query Language(HQL) 사용 가능
1. 다른 빅데이터 도구 및 프레임워크와 통합성

### Catalyst Optimizer
**DataFrame**은 이 **Catalyst Optimizer**를 이용해 최적화를 하는데

다음과 같은 4단계로 Catalyst Tree로 변환한다

1. 논리 계획 분석을 하여 참조 해결
1. 논리 계획 최적화
1. 실제 계획
1. *query*의 일부를 Java의 bytecode로 컴파일하기 위한 코드 생성

**Catalyst Optimizer**에 더 궁금하다면 아래의 포스트를 참고하면 되겠다

- [Catalyst Optimizer by DataBricks](https://databricks.com/glossary/catalyst-optimizer)
- [Understanding the Catalyst Optimizer by DataFlaire Team](https://data-flair.training/blogs/spark-sql-optimization/)

## Spark Session
**DataFrame**은 *SparkSession*이라는 녀석이 있다.(**DataSet** 또한 마찬가지)

**Spark 2.0.0** 버전에서 나온 **Spark SQL** entry point이다.

## 한계점
이런 **DataFrame** 또한 한계점이 존재한다.

1. Compile-time type safety를 제공해주지 않는다
   - 만약 스키마를 알 수 없다면 데이터를 조작할 수 없다
1. Domain Object를 한 번 **DataFrame**으로 변경하면 다시 Domain Object로 돌릴 수 없다.

# DataSet
**Spark 1.6**에 나온 **Spark SQL**의 데이터 구조며, 강력한 타입과 관계형 스키마로 변형할 수 있다.

**encoder**와 함께 구조화된 *query*를 나타낸다.

**DataFrame** API를 확장한 것으로, type safety와 OOP interface를 제공한다.

## Encoder
**encoder**는 Spark SQL의 Serialization/Deserialization의 기본 개념이다.

**encoder**는 *JVM Object*와 Spark 내부 binary format를 서로 변환해준다.

**Spark**는 **Advanced Encoder**를 내장하고 있다.

이 **encoder**를 통해 bytecode를 만들고 off-heap data와 상호작용한다.

> Off-heap은 간략하게 말하면 JVM Heap Memory 영역이 아닌 곳을 말한다.

**encoder**는 전체 객체를 Deserialize 할 필요가 없기 때문에 개별 속성에 대해 on-demand access를 제공한다.

**Spark SQL**은 input-output time과 공간을 효율적으로 만들기 위해서 SerDe Framework를 사용한다.

또한 **encoder**는 이미 스키마에 대해서 알고 있기 때문에 Serialization/Deserialization을 할 수 있게 된다.


**DataSet** 또한 **RDD**나 **DataFrame**과 같이 lazy하게 동작하며 **Action**을 통해서 실제 계산을 한다.

내부적으로 **DataSet**은 논리적 계획으로 표현되며, 이 논리적 계획은 데이터를 생성해야 된다는 계산적인 *query*를 알려주게 된다.

논리적 계획은 base catalyst query plan으로 논리적 연산자가 논리적 query 계획을 구성하기 위한 녀석이다.

이를 분석하고 해결하여 실제 query 계획을 만들 수 있다.

**DataSet**은

1. **RDD**의 편의성
1. **DataFrame**의 퍼포먼스 최적화
1. 스칼라의 정적 type-safety

를 제공해준다.

## Why DataSet?
**DataSet**은 **RDD**와 **DataFrame**이 가진 한계를 극복하고자 탄생되었다.

**DataFrame**은 compile time type-safety가 보장되지 않으므로 구조를 알지 못하면 데이터의 조작에 어려움을 겪는다.

또한 **RDD**에는 자동 최적화가 없어 필요할 때 수동으로 최적화를 해야 했다.

## 기능
**DataSet**은 그래서 다음과 같은 기능을 제공해준다.

1. 최적화된 *query*
  - **Catalyst Query Optimizer**와 *Tungsten*을 통해 최적화된 *query* 제공
1. compile-time 분석
  - compile-time에 syntax와 분석을 확인할 수 있음.
  - **DataFrame**이나 **RDD**에서는 지원되지 않음
1. Persistent Storage
  - Persistent storage에 저장을 할 수 있음
1. 내장된 변환
  - `toDS`를 통해 **DataSet**으로
  - `toDF`를 통해 **DataFrame**으로
1. 빠른 계산
  - **RDD**보다 빠름
  - **RDD**는 수동으로 직접 퍼포먼스 최적화를 해야됨
1. 적은 메모리 사용
  - 캐싱을 하면 최적의 레이아웃을 만들어 메모리 사용률을 줄임(binary format)
1. 자바와 스칼라에 API 제공

# RDD vs DataFrame vs Dataset

||**RDD**|**Data Frame**|**DataSet**|
|Release|`1.0`|`1.3`|`1.6`|
|Data Representation|Java나 Scala object|*column*의 이름이 있는 organized된 분산 컬렉션. RDB 테이블과 유사|**Data Frame**의 확장. type-safe, oop interface 제공, Catalyst query optimizer 의 성능 이점, off heap storage 메커니즘 제공|
|Data Format|비정형/정형데이터의 쉽고 효율적인 처리. **Data Frame**과 **DataSet**처럼 스키마를 유추하지 않음|비정형/반정형 데이터에서만 동작. *column name*을 가진 형태로 **Spark**가 스키마를 관리|**DataFrame**과 같이 정형/반정형 데이터의 쉽고 효율적인 처리. *row*의 JVM object 형태나 *row object collection* 형태로 표현. **encoder**를 통해 테이블 형태로 표현|
|Data Source API|어떤 데이터소스든 사용가능|**AVRO**, **CSV**, **TSV**, **JSON**, **HDFS**, **HIVE Table**, **RDB** 등의 데이터 소스가 가능|어떤 데이터소스도 가능|
|immutablility, interoperability|**RDD**는 기본적으로 불변. **RDD**가 표형식일경우 `RDD.toDF`로 **DataFrame**으로 변경가능|**DataFrame**으로 변환하고 나면 domain object를 재생성할 수 없음. `RDD.toDF`를 할경우 원래 형태의 `RDD`로 돌아올 수 없다는 것|**DataFrame**의 확장버전으로 **RDD**와 **DataFrame**을 **DataSet**로 변환할 수 있음|
|compile time type safety|친숙한 OOP 스타일과 compile-time typesafey 제공|**DataFrame**에 존재하지 않는 *column*에 접근하려고 하면 compile-time typesafe를 보장하지 않음. run-time에서만 확인가능|compile-time safe 지원|
|Optimization|Optimization engine이 없음. 개발자가 최적화해야 됨|Catalyst Optimizer가 있음. 이를 이용해서 최적화 진행|쿼리 계획 최적화를 위해 **DataFrame**의 Catalyst optimization이 있음|
|Serialization|Java Serization 사용|Off Heap storage를 사용(InMemory)하여 binary format으로. Schema를 알고 있기 때문에 가능. Tungsten 실행 백엔드를 가지고 있어서 메모리를 명시적으로 관리하고 동적으로 bytecode를 만듬|**Encoder**가 있기 때문에 Spark 내부의 Tungsten binary format 사용|
|GC(Garbage Collection)|각 Object의 생성과 파괴로 인한 GC의 overhead가 있음|각 *row*의 개별 Object를 구성할 때 GC 코스트 방지|Serialization때문에 GC가 OBject를 파괴할 필요가 없음. *off heap data serialization*을 사용함|
|Efficiency / Memory use|Java와 Scala object에서 개별적으로 Serialization을 하면 효율성이 저하됨|Serialization에 `off heap memory`를 사용하면 overhead가 줄어듬.|Serializaed data에 대한 작업을 수행하고 메모리 사용성을 개선할 수 있음|
|Lazy Evolution|기본적으로 lazy evolution. **Transformation**의 경우 이것을 실행했다는 것만 기억하고 **Action**이 **Driver Program**에 결과를 보낼 필요가 있을 때만 계산|Lazy evolution. **Action**이 나타날 때만 동작함|**RDD**나 **DataFrame**과 같음|
|Language Support|Java, Scala, Python, R|**RDD**와 같음|Scala와 Java. Spark 2.1.1 기준|
|Schema Projection|명시적으로 사용됨. 스키마 정의가 필요함|데이터 소스로부터 스키마를 검색하고 수행함. Hive의 경우 Hive Meta store, DB의 경우 DB Engine.|Spark SQL Engine을 사용하여 자동 탐색|
|Aggregation|느림|대규모 데이터 셋에 대한 집계 통계를 작성하기 때문에 빠르게 분석함|빠름|
|Use-cases|low-level 작업이 필요하거나 추상화가 필요한 경우에 사용|high-level 작업이 필요하거나 정형/반정형 등 많은 케이스|**DataFrame**과 동일|
