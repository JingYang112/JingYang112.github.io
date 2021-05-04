---
title: Sprng知识点
date: 2021-05-04 17:14:00
author: yangjing
summary: spring学习记录
tags: 
  - Spring
---



# spring笔记

## 一、Spring注入对象属性的方式

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="ad" class="com.yang.Adress">
        <property name="adress" value="紫禁城"></property>
    </bean>
<!--    第一种：普通属性-->
    <bean id="student" class="com.yang.Student" name="st" >
        <property name="name" value="你好，我是小爱同学"></property>
        <!--    第二种：对象属性 -->
        <property name="adress" ref="ad"></property>
<!--      第三种：数组注入-->
        <property name="books" >
            <array>
                <value>三国演义</value>
                <value>水浒传</value>
                <value>红楼梦</value>
                <value>西游记</value>
            </array>
        </property>
<!--        第四种：list-->
        <property name="hobbys">
            <list>
                <value>打球</value>
                <value>下棋</value>
                <value>看书</value>
            </list>
        </property>
<!--        第五种：map-->
        <property name="card">
            <map>
                <entry value="23425425223" key="银行卡"></entry>
                <entry value="1241325345" key="学生卡"></entry>
            </map>
        </property>
<!--        第六种：set-->
        <property name="games">
            <set>
                <value>LOL</value>
                <value>COC</value>
                <value>BOB</value>
            </set>
        </property>
<!--        第七种：空指针-->
        <property name="wifi">
            <null></null>
        </property>
<!--        第八种：配置文件-->
        <property name="info">
            <props >
                <prop key="uuid">10086</prop>
                <prop key="username">yangjing</prop>
                <prop key="pwd">123456</prop>
                <prop key="flag">ture</prop>
            </props>
        </property>
    </bean>
</beans>
```

## 二、C命名和P命名空间

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd"
         xmlns:p="http://www.springframework.org/schema/p"
         xmlns:c="http://www.springframework.org/schema/c">

    <!--p命名空间直接注入属性的值 无参构造-->
    <!--    <bean id="u" class="com.yang.User" p:name="帅的人" p:sex="男">-->

    <!--    </bean>-->
    <!-- c命名空间通过构造器注入  有参构造-->
    <bean id="u2" class="com.yang.User" c:name="yang" c:sex="男"></bean>
</beans>
```

## 三、调用容器对象的方式

```
import com.yang.dao.UserDaoImpl;
import com.yang.service.UserServiceImpl;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test1 {
    @Test
    public  void KK(){
//      获取aplicationContext
        ApplicationContext Context = new ClassPathXmlApplicationContext("beans.xml");
        UserServiceImpl userService = (UserServiceImpl) Context.getBean("UserServiceImpl");
        userService.GetUser();
    }
}
```

## 四、自动装配、注解开发

```

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
<!--   指定要扫描的包，包下注解全部生效-->
    <context:component-scan base-package="com.yang.pojo"/>
<!--    开启注解的支持-->
    <context:annotation-config/>
    <bean id="dog" class="com.yang.Dog"></bean>
    <bean id="cat2" class="com.yang.Cat"></bean>
    <bean id="cat1" class="com.yang.Cat"></bean>
<!--    自动装配，ByName id需要与GEt Set属性名对应   -->
<!--    自动装配，ByNType 类型不能重复 class里面的包类型 -->
    <bean id="pe" class="com.yang.People">
     </bean>
</beans>
```

### 注解

#### UserConfig

```
package com.yang.config;

import com.yang.pojo.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.jws.soap.SOAPBinding;

@Configuration
public class UserConfig {
    @Bean
    public User getUser(){
        return new User();
    }
}
```

#### User

```
package com.yang.config;

import com.yang.pojo.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.jws.soap.SOAPBinding;

@Configuration
public class UserConfig {
    @Bean
    public User getUser(){
        return new User();
    }
}
```

#### MyTest

```
import com.yang.config.UserConfig;
import com.yang.pojo.User;
import jdk.nashorn.internal.runtime.regexp.joni.Config;
import org.junit.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContextExtensionsKt;

public class MyTest {
    @Test
    public  void GetUserName(){
        AnnotationConfigApplicationContext Context = new AnnotationConfigApplicationContext(UserConfig.class);
        User getuser = (User) Context.getBean("getUser");
        System.out.println(getuser.getName());
    }

}
```

## 五、动态代理

### 一、ProxyinnovatioHandler

