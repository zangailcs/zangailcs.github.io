---
title: Mybatis笔记02--CRUD&核心配置&resultMap
categories: Mybatis
date: 2022-06-19 17:00:00
typora-root-url: ../
---

### Mybatis笔记02--CRUD&核心配置&resultMap

#### 1. CRUD

（1）**namespace**: namespace中的包名要和Dao/mapper接口的包名一致！

（2）**select**: 选择/查询语句；

- id：就是对应的namespace中的方法名；
- resultType: Sql语句执行的返回值
- parameterType：参数类型

   **步骤**：


1. 编写接口

   ```java
   // 根据id查询用户
   User getUserById(int id);
   ```

2. 编写对应的mapper中的sql语句

   ```xml
   <select id="getUserById" parameterType="int" resultType="com.lics.pojo.User">
       select * from mybatis.user where id = #{id}
   </select>
   ```

3. 测试

   ```java
   @Test
   public void test02() {
       // 获取sqlSession对象
       SqlSession sqlSession = MybatisUtils.getSqlSession();
   
       // 执行SQL
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
       User user = mapper.getUserById(1);
       System.out.println(user);
       sqlSession.close();
   }
   ```

​    （3）增

      ```xml
      <insert id="addUser" parameterType="com.lics.pojo.User">
          insert into mybatis.user (id, name, pwd) values
          (#{id}, #{name}, #{pwd})
      </insert>
      ```

​    （4）删

```xml
<delete id="deleteUser" parameterType="int">
    delete from mybatis.user
    where id=#{id};
</delete>
```

​    （5）改

```xml
<update id="updateUser" parameterType="com.lics.pojo.User">
    update mybatis.user
    set name = #{name}, pwd=#{pwd}
    where id=#{id};
</update>
```

**<font color="red">注意： 增删改需要提交事务！ </font>**

#### 2. 核心配置

MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。 配置文档的顶层结构如下：

```xml
configuration（配置）
    properties（属性）
    settings（设置）
    typeAliases（类型别名）
    typeHandlers（类型处理器）
    objectFactory（对象工厂）
    plugins（插件）
    environments（环境配置）
    environment（环境变量）
    transactionManager（事务管理器）
    dataSource（数据源）
    databaseIdProvider（数据库厂商标识）
    mappers（映射器）
```

##### 2.1 环境配置（environments）

Mybatis可以配置成适应多种环境

**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境。**

为了指定创建哪种环境，只要将它作为可选的参数传递给 SqlSessionFactoryBuilder 即可。可以接受环境配置的两个方法签名是：

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, properties);
```

如果忽略了环境参数，那么将会加载默认环境，如下所示：

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, properties);
```

Mybatis默认的事务管理器：JDBC， 连接池：POOLED

##### 2.2 属性（properties）

这些属性可以在外部进行配置，并可以进行动态替换。既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置【db.properties】。

编写一个配置文件db.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=true&characterEncoding=UTF-8
username=root
password=***
```

也可以在核心配置文件中引入

```xml
<!-- 引入外部配置文件-->
<properties resource="db.properties">
    <property name="username" value="root"/>
    <property name="password" value="111"/>
</properties>
```

- 可以直接引入外部文件

- 可以在<properties> </properties>中增加一些属性配置

- 如果上面两处有同一个字段，优先使用外部文件中的！

     详细说明如下：

     如果一个属性在不只一个地方进行了配置，那么，MyBatis 将按照下面的顺序来加载：

  - 首先读取在 properties 元素体内指定的属性。
  - 然后根据 properties 元素中的 resource 属性读取类路径下属性文件，或根据 url 属性指定的路径读取属性文件，并**覆盖**之前读取过的同名属性。
  - 最后读取作为方法参数传递的属性，并**覆盖**之前读取过的同名属性。

   因此，通过方法参数传递的属性具有最高优先级，resource/url 属性中指定的配置文件次之，最低优先级的则是 properties 元素中指定的属性。

##### 2.3 类型别名（typeAliases）

类型别名可为 Java 类型设置一个缩写名字，意在降低冗余的全限定类名书写。

```xml
<typeAliases>
    <typeAlias type="com.lics.pojo.User" alias="User"/>
</typeAliases>
```

也可以指定一个包名，mybatis会在包名下搜索需要的Java Bean，比如：

扫描实体类的包，它的默认别名就是这个类的 类名，首字母小写。

```xml
<typeAliases>
    <package name="com.lics.pojo"/>
</typeAliases>
```

##### 2.4 映射器（mappers）

MapperRegistry: 注册绑定Mapper文件

方式一：【推荐使用】使用相对于类路径的资源引用 

```xml
<mappers>
    <mapper resource="com/lics/dao/UserMapper.xml" />
</mappers>
```

方式二：使用class文件绑定注册

```xml
<mappers>
    <mapper class="com.lics.dao.UserMapper" />
</mappers>
```

​	注意点：

- 接口和他的Mapper配置文件必须同名，且在同一个包下！

方式三：使用扫描包进行注册

```xml
<mappers>
    <package name="com.lics.dao"/>
</mappers>
```

  注意点同方式二。

#### 3. 生命周期和作用域

作用域，和 生命周期，是至关重要的，因为错误的使用会导致非常严重的**并发问题**。

**SqlSessionFactoryBuilder**

- 一旦创建了 SqlSessionFactory，就不再需要它
- 局部变量

**SqlSessionFactory**：

- 可以想象成数据库连接池
- SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例**。
- 最佳作用域是应用作用域，最简单的就是使用**单例模式**或者静态单例模式

**SqlSession**：

- 连接到连接池的一个请求
- SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域
- 返回一个响应后，就关闭它，否则资源被占用！

#### 4. 解决类的属性名和数据库中字段名不一致的问题

###### 4.1 问题

数据库字段：

<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/MyBatis/01-dbnames.png" width="70%"  style="center"/>

类中属性：

```java
public class User {
    private int id;
    private String name;
    private String password;
}
```

查询出现问题：

<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/MyBatis/02-problem-pwdnull.png" width="70%"  style="center"/>

解决办法：

- 起别名（不推荐）

```xml
<select id="getUserList" resultType="user">
    select id, name, pwd as password from mybatis.user
</select>
```

- **resultMap**

###### 4.2 resultMap

结果集映射

```xml
<resultMap id="UserMap" type="user">
    <!-- column：数据库中的字段  property：属性名 -->
    <result column="id" property="id" />
    <result column="name" property="name" />
    <result column="pwd" property="password" />
</resultMap>

<select id="getUserList" resultMap="UserMap">
    select * from mybatis.user
</select>
```

- `resultMap` 元素是 MyBatis 中最重要最强大的元素
- ResultMap 的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。
