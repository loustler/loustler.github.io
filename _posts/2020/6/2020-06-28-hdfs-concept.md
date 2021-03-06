---
layout: post
title:  "[Hadoop] HDFS의 컨셉"
date:   2020-06-29 00:57:14 +0900
categories: [languages]
tags : [bigdata, hadoop]
---
HDFS의 컨셉과 설계에 대해서 공부한 것을 기록한다.

<!--more-->


# 설계
HDFS는 범용 하드웨어로 구성된 클러스터에서 실행되고 대용량 파일을 다룰 수 있도록 설계된 파일시스템(File System)이다.

- 매우 큰 파일
  - TB 이상의 데이터를 말함
- 스트리밍 방식의 데이터 접근
  - 가장 효율적인 데이터 처리 패턴은 한 번 쓰고 여러번 읽는 것 이라는 아이디어에 출발
  - 데이터셋은 생성되거나 원본에서 복사되며 시간이 흐르면서 다양한 분석을 수행할 수 있음
- 범용 하드웨어
  - 노드 장애가 발생할 확률이 높은 범용 하드웨어로 구성된 대형 클러스터에서 문제없이 실행되도록 설계됨

## 사용하기에 좋지 않은 것

- 빠른 데이터 응답 시간
  - 데이터를 접근할 때 수십 ms 수준의 빠른 응답을 원하는 Application은 부적합
  - 높은 데이터 처리량을 위해 최적화되어 있고 이를 위한 트레이드 오프(trade-off)로 응답시간을 희생함
- 수많은 작은 파일
  - NameNode는 파일 시스템의 메타데이터를 **메모리**에서 관리하기 때문에 저장할 수 있는 파일 수는 NameNode의 메모리 용량에 좌우됨
  - 파일(File), 디렉터리(Directory), 블록(Block)은 각각 150 Byte 정도의 메모리가 필요하다고 함
- 다중 Writer와 파일의 임의수정
  - HDFS는 단일 Writer로 파일을 씀
  - 파일에 append를 하는 것은 가능하지만 파일의 임의의 위치에 내용을 수정하는 것은 허용하지 않음
  - 3.0부터 다중 Writer 지원

# 개념
## Block
물리적인 디스크의 블록 크기라는 개념이 있는데, 블록 크기는 한 번에 읽고 쓸 수 있는 데이터의 최대량을 말한다.

파일시스템 블록의 크기는 수 KB이고, 디스크 블록의 크기는 512 Byte다

HDFS 또한 이와 같은 블록의 개념을 가지고 있다.

그러나 블록의 크기는 디스크나 파일 시스템의 크기와는 다른 128MB라는 굉장히 큰 단위다(Hadoop 1.x 는 64BMB, Hadoop 2.x 부터 128MB)

Linux 의 블록 크기가 4KB인걸 생각하면 엄청나게 큰 크기다.

또한 디스크를 위한 파일시스템은 디스크 블록보다 작은 크기의 데이터라도 블록 1개를 전체를 점유하지만,

HDFS 파일은 데이터가 블록 크기보다 작더라도 전체를 점유하지 않고 일부만 점유한다.

### Block Size가 큰 이유
128MB인 이유는 128MB는 131072 KB이고 이건 134217728 Byte이기 때문이다

이건 결국 Binary로는 1000000000000000000000000000 으로 표현되기 때문

이렇게 한 이유는 데이터를 메모리에 쌓을 때 비트를 낭비하지 않기 때문이라고 한다.

또한 탐색 비용을 최소화하기 위해서며, 블록이 매우 크면 블록의 시작점을 찾는데에 빠르게 찾을 수 있고 줄이게 된 그 시간을 실제로 데이터를 전송하는데에 할애할 수 있다.

그래서 여러 개의 블록으로 구성된 대용량 파일을 전송하는 시간은 디스크 전송 속도에 크게 영향을 받게 된다.


HDFS와 같은 분산 시스템에서 블록 추상화의 개념을 도입하면서 얻게된 이득이 있는데

1. 파일 하나의 크기가 단일 디스크의 용량보다 더 커질 수 있다
  - 여러 개의 블록이 동일한 디스크에만 저장될 필요가 없으므로 클러스터에 있는 어떤 디스크에도 저장될 수 있음
1. 스토리지의 서브시스템을 단순하게 만들 수 있음
  - 분산 시스템의 장애유형이 너무 다양해 이런 단순화가 더 중요함
  - 블록을 다루는 스토리지의 서브시스템의 경우 스토리지 관리를 단순화하기 쉽고 메타데이터에 대해 고민을 덜 한다

