---
layout: post
title:  "[Apache Spark] Spark Architecture"
date:   2020-05-23 01:00:14 +0900
categories: [data_eng]
tags : [data, bigdata, spark]
---
Spark의 Architecture에 대해서 알아보자

<!--more-->

# Spark Architecture
먼저 간단한 그림부터 살펴보고 차근차근 살펴보자

![Spark Architecture Overview](/files/spark-architecture.png)

**Spark**에는 모든 스파크 구성 요소와 레이어가 느슨하게 결합된 잘 정의된 계층 구조가 있다.

이런 계층 구조 아키텍처로 인해 다양한 확장이 가능하고 라이브러리와 통합이 더 잘된다.

**Spark**에는 2개의 핵심 추상화가 있다.

1. RDD(Resilient Distributed DataSet, 탄력적 분산 데이터셋)
1. DAG(Directed Acyclic Graph, 방향성 비순환 그래프)


## RDD(Resilient Distributed DataSet)
**RDD**는 주된 스파크 프로그래밍 추상화의 구조이며, 여러 노드에 흩어져 있으면서 병렬처리 될 수 있는 아이템들의 모음을 말한다.

또한 모든 스파크 어플리케이션의 빌딩 블록이다.

**RDD**의 각 의미를 알아보면

- **Resilient**: 내결함성 및 장애시 데이터를 재구성할 수 있다.
- **Distributed**: 클러스터의 여러 노드에 분산된 데이터
- **DataSet**: 값과 함께 파티션된 데이터 모음

또한 **RDD**는 기본적으로 lazy transformation을 따르며 본질적으로 불변이다.

**RDD**의 데이터는 키를 기준으로 chunk로 분할된다.

**RDD**는 동일한 데이터 chunk가 여러개의 노드에 복제되므로 모든 문제에서 빠르게 복구할 수 있기 때문에 복원력이 뛰어나다.

따라서 만약 1개의 노드가 실패하더라도 다른 노드에서 데이터 처리를 진행한다.

이로 인해 빠르게 데이터 셋에 대한 계산을 처리할 수 있다.

분사환경에서 **RDD**에 있는 각 데이터셋은 논리적 파티션으로 분할되며, 클러스터의 다른 노드에서 계산될 수도 있다.

이로 인해서 데이터의 변환(transformation)이나 실행(action)을 병렬적으로 실행할 수 있다.

이것들에 대해선 **Spark**가 책임지고 관리하므로 신경쓸 필요는 없다.

**RDD**가 어떻게 처리되는지 아래 그림을 통해 알아보자.

![RDD Workflow](/files/rdd-workflow.png)

참고로 **RDD**를 생성하는 방법으로는

1. 외부 데이터셋 로드(HDFS, HBase 등)
1. 드라이버 프로그램에서 객체 컬렉션을 분산

2가지의 방법을 할 수 있다.

위에서 **Transformation**과 **Action**에 대해서 이야기 했었다.

- **Transformation**: 이미 존재하는 **RDD**에서 새로운 **RDD**를 만들어내는 연산
- **Action**: **RDD**를 기초로 결과 값을 계산하여 그 값을 **Driver Program**에 되돌려주거나 외부 스토리지(HDFS 등)에 저장하는 연산

**Spark**는 **RDD**가 처음에 **Action**을 할 때 처리를 시작하기 때문에 *lazy evalution* 방식이다.

## Architecture
실제 **Spark**가 동작하는 걸 그림과 함께 살펴보자

![Spark Architecture](/files/working-spark-architecture.png)

실제로 **Spark**가 어떻게 동작하는지 알아보자.

### Driver Program, Spark Context

우선 *Master Node*에는 **Spark Application**을 실행시킬 **Drvier Program**이 있다.

**Driver Program**가 가장 먼저 하는 것은 **Spark Context**를 만드는 것이다.

**Spark Context**는 모든 스파크 기능의 게이트웨이라고 할 수 있다. 약간 DB Connection과 같다.

내가 DB로 보내는 모든 커맨드들은 어떤 것이든 DB Connection을 지나지 않은가?

**Spark Context**는 그것과 같다고 할 수 있다.

즉, **Spark**로 수행하는 모든 것은 **Spark Context**를 거치게 되어 있다.

**Spark Context**는 **Cluster Manager**와 함께 동작하며 여러가지의 *job*을 관리한다.

**Spark Context**와 **Driver Program**은 클러스터 내에서 *job*의 실행 처리한다.

이렇게 실행된 *job*은 여러개의 *task*로 나뉘어져서 **Worker Node**에 분산된다.

**RDD**가 **Spark Context**에서 생성이 되면 언제든지 여러 노드에 거쳐 분산되고 해당 노드에서 캐싱될 수 있다.

### Worker Node
**Worker Node**는 우리가 흔히 알고 있는 혹은 알다시피 *Slave Node*이며 *job*은 기본적으로 여러개의 *task*를 실행한다.

즉 1개의 *job*이 여러개의 *task*를 만들어내는 것이다.

이 *task*들이 **Worker Node**에 분산되어 있는 **RDD**에서 실행이 되면, 그 결과를 다시 **Spark Context**로 돌려준다.

**Spark Context**는 작업의 수행, 중단, **Worker Node**에 분배를 한다.

*task*들은 분산된 **RDD**에서 동작하고, 작업을 수행하며 결과를 모아서 main **Spark Context**로 돌아간다.

만약 **Worker Node**의 숫자를 늘리게 되면 *job*을 더 많은 파티션으로 나누고 실행해서 여러 시스템에서 병렬로 실행할 수 있다.

즉, **Worker Node**를 늘리게 되면 더 빠르게 *job*을 처리한다는 것이다. 또한, 메모리도 자연히 증가하기 때문에 캐시를 이용하여 더 빠르게 작업할 수 있게 된다.

### workflow
실제 스파크 코드가 실행이 어떻게 되는지 알아보자

1. Client가 Spark Application Code를 submit을 한다.
1. **Driver**가 유저가 작성한 **Transformation**과 **Action**이 포함된 코드를 논리적으로 DAG로 변환. 이 때 파이프라이닝 변환과 같은 최적화도 같이 수행한다.
1. DAG를 실제 수행 계획으로 변경함. 변경할 때는 많은 *stage*가 생기게 된다.
1. 실제 수행 계획을 *task*라 불리는 실제 수행 단위를 만들고 *task*들을 번들하여 **Cluster**에 보낸다.
1. **Driver**가 **Cluster Manager**와 이야기하면서 리소스에 대해서 협상함(리소스 요청). **Cluster Manager**가 **Driver**를 대신해서 **Worker Node**에 *executor*를 런치한다.
1. 이 때, **Driver**가 *task*들을 데이터 배치에 기반해서 *executor*들에게 보내준다.
1. *executor*가 시작되면 **Driver**에 등록이 되고 이로 인해 **Driver**가 실행되고 있는 *task*를 볼 수 있게 된다.
1. *task*를 실행하는 동안, **Driver Program**이 실행되고 있는 *executor* 세트를 모니터링을 한다
1. **Driver Node**는 데이터 배치를 기반으로 향후 작업을 스케줄한다

