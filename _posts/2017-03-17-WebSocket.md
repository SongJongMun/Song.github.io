---
layout: post
title:  "WebSocket #1"
categories: [js, websocket, browser]
tags: [js, websocket, browser]
description: 브라우저 WebSocket에 대해서 알아보자
---


# WebSocket

## 정의(Mozilla)

WebSocket은 ws 프로토콜을 기반으로 클라이언트와 서버 사이에 지속적인 완전 양방향 연결 스트림을 만들어 주는 기술입니다. 일반적인 웹소켓 클라이언트는 사용자의 브라우저일 것이지만, 그렇다고 해서 이 프로토콜이 플랫폼에 종속적이지는 않습니다.

## 기존의 브라우저 동작 방식

전형적인 브라우저 렌더링 방식은 HTTP 요청(HTTP Request)에 대한 HTTP 응답(HTTP Response)을 받아서 브라우저의 화면을 깨끗하게 지우고 받은 내용을 새로 표시하는 방식이다. 

## 웹 소켓을 이용한 브라우저 동작 방식

보다 쉽게 상호작용하는 웹 페이지를 만들려면 브라우저와 웹 서버 사이에 더 자유로운 양방향 메시지 송수신(bi-directional full-duplex communication)이 필요하다. 

그래서 HTML5 표준안의 일부로 WebSocket API(이후 WebSocket)가 등장했다.

## 그래서, 어떻게 이용하는데?

WebSocket은 다른 HTTP 요청과 마찬가지로 80번 포트를 통해 웹 서버에 연결한다. 

HTTP 프로토콜의 버전은 1.1이지만 다음 헤더의 예에서 볼 수 있듯이, Upgrade 헤더를 사용하여 웹 서버에 요청한다. 당연한 이야기겠지만 클라이언트인 브라우저와 마찬가지로 웹 서버도 WebSocket 기능을 지원해야한다.

```
GET /... HTTP/1.1  
Upgrade: WebSocket  
Connection: Upgrade
```

브라우저는 "Upgrade: WebSocket" 헤더 등과 함께 랜덤하게 생성한 키를 서버에 보낸다. 웹 서버는 이 키를 바탕으로 토큰을 생성한 후 브라우저에 돌려준다. 이런 과정으로 WebSocket 핸드쉐이킹이 이루어진다.

그 뒤 Protocol Overhead 방식으로 웹 서버와 브라우저가 데이터를 주고 받는다. Protocol Overhead 방식은 여러 TCP 커넥션을 생성하지 않고 하나의 80번 포트 TCP 커넥션을 이용하고, 별도의 헤더 등으로 논리적인 데이터 흐름 단위를 이용하여 여러 개의 커넥션을 맺는 효과를 내는 방식이다.

이런 방식을 사용하기 때문에 방화벽이 있는 환경에서도 무리 없이 WebSocket을 사용할 수 있다.

## 브라우저에서는?

WebSocket 생성자는 다음과 같이 이루어져 있다.

```javascript
WebSocket WebSocket(
  in DOMString url,
  in optional DOMString protocols
);
```

url는 WebSocket 서버가 응답할 URL로 `필수` Parameter이며 protocols는 서브 프로토콜들을 지정하는데 사용된다.

하나의 서버가 여러개의 WebSocket 서브 프로토콜을 구현할 수 있도록 해준다(한개의 URI에서 여러개의 기능을 담당), 않넣어도 상관없다.

만약 접속을 시도하고 있는 포트가 차단되었으면 `SECURITY_ERR`예외를 발생한다.(onerror 핸들러가 실행된 다음, onclose 핸들러가 실행된다.)



```javascript
if ('WebSocket' in window) {  
    var oSocket = new WebSocket(“ws://localhost:80”);

    oSocket.onmessage = function (e) { 
        console.log(e.data); 
    };

    oSocket.onopen = function (e) {
        console.log(“open”);
		oSocket..send("Here's some text that the server is urgently awaiting!"); 
    };

    oSocket.onclose = function (e) {
        console.log(“close”);
    };

    oSocket.send(“message”);
    oSocket.close();
}
```

WebSocket Object의 생성 후 readyState 값은 `CONNECT`이며, 연결이 수립되어 데이터가 전송 가능한 상태가 되면 `OPEN`으로 바뀐다.

### 혹은 다음과 같이 JSON을 사용하여 복잡한 데이터를 전송 / 수신할 수 있다.


```javascript
// Send text to all users through the server
function sendText() {
  // Construct a msg object containing the data the server needs to process the message from the chat client.
  var msg = {
    type: "message",
    text: document.getElementById("text").value,
    id:   clientID,
    date: Date.now()
  };

  // Send the msg object as a JSON-formatted string.
  exampleSocket.send(JSON.stringify(msg));
  
  // Blank the text input element, ready to receive the next line of text from the user.
  document.getElementById("text").value = "";
}
```

```javascript
exampleSocket.onmessage = function(event) {
  var f = document.getElementById("chatbox").contentDocument;
  var text = "";
  var msg = JSON.parse(event.data);
  var time = new Date(msg.date);
  var timeStr = time.toLocaleTimeString();
  
  switch(msg.type) {
    case "id":
      clientID = msg.id;
      setUsername();
      break;
    case "username":
      text = "<b>User <em>" + msg.name + "</em> signed in at " + timeStr + "</b><br>";
      break;
    case "message":
      text = "(" + timeStr + ") <b>" + msg.name + "</b>: " + msg.text + "<br>";
      break;
    case "rejectusername":
      text = "<b>Your username has been set to <em>" + msg.name + "</em> because the name you chose is in use.</b><br>"
      break;
    case "userlist":
      var ul = "";
      for (i=0; i < msg.users.length; i++) {
        ul += msg.users[i] + "<br>";
      }
      document.getElementById("userlistbox").innerHTML = ul;
      break;
  }
  
  if (text.length) {
    f.write(text);
    document.getElementById("chatbox").contentWindow.scrollByPages(1);
  }
};
```



WebSocket 프로토콜을 나타내는 ws://는 URI 스키마를 사용한다.
만약 https처럼 암호화 소켓을 사용하려면 wss://를 사용하면 된다.


위의 코드를 실행하면 먼저 new WebSocket() 메서드로 웹서버와 연결한다. 그리고 생성된 WebSocket 인스턴스를 이용하여 소켓에 연결할 때(onopen), 소켓 연결을 종료할 때(onclose), 메시지를 받았을 때(onmessage) 등의 이벤트를 각각 정의할 수 있다. 서버에 메시지를 보내고 싶을 때에는 send() 메서드를 이용한다. 


## 참고 사이트

[Mozilla-WebSocket](https://developer.mozilla.org/ko/docs/WebSockets/Writing_WebSocket_client_applications)

[Naver D2 Blog](http://d2.naver.com/helloworld/1336)

[Spring with WebSocket](http://blog.naver.com/PostView.nhn?blogId=beabeak&logNo=220471878778&parentCategoryNo=&categoryNo=86&viewDate=&isShowPopularPosts=true&from=search)

#부록 : 사용하는 방법