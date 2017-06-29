---
layout: post
title:  "Spring boot에서 XML-Object를 Mapping 해보자"
categories: [Spring boot, xml, object, mapping]
tags: [Spring boot, xml, object, mapping]
description: Spring boot xml mapping object
---
모니터링 하는 서버 그룹의 정보를 저장해두고, 어떤 서버가 죽어있는지, 혹은 어떤 서버가 임시로 붙어있는지를 판단하기 위한 기능이 필요하다

그래서 기존 서버에 존재하는 리스트를 xml파일로 작성, spring boot에서 파싱하여 오브젝트로 관리하고 기존 정보와 zookeeper에서 받아온 정보를 비교하여 모니터링 페이지에 출력하려고 한다.

근데 XML 파일을 오브젝트로 매핑해주는게 참 쉽지가 않다....
Spring bean을 사용하지 않으니까(라기보다는 자동으로 설정해주니까)

# XML 데이터를 다룰 때 알아야 할 개념

## 기본 개념

```
...

Object/XML Mapping, or O/X mapping for short, is the act of converting an XML document to and from an object. This conversion process is also known as XML Marshalling, or XML Serialization. This chapter uses these terms interchangeably.

Within the field of O/X mapping, a marshaller is responsible for serializing an object (graph) to XML. In similar fashion, an unmarshaller deserializes the XML to an object graph. This XML can take the form of a DOM document, an input or output stream, or a SAX handler.

 ....
```

간단히 Object/XML 맵핑 또는 O/X 맵핑은 XML와 오브젝트간에 변환하는 행위이다.

이 변환 프로세스는 XML Marshalling 또는 XML Serialization이라고도 불린다.

O/X 매핑 필드 내에서, Marshaller는 객체(그래프)를 XML로 직렬화하는 역할을 담당합니다.

비슷한 방법으로, unmarshaller는 XML을 deserialize하여 객체 그래프로 만듭니다.

이 XML은 DOM 문서, 입력 또는 출력 스트림 또는 SAX 처리기의 형태를 취할 수 있습니다.

## 기본 개념(예외처리 부분)

```
Spring provides a conversion from exceptions from the underlying O/X mapping tool to its own exception hierarchy with the XmlMappingException as the root exception. As can be expected, these runtime exceptions wrap the original exception so no information is lost.
```

Spring은 O/X 매핑 도구의 예외를 포함한 변환을 제공한다. 해당 예외는 XmlMappingException을 루트 예외로 가지며,(~~뭔소리여~~) O/X 매핑 툴의 예외를 자체 예외 클래스로 변환한다.
이 런타임 예외는 원본정보(아마 XML이나 Object)를 손상시키지 않는다.

## Marshalling

특정 데이터를 XML 형태로 만드는 행위

## Unmarshalling

XML 데이터를 특정 데이터로 만드는 행위

## OXM

Object XML Mapping의 약자이다.
(Spring문서에서는 Object/XML Mapping 이라고 불리우며, O/X Mapping이라고 쓰여져 있다.)

XML 데이터의 객체 맵핑을 다루는 개념이라고 생각하면 되고,

Global Interface로 Marshaller 와 Unmarshaller를 가지고 있다.

## JAXB

Java Architecture for XML Binding의 약자이다.

Annotation을 통해 OXM을 쉽게 진행해 줄 수 있는 도구이다.

# JAXB를 이용해서 Unmarshalling을 진행해보자

~~Marshalling은 아직 안해봤기 때문에~~

## Unmarshal

우선 Unmarshalling을 진행하려면 해당 XML을 본뜬 Object를 만들어 놔야 한다.. 하지만 일단 큰 그림으로 실제 코드 상에서 어떻게 Unmarshalling을 진행하는지 살펴보자..

일단 Unmarshalling 코드는 다음과 같다.

``` java
JAXBContext
    .newInstance(GamebaseServer.class)
    .createUnmarshaller()
    .unmarshal(getFileFromClassPathResource(path));
```

매우 간단하다. JAXBContext를 생성하고 Mapping Object Class를 대상(GamebaseServer는 Mapping 대상 Object이다.)으로 Instance를 생성한 다음 Unmarshaller를 만들고.... unmarshal을 진행하면 된다!(참고로 unmarshal 메소드는 인자로 file 변수를 받는다.)

## Mapping Object

### Unmarshalling 호출 방법은 알겠는데 Object 설정은?

단순히 Annotation을 선언해 주기만 하면 알아서 매핑해준다.

### Annotation 종류

간단하게 몇개 쓰이는 정도만...

- - -

### @XmlAccessorType

XML 데이터를 어떤 방법으로 맵핑할지를 선언해줄 수 있는 annotaion으로 NONE을 사용할 시 XmlElement로 annotate된 객체만 맵핑한다.

Object내에 XmlElement로 선언된 객체가 아니면 대상에서 제외시킨다는 말

- - -

### @XmlRootElement

