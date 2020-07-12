---
layout: post
title:  "[Hadoop] Hadoop HDFS의 File Read/Write"
date:   2020-07-09 00:58:14 +0900
categories: [languages]
tags : [bigdata, hadoop]
---
Client를 통해 HDFS에 File을 read/write할 때 어떻게 HDFS 내부에서 동작하는지 공부하였다.

<!--more-->

# File Read
![HDFS client read file](/files/hdfs-client-read.png)

## Overview
1. Client가 NameNode에게 읽고자 하는 파일의 블록들의 위치를 알려달라고 요청
1. Client가 요청한 파일의 블록들을 가진 데이터노드들(Client로부터 가까운 순으로 정렬됨)로부터 데이터를 읽음.
   - 물론 Client가 동작하고 있는 노드에 데이터 블록이 있다면 거기를 먼저 읽음
1. 첫번째 블록부터 마지막 블록까지 데이터를 쭉 스트림을 통해 읽음
1. 만약 읽는 도중 해당 데이터 노드에 장애가 발생하면 해당 블록을 가지고 있는 그 다음 가까운 노드로부터 데이터를 읽음

## Detail
1. 열고자 하는 파일을 HDFS Client의 [DistributedFileSystem](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DistributedFileSystem.java) `open`을 통해 열기
1. [DistributedFileSystem](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DistributedFileSystem.java)에서 RPC를 이용하여 NameNode를 호출해서 블록위치 파악
1. [FSDataInputStream]()을 통해 스트림 형식으로 읽기 위해 `read`를 호출
1. [DFSInputStream](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DFSInputStream.java)이 파일의 첫번째 블록을 저장하고 있는 DataNode와 연결하여 데이터를 읽는다.
1. 다음 블록을 가진 데이터 노드를 찾고 데이터를 읽는다.
1. 모든 블록을 읽으면 [FSDataInputStream]()의 연결을 끊는다.(`close` method)

Client가 블록을 순서대로 1개씩 스트림으로 읽다보니 연속된 스트림을 읽는것처럼 느껴진다.

Client가 NameNode가 아닌 DataNode를 통해서 요청을 처리하다보니 NameNode에서는 병목이 거의 발생하지 않는다.

# File Write

## Overview
1. namespace(경로)에 파일을 생성하기 위해서 NameNode에게 파일을 생성해달라고 요청보냄
1. NameNode로부터 에러가 발생한 게 없으면 Client가 데이터를 write
1. replication 숫자만큼 데이터가 DataNode에 쓰여짐

## Detail
1. [DistributedFileSystem](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DistributedFileSystem.java)의 `create`를 통해 파일을 생성하고 NameNode에 RPC로 요청을 보냄
1. NameNode는 파일이 이미 있는지, Client가 생성권한은 있는지 등 체크를 하고 나면 파일 레코드를 생성하고 아니라면 에러를 일으킨다(`IOException`)
1. Client는 [FSDataOutputStream]()을 통해 데이터를 write한다
1. [FSDataOutputStream]()는 데이터를 패킷으로 분리하고 내부 Queue인 Data Queue로 패킷을 보냄
1. [DataStreamer]()가 Data Queue에 담긴 패킷을 처리하게 된다
1. NameNode에 replication을 저장할 DataNode 목록을 요청
1. 할당받은 DataNode들은 파이프라인을 구성하고 [DataStreamer]()가 패킷을 write
   1. 파이프라인의 첫번째 Data Node에 데이터가 쓰여지면
   1. 첫번째 DataNode가 각 패킷을 저장하고 두번째 DataNode에 패킷을 보낸다.
   1. 두번째 DataNode도 동일하게 각 패킷을 저장하고 세번째 DataNode에 패킷을 보낸다.
   1. 세번째 DataNode는 각 패킷을 저장한다.
1. [DFSOutputStream]()이 각 DataNode로부터 패킷이 모두 쓰여졌는지 ack 응답을 처리하기 위해 Ack Queue를 사용하여 내부 패킷들을 유지한다

장애가 발생한다면 Ack Queue에 있던 모든 패킷이 다시 Data Queue로 돌아간다.

장애가 발생했던 Data Node는 복구가 되면 불완전한 블록을 삭제한다.

## 복제본 배치
파일을 write 할 때 replication 설정만큼 작성한다고 했는데 이 replication이 배치되는 기준은 무엇일까?

우선 Client가 Node에서 실행되지 않고 있다면 Client와 같은 Node에 있는 DataNode에 배치를 한다.

만약 Client가 DataNode에서 실행되지 않는다면 랜덤하게 배치를 한다.

이렇게 첫번째 replication이 배치되면, 두번째 replication은 이어서 첫번째 노드와 다른 무작위 Rack의 DataNode에 배치하고

세번째 replication은 두번째 replication과 같은 Rack에 위치한 다른 DataNode에 배치한다.

그 이상 replication은 random으로 배치한다.

이렇게 2개의 Rack에 block을 저장함으로써 신뢰성을 확보하며, Rack은 Network Switch(Rack Switch)를 기준으로 분류한다.