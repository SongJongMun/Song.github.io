---
layout: post
title:  "Angular JS #1"
categories: [js, angular]
tags: [js, angular]
description: 앵귤러 JS 개요
---


# Angular JS

AngularJS 는 2009년 Miško Hevery과 Adam Abrons에 의해 개발된 MVC(또는 MVW – Model View Whatever) 웹 프레임워크로, SPA(Single Page Application) 형태의 웹 애플리케이션을 빠르게 개발할 수 있도록 도와줍니다.

## Angular JS의 특징

기존 방식(DOM 제어방식)은 변경이 필요한 대상 DOM 요소를 먼저 선택하고, 이후 필요한 작업을 수행하는 형태로 진행하게 됩니다. 반면 AngularJS는 출력할 데이터에 초점을 맞추어 작업이 수행되며, 데이터의 값이 변경되면 출력도 자동적으로 수행되도록 처리됩니다.

![AngluarJS의 양방향 데이터바인딩](http://www.nextree.co.kr/content/images/2016/09/------angular-300x199-1.png)

AngularJS는 다른 JS와 달리 양방향 데이터바인딩을 지원한다. 페이지가 로드되면 AngularJS는 동일한 이름의 input과 데이터 모델을 연결하고 이둘의 싱크를 유지시킨다. 개발자는 단지 UI의 어느 부분에 어떤 자바스크립트 속성을 할당할지 선언만 하고(앞의 예제의 ng-model) 동기화는 자동으로 이루어지게 된다.

AngularJS는 $watch 명령어를 이용하여 모델과 뷰를 실시간으로 감시하며, 이때 사용자의 클릭이나 타이핑 등의 동작으로 애플리케이션을 조작(예제에서 Input Box에 값을 입력하는 행위)이 있을 경우 모델은 변경이 발생했음을 뷰에 알려서, 뷰가 변경사항을 현재 표시 중인 내용에 적용한다. 이렇듯 AngularJS는 뷰와 모델이 지속적이고 실시간으로 업데이트시킨다

## 장점?

1. 유지보수가 쉽다, 개발속도가 빠르다.
2. 간편한 데이터 바인딩을 통해 뷰 업데이트가 쉽다.
3. 코드 패턴이 동일해 개인간 차이에 따른 결과물의 차이가 적다. 코드량이 감소한다.
4. SPA 개발에 최적화되어 있다.
5. 기능적인 분리가 명확해 협업이 쉽다.

# Angluar JS의 MVC 구조??

모델 : 보통 JSON으로 표현되는 애플리케이션의 특정한 데이터 구조를 말한다. 뷰가 서버와 통신하기 위해 꼭 필요한 내용이니 더 진행하기 전에 다음 JSON을 잘 살펴보자. 예를 들어 User ID 그룹은 다음과 같은 모델을 가질 수 있다:

```
{
  "users" : [{
    "name": "Joe Bloggs",
    "id": "82047392"
  },{
    "name": "John Doe",
    "id": "65198013"
  }]
}
```

뷰 : 뷰는 간단하다. HTML 혹은 렌더링된 결과를 말한다. MVC 프레임워크를 사용한다면 뷰를 갱신할 모델 데이터를 내려받은 뒤 HTML에서 해당 데이터를 보여줄 것이다.

컨트롤러 : 말 그대로 한번 생각해보자. 무언가를 조정한다. 근데 무엇을 조정할까? 데이터다. 컨트롤러는 서버 에서 직접 뷰 로 접근하는 일종의 중간 통로로서 필요할 때마다 서버와 클라이언트 통신으로 데이터를 변경한다.

[Nextree](http://www.nextree.co.kr/p3241/)에서는 다음과 같이 설명한다.

1. Scope<br>
Scope는 모델 변경을 감지하고 표현하기위한 책임을 갖는다
Scope는 DOM 구조와 가깝게 하이어라키 구조를 갖는다.

2. Model<br>
모델은 화면 템플릿에 합쳐지는 데이터를 가지고 있는 일반 자바스크립트 객체이다. (데이터라고 생각하면 된다.)<br>
Scope는 항상 모델을 참조하고 있다.

3. View<br>
템플릿 스트링과 모델을 합쳐서 HTML을 만들고 DOM으로 해석되어 Browser에 표현된다.<br>
Angular는 템플릿이 HTML이어서 바로 DOM으로 해석되고 DOM안에 directive가 템플릿 엔진인 $compile지시어를 통해 $watch를 설정하고 모델의 변경을 계속 감시하게 된다.<br>
View는 템플릿으로 Scope의 투영체이고, Scope는 Model과 View의 연결하며 controller로 이벤트를 보내는 역할을 한다.

4. Controller<br>
Controller는 View뒤에서 반드시 수행하는 코드이다.
Controller 역할은 모델을 생성하고 콜백 메소드를 가지고 View로 퍼블리싱을 담당한다.<br>
Controller 는 자바스크립트이고 업무적 행위를 정의한다. 또한 DOM rendering 정보가 일체 없다.

5. Directives (지시어)<br>
지시어는 HTML 을 확장하여 주고 행위를 일으키는 주체이다.
예를들어 앞의 예제에서 보았듯이 데이터 바인딩을 위한 이중 중괄호 표기{{}}, 컨트롤러가 뷰의 어느 부분을 감독할지를 지정하는 ng-controller, 인풋을 해당 모델의 구성물에 바인딩하는 ng-model 모두 Directive를 이용한 확장 문법이다.

# HTML과의 차이는?
HTML의 특성상 이것은 다이나믹 Applicaiton을 위해 디자인된게 아니라는 것은 누구나 알 수 있을 것이다. 

HTML은 정적인 마크업언어로써 요소들을 생한다든지 어떠한 요소가 변했을때 UI요소를 필터링한다든지 또는 다이나믹 Application에 필요한 다양한 일들을 할 수 없다. 
이러한 문제를 해결하기 위한것으로 Javascript를 이용하여 DOM을 수정할 수 있지만 개발자의 능력이 매우 중요하게 된다.

![기존 JS의 단방향 데이터 바인딩](http://www.nextree.co.kr/content/images/2016/09/------other-300x201.png) 

# Angluar JS + WebSocket?
데이터를 쉽게 View에 반영하는 방법 : Angluar JS
데이터를 쉽게 Controller에서 얻어오는 방법 : WebSocket
이렇게 하면 `실시간`으로 `무언가`의 데이터를 보여줘야 하는 웹 페이지의 프론트 앤드 설계를 진행할 수 있을 것 같다. 


#그러면 어떻게 사용할까?