```
package com.yang.demo04;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyinnovatioHandler implements InvocationHandler {
    //被代理的接口
    private Object target;

//    public void setRent() {
//        this.rent = rent;
//    }

    public void setTarget(Object target) {
        this.target = target;
    }

    //生成得到代理类
    public Object getProxy(){
       return Proxy.newProxyInstance(this.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
    }

//    处理代理实例并返回结果
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        log(method.getName());
        //动态代理类的本质使用反射实现
        Object result = method.invoke(target,args);//动态生成，没有去写它
        return result;
    }
    public void log(String msg){
        System.out.println("执行了："+msg+"方法");
    }
}
```

### 二、Client

```
package com.yang.demo04;

import com.yang.demo02.UserService;
import com.yang.demo02.UserServiceImpl;

public class Client {
    public static void main(String[] args) {
        //真是角色
        UserServiceImpl userService = new UserServiceImpl();

        ProxyinnovatioHandler pih = new ProxyinnovatioHandler();
        //设置代理对象
        pih.setTarget(userService);
        //动态生成代理类
        UserService proxy = (UserService) pih.getProxy();
        proxy.add();
    }

}
```

## 六、Aop

### 一、applicationContext.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/aop
       https://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="userService" class="com.yang.service.UserServiceImpl"></bean>
    <bean id="log" class="com.yang.log.Log"></bean>
    <bean id="afterLog" class="com.yang.log.AfterLog"></bean>
<!--    方法三：注解支持-->
    <bean id="dy" class="com.yang.diy.AnnotationCutpoint"></bean>

<!--    开启注解支持 aip(false)默认 和glib-->
    <aop:aspectj-autoproxy proxy-target-class="false"></aop:aspectj-autoproxy>
<!--&lt;!&ndash;方法一：使用原生spring AIP 接口&ndash;&gt;-->
<!--&lt;!&ndash;    配置aop：需要导入aop的约束 aip接口&ndash;&gt;-->
<!--    <aop:config>-->
<!--        &lt;!&ndash;        切入点 * * *&ndash;&gt;-->
<!--        <aop:pointcut id="pointcut" expression="execution(* com.yang.service.UserServiceImpl.*(..))"/>-->
<!--&lt;!&ndash;        执行环绕添加&ndash;&gt;-->
<!--        <aop:advisor advice-ref="log" pointcut-ref="pointcut"></aop:advisor>-->
<!--        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"></aop:advisor>-->
<!--    </aop:config>-->
<!--方法二：自定义类 (切面定义)-->
<!--    <bean id="diy" class="com.yang.diy.DiyPointcut"></bean>-->
<!--ref 引入要切入的类-->
<!--    <aop:config>-->
<!--        <aop:aspect ref="diy">-->
<!--            &lt;!&ndash;        切入点 * * *&ndash;&gt;-->
<!--            <aop:pointcut id="pointcut" expression="execution(* com.yang.service.UserServiceImpl.*(..))"/>-->
<!--            &lt;!&ndash; 通知&ndash;&gt;-->
<!--            <aop:before method="before" pointcut-ref="pointcut"></aop:before>-->
<!--            <aop:after method="after" pointcut-ref="pointcut"></aop:after>-->
<!--        </aop:aspect>-->
<!--    </aop:config>-->

</beans>
```

### 二、自定义切面类AnnotationCutpoint

```
package com.yang.diy;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect
public class AnnotationCutpoint {

    @Before("execution(* com.yang.service.UserServiceImpl.*(..))")
    public void before(){
        System.out.println("*********方法执行前*******");
    }
    @After("execution(* com.yang.service.UserServiceImpl.*(..))")
    public void after(){
        System.out.println("*********方法执行后*******");
    }
//环绕增强中传参
    @Around("execution(* com.yang.service.UserServiceImpl.*(..))")
    public void Aroud(ProceedingJoinPoint jpt) throws Throwable {
        System.out.println("环绕前");
        Object proceed = jpt.proceed();//执行方法
//        System.out.println(jpt.getSignature());
        //
        System.out.println("环绕后");
    }
}
```

## 七、整合mybatis

* mybatis-config.xml

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置-->
<configuration>

<!--    <properties resource="db.properties"></properties>-->
<!--    别名-->
<!-- 设置-->
<!--    <settings>-->
<!--        <setting name="" value=""/>-->
<!--    </settings>-->

    <typeAliases>
        <package name="com.yang.pojo"/>
    </typeAliases>

<!--    <mappers>-->
<!--        <mapper class="com.yang.mapper.UserMapper"/>-->
<!--    </mappers>-->
</configuration>
```

* spring-dao.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">


