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
