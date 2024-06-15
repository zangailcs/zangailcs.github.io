---
title: maven入门笔记02--根据坐标创建Maven工程
categories: Maven
date: 2022-09-04 23:00:00
typora-root-url: ../
---

# Maven入门02-实验一（根据坐标创建Maven工程）

### 1. Maven核心概念：坐标

#### ① 数学中的坐标：

以三维空间坐标系为例，使用x、y、z三个向量，可以在空间中唯一的定位到一个点。

#### ② Maven中的坐标

##### [1] 向量说明

使用三个<font color='cornflowerblue'>向量</font>在<font color='cornflowerblue'>Maven的仓库</font>中<font color='cornflowerblue'>唯一</font>的定位到一个<font color='cornflowerblue'>jar</font>包。

- <font color='cornflowerblue'>groupId</font>：公司或组织的id
- <font color='cornflowerblue'>artifactId</font>：一个项目或者是项目中的一个模块的id
- <font color='cornflowerblue'>version</font>：版本号

##### [2] 三个向量的取值方式

- groupId：公司或者组织域名的倒序，通常也会加上项目名称
  - 例如：com.lics.helloMaven
- artifactId：模块的名称，将来作为Maven工程的工程名
  - 例如：pro01-lics-maven
- version：模块的版本号，根据需要设定
  - 例如：SNAPSHOT表示快照版本，正在迭代过程中，不稳定的版本
  - 例如：RELEASE表示正式版本

### 2. 坐标和仓库中jar包的存储路径之间的对应关系

坐标：

```xml
<groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
<scope>provided</scope>
```

对应的jar包在maven本地仓库中的位置：

```text
${maven本地仓库根目录}$\javax\servlet\javax.servlet-api\4.0.1\javax.servlet-api-4.0.1.jar
```

### 3. 实验操作

#### ① 创建目录作为后面操作的工作空间

例如：D:\codes\maven-workspace

::: notice

此时我们已经有了三个目录，分别是：

- Maven核心程序：中军大帐
- Maven本地仓库：兵营
- 本地工作空间：战场

:::

#### ② 在工作空间目录下打开cmd

#### ③ 使用命令生成Maven工程

运行 <font color='cornflowerblue'>**mvn archetype:generate**</font> 命令

```bash
Choose a number or apply filter (format: [groupId:]artifactId, case sensitive contains): 7: 【默认值，7代表quickstart】
Define value for property 'groupId': com.lics.maven
Define value for property 'artifactId': pro01-maven-java
Define value for property 'version' 1.0-SNAPSHOT: :【默认版本号】
Define value for property 'package' com.lics.maven: :
```

#### ④ 调整

Maven默认依赖的是较低的junit版本3.8.1，可以改成较合适的4.12版本。

自动生成的App.java和AppTest.java可以删除。

```xml
<!-- 依赖信息配置 -->
<dependencies>
    <dependency>
        <!-- 通过坐标来依赖其他的jar包 -->
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        
        <!-- 依赖的范围 -->
        <scope>test</scope>
    </dependency>
</dependencies>
```

#### ⑤ 自动生成的pom.xml解读

```xml
<!-- project标签: 根标签, 表示对当前工程进行配置、管理 -->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <!-- modelVersion标签: 从maven 2开始就固定是4.0.0 -->
  <!-- 代表当前 pom.xml 所采用的标签结构 -->
  <modelVersion>4.0.0</modelVersion>

  <!-- gav 坐标信息 -->
  <groupId>com.lics.maven</groupId>
  <artifactId>pro01-maven-java</artifactId>
  <version>1.0-SNAPSHOT</version>

  <!-- packaging标签: 打包方式 -->
  <!-- 取值jar: 生成jar包, 说明这是一个 Java 工程 -->
  <!-- 取值war: 生成war包, 说明这是一个 Web 工程 -->
  <!-- 取值pom: 说明这个工程是用来管理其他工程的工程 -->
  <packaging>jar</packaging>

  <name>pro01-maven-java</name>
  <url>http://maven.apache.org</url>

  <!-- 在Maven中定义属性值 -->
  <properties>
    <!-- 在构建过程中读取源码时使用的字符集 -->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <!-- dependencies标签: 配置具体的依赖信息, 可以包含多个 dependency 子标签 -->
  <dependencies>
    <!-- dependency标签: 配置一个具体的依赖信息 -->
    <dependency>
      <!-- 坐标信息 -->
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>

      <!-- scope标签: 当前依赖的范围 -->
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

### 4. Maven核心概念：POM

#### ① 含义

POM：<font color='cornflowerblue'>P</font>roject <font color='cornflowerblue'>O</font>bject <font color='cornflowerblue'>M</font>odel，项目对象模型。和POM类似的是：DOM(Document Object Model)，文档对象模型。它们都是模型化思想的具体体现。

#### ② 模型化思想

POM表示将工程抽象为一个模型，再用程序中的对象来描述这个模型。这样我们就可以用程序来管理项目了。在开发过程中，最基本的做法就是将现实中的事物抽象为模型，然后封装模型相关的数据作为一个对象，在程序中计算与现实事物相关的数据。

#### ③ 对应的配置文件

POM理念集中体现在Maven工程根目录下pom.xml这个配置文件中。所以这个pom.xml配置文件就是Maven 工程的核心配置文件。学习Maven就是学习这个文件怎么配置，各个配置有什么用。

### 5. Maven核心概念：约定的目录结构

#### ① 各个目录的作用

<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/Spring/maven02-folderStruct" width="50%" style="center"/>

另外还有一个target目录专门存放构建操作输出的结果。

#### ② 约定目录结构的意义

为了构建过程能尽可能地自动化，必须约定目录结构的作用。例如：Maven执行编译操作，必须先去Java源码目录读取Java源代码，然后把编译结果放在target目录。

#### ③ 约定大于配置

开发领域的技术发展趋势：约定大于配置，配置大于编码。

Maven对于目录结构这个问题，没有采用配置的方式，而是基于约定。这样在开发过程中非常方便。











