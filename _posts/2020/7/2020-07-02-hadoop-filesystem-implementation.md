---
layout: post
title:  "[Hadoop] Hadoop FileSystem의 몇가지 구현체"
date:   2020-07-02 22:57:14 +0900
categories: [languages]
tags : [bigdata, hadoop]
---
Hadoop FileSystem 그 중에서도 구현체 몇가지에 대해서 공부한 것을 간단하게만 정리한다.

<!--more-->

HDFS는 Hadoop의 FileSystem의 추상화 개념을 구현한 것 중 하나이다.

HDFS가 Hadoop FileSystem의 구현체 중 하나라면 다른 구현체들도 있지 않겠는가?

다른 구현체들은 다음과 같다

|FileSystem|URI Scheme|Implementation|Comments|
|---|---|---|
|Local|`file://`|[`fs.LocalFileSystem`](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/fs/LocalFileSystem.java)|Local용 파일 시스템. client의 checksum을 이용함(이용하지 않는 건 RawLocalFileSystem)|
|HDFS|`hdfs://`|[`hdfs.DistributedFileSystem`](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/DistributedFileSystem.java)|HDFS|
|WebHDFS|`webhdfs://`|[`hdfs.web.WebHdfsFileSystem`](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/web/WebHdfsFileSystem.java)|HTTP를 통한 HDFS|
|Secure WebHDFS|`swebhdfs://`|[`hdfs.web.SWebHdfsFileSystem`](https://github.com/apache/hadoop/blob/trunk/hadoop-hdfs-project/hadoop-hdfs-client/src/main/java/org/apache/hadoop/hdfs/web/SWebHdfsFileSystem.java)|HTTPS 버전의 WebHDFS|
|HAR|`har://`|[`fs.HarFileSystem`](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/fs/HarFileSystem.java)|Archive 파일을 위한 FileSystem. 주로 NameNode의 메모리 용량을 줄이기 위해서 많은 파일을 묶는데 사용됨|
|View|`viewfs://`|[`viewfs.ViewFileSystem`](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/fs/viewfs/ViewFileSystem.java)|다른 Hadoop FileSystem을 위한 Client mount table|
|FTP|`ftp://`|[`fs.ftp.FTPFileSystem`](https://github.com/apache/hadoop/blob/trunk/hadoop-common-project/hadoop-common/src/main/java/org/apache/hadoop/fs/ftp/FTPFileSystem.java)|FTP|
|S3|`s3a://`|[`fs.s3a.S3AFileSystem`](https://github.com/apache/hadoop/blob/trunk/hadoop-tools/hadoop-aws/src/main/java/org/apache/hadoop/fs/s3a/S3AFileSystem.java)|AWS S3를 지원. 구버전 s3n 구현체를 대체|
|Azure|`wasb`|[`fs.azure.NativeAzureFileSystem`](https://github.com/apache/hadoop/blob/trunk/hadoop-tools/hadoop-azure/src/main/java/org/apache/hadoop/fs/azure/NativeAzureFileSystem.java)|Azure를 지원|
|Swfit|`swift`|[`fs.swift.snative.SwiftNativeFileSystem`](https://github.com/apache/hadoop/blob/trunk/hadoop-tools/hadoop-openstack/src/main/java/org/apache/hadoop/fs/swift/snative/SwiftNativeFileSystem.java)|[OpenStack Swift](https://wiki.openstack.org/wiki/Swift)를 지원|

