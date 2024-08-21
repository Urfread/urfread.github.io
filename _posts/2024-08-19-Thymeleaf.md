---
layout: post
title: Theymeleaf入门
summary: 熟悉thymeleaf、拦截器、jpa和mybatis的使用
date: 2024-08-19
tags: Spring   
---

## 入门案例
### 引入依赖
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

### 编写配置文件
```properties
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
spring.thymeleaf.mode=HTML
spring.thymeleaf.cache=false
```

### 编写html文件
注意保存位置`src/main/resources/templates/greeting.html`
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Greeting</title>
</head>
<body>
<h1 th:text="${greeting.name}"></h1>
</body>
</html>
```

### 编写后端服务类
`Greeting.java`

```java
package com.example.demo;
public class Greeting {
    private String name;
    public Greeting() {
    }
    public Greeting(String name) {
        this.name = name;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```

`GreetingController.java`
```java
package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class GreetingController {

    @GetMapping("/greeting")
    public String greeting(Model model) {
        model.addAttribute("greeting", new Greeting("Hello World"));
        return "greeting";
    }
}
```

`DemoApplication.java`
```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}

}
```