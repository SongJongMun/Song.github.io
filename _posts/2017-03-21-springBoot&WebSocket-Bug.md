---
layout: post
title:  "Spring boot & WebSocket 삽질"
categories: [Spring boot, WebSocket, SockJS]
tags: [spring, websocket, sockjs, settings]
description: Spring-Boot에서 WebSocket 쓰기
---


#### 원래는 Spring Boot에서 WebSocket과 MVC Model을 둘 다 지원할 수 있도록 간단한 프로젝트를 만들어보려 했었다.


---

버그 : 인터넷에서 가이드를 보고 따라했는데, JSP 페이지가 반환되지 않고, 반환문자열(ResponseBody)부분만 페이지에 나타나게 된다.

원인 : WebSocket 소스코드를 무분별하게 사용한 것이 원인

문제의 소스코드
``` java
@Configuration
@EnableWebSocket
@EnableWebMvc
@ComponentScan
public class ControllerConfiguration extends WebMvcConfigurerAdapter implements WebSocketConfigurer {
    @Autowired
    private EchoHandler echoHandler;

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry.addHandler(echoHandler, "/echo").withSockJS();
    }
}
```

Annotation 부분의
@EnableWebMvc 부분이 문제가 되었다. 이 부분을 제거하니 정상적으로 jsp 페이지가 반환되었다.