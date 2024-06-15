---
title: Mybatis笔记04--多对一与一对多
categories: Mybatis
date: 2022-06-25 17:00:00
typora-root-url: ../
---

### Mybatis笔记04--多对一与一对多

#### 1. 多对一处理

多个学生选一位老师的课

- 多个学生，对应一位老师
- 对于学生而言，**关联**，多个学生关联一个老师 【多对一】
- 对于老师而言，**集合**，一个老师有很多个学生 【一对多】

```sql
CREATE TABLE `teacher` (
  `id` INT(10) NOT NULL,
  `name` VARCHAR(30) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO teacher(`id`, `name`) VALUES (1, '秦老师'); 

CREATE TABLE `student` (
  `id` INT(10) NOT NULL,
  `name` VARCHAR(30) DEFAULT NULL,
  `tid` INT(10) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `fktid` (`tid`),
  CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('1', '小明', '1'); 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('2', '小红', '1'); 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('3', '小张', '1'); 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('4', '小李', '1'); 
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('5', '小王', '1');
```

##### 按照查询嵌套处理

```xml
<!--
    思路：
        1. 查询所有的学生信息
        2. 根据查询出的学生的tid，寻找对应的老师
-->
<select id="getStudent" resultMap="StudentTeacher">
    select * from student s, teacher t
    where s.tid = t.id;
</select>

<resultMap id="StudentTeacher" type="Student">
    <result property="id" column="id"/>
    <result property="name" column="name"/>
    
    <!-- 复杂属性（Teacher），需要单独处理
            对象：association
            集合：collection
        -->
    <association property="teacher" column="tid" javaType="Teacher" select="getTeacher" />
</resultMap>

<select id="getTeacher" resultType="Teacher">
    select * from teacher where id = #{tid}
</select>
```

##### 按照结果嵌套处理

```xml
<!-- 按照结果嵌套处理 -->
<select id="getStudent2" resultMap="StudentTeacher2">
    select s.id sid, s.name sname, t.name tname, t.id tid
    from student s, teacher t
    where s.tid = t.id;
</select>

<resultMap id="StudentTeacher2" type="Student">
    <result property="id" column="sid" />
    <result property="name" column="sname" />
    <association property="teacher" javaType="Teacher">
        <result property="name" column="tname" />
        <result property="id" column="tid" />
    </association>

</resultMap>
```



回顾mysql多对一查询方式：

- 子查询	-- 【按照查询嵌套处理】
- 联表查询  -- 【按照结果嵌套处理】

#### 2. 一对多处理

比如：一个老师教授多个学生。对于老师而言，就是一对多的关系。

##### 按照查询嵌套处理

```xml
<select id="getTeacher2" resultMap="TeacherStudent2" >
    select * from teacher
    where id = #{tid}
</select>

<resultMap id="TeacherStudent2" type="Teacher">
    <result property="id" column="id" />
    <collection property="students" javaType="ArrayList" ofType="Student" select="getStudentByTeacherId" column="id" />
</resultMap>

<select id="getStudentByTeacherId" resultType="Student">
    select * from student
    where tid = #{tid}
</select>
```

##### 按照结果嵌套处理

```xml
<select id="getTeacher" resultMap="TeacherStudent">
    select s.id sid, s.name sname, t.name tname, t.id tid
    from student s, teacher t
    where s.tid = t.id and t.id = #{tid};
</select>

<resultMap id="TeacherStudent" type="Teacher">
    <result property="id" column="tid" />
    <result property="name" column="tname" />

    <!-- 复杂属性（Teacher），需要单独处理
        对象：association
        集合：collection
       javaType = 指定属性的类型！
       集合中的泛型信息，用 ofType获取
    -->
    <collection property="students" ofType="Student">
        <result property="id" column="sid" />
        <result property="name" column="sname" />
    </collection>
</resultMap>
```

#### 3. 小结

1. 关联 -- association 【多对一】

2. 集合 -- collection  【一对多】

3. javaType   &   ofType

   - javaType  用来指定实体类中属性的类型

   - ofType 用来指定映射到List或者集合中的pojo类型（泛型中的约束类型）

注意点：

- 保证SQL的可读性，尽量保证通俗易懂
- 注意一对多和多对一中，属性名和字段的问题

   
