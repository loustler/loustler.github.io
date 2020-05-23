---
layout: post
title:  "[MultiThreading] 기초 1. 프로세스와 쓰레드"
date:   2020-04-19 23:54:35 +0900
categories: [concurrency]
tags : [concurrency]
---

프로세스와 쓰레드, 그리고 Green Thread를 비롯한 여러가지에 대해서 공부하고 정리한다.

<!--more-->

우리가 알고 있는 Process, Thread 뿐만 아니라 Green Thread, Fiber, Coroutine를 비롯하여 여러가지에 대해 공부하였고,

이것을 정리해놓고자 한다.

1. **Process**
1. **Thread**
1. **Green Thread**
1. **Fiber**
1. **Coroutine**

# Process

현재 실행중인 Computer Program의 인스턴스를 의미한다.

OS에 의해서 관리가 되며 OS가 **Processor**에 할당해주면 Computer Program을 실행시킨다.

> Computer Program 자체는 그 자체만으로 아무것도 할 수 없고 실제로 **Process**가 할당되어야 무언가를 할 수 있는 수동적인 명령어들의 모음이기 때문

또한 1개 이상의 **Thread**를 가지게 된다.

각 **Process**는 고유한 Memory 영역을 비롯한 system resource를 OS로부터 할당받게 되고 서로의 resource에 접근할 수 없다.

## MultiTasking
여러개의 **Process**가 **Processor**를 비롯한 system resource를 공유할 수 있도록하는 방법이다.

각 **Processor**는 한 번에 1개의 작업(Task)을 수행하는데, Multitasking을 통해 다른 작업이 완료될 때까지 기다릴 필요 없이 실행중인 작업을 전환(switching)할 수 있다.

OS의 구현에 따라서 작업들이 input/output 작업을 할 때, 작업이 전환될 수 있다.

# Thread
**Process**의 구성요소로서 OS의 Scheduler에 의해 독립적으로 관리될 수 있는 가장 작은 단위의 프로그래밍된 명령 시퀀스다.

**Process**와 달리 같은 **Process**에 있는 **Thread**들은 서로 memory와 같은 resource를 공유하며 서로 접근할 수 있다.

실행되는 동안 **Program Stack(Call Stack)**과 **Program Counter(PC)**에 현재 상태를 표시한다.

**Call Stack**에는 현재 실행중인 메서드에 이르기까지의 호출 시퀀스를 지역 변수, 매개변수 등을 포함해 표시하며,

**PC**에는 현재 메서드에서 실행중인 명렁어의 위치를 표시한다.

이를 통해서 **Processor**가 **Thread**의 스택 상태를 조작하고, PC가 가르키는 명령을 실행한다.

일반적으로 OS에서 관리를 하므로 **Native Thread**라고 부르기도 한다.

## MultiThreading
주로 **MultiTasking** 환경에서 찾을 수 있다.

1개의 Process Context에 여러개의 **Thread**가 존재할 수 있는 광범위한 프로그래밍 및 실행모델이다.

이런 **Thread**들은 **Process**의 resource를 공유하지만, 독립적으로 실행을 될 수도 있다.

**Thread Programming Model**은 개발자에게 유용한 동시 실행에 대한 추상화를 제공해준다.

**MultiThreading**을 1개의 **Process**에 적용하면, MultiProcessing System에서 병렬 실행(Parallel Execution)을 수행할 수 있다.

## Process vs Thread

|**Process**|**Thread**|
|---|---|
|독립적으로 존재|**Process**의 하위집합|
|**Thread**에 비해 많은 state 정보를 전달|**Process** state와 Memory와 기타 resource 공유|
|독립된 address space를 가짐|Address space를 공유|
|서로 통신하려면 IPC(Inter-Process Communication)을 통해서만 통신가능||
|Context Switch가 **Thread**에 비해 느림|Context Switch가 **Process**에 비해 빠름|

> Address Space는 Heap 영역이라고 생각해도 된다

# Green Thread
**Thread**의 종류 중 하나로 기존의 OS의 아래에 있는 **Native Thread**와는 다른 Runtime Library나 VM에 의해서 scehdule되는 Thread를 말한다.

Native OS 기능에 의존하지 않고 **MultiThread**환경을 에뮬레이션하며, Kernel space 대신 User space에서 관리가 된다.

또한 **Native Thread**가 지원되지 않는 환경에서도 동작할 수 있다.

## Performance
**Multi-core Processor**에서 **Native Thread** 구현체는 multiple processor에서 동작할 수 있도록 자동으로 할당되지만,

**Green Thread** 구현체는 그렇지 못함

