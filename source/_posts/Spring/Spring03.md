---
title: Spring笔记03--AOP
categories: Spring
date: 2022-06-28 20:00:00
typora-root-url: ../
---

# Spring笔记03--AOP

### 1. 代理模式

代理模式就是Spring AOP的底层！

代理模式的分类：

- 静态代理
- 动态代理

### 2. AOP

#### 2.1 什么是AOP

AOP（Aspect Oriented Programming），面向切面编程。通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。

<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/Spring/03-01-AOP-example.png" width="80%" style="center"/>

#### 2.2 AOP在Spring中的作用

提供声明式事务，允许用户自定义切面。

<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/Spring/03-02-AOP-usage.png" width="80%" style="center"/>

#### 2.3 使用Spring实现AOP

【重点】使用AOP，需要导入一个依赖包：

```xml
<!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.9.1</version>
    <scope>runtime</scope>
</dependency>
```



方式一：使用Spring的API接口 【主要是SpringAPI接口实现】

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 注册bean -->
    <bean id="userService" class="com.lics.service.UserServiceImpl" />
    <bean id="log" class="com.lics.log.Log" />
    <bean id="logAfter" class="com.lics.log.LogAfter" />

    <!-- 配置aop -->
    <aop:config>
        <!-- 切入点 -->
        <aop:pointcut id="pointcut" expression="execution(* com.lics.service.UserServiceImpl.*(..))"/>
        <!-- 执行环绕增加！ -->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut" />
        <aop:advisor advice-ref="logAfter" pointcut-ref="pointcut" />
    </aop:config>
    
</beans>
```

```java
import org.springframework.aop.MethodBeforeAdvice;

import java.lang.reflect.Method;

public class Log implements MethodBeforeAdvice {
    @Override
    public void before(Method method, Object[] args, Object target) throws Throwable {
        System.out.println(target.getClass().getName() + "的" + method.getName() + "被执行了");
    }
}
```

方式二：自定义实现AOP【主要是切面定义】

```xml
<bean id="diy" class="com.lics.diy.DiyPointCut"/>

<aop:config>
    <!-- 自定义切面， ref 要引用的类-->
    <aop:aspect ref="diy">
        <!-- 切入点 -->
        <aop:pointcut id="point" expression="execution(* com.lics.service.UserServiceImpl.*(..))"/>
        <!-- 通知 -->
        <aop:before method="before" pointcut-ref="point" />
        <aop:after method="after" pointcut-ref="point" />
    </aop:aspect>
</aop:config>
```

方式三：使用注解实现

```xml
<bean id="annotationPointCut" class="com.lics.diy.AnnotationPointCut" />
<!-- 开启注解支持 -->
<aop:aspectj-autoproxy/>
```

```java
@Before("execution(* com.lics.service.UserServiceImpl.*(..))")
public void before() {
    System.out.println("*********方法执行前********");
}

@After("execution(* com.lics.service.UserServiceImpl.*(..))")
public void after() {
    System.out.println("*********方法执行后********");
}

@Around("execution(* com.lics.service.UserServiceImpl.*(..))")
public void around(ProceedingJoinPoint joinPoint) throws Throwable {
    System.out.println("*********环绕前********");
    joinPoint.proceed();
    System.out.println("*********环绕后********");
}
```
