---
title: java简洁编码
date: 2022-02-23 17:46:17
tags:
  - 编码规范
categories: java
cover: https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fwww.6ke.com.cn%2Fuploads%2Fallimg%2F191027%2F152041O33-25.jpg&refer=http%3A%2F%2Fwww.6ke.com.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1648206098&t=21fb74612cca32f1172932140345a547
top_img: https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fwww.6ke.com.cn%2Fuploads%2Fallimg%2F191027%2F152041O33-25.jpg&refer=http%3A%2F%2Fwww.6ke.com.cn&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1648206098&t=21fb74612cca32f1172932140345a547
---

# 一、方法

## 1、方法不要返回 null

**别处调用方法时，避免不必要的空指针**

> 反例：

![image-20220223175053632](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220223175053632.png)

> 正例：

![image-20220223175109480](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220223175109480.png)



## 2、总是在 Switch 语句的 Default 中抛出异常

> 当 value == 3 时，将会出现无法找到的提示，而不会让人不知所谓。

```java
// Bad
switch (value) {
    case 1: foo(); break;
    case 2: bar(); break;
}
// Good
switch (value) {
    case 1: foo(); break;
    case 2: bar(); break;
    default:
        throw new ThreadDeath("That'll teach them");
}
```

# 二、常量与变量

## 1、如果变量的初值会被覆盖，就没有必要给变量赋初值

```java
// Bad
List<UserDO> userList = new ArrayList<>();
if (isAll) {
    userList = userDAO.queryAll();
} else {
    userList = userDAO.queryActive();
}
// Good
List<UserDO> userList;
if (isAll) {
    userList = userDAO.queryAll();
} else {
    userList = userDAO.queryActive();
}
```

## 2、尽量使用移位来代替正整数乘除

> 用移位操作可以极大地提高性能。对于乘除2^n(n为正整数)的正整数计算，可以用移位操作来代替。

```java
// Bad
int num1 = a * 4;
int num2 = a / 4;
// Good
int num1 = a << 2;
int num2 = a >> 2;
```



# 三、字符串

## 1、尽量使用StringBuilder进行字符串拼接

>  String是final类，内容不可修改，所以每次字符串拼接都会生成一个新对象。StringBuilder在初始化时申请了一块内存，以后的字符串拼接都在这块内存中执行，不会申请新内存和生成新对象。

```java
// Bad
String s = "";
for (int i = 0; i < 10; i++) {
    if (i != 0) {
        s += ',';
    }
    s += i;
}
// Good
StringBuilder sb = new StringBuilder(128);
for (int i = 0; i < 10; i++) {
    if (i != 0) {
        sb.append(',');
    }
    sb.append(i);
}
```



## 2、不要使用""+转化字符串

> 使用""+进行字符串转化方便但是**效率低**，建议使用String.valueOf()

```java
// Bad
int i = 12345;
String s = "" + i;
// Good
int i = 12345;
String s = String.valueOf(i);
```



## 3、字符串比较，将 String 字符串放在最前面

> 为了防止偶发性的 NullPointerException 异常，通常将 String 放置在 equals() 函数的左边来实现字符串比较，如下代码：

```java
// Bad
if (variable.equals("literal")) { ... }
// Good
if ("literal".equals(variable)) { ... }
```



# 四、集合

## 1、初始化集合时，尽量指定集合大小

> Java集合初始化时都会指定一个默认大小，当默认大小不再满足数据需求时就会扩容，每次扩容的时间复杂度有可能是O(n)。所以，尽量指定预知的集合大小，就能**避免或减少集合的扩容次数**。

