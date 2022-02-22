---
title: Java Iterator(迭代器)
date: 2022-02-16 09:08:31
tags:
  - 迭代器
categories: java
cover: https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222134730562.png
top_img: https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222134730562.png
---







结构图:

![image-20220222134730562](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222134730562.png)

# 1、简述java迭代器

​		Iterator迭代器的定义：迭代器（Iterator）模式，又叫做游标（Cursor）模式。GOF给出的定义是，提供一种方法访问一个容器container对象中各个元素，**而又不需暴露该对象的内部细节**。迭代器通常被称为“轻量级”对象，因为创建它的代价小。

- 它用于逐个遍历集合对象元素
- 它适用于所有Collection类，如上结构图所示
- 它支持read和**remove**操作

> 注意：对collection类遍历进行remove时不能使用for或者foreach，只能使用迭代器去操作，[否则抛同步异常](https://jingyan.baidu.com/article/d621e8da21e47e2865913fc8.html)

# 2、java Iterator源码

>  类图

![image-20220222140613248](https://gitee.com/coder-SJW/blogimg/raw/master/img/image-20220222140613248.png)

> 方法

```java
public interface Iterator<E> {
    /**
     * 检查集合中是否还有元素
     */
    boolean hasNext();

    /**
     * 返回迭代的下一个元素
     * @throws NoSuchElementException 如果没有可迭代的元素将抛出异常
     */
    E next();

    /**
     *将迭代器新返回的元素删除
     */
    default void remove() {
        throw new UnsupportedOperationException("remove");
    }

    /**
     * 对每个剩余元素执行给定操作，直到所有元素都被处理或操作抛出异常。
     * 如果指定了该顺序，则操作按迭代顺序执行
     * @since 1.8
     */
    default void forEachRemaining(Consumer<? super E> action) {
        Objects.requireNonNull(action);
        while (hasNext())
            action.accept(next());
    }
}
```

> 使用方法

```java
public class IteratorDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            list.add("item" + i);
        }
        //获取迭代器
        Iterator<String> listIterator = list.iterator();
        //判断是否还有元素
        while (listIterator.hasNext()) {
            System.err.println(listIterator.next());
            //对剩下的元素执行指定操作
            listIterator.forEachRemaining((String consumer) -> {
                System.err.println(consumer.concat("-test"));
            });
        }

    }
}
//结果
item0
item1-test
item2-test
item3-test
item4-test
```

# 3、Iterator总结

Java中的Iterator功能比较简单，并且只能**单向移动**：

(1) 方法iterator()将返回一个Iterator。首次调用next()方法时，它将返回第一个元素

(2) next() 返回下一个元素

(3) hasNext() 检查集合中是否还有元素

(4) remove() 将迭代器新返回的元素删除

(5) forEachRemaining() 对每个剩余的元素执行指定的操作
