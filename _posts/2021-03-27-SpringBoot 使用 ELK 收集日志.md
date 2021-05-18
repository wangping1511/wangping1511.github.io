---
layout: article
title: SpringBoot 使用 ELK 收集日志
tags: ["SpringBoot", "日志", "Elastic Search", "Kibana", "Logstash"]
---

## 启动 ELK

启动 ES 和 Kibana

安装 logstash json_lines 插件

```
logstash-plugin install logstash-codec-json_lines
```

修改 logstash 的配置文件

```conf
input {
  tcp {
    host => "127.0.0.1"
    port => 8100
    mode => "server"
    tags => ["tags"]
    codec => json_lines
  }
}

output {
  elasticsearch {
    hosts => ["http://localhost:9200"]
    index => "springboot-log-%{+YYYY.MM.dd}"
  }
}
```

启动 logstash

## Springboot 集成

POM 引入

```xml
<dependency>
  <groupId>net.logstash.logback</groupId>
  <artifactId>logstash-logback-encoder</artifactId>
  <version>5.3</version>
</dependency>
```

添加 `logback-spring.xml` 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- 控制台输出 -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>%yellow(%d{yyyy-MM-dd HH:mm:ss.SSS}) [%thread] %highlight(%-5level) %green(%logger{50}) - %highlight(%msg) %n</pattern>
        </encoder>
    </appender>

    <!--输出到logstash的appender-->
    <appender name="LOGSTASH" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
        <!--可以访问的logstash日志收集端口-->
        <destination>127.0.0.1:8100</destination>
        <encoder charset="UTF-8" class="net.logstash.logback.encoder.LogstashEncoder"/>
    </appender>

    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
        <appender-ref ref="LOGSTASH"/>
    </root>
</configuration>
```

检查：http://127.0.0.1:5601

![image-20210329184050977](https://i.loli.net/2021/03/29/3upaAmYKvbHQNjC.png)

