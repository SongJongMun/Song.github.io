---
layout: post
title: GC 비교
categories:
  - Java
tags:
  - Java
  - Garbage Collection
  - GC
description: GC
---

# G1 Collector와 CMS GC 비교

## Why faster?

[Takipi Blog](http://blog.takipi.com/garbage-collectors-serial-vs-parallel-vs-cms-vs-the-g1-and-whats-new-in-java-8/)

>The G1 collector utilizes multiple background threads to scan through the heap that it divides into regions, spanning from 1MB to 32MB

말 그대로 G1 Collector은 기존 CMS와 같은 GC와 달리 메모리영역을 일정 크기의 Region으로 나누어 관리하는 GC이다.

G1은 또한 CMS 콜렉터가 전체 STW 콜렉션 중에 만 수행하는 힙 (heap)을 이동 중에 압축하는 또 다른 이점을 가지고 있다.

## G1 Collector에 대한 추가 정보★★★★

출처 : [eddiego](http://eddiego.tistory.com/1)

> JDK7 부터 도입됨. server style GC. 작은 힙사이즈에서는 그닥. STW time 최소화를 목표로 설계

> 물리적인 Young, Old영역은 없어짐. 논리적으로 구분.
> 전체메모리를 1MB정도 크기의 region으로 나누어서 관리.  region보다 큰 오브젝은 region 여러개를 붙여서 할당(Humongos)

> 초기 object은 임의의 region에 생성됨. region별로 그때그때 스캔하여 안쓰는 object은 정리

> Evacuation: 특정 region이 살아있는 object이 매우 줄어들면 age에 따라 (논리적으로) Survivor나 Old영역으로 옮기고 빈 region은 폐기

> Compaction: Evacuation에 의해 듬성듬성 해진 region 조각모음. 해당 region을 참조하는 thread는 멈추나 VM전체가 멈추지는 않는다.
