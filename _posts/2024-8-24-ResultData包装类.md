---
layout: post
title: 编写一个枚举类
date: 2024-08-24
tags: Java、Spring  
---
## `ResultData.java`
```java
package com.example.common.model;

import lombok.Data;
import lombok.experimental.Accessors;

/**
 * Class Description: 
 * This class represents a standardized response structure that can be used to return results
 * from various operations within the application. It includes a code, message, data, and timestamp.
 * The class utilizes Lombok annotations for boilerplate code reduction and supports chained method calls.
 *
 * @param <T> the type of data that will be returned in the response
 *
 * @author urfread
 * @date 2024-08-24 08:23
 */
@Data
@Accessors(chain = true)
public class ResultData<T> {
    private String code;
    private String message;
    private T data;
    private Long timestamp;

    public ResultData() {
        this.timestamp = System.currentTimeMillis();
    }

    /**
     * Creates a successful ResultData instance with the given data.
     *
     * @param data the data to include in the response
     * @param <T> the type of data
     * @return a ResultData instance with a success code and message
     */
    public static <T> ResultData<T> success(T data) {
        return new ResultData<T>()
                .setCode(ResultCodeEnum.RC_200_SUCCESS.getCode())
                .setMessage(ResultCodeEnum.RC_200_SUCCESS.getMessage())
                .setData(data)
                .setTimestamp(System.currentTimeMillis());
    }

    /**
     * Creates a failure ResultData instance using a predefined ResultCodeEnum.
     *
     * @param resultCode the ResultCodeEnum to use for setting the code and message
     * @param <T> the type of data (typically null in a failure case)
     * @return a ResultData instance with a failure code and message
     */
    public static <T> ResultData<T> failure(ResultCodeEnum resultCode,String message) {
        return new ResultData<T>()
                .setCode(resultCode.getCode())
                .setMessage(message)
                .setTimestamp(System.currentTimeMillis());
    }
    public static void main(String[] args) {
        System.out.println(ResultData.success("success"));
        System.out.println(ResultData.failure(ResultCodeEnum.RC_400_FAIL,null));
    }
}

```

## 应用案例
```java
// 请求路径...
public ResultData<String> hello(){
    return ResultData.success("Hello World");
}
```