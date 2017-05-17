
## 1. **Mybatis简介**
MyBatis 是支持普通 SQL查询，存储过程和高级映射的优秀持久层框架。MyBatis 消除了几乎所有的JDBC代码和参数的手工设置以及结果集的检索。MyBatis 使用简单的 XML或注解用于配置和原始映射，将接口和 Java 的POJOs（Plain Old Java Objects，普通的 Java对象）映射成数据库中的记录。
每个MyBatis应用程序主要都是使用SqlSessionFactory实例的，一个SqlSessionFactory实例可以通过SqlSessionFactoryBuilder获得。SqlSessionFactoryBuilder可以从一个xml配置文件或者一个预定义的配置类的实例获得。
用xml文件构建SqlSessionFactory实例是非常简单的事情。推荐在这个配置中使用类路径资源（classpath resource)，但你可以使用任何Reader实例，包括用文件路径或file://开头的url创建的实例。MyBatis有一个实用类----Resources，它有很多方法，可以方便地从类路径及其它位置加载资源。

## 2. **开始前准备**
---
### 2.1 创建测试项目，普通java项目或者是JavaWeb项目均可
![这里写图片描述](http://img.blog.csdn.net/20160227230004435)
### 2.2 添加依赖jar包
* mybatis：**mybatis-3.2.0.jar**
* mysql: **mysql-connector-java-5.1.25.jar** 连接数据库
* log4j: **log4j-1.2.13.jar** 测试时需要
 
 ![这里写图片描述](http://img.blog.csdn.net/20160227230642177)

### 2.3 数据库准备
```sql
CREATE DATABASE mybatis;
USE mybatis;
DROP TABLE IF EXISTS `users`;
CREATE TABLE `users` (
  `id` INT(11) NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(100) NOT NULL,
  `age` INT(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB CHARSET=utf8;
```

## 3.  **Mybatis**实例入门

### 3.1  整体代码结构

![这里写图片描述](http://img.blog.csdn.net/20160227231106353)

### 3.2 创建实体对象**User.java**
```java
package com.stephen.mybatis.model;

public class User {

    private Integer id;
    private String name;
    private Integer age;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User [id=" + id + ", name=" + name + ", age=" + age + "]";
    }
}

```

### 3.2  添加mybatis配置文件**mybatis-config.xml**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!-- 配置configuration -->
<configuration>
    <!-- 加载配置文件 -->
    <!--可以使用配置文件
    <properties resource="jdbc.properties"></properties>
    -->

    <!-- 定义具体的别名 -->
    <typeAliases>
        <!-- 定义某个类的别名
            <typeAlias alias="User" type="com.stephen.mybatis.model.User"/>
         -->
        <!-- package这个包下的所有的实体类的别名为其类名 -->
        <package name="com.stephen.mybatis.model"/>
    </typeAliases>

    <!-- 多个环境，如开发环境，正式环境等 -->
    <environments default="development">
        <!-- 定义development环境 -->
        <environment id="development">
            <!-- 事务管理的类型JDBC和MANAGED（容器管理模式） -->
            <transactionManager type="JDBC" />

            <!-- 数据源配置
                UNPOOLED：开一个，用完后关闭
                POOLED：数据库连接池
                JNDI：JNDI的数据源
            -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver" />
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis" />
                <property name="username" value="root" />
                <property name="password" value="root" />
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <!-- 注册UserMapper.xml文件 ,所有的mapper文件需要注册到mappers中-->
        <mapper resource="com/stephen/mybatis/model/UserMapper.xml"/>
     </mappers>
</configuration>
```

### 3.3 添加实体对象的Mapper文件**UserMapper.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- 为这个mapper指定一个唯一的namespace，namespace的值习惯上设置成包名+sql映射文件名，
这样就能够保证namespace的值是唯一的 -->
<mapper namespace="com.stephen.mybatis.model.UserMapper">
    <!-- 配置一个结果映射对象 -->
    <resultMap type="com.stephen.mybatis.model.User" id="userMap">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="age" column="age"/>
    </resultMap>

    <!-- 
        id:在命名空间中唯一的标识符,可以被用来引用这条语句。
        parameterType:将会传入这条语句的参数类的完全限定名或别名。
        parameterMap:这是引用外部 parameterMap 的已经被废弃的方法。使用内联参数映射和 parameterType 属性。
        resultType:属性指明查询返回的结果集类型,User类就是users表所对应的实体类
        useGeneratedKeys:主键自动增长的功能. 『注意Oracle主键不自增』.
    -->
    <!-- 注意：
        1.#传出的参数会被加''进行转移处理，可以很大程度的上防止SQL注入，使用 $传得值是直接替换，不能防止sql注入
        2.CDATA全名:character data，在标记CDATA下，所有的标记、实体引用都被忽略，而被XML处理程序一视同仁地当做字符数据看待，CDATA的形式如下：  <![CDATA[文本内容]]> CDATA的文本内容中不能出现字符串“]]>”，另外，CDATA不能嵌套。
    -->
    <!-- 还记得我们在配置文件如下，这就是将我们model下的类取别名，parameterType="User"就是配置起的作用，
    -等价于parameterType="com.stephen.mybatis.model.User"
        <typeAliases><package name="com.stephen.mybatis.model"/></typeAliases>
     -->
    <insert id="add" parameterType="User" useGeneratedKeys="true" keyProperty="id">
        <![CDATA[
            INSERT INTO users(name, age) VALUES(#{name}, #{age})
        ]]>
    </insert>

    <select id="get" resultMap="userMap" parameterType="Integer">
        <![CDATA[
            SELECT * FROM users WHERE id = #{id}
        ]]>
    </select>

    <delete id="delete" parameterType="Integer">
        <![CDATA[
            DELETE FROM users WHERE id = #{id}
        ]]>
    </delete>

    <update id="update" parameterType="User">
        <![CDATA[
            UPDATE users SET name = #{name}, age = #{age} WHERE id = #{id}
        ]]>
    </update>

</mapper>
```
**其他属性介绍**:

* **flushCache**:  将其设置为 true,不论语句什么时候被带哦用,都会导致缓存被清空。默认值:false。
* **timeout**: 这个设置驱动程序等待数据库返回请求结果, 并抛出异常时间的最大等待值。默认不设置(驱动自行处理)。
* **statementType**:  STA TEMENT,PREPARED 或 CALLABLE 的一种。这会让 MyBatis 使用选择使用 Statement,PreparedStatement 或 CallableStatement。默认值:PREPARED。
* **useGeneratedKeys**:  ( 仅 对 insert 有 用 ) 这 会 告 诉 MyBatis 使 用 JDBC 的 getGeneratedKeys 方法来取出由数据(比如:像 MySQL 和 SQL Server 这样的数据库管理系统的自动递增字段)内部生成的主键。默认值:false。
* **keyProperty** :(仅对 insert 有用) 标记一个属性, MyBatis 会通过 getGeneratedKeys 或者通过 insert 语句的 selectKey 子元素设置它的值。默认: 不设置。
* **keyColumn**: (仅对 insert 有用) 标记一个属性, MyBatis 会通过 getGeneratedKeys 或者通过 insert 语句的 selectKey 子元素设置它的值。默认: 不设置。

### 3.4 测试类**Mian.java**

```java
package com.stephen.mybatis;

import java.io.IOException;
import java.io.InputStream;
import java.io.Reader;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import com.stephen.mybatis.model.User;

/**
 * 
 * @author Stephen Huang
 * 
 */
public class Main {

    public static void main(String[] args) {
        //mybatis的配置文件
        String resource = "mybatis-config.xml";
        //获取session，两种方式
        SqlSession session = getSqlSession1(resource);
//        SqlSession session = getSqlSession2(resource);

        //根据映射sql的标识字符串执行CRUD
        String add = "com.stephen.mybatis.model.UserMapper.add";
        String get = "com.stephen.mybatis.model.UserMapper.get";
        String update = "com.stephen.mybatis.model.UserMapper.update";
        String delete = "com.stephen.mybatis.model.UserMapper.delete";
        
        //add test
        User user = new User();
        user.setName("stephen");
        user.setAge(18);
        session.insert(add, user);
        System.out.println("add user:" + user);
        
        //get test
        user = session.selectOne(get, user.getId());
        System.out.println("get user:" + user);
        //update test
        user.setAge(28);
        session.update(update, user);
        System.out.println("update user age=28:" + user);
        //delete test
        System.out.println("delete user id:" + user.getId());
        session.delete(delete, user.getId());
        System.out.println(session.selectOne(get, user.getId()));
    }

    public static SqlSession getSqlSession1(String resource) {
        SqlSession session = null;
        try {
            //使用MyBatis提供的Resources类加载mybatis的配置文件（它也加载关联的映射文件）
            Reader reader = Resources.getResourceAsReader(resource);
            //构建sqlSession的工厂
            SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(reader);
            //创建能执行映射文件中sql的sqlSession
            session = sessionFactory.openSession();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return session;
    }

    public static SqlSession getSqlSession2(String resource) {
        SqlSession session = null;
        try {
            //使用类加载器加载mybatis的配置文件（它也加载关联的映射文件）
            InputStream is = Main.class.getClassLoader().getResourceAsStream(resource);
            //构建sqlSession的工厂
            SqlSessionFactory sessionFactory = new SqlSessionFactoryBuilder().build(is);
            //创建能执行映射文件中sql的sqlSession
            session = sessionFactory.openSession();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return session;
    }
}

```

### 4 测试结果
```
log4j:WARN No appenders could be found for logger (org.apache.ibatis.logging.LogFactory).
log4j:WARN Please initialize the log4j system properly.
add user:User [id=9, name=stephen, age=18]
get user:User [id=9, name=stephen, age=18]
update user age=28:User [id=9, name=stephen, age=28]
delete user id:9
null

```

[**GitHub代码参考下载地址**](https://github.com/StephenTao/mybatis_demo)
