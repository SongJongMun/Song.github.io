---
layout: post
title:  "Dispatcher Servlet"
categories: [Spring, Dispathcer]
tags: [Spring, Dispathcer]
description: Spring Dispathcer Servlet
---

## Spring MVC 이전의 모델 형태

* Model 1 -> Model 2를 거쳐서 Spring MVC 형태로
* Model 2는 MVC 구조를 지닌다

## MVC 구조

* web.xml에서 servlet을 정의하고 servlet을 mapping
* java 코드에서는 HttpServlet을 extends하여 doGet(), doPost() 등을 통해 Servlet의 동작을 정의

## Front Controller

* login Servlet, logout Servlet 등 각 동작에 해당하는 서블릿을 따로 두지 않음
* 로직 맨 앞에서 모든 요청을 받아서 처리하는 Controller
* 이를 이용하는 패턴을 Front Controller Patter이라고 한다
ex) *.do 로 끝나는 URL은 모두 Front Controller Servlet이 가로채서 처리

# Spring MVC

## Dispatcher Servlet의 구성

* web.xml에 모든 Servlet 정의가 사라진다
* 대신 Dispatcher Servlet이 정의됨
* Dispatcher Servlet이 Front Controller Servlet의 역할을 한다
* Dispatcher Servlet은 기존 Front Controller Servlet처럼 다른 Servlet을 호출하는 것이 아니라, Controller라는 Bean을 호출

### 1\. HandlerMapping

* 서버로 들어온 요청을 어느 핸들러로 전달할 지 결정하는 역할
* 즉, 들어온 요청에 대한 책임을 무엇을 보고 결정하느냐
    * BeanNameUrl~
        * ![Inline-image-2017-03-21 11.53.18.059.png](/files/1911389054079374444)
        * Handler Mapping을 따로 Bean으로 설정 안해줘도 동작하는 이유는 \<mvc:annotation-driven />을 호출하면 자동으로 BeanNameUrlHandlerMapping이 활성화되기 때문
    * SimpleUrl~
        * ![Inline-image-2017-03-21 11.46.50.642.png](/files/1911389330850748419)
        * 다음과 같이 key=value와 같은 형태로 핸들러를 매핑한다
    * RequestMapping~ : Annotation Based Spring에서 주로 사용

### 2\. HandlerAdapter

* DispatcherServlet과 실제 핸들러 구현제 차이를 이어주는 Object Adapter
* 즉, DispatcherServlet은 핸들러들의 생김새를 모르기 때문에 핸들러를 제어하려면 각 핸들러의 모습을 알고있는 얘네가 필요하다
    * HttpRequest~
    * SimpleController~
    * AnnotationMethod~ : deprecated
    * RequestMapping~ : Annotation Based Spring에서 주로 사용
    * SimpleServlet~ : Controller가 아닌 Simple Servlet을 사용 가능하게 해주는 Adapter

### 예)

* mvc:resources, mvc:annotation-driven 등의 `핸들러`들이 *context.xml에 선언되면 그에 맞는 Handler Mapping과 Handler Adapter가 활성화된다

### 3\. HandlerExceptionResolver

* 요청 처리 과정에서 발생하는 예외를 제어하고 할 때 사용
    * DefaultHandlerExceptionResolver : http status code를 리턴하는 등

### 4\. RequestToViewNameTranslator

* 핸들러가 아무것도 리턴하지 않았을 때 view 이름을 결정하는 역할

### 5\. ViewResolver

* `문자열 기반`의 view 이름을 토대로 실제 view 구현체를 결정하는 역할
    * UrlBased~
        * ![Inline-image-2017-03-21 12.12.57.041.png](/files/1911389547016582814)
    * InternalResource~
    * Velocity~
    * Freemarker~
    * 아래 두 ViewResolver는 각 언어가 제공하는 view를 지원한다

### 6\. LocaleResolver / LocaleContextResolver

* view 렌더링 시 국제화 지원을 위한 Locale과 Timezone을 결정
![Inline-image-2017-03-21 13.55.09.319.png](/files/1911389691122573434)
![Inline-image-2017-03-21 13.55.33.843.png](/files/1911389824185695816)
![Inline-image-2017-03-21 13.58.12.669.png](/files/1911389946116109149)
    * AcceptHeader~
    * Cookie~
    * Session~
        * ![Inline-image-2017-03-21 14.01.25.290.png](/files/1911390254943511352)
    * Fixed~
        * ![Inline-image-2017-03-21 13.57.37.071.png](/files/1911390367967255045)

### 7\. ThemeResolver

* view 렌더링 시 어떤 테마를 사용할 지 결정
    * Cookie~
    * Fixed~ : Hard-coded
    * Session~

### 8\. MulitpartResolver

* 멀티파트 요청에 대한 구현체를 결정하는 역할
    * Commons~
    * StandardServlet~

### 9\. FlashMapManager

* redirect와 같이 하나의 요청에서 다른 요청으로 속성 값을 전달하는데 FlashMap을 사용할 수 있는 매커니즘을 제공

## DispatcherServlet.properties

* DispatcherServlet에 기본적으로 설정되어있는 Default값
* 개발자가 명시하지 않는 속성에 대해서는 이 Default값을 따른다
* 이 파일은 기본 설정 파일이므로 건드리지 말것
* 하지만 이 Default값을 알아두면 불필요한 중복 설정을 피할 수 있다

### 예)

* LocaleResolver - AcceptHeaderLocaleResolver
* HandlerMapping - BeanNameUrlHandlerMapping, DefaultAnnotationHandlerMapping
* HandlerAdapter - HttpRequestHandlerAdapter, SimpleControllerHandlerAdapter, AnnotationMethodHandlerAdapter
* ViewResolver - InternalResourceViewResolver

