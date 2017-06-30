---
layout: post
title:  "Spring Batch"
categories: [Spring, Batch]
tags: [Spring, Batch]
description: Spring Batch
---

# Spring batch

Spring batch는 batch 작업을 위한 스프링 프로젝트라고 생각하면 된다. Batch 작업의 요구사항은 주로 아래와 같다.

* 대용량 데이터를 처리할 수 있어야 함.
* 자동화.
* 안정성.
* 견고함.
* 성능.

이런 요구사항을 가지고 만들어진 프로젝트가 바로 Spring batch 프로젝트이다. Spring batch는 몇 개의 컴포넌트로 구성이 되는데 Job이라고 불리는 작업과 연관이 있는 컴포넌트들을 가지고 배치 작업을 수행한다.

## Component

### Job Launcher

Job Execution을 실행하는 기반 컴포넌트. 배치 작업의 트리거를 주는 역할을 한다고 생각하면 된다.

### Job

하나의 배치 작업을 정의. 주기적으로 실행되야 하는 동작을 정의하는 부분이다. 하나의 Job은 여러 개의 Step으로 구성되어 있다.

### Step

Job 내부에서 나눠지는 단계를 의미한다.

### Tasklet

각각의 Step에서 반복적으로 수행되는 로직이다. 주로 트랜잭션 처리를 위해서 사용된다.

### Job Instance

Job으로 정의된 부분이 Job Launcher에 의해 실행되면 하나의 Job instance가 생기게된다.

### Job Execution

Job을 실행할 때, 정의된 동작에 의해 실제 수행된 Execution을 의미한다. 예를 들어, 월요일 오전에 첫 번째 Batch 작업이 돌게 되었을 때 실패하고 두 번째 Batch 작업이 돌게 되었을 때 성공한다면 첫 번째와 두 번째는 각각 다른 Job Execution이 되고 같은 Job을 수행하였기 때문에 같은 Job Instance가 된다.