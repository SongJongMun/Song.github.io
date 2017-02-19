---
layout: post
title:  "Eclipse - Tsk Tag"
desc: "Eclipse - Tsk Tag"
keywords: "eclipse, taskTag"
date:   2017-02-19
categories: [Spring]
tags: [spring]
icon: fa-bookmark-o
---

2017. 02. 19 Eclipse - Tsk Tag

---

# TODO로 Task List 관리하기


## TODO가 왜 필요할까

1. 오늘은 여기까지 했음. 내일은 이부분 이부분 수정하자
2. 나중에 무언가가 추가되면 여기를 이렇게 저렇게 바꿔야 할껄?
3. 기능은 완료되었는데 소스정리가 필요함
4. 이부분에서 에러가 나는데 작성한 사람이 수정해줘

이러한 내용들을 그냥 주석으로도 처리할 수 있지만 따로 뽑아서 볼 수도 있다. 
주석을 일일이 찾아다니는 것보다 이렇게 작성하면 좀 더 편하게 개발할 수 있지 않을까 싶어 써보았다.

## TODO 작성하는 방법

그냥 모든 주석에 작성할 수 있다. //,  /* ~ */, /\*\* ~ *\/ 등의 주석에서 사용할 수 있다.

![todo0.PNG](/files/1888961859588990577)

## 작성한 TODO List 확인하는 방법

이클립스 상단 바의 window - show View - Others - Tasks 를 클릭하면 작성한 TODO 리스트들을 한번에 확인 가능할 수 있는 별도의 창이 열리게 된다.

![todo1.png](/files/1888962498137723174)

![todo2.PNG](/files/1888962579380342314)

해당 TODO 란을 더블클릭하면 주석문이 위치한 코드로 바로 이동가능하다.

## TODO 외의 다른 Task Tag는?

이클립스에서는 기본적으로 "FIXME", "TODO", "XXX" 3가지 Task Tag가 정의되어 있다.

각 Task Tag의 용도는 다음과 같다.

```
// TODO -> ...를 ... 순으로 정렬하는 기능이 추가되어야만 한다.
// 정렬할 때 속도를 최우선으로 고려하여 ... 알고리즘을 사용한다.
// 필수 기능은 아니므로 필수 기능이 완료된 이후에 구현한다.
```
```
// FIXME: 이 버그는 ... 문제로 발생했다.
// ... 방법으로 해결할 수 있으나,
// 사소한 문제이고 개발 기간의 제약이 있어 차후에 해결한다.
```
```
// XXX : 나머지...
```
## 직접 만드는 Task Tag

Window - Preferences - java - compiler - task tags를 가면 직접 Task Tag를 정의할 수 있고 중요도도 설정 가능하다.

![todo3.PNG](/files/1888966232568919438)

![todo4.PNG](/files/1888966686785623178)

![todo5.PNG](/files/1888966747291761830)


## Task Tag - 단축키로 만드려면

Window -> Preferences -> Java -> Editor -> Templates -> New 설정 창에서 새로운 템플릿을 만들어줘야 한다.



![todo6.PNG](/files/1888969429405960544)

![todo7.PNG](/files/1888970693622172430)

다음과 같이 템플릿을 만들고 적용할 수 있다.

소스코드 상에서 todo를 친 다음 Ctrl + spacebar만 눌러주면 자동으로 주석문이 만들어진다.

참고로 해당 설정 창에서 다른 Templete를 만들어 놓으면 유용하게 쓸 수 있다.
예를 들면 메소드 위에서 /\*\*문을 치고 엔터키만 누르면 자동으로 다음과 같은 주석이 만들어진다.

![todo8.PNG](/files/1888972240756854105)