## @MVC

* \<mvc:annotation-driven /> 또는 @EnableWebMvc
    * HandlerMapping - RequestMappingHandlerMapping
    * HandlerAdapter - RequestMappingHandlerAdapter

# DispatcherServlet의 요청 처리 프로세스

![Inline-image-2017-03-21 15.16.41.657.png](/files/1911390487634401793)

## 초기화

1. DispatcherServler.properties 읽어와서 기본 전략 설정
2. init() : Bean 생성
    * servletBean
    * applicationContext
    * webApplicationContext
    * 설정을 읽어보고 일치하는 값이 있으면 그에 맞게 생성 : CoC(Convention of Configuration) 방식과 유사
    * 없으면 DispatcherServler.properties의 전략에 따라 생성
    * HandlerMapping과 HandlerAdapter, HandlerExceptionResolver 등은 여러개가 정의될 수 있다
        * 설정을 모두 스캔하여 Map으로 저장
        * 없을 시엔 마찬가지로 Default값 사용
3. DI

## Terms

* ApplicationContext = BeanFactory + α
* WebApplicationContext = ApplicationContext + ServletContext

## mvc:annotation-driven??

* AnnotationDrivenBeanDefinitionParser.java에서 어노테이션이 있는 클래스를 동적으로 빈으로 생성
    * HandlerMapping, HandlerAdapter 등이 동적으로 생성된다
* Jackson 등의 Message Converter를 자동으로 등록

## @EnableWebMvc??

* 위에꺼랑 뭔가가 다르다고 했는데 놓친듯하다

## Handler Execution Chain

* Controller + Interceptors
* 하나의 Controller에 대해 여러개의 Interceptor들이 연쇄적으로 처리되는 과정
* request가 들어오면 Handler 목록에서 이를 처리할 수 있는 Handler를 탐색
* 처리할 수 있는 Handler가 존재할 때 이를 반환

## LocaleContextHolder / RequestContextHolder

* ThreadLocale을 사용하여 Locale값이나 Request attribute의 값을 인자 전달 없이 가져올 수 있다.
* 단, DispatcherServlet의 Thread를 참조할 수 있는 범위에서만 사용 가능하다
    * Filter
    * AOP 등에서는 사용 불가

## Interceptor의 구현

* HandlerInterceptor 인터페이스를 구현하여 만들 수 있다
* 하지만 HandlerInterceptorAdapter를 extends하여 필요한 메소드만 오버라이딩할 수도 있다
![Inline-image-2017-03-21 15.48.53.802.png](/files/1911390673177357927)
다음과 같이 동일한 Controller에 대해서 여러개의 Interceptor를 연쇄적으로 적용할 수 있다 : `Handler Execution Chain`

## 요청 선처리 작업

### FrameworkServlet.service() -> HttpServlet.doGet()/doPost()/... -> FrameworkServlet.processRequest()

### FrameworkServlet.processRequest()

### DispatcherServlet.doService()

1. ContextHolder에 필요한 정보를 add
2. request attribute에 필요한 정보를 add

## HandlerExecutionChain 결정 및 실행

### DispatcherServlet.doDispatch()

1. 받은 요청을 핸들링 할수 있는 Handler에 맞는 Adapter를 가져온다
2. Interceptor들에게 preHandle을 처리하라고 지시 : Handler Execution Chain
3. Handler 처리
4. Interceptor들에게 postHandle을 처리하라고 지시 : Handler Execution Chain

## 뷰 렌더링 / 예외 처리

### DispatcherServlet.processDispatchResult()

### DispatcherServlet.processHandlerException()

* 발생한 특정 exception을 처리할 수 있는 ExceptionResolver를 탐색 (탐색결과 : ModelAndView)

### DispatcherServlet.render()

* String으로 받은 view 이름을 가지고 ViewResolver 목록에서 해당 view를 rendering할 수 있는 ViewResolver를 탐색

## 요청 처리 종료

### DispatcherServlet.processRequest().finally

# @RequestMapping 처리

## RequestMappingHandlerAdapter

### RequestMappingHandlerAdater가 지원하는 Method Parameter

* @RequestParam
* @PathVariable
* @ResponseBody 등의 Annotation
* Map, Model, Session 등....
* Parameter로 존재하는 일반 객체는 @ModelAttribute가 생략된 것
* @ModelAttribute로 들어온 값은 필드명을 사용하여 최대한 Mapping시킨다
* 메소드 레벨에서 @ModelAttribute가 붙게되면 그 클래스 안의 모든 request 수행 전에 해당 메소드가 수행된다
    * ![Inline-image-2017-03-21 16.36.51.066.png](/files/1911390842464153124)
    * "test"라는 이름으로 view(*.jsp)에서도 참조가 가능

### RequestMappingHandlerAdater가 지원하는 Return Type

* @ResponseBody
* ModelAndView, View
* Map, Model, ModelMap
* String
* Void 등....

### MessageConverters

* List나 Map타입을 리턴하는 @ResponseBody 메소드의 경우 Jackson, Gson 등의 라이브러리를 검색
* 이 라이브러리를 이용하여 자동으로 해당 타입을, 반환 가능한 Json String으로 변환

# @ControllerAdvice

* Component scan 단계에서 자동으로 생성
* 각 클래스에서 발생할 수 있는 Exception들을 한군데로 모을 수 있음
* @ControllerAdvice(annotations = ClassName.class) 를 통해 ClassName Annotation이 붙은 클래스만 선택하여 Exception Handling을 할 수 있다.![Inline-image-2017-03-21 11.46.50.642.png](/files/1911391013745584377)