**Green Thread**는 몇몇 VM들에서 **Native Thread**에 비해서 빠르게 시작될 수 있다.

## IN the JVM
Java 1.1에서는 유일한 threading model이었다(적어도 Solaris 버전에서는).

**Green Thread**가 **Native Thread**와 비교해서 가지고 있는 몇가지 한계점으로 인해, 이후 Java 버전에서는 **Green Thread**를 제거하고 **Native Thread**를 선호한다.

[Kilim 프로젝트](https://github.com/kilim/kilim)과 [Quasar 프로젝트](https://github.com/puniverse/quasar)에서 Java Compiler가 생성한 Byte Code를 수정하여 Green Thread를 지원하는데,

Quasar 프로젝트가 [Kotlin](https://kotlinlang.org/)과 [Closure](https://clojure.org/)을 지원한다.

# Fiber
**Thread** 중에서도 특히나 더 가벼운 버전이다.

얼마나 가벼울지는 이름 자체만 봐도 예상이 갈 것이다.

**Thread**처럼 address space를 공유하는데, **Fiber**는 협력적인 multitasking을 하고 **Thread**는 선점 multitasking을 한다.

**Thread**는 종종 사용중인 Busy **Thread**를 선점하고 다른 **Thread**를 재개하기 위해서 Kernel의 Thread Scheduler에 의존한다.

그러나 **Fiber**는 **Fiber Scheduler**에 의해서 schedule이 되지만, 다른 **Fiber**를 실행시키기 위해서는 스스로가 멈춰야 한다.

## Coroutine?
**Fiber**는 본질적으로 **Coroutine**과 같은 컨셉을 설명한다.

**Coroutine**은 언어 레벨의 구조에 제어를 흐름하는 형태인 반면에, **Fiber**는 시스템 레벨의 구조이며 동시에 실행되지 않는 **Thread**로 간주된다.

## Process, Thread and Fiber
**Process**, **Thread** 그리고 **Fiber**의 관계는 아래 그림을 참고하면 되겠다.

![Process, Fiber and Thread by Trinh Phuc Tho's SlideShare](/files/process-thread-fiber.jpg)

# Coroutine
비선점형 multitasking을 위해 subroutine을 일반화한 Computer Program Component다.

**Coroutine**은 협력작업, exception, event loops, iterators 와 같은 Program Component를 구현하는데 적합하다

## Thread?
**Coroutine**는 **Thread**와 매우 비슷하다.

그러나 **Coroutine**은 협력적인 multitasking으로 선점형 mutlitasking인 **Thread**와는 다르다(스케줄링기법이 다르다).

즉 **Coroutine**은 concurrency(동시성)을 제공해주지만 parallelism은 아니라는 것이다.

**Coroutine**의 장점으로 **Thread**에 비해 hard-cotext에서 사용될 수 있다는 것이다.

왜냐하면 **Coroutine**간 context switching은 system call이나 blocking call이 필요하지 않기 때문이다.

이 말은 mutex, semaphore와 같은 별도의 synchronisation(동기화) 작업이 필요하지 않다는 것을 말한다.

그리고 ciritical section을 보호하는데 있어서 OS의 개입이 필요없다.

## Support

Kotlin은 API에서 Coroutine을 지원하고 있으니 [가이드](https://kotlinlang.org/docs/reference/coroutines-overview.html)를 참고해보면 되겠다.

Java는 기본적으로는 제공하지 않으며 그렇다고 Coroutine의 구현까지 제재하고 있지는 않다. [Da Vinci JVM](http://openjdk.java.net/projects/mlvm/)처럼 JVM이나

byte code를 수정하여 제공해주는 [Java Coroutines](https://code.google.com/archive/p/coroutines/)나 [Java Flow](http://commons.apache.org/sandbox/commons-javaflow/)를 통해서도 사용할 수 있다.

Scala 또한 Scala macro를 통하여 Coroutine을 제공해주는 [coroutines 프로젝트](https://github.com/storm-enroute/coroutines)가 있다.

# References
1. [Process by WIKIPEDIA](https://en.wikipedia.org/wiki/Process_(computing))
1. [Thread by WIKIPEDIA](https://en.wikipedia.org/wiki/Thread_(computing)#Multithreading)
1. [Green Thread by WIKIPEDIA](https://en.wikipedia.org/wiki/Green_threads)
1. [Fiber by WIKIPEDIA](https://en.wikipedia.org/wiki/Fiber_(computer_science))
1. [Coroutine by WIKIPEDIA](https://en.wikipedia.org/wiki/Coroutine#Implementations_for_Scala)