<!--   DataSource :使用spring 数据源替换mybatis-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url"
                  value="jdbc:mysql://localhost:3306/user?useUnicode=true&amp;characterEncoding=utf-8&amp;useSSL=false&amp;serverTimezone = GMT"/>
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </bean>
<!--    sqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!--    绑定mybatis配置文件-->
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath:com/yang/mapper/*.xml"/>
    </bean>
<!--整合第一种方式-->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate" >
        <!--        使用构造器注入SqlSessionFactory，因为它没有set方法-->
        <constructor-arg index="0" ref="sqlSessionFactory"/>
    </bean>
</beans>
```

* applicationContext.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <import resource="spring-dao.xml"/>

    <bean id="userMapper" class="com.yang.mapper.UserMapperImpl">
<!--        <property name="sqlSession" ref="sqlSession"/>-->
        <property name="sqlSession" ref="sqlSession"/>
    </bean>

    <bean id="userMapper2" class="com.yang.mapper.UserMapperImpl2">
        <property name="sqlSessionFactory" ref="sqlSessionFactory" />
    </bean>

</beans>
```



## 八、事务

###### spring-dao.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">


<!--   DataSource :使用spring 数据源替换mybatis-->
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
    <property name="url"
              value="jdbc:mysql://localhost:3306/user?useUnicode=true&amp;characterEncoding=utf-8&amp;useSSL=false&amp;serverTimezone = GMT"/>
    <property name="username" value="root"/>
    <property name="password" value="123456"/>
</bean>
<!--    sqlSessionFactory-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource" />
    <!--    绑定mybatis配置文件-->
    <property name="configLocation" value="classpath:mybatis-config.xml"/>
    <property name="mapperLocations" value="classpath:com/yang/mapper/*.xml"/>
</bean>
<!--整合第一种方式-->
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate" >
    <!--        使用构造器注入SqlSessionFactory，因为它没有set方法-->
    <constructor-arg index="0" ref="sqlSessionFactory"/>
</bean>

<!--    配置声明式事务-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <constructor-arg ref="dataSource" />
</bean>

<!--    结合AOP实现事务的织入-->
<!--    配置事务通知-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <!--        给那些方法配置事务-->
    <!--        配置事务的传播特性-->
    <tx:attributes>
        <tx:method name="add" propagation="REQUIRED"/>
        <tx:method name="delete" propagation="REQUIRED"/>
        <tx:method name="insert" propagation="REQUIRED"/>
        <tx:method name="update" propagation="REQUIRED"/>
        <tx:method name="*" propagation="REQUIRED"/>
    </tx:attributes>
</tx:advice>

<!--    设置事务切入-->
<aop:config>
    <aop:pointcut id="txPointCut" expression="execution(* com.yang.mapper.*.*(..))"/>
    <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
</aop:config>
</beans>
```

###### applicationContext.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <import resource="spring-dao.xml"/>

    <bean id="userMapper" class="com.yang.mapper.UserMapperImpl">
        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    </bean>

</beans>
```

###### mybatis-config.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置-->
<configuration>

<!--    <properties resource="db.properties"></properties>-->
<!--    别名-->
<!-- 设置-->
<!--    <settings>-->
<!--        <setting name="" value=""/>-->
<!--    </settings>-->

    <typeAliases>
        <package name="com.yang.pojo"/>
    </typeAliases>

<!--    <mappers>-->
<!--        <mapper class="com.yang.mapper.UserMapper"/>-->
<!--    </mappers>-->
</configuration>
```

## UserMapper

```java
package com.yang.mapper;

import com.yang.pojo.User;

import java.util.List;

public interface UserMapper {
    List<User> getUserList();
    //     增加用户
    public int addUser(User user);
    //删除
    public void deleUser(Integer id);
    //修改用户
    public int update(int id);
    //查询用户
    public int selectuser(int id);
}
```

## UserMapperImpl

```java
package com.yang.mapper;

import com.yang.pojo.User;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.support.SqlSessionDaoSupport;

import java.util.List;

public class UserMapperImpl extends SqlSessionDaoSupport implements UserMapper {

//    使用sqlseesion来做，现在是sqlssesiontemple

    public List<User> getUserList() {

        User user = new User(13,"yang","男",15);
        UserMapper mapper = getSqlSession().getMapper(UserMapper.class);
        mapper.addUser(user);
        mapper.deleUser(11);
        return  mapper.getUserList();
    }

    public int addUser(User user) {
        return getSqlSession().getMapper(UserMapper.class).addUser(user);
    }

    public void deleUser(Integer id) {
        getSqlSession().getMapper(UserMapper.class).deleUser(id);
    }

    public int update(int id) {
        return 0;
    }

    public int selectuser(int id) {
        return 0;
    }
}
```