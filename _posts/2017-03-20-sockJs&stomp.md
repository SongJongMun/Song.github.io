---
layout: post
title:  "STOMP & SockJS 개요"
categories: [STOMP, SockJS]
tags: [STOMP, SockJS, protocol, js, websocket]
description: STOMP & SockJS 개요
---

# STOMP

STOMP은 Simple Text를 지향하는 Messaging Protocol이다.

STOMP는 텍스트를 전달할 때 다음과 같은 종류의 프레임을 가진다.

## Frame & Headers
1. CONNECT or STOMP(content 포함 가능)
<br> REQUIRED: accept-version, host
<br> OPTIONAL: login, passcode, heart-beat

2. CONNECTED
<br> REQUIRED: version
<br> OPTIONAL: session, server, heart-beat

3. SEND
<br> REQUIRED: destination
<br> OPTIONAL: transaction

4. SUBSCRIBE
<br> REQUIRED: destination, id
<br> OPTIONAL: ack

5. UNSUBSCRIBE
<br> REQUIRED: id
<br> OPTIONAL: none

6. ACK or NACK
<br> REQUIRED: id
<br> OPTIONAL: transaction

7. BEGIN or COMMIT or ABORT
<br> REQUIRED: transaction
<br> OPTIONAL: none

8. DISCONNECT
<br> REQUIRED: none
<br> OPTIONAL: receipt

9. MESSAGE(content 포함 가능)
<br> REQUIRED: destination, message-id, subscription
<br> OPTIONAL: ack

10. RECEIPT
<br> REQUIRED: receipt-id
<br> OPTIONAL: none

11. ERROR(content 포함 가능)
<br> REQUIRED: none
<br> OPTIONAL: message

## 그렇다면 Header와 Body는 어떻게 나누어지나.?

```
SEND
destination:/queue/a
receipt:message-12345

hello queue a^@
```
다음과 같이 빈줄로 나뉘어진타

프로토콜 프레임에 특이한 점이 있는데 하나는 Body의 끝이 ^@로 끝나며, 

또 하나는 각 frame의 command 다음에 해당 command의 속성이 key-value 값으로 한 줄씩 들어가 있다는 점이다.

STOMP의 자세한 사용법은 다음 페이지에 작성되어 있다.
[stomp.github.io](http://stomp.github.io/stomp-specification-1.2.html#STOMP_Frames)

## Client에서는 어떻게 쓰나?
다음 코드는 SockJS, AngluarJS, STOMP를 사용한 javascipt 코드이다. 참고하여 쓰도록 하자

```javascript
$scope.client;
$scope.name = '';
$scope.message = '';

$scope.init = function() {
  var socket = new SockJS('http://localhost:8080/endpoint'); //SockJS endpoint를 이용
  $scope.client = Stomp.over(socket); //Stomp client 구성
  $scope.client.connect({}, function(frame) {
    console.log('connected stomp over sockjs');
    // subscribe message
    $scope.client.subscribe('/subscribe/echo', function(message) {
      console.log('receive subscribe');
      console.log(message);
    });
  });
};

// send message
$scope.send = function() {
  var data = {
    name: $scope.name,
    message: $scope.message
  };
  $scope.client.send('/app/echo', {}, JSON.stringify(data));
};
```

## Server에서는 어떻게 쓰나?

[Spring-4x에서의-WebSocket-SockJS-STOMP](http://netframework.tistory.com/entry/Spring-4x%EC%97%90%EC%84%9C%EC%9D%98-WebSocket-SockJS-STOMP)

## 참고로...

STOMP는 기본적으로 subscribe를 통해서 데이터를 전달받는다. 그래서 수신한 메시지가 어떤 메소드로 송신되었는지 명확히 알 수 있다.

---

# Sock JS

Sock JS는 WebSocket의 확장판으로, 지원범위가 넓지 않은(IE 10 이상부터 지원) 브라우저에서 websocket과 동일한 기능을 제공해주기 위한 방법 중 하나이다.

기본적으로 WebSocket과 인터페이스가 동일하며(내부적으로 WebSocket을 이용하니까..)다음과 같은 차이점을 가진다.

1. ws, wss가 아닌 http, https
2. browser에서 기본으로 제공하지 않음, external Library
3. IE 6 버전 이상부터 지원함
4. Server도 SockJS 서버 Library를 사용해야함

#### 서버쪽에서도 WebSocket을 지원하는방법 그대로 써주기만 하면 된다. EndPoint 설정만 다음과 같이 해주면 된다!

 registry.addEndpoint("/endpoint")**.withSockJS()**;

### WebSocket을 지원하지 않는 브라우저에서 SockJS가 해주는 방식들은?

1. polling<br>
![Polling](http://2.bp.blogspot.com/-cvWY81etsao/ViZSUVxywxI/AAAAAAAAMHo/wxrd6dIntM8/s320/HttpPolling.gif)<br>
클라이언트가 서버로 http Request를 계속 날려 데이터를 받아오는 방식이다. 통신에 부담이 크며, 빠른 응답을 기대하기도 어렵다.

2. long polling<br>
![LongPolling](http://2.bp.blogspot.com/-eL9rxi8th2A/ViZSW0ggEwI/AAAAAAAAMH4/k4S4-dRz3t4/s320/HttpLongPolling.gif)<br>
클라이언트에서 일단 http request를 날린 다음 계속 기다리다가 서버에서 클라이언트로 전달할 이벤트가 있다면 그 순간 response 메시지를 전달하면서 연결이 종료된다.

3. streaming<br>
![Streaming](http://4.bp.blogspot.com/-sRVlAdeU-Kw/ViZSWw-wB2I/AAAAAAAAMH0/3CmKGISDV-A/s320/HttpStreaming.gif)<br>
클라이언트에서 서버로 일단 request를 날리고, 이벤트를 요청할 때도 해당요청을 안끊고, 필요한 메시지들만 공유한다.(flush 방식)






