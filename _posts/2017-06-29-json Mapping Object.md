---
layout: post
title:  "json File과 Object를 매핑해보자!"
categories: [Json, Mapping]
tags: [Java, Spring boot, Object Mapping]
description: json Mapping To Object
---
겨우 XML -> Object 매핑을 구현했으나 팀원들의 의견이 JSON을 사용하는게 좀 더 나을 것같다고 했다.
~~열심히 구현한 xmlReader 클래스는 하늘나라로~~

그래서 이참에 Json -> Object 매핑을 구현해가면서 설명해 보려고 한다.

근데 Spring Boot로 만든 jar 파일이 서버에서 돌고 있을때 Resources 폴더 내에 또 다른 json 파일을 만들고 수정할 수 있나..?
왜 Project folder 바로 밑에 json 파일을 만들라고 하는지 아직 이해를 못했다.

그러면 본격적으로 JSON ->Object 매핑 과정을 알아보자

![ㅎ](data:image/jpeg;base64,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)

# 일단 java 코드부터...

## XML보다 더 간단하다!!

``` java

private ObjectMapper mapper = new ObjectMapper();

...

GamebaseServiceStatus gamebaseServiceStatus =
    mapper.readValue(
            new File(gamebaseServiceStatusJsonFilePath),
            GamebaseServiceStatus.class
        );
```

이렇게 하면 된다.

# Mapping Target Object(VO)는?

~~이건 XML Unmarshalling이 더 쉽다.~~

``` xml
phase : alpha
servers : {
    server : [
        {
            module : none
            hostname : 901
            address : 10.0.0.0
        },
        .....
        {
            module : lighthouse
            hostname : 902
            address : 10.0.0.0
        }
    ]
},
stage : {
    service : [
        {
            name : console
            hosts : {
                host : [
                    {
                        index : 0
                        port : 10090
                    },
                    .....
                    {
                        index : 3
                        port : 15090
                    }
                ]
            },
        .....
        {
            name : console
            hosts : {
                host : [
                    {
                        index : 0
                        port : 10090
                    },
                    .....
                    {
                        index : 3
                        port : 15090
                    }
                ]
            }
        }
    ]
},
sandbox : {
}
```

