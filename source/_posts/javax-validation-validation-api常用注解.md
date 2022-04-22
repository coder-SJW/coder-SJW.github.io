---
title: '''javax.validation:validation-api常用注解'''
date: 2022-02-25 16:11:35
tags:
  - 常用注解
categories: java
cover: https://gimg2.baidu.com/image_search/src=http%3A%2F%2Finews.gtimg.com%2Fnewsapp_bt%2F0%2F13706307053%2F1000.jpg&refer=http%3A%2F%2Finews.gtimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1648370848&t=0f25b6de1146b5270cd0e2d68f6a6766
top_img: https://gimg2.baidu.com/image_search/src=http%3A%2F%2Finews.gtimg.com%2Fnewsapp_bt%2F0%2F13706307053%2F1000.jpg&refer=http%3A%2F%2Finews.gtimg.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1648370848&t=0f25b6de1146b5270cd0e2d68f6a6766
---

# 一、概述

在我们应用程序的业务逻辑中，经常会碰到参数校验的情况，手动的在代码层上面进行校验就会带来很不好的体验，阅读、维护的成本会大大增加，造成冗余。因此有了这个JSR 303。

Bean Validation为JavaBean提供了相应的API来给我们做参数的验证。通过Bean Validation比如@NotNull @Pattern等方法来对我们字段的值做进一步的教研。

Bean Validation 是一个运行时框架，在验证之后错误信息会直接返回。

完整的jar包：
javax.validation:validation-api:2.0.1.Final.jar 包

> 注意：SpringBoot2 项目web组件已经内置了这个依赖了

```xml
<!--添加依赖-->
<dependency>
 <groupId>javax.validation</groupId>
 <artifactId>validation-api</artifactId>
 <version>2.0.1.Final</version>
</dependency>
```



# 二、常用验证注解

## @NotEmpty

 不能为null，而且长度必须大于0（一般注解在**集合**属性上）

![image-20220225162411902](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220225162411902.png)

## @NotBlank

@NotBlank**只用于String**,不能为null且trim()之后size>0

![image-20220225162425290](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220225162425290.png)

## @NotNull

不能为null，但可以为empty；用在基本类型上

![image-20220225162453346](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220225162453346.png)

> 例子：

```java
import lombok.Data;
import org.hibernate.validator.constraints.Length;

import javax.validation.constraints.*;
import java.io.Serializable;
import java.util.Date;

@Data
public class Test implements Serializable {

    private static final long serialVersionUID = 1L;

    
    @NotNull(message = "用户id不能为空")
    private Long userId;

    @NotBlank(message = "studentId不能为空")
    private String studentId;

    @NotEmpty(message = "课程id不能为空")
    private List<String> courseIdS;


}
```

# 三、@Valid和@Validated的区分

## 1、@Valid

> @Valid注解用于校验，所属包为：javax.validation.Valid。

①、首先需要在实体类的相应字段上添加用于充当校验条件的注解，如：@Min,如下代码（age属于Girl类中的属性）：如上图

②、其次在controller层的方法的要校验的参数上添加@Valid注解，并且需要传入BindingResult对象，用于获取校验失败情况下的反馈信息，如下代码：

```java
@PostMapping("/girls")  
public Girl addGirl(@Valid Girl girl, BindingResult bindingResult) {  
    if(bindingResult.hasErrors()){  
        System.out.println(bindingResult.getFieldError().getDefaultMessage());  
        return null;  
    }  
    return girlResposity.save(girl);  
}  
```

> bindingResult.getFieldError.getDefaultMessage()用于获取相应字段上添加的message中的内容

## 2、@Validated

> @Valid是javax.validation里的。
>
> @Validated是@Valid 的一次封装，**是Spring提供的校验机制使用**，所属包: org.springframework.validation.annotation.Validated。



# 四、使用例子

未完待续...
