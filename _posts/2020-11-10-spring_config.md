---
layout: post
title: "spring config"
comments: true
---

### spring config sever 설정값입니다.   

application.yml  
```yml
server:
  port: 9999

spring:
  cloud:
    config:
      server:
        git:
          uri: http://ip:port/xxx/config.git
```

dependencies 설정입니다. (gradle 기준)  
```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    implementation 'org.springframework.cloud:spring-cloud-config-server'
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
    }
}
```

어플리케이션은 아래와 같이 하나만 처리하시면됩니다.  

```java
@SpringBootApplication
@EnableConfigServer
public class SpringCloudConfigServerApplication {

  public static void main(String[] args) {
    SpringApplication.run(SpringCloudConfigServerApplication.class, args);
  }

}
```
어플리케이션 구동에 @EnableConfigServer 를 추가하시면 서버 구성이 완료 됩니다.   


### spring config client 설정값입니다.   

```yml
server:
  port: 8080

spring:
  profiles:
    active: dev

  cloud:
    config:
      uri: http://xxx:9999
  application:
    name: aaaa
```
올려진 서버 cofig 정볼르 바라봅니다.
해당 git 서버에 올라간 값이
aaaa-dev,
aaaa-prod 형태여야하며, application:name 이 없다면,
application 을 자동으로 찾도록 되어있습니다.   

```java
@Component
@ConfigurationProperties(prefix="xxxx")
public class XXXConfiguration {
  String message;

  public String getMessage() {
    return message;
  }

  public void setMessage(String message) {
    this.message = message;
  }
}
```  
configuration 을 정의하고, 해당값은 aaaa-prod 의 xxxx 란 속성 밑의 message라는 값을 가져오게 되어있습니다.  

dependencies는 다음과 같습니다.  
```gradle
    implementation('org.springframework.cloud:spring-cloud-starter-config')
    implementation('org.springframework.boot:spring-boot-starter-actuator')
```
해당 값이 서비스에 설정되어야지만 정상적으로 동작하며,
프로퍼티 리로드를 적용하기 위해서는,
actuator 를 설치한 후,

yml 파일에 추가합니다.
```yml
management:  #actuator
  endpoints:
    web:
      # base-path: / #default is /actuator prefix, can be modified here
      exposure:
        include: "refresh"
```
위와같이 설정하는 이유는 refresh 만 처리하기 위해서입니다.

```
curl -X POST xxx:port/actuator/refresh
```
시에 리플레쉬가 되는 것을 확인할 수 있습니다.  


예전에는 프로퍼티 리로드라는 기능을 직접 구현해본적이 있습니다.  

```java
private static void _loadPropertiesUsingCommonsConfiguration(Properties props, File file) throws ConfigurationException {
  final PropertiesConfiguration config = new PropertiesConfiguration();
  config.setDelimiterParsingDisabled(true);
  config.setFile(file);
  config.load();
  final Iterator<String> it = config.getKeys();
  while (it.hasNext()) {
    final String sKey = it.next();
    props.put(sKey, config.getString(sKey));
  }
}
```

로 우선 서비스가 시작할때, Properties 에 등록을 한 후,  
수정 사항이 있을 때마다,  
별도의 api가,

```java
PropertyLoader.getInstance().loadProperties(asFile);
```
해당 인스턴스의 프로퍼티를 리로드 하도록 구현한 것입니다.  
이 버전의 가장 큰 문제는,
각 서버마다 직접 들어가서 properties 파일을 수정해야한다는 문제가 있었지요.  

그게 번거로워, 해당 기능에 DB_CONFIG 라는 테이블을 만들어,  
1차는 DB에서, DB에 데이터가 없을시에는 프로퍼티로 가져오도록 구현하는 기능을 추가한 적이 있습니다.  

예전부터 spring config 에 대해서는 알고 있었으나,  
예전 레거시 소스로도 충분히 대응가능 적용해보지 않았지만,  
이번에 신규프로젝트 진행하면서 욕심을 내보았는데,  

역시 새로운 기술은 항상 즐거운거 같습니다!   

2020-11-12 추가 내용..
현재 서비스 적용중에 잠시 보류하게 되었습니다.  
처음 시작 시에 해당 config 값이 잘못되어있는 경우,
(ex : boolean 변수에 String 값 boolean a = "1234") 같은 값이 들어갈 경우, 서비스가 재시작이 안되게,  
```
  cloud:
    config:
      uri: http://xxxx:9999
      fail-fast: true
``` 

fail-fast : true를 주어서, 에러가 나게 적용했습니다.  
그러나 중간에 값을 변경후, 해당 클라이언트에 /actuator/refresh 할때
boolean 변수에 String 값을 강제로 넣을 경우, 적용은 되대, 해당 값을 get 할때 타입에러가 나는 문제가 생겼습니다.  

검증을 하기 위한 별도의 소스를 추가 개발을 하던가, 에러시 defalut 값이 나갈 수 있게 하여 전체 변수를 String으로 변경하여, 초기 에러가 안나게 막든지,
아니면 이럴 경우 에러가 난 후, 적용이 안되게하든지 다양한 방법을 찾아봐야할 것으로 보입니다.  
(기존 테스트 시에 에러가 나서 변경이 안되었던걸로 보이는데, 조금 더 테스트가 필요해보여, 실 적용은 우선 보류했습니다. Toy project로 올려야할듯..)