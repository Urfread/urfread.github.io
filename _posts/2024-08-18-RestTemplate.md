---
layout: post
title: RestTemplate入门
summary: 简单了解下RestTemplate
date: 2024-08-18
tags: Spring   
---

> RestTemplate的用途，简化Java程序发送和处理HTTP。不过，它后边有了WebClient，了解一下先。

## 介绍

这次咱做一个服务端，再做一个客户端。

服务端有一个`/api/random`的接口，调用可以返回一个随机的名言

客户端启动的时候，自动发送访问服务端接口的请求，并把结果输出在命令行。

> 这得分两个模块编写吧，一个server，一个client。记着在每个模块都写上启动类和实体类。

## 先定义两个实体类

### `Quote.java`
```java
package com.example.demo.pojo;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown = true)
public record Quote(String type, Value value) { }
```

### `Value.java`
```java
package com.example.demo.pojo;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown = true)
public record Value(Long id, String quote) { }
```

## 服务端

### `QuoteController.java`

在这个控制器中处理`/api/random`的请求，返回一个随机的名言。

```java
package com.example.demo1;

import com.example.demo1.pojo.Quote;
import com.example.demo1.pojo.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.Random;
import java.util.concurrent.atomic.AtomicLong;

/**
 * Class Description: 这是处理/api/random请求的controller.
 *
 * @author urfread
 * @date 2024-08-18 10:43
 */
@RestController
public class QuoteController {
    private final AtomicLong counter = new AtomicLong();
    private static final String[] QUOTES = {
            "The only limit to our realization of tomorrow is our doubts of today.",
            "The purpose of our lives is to be happy.",
            "Life is what happens when you’re busy making other plans.",
            "Get busy living or get busy dying."
    };

    @GetMapping("/api/random")
    public Quote getRandomQuote() {
        Random random = new Random();
        int randomIndex = random.nextInt(QUOTES.length);
        return new Quote("", new Value(counter.incrementAndGet(), QUOTES[randomIndex]));
    }
    @GetMapping("/hello")
    public String hello(@RequestParam(value = "name", defaultValue = "World") String name) {
        return String.format("Hello %s!", name);
    }
}

```

服务端挺简单的，就不再多说了。

## 客户端
### `ConsumingRestApplication.java`
客户端代码挺新鲜的，注释已经写在里边了。
```java
package com.example.demo;

import com.example.demo.pojo.Quote; // 引入 Quote 类
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Profile;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
public class ConsumingRestApplication {

	// 创建日志记录器，用于记录应用的日志信息
	private static final Logger log = LoggerFactory.getLogger(ConsumingRestApplication.class);

	// 主方法，应用程序的入口点
	public static void main(String[] args) {
		// 启动 Spring Boot 应用
		SpringApplication.run(ConsumingRestApplication.class, args);
	}

	// 定义一个 RestTemplate Bean，用于发送 HTTP 请求
	@Bean
	public RestTemplate restTemplate(RestTemplateBuilder builder) {
		// 使用 RestTemplateBuilder 创建并配置 RestTemplate 实例
		return builder.build();
	}

	// 定义一个 CommandLineRunner Bean，这个 Bean 在应用启动后会被执行
	@Bean
	@Profile("!test") // 只有在非 "test" 配置文件中才会激活这个 Bean
	public CommandLineRunner run(RestTemplate restTemplate) throws Exception {
		// CommandLineRunner 的 run 方法在应用启动时会被调用
		return args -> {
			// 使用 RestTemplate 发送 GET 请求，获取来自指定 URL 的 Quote 对象
			Quote quote = restTemplate.getForObject(
					"http://localhost:8081/api/random", // 目标 URL
					Quote.class // 期望的响应类型
			);
			// 将获取到的 Quote 对象转换为字符串并记录到日志中
			log.info(quote.toString());
		};
	}
}

```

## 参考
[Consuming a RESTful Web Service](https://spring.io/guides/gs/consuming-rest)