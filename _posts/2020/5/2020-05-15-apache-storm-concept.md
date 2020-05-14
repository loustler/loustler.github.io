---
layout: post
title:  "[Apache Storm] Storm Concepts"
date:   2020-05-15 00:04:44 +0900
categories: [data_eng]
tags : [data, bigdata, storm]
---

실시간 스트리밍 처리에 사용하는 Apache Storm과 그 Concept에 대해서 공부한 것을 공유한다.

<!--more-->

[Apache Storm](https://storm.apache.org)은 Apache 산하에 있는 최상위 프로젝트 중 하나로, 스트리밍 처리를 할 수 있는 데이터 처리 플랫폼이다.

이전 회사에서 사용을 했었던 경험이 있었지만, 아무래도 시간이 지나고 현재는 사용하지 않게 되다보니 많은 부분을 잊어버려 다시 공부할 겸 포스팅한다.

요즘은 Spark Stream을 많이 사용할 것으로 예상되고 Apache Storm은 많이 사용되지 않을 것 같기도 하다.

아무튼 **Apache Storm**의 기본적인 컨셉은 아래와 같이 이루어져 있다.

1. Topologies
1. Streams
1. Spouts
1. Bolts
1. Stream groupings
1. Reliability
1. Tasks
1. Workers

# Topologies
실시간 데이터 처리를 위한 로직들이 **Topology**로 패키지된다.

우리가 알고 있는 Hadoop의 MR의 작업과 유사하며, 다른점으로는 MR은 종료가 있는 반면 **Topology**는 영구적으로 실행된다(명시적으로 종료할 때까지).

**Topology**는 그룹핑된 Stream과 연결된 **Bolt**와 **Spout**의 그래프를 의미한다.

즉 실시간 스트리밍을 어떻게 처리할지 **Bolt**와 **Spout**로 구성된 작은 데이터 처리 파이프라인? 이라고 봐도 될 거 같다.

**Spout**로부터 들어온 데이터들이 여러 개의 **Bolt**를 거쳐서 어떻게 처리되는지 명세한 DSL? 이라고 봐도 될 거 같기도 하다.

**Topology**는 n개의 **Spout**와 n개의 **Bolt**들로 구성된다.

# Streams
Storm의 핵심 추상화 중 하나로, 분산 방식으로 병렬로 처리되고 생성되는 무한한 tuple sequence다.

**Stream**은 Stream tuple의 필드 이름을 지정하는 schema로 정의된다.

이런 Stream tuple은 기본적으로 **integer**, **long**, **short**, **byte**, **string**, **double**, **float**, **boolean**, **byte array**와 같은 primitive type이 포함될 수 있다.

또 tuple에서 custom type을 위한 **Serializer**를 정의할 수도 있다.

이런 모든 **Stream**은 선언할 때 ID가 부여된다. 만약 단일 Stream **Spout**, **Bolt**를 사용한다면 별도의 ID를 지정하지 않고 단일 Stream을 선언하는 방법도 있다. 이때는 Stream에 ID는 `default`가 된다.

필자가 이해한 것으로 설명을 하겠다.

우선 **Stream**은 **Tuple**들이 조합된 것을 의미한다.

**Tuple**은 Storm의 main data structure로 값들의 list로 고유의 이름을 가지고 있으며 이때 값들은 아무 타입이나 될 수 있다.

단 **Tuple**에 값으로 custom type을 사용할 경우 이에 대한 **Serializer**를 제공해줘야 된다(prmitive type은 Serialization이 보장됨).

이게 무슨 소리냐면 **Stream**은 우리가 알고 있는 그 **Stream**이 맞다.

보통 **Stream**이라 하면 타입이 있을 것이 아닌가? 어떤 데이터들이 흐르는 것이니까

그 데이터의 타입이 **Tuple**이라는 녀석이고 이 **Tuple**에 원하는 값을 넣고 빼서 **Stream** 처리를 한다고 보면 된다.

**Tuple**은 어떤 값도 들어갈 수 있으니 우리가 원하는 대로 정의를 하고, primitive type을 제외하고 별도의 Class를 만들어서 사용을 하려면 Serialization을 할 수 있게 **Serializer**를 제공하라는 것이다.

# Spouts
**Spout**는 **Topology**에 들어오는 **Stream**의 source가 나오는 곳이다.

**Spout**는 수도꼭지라고 생각하면 된다.

수도꼭지를 통해서 물이 흘러나오지 않은가? 그럼 이 수도꼭지 **Spout**을 통해서 **Stream** 데이터들이 쏟아져 나오는 것이다.

보통 **Spout**는 외부의 source를 사용하게 된다. Kafka와 함께 사용하던지 말이다.

그래서 **Spout**는 **Stream**데이터를 처리 하기 위해서 `public void nextTuple()`이라는 중요한 메서드가 있는데 이것을 통해서 계속 **Tuple**을 받아온다.

이 때 **Tuple**의 처리 여부를 확인하기 위해서 `public void ack(Object msgId)와 `public void fail(Object msgId)`가 있다.

만약 `fail`로 처리한다면 실패처리된 **Tuple**은 Storm의 내부 큐로 다시 넣어 재시도를 하게 된다.

**Spout**이 이렇게 읽은 **Tuple**을 어디론가 흘려 보내야 되지 않겠는가? 이런 **Tuple**을 **Spout**는 **Bolt**로 보내 실질적인 스트리밍 데이터 처리를 하게 된다.

# Bolts
**Bolt**는 실질적으로 스트림 데이터 **Tuple**의 처리를 담당하는 곳이다.

즉 **Topology**에서 이루어지는 데이터의 처리는 모두 **Bolt**를 통해서 이루어진다.

**Bolt**는 우리가 데이터를 다룰 때 하게 되는 *filtering*, *function*, *aggregation*, *join*, *DB 작업* 등을 수행할 수 있다.

또 **Bolt**는 앞에서 말했듯이 보통 여러개의 **Bolt**로 이루어지는 경우가 많으며, 어떤 **Bolt**들은 다음 **Bolt**로 변환되거나 validation가 된, 즉 일종의 전처리를 담당할 수도 있다.

또 **Bolt**는 여러개의 **Stream**을 처리할 수도 있다.

**Bolt**에서 가장 핵심이 되는 method는 `public void execute(Tuple input)`이며, 이 `execute` method를 구현하여 데이터를 처리한다.

이 때, `OutputCollector`를 통해서 **Tuple**을 emit할 수 있다.

# Stream groupings
**Topology**의 정의의 일부는 각 **Bolt**가 입력으로 받아야 되는 **Stream**을 지정하는 것이다.

쉽게 말하면 앞에서 **Topology**는 스트림 데이터가 어디서 나와서 어떻게 처리되는지를 **Spout**와 **Bolt**로 정의한 DSL 혹은 그래프와 같다고 했다.

여기에서 **Bolt**가 입력으로 받아야 될 **Stream**을 지정해주는 것을 말한다. 데이터의 Input으로

**Stream Grouping**은 **Bolt**의 작업간에 입력으로 받은 **Stream**을 어떻게 분할을 할지 정하는 방법을 정의하는 것이다.

Storm은 8개의 **Stream Grouping**을 제공해주며, 원한다면 [CustomStreamGrouping](https://storm.apache.org/releases/2.1.0/javadocs/org/apache/storm/grouping/CustomStreamGrouping.html)을 이용하여 custom으로 정의할 수도 있다.

8개의 **Stream Grouping**은 다음과 같다.

1. **Shuffle grouping**
   - 각 **Bolt**가 동일한 개수의 **Tuple**을 가져갈 수 있도록 보장하는 방법으로 무작위(random)으로 분배한다
1. **Fields grouping**
   - grouping에 사용할 field를 지정하여 이 지정된 field를 이용해서 분할한다
   - 만약 `user_id`라는 field로 grouping을 한다면 동일한 `user_id`를 가진 **Tuple**은 동일한 **Bolt**에만 가게 된다
1. **Partial Key grouping**
   - 위의 **Fields grouping**와 유사하지만, 2개의 downstream **Bolt** 사이에서 load balance가 된다. 데이터가 왜곡될 때 resource를 효율적으로 사용할 수 있다
1. **All grouping**
   - **Stream**이 모든 **Bolt**에 분할됨
1. **Global grouping**
   - 전체 **Stream**이 1개의 **Bolt**에 전달됨
1. **None grouping**
   - 별도의 Grouping을 하지 않는 것과 같음
1. **Direct grouping**
   - **Stream**을 생산하는 *Producer*가 *Consumer*를 지정하는 방식. 즉 어떤 **Bolt**가 **Stream**을 처리할지 *Producer*에서 정한다는 것이다.
1. **Local or shuffle grouping**
   - Target **Bolt**가 1개 이상의 task를 동일한 work process에서 실행중이라면 **Tuple**은 그 작업으로 셔플된다. 그 외는 일반 **Shuffle Grouping**과 동일

# Reliability
Storm은 **Spout Tuple**이 **Topology** 내에서 완전히 처리되도록 보장한다.

모든 **Spout Tuple**에 의해서 trigger 되는 **Tuple Tree**를 추적하고 해당 **Tuple Tree**가 성공적으로 완료되는 시기를 결정한다.

그래서 모든 **Topology**에는 *Message Timeout*이 존재한다. 만약 이 *Message Timeout* 안에 **Spout Tuple**이 완료되지 않으면, **Tuple**을 실패시킨 뒤 다시 실행한다.

이런 Storm의 Reliability 능력의 장점을 얻기 위해서는,

1. **Tuple Tree**에 edge가 생성될 때
1. 각 **Tuple**의 처리가 완료될 때

Storm에게 이것을 알려줘야 된다.

이런 일련의 작업들은 [OutputCollector](https://storm.apache.org/releases/2.1.0/javadocs/org/apache/storm/task/OutputCollector.html)를 통해서 처리하는데,

**Bolt**에서 emit할 때 사용한 그것과 똑같다.

이 때, `public void ack(Tuple tuple)` method를 이용해서 **Tuple**의 작업이 완료되었음을 Storm에게 알려준다.

# Tasks
각 **Spout**와 **Bolt**는 Cluster 내에서 많은 **Task**를 실행한다.

이런 각 **Task**는 1개의 Thread of execution에 해당되며, **Stream Grouping**는 어떻게 Tuple을 1개이상의 **Task**에서 다른 **Task** Set에게 보내줄지 정의한다.

병렬성의 정도는 [TopologyBuilder](https://storm.apache.org/releases/2.1.0/javadocs/org/apache/storm/topology/TopologyBuilder.html)의 `setBolt`와 `setSpout`를 통해서 결정할 수 있다.

# Workers
**Topology**는 1개 이상의 **Worker Process**를 통해 실행된다.

각 **Work Process**는 물리적인 JVM이며, **Topology**의 모든 **Task** subset을 실행시킨다.

이 때 Storm은 모든 **Work**에게 동일한 **Task**를 분산시키려한다.


Storm의 기본적인 Concept에 대해서 Storm의 [공식 Docs](https://storm.apache.org/releases/2.1.0/Concepts.html)를 통해서 공부를 했고, 그것을 최대한 풀어서 필자가 이해한 방법으로 설명하려 했다.
