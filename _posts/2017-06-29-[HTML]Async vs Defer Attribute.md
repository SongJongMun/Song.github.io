---
layout: post
title:  "[HTML] async vs defer attributes"
categories: [HTML,js loading]
tags: [HTML,js loading, attribute, async, defer]
description: async vs defer
---
# Script Loading Type

![Legend](http://www.growingwiththeweb.com/images/2014/02/26/legend.svg)

## 일반적인 스크립트 태그

```
1. html 파싱(Parsing) 중, 스크립트 태그를 읽게 되면

2. 잠시 파싱을 멈추고 스크립트를 다운받아 실행한 후

3. 다시 파싱 작업을 이어간다.
```

![script](http://www.growingwiththeweb.com/images/2014/02/26/script.svg)

`html 태그의 앞부분에 스크립트 태그를 배치하게되면(특히나 헤드 태그 내에)`<br>
<br>
페이지 렌더링이 시작되기도 전에(페이지 렌더링은 body 태그의 시작과 함께 시작된다) 스크립트를 다운로드하고 실행하는데 시간을 할애하게 된다.

사용자의 입장에서 생각해봤을 때, 당신의 웹 페이지를 보는 사용자는 스크립트가 다운로드 되는 동안 텅빈 하얀 페이지를 보고만 있어야 한다.

물론 짧은 스크립트일 때는 이 시간은 극히 짧으니 그리 신경 쓸 필요가 없다,

그렇지만 만약에 인터넷 커넥션이 느리거나 사이즈가 큰 스크립트를 다운받는다면 아마 사용자는 긴 시간동안 페이지 로딩을 기다려야 할 것이다.

`이러한 문제들을 해결하기 위해서 HTML5 에 들어 async 속성과 defer 속성이 추가되었다.`

## Async 속성을 가진 스크립트 태그

```
1. HTML 파싱과 동시에 스크립트 다운로드가 진행

2. 스크립트 다운로드가 완료되는 순간 HTML 파싱을 잠시 멈추고 

3. 스크립트를 실행한다.
```

![Async Tag](http://www.growingwiththeweb.com/images/2014/02/26/script-async.svg)

이러한 특성 때문에 순서대로 다운로드가 시작된 스크립트라도 순서대로 실행되지는 않는다. 아래 예제를 보자

``` html
<html>
<head>
<script async src = "www.example.com/js/script1.js"%3E%3C/script%3E
<script async src = "www.example.com/js/script2.js"%3E%3C/script%3E
//script2이 script1 보다 먼져 실행될 수도 있다.
</head>
<body>
This is Body
Body End
</body>
</html>
```

이러한 이유로 DOM(Document Object Model)을 수정하는 스크립트 같은 경우에는 async 속성을 사용하지 않는게 좋다.

## Defer 속성을 가진 스크립트 태그

```
1. HTML 파싱과 동시에 스크립트 다운로드가 진행된다. 

2. 그러나 async 속성과는 달리 

3. HTML 파싱이 완료된(</html> 태그를 읽은 후) 후에야 스크립트가 실행된다.
```

![Defer Tag](http://www.growingwiththeweb.com/images/2014/02/26/script-defer.svg)

아래의 예제를 보자

``` html
<html>
<head>
<script defer src = "www.example.com/js/script1.js"%3E%3C/script%3E
<script defer src = "www.example.com/js/script2.js"%3E%3C/script%3E
//script1이 script2 보다 먼져 실행된다
</head>
<body>
This is Body
Body End
</body>
</html>
```

스크립트 태그가 헤드 태그 내에 위치해 있으나 `HTML 파싱을 멈추지 않고 바로 다운로드가 진행`된다.

그리고 `</html> 엔드 태그를 읽은 후에야 스크립트를 실행`하게 된다.

그리고 async 태그와는 달리 스크립트는 `HTML 문서 내에 위치한 순서대로 실행` 된다.

그러니 위의 예제에서는 html 문서 파싱이 완료된 후에 script1.js 가 실행되고 그 후에야 script2.js 가 실행된다.

async 속성이나 defer 속성이든 둘다 외부 스크립트 파일에만 적용이 가능한 속성이니 내부 스크립트에 쓰는 일은 없도록 하자

![Script Tag - Async, Defer](https://1.bp.blogspot.com/-t-davtzjn54/VY1qK40MmCI/AAAAAAAACD8/P6eZY2CpLWM/s1600/HTML_JavaScript_Script_Tag_Async_Defer_Attribute.jpg)

# 결론

페이지 로딩속도가 느림, 전체 로딩 속도가 느린것뿐만 아니라 스크립트 파일을 전체 실행할 때 까지 회색화면상태로 계속 기다려야됌
해결 방법 1. Minified Javascipt를 사용한다.
해결 방법 2. Async와 Defer를 사용하여 HTML 파싱하는 동안 JS 파일을 다운받는다.

Async을 무분별하게 사용하면 JS 파일간의 의존관계가 성립되지 않아 많은 버그를 만들어 낼 수 있다.

### Async -> 파싱하면서 다운, 다운 다 받으면 잠시 스크립트 실행

### Defer -> 파싱하면서 다운, 다운 다 받고 파싱이 다 끝나면 스크립트 실행

## 스크립트가 모듈화되어 있고 다른 스크립트에 의존하지 않는다. -> Async

## 스크립트가 다른 비동기 스크립트에 의존하거나 의존된다면(의존관계가 있는 모듈일 경우) -> Defer

## 스크립트가 작고 비동기 스트립트가 의존하는 경우 -> 비동기 스크립트 위에 속성없는 인라인 스크립트로