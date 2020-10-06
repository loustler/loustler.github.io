---
layout: post
title:  "[Hadoop]HBase Compaction"
date:   2020-10-06 23:30:14 +0900
categories: [dataeng]
tags : [bigdata, data, bigdata, hbase]
---
HBase의 compaction에 대해서 알아본다.

<!--more-->

[이전 포스트](/data_eng/hbase-architecture/)에서 HBase에서 Write가 어떻게 이루어지는지 알아봤었다.

Write 하는 과정에 대해서 다시 한 번 풀어보면서 compaction에 대해서 풀어보고자 한다.

# Hbase Write

먼저 row는 rowKey를 이용하여 내림차순으로 정렬되며, Cell은 버저닝이 된다는 것을 알 것이다.

우리가 write를 하게 되면 rowKey와 함께 데이터들을 쓰게 된다.

이런 베이스를 가지고 설명해보자면,

1. Client를 통해 write를 요청
1. rowKey를 기반으로 row들이 정렬되어 있기 때문에 새로운 row가 삽입될 위치는 rowKey를 이용해서 알아냄
    1. Client가 Zookeeper에게 HBase의 meta table의 위치를 알아냄
    1. meta table을 통해서 region의 위치를 알아내면 client가 region의 정보를 캐시함
    1. 만약 이 사이클로 했다가 장애가 발생했다면 리전이 적합하지 않은 것이므로 다시 한 번 위의 과정을 다시 반복한다.
1. write 요청이 Region server에 도착
1. commit log(Write Ahead Log, WAL)에 write
1. 그 후 MemStore(Write cache)에 write 후 클라이언트에게 정상적으로 write 되었다고 응답
1. Memstore의 threshold에 이르면 내용을 HFile에 flush

여기까지가 Write가 이루어지는 과정이다.

# Compaction

MemStore의 threshold에 이르러 Disk에 위치한 HFile에 flush 되는 것을 compaction 이라고 한다.

row들이 변경/삭제가 되게 되면 HFile에 존재하는 데이터들 중 필요없어진 데이터들에 대해서 삭제 마킹이 매겨진다.

이 때, 지울 것은 지우고 합칠 것은 합치는 작업이 이루어지게 된다. 1개의 Region의 HFile을 모아서 병합하는 과정이 진행된다.

이런 과정 중에서도 compaction이 발생하게 되는데 아래와 같은 compaction이 있다.

- minor compaction: 사이즈가 작고 수가 많은 최근에 생성된 HFile을 소수의 큰 HFile로 병합
- major compaction: Region의 모든 Column Family의 모든 HFile 파일(StoreFile)을 하나로 병핣