```java
// Bad
List<UserDO> userDOList = ...;
Set<Long> userSet = new HashSet<>();
Map<Long, UserDO> userMap = new HashMap<>();
List<UserVO> userList = new ArrayList<>();
for (UserDO userDO : userDOList) {
    userSet.add(userDO.getId());
    userMap.put(userDO.getId(), userDO);
    userList.add(transUser(userDO));
}
// Good
List<UserDO> userDOList = ...;
int userSize = userDOList.size();
Set<Long> userSet = new HashSet<>(userSize);
Map<Long, UserDO> userMap = new HashMap<>((int) Math.ceil(userSize * 4.0 / 3));
List<UserVO> userList = new ArrayList<>(userSize);
for (UserDO userDO : userDOList) {
    userSet.add(userDO.getId());
    userMap.put(userDO.getId(), userDO);
    userList.add(transUser(userDO));
}
```



## 2、不要使用循环拷贝集合，尽量使用JDK提供的方法拷贝集合

> JDK提供的[方法](https://www.runoob.com/java/java-arraylist-addall.html)**可以一步指定集合的容量，避免多次扩容浪费时间和空间**。同时，这些方法的底层也是调用System.arraycopy方法实现，进行数据的批量拷贝效率更高。

```java
// Bad
List<UserDO> user1List = ...;
List<UserDO> user2List = ...;
List<UserDO> userList = new ArrayList<>(user1List.size() + user2List.size());
for (UserDO user1 : user1List) {
    userList.add(user1);
}
for (UserDO user2 : user2List) {
    userList.add(user2);
}
// Good
List<UserDO> user1List = ...;
List<UserDO> user2List = ...;
List<UserDO> userList = new ArrayList<>(user1List.size() + user2List.size());
userList.addAll(user1List);
userList.addAll(user2List);
```



## 3、不要使用size方法检测空，必须使用isEmpty方法检测空

> 使用size方法来检测空逻辑上没有问题，但使用isEmpty方法使得代码更易读，并且可以获得更好的性能。**任何isEmpty方法实现的时间复杂度都是O(1)，但是某些size方法实现的时间复杂度有可能是O(n)**。

```java
// Bad
List<UserDO> userList = ...;
if (userList.size() == 0) {
    ...
}
Map<Long, UserDO> userMap = ...;
if (userMap.size() == 0) {
    ...
}
// Good
List<UserDO> userList = ...;
if (userList.isEmpty()) {
    ...
}
Map<Long, UserDO> userMap = ...;
if (userMap.isEmpty()) {
    ...
}
```

## 4、Optional判null

[java8新特性 Optional](https://www.runoob.com/java/java8-optional-class.html)

```java
//获取子目录列表
public List<CatalogueTreeNode> getChild(String pid) {
            if (V.isEmpty(pid)) {
            pid = BasicDic.TEMPORARY_DIRECTORY_ROOT;
        }
        CatalogueTreeNode node = treeNodeMap.get(pid);
 
        return Optional.ofNullable(node)
                .map(CatalogueTreeNode::getChild)
                .orElse(Collections.emptyList()); //orElse(T other):如果存在该值，返回值， 否则返回 other。
    }
}
```

> Optional.ofNullable(node)

![image-20220225112903433](C:\Users\28552\AppData\Roaming\Typora\typora-user-images\image-20220225112903433.png)

## 5、(推荐)CollectionUtils判null、空

> CollectionUtils属于org.apache.commons

![image-20220225113354421](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220225113354421.png)

> CollectionUtils.isEmpty()方法源码

![image-20220225113252878](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220225113252878.png)

> 如果直接使用list.isEmpty(),list为null就会抛异常；ColletionsUtils里面也判断了isEmpty，但为什么他没报错呢？

我们都知道&&和||都是短路模式，&&必须两个都为正确(true)，才会返回正确，只要第一个为false，则下面的不执行，直接返回错误。而||则是只要第一个返回true，则后面的就不再执行。

所以：他是在先判断isEmpty之前判断的是否为null，并且用的短路模式，||当前面的为正确(true)，则后面的isEmpty则不会运行，直接返回正确，从而业务能正常运行下去，**不会报空异常**。

# 五、框架

## 1、用@RequiredArgsConstructor代替@Autowired

> @RequiredArgsConstructor：lombok提供

![image-20220223174838495](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220223174838495.png)

