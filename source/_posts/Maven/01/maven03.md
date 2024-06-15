---
title: maven入门笔记03--执行Maven的构建命令&创建Maven的Web工程&依赖的范围
categories: Maven
date: 2022-09-12 23:00:00
typora-root-url: ../

---

# Maven入门03-实验二（执行Maven的构建命令）

### 1. 要求

运行Maven中和构建相关的命令时，必须进入到pom.xml所在的目录。如果没有在pom.xml所在的目录运行，会看到下面的错误提示：

```text
The goal you specified requires a project to execute but there is no POM in this directory
```

### 2. 清理操作

mvn clean

效果：删除target目录

### 3. 编译操作

- 主程序编译：mvn compile
- 测试程序编译：mvn test-compile
- 主体程序编译结果存放的目录：target/classes
- 测试程序编译结果存放的目录：target/test-classes

### 4. 测试操作

mvn test

执行时会经历以下各个生命周期：resources、compile、testResources、testCompile、test

![image-20220912195049163](https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/Spring/maven03-mvnTestRunning.png)

测试报告的存放目录：target\surefire-reports

### 5. 打包操作

mvn package

打包的结果--jar/war包，存放的目录：target

### 6. 安装操作

mvn install

```text
[INFO] Installing D:\codes\maven-workspace\pro01-maven-java\target\pro01-maven-java-1.0-SNAPSHOT.jar to D:\envs\maven-repo\com\lics\maven\pro01-maven-java\1.0-SNAPSHOT\pro01-maven-java-1.0-SNAPSHOT.jar
[INFO] Installing D:\codes\maven-workspace\pro01-maven-java\pom.xml to D:\envs\maven-repo\com\lics\maven\pro01-maven-java\1.0-SNAPSHOT\pro01-maven-java-1.0-SNAPSHOT.pom
```

安装的效果：

- 将本地构建过程中生成的jar包存入maven本地仓库。
- 将pom.xml文件转换为XXX.pom文件一起存入本地仓库。（只是名字改变，本质上是同一个文件）

# Maven入门03-实验三（创建Maven的Web工程）

### 1. 说明  

使用mvn archetype:generate命令生成Web工程时，需要使用一个专门的archetype：<font color='cornflowerblue'>maven-archetype-webapp</font>

生成的简单的webapp项目结构：

```xml
project
|-- pom.xml
`-- src
    `-- main
        `-- webapp
            |-- WEB-INF
            |   `-- web.xml
            `-- index.jsp
```

用法：

```bash
mvn archetype:generate -D archetypeGroupId=org.apache.maven.archetypes -D archetypeArtifactId=maven-archetype-webapp -D archetypeVersion=1.4
```

