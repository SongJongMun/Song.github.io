---
layout: post
title: Java Garbage Collection 재공부
categories:
  - Java
tags:
  - Java
  - Garbage Collection
  - GC
description: GC
---

# 자바 성능 튜닝 이야기 17장 이야기

# 자바 Garbage Collection 재 공부

## 참조 문서(Java Hotspot GC)

<http://www.oracle.com/technetwork/java/javase/tech/g1-intro-jsp-135488.html>

## GC란?

메모리를 관리하기 위한 `로직`

C와는 달리 개발자가 메모리를 처리하기 위한 로직을 만들 필요가 없다!

그러다면 GC를 수행하는 메모리영역은 어떻게 되나?

### Java Runtime Data Area

![Image](http://blog.codecentric.de/wp-content/uploads/2009/12/java-memory-architecture-1.jpg)

1. PC Register
2. JVM Stack
3. Heap
4. Method Area
5. Runtime Constant Pool
6. Native Method Stack

크게 Heap 영역(3)과 Non-Heap영역(1, 2, 4, 5, 6)으로 나누어진다.

#### Heap 영역

`Shared Memory` / `Class Instance Memory`

클래스 인스턴스 배열이 저장되는 곳이다.

클래스 인스턴스 배열 : `여러 스레드에서 공유하는 메모리`

#### Non-Heap 영역

1. Method 영역 -> JVM 스레드에서 공유하는 영역 -> Runtime Constant Pool : 실제 상수값 또는 실행시에 변하는 필드 참조 정보 -> Field Information : Method Data, Method, 생성자 코드

2. JVM Stack -> 메소드가 호출되는 정보(Frame) -> 메소드 내부에 선언된 Local Instance, Temporary Results, Method Run Info, return Value

3. PC 레지스터 -> JVM Instruction Address

## GC의 역활

1. Memory 할당
2. 사용중인 메모리 인식
3. 사용하지 않는 메모리 인식

`2,3을 어떻게 인식해야 하나, 코드로 구현하게 된다면..?`

## Java의 메모리 영역

![JAVA Memory Area](http://cfile24.uf.tistory.com/image/216AE04C5654207F0AA967)

Java GC가 이용하는 메모리영역은 이미지에 나와있는 `Young Generation(Eden, Survivor1, Survivor2), Tenured Generation(Old + alpha)` 메모리 영역이다. (Perm 영역은 잘 사용하지 않을 뿐더러 JDK8 부터 사라지는 영역이다.)

### 메모리가 쌓이는 영역, Eden

`메모리 저장의 1차 저장소`

메모리에 객체가 생성되면, Eden 영역의 주소에 객체가 지정된다.

Eden영역에 데이터가 가득 차면, Eden 영역의 객체가 어딘가로 옮겨지거나 삭제되어야 한다.

이때 옮겨지는 영역이 Young 영역이다.

### Memory Switching Area, Survivor

`메모리 저장의 2차 저장소`

Eden 영역이 가득 차면, `Eden영역에서 Survivor 영역으로의 GC(Minor GC)`가 일어나게 된다. GC 수행시 두 개의 Survivor영역 중 객체가 저장된 영역을 From Survivor라고 부르며, 비어있는/GC 후 새로 메모리가 배정되는 Survivor영역을 To Survivor영역이라고 불리기도 한다.

이때 Eden영역에서 Survivor영역으로 옮겨지는 메모리들은 GC후에 살아남아있는 객체들이다. (GC는 어떤 기준으로 메모리를 선별하는가?)

간혹 GC를 수행할 때, Young 영역(Eden)에서 Survivor영역을 안 거치고, Old 영역으로 가는 객체가 존재할 수 있다.

GC를 수행하면서, 남은 Survivor 영역에 `못 들어갈만큼 큰 메모리를 가진` 객체는 바로 Old 영역으로 배치된다.

## Old 영역

Young Generation 영역에서 저장되었던 객체 중에 오래된 객체가 이동되어 저장되는 영역이다.

`Old 영역에서 수행되는 GC는 Major GC(Full GC)라고 불린다.`

## GC 방식

1. Serial Collector
2. Parallel Collector
3. Parallel Compacting Collector
4. Concurrent Mark-Sweep Collector
5. Garbage First Collector(G1 Collector)

### Serial Collector

1개의 CPU를 사용하여 Minor GC와 Major GC를 수행하는 방식(Serial하게)

GC를 수행할 때 애플리케이션 수행이 정지되며, 이 순간을 Stop-the-world라고 불린다.

"-XX:+UseSerialGC" 옵션으로 활성화 할 수 있다.

#### 수행방식

1. Eden 영역이 꽉 차면 빈 Survivor영역으로 살아있는 객체가 이동한다. (너무 큰 메모리는 바로 Old 영역으로 이동한다.)

2. From Survivor영역의 살아있는 메모리도 To Survivor로 이동한다. (To Survivor가 가득 차거나 너무 큰 메모리는 Old 영역으로 이동한다.)

3. Old 영역에 있는 객체들은 Mark-Sweep-Compact Collection 알고리즘을 수행한다.

4. GC 수행 후 메모리가 위치되어 있는 공간은 To Survivor와 Old 영역이다.(나머지는 비어있는 상태)

> Mark-Sweep-Compact Collection 알고리즘 : 쓰이지 않는 객체를 표시해서 삭제하고 한곳으로 모으는 알고리즘

Mark-Sweep-Compact Collection Algorithm은 다음과 같이 수행된다.

1. Old 영역의 객체들 중 살아있는 객체를 식별한다.(Mark)

2. Old 영역의 객체들을 훑어 쓰레기 객체들을 식별한다.(Sweep)

3. 필요없는 객체들을 지우고 살아있는 객체들을 한 곳으로 모은다.(Compact)

4. Mark-Sweep-Compact 단계를 거친 Old 영역은 한쪽으로 메모리가 압축되어 있는 모습을 보인다.

`Mark-Sweep-Compact Collection은 대기시간이 많아도 크게 문제되지 않는 클라이언트 역활의 시스템에서 사용된다.`

### Parallel Collector(Throughput Collector)

다른 CPU가 대기 상태로 남아있는 것을 최소화 하는 것(말 그대로 병렬로 처리하는 GC, 여러 CPU가 있는 서버에 적합)

Young 영역을 병렬로 처리한다. Old 영역은 Serial GC와 동일하다.

### Parallel Compacting Collector

Old영역에서 Serial Collector와 다른 방식을 사용한다.

1. 살아있는 객체 식별 후 표시(표시단계)

2. 이전 GC를 수행하여 컴팩션된 영역에 살아있는 객체 위치 조사(종합단계)

3. 컴팩션 수행, 수행 이후엔 컴팩션 된 영역과 비어있는 영역으로 나뉨(컴팩션단계)

#### Serial GC와 다른점이 뭐야?

1. Serial GC => Sweap 단계 : `단일 스레드`가 Old 영역 전체 탐색

2. Parallel Compacting Collector => Compaction(Summary) 단계 : `여러 스레드`가 Old 영역 나누어 수행, 앞서 진행된 GC에서 Compaction된 영역 별도로 수행

### CMS Collector(Low Latency Collector)

Major GC :

1. 표시 단계 : 초기 표시단계, 컨커런트 표시단계, 재표시 단계를 수행하여 살아있는 객체에 대해 표시하는 단계

2. 컨커런트 스윕 단계 : 표시된 스레기들을 제거

`Compacting 작업이 존재하지 않으므로, 메모리를 몰아넣지 않는다.`

> 초기 표시 단계(Stop-the-world) : 잠시 프로세스를 중지하고 살아있는 객체 탐색

> 컨커런트 표시 단계 : 서버 수행과 동시에 생존 객체 표시

> 재표시 단계(Stop-the-world) : 컨커런트 표시단계에서 표시하는 동안 변경된 객체를 찾아 다시 표시


`점진적 방식을 사용할 수 있는 GC 방식이다. Young 영역의 GC를 나누어 수행하여 서버 대기 시간을 줄일 수 있다.`

`서버 대기 시간이 짧아야 하는 반면 CPU가 많지 않은 웹 서버와 같은 곳에서 이용한다.`

### G1 Collector

메모리 영역을 바둑판형식, 작은 구역(`Region`)들로 나누어 관리한다.

기존 GC와는 다르게, Young(Survivor), Old 영역이 정해져 있고 메모리를 배치하는 것이 아닌, 메모리가 배치되고 나서 그 속성에 따라 Region의 Attribute가 정해진다.

Young(Eden) 영역은 초기에 몇 개의 영역을 지정하여 정해진다.

1. Not-Linear한 `초기 Eden구역에 객체가 생성`된다.
2. Minor GC 수행시 살아있는 객체들은 다른 구역으로 옮겨지고, 그 `구역은 새로운 Survivor영역이 된다.`
3. 몇 번의 Minor GC 수행 후에도, 해당 Survivor구역의 객체가 살아있으면(`노화 임계값(aging threshold)`), `Old 영역으로 승격된다.(Stop-the-world)`

Major GC는 다음과 같이 수행된다.

1. 초기표시(Stop-the-world) : Old 영역의 객체들에서 Survivor영역 객체들을 참고하고 있는 객체들 표시
2. 기본 구역 스캔 : Old 영역 참조를 가지는 Survivor영역 탐색, Young GC 이전에 수행 됨.
3. 컨커런트 표시 : 전체 Heap 영역에 Reachable / Live 객체를 탐색, Young GC 수행시 Stop.
4. 재표시(Stop-the-world) : Heap에 살아있는 객체들의 표시 작업을 완료,
5. 청소(Stop-the-world) : 살아있는 객체와 비어있는 구역을 식별하여, 필요없는 개체들을 지우고 비어있는 구역 초기화
6. 복사(Stop-the-world) : 살아있는 객체들을 비어있는 구역으로 모은다.



## 참고 자료

[http://hoonmaro.tistory.com/19](http://hoonmaro.tistory.com/19)

[http://imp51.tistory.com/entry/G1-GC-Garbage-First-Garbage-Collector-Tuning](http://imp51.tistory.com/entry/G1-GC-Garbage-First-Garbage-Collector-Tuning)
