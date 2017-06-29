---
layout: post
title:  "Stomp Setting with Spring Boot"
categories: [Spring Boot, Stomp, SockJS]
tags: [Spring Boot, Stomp, SockJS]
description: Stomp Setting with Spring Boot
---

# Initialize

```java
@Configuration
@AllArgsConstructor
@EnableWebSocketMessageBroker
public class StompConfigure extends AbstractWebSocketMessageBrokerConfigurer {

	private final StompProperties stompProperties;

	@Override
	public void registerStompEndpoints(StompEndpointRegistry registry) {
		registry.addEndpoint(stompProperties.getEndpointPath()).setAllowedOrigins("*").withSockJS();
		registry.addEndpoint(stompProperties.getFilterPath()).setAllowedOrigins("*").withSockJS();
	}

	@Override
	public void configureMessageBroker(MessageBrokerRegistry registry) {
		//base : /monitoring
		registry.setApplicationDestinationPrefixes(stompProperties.getMonitoringPath());
		registry.enableSimpleBroker(stompProperties.getMonitoringPath());
	}
}
```

# Using(Server)
```java
// Client가 구독 요청 시 응답하는 Methods
// ex. /subscribe/all
@SubscribeMapping(value = "${stomp.config.path.subscribe.subscribeShoot}")
public StompMonitoringMessage sendSandboxMonitoringNodes() {
	return updateHealthStatusAndGetMonitoringMessage(MonitoringProfile.Sandbox);
}

// 구독 요청과는 별개로 Message 수신 시 반응하는 메소드
// ex. /sub
@MessageMapping(value = "${stomp.config.path.messageShoot}")
public void updateFilter(MonitoringFilter.filter filter) {
	log.debug("receive filter update message from client : {}", filter);
	monitoringService.updateFilter(filter);
}

// 혹은 이렇게 다양하게 받을 수 있다.
@SubscribeMapping(value = "${stomp.config.path.chartShooting}/{databsae}/{typecode}")
public Object requestChartData(@DestinationVariable String databsae, @DestinationVariable String typecode){
	return influxdbService.getApiRequestAndTotalReponse(databsae, typecode);
}
```
# Using(Client)

```javascript

ctrl.initStomp = function() {
	var socketAddress = "http://" + window.location.hostname + ":" + stompContext.monitoringServerPort + stompContext.endPointPath;
	var socket = new SockJS(socketAddress);
	client = Stomp.over(socket);
	chartClient = Stomp.over(new SockJS(socketAddress));
	chartClient.connect();
}

// ex. monitoring/subscribe/all
ctrl.connectAndSubscribe = function(channel){
	client.connect({}, function(frame) {
		client.subscribe(ContextHandler.getSubscribePathByChannel(channel), function(message){
			
			.......
			.......
			.......
			
		});
	});
}

chartClient.subscribe("/monitoring/chart/" + module + "/" + currentProfiles, function(message){
	
	.........
	.........
	
	chartClient.unsubscribe();
});

//switch Subscribe Channel
$scope.changeSubscribe = function(channel){
	
	.....
	
	client.disconnect(function() {
		ctrl.init();
		
		ctrl.connectAndSubscribe(channel);
	});	
}

// shooting message
client.send(stompContext.messageShootingPath, {priority:9}, JSON.stringify(message));
```

	