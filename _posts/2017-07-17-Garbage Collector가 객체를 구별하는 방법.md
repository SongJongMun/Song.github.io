---
layout: post
title: GC가 Garbage를 찾아내는 과정
categories:
  - Java
tags:
  - Java
  - Garbage Collection
  - GC
description: GC
---

# GC의 역활?

1. Heap 내부 객체 중 Garbage를 찾아낸다.
2. 찾아낸 Garbage를 처리(제거) 하여 Heap의 메모리를 회수한다.

# GC-Reachability??

[D2 Blog](http://d2.naver.com/helloworld/329631)

>Java GC는 객체가 가비지인지 판별하기 위해서 reachability라는 개념을 사용한다. 어떤 객체에 유효한 참조가 있으면 'reachable'로, 없으면 'unreachable'로 구별하고, unreachable 객체를 가비지로 간주해 GC를 수행한다. 한 객체는 여러 다른 객체를 참조하고, 참조된 다른 객체들도 마찬가지로 또 다른 객체들을 참조할 수 있으므로 객체들은 참조 사슬을 이룬다. 이런 상황에서 유효한 참조 여부를 파악하려면 항상 유효한 최초의 참조가 있어야 하는데 이를 객체 참조의 root set이라고 한다.

![Runtime Data Area](http://d2.naver.com/content/images/2015/06/helloworld-329631-1.png)

자바 런타임 데이터 영역은 크게 다음과 같이 3가지 영역으로 나누어져있다.

1. 스레드 차지 영역
2. 메서드 영역
3. 큰 Heap

그리고 힙에 존재하는 객체들에 대한 참조는 대표적으로 4가지 종류가 있다.

1. Heap 내부 다른 객체 참조
2. Java 스택, 메소드 실행 시 사용하는 지역변수들, 파라미터들에 의한 참조
3. 네이티브 스택, 즉 JNI(Java Native Interface)에 의해 생성된 객체에 대한 참조
4. 메서드 영역의 정적 변수에 의한 참조

이들 중 힙 내의 다른 객체에 의한 참조를 제외한 나머지 3개가 root set으로, reachability를 판가름하는 기준이 된다.

## Strong reference

Root Set에 연결된 객체들 사이의 Link를 통해 도달 가능한 객체는 Reachable상태가 되고, 아닌 객체들은 unreachable상태로 정의된다.

 reachable 객체를 참조하더라도, 다른 reachable 객체가 이 객체를 참조하지 않는다면 이 객체는 unreachable 객체이다.
![Reachable, Unreachable](http://d2.naver.com/content/images/2015/06/helloworld-329631-2.png)

이 그림에서 참조는 모두 java.lang.ref 패키지를 사용하지 않은 일반적인 참조이며, 이를 흔히 strong reference라 부른다.

## Soft, Weak, Phantom Reference
java.lang.ref는 soft reference와 weak reference, phantom reference를 클래스 형태로 제공한다. 예를 들면, java.lang.ref.WeakReference 클래스는 참조 대상인 객체를 캡슐화(encapsulate)한 WeakReference 객체를 생성한다. 이렇게 생성된 WeakReference 객체는 다른 객체와 달리 Java GC가 특별하게 취급한다(이에 대한 내용은 뒤에서 다룬다). 캡슐화된 내부 객체는 weak reference에 의해 참조된다.
다음은 WeakReference 클래스가 객체를 생성하는 예이다.

```java
WeakReference<Sample> wr = new WeakReference<Sample>( new Sample());  
Sample ex = wr.get();  
...
ex = null;
```

위 코드의 첫 번째 줄에서 생성한 WeakReference 클래스의 객체는 new() 메서드로 생성된 Sample 객체를 캡슐화한 객체이다. 참조된 Sample 객체는 두 번째 줄에서 get() 메서드를 통해 다른 참조에 대입된다. 이 시점에서는 WeakReference 객체 내의 참조와 ex 참조, 두 개의 참조가 처음 생성한 Sample 객체를 가리킨다.

![](http://d2.naver.com/content/images/2015/06/helloworld-329631-3.png)

위 코드의 마지막 줄에서 ex 참조에 null을 대입하면 `처음 생성한 Sample 객체는 오직 WeakReference 내부에서만 참조된다. 이 상태의 객체를 weakly reachable 객체라고 한다.`

![](http://d2.naver.com/content/images/2015/06/helloworld-329631-4.png)

Java 스펙에서는 SoftReference, WeakReference, PhantomReference 3가지 클래스에 의해 생성된 객체를 "reference object"라고 부른다. 이는 흔히 strong reference로 표현되는 일반적인 참조나 다른 클래스의 객체와는 달리 3가지 Reference 클래스의 객체에 대해서만 사용하는 용어이다. 또한 이들 reference object에 의해 참조된 객체는 "referent"라고 부른다. Java 스펙 문서를 참조할 때 이들 용어를 명확히 알면 좀 더 이해하기 쉽다. 위의 소스 코드에서 new WeakReference() 생성자로 생성된 객체는 reference object이고, new Sample() 생성자로 생성된 객체는 referent이다.
