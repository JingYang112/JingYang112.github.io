---
title: java基础
categories: java
date: 2021-07-28 17:20:00
top: true
tags:
  - java
  - 编程语言
summary: java基础知识总结
author: yangjing
---



# java:

**名词概念**

编译型：类似翻译一本书，一下子翻译完

解释型：一边读一遍翻译

java拥有以上两种特性

## java的运行过程

编译.java文件——》得到字节文件.class——》加载到类加载器中——》字节码校验——》解释器——》操作系统显示

## 关键字

![image-20210702200507723](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210702200507723.png)

## 八大数据类型

java强类型语言

* byte

* short

* int 

* long

  

* float

* double

  

* char

* boolean

==**1L表示是long型的**==



## 变量

1. 类型等级byte->short->char->int->long->float->double

2. 类型转换

* 小转大无需操作，大转小强制转换（int）x，但是需要注意内存溢出
* boolean不能转换
* 转化存在精度问题

3. 变量类型

* 类变量  static +类型名 +变量
* 实例变量 方法名上面
* 局部变量 方法里面

![image-20210703185147686](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210703185147686.png)

### 常量

1. 定义：final+类型+变量；如：

   ```java
   final String name;
   static final String name;
   final  static  String name;
   修饰符不区分顺序
   ```



### 运算符

![image-20210703185304172](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210703185304172.png)



**不同类型间运算**

* 结果属于强运算符:比如

  ```java
  long a=123;
  double b=323d;
  float c=545f;
  a+b:类型为double
  a+c:类型为float
  ```

  

**&&**：

```java
int a=10;
if(a>0&&a++)
这里a++不会执行
```

**<<,>>>**：

* <<相当于把这个数*2

* ">>"相当于把这个数/2

### java doc

![image-20210703194655553](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210703194655553.png)



### 反编译：

每个变量对应一个唯一的hashcode 234245

### goto:

![image-20210704200650151](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210704200650151.png)





### 方法

* 引用传递和值传递

> java是值传递

**方法重载**

一个类里面有相同的方法，只是参数类型不一样



![image-20210704203406428](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210704203406428.png)

可变参数：

> string... args 这种类型，每个方法只有一个可变长参数，只能最后一个是可变参数

## 存储

### java使用栈机制存储变量，使用堆存储对象







## 对象

类是对象的抽象，对象是类的实例。

## 构造器

new对象的实质是调用构造器，默认无参构造，定义了有参构造，无参构造必须显示定义

作用：初始化变量

1. 与类名相同
2. 无返回值

![image-20210707223440801](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210707223440801.png)



## 继承：

- 父类也叫基类，子类也叫扩展类
- 子类是父类的扩展

- 所有类默认继承Object
- 继承即拥有所有属性和方法
- 不能多继承

## 封装

- 改露的露，改藏的藏
- 禁止直接访问类的属性，而是通过接口操作
- 满足“高内聚，低耦合”的要求
- get/set方法

## super

- super只能调用父类的构造方法
- super只能在子类的方法或者构造方法中调用
- super和this不能同时指向构造方法，只能保证一个在方法第一行

**和this的区别**

- super继承才能调用
- this直接能用



## 重写

- 需要继承，子类重写父类方法
- 方法名和参数列表完全相同，方法体可以不同
- 修饰符范围只能扩大不能缩小
- 抛出异常的范围只能缩小不能扩大

非静态方法情况下

为啥要重写：父类的一些方法子类不满足或者不需要





静态方法：引用看左边，左边的引用指向子类

![image-20210708205459350](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210708205459350.png)



## 多态：

> 引用变量指向的具体实例和该引用发出的方法调用在编程的时候并不确定

同一个方法，根据发送对象的不同而执行多种不同行为的方式。

一个对象的实际类型是确定的，但可以指向对象的引用类型有很多。

注意事项：

1. 多态是方法的多态
2. 存在条件，继承关系，方法需要重写，父类引用指向子类 Father f=new Son();

1. static
2. final
3. private



## instanceof：

判断两个类是否有父子关系

类转化，两个有关系的类才能转换，提高代码利用率

前提：

1. 父类的引用指向子类
2. 父转子向下转，需要强制转化
3. 子转父，直接转，但是会丢失方法



# 抽象类

注意要点：接口可以实现多继承？

1. 不能得到一个该类的实例
2. 抽象方法必须在抽象类中
3. 抽象方法由子类实现
4. 抽象类中可以写普通方法

存在意义：提高开发效率和可拓展性



## 接口

接口中所有的定义都是抽象的默认public abstract

接口中的属性是常量

可以实现多个接口，必须重写所有的方法

接口是一个标准、规约，只有规范，无法自己写代码



## 异常

快捷键 ctrl+alt+t

多个catch,必须按照异常从小到大逐个捕获







![image-20210710163724414](C:\Users\yang\AppData\Roaming\Typora\typora-user-images\image-20210710163724414.png)































## 面试

1. （浮点数比较会出问题有误差，接近但是不等于）

​       银行业务不能用浮点型表示，用数学工具里面的一个BigDecimal表示

2. 等于判断

   ```java
   string str2=new String()
   string str1=new String()
   str1!=str2
   string str1="new String"
   string str2="new String"
   str1==str2
   ```

3. 怎么计算2*8最快

   ```java
   2<<3
   ```

4. 字符串拼接

```java
        int a=10;
        int b=20;
//        字符串两侧+上面“”会被转成String
        System.out.println(""+a+b);
        System.out.println(a+b+"");
        结果为：1020
        结果为：30
```



