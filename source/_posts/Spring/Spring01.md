---
title: Spring笔记01--简介&依赖注入&bean的作用域
categories: Spring
date: 2022-06-26 22:00:00
typora-root-url: ../
---

# Spring笔记01--简介&依赖注入&bean的作用域

### 1. Spring

#### 1.1 简介

官网：https://spring.io/projects/spring-framework#overview

github：https://github.com/spring-projects/spring-framework

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.21</version>
</dependency>
```

#### 1.2 优点

- Spring是一个开源、免费的框架（容器）！
- Spring是一个轻量级的、非入侵式的框架！
- 控制反转（IOC），面向切面编程（AOP）！
- 支持事务的处理，对框架整合的支持！

#### 1.3 组成 

<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/Spring/Spring01-01-7modules.jpg" width="80%" style="center"/>

### 2. IOC理论

之前：

```java
private UserDao userDao =  new UserDaoImpl();
```

利用set进行动态实现值的注入:

```java
public void setUserDao(UserDao userDao) {
    this.userDao = userDao;
}
```

- 之前，程序主动创建对象，控制权在程序员手上；用户变更需求后，需要修改原来的代码
- 使用了set注入后，程序不再具有主动性，而是被动地接受对象！

这种思想，从本质上解决了问题，程序员不需要再去管理对象的创建！系统的耦合性大大降低，可以更加专注于业务的实现。这就是IOC的原型。

### 3. IOC创建对象的方式

1. 使用无参构造创建对象，默认！

2. 若需要用有参构造创建对象，方法：

   - 第一种：下标赋值

     ```xml
     <!-- 有参构造第一种： 下标赋值 -->
     <bean id="user" class="com.lics.pojo.User">
         <constructor-arg index="0" value="lics" />
     </bean>
     ```

   - 第二种：类型匹配 【不建议使用】

     ```xml
     <!-- 有参构造第一种： 下标赋值 -->
     <bean id="user" class="com.lics.pojo.User">
         <constructor-arg type="java.lang.String" value="lics-type" />
     </bean>
     ```

   - 第三种：参数名 【通常使用】

     ```xml
     <bean id="user" class="com.lics.pojo.User">
         <constructor-arg name="name" value="lics-arg-name" />
     </bean>
     ```

3. 在配置文件加载的时候，容器中管理的对象就已经初始化了，无论是否使用。

### 4. Spring配置

#### 4.1 别名

```xml
<!-- 如果添加了别名，也可以通过别名获取到这个bean -->
<alias name="user" alias="userNew" />
```

#### 4.2 Bean的配置

```xml
<!--
    id：bean的唯一标识符
    class：bean对象所对应的全限定名：包名+类型
    name：也是别名，且name可以同时取多个别名
 -->
<bean id="userNew" class="com.lics.pojo.User" name="user2 u2,u3;u4">
    <constructor-arg type="java.lang.String" value="lics-type" />
</bean>
```

#### 4.3 import

一般用于团队开发使用，将多个配置文件，合并为一个

```xml
<import resource="beans.xml" />
```

### 5. 依赖注入

#### 5.1 构造器注入

​	见3

#### 5.2 Set方式注入 【重点】

- 依赖注入：本质是Set注入
  - 依赖：bean对象的创建依赖于容器
  - 注入：bean对象中的所有属性由容器来注入

```xml
<bean id="address" class="com.lics.pojo.Address">
    <property name="address" value="地址" />
</bean>

<bean id="student" class="com.lics.pojo.Student">
    <!-- 第一种，普通注入，value  -->
    <property name="name" value="lics" />

    <!-- 第二种，Bean注入，ref  -->
    <property name="address" ref="address" />

    <!-- 数组  -->
    <property name="books">
        <array>
            <value>红楼梦</value>
            <value>西游记</value>
            <value>水浒传</value>
            <value>三国演义</value>
        </array>
    </property>

    <!-- List  -->
    <property name="hobbies">
        <list>
            <value>听歌</value>
            <value>看电影</value>
            <value>敲代码</value>
        </list>
    </property>

    <!-- Map  -->
    <property name="card">
        <map>
            <entry key="身份证" value="1234556" />
            <entry key="银行卡" value="3243241" />
        </map>
    </property>

    <!-- Set  -->
    <property name="games">
        <set>
            <value>lol</value>
            <value>coc</value>
            <value>bob</value>
        </set>
    </property>

    <!-- null  -->
    <property name="wife">
        <null/>
    </property>

    <!-- Properties  -->
    <property name="info">
        <props>
            <prop key="driver">20220628</prop>
            <prop key="url">nnn</prop>
            <prop key="username">root</prop>
            <prop key="password">123</prop>
        </props>
    </property>
</bean>
```

#### 5.3 拓展方式注入

- p命名空间（property -- set方法）
- c命名空间（construct-args -- 有参构造）

### 6. bean的作用域

<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/Spring/01-bean-scopes.png" width="85%" style="center"/>

- 单例模式（Spring默认机制）

- 原型模式：每次从容器中getBean的时候，都会产生一个新对象！

  ```xml
  <bean id="accountService" class="com.something.DefaultAccountService" scope="prototype"/>
  ```

- 其余的request、session、application等只能在web开发中使用到！
