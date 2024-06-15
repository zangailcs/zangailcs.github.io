---
title: Mybatis笔记03--日志&分页&注解开发
categories: Mybatis
date: 2022-06-22 17:00:00
typora-root-url: ../
---

### Mybatis笔记03--日志&分页&注解开发

#### 1. 日志

##### 1.1 日志工厂

当数据库出现异常时，需要排错。日志是最好的助手！

曾经：sout、debug

现在：日志工厂！

<img src="https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/MyBatis/03-settings-log.png" width="90%"  style="center"/>

- SLF4J 
- LOG4J 【掌握】 
- LOG4J2 
- JDK_LOGGING 
- COMMONS_LOGGING 
- STDOUT_LOGGING 【掌握】 
- NO_LOGGING

在设置中设定MyBatis中具体使用哪一个日志！

**STDOUT_LOGGING 标准日志输出**

在mybatis的核心配置文件中，配置日志

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

##### 2.2 LOG4J 

什么是LOG4J？

- 是[Apache](https://baike.baidu.com/item/Apache/8512995)的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是[控制台](https://baike.baidu.com/item/控制台/2438626)、文件、[GUI](https://baike.baidu.com/item/GUI)组件
- 也可以控制每一条日志的输出格式
- 通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程
- 通过一个[配置文件](https://baike.baidu.com/item/配置文件/286550)来灵活地进行配置，而不需要修改应用的代码



1. 先导入log4j的包

   ```xml
   <!-- https://mvnrepository.com/artifact/log4j/log4j -->
   <dependency>
       <groupId>log4j</groupId>
       <artifactId>log4j</artifactId>
       <version>1.2.17</version>
   </dependency>
   ```

2. log4j.properties

   ```properties
   #将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
   log4j.rootLogger=DEBUG,console,file
   
   #控制台输出的相关设置
   log4j.appender.console = org.apache.log4j.ConsoleAppender
   log4j.appender.console.Target = System.out
   log4j.appender.console.Threshold=DEBUG
   log4j.appender.console.layout = org.apache.log4j.PatternLayout
   log4j.appender.console.layout.ConversionPattern=[%c]-%m%n
   
   #文件输出的相关设置
   log4j.appender.file = org.apache.log4j.RollingFileAppender
   log4j.appender.file.File=./log/lics.log
   log4j.appender.file.MaxFileSize=10mb
   log4j.appender.file.Threshold=DEBUG
   log4j.appender.file.layout=org.apache.log4j.PatternLayout
   log4j.appender.file.layout.ConversionPattern=[%p][%d{yy-MM-dd}][%c]%m%n
   
   #日志输出级别
   log4j.logger.org.mybatis=DEBUG
   log4j.logger.java.sql=DEBUG
   log4j.logger.java.sql.Statement=DEBUG
   log4j.logger.java.sql.ResultSet=DEBUG
   log4j.logger.java.sql.PreparedStatement=DEBUG
   ```

3. 配置log4j为日志的实现

   ```xml
   <settings>
       <setting name="logImpl" value="LOG4J"/>
   </settings>
   ```

**简单使用**

1. 在要使用log4j的类中，导入包 import org.apache.log4j.Logger;

2. 日志对象，参数为当前类的class

   ```java
   static Logger logger = Logger.getLogger(UserMapperTest.class);
   ```

3. 日志级别

   ```java
   logger.info("info:进入了test02方法");
   logger.debug("debug:进入了test02方法");
   logger.error("error:进入了test02方法");
   ```



#### 2. 分页

**为什么要分页？**

- 减少数据的处理量

**使用limit分页**

```mysql
语法：
select * from user limit startIndex, pageSize;
select * from user limit n; # [0, n-1]
```

**使用Mybatis实现分页，核心：SQL**

1. 接口

   ```java
   // 分页
   List<User> getUserByLimit(Map<String, Integer> map);
   ```

2. Mapper.XML

   ```xml
   <select id="getUserByLimit" parameterType="map" resultType="user" resultMap="UserMap">
       select * from mybatis.user
       limit #{startIndex}, #{pageSize}
   </select>
   ```

3. 测试

   ```java
   @Test
   public void testGetUserByLimit() {
       SqlSession sqlSession = MybatisUtils.getSqlSession();
   
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
   
       HashMap<String, Integer> map = new HashMap<>();
   
       map.put("startIndex", 2);
       map.put("pageSize", 2);
   
       List<User> userByLimit = mapper.getUserByLimit(map);
   
       System.out.println(userByLimit);
   
       sqlSession.close();
   }
   ```

#### 3. 使用注解开发

##### 3.1 注解在接口上实现

```java
@Select("select * from user")
List<User> getUserListByAnnotations();
```

##### 3.2 需要在核心配置文件中绑定接口

```xml
<mappers>
    <mapper class="com.lics.dao.UserMapper" />
</mappers>
```



本质：反射机制实现

底层：动态代理

##### 3.3 CRUD

编写接口，增加注解

```java
@Select("select * from user")
List<User> getUserListByAnnotations();

@Update("update user " +
        "set id = #{id}, name = #{name}, pwd = #{password} " +
        "where id = #{id}")
int updateUserByAnnotations(User user);

@Insert("insert into user (id, name, pwd) " +
        "values " +
        "(#{id}, #{name}, #{password})")
int addUserByAnnotations(User user);

@Delete("delete from user " +
        "where id = #{id}")
int deleteUserByIdByAnnotations(@Param("id") int id);
```



**关于@Param() 注解**

- 基本类型或者String类型的参数，需要加上
- 引用类型不需要加
- 如果只有一个基本类型，可以忽略，但建议都加上！
- 在SQL中引用的就是@Param() 中设定的属性名。



**#{} 和 ${} 区别**

```java
1）#{}是预编译处理，${}是字符串替换。
2）mybatis在处理两个字符时，处理的方式也是不同的：
		①、处理#{}时，会将sql中的#{}整体替换为占位符（即：?），调用PreparedStatement的set方法来赋值；
		②、在处理${} 时，就是把${}替换成变量的值。
3）假如用${}来编写SQL会出现：恶意SQL注入，对于数据库的数据安全性就没办法保证了。
4）使用 #{} 可以有效的防止SQL注入，提高系统安全性：
	预编译的机制。预编译是提前对SQL语句进行预编译，而后再调用SQL，注入的参数就不会再进行SQL编译。而SQL注入是发生在编译的过程中，因为恶意注入了某些特殊字符，最后被编译时SQL时轻而易举的通过，从而导致数据泄露。而预编译机制则可以很好的防止SQL注入。
```

#### 4. Lombok

使用步骤：

1. 在IDEA中安装Lombok插件！

2. 在项目中导入lombok的jar包

   ```xml
   <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
       <version>1.18.22</version>
   </dependency>
   ```

3. 在实体类上加注解

```java
@Getter and @Setter
@FieldNameConstants
@ToString
@EqualsAndHashCode
@AllArgsConstructor, @RequiredArgsConstructor and @NoArgsConstructor
@Log, @Log4j, @Log4j2, @Slf4j, @XSlf4j, @CommonsLog, @JBossLog, @Flogger, @CustomLog
@Data
@Builder
@SuperBuilder
@Singular
@Delegate
@Value
@Accessors
@Wither
@With
@SneakyThrows
@val
@var
experimental @var
@UtilityClass
Lombok config system
Code inspections
Refactoring actions (lombok and delombok)
```

说明：

```java
@Data: 无参构造、get、set、toString、hashCode、equals
@AllArgsConstructor：有参构造
@NoArgsConstructor：无参构造
```

