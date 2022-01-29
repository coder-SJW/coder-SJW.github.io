---
title: springboot分页
date: 2021-12-31 01:51:35
tags: 
 - springboot
 - 分页
categories: SpringBoot
cover: https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210126151605884.png
# SpringBoot分页
---

---



### 一、分页插件（pagehelper）

​		之前写项目时都是自己去写分页，头大！自己写的分页很可能要写两次查询，一次查内容，二次查条数。这种就是费力不讨好的事，所以去看了一下pagehelper分页插件（都说是最方便使用的分页插件），学完随便做个笔记。

​		**pagehelper插件主要是把PageHelper.startPage(pageNum, pageSize)放在sql查询之前。他会去处理查询内容，进行分页处理。**

##### 1、导包

```xml
<!-- pagehelper -->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>1.2.5</version>
        </dependency>
```

##### 2、分页请求类

​		**用于接收请求信息**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class PageRequest {
    //当前页码
    private int pageNum;
    //每页数量
    private int pageSize;
    //查询条件
    private Map<String,Object> params = null;
}
```

##### 3、例子

1. 控制层

   ​		这里就是接收前端的json数据将其映射成pageRequest对象。

   ```java
   @Controller
   public class QuestionController {
       @Resource
       QuestionService questionService;
       @PostMapping("/selectQuestions")
       @ResponseBody
       @ApiOperation("分页查询题库")
       public PageInfo<Question> selectQuestions(@RequestBody PageRequest pageRequest){
           return questionService.selectQuestions(pageRequest);
       }
   }
   ```

2. 业务层

   ​		在service层中pagehelper插件会去实现分页效果。

   ​		**主要语句：PageHelper.startPage(pageNum, pageSize)。它要放在查询sql的前面就对该查询结果有效。**

   ​		PageInfo：是该插件提供的一个返回类，可以封装查询的分页信息。

   ```java
   @Service
   public class QuestionServiceImpl implements QuestionService {
       @Resource
       QuestionDao questionDao;
       @Override
       /**
        * 调用分页插件完成分页
        */
       public PageInfo<Question> selectQuestions(PageRequest pageRequest) {
           int pageNum = pageRequest.getPageNum();
           int pageSize = pageRequest.getPageSize();
           PageHelper.startPage(pageNum, pageSize);
           List<Question> questionList = questionDao.selectQuestions(pageRequest.getParams());
           return new PageInfo<Question>(questionList);
       }
   }
   ```

3. dao层

   ```java
   public interface QuestionDao {
       /**
        * 分页查询试题
        * @param params：查询条件
        */
       List<Question> selectQuestions(@Param("params") Map<String,Object> params);
   }
   ```

   ​		mapper.xml中用<foreach>语句拼接查询条件

   ```xml
   <!DOCTYPE mapper
           PUBLIC "-//mybati.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <!--${}不带'',#{}自带引号-->
   <mapper namespace="com.sjw.zxks.dao.QuestionDao">
       <select id="selectQuestions" resultType="com.sjw.zxks.pojo.Question">
           select * from question
           <if test="params != null">
               where
               <foreach collection="params" separator="and" index="key"  item="value">
                   ${key} = ${value}
               </foreach>
           </if>
       </select>
   </mapper>
   ```

##### 4、接口测试

 ![image-20210126151605884](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210126151605884.png)

   ![image-20210126151626505](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20210126151605884.png)

