---
layout: post
title:  "TimeLimit을 포함한 URL - Json Get 하기"
categories: [Json, TimeLimit, URL]
tags: [Spring boot, Json, TimeLimit, URL]
description: TimeLimit With json Get
---
시스템 메소드를 이용하지 않고

# 기존의 방법 - No Time Limit

## Jackson ObjectMapper

``` java
private ObjectMapper mapper = new ObjectMapper();

...

mapper.readTree(new URL(url)).toString()
```

# TimeLimit옵션이 필요할 경우

## RestTemplete + HttpComponentsClientHttpRequestFactory

``` java
HttpComponentsClientHttpRequestFactory factory = new HttpComponentsClientHttpRequestFactory();
factory.setReadTimeout(1000 * 5); // 5 초
factory.setConnectTimeout(5000);
RestTemplate restTemplate = new RestTemplate(factory);

restTemplate.getForObject(url, String.class);
```

## AsyncHttpRequest(비동기 방법 + TimeLimit)

설정

```
gamebase-asyncHttp:
    connectTimeout: 5000
    requestTimeout: 10000
```

구현

``` java
Request request = HttpRestUtils.buildAsyncHttpRequest(HttpMethod.GET, url);
final Instant start = Instant.now();

...

asyncHttpClient.executeRequest(request, new AsyncCompletionHandler<Integer>() {
    @Override
    public Integer onCompleted(Response response) throws IOException {
        long lapTime = Duration.between(start, Instant.now()).toMillis();
        if (lapTime > 100)
            log.warn("Time taken:{} ms, url:{}", lapTime, url);

        Map<String, Number> metrics = objectMapper.readValue(response.getResponseBodyAsStream()
                , new TypeReference<Map<String, Number>>() {});
        if (log.isTraceEnabled()) log.trace("{}", metrics);

        writeMetrics(metrics, node.getHostname(), node.getTypeCode());

        return response.getStatusCode();
    }

    @Override
    public void onThrowable(Throwable cause) {
        long lapTime = Duration.between(start, Instant.now()).toMillis();
        log.error("Time taken:{} ms, url:{}, {}", lapTime, url, cause.getMessage());
    }
});
```

## AsyncHttpClient + Future ( 동기화 방법 + Time Limit )

``` java
private final AsyncHttpClient asyncHttpClient;

...

Future<Response> f = asyncHttpClient.prepareGet("http://" + v.getAddress() + "/apache-health").execute();

try {
    Response response = f.get();
    if (response.getStatusCode() == 200 && isResponsePageValid(response))
        alive = true;
} catch (Exception e) {
    log.debug("Apache Server Ping Error [{}]", e.getMessage());
}
```

### 혹은

``` java
Future<Response> f = asyncHttpClient.prepareGet(url).setRequestTimeout(800).execute();

try {
    Response response = f.get();
    if (response.getStatusCode() == 200)
        return response.getResponseBody();
    else
        return "";
} catch (Exception e) {
    return "";
}
```