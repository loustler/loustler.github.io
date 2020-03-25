---
layout: post
title:  "[OLAP]What is OLAP, OALP란?"
date:   2020-03-25 22:50:30 +0900
categories: [data_eng]
tags : [data]
---

OLAP(Online Analytical Processing)는 무엇일까?

<!--more-->

OLAP(Online Analytical Processing)는 동시간대에 여러개의 DB System으로부터 정보를 분석할 수 있게 해주는 소프트웨어의 종류 중 하나이다.

OLAP는 서로 다른 관점으로부터 비즈니스 데이터를 추출하고 볼 수 있도록 하는 기술이다.

이런 분석들은 꽤 자주 데이터를 조인(join)하거나 그룹화를 하거나(group) 결합(aggreate)를 해야 한다.

이런 일련의 작업들은 RDB에서 많은 리소르를 요구한다.

OLAP 데이터를 사용하게 되면 미리 계산되고(pre-calculated) 미리 결합된(pre-aggreagated) 데이터를 사용함으로써 빠른 분석을 할 수 있다.

이런 OLAP를 지원하는 OLAP DB는 1개 이상의 cube로 나뉘어져 있다. 이 cube들은 리포트를 쉽게 만들고 볼 수 있도록 설계되어있다.

먼저 OLAP Cube를 살펴보자.

![OLAP Cube from grapecity](https://gccontent.blob.core.windows.net/gccontent/blogs/legacy/c1/2014/11/OLAP_cube-300x257.png)

위 그림을 보면서 설명을 하겠다.

먼저 OLAP Cube는 데이터 구조(Data Structure)로 매우 빠른 데이터 분석을 위해 최적화된 녀석이다.

OLAP Cube는 차원(demension)들로 분리되는 계수(measure)라 불리는 수차화된 사실들로 이루어져 있다.

이런 OLAP Cube는 하이퍼큐브(HyperCube)라고도 한다.

보통 Data operation들과 분석은 행(row)과 열(column) 형식으로 배열되는 간단한 스프레드 시트를 사용하여 실행한다.

이런 스프레드 시트와 같은 것들은 행과 열로 이루어져 있어 2개의 차원(demension)으로 이루어진 데이터들이다.

그러나 OLAP는 다차원(multidimension) 데이터를 포함하며, 이 데이터들은 보통 다르고 서로 관계없는 곳으로부터 얻게 된다.

그래서 사실 스프레드시트를 사용하는 건 최선의 옵션이 아니다.

Cube는 다차원 데이터를 논리적이고 질서있는 방식으로 저장하고 분석할 수 있다.


그럼 이 큐브들은 어떻게 동작할까?

DW(Data Warehouse)는 여러개의 데이터 소스와 텍스트 파일, 엑셀 시트, 멀티미디어 파일과 같은 형식에서 정보를 추출한다.

이렇게 추출한 데이터들은 정리가 되었으며 변환된 것이다.

이런 데이터는 추가분석을 위해 선계산된(pre-calculated) 정보로 OLAP 서버 혹은 OLAP cube에 로드가 된다.

OLAP는 기본적으로 분석을 위한 operation이 있다.

다음 포스트에서는 이 operation들에 대해서 작성하도록 하겠다.

그럼 이 포스트를 끝내기 전에 요약을 하겠다.

OLAP란?

> 서로 다른 관점으로부터 비즈니스 데이터를 추출하고 볼 수 있도록 하는 기술로, 데이터들을 선계산, 선결합 형태로 만들어 빠르게 분석할 수 있게 해준다.

OLAP Cube란?

> 매우 빠른 데이터 분석을 위해 최적화된 데이터 구조로, 다차원 데이터를 논리적이고 질서있는 방식으로 저장하고 분석할 수 있다
