---
layout: post
title: RestTemplate入门2
date: 2024-08-23
tags: [Spring,微服务]
---

# 怎么启动两个spring服务的时候，让一个服务调用另一个服务？
## 第一步 编写被服务调用的服务
先写好要被调用的服务，挺简单的，补充一点：
改服务端口的方法（yml格式）
```yml
server:
    port:8081
```

一会儿咱访问这个服务上的路径，应该是`http://localhost:8081/**`

## 第二步 编写被外部调用的服务
### 添加一个配置
```java
package com.example.orderservice;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class AppConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
``` 

### 编写调用另一个服务的逻辑
```java
package com.example.orderservice;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;
@RestController
@RequestMapping("/test")
public class TestController {

    private final RestTemplate restTemplate;
    String baseUrl = "http://localhost:8081";

    @Autowired
    public TestController(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }

    @GetMapping("/hello")
    public String hello() {
        String url = baseUrl+"/test/hello";
        return restTemplate.getForObject(url, String.class);
    }
}
```

## 要点详解:
RestTemplate 是 Spring Framework 提供的一个用于同步 HTTP 请求的工具类。它简化了发送和接收 HTTP 请求的过程，并处理了底层的网络细节。RestTemplate 在 Spring 5 中被 WebClient 所取代，但仍在许多旧版本的 Spring 应用中广泛使用。

- 主要功能
  - 简化 HTTP 请求: 提供了简单的 API 来发送 GET、POST、PUT、DELETE 等 HTTP 请求，并处理响应。

  - 自动处理响应: 将 HTTP 响应体自动转换为所需的 Java 对象，如 String、List、Map 或自定义类。

  - 支持多种请求类型: 支持发送和接收各种类型的请求和响应，例如 JSON、XML 等。

  - 异常处理: 内置了异常处理机制，用于处理 HTTP 错误状态码和其他通信问题。

- 常用方法
  - getForObject(String url, Class<T> responseType): 发送 GET 请求并将响应体转换为指定类型的对象。

  - postForObject(String url, Object request, Class<T> responseType): 发送 POST 请求，将请求体和 URL 发送到服务器，并将响应体转换为指定类型的对象。

  - put(String url, Object request): 发送 PUT 请求，将请求体发送到服务器。

  - delete(String url): 发送 DELETE 请求以删除资源。
  