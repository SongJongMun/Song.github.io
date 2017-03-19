---
layout: post
title:  "Spring-boot??"
categories: [Spring, boot]
tags: [Spring, boot]
description: Spring-boot??
---


# SpringBoot?

스프링을 사용하기 쉽고, 편하게 이용할 수 있도록 하는 라이브러리라고 생각하면 된다.

## Spring Boot의 특징

1. war파일을 사용하지 않고 embed tomcat 또는 jetty 사용가능
2. Spring Boot에서 지원하는 stater POM으로 Maven을 간단하게 사용
3. Spring에 수많은 설정을 자동으로 설정(xml설정이 필요 없음), autoconfigure


## 설정?

### pom.xml

```xml
<parent>
    <groupid>org.springframework.boot</groupid>
    <artifactid>spring-boot-starter-parent</artifactid>
    <version>1.1.8.RELEASE</version>
</parent>
<dependencies>
    <dependency>
        <groupid>org.springframework.boot</groupid>
        <artifactid>spring-boot-starter-web</artifactid>
    </dependency>
</dependencies>
```

### @EnableAutoConfiguration

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
 
@ComponentScan
@EnableAutoConfiguration
public class Application {
 
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

### Controller

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
 
@Controller
@RequestMapping("/")
public class MainController {
 
    @RequestMapping
    public @ResponseBody String index() {
        return "Hello Woniper Spring Boot~";
    }
}
```

이렇게 구성을 하기만 해도 간단한 Spring 프로젝트를 만들 수 있다.


## 참조 사이트

[Spring Boot Docs](http://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-build-systems.html#using-boot-maven)

[Spring Boot Application Properties](http://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html)

[SlideShare](https://www.slideshare.net/madvirus/spring-boot-42817314)


