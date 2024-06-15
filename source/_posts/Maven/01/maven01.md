---
title: maven入门笔记01--简介
categories: Maven
date: 2022-09-04 22:00:00
typora-root-url: ../
---

# Maven入门01--简介

Maven是Apache组织维护的一款专门为Java项目提供**构建**和**依赖**管理支持的工具。

### 什么是Maven

#### 1. 构建

Java项目开发中，构建指的是使用**原材料**生产**产品**的过程。

- 原材料
  - Java源代码
  - 基于HTML的Thymeleaf文件
  - 图片
  - 配置文件
  - ......
- 产品
  - 一个可以在服务器上运行的项目



构建过程包含的主要环节：

- 清理：删除上一次构建的结果，为下一次构建做准备
- 编译：Java源程序编译成*.class文件
- 测试：运行提前准备好的测试程序（针对特定功能点的Junit）
- 报告：针对刚才测试的结果生成一个全面的信息
- 打包：
  - java工程：jar包
  - web工程：war包
- 安装：把一个Maven工程经过打包操作生成的jar包或war包存入Maven仓库
- 部署：
  - 部署jar包：把一个jar包部署到Nexus私服服务器上
  - 部署war包：借助相关Maven插件（例如cargo），将war包部署到Tomcat服务器上



### 2. 依赖

如果A工程里用到了B工程的类、接口、配置文件等资源，就可以说A依赖B。



依赖管理过程中要解决的具体问题：

- jar包的下载：使用Maven之后，jar包会从规范的远程仓库下载到本地
- jar包之间的依赖：通过依赖的传递性自动完成
- jar包之间的冲突：通过对依赖的配置进行调整，让某些jar包不被导入



### 3. Maven的工作机制

<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/Spring/Maven01_%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6.png" width="80%" style="center"/>



### 4. 解压Maven核心程序

核心程序压缩包：apache-maven-3.8.6-bin.zip，解压到**非中文、没有空格**的目录。

在解压目录中，重点关注Maven的核心配置文件：**conf/settings.xml**

### 5. 指定本地仓库

本地仓库默认值：${user.home}/.m2/repository。由于本地仓库的默认位置是在用户的家目录（windows下通常在C盘）。将来Maven仓库中jar包越来越多，仓库体积越来越大，可能会拖慢C盘运行速度，影响系统性能。所以建议将Maven的本地仓库放在其他盘符下。配置方式如下：

```xml
<!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ${user.home}/.m2/repository
  <localRepository>/path/to/local/repo</localRepository>
  -->
<!-- 配置Maven本地仓库 -->
<localRepository>D:\envs\maven-repo</localRepository>
```

### 6. 配置阿里云提供的镜像仓库

Maven下载jar包默认访问境外的中央仓库，而国外网站速度很慢。改成阿里云提供的镜像仓库，访问国内网站，可以让maven下载jar包的时候速度更快。配置的方式是：将下面mirror标签复制到settings.xml文件的mirrors标签的内部。

```xml
<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>central</mirrorOf>
    <name>aliyun</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

### 7. 配置Maven工程的基础JDK版本

如果按照默认配置运行，java工程默认的JDK版本是1.5，而常用的是1.8版本。需要将下述内容放在settings.xml文件的profiles标签的内部。

```xml
<profile>
    <id>jdk-1.8</id>
    <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
    </activation>
    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>
</profile>
```































