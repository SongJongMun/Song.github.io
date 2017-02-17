---
layout: post
title:  "Apache, Tomcat 설정"
desc: "Apache, Tomcat"
keywords: "Apache, Tomcat"
date:   2017-02-16
categories: [Study]
tags: [study]
icon: fa-bookmark-o
---

Apache, Tomcat, Maven

---



# Apache 설치 후 세팅

CentOS에서는 맨 처음 Apache와 Tomcat을 설정하면 ipTable이 자동으로 설정되어있다.

따라서, 톰캣을 이용한 웹 서비스 제공 시 ipTable에서 막아놓은 포트(80)에 대한 Proxy 설정을 해줘야 한다.

>
> Proxy란 ?
>
> 대리인(Agent)
> 혹은 웹 클라이언트의 요청 URL를 해당 서버가 아닌 프록시 서버로 요청 
>
> Client가 80번 포트로 보내는 요청을 직접 받아 처리할수가 없음 -> 따라서 Apache 서버가 80포트의 요청을 다른 포트로 향하는 요청으로 바꿔준다. 
> Tomcat은 지정된 포트를 유지하면서 운용하면 되고, Apache에서는 80 포트로 들어오는 모든 요청을 Tocmat의 포트에 맞춘 요청으로 전향시켜준다.
>

## Apache 설정을 하는 과정은 다음과 같다.

APACHE/conf/httpd.conf 파일을 불러온다.

1. 80번째줄과 83번째 줄의 주석문을 지운다.

```
 80 : LoadModule proxy_module modules/mod_proxy.so

 83 : LoadModule proxy_http_module modules/mod_proxy_http.so
```

위 2개의 모듈은 다음과 같은 기능을 한다. 

---

`proxy_module modules/mod_proxy.so` : 
> mod_proxy and related modules implement a proxy/gateway for Apache HTTP Server, supporting a number of popular protocols as well as several different load balancing algorithms.
> proxy_http_module을 import 하기 위해 필요한 기본 프록시 모듈이다.
> 

`proxy_http_module modules/mod_proxy_http.so` : 
> This module requires the service of mod_proxy. It provides the features used for proxying HTTP and HTTPS requests. mod_proxy_http supports HTTP/0.9, HTTP/1.0 and HTTP/1.1. It does not provide any caching abilities.
> http 요청을 확인하고 각 포트에 맞는 포워딩 설정을 하는 모듈

---
2. VirtualHost를 설정한다.

```xml

<VirtualHost *:80>
    ServerName gowith.nhnent.com
    ServerAlias gowith.nhnent.com

    ProxyPass / http://127.0.0.1:9001/
    ProxyPassReverse / http://127.0.0.1:9001/

    ErrorLog "| /home1/irteam/apps/apache/bin/rotatelogs -l  /home1/irteam/logs/apache/error.log.%Y%m%d 8640    0"
    CustomLog "| /home1/irteam/apps/apache/bin/rotatelogs -l /home1/irteam/logs/apache/access.log.%Y%m%d 864    00"     combined env=!nolog-request
</VirtualHost>

```

80포트로 요청하는 모든 메시지를 xml 설정에 정의한 프록시로 가도록 해준다.

---

# Tomcat

## Server.xml 설정

`tomcat/conf/server.xml` 파일에서는 다음과 같은 설정으로 지정된 모든 포트에 대해 전향한다.
예 .) 

```xml

 <Connector port="9001" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />

 <Engine name="Catalina" defaultHost="localhost">
	<Host name="localhost"  appBase="webapps"
		deployOnStartup="true"
		unpackWARs="true" autoDeploy="true"
		xmlValidation="false" xmlNamespaceAware="false">
	   <Context path="/" docBase="/home1/irteam/deploy" reloadable="true"/>
	</Host>
 </Engine>

 ```
 
 Engine안의 내용은 9001포트(코드에 있는) 요청을 서비스하기 위한 엔진이다.
 
Apache에서 설정한 poot를 대상으로 배포하도록 설정한다.
 
 ## Tomcat 오류 찾기
 
 만약 Tomcat 실행과 동작 도중에 Error가 나온다면 Tomcat/logs/catalina.out 파일을 보자