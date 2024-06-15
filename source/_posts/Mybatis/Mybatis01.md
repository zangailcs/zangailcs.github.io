---
title: Mybatis笔记01--环境搭建
categories: Mybatis
date: 2022-06-18 17:00:00
typora-root-url: ../
---

### Mybatis笔记01--环境搭建

#### 获取Mybatis

-  maven仓库

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.7</version>
</dependency>

```

- github: https://github.com/mybatis/mybatis-3/releases
- 中文文档：https://mybatis.org/mybatis-3/zh/index.html

#### 搭建环境

##### 1.  创建数据库

```sql
create database `mybatis`;

use `mybatis`;

create table `user` (
  `id` int(20) not null primary key,
  `name` varchar(30) DEfault null,
  `pwd` VARCHAR(30) DEFAULT NULL
) engine=innodb default charset=utf8;

INSERT INTO `user` (`id`, `name`, `pwd`) VALUES 
(1, '张三', '123456'), 
(2, '李四', '123456'), 
(3, '王五', '123890'); 
```

##### 2. 创建maven项目，并导入maven依赖

```xml
	<dependencies>
        <!--   mysql驱动   -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.49</version>
        </dependency>
        <!--   mybatis   -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.10</version>
        </dependency>
        <!--   junit   -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13</version>
        </dependency>
    </dependencies>
```

##### 3. 创建一个模块module

- ###### 编写mybatis的核心配置文件

```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <!--configuration 核心配置文件-->
  <configuration>
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="com.mysql.jdbc.Driver"/>
                  <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                  <property name="username" value="root"/>
                  <property name="password" value="zxc"/>
              </dataSource>
          </environment>
      </environments>
  </configuration>
```

- ###### 编写mybatis工具类

```java
  public class MybatisUtils {
      private static SqlSessionFactory sqlSessionFactory;
  
      static {
          try {
              // 使用Mybatis第一步： 获取sqlSessionFactory对象
              String resource = "mybatis-config.xml";
              InputStream inputStream = Resources.getResourceAsStream(resource);
              sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
          } catch (IOException e) {
              throw new RuntimeException(e);
          }
      }
  
      // 既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
      // SqlSession 提供了在数据库执行 SQL 命令所需的所有方法
      public static SqlSession getSqlSession() {
          return sqlSessionFactory.openSession();
      }
  }
```

- ###### 编写代码

  - 编写实体类

  - 编写接口

    ```java
    public interface UserDao {
        List<User> getUserList();
    }
    ```

  - 接口实现类由原来的***Impl转换为一个Mapper配置文件

    ```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.lics.dao.UserDao">
        <select id="getUserList" resultType="com.lics.pojo.User">
            select * from mybatis.user
        </select>
    </mapper>
    ```
  
  - <font color='red'>每一个Mapper.XMl都需要在mybatis的核心配置文件中注册！ </font>
  
    ```xml
    	<mappers>
            <mapper resource="com/lics/dao/UserMapper.xml" />
        </mappers>
    ```
  
  - 测试代码
  
    ```java
    public class UserDaoTest {
    
      @Test
      public void test01() {
          // 获取sqlSession对象
          SqlSession sqlSession = MybatisUtils.getSqlSession();
    
          // 执行SQL
          UserDao mapper = sqlSession.getMapper(UserDao.class);
          List<User> userList = mapper.getUserList();
    
          System.out.println(userList);
    
          sqlSession.close();
      }
    }
    ```
  
  
  
  ##### 4. xml文件导出失败：
  
  在pom文件中增加以下设置：
  
  ```xml
  <!--maven的资源过滤问题-->
  <build>
      <resources>
          <resource>
              <directory>src/main/resources</directory>
              <includes>
                  <include>**/*.properties</include>
                  <include>**/*.xml</include>
              </includes>
              <filtering>true</filtering>
          </resource>
          <resource>
              <directory>src/main/java</directory>
              <includes>
                  <include>**/*.properties</include>
                  <include>**/*.xml</include>
              </includes>
              <filtering>true</filtering>
          </resource>
      </resources>
  </build>
  ```
  
  
