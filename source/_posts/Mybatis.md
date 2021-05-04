---
title: Mybatis知识点
date: 2021-05-04 17:14:00
author: yangjing
summary: Mybatis学习记录
categories: SSM
tags: 
  - Mybatis
---

## Mybatis知识点

### 一、MybatisUtils工具类

```java
public class MybatiesUtils {

    private static SqlSessionFactory sqlSessionFactory;

    static {
        String resource = "mybatis-config.xml";
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
        } catch (IOException e) {
            e.printStackTrace();
        }
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    }
    //获取sqlssesion实例
    public static SqlSession getSqlssesion(){
        return sqlSessionFactory.openSession();
    }
}
```

### 二、mapper.xml文件

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.yang.mapper.UserMapper">
</mapper>
```

### 三、mybatis-config.xml

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/user?useUnicode=true&amp;characterEncoding=utf-8&amp;useSSL=false&amp;serverTimezone = GMT"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="com/yang/dao/UserMapper.xml"/>
    </mappers>

</configuration>
```

### 四、mapper.xml在mapper包下无法编译在pom.xml加入以下

```java
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
    </resources>
</build>
```

### 五、日志、别名、引入数据库配置

#### 1、log4j.properties

```java
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
log4j.appender.file.File=./log/yang.log
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=[%p][%d{yyyy-MM-dd}][%c]%m%n
#日志输出级别
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

#### 2、db.properties

```java
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/user?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone = GMT
username=root
password=123456
```

#### 3、mybatis.xml

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置-->
<configuration>


    <properties resource="db.properties"></properties>

    <settings>
<!-- 标准日志工厂实现  <setting name="logImpl" value="STDOUT_LOGGING"/>-->
        <setting name="logImpl" value="LOG4J"/>
    </settings>

    <!--    别名 默认别名为类名小写-->
    <typeAliases>
        <package name="com.yang.pojo"/>
    </typeAliases>



    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
<!--        <mapper resource="com/yang/mapper/UserMapper.xml"/>-->
<!--        <mapper class="com.yang.mapper.UserMapper"/>-->
<!--        <mapper class="com.yang.mapper.UserMapper"/>-->
        <package name="com.yang.mapper"/>
    </mappers>

</configuration>
```

### 六、模糊查询

#### 方式一、concat

```java
<if test="name!=null">
    and name like concat('%',#{name},'%')
</if>
```

#### 方式二、 < bind >

```<bind name="name" value=" '%' +name+'%' "/>```

### 七、动态sql

##### 模板

```java
<!--    动态sql-->
    <sql id="pub_sql">
       <!-- <bind name="name" value=" '%' +name+'%' "/>-->
        <choose>
            <when test="id!=null">
                id=#{id}
            </when>
            <when test="name!=null">
                and name=#{name}
            </when>
        </choose>
    </sql>

    <select id="QueryStudent" resultType="Student" parameterType="map">
        select * from student
    <where>

    <include refid="pub_sql"></include>
<!--            <choose>-->
<!--                <when test="id!=null">-->
<!--                    id=#{id}-->
<!--                </when>-->
<!--                <when test="name!=null">-->
<!--                    and name=#{name}-->
<!--                </when>-->
<!--            </choose>-->

<!--        <if test="id!=null ">-->
<!--            and id=#{id}-->
<!--        </if>-->
<!--&lt;!&ndash;        <if test="name!=null">&ndash;&gt;-->
<!--&lt;!&ndash;            and name like concat('%',#{name},'%')&ndash;&gt;-->
<!--&lt;!&ndash;        </if>&ndash;&gt;-->
<!--        <if test="name!=null">-->
<!--            and name like #{name}-->
<!--        </if>-->
    </where>
    </select>
```

### 八、多表查询

#### 1、多对一

###### 多个学生对应老师

```//    private Teacher teacher;
private Teacher teacher;
```

1. ###### 联表查询

   ```
   <!--    联表查询-->
       <select id="GetStuList1" resultMap="StudentTeacher1">
           select s.id sid,s.name sname,t.name tname,t.id tid
           from student s,teacher t
           where s.tid=t.id;
       </select>
   
       <resultMap id="StudentTeacher1" type="Student">
       <result column="sid" property="id"></result>
       <result column="sname" property="name"></result>
       <association property="teacher" javaType="Teacher">
           <result column="tname" property="name"></result>
           <result column="tid" property="id"></result>
       </association>
       </resultMap>
   ```

2. ###### 子查询

   ```
       <!--子查询-->
       <select id="GetStuList" resultMap="StudentTeacher">
           select * from student
       </select>
       <resultMap id="StudentTeacher" type="Student">
           <result column="id" property="tid"></result>
           <result column="name" property="name"></result>
   <!--        复杂查询，多对一-->
           <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"></association>
       </resultMap>
       <select id="getTeacher" resultType="Teacher">
           select * from teacher where tid=#{tid}
       </select>
   ```

#### 2、一对多

###### 老师对多学生

```private List<Student> students;```

1. ###### 子表查询

   ```java
   <!--    子表查询-->
       <select id="GetTeacher1" resultMap="TeacherStudent1">
           select * from teacher where id=#{tid}
   </select>
   
       <resultMap id="TeacherStudent1" type="Teacher">
           <collection property="students" javaType="ArrayList" ofType="Student" select="getStuent" column="id">
           </collection>
       </resultMap>
   
       <select id="getStuent" resultType="Student">
           select * from student where tid=#{tid}
       </select>
   ```

2. ###### 联表查询

   ```java
   <!--按结果嵌套查询-->
       <select id="GetTeacher" resultMap="TeacherStudent">
           select s.id sid,s.name sname,t.name tname,t.id tid
           from student s,teacher t
           where s.tid=t.id and t.id=#{tid}
       </select>
   
       <resultMap id="TeacherStudent" type="Teacher">
           <result property="name" column="tname"></result>
           <result property="id" column="tid"></result>
           <collection property="students" ofType="Student">
               <result property="id" column="sid"></result>
               <result property="name" column="sname"></result>
               <result property="tid" column="tid"></result>
           </collection>
       </resultMap>
   ```