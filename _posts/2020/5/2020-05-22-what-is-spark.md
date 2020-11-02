---
layout: post
title:  "[Apache Spark] Spark?"
date:   2020-05-22 22:57:44 +0900
categories: [data_eng]
tags : [data, bigdata, spark]
---
빅데이터를 처리할 때 많이 사용한다는 Spark

Spark는 어떤 녀석인지 알아보겠다.

<!--more-->

이전의 많은 포스트들에서 **Spark**에 대해서 이야기를 했었다.

그렇게 많이 언급한 **Spark**는 과연 어떤 녀석일까?

# What is Spark?
**Spark**는 범용적이면서 빠른 속도로 작업을 수행할 수 있도록 클러스터용 연산 플랫폼이다.

디스크 기반으로 연산을 하는 **Hadoop**과 달리 **Spark**는 인메모리 연산을 한다.

그렇기 때문에 **Spark** 공식 홈페이지를 가보면 떡하니 **Hadoop**과 비교하여 **Spark**가 얼마나 빠른지 그래프로 보여주고 있다.

**Spark**가 범용적이란 것은 기존에 분산 시스템에서 돌아가던 Batch Application을 비롯하여 많은 것을 대체할 수 있기 때문이다.

# Spark의 기능
**Spark**는 다양한 기능들을 제공해주는데 다음과 같다

1. 속도
  - **Spark**는 앞에서도 말했다시피 인메로리 기반의 연산을 진행하기 때문에 **Hadoop**에 비해서 100배나 빠른 속도를 자랑한다.
1. 강력한 캐싱
  - 프로그래밍하기 쉽도록 강력한 메모리 캐싱기능과 디스크기반 캐싱 능력도 제공한다.
1. 배포
  - [Mesos](http://mesos.apache.org/)나 [Hadoop's Yarn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) 그리고 **Spark**만의 클러스터 매니저를 통해서 배포할 수 있다.
1. 실시간
  - 인메모리 기반 연산으로 인해 실시간 연산을 제공하며 낮은 응답시간을 자랑한다.
1. 폴리글랏
  - 고수준 API 제공으로 Java, Scala, Python을 비롯하여 R도 지원한다. 여기에서 어떤 언어를 사용하더라도 쉽게 개발을 할 수 있게 된다.
1. 확장성

# Spark Eco-System
Spark도 **Hadoop**처럼 Eco-System을 가지고 있다.

1. **Spark Core**
   - **Spark**의 핵심으로 기본적인 병렬, 분산 데이터 프로세싱을 위한 엔진이 있다.
   - 다른 추가적인 라이브러리들은 이 **Spark Core** 위에서 동작한다.
   - 메모리 관리, 장애 복구, 작업 스케줄링, 클러스터에서 job 분배 및 모니터링, 저장장치와의 연동을 책임짐
   - RDD(Resilient Distributed DataSet, 탄력적 분산 데이터셋)를 정의하는 API의 기반
1. **Spark Streaming**
   - **Spark**에서 실시간 스트리밍 데이터 처리를 하기 위한 컴포넌트
   - **Spark Core** 수준의 높은 처리량과 라이브 데이터 스트림에서의 스티림 프로세싱의 장애 허용(fault-tolerant), 확장성을 지원하도록 설계됨
   - **RDD**를 확장하였음
1. **Spark SQL**
   - 정형 데이터를 처리하기 위한 패키지
   - Parquet, JSON 등 다양한 데이터 소스를 지원
   - Hive Query Language(HQL)을 통하거나 SQL을 통해 데이터에 쿼리를 할 수 있게 해줌
   - RDBMS에 익숙한 사용자에게 기존 관계형 데이터 프로세싱의 경계를 확장할 수 있는 이전도구
   - 단순한 SQL 인터페이스를 제공하는 것 이상으로 SQL과 복잡한 분석 작업을 서로 연결할 수 있도록 지원해줌
   - **RDD**를 확장하였음
1. **Spark MLlib**
   - 일반적인 머신러닝 기능을 가지고 있는 라이브러리
   - 분류, 회귀 ,클러스터링, 협업 필터링 등 다양한 타입의 머신 러닝 알고리즘 지원
   - **Spark MLlib** 모든 기능들은 클러스터 전체를 사용하여 실행되도록 설계됨
1. **Spark GraphX**
   - 그래프를 다루기 위한 라이브러리로 그래프 병렬 연산 수행
   - **RDD**를 확장하였음
   - 그래프 알고리즘들의 라이브러리 지원
1. **Spark R**
   - 분산형 데이터 프레임 구현체를 제공하는 R 패키지

# Uses Cases
**Spark**를 이용하여 할 수 있는 건 무엇이 있을까?

1. 데이터 처리 어플리케이션
1. 데이터 과학 작업

