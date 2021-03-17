layout: article
aside:
  toc: true
title: Spring Cloud Alibaba
tags: ["Spring Cloud Alibaba"]

## 构建 Spring Cloud Alibaba 项目

[组件版本关系](https://github.com/alibaba/spring-cloud-alibaba/wiki/版本说明) 使用 Maven 构建，基础的 POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.simple.springcloud-alibaba-demo</groupId>
    <artifactId>springcloud-alibaba-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
  
    <packaging>pom</packaging>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.8.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.2.5.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```



## Nacos

 [nacos 下载地址](https://github.com/alibaba/nacos/tags)

单机启动 Nacos，启动成功访问 http://ip:8848/nacos，账户名密码：nacos

```bash
$ ./bin/startup.sh -m standalone
```



### 服务发现



添加 maven 依赖

```xml
<dependency>
  <groupId>com.alibaba.cloud</groupId>
  <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>

<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

添加 nacos 服务发现配置

```yaml
spring:
  application:
  	# 服务名称
    name: nacos-discovery-provider
  cloud:
    nacos:
      discovery:
      	# nacos 服务地址
        server-addr: 127.0.0.1:8848
```

在启动类加上 `@EnableDiscoveryClient` 注解

nacos 管理平台发现了对应的服务

![image-20210223235705459](https://i.loli.net/2021/02/23/iD1Ja2C3tUwVgAN.png)



### 负载均衡