중첩 구조로 되어 있는 XML 데이터를 뽑아올 때 주로 Class내에서 Inner Class를 선언해서 사용하는데(이때 Inner Class 선언 시 반드시 Static Class로 선언해 줘야 한다.) 이런 Class에다가 붙이는 선언이다.

해당 클래스가 XML 특정 노드의 루트라는 것을 뜻한다.

예시
XML

``` xml
<servers>
    <server>
        <module>none</module>
        <hostname>901</hostname>
        <address>0.0.0.1</address>
        <spec>S100</spec>
    </server>

    <server>
        <module>none</module>
        <hostname>902</hostname>
        <address>0.0.0.2</address>
        <spec>S100</spec>
    </server>

    <server>
        <module>none</module>
        <hostname>903</hostname>
        <address>0.0.0.3</address>
        <spec>S100</spec>
    </server>
</servers>
```

JAVA

``` java
@ToString
@XmlRootElement
public static class server {
    private String module;
    private String hostname;
    private String address;
    private String spec;

    @XmlElement(name = "module")
    public String getModule() {
        return module;
    }

    public void setModule(String module) {
        this.module = module;
    }
    ......
```

코드에 Lombok을 이용하여 getter/setter를 명시하면 unmarshalling 수행 시 에러가 발생해서 직접 getter setter 함수를 작성하고 getter에만 XmlElement를 선언하였다.(name=module은 무시해도 좋을듯.. 걍 써놓은거라)

- - -

### @XmlElement

가장 기본적인 선언으로 변수에 사용하는 annotation이다. 해당 변수가 xml의 노드임을 뜻한다.(이름이 같다면 굳이 명시안해줘도 된다.)

``` xml
<phase>alpha</phase>
```

``` java

private String phase;

@XmlElement(name = "phase")
public String getPhase() {
    return phase;
}

public void setPhase(String phase) {
    this.phase = phase;
}
```

- - -

### @XmlElementWrapper

해당 변수가 특정 노드들을 감싸고 있는 Wrapper란 것을 명시해주는 annotaion 이다. 주로 List에서 쓰인다.

``` xml
<servers>
    <server>
        <module>none</module>
        <hostname>901</hostname>
        <address>0.0.0.1</address>
        <spec>S100</spec>
    </server>

    <server>
        <module>none</module>
        <hostname>902</hostname>
        <address>0.0.0.2</address>
        <spec>S100</spec>
    </server>

    <server>
        <module>none</module>
        <hostname>903</hostname>
        <address>0.0.0.3</address>
        <spec>S100</spec>
    </server>
</servers>
```

``` java

private List<server> servers;

    ...


@XmlElementWrapper(name = "servers")
@XmlElement(name = "server")
public List<server> getServers() {
    return servers;
}

public void setServers(List<server> servers) {
    this.servers = servers;
}
```

- - -

### @XmlAttribute

말 그대로 속성을 가져올 때 쓰는 선언이다.
근데 잘 안되더라

- - -

#### 또 다른 예시들...

샘플1)

XML 데이터 형태 :

``` xml
<response>
    <a>값</a>
</reponse>
```

자바 객체 :

``` java
@XmlRootElement(name = "response")
public class Response{
    private String a;
 
    public String getA() {
        return a;
    }
 
    public void setA(String a) {
        this.a= a;
    }
}
```

샘플2)
XML 데이터 형태 :

``` xml
<response>
<item>
<a>값</a>
</item>
<response>
```

자바 객체 :

``` java
@XmlRootElement(name = "response")
public class Response{
    private Item item;
 
    public Item getItem(){
        return item;
    }
 
    public void setItem(Item item){
        this.item = item;
    }
 
    @XmlRootElement(namme="item")
    public static class Item{
        private String a;
 
        public String getA() {
            return a;
        }
 
        public void setA(String a) {
            this.a= a;
        }
    }
}
```

샘플3)

``` xml
XML 데이터 형태 : 
<response>
<items>
<item>
<a>값</a>
</item>
...
<item>
<a>값</a>
</item>
<items>
<response>
```

자바 객체 :

``` java
@XmlRootElement(name = "response")
public class Response{
    private List<Item> items;

    @XmlElementWrapper(name="items")
    @XmlElement(name="item")
    public List<Item> getItems(){
        return items;
    }
 
    public void setItems(List<Item> items){
        this.items = items;
    }
 
    @XmlRootElement(namme="item")
    public static class Item{
        private String a;
 
        public String getA() {
            return a;
        }
 
        public void setA(String a) {
            this.a= a;
        }
    }
}
```

# 참고 사이트

[Marshalling XML Using O/X Mappers - Spring docs](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/oxm.html)<br>
<br>
[JAXB annotations for soa/ioc](http://www.caucho.com/resin-3.1/doc/jaxb-annotations.xtp)<br>
<br>
[JAXB annotations blog](http://vicki.tistory.com/21)