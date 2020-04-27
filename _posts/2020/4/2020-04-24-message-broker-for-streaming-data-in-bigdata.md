---
layout: post
title:  "[BigData] 빅데이터 스트리밍 데이터 수집을 위한 Message Broker"
date:   2020-04-24 23:56:44 +0900
categories: [data_eng]
tags : [data, bigdata]
---

[지난 포스트](/data_eng/ingestion-bulk-and-streaming-data-in-bigdata/)에서 언급하고 말았던,

스트리밍형 데이터의 수집을 위한 Message Broker의 여러가지에 대해 공부한 것을 공유하려 한다.

<!--more-->

스트리밍형 데이터의 수집을 위한 Message Broker에 대해서 설명하고,

또 이런 Message Broker를 사용했을 때 어떤 이점이 있는지

그리고 Message Broker에서 신뢰성을 위해 **at most once** 와 같은 guarantee를 해준다고 했는데 그게 어떤건지, 어떻게 하는지 등에 대해서 설명한다.

Messsage Broker는 **Producer**와 **Consumer**라는 개념이 있는데,

말그대로 **Producer**는 message(data)를 생산하는 주체를 말하고,

**Consumer**는 message를 소비하는 주체를 일컫는다.

보통 빅데이터에서 Message Broker에 쌓여있는 데이터(message)들을 가져가서 처리하는 별도의 **Consumer**를 두어 이를 분산스토리지에 저장한다.

**Apache Kafka**와 같은 Message Broker 역할을 해주는 것은 message를 보내놓은 **topic** 과 같은 일종의 메시지들이 모여있는 방(room)을 통해

여러개의 **Consumer**가 연결하여 message(data)를 소비할 수 있게 해준다. 이런 것을 **Message Routing**이라고 한다.

Message Broker는 이런 **Consumer**의 ID 등을 받아서 이를 통해 어디까지 message(data)를 소비했는지 offset 관리를 통해,

**Consumer**가 다시 연결했을 때, 가져가지 못한 message들만 가져갈 수 있게 해주기도 한다.

Message Broker가 신뢰성을 위해 guarantee 해주는 방식으로

1. **at most once**
   - message의 성공/실패 여부와 상관없이 1번만 전송하는 것을 말하고
1. **exactly once**
   - message가 손실되거나 중복없이 1번만 전달
1. **at least once**
   - message 전달을 보장하며 중복의 가능성이 있음

가 있다.

먼저 **at most once**는 무슨 일이 일어나도 절대로 message를 다시 전송하지 않는 것을 말한다. 대게 데이터의 결손을 피하고자 재전송을 하는데, 이 방식은 재전송을 보장하지 않는다.

message를 받는 쪽이 오류가 있었더라도 Message Broker가 message를 보내지 않은 것은 아니기 때문이다.

**Consumer**가 message를 받고 ack 응답을 보내주기 전에 장애로 장애로 인해 ack 응답을 보내주지 못했더라도 **at most once**는 message를 재전송하지 않는다.

**exactly once**는 **Consumer**와의 통신 내용을 보장하기 위해 이를 중계해줄 Coordinator가 필요하다. Coordinator를 통해 서로 응답을 주고 받으며,

Coordinator의 지시에 따라 message의 재전송 여부 등을 결정한다.

그러나 이런 Coordinator로 인해

1. Coordinator 자체의 안정성
1. 퍼포먼스 문제

로 인해 **exactly once**는 잘 사용되지 않는다고 한다.

**at least once**는 중복 여부에 상관없이 메시지를 보내주는 것으로, 중복 제거는 **Consumer**가 처리해야 한다.

**Consumer**에게 ack 응답을 받기 전까지 오류가 감지되는 등의 상황이 발생하면 message를 재전송한다.

그래서 만약 ack 응답이 Message Broker의 timeout보다 더 오래 걸린다면 중복된 message를 받을 수도 있다.

||**at most once**|**exactly once**|**at least once**|
|정의|message의 성공/실패 여부와 상관없이 1번만 전송|message가 손실되거나 중복없이 1번만 전달|message 전달을 보장하며 중복의 가능성이 있음|
|재전송 여부|재전송 하지 않음|**Consumer**가 받지 못했을 경우 정확히 1번 전송을 보장하기 위해 재전송|ack 응답을 받지 못하면 재전송|
