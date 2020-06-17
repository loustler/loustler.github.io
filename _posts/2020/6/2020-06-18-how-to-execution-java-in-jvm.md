---
layout: post
title:  "[JVM] Java의 실행"
date:   2020-06-18 00:29:14 +0900
categories: [languages]
tags : [java, scala, jvm]
---
JVM에서 Java가 어떻게 실행이 되는지 알아보겠다.

<!--more-->

JVM에서 Java가 실행이 어떻게 되는지 공부한 것을 작성해보려 한다.

이 또한 여러 서적들과 블로그들을 통해서 공부를 했고,

누가 보더라도 쉽게 이해가 될 수 있도록 설명하려고 노력한다(필자 본인이 나중에 보더라도 언제든지 알아볼 수 있도록).

# JVM
일단 이 그림을 먼저 보자

![JVM Architecture](/files/jvm-architecture.png)

위 그림의 여러 파트를 설명하려 한다.

간단하게 Java의 실행과정은 다음과 같다

1. Java 파일(`.java`)을 컴파일(`javac`)하여 byte code로 전환(`.class`)
1. **ClassLoader**를 통해 Class 로드(`.class` 파일들)
1. **Execution Engine**을 통해 로드한 Class 해석
1. **Runtime Data Area**에 배치
1. 실질적인 수행

## Class Loading

Class Loading은 총 3개의 Class Loader가 관여한다.

1. Bootstrap ClassLoader
1. Extension ClassLoader
1. Application ClassLoader

### Bootstrap ClassLoader
> Java Runtime Core Class를 로드한다

Runtime Core Class라 함은 우리가 흔히 사용하는 `java.lang` 패키지 안에 있는 클래스들을 떠올리면 된다.

이런 Runtime Class는 `$JAVA_HOME/jre/lib`에 있는 `rt.jar`를 가져온다(Java 8 기준)

주로 `C`나 `C++`와 같은 Native Language로 구현되어 있다

Bootstrap ClassLoader의 주요임무는 최소한의 필수 클래스를 로드하는 것이다.

최소한의 필수 클래스들은

1. java.lang.Object
1. java.lang.Class
1. java.lang.Cloassloader

이다.

## Extension ClassLoader
Bootstrap ClassLoader를 자기 부모로 설정을 하고 필요할 때 Bootstrap ClassLoader에게 Class Loading 작업을 넘겨버림(주로 Extension ClassLoader가 Class Loading에 실패했을 때).

특정 OS나 플랫폼에 Native Code를 제공하고 기본 환경을 overriding할 수 있음

또한 security extension function 같은 확장 클래스들을 로드한다.

이런 extension class들은 `$JAVA_HOME/jre/lib/ext` 같은 곳에 위치한다.

이 설정값은 `java.ext.dirs`를 통해 설정할 수 있다.

## Application ClassLoader
Extension ClassLoader를 부모로 두며, 지정된 class path에 있는 유저 클래스들을 로드


이렇게 Bootstrap ClassLoader -> Extension ClassLoader -> Application ClassLoader 순으로 로드가 되고,

이렇게 로드가 된 ClassLoader들이 Class Loading에 실패하면 로드된 순서의 역순으로 클래스 로드를 시도한다.

그렇게 해도 실패하는 경우 `ClassNotFoundException`이 발생하게 된다.

## Byte Code Execution
`javac`를 통해 만들어진 Byte Code(`.class`) 파일을 로드를 하고 실행을 할 때,

JVM은 Byte Code 형식(Class file 형식)이 올바른 형식을 갖추고 있는지 validation을 진행한다.

이런 Class file의 형식을 한 번 보면 아래의 표와 같다

|Component|Desc|
|---|---|
|Magic Number|`0xCAFEBABE`로 제임스 고슬링이 정한 Magic Number(~ java8). `0xCAFEDADA`는 Java9 이후|
|Class file format version|Class file major/minor version|
|Constant Pool|Class constant variable이 모이는 곳. class 이름, interface 이름, field 이름 등등|
|Access Flag|Abstract Class, Static class 등 클래스 종류 표시|
|this class|현재 class 이름|
|super class|super class 이름|
|interface|class가 구현한 모든 interface|
|field|class의 모든 field|
|method|class의 모든 method|
|attribute|class의 모든 attribute(파일명 등)|

> Magic Number는 Unix에서 파일 종류를 식별하는 용도로 사용되어서 한 번 정해지면 바꾸기가 어려움.

이런 Class file format에 대해서 설명을 더 하자면,

- 만약 Class file format이 호환이 안된다면 `UnsupportedClassVersionError`가 발생하게 됨
- constant pool은 JVM이 코드를 실행할 때 필요할 때 constant pool에서 찾아보고 값을 참조함
- Access Flag는 클래스에 적용한 modifier를 결정
   - `ACC_PUBLIC`(`0x0001`)
   - `ACC_FINAL`(`0x0010`)
   - `ACC_SUPER`(`0x0020`)
   - `ACC_INTERFACE`(`0x0200`)
   - `ACC_ABSTRACT`(`0x0400`)
   - `ACC_SYNTHETIC`(`0x1000`)
   - `ACC_ANNOTATION`(`0x2000`)
   - `ACC_ENUM`(`0x4000`)
- this class, super class, interface 는 클래스에 포함된 타입 계층을 나타냄

