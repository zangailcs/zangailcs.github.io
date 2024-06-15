---
title: Spring笔记02--自动装配&注解开发
categories: Spring
date: 2022-06-28 17:00:00
typora-root-url: ../
---

# Spring笔记02--自动装配&注解开发

### 1. bean的自动装配

- 自动装配是Spring满足bean依赖的一种方式！
- Spring会在上下文中自动寻找，并自动给bean装配属性！



在Spring中有三种装配的方式：

1. 在xml中显示地配置
2. 在java中显示地配置
3. 隐式地自动装配【重要】

#### 1.1 byName自动装配

```xml
<!--
byName: 会自动在容器上下文中查找，和自己对象set方法后面的值对应的bean id！
-->
<bean id="person" class="com.lics.pojo.Person" autowire="byName">
    <property name="name" value="lics" />
</bean>
```

#### 1.2 byType自动装配

```xml
<bean class="com.lics.pojo.Cat" />
<bean class="com.lics.pojo.Dog" />
<!--
byName: 会自动在容器上下文中查找，和自己对象set方法后面的值对应的bean id！
byType: 会自动在容器上下文中查找，和自己对象属性类型相同的bean！
-->
<bean id="person" class="com.lics.pojo.Person" autowire="byType">
    <property name="name" value="lics" />
</bean>
```



小结：

- byName时，需要保证所有bean的id唯一，且这个bean需要和自动注入属性的set方法的值一致！
- byType时，需要保证所有bean的class唯一，且这个bean需要和自动注入属性的类型一致！



#### 1.3 使用注解实现自动装配

要使用注解须知：

1. 导入约束： context约束
2. 配置注解的支持： <context:annotation-config/>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>
</beans>
```

- @Autowired：可以用在属性名上，或set方法上，**且通过byType的方式实现**

- 若自动装配无法通过一个@Autowired注解完成时，可以使用 @Qualifier(value = "xxx") 配合，指定一个唯一的bean对象注入！

### 2. 使用注解开发

- @Component：组件，放在类上，说明这个类被Spring管理了，就是bean！
- @Value：注入属性
- @Scope：作用域

### 3. 使用Java的方式配置Spring

JavaConfig是Spring的一个子项目，在Spring 4 之后，成为了一个核心功能！