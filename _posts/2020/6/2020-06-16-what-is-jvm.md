---
layout: post
title:  "[JVM] JVM?"
date:   2020-06-17 00:42:14 +0900
categories: [languages]
tags : [java, scala, jvm]
---
JVM은 도대체 어떤 녀석인지 간단하게 알아보자

<!--more-->

JVM(Java Virtual Machine)은 아마 Java나 Scala, Kotlin 등을 사용하고 있는 분이라면 이미 귀에 못이 박히도록 들었을 것으로 생각한다.

필자 또한 Scala로 개발을 하면서 알고는 있었지만 막상 JVM에 대해서 알고자 하지는 않았던 것 같았다.

그래서 여러개의 개발서적을 구입하고 또 여러 블로그들을 찾아서 그 내용을 읽어보았었다.

그렇게 공부한 내용들을 나중에 나 자신이 다시 봐도 이해를 바로 할 수 있도록,

혹은 필자처럼 JVM을 이제 공부해보려는 분들이 이해하기 쉽도록 작성하려 한다.

# JVM(Java Virtual Machine)?
JVM은 이 글을 읽는 분이라면 어떤 녀석인지는 알고 있을 것이다.

JVM은 하나의 개념 혹은 스펙(Speicification)이다.

그래서 우리는 JVM을 다운로드 받다 보면 정말 많은 JVM이 있다는 것을 알게 된다.

보통 우리가 가장 많이 아는 [OpenJDK](https://openjdk.java.net/)와 [Oracle Java](https://www.oracle.com/java/) 같은 것들이 있다.

이런 것들은 이런 JVM 스펙을 벤더들이 각자가 구현한 것이다

우리가 흔히 알고 있는 이런 JVM 구현체들 외에도

1. [Zulu](https://www.azul.com/downloads/zulu-community/?architecture=x86-64-bit&package=jdk) by Azul
1. [IcedTea](https://icedtea.classpath.org/) by Red Hat
1. [Zing](https://www.azul.com/products/zing/) by Azul

등이 있다.

## JVM 내부구조
우리가 개발을 하다가 어떨때 `java.lang.StackOverflowError` 같은 녀석을 본 적이 한 두번은 있을 것이다.

왜 뜬금없이 이 에러를 말하냐면 JVM이 Stack을 기반으로 하고 있기 때문이다.


![JVM Architecture](/files/jvm-architecture.png)

각각에 대해서 설명을 하자면

- Method Area: Class, Variable, Method, static variable, constant variable 등이 저장되는 영역
- Heap Area: `new` 명령을 통해서 생성된 Instance와 object가 저장
- Stack Area: Method 내에서 사용되는 값들이 저장되며, method가 호출될 때 **LIFO**로 하나씩 생성되고, 실행이 완료되면 **LIFO**로 하나씩 지워짐
- PC Register: CPU의 Register와 역할이 비슷. 현재 수행중인 JVM 명령의 주소가 저장
- Natvie Method Stack: 다른 언어(C/C++ 등)의 method 호출을 위해 할당되는 구역. 각 언어 맞게 Stack이 형성됨


아마 **Heap Area**가 가장 익숙하지 않을까 생각된다. 우리가 흔히 말하는 GC(Garbage Collection)이 Heap에서 일어나고 주요 Memory 이슈가 여기에서 발생하기 때문이다.

**Heap Area**는 JVM 스펙이 그러하듯 각 벤더마다 스펙을 최대한 따르며 구현을 하고 있다.

## Hotspot JVM?
Hotspot JVM은 현재 우리가 가장 많이 사용하고 있는 JVM 일 것이며, 가장 일반적인 JVM이다.

그래서 보통 JVM이라고 하면 Hotspot JVM을 말할 것이다.

발표된 이후 Sun microsystems가 이를 개발한 회사를 인수하여 Java 1.3 부터 default JVM이 되었다.

앞에서 JVM은 스펙이자 개념이라고 하지 않았는가?

그래서 이 Hotspot JVM도 **Heap Area**를 구현하고 있다.

구조를 알아보는 것은 다음 포스트를 통해서 작성하겠다.

여러 주제로 나누어서 관리를 하는게 보기에도 편하고 기억하기에도 편하기 때문이다.