또한 Fault Tolerance와 Availability 를 제공하는데 필요한 복제를 구현하는데에 매우 적합한데,

HDFS는 블록의 손상과 디스크 및 머신의 장애에 대처하기 위해 각 블록을 3개의 머신에 복제한다(기본값 replication: 3)

만약 1개의 블록을 이용할 수 없는 상황이라면 다른 머신에 있는 복사본을 읽게끔 한다.

또한 읽기 부하가 심하다면 이런 replication의 숫자를 3에서 더 올려서 부하분산을 시킬 수 있다.

다만 이런경우라면 용량이 더 커지는 것을 감안해야 된다. 일종의 trade-off다.

## NameNode, DataNode
HDFS Cluster는 Master-Worker 패턴으로 2개의 종류로 이루어져 있다

NameNode가 Master, DataNode가 Worker로 1개의 Master와 N개의 Worker로 이루어져 있다.

### NameNode
파일시스템의 네임스페이스(namespace)를 관리한다.

파일시스템 트리와 그 트리에 포함된 모든 파일과 디렉터리(Directory)에 대한 메타데이터(metadata)를 유지한다.

이 정보는 namespace image와 edit log라는 2종류의 파일로 로컬 디스크에 저장된다.

또한 파일에 속한 모든 블록이 어느 DataNode에 있는지 파악하고 있으며 이런 위치정보는 시스템이 시작할 때 모든 DataNode로부터 받아서 재구성하기 때문에 영속성을 가지지는 않는다.

### Client
사용자를 대신해 NameNode와 DataNode 사이에서 통신하고 파일시스템에 접근한다.

### DataNode
파일시스템의 실질적인 Worker로 Client나 NameNode의 요청이 있을 때 블록을 저장/탐색하며 저장하고 있는 블록의 목록을 주기적으로 NameNode로 보낸다.

### 장애복구
NameNode가 없으면 파일시스템이 동작할 수 없다.

따라서 NameNode에 대한 장애복구는 매우 필수적인데 다음과 같은 방식으로 장애복구 기능을 제공한다.

1. 메타데이터를 지속적으로 보존하기 위해 파일로 백업
  - NameNode가 다수의 파일시스템에 영구적인 상태를 저장하도록 하는 것
  - 백업 작업 자체를 Atomic하게 실행
  - 로컬 디스크와 NFS 마운트를 이용해 2곳 모두 백업
1. 보조 네임노드(Secondary NameNode) 운영
  - 보조 네임노드의 주역할은 edit log가 너무 커지지 않도록 주기적으로 namespace 이미지를 edit log와 병합하여 새로운 namespace 이미지를 만드는 것
  - 작업을 하기 위해서는 많은 자원이 필요하므로 주 NameNode와는 별도의 machine에서 실행해야 됨
  - 주 NameNode의 장애를 대비해 namespace 이미지의 복제본을 보관함
    - 여기에는 약간의 시간차를 두고 실행되기 때문에 어느정도이 데이터 손실이 불가피

## 블록 캐싱(Block Caching)
DataNode는 디스크에 저장된 블록을 읽는데 읽는 빈도가 높은 블록 파일은 off-heap block cache라는 DataNode의 메모리에 명시적으로 캐싱할 수 있음

기본적으로 블록은 1개의 DataNode 메모리에만 캐싱되지만 파일단위로도 설정이 가능하다

Hadoop MR이나 Spark가 블록이 캐싱된 DataNode에서 task가 실행되도록 할 수도 있으며, 이를 통해 읽기성능을 높일 수 있다.

특히 Join을 하는 경우에 작은 lookup table을 캐싱하는 것은 좋은 활용사례

Cache pool은 cache 권한이나 자원의 용도를 관리하는 관리 그룹의 역할로 cache directive를 추가하여 특정 파일을 캐시하도록 할 수 있다.

## HDFS Federation
NameNode는 위에서 말했듯이 메모리에서 모든 파일과 각 블록에 대한 참조 정보를 메모리에서 관리한다

그렇기 때문에 메모리가 HDFS의 확장성에 가장 큰 걸림돌이 된다.

왜냐하면 결국 HDFS를 확장한다는 것은 현재로서는 소화하기 힘들정도로 데이터가 포화상태에 이르렀다는 것이고(혹은 곧 포화상태가 되거나),

HDFS를 확장하게 되면 NameNode에 더 많은 메모리가 필요하게 될 거라는 것이다.

