---
title: Mybatis笔记05--动态SQL&缓存
categories: Mybatis
date: 2022-06-25 18:00:00
typora-root-url: ../
---

### Mybatis笔记05--动态SQL&缓存

#### 1. 动态SQL

**什么是动态SQL：动态SQL就是根据不同的条件生成不同的SQL语句。**

```xml
如果你之前用过 JSTL 或任何基于类 XML 语言的文本处理器，你对动态 SQL 元素可能会感觉似曾相识。在 MyBatis 之前的版本中，需要花时间了解大量的元素。借助功能强大的基于 OGNL 的表达式，MyBatis 3 替换了之前的大部分元素，大大精简了元素种类，现在要学习的元素种类比原来的一半还要少。
if
choose (when, otherwise)
trim (where, set)
foreach
```

##### 搭建环境

```sql
CREATE TABLE `blog`(
    `id` VARCHAR(50) NOT NULL COMMENT '博客id',
    `title` VARCHAR(100) NOT NULL COMMENT '博客标题',
    `author` VARCHAR(30) NOT NULL COMMENT '博客作者',
    `create_time` DATETIME NOT NULL COMMENT '创建时间',
    `views` INT(30) NOT NULL COMMENT '浏览量'
)ENGINE=INNODB DEFAULT CHARSET=utf8;
```

##### IF

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from blog
    where 1=1
    <if test="title != null">
        and title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</select>
```

##### choose、when、otherwise

```xml
<select id="queryBlogChoose" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <choose>
            <when test="title != null">
                title = #{title}
            </when>
            <when test="author != null">
                author = #{author}
            </when>
            <otherwise>
                views = #{views}
            </otherwise>
        </choose>
    </where>
</select>
```

##### trim、where、set

**使用where元素**：where 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，where 元素也会将它们去除。

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <if test="title != null">
            title = #{title}
        </if>
        <if test="author != null">
            author = #{author}
        </if>
    </where>
</select>
```

**使用set元素**：用于动态更新语句的类似解决方案叫做 set。set 元素可以用于动态包含需要更新的列，忽略其它不更新的列。

```xml
<update id="updateBlog" parameterType="map">
    update mybatis.blog
    <set>
        <if test="title != null">
            title = #{title},
        </if>
        <if test="author != null">
            author = #{author},
        </if>
    </set>
    where id = #{id}
</update>
```

**trim**：

*prefixOverrides* 属性会忽略通过管道符分隔的文本序列（注意此例中的空格是必要的）。上述例子会移除所有 *prefixOverrides* 属性中指定的内容，并且插入 *prefix* 属性中指定的内容。

和 *where* 元素等价的自定义 trim 元素为：

```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
  ...
</trim>
```

通过使用*trim*元素来达到set的效果：

```xml
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

##### SQL片段

有时需要将一些功能的部分抽取出来，方便复用。

1. 使用sql标签抽取公共的部分

   ```xml
   <sql id="if-title-author">
       <if test="title != null">
           title = #{title}
       </if>
       <if test="author != null">
           author = #{author}
       </if>
   </sql>
   ```

2. 在需要使用的地方用include标签引用

   ```xml
   <include refid="if-title-author" />
   ```

##### ForEach![image-04-foreach](/..	https://lics-blogs-1258546254.cos.ap-nanjing.myqcloud.com/images/MyBatis/04-foreach.png)

#### 2. 缓存

##### 2.1 简介

查询：链接数据库 -- 耗资源！

​	将一次查询的结果，暂存到一个可以直接取到的地方：内存 -- 缓存

再次查询相同数据的时候，直接走缓存，就不用走数据库了。

1. 什么是缓存[Cache]？
   - 存在内存中的数据
   - 将经常查询的数据放在缓存（内存）中，用户去查询数据不用从关系型数据库数据文件（硬盘上）查询，从缓存中查询，从而提高查询效率，解决高并发系统的性能问题。
2. 为什么使用缓存？
   - 减少和数据库的交互次数，减少系统开销，提高系统效率。
3. 什么样的数据能使用缓存？
   - 经常查询且不经常改变的数据

##### 2.2 Mybatis缓存

- MyBatis 内置了一个强大的事务性查询缓存机制，它可以非常方便地配置和定制。
- MyBatis 中默认定义了两级缓存：**一级缓存**和**二级缓存**
  - 默认情况下，只启用了本地的会话缓存，它仅仅对一个会话中（sqlSession）的数据进行缓存。
  - 二级缓存需要手动开启和配置，是基于namespace（一个接口/一个Mapper.xml文件）级别的缓存。

##### 2.3 一级缓存

- 默认是开启的，只在一次sqlSession中有效

  

​	缓存失效的情况：

1. 查询不同的东西

2. 增删改操作，可能会改变原来的数据，所以必定会刷新缓存！

3. 查询不同的Mapper.xml

4. 手动清理缓存：

   ```java
   sqlSession.clearCache(); 
   ```

##### 2.4 二级缓存

要启用全局的二级缓存，只需要在SQL 映射文件中添加一行：

```xml
<cache/>
```

也可以自定义参数：

```xml
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

小结：

- 只要开启了二级缓存，在同一个Mapper下就有效
- 所有的数据都会先放在一级缓存中
- 只有当会话关闭或者提交时，才会提交到二级缓存











