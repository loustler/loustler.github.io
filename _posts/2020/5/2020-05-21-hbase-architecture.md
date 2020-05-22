---
layout: post
title:  "[Apache HBase] HBase의 아키텍처"
date:   2020-05-21 23:33:44 +0900
categories: [data_eng]
tags : [data, bigdata, hbase]
---

HBase가 무엇인지 알아봤으니, HBase의 아키텍처에 대해서 알아보겠다.

<!--more-->

HBase는 [이전 포스트](/data_eng/what-is-hbase)에서 알아봤듯이 분산 환경에서 동작하는 Columnar(Column Oriented) DB다.

이렇게 분산 환경에서 동작을 하는 HBase의 아키텍처에 대해서 알아보겠다.

# Architecture
HBase는 지연시간이 짧은 랜덤 읽기/쓰기를 지원한다.

또한 테이블들이 처리하기에 너무 커지게 되면 시스템에 의해서 자동으로 분산된다(자동 샤딩)

## Region, Region Server

테이블을 **Region**으로 나누어서 저장하기 때문이며, 이런 기능들은 **Region Server**를 통해서 제공된다.

**Region**은 테이블을 위한 분산 및 가용성의 기본 요소 단위로, **Column Family**마다 **Store**로 이루어져 있다.

다시 말해 **Region**은 연속으로 정렬된 **Row**의 집합을 일컫으며, 이 집합들은 *Start Key*와 *End Key*를 통해서 추출된다.

아래 그림을 참고하자 그림을 통해서 보면 바로 직관적으로 이해가 된다.

![Nick Dimiduk on SlideShare, Apache HBase 1.0 Release](/files/hbase_region_logical_architecture.jpg)

이런 **Region**들을 관리하는 것이 **Region Server**다.

**Store**는 Table 안에 있는 각 **Region** 안에 **Column Family**를 저장한다.

## HMaster, Zookeeper

아래 그림은 [HBase 공식문서](http://hbase.apache.org/book.html#trouble.namenode.hbase.objects)에서 캡쳐했다.

![Browsing HDFS for HBase Objects](/files/hdfs_directory_structure.png)

위 캡쳐를 통해 HBase에 어떻게 HDFS에 저장이 되는지 눈으로 더 쉽게 볼 수 있다.

이런 **Region Server**는 필요에 의해 추가되거나 제거될 수 있다.

나머지 아키텍처를 그림과 함께 설명을 하겠다.

![HBase Architecture Diagram by Guru99](/files/hbase_architecture_overview.png)

그림에서 볼 수 있듯이 HBase는 Master-Slave 구조다.

무엇이 Master로 보이고 Slave일까?

이름에서부터 알 수 있겠지만 **HMaster**가 Master고, 지금까지 설명해왔던 **Region Server**가 Slave다.

### HMaster

**HMaster**가 그럼 무엇을할까?

**HMaster**는

1. Hadoop Cluster 모니터링 및 관리
1. DDL 명령어 실행
1. **Region**을 **Region Server**에 할당 및 **Region Server** 관리
1. Failover, LoadBalancing 등
1. Client가 변경하기 원하는 어떤 스키마나 Metadata 작업을 하려고 할 때, 이런 것들을 책임지고 담당

> **HMaster**는 HDFS의 NameNode에서 실행됨

### Region Server
Slave인 **Region Server**는 앞에서도 말한 것도 있지만 다시 정리해서 말하자면,

1. **Region** 관리 및 호스팅
1. **Region** 자동 분할
1. 읽기/쓰기 요청 처리
1. Client와 직접 커뮤니케이션
1. WAL(Write Ahead Log). 진짜 간단하게 말하면 변경 내용에 대한 로그 레코드를 남기는 것을 말한다. 궁금하다면 [Naver D2](https://d2.naver.com/helloworld/407507) 참고
1. Block Cache - Read cache로 자주 읽은 데이터를 저장함. LRU(Least Recently Used) 기법을 사용
1. MemStore - Write Cache로 디스크에 아직 쓰지 않은 데이터를 가지고 있으며 **Region**에 있는 모든 **Column Family**는 **MemStore**가 있다. 일종의 버퍼라고 생각해도 될 거 같다.
1. HFile - 실제 스토리지 파일으로 **Row**들이 정렬된 key value로 쌓여있는 파일으로 disk에 존재하는 녀석이다.

**Region Server**가 하는게 좀 많은데 **WAL**, **Block Cache**, **MemStore**, **HFile**에 대해서 아래 [edureka](https://www.edureka.co/blog/hbase-architecture/)에서 볼 수 있는 그림을 통해서 살펴보자.

![Figure: Region Server Components](https://d1jnx9ba8s6j9r.cloudfront.net/blog/wp-content/uploads/2016/11/Region-Server-Components-HBase-Architecture-Edureka-768x440.png)

> 참고로 **Region Server**는 HDFS의 DataNode에서 실행됨

### Zookeeper
**Zookeeper**는 그럼 무엇을 할까?

**Zookeeper**는 알고 있는 분들은 알고 있으시겠지만, 분산형 코디네이션 서비스다. 이 **Zookeeper**에 대해서는 차후에 포스트를 한 번 써서 정리를 한 번 해보겠다.

**Zookeeper**는

1. HBase 분산 환경에서 코디네이터처럼 사용
1. **HMaster**에 속한 모든 **Region Server**가 일정 간격으로 hearbeat를 **Zookeeper**에 보냄으로써 서버가 정상동작(살아있음)을 감지
1. 설정 정보 유지
1. **HMaster** 또한 heartbeat를 **Zookeeper**에게 보내서 서버가 정상동작을 감지하고, heatbeat를 보내지 못하면 session을 삭제하여 비활성화 되어 있던 다른 **HMaster**를 활성화시키게 해줌
1. *.META* 서버의 path도 관리하여, 어떤 클라이언트든지 **Region**을 검색할 수 있게 해줌

## Write Mechanism
HBase가 쓰기를 하는 메커니즘은 다음과 같다

1. Client가 데이터를 쓰기 전에 **Region Server**을 통하여 **Region**과 통신함
   - WAL 파일에 append됨
   - WAL 파일은 **Region Server**가 관리하고 있으며, **Region Server**는 데이터를 복구할 때 WAL 파일을 사용
1. WAL에 쓰여진 데이터는 **MemStore**에 복사됨.
1. 한 번 **MemStore**에 위치하게 되면, Client에게 데이터를 받았다고 알려주고 Client는 이를 알게 된다.
1. **MemStore**의 threshold에 이르게 되면, 가지고 있던 데이터들을 **HFile**에 dump하거나 commit한다.

## Read Mechanism
HBase로부터 데이터를 읽을 때는 다음과 같다

1. Client가 읽기 요청
1. Scanner가 먼저 Block Cache에서 Row Cell을 찾음. 최근에 읽은 key-value pair가 저장되어 있기 때문
1. Scanner가 실패하면(최근에 읽은 데이터가 아닐 때, Block Cache에 없을 때), **MemStore**로 이동한다. 혹시 아직 dump가 아직 되지 않았으면 최근에 쓴 파일들이 **MemStore**에 있기 때문에 여기에서 검색을 해봄
1. **MemCache**에서 찾지 못할 경우, 마지막으로 *Bloom Filter*와 **Block Cache**를 이용해서 **HFile**로부터 데이터를 가지고 옴