``` java
public class GamebaseServiceStatus {

	@JsonProperty(value = "phase")
	private String phase;

	private List<server> servers;

	private List<service> stage;

	private List<service> sandbox;

	private List<service> admin;

	public String getPhase() {
		return phase;
	}

	public void setPhase(String phase) {
		this.phase = phase;
	}

	public List<server> getServers() {
		return servers;
	}
	
	@JsonDeserialize(using = ServerObjectDeserializer.class)
	public void setServers(List<server> servers) {
		this.servers = servers;
	}

	public List<service> getStage() {
		return stage;
	}
	
	@JsonProperty(value = "stage")
	@JsonDeserialize(using = ServiceObjectDeserializer.class)
	public void setStage(List<service> stage) {
		this.stage = stage;
	}

	public List<service> getSandbox() {
		return sandbox;
	}

	@JsonProperty(value = "sandbox")
	@JsonDeserialize(using = ServiceObjectDeserializer.class)
	public void setSandbox(List<service> sandbox) {
		this.sandbox = sandbox;
	}

	public List<service> getAdmin() {
		return admin;
	}

	@JsonProperty(value = "admin")
	@JsonDeserialize(using = ServiceObjectDeserializer.class)
	public void setAdmin(List<service> admin) {
		this.admin = admin;
	}

	// sub components

	@ToString
	@JsonRootName(value = "server")
	public static class server{
		@JsonProperty(value = "module")
		private String module;
		@JsonProperty(value = "hostname")
		private String hostname;
		@JsonProperty(value = "address")
		private String address;
		@JsonProperty(value = "spec")
		private String spec;

		public String getModule() {
			return module;
		}

		public void setModule(String module) {
			this.module = module;
		}

		public String getHostname() {
			return hostname;
		}

		public void setHostname(String hostname) {
			this.hostname = hostname;
		}

		public String getAddress() {
			return address;
		}

		public void setAddress(String address) {
			this.address = address;
		}

		public String getSpec() {
			return spec;
		}

		public void setSpec(String spec) {
			this.spec = spec;
		}
	}

	@ToString
	@JsonRootName(value = "service")
	public static class service {
		@JsonProperty(value = "name")
		private String name;
		
//		@JsonProperty(value = "hosts")
		private List<host> hosts;

		public List<host> getHosts() {
			return hosts;
		}
		
		@JsonProperty(value = "hosts")
		@JsonDeserialize(using = HostObjectDeserializer.class)
		public void setHosts(List<host> service) {
			this.hosts = service;
		}

		public String getName() {
			return name;
		}

		public void setName(String name) {
			this.name = name;
		}
	}

	@ToString
	@JsonRootName(value = "host")
	public static class host {
		@JsonProperty(value = "index")
		private Integer index;
		@JsonProperty(value = "port")
		private Integer port;

		public Integer getIndex() {
			return index;
		}

		public void setIndex(Integer index) {
			this.index = index;
		}

		public Integer getPort() {
			return port;
		}

		public void setPort(Integer port) {
			this.port = port;
		}
	}
}

class ServiceObjectDeserializer extends JsonDeserializer<List<GamebaseServiceStatus.service>> {
	private static final String SERVICE = "service";
    private static final ObjectMapper mapper = new ObjectMapper();
    private static final CollectionType collectionType =
            TypeFactory
            .defaultInstance()
            .constructCollectionType(List.class, GamebaseServiceStatus.service.class);

    @Override
    public List<GamebaseServiceStatus.service> deserialize(JsonParser jsonParser, DeserializationContext deserializationContext)
            throws IOException, JsonProcessingException {

        ObjectNode objectNode = mapper.readTree(jsonParser);
        JsonNode nodeAuthors = objectNode.get(SERVICE);

        if (null == nodeAuthors                     // if no author node could be found
                || !nodeAuthors.isArray()           // or author node is not an array
                || !nodeAuthors.elements().hasNext())   // or author node doesn't contain any authors
            return null;

        return mapper.reader(collectionType).readValue(nodeAuthors);
    }
}

class ServerObjectDeserializer extends JsonDeserializer<List<GamebaseServiceStatus.server>> {
	private static final String SERVER = "server";
    private static final ObjectMapper mapper = new ObjectMapper();
    private static final CollectionType collectionType =
            TypeFactory
            .defaultInstance()
            .constructCollectionType(List.class, GamebaseServiceStatus.server.class);

    @Override
    public List<GamebaseServiceStatus.server> deserialize(JsonParser jsonParser, DeserializationContext deserializationContext)
            throws IOException, JsonProcessingException {

        ObjectNode objectNode = mapper.readTree(jsonParser);
        JsonNode nodeAuthors = objectNode.get(SERVER);

        if (null == nodeAuthors                     // if no author node could be found
                || !nodeAuthors.isArray()           // or author node is not an array
                || !nodeAuthors.elements().hasNext())   // or author node doesn't contain any authors
            return null;

        return mapper.reader(collectionType).readValue(nodeAuthors);
    }
}

class HostObjectDeserializer extends JsonDeserializer<List<GamebaseServiceStatus.host>> {
	private static final String HOST = "host";
    private static final ObjectMapper mapper = new ObjectMapper();
    private static final CollectionType collectionType =
            TypeFactory
            .defaultInstance()
            .constructCollectionType(List.class, GamebaseServiceStatus.host.class);

    @Override
    public List<GamebaseServiceStatus.host> deserialize(JsonParser jsonParser, DeserializationContext deserializationContext)
            throws IOException, JsonProcessingException {

        ObjectNode objectNode = mapper.readTree(jsonParser);
        JsonNode nodeAuthors = objectNode.get(HOST);

        if (null == nodeAuthors                     // if no author node could be found
                || !nodeAuthors.isArray()           // or author node is not an array
                || !nodeAuthors.elements().hasNext())   // or author node doesn't contain any authors
            return null;

        return mapper.reader(collectionType).readValue(nodeAuthors);
    }
}
```

`설명은 내일 써드릴게요`<br>
<br>
JAXB를 이용한 XML Unmarshalling에서는 몇 개의 Annotation만으로도 충분히 이해하면서 Mapping이 가능했다.
~~물론 XML의 구조와 POJO의 구조를 맞추기 위해서는 약간의 노가다가 필요하다~~

간단한 Json 구조의 경우 Mapping은 쉬운데... 이게 여러개의 Object가 중첩된 구조다 보니 Mapping을 위해 중첩된 Object별로 Deserializer를 더 구현해주고 이를 Annotation에서 이용해야 한다.(왜 그런지는 아직 이해 못함)

내가 작성한 Json의 경우 최대 Object - List\<Object> - Object - List\<Object> 까지 복잡한 구조를 가진다.
~~그러니 일찌감치 XML을 이용하겠다고 못박거나 H2 DB같은걸 처음부터 이용합시다~~

# XML -> Json으로 넘어오면서 느낀거...

XML은 여러개면 배열... 한개면 원소로 매핑 가능한데
JSON은 대괄호`[ ~ ]`를 넣어야 되서 불편함

JAXB를 이용해서 XML를 Mapping하는 것 처럼 JSON 매핑이 가능하다고는 하는데 별도의 라이브러리를 더 추가해야하고 아직 안해봐서 지금 올리긴 좀 그럼