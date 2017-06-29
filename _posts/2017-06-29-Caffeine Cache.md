---
layout: post
title:  "[Spring] Caffeine Cache"
categories: [Spring, cache]
tags: [Spring boot, Caffeine Cache]
description: Caffeine Cache
---
# Spring Cache

Spring boot 에서는 다양한 캐시들을 지원한다. 기본적으로 아무 설정 하지 않았을 경우에는 ConcurrentMapCacheManager의 ConcurrentHashMap을 사용해서 캐시를 사용하고 JSR-107 (JCache) 명세에 따른 캐시도 지원한다. 그 구현체들은 EhCache, Hazelcast, Infinispan, apache ignite 등이 있다.(이 외에도 더 있던거 같았다.) 이외에도 JSR-107 표준 명세로는 따르지 않았지만 Redis, Caffeine, Guava등이 존재하고 있다. Caffeine 캐시 같은 경우에는 JCache도 지원한다.

## 장점

Caffeine Cache 사이트의 설명이다

[https://github.com/ben-manes/caffeine/wiki/Efficiency](https://github.com/ben-manes/caffeine/wiki/Efficiency)

```
Least Recently Used는 일반적인 시나리오에서 단순성과 적중률 때문에 인기있는 퇴거 정책입니다. 그러나 일반적인 작업 부하에서 LRU는 최적이 아니며 전체 검색과 같은 경우에는 적중률이 낮을 수 있습니다. 다음 절에서는 방대한 평가 후에 가장 좋은 대안을 비교합니다. 간단히하기 위해 O (1) 퇴거 정책의 상위 3 개만이 논의됩니다. 동시 구현에보다 적합하도록 O (n) 최악의 경우 시간 복잡성을 처리하는 클럭 기반 정책은 제외됩니다.

카페인은 히트 율이 높고 메모리 사용량이 적기 때문에 Window TinyLfu 정책을 사용합니다.

적응 형 대체 캐시(ARC)

ARC는 한 번 표시된 항목의 대기열, 여러 번 표시된 항목의 대기열 및 모니터링중인 퇴거 된 항목의 비 상주 대기열을 사용합니다. 대기열의 최대 크기는 작업로드 패턴과 캐시 유효성을 기반으로 동적으로 조정됩니다.

결론

Window TinyLfu는 거의 최적의 적중률을 제공하며 ARC 및 LIRS와 경쟁적입니다. 이는 간단하면서도 비거주 항목을 필요로하지 않으며 메모리 사용 공간이 적습니다. 이 정책은 다양한 작업 부하에서 LRU를 크게 향상시켜 범용 캐시에 적합한 선택입니다.
```

## pom.xml

``` xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<dependency>
    <groupId>com.github.ben-manes.caffeine</groupId>
    <artifactId>caffeine</artifactId>
</dependency>
```

## Configuration

``` java
@Configuration
public class CaffeineCacheConfiguration {

    @Bean
    public CacheManager cacheManager(Ticker ticker) {
        CaffeineCache tcAppCache = buildCache("tcAppCache", ticker, 24, TimeUnit.HOURS);
        CaffeineCache healthCache = buildCache("healthCache", ticker, 30, TimeUnit.SECONDS);

        SimpleCacheManager manager = new SimpleCacheManager();
        manager.setCaches(Arrays.asList(tcAppCache, healthCache));
        return manager;
    }

    private CaffeineCache buildCache(String name, Ticker ticker, long duration, TimeUnit unit) {
        return new CaffeineCache(name, Caffeine.newBuilder()
                .expireAfterWrite(duration, unit)
                .maximumSize(10000)
                .ticker(ticker)
                .build());
    }

    @Bean
    public Ticker ticker() {
        return Ticker.systemTicker();
    }
}
```

혹은 YAML로 설정해줘도 된다고 한다.

```
spring:
  cache:
    cache-names: timeCached
    caffeine:
      spec: expireAfterAccess=30s
```

## Use

``` java
@Cacheable(value="healthCache")
	public void updateDatabaseHealthStatus(String monitoringProfile) {
		if(	monitoringProfile.equals(MonitoringConstants.MonitoringProfile.System) ||
				monitoringProfile.equals(MonitoringConstants.MonitoringProfile.All)) 
			return;
		
		if(monitoringTable.getAllmonitoringConditions().get(monitoringProfile) == null) return;
		
		monitoringTable.getAllmonitoringConditions()
			.get(monitoringProfile) 
			.entrySet()
			.stream()
			.forEach(e -> {
				e.getValue().forEach(n -> {
					..........
				});
			});
	}
```

## 참고

[https://www.youtube.com/watch?v=3McRajvmdlw](https://www.youtube.com/watch?v=3McRajvmdlw)