이런 NameNode 메모리 확장성 문제를 해결하기 위해서 Hadoop 2부터 HDFS Federation을 지원해준다

HDFS Federation을 활용하면 NameNode가 FileSystem의 namespace 일부를 나누어서 관리를 하는 방식으로 새로운 NameNode를 추가할 수 있게 된다.

각 NameNode가 특정 directory를 관리하는 방식이라고 생각하면 되겠다.

이걸 적용하게 되면 각 NameNode는 

1. namespace의 metadata를 구성하는 **NameSpace Volume** 을 관리
  - 서로 독립적
1. namespace에 포함된 파일 전체 블록을 보관하는 **Block Pool** 을 관리
1. 서로 통신할 필요가 없음
1. 특정 NameNode의 장애가 전파되지 않음(다른 NameNode가 관리하는 NameSpace에세 영향을 주지 X)

> Block Pool의 저장소는 분리되어 있지 않음

또 모든 DataNode는 Cluster의 각 NameNode마다 등록되어 있고, 여러 Block Pool로부터 블록을 저장한다.

HDSF Federation Cluster에 접근하려면 Client는 파일 경로와 NameNode를 mapping한 Client쪽 mount table을 이용함

## HDFS 고가용성
NameNode의 장애가 발생했을 때, 데이터 손실을 최소화하여 장애복구를 하기 위해서 아래와 같은 방안을 제시를 했었다.

1. 데이터를 지속적으로 파일백업(NFS와 로컬)
1. Secondary NameNode를 사용하여 checkpoint(스냅샷)를 생성하는 방식

이 두 방법을 모두 사용하더라도 실상 고가용성을 100% 보장하기는 어렵다.

NameNode 자체가 여전히 단일 장애포인트가 되기 때문이다(SPOF, Single Point Of Failure)

NameNode에 장애가 발생하게 되면 사실상 모든 Hadoop System이 먹통이 되어버리게 된다.

NameNode를 새로 실행시키면 이 문제는 해결이 되지만, 만약 규모가 큰 대형 클러스터의 경우 새로운 NameNode를 실행시키는데 어마어마한 시간이 소요된다.

그 시간동안 NameNode가 동작하지 못하게 되면서 아무것도 처리할 수 없게 된다.

NameNode는 다음과 같이 시작이 되는데

1. namespace image를 메모리에 로드
1. edit log 갱신
1. 모든 DataNode로부터 Block Report를 받아서 안정상태에 안착

이 과정이 끝나서 안전모드가 끝날 때 까지는 어떠한 작업도 할 수 없다.

장애가 발생할 확률이 높지는 않지만 발생한다면 매우 치명적이기 때문에 이런 문제를 해결하기 위해 Hadoop 2부터 HA(High Availability)를 지원한다.

HA는 NameNode를 1쌍으로 구성해서 active-standby 상태로 만든다.

Active NameNode에 장애가 발생하면 standby NameNode가 역할을 이어받아서 무중단으로 클라이언트의 요청을 처리한다.

이런 방식을 지원하기 위해서 HDFS가 변경이 되었는데

1. NameNode는 edit log를 공유하기 위해 HA 공유 스토리지를 사용해야 함
   - 동작
       1. Standby NameNode가 활성화 되면 먼저 Active NameNode의 상태를 동기화하기 위해 공유된 edit log를 읽음
       1. 이후 Active NameNode에 새로 추가되는 항목들도 읽음
   - 이를 위한 옵션으로 2가지가 있음
       1. QJM(Quorum Journal Manager)
       1. NFS Filter
1. DataNode는 Block Report를 2개의 NameNode에 보내야 함(Active Node, Standby Node)
   - Block Mapping 정보는 Disk가 아니라 NameNode 메모리에 보관되기 때문
1. Client는 NameNode 장애를 사용자에게 투명한 방식으로 처리할 수 있게 구성해야 함
1. Standby NameNode는 Secondary NameNode의 역할을 포함하고 있으며, Active NameNode namespace checkpoint 작업을 주기적으로 수행

### HA Shared Storage
HA 공유 스토리지를 위한 방법으로 2가지를 위에서 언급했었다.

언급했던 방법에 대해서 간략히 추가를 하자면,

1. QJM(Quorum Journal Manager)
   - HDFS 전용 구현체로 HA Edit Log 지원 목적으로 설계 됨
   - Journal Node Group에서 동작하며 edit log는 전체 journal node에 동시에 쓰여짐
   - Journal Node는 일반적으로 3개
   - Zookeeper의 작동 방식과 유사
   - HDFS 권장 옵션
1. NFS Filter
