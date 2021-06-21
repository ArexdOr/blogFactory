# Mybatis

## 1. MyBatis简介

###  1.1 什么是Mybatis

- MyBatis 是一款优秀的**持久层框架**
- MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集的过程
- MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 实体类 【Plain Old Java Objects,普通的 Java对象】映射成数据库中的记录。
- MyBatis 本是apache的一个开源项目ibatis, 2010年这个项目由apache 迁移到了google code，并且改名为MyBatis 。
- 2013年11月迁移到**Github** .
- Mybatis官方文档 : http://www.mybatis.org/mybatis-3/zh/index.html
- GitHub : https://github.com/mybatis/mybatis-3

### 1.2 持久化

**持久化是将程序数据在持久状态和瞬时状态间转换的机制。**

- 即把数据（如内存中的对象）保存到可永久保存的存储设备中（如磁盘）。持久化的主要应用是将内存中的对象存储在数据库中，或者存储在磁盘文件中、XML数据文件中等等。
- JDBC就是一种持久化机制。文件IO也是一种持久化机制。
- 在生活中 : 将鲜肉冷藏，吃的时候再解冻的方法也是。将水果做成罐头的方法也是。

**为什么需要持久化服务呢？那是由于内存本身的缺陷引起的**

- 内存断电后数据会丢失，但有一些对象是无论如何都不能丢失的，比如银行账号等，遗憾的是，人们还无法保证内存永不掉电。
- 内存过于昂贵，与硬盘、光盘等外存相比，内存的价格要高2~3个数量级，而且维持成本也高，至少需要一直供电吧。所以即使对象不需要永久保存，也会因为内存的容量限制不能一直呆在内存中，需要持久化来缓存到外存。

> 持久层

### **1.3 持久层**

- 完成持久化工作的代码块 .  ---->  dao层 【DAO (Data Access Object)  数据访问对象】
- 大多数情况下特别是企业级应用，数据持久化往往也就意味着将内存中的数据保存到磁盘上加以固化，而持久化的实现过程则大多通过各种**关系数据库**来完成。
- 不过这里有一个字需要特别强调，也就是所谓的“层”。对于应用系统而言，数据持久功能大多是必不可少的组成部分。也就是说，我们的系统中，已经天然的具备了“持久层”概念？也许是，但也许实际情况并非如此。之所以要独立出一个“持久层”的概念,而不是“持久模块”，“持久单元”，也就意味着，我们的系统架构中，应该有一个相对独立的逻辑层面，专注于数据持久化逻辑的实现.
- 与系统其他部分相对而言，这个层面应该具有一个较为清晰和严格的逻辑边界。【说白了就是用来操作数据库存在的！】

#### 为什么需要Mybatis

- Mybatis就是帮助程序猿将数据存入数据库中 , 和从数据库中取数据 .

- 传统的jdbc操作 , 有很多重复代码块 .比如 : 数据取出时的封装 , 数据库的建立连接等等... , 通过框架可以减少重复代码,提高开发效率 .

- MyBatis 是一个半自动化的**ORM框架 (Object Relationship Mapping) -->对象关系映射**

- 所有的事情，不用Mybatis依旧可以做到，只是用了它，所有实现会更加简单！**技术没有高低之分，只有使用这个技术的人有高低之别**

- MyBatis的**优点**

- - 简单易学：本身就很小且简单。没有任何第三方依赖，最简单安装只要两个jar文件+配置几个sql映射文件就可以了，易于学习，易于使用，通过文档和源代码，可以比较完全的掌握它的设计思路和实现。
  - 灵活：mybatis不会对应用程序或者数据库的现有设计强加任何影响。sql写在xml里，便于统一管理和优化。通过sql语句可以满足操作数据库的所有需求。
  - 解除sql与程序代码的耦合：通过提供DAO层，将业务逻辑和数据访问逻辑分离，使系统的设计更清晰，更易维护，更易单元测试。sql和代码的分离，提高了可维护性。
  - 提供xml标签，支持编写动态sql。
  - .......

- 最重要的一点，使用的人多！公司需要！

## 2. 入门

**思路流程：搭建环境-->导入Mybatis--->编写代码--->测试**

> 代码演示

### 1、搭建实验数据库

```sql
CREATE DATABASE `mybatis`;

USE `mybatis`;

DROP TABLE IF EXISTS `user`;

CREATE TABLE `user` (
`id` int(20) NOT NULL,
`name` varchar(30) DEFAULT NULL,
`pwd` varchar(30) DEFAULT NULL,
PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

insert  into `user`(`id`,`name`,`pwd`) values (1,'狂神','123456'),(2,'张三','abcdef'),(3,'李四','987654');
```

### 2、导入MyBatis相关 jar 包

- GitHub上找

```xml
<dependency>
   <groupId>org.mybatis</groupId>
   <artifactId>mybatis</artifactId>
   <version>3.5.2</version>
</dependency>
<dependency>
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
   <version>5.1.47</version>
</dependency>
```

### 3、编写MyBatis核心配置文件

- 查看帮助文档

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
       PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
   <environments default="development">
       <environment id="development">
           <transactionManager type="JDBC"/>
           <dataSource type="POOLED">
               <property name="driver" value="com.mysql.jdbc.Driver"/>
               <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=utf8"/>
               <property name="username" value="root"/>
               <property name="password" value="123456"/>
           </dataSource>
       </environment>
   </environments>
   <mappers>
       <mapper resource="com/kuang/dao/userMapper.xml"/>
   </mappers>
</configuration>
```

### 4、编写MyBatis工具类

- 查看帮助文档

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.IOException;
import java.io.InputStream;

public class MybatisUtils {

   private static SqlSessionFactory sqlSessionFactory;

   static {
       try {
           String resource = "mybatis-config.xml";
           InputStream inputStream = Resources.getResourceAsStream(resource);
           sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
      } catch (IOException e) {
           e.printStackTrace();
      }
  }

   //获取SqlSession连接
   public static SqlSession getSession(){
       return sqlSessionFactory.openSession();
  }

}
```

### 5、创建实体类

```java
public class User {
   
   private int id;  //id
   private String name;   //姓名
   private String pwd;   //密码
   
   //构造,有参,无参
   //set/get
   //toString()
   
}
```

### 6、编写Mapper接口类

```java
import com.kuang.pojo.User;
import java.util.List;

public interface UserMapper {
   List<User> selectUser();
}
```

### 7、编写Mapper.xml配置文件

- namespace 十分重要，不能写错！

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
       PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.kuang.dao.UserMapper">
 <select id="selectUser" resultType="com.kuang.pojo.User">
  select * from user
 </select>
</mapper>
```

### 8、编写测试类

- Junit 包测试

```java
public class MyTest {
   @Test
   public void selectUser() {
       SqlSession session = MybatisUtils.getSession();
       //方法一:
       //List<User> users = session.selectList("com.kuang.mapper.UserMapper.selectUser");
       //方法二:
       UserMapper mapper = session.getMapper(UserMapper.class);
       List<User> users = mapper.selectUser();

       for (User user: users){
           System.out.println(user);
      }
       session.close();
  }
}
```

### 9、运行测试，

成功的查询出来的我们的数据，ok！



> 问题说明

#### 重要 ！**可能出现问题说明：Maven静态资源过滤问题**

```xml
<resources>
   <resource>
       <directory>src/main/java</directory>
       <includes>
           <include>**/*.properties</include>
           <include>**/*.xml</include>
       </includes>
       <filtering>false</filtering>
   </resource>
   <resource>
       <directory>src/main/resources</directory>
       <includes>
           <include>**/*.properties</include>
           <include>**/*.xml</include>
       </includes>
       <filtering>false</filtering>
   </resource>
</resources>
```

有了MyBatis以后再也不用写原生的JDBC代码了，舒服！

### 10思路总结

#### 	单词:

​		mapper---映射器

#### 	背景：

​		1.你首先需要连接Mysql的数据库(输入正确的数据库用户名、密码、想要连接的数据库名、以及URL例如：jdbc:mysql://localhost:3306/mybatis)
​		2.在pom.xml里面导入所依赖的jar包(以下这三个)

```xml
<!--导入依赖-->
    <dependencies>
        <!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <!--mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

​	顺便还有如果你想在非reources文件夹下加配置文件，maven是可能读取不到的，因为默认就是reources文件夹,需要在pom.xml的project标签中添加以下代码

```xml
<!--在pom.xml的build中配置resource，我们希望在java文件中的配置文件也能生效，
    以下代码防止了我们资源导出失败的问题-->
    <build>
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>
```

#### 	实操:

##### 		1.创建工具类和核心配置文件

​		我们想要和数据库进行连接，就需要一个工具类(格式:数据库名Utils  如:MyBatisUtils)来获取数据库连接对象。
​			1.1连接过程中我们需要调用mybatis-config.xml核心配置文件,所以需要编写下代码,如下

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--configuration：核心配置文件-->
<!--作用:连接数据库-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <!--主要修改以下三个property和最下面的mapper标签即可了-->
                <!--&amp;就相当于 & 符号，只是在xml中需要些转义-->
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=utf8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>

       <!--
       我们如果想自定义一个test环境也是可以的，再将environments的default设置为test
       <environment id="test">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=utf8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
        -->
    </environments>
    
    <!--每一个Mapper.xml都需要在Mybatis核心配置文件中去注册-->
    <mappers>
        <mapper resource="com/kuang/dao/UserMapper.xml"/>
    </mappers>
</configuration>
```

​		1.2配置文件写完之后，我们再继续写工具类(MyBatisUtils)

```java
package com.kuang.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

/**这时MyBatis的工具类
 * @author <作者>
 * @version <版本>
 * @since <JDK版本>
 */
public class MyBatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    static {
        try {
            //使用MyBatis第一步：获取sqlSessionFactory对象
            String source = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(source);
            //建造一个数据库工厂
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    //要获取数据库对象就用这个类就可以了
    public static SqlSession getSqlSession(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //当然也可以直接return整合到一句，这里为了清晰就不弄了
        return sqlSession;
    }
}

```

##### 	2.创建一个与数据库属性对应的实体类

​	在文件夹pojo中(黑马中就是叫domain，两者没有差别),里面编写对应的私有成员变量，和无参，有参构造，对应的get和set，和tostring

##### 	3.编写对应的表的接口(老版的叫UserDao,新版的叫UserMapper)

​	以前我们需要什么表，就是什么Dao!，现在就是什么Mapper)!

​	里面编写一些我们的需求(也就是实际开发中的抽象方法)，比如我们想要查询所有用户信息，就需要一个List< User > getUserList的抽象方法来存放多个用户的信息,

##### 	4.重点,编写UserMapper.xml 

这就相当于之前我们中的UserDaoImpl实现方法，现在就是用了配置文件的方式去实现接口

​	重要属性解读：
​		namespcae：选择你需要绑定的表接口
​		< select></ select>:选择你所操作的方式，是update，insert还是什么根据实际要求去写

​		id：绑定接口里面的方法
​		resultType：取决于你这个方法的返回值类型
​		标签体内容：你选择的标签所对应的sql语句

```xml
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--
namespace 绑定一个对应的Dao/Mapper接口
相当于 java反射中class中的forname吧,Mybatis就会知道怎么去哪里找对应的接口
也就是说一个namespace的存在，就代表一个Dao接口 
-->
<mapper namespace="com.kuang.dao.UserDao">
    <!--
    查询语句:
        id:
            这就是在Dao/Mapper中的方法名，这个UserMapper.xml我感觉就是个Impl，
            id就是为哪个方法重写
        resultType:
            就是返回的类型，就相当于你在Impl中查询出来之后结果的类型
            我们这里要查的就是用户，因为是可能为多个那么肯定要用List集合，
            但本质存的还是User类型，我们这里要加全限定名
            resultType会对结果集封装
    -->
    <select id="getUserList" resultType="com.kuang.pojo.User">
        select * from  mybatis.user
    </select>
</mapper>
```

##### 5.编写Test方法，进行调试

```java
package com.kuang.dao;

import com.kuang.pojo.User;
import com.kuang.utils.MyBatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

/**
 * @author <作者>
 * @version <版本>
 * @since <JDK版本>
 */
public class UserDaoTest {
    @Test
    public void test01(){
        //获取数据库对象
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        /*try catch finally 为什么要这样干呢？
        	作用：主要是我们需要去关闭sqlSession，而如果报错了就无法正常关闭了，所以用finally就能不管发生什么都能正常去关闭它.
        */
        try{
            /*
        方式一
            执行SQL,从sqlSession中获取mapper
            也就是说，其实UserDao就相当于一个Mapper，想要获取Mapper，你肯定要将一个类的字节码传入
            就是你在UserMapper.xml里面对应的方法绑定的Mapper接口
            这里是反射，实现了一个接口(UserDao)，并且创建了实现类的实例
         */
            UserDao usedao = sqlSession.getMapper(UserDao.class);
            List<User> userList = usedao.getUserList();
            for (User user : userList) {
                System.out.println(user);
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            //不管怎么样都要关闭sqlSession
            sqlSession.close();
        }
    }
}

```

## 3. CRUD功能实现

​	CRUD知识回顾:crud是指在做计算处理时的增加(Create)、[检索](https://baike.baidu.com/item/检索/11003896)(Retrieve)、更新(Update)和删除(Delete)几个单词的首字母简写。crud主要被用在描述软件系统中数据库或者[持久层](https://baike.baidu.com/item/持久层/3584971)的基本操作功能。

### 	前提注意:增删改必须要提交事务，即执行commit方法

### 	思考:

​	我们需要增加我们的需求(增删改查)需要改哪些文件
![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\基本结构.png)

思考后，发现只需要改
	1.UserMapper(相当于操作数据库的接口，里面放抽象方法)
	2.UserMapper.xml(相当于操作数据库接口的实现类，只是用了配置文件的方式)
	3.UserMapperTest(一个测试类，有点像我们之前的service层，主要是调用我们在xml里面配置的一些实现方法)

### 步骤:

#### 	1.在UserMapper中添加新方法

​	用户(如管理系统的某个功能，管理者就是用户；如果是什么网站，那么用户就是浏览者)想要增加什么功能，我们就首先要创建个抽象方法
​	

```java
package com.kuang.dao;

import com.kuang.pojo.User;

import java.util.List;

/**
 * @author <作者>
 * @version <版本>
 * @since <JDK版本>
 */
public interface UserMapper {
    /**
     * 查询所有用户信息
     * @return 返回一个存放多个用户信息的列表
     */
    List<User> getUserList();

    /**
     * 根据id值来查询具体的某个用户信息
     * @param id 用户的id值
     * @return 返回某个具体的用户信息
     */
    User getUserById(int id);

    /**
     * 根据用户注册基本信息来向数据库添加用户
     * @param user 这里应该就是Service层封装好的用户注册的基本信息,不用在写什么int id,String name,String pwd了
     * @return 这里的int是返回受影响的行数
     */
    int addUser(User user);
}

```

#### 2.在UserMapper.xml中添加需要实现的对应方法的对应标签(insert、delete、update、select等标签)

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--
namespace 绑定一个表对应的Dao/Mapper接口
相当于 java反射中class中的forname吧,Mybatis就会知道怎么去哪里找对应的接口
就相当于以前黑马的时候一个表的Dao，这里com.kuang.dao.UserMapper就是User表的Dao
以前的UserDao转变为现在的UserMapper,这都是我们的编程规范
-->
<mapper namespace="com.kuang.dao.UserMapper">

    <!--
    查询语句:
        id:
            这就是在Dao/Mapper中的方法名，这个UserMapper.xml我感觉就是个表的Impl，
            id就是为哪个方法重写(对于以前DaoImpl来说)
        resultType:
            就是返回的类型，就相当于你在Impl中查询出来之后结果的类型
            我们这里要查的就是用户，因为是可能为多个那么肯定要用List集合，
            但本质存的还是User类型，我们这里要加全限定名
            resultType会对结果集封装
    -->
    <select id="getUserList" resultType="com.kuang.pojo.User">
        /*为什么要在表名前加mybatis(数据库名),因为我们在其他数据库中也有叫user表的，所以肯定要让mybatis知道我们要查询哪张表吧，之后都一样，都要让mybatis知道我们想删除哪个数据库中的哪个表....*/
        select * from  mybatis.user
    </select>

    <!--
    简单查询语句
    新知识点:
        parameterType:代表的是对应的方法的形参的类型，从左往右
    -->
    <select id="getUserById" parameterType="int" resultType="com.kuang.pojo.User">
                                            /*  id就是你形参的名字 */
        select * from mybatis.user where id = #{id}
    </select>

    <!--
    简单增加用户语句
        方法名叫addUser
        形参类型是一个user对象,里面存放着id，name，pwd属性
    -->
    <insert id="addUser" parameterType="com.kuang.pojo.User">
        /*
        getMapper是通过反射实例化的功能接口，所以可以忽略private权限,
          直接取到User里面的id，name，pwd属性
        */
        insert into mybatis.user (id, name, pwd) values (#{id},#{name},#{pwd})
    </insert>
</mapper>
```

#### 	3.编写对应的UserMapperTest类

​		这也就是模拟我们之前的web环境，哪些用户信息我们这里就自己给了，作用主要是为了看看mybatis能不能正常执行!选择对应的测试方法进行 一 一 执行即可

```java
package com.kuang.dao;

import com.kuang.pojo.User;
import com.kuang.utils.MyBatisUtils;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

import java.util.List;

/**
 * @author <作者>
 * @version <版本>
 * @since <JDK版本>
 */
public class UserMapperTest {
    @Test
    public void test01() {
        //获取sqlSession对象，以后可以放到外面，这样不用每次声明了，我们现在主要是熟悉下
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        try {
            /*
            执行SQL,从sqlSession中获取mapper
            也就是说，其实UserDao就相当于一个UserMapper，想要获取Mapper，你肯定要将一个类的字节码传入
            就是你在UserMapper.xml里面对应的方法绑定的Mapper接口
            这里是反射，实现了一个接口(UserDao)，并且创建了实现类的实例
         */
            UserMapper usedao = sqlSession.getMapper(UserMapper.class);
            List<User> userList = usedao.getUserList();
            for (User user : userList) {
                System.out.println(user);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //关闭sqlSession
            sqlSession.close();
        }
    }

    @Test
    public void Test02() {
        //获取sqlSession对象，以后可以放到外面，这样不用每次声明了，我们现在主要是熟悉下
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        /*
            获得接口
            就等价于之前我们之前的
            UserDao userDao = new UserDaoImpl();
        */
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        //调用方法
        User user = userMapper.getUserById(2);
        System.out.println(user);//User{id=2, name='李四', pwd='112421'}
        sqlSession.close();
    }

    /**
     * 注意：增删改需要提交事务
     */
    @Test
    public void Test03() {
        //1.获取sqlSession对象，以后可以放到外面，这样不用每次声明了，我们现在主要是熟悉下
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        //2.获取接口的字节码
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        /*第一种方式赋值:太麻烦了
        User user = new User();
        user.setId(4);
        user.setName("赵六");
        user.setPwd("168947");
        */

        /*
        第二种方式赋值:一般般
            对象通过有参构造直接赋值不是更快吗
        User user = new User(4,"赵六","168947");
        mapper.addUser(user);
        */

        /*
        第三种方式：快又简洁!
            匿名对象方式同时利用有参构造进行传值
         */
        int res = mapper.addUser(new User(4, "赵六", "168947"));
        //res代表插入成功的条数，大于0说明提交成功了，当然了这在企业开发中也不会这样写
        if (res > 0) {
            System.out.println("插入成功");
            //重点:需要提交事务
            sqlSession.commit();
        }
        //关闭
        sqlSession.close();
    }
}

```

### 技巧：万能Map

​	假如，在我们的实体类或者数据库的表中，有很多的参数或字段，这个时候如果用户只是想单单的修改一个密码（或者其他的都可以，如用户名），那我们只有两个构造，一个无参一个全参构造，那么肯定是要全部参数都传入给user对象，这样也太麻烦了，那也不可能写很多不同参数列表的构造方法取传参吧。所以这时候就需要用map来传入参数

```java
/*思考：现在我们可以正常的传入参数了，但是我们表中的字段很多呢，我们用户只是想单纯修改密码,
        但是我们如果现在只有两个构造，一个无参，一个全参，那么就必须把参数全部都填上，特别麻烦，
        当然了造很多很多的构造方法不是更加麻烦吗？
        所以为了解决这个方法，就有了map集合的方式来传入参数
*/
int res = mapper.updateUser(new User(3, "王虎", "129876"));
```

​	步骤还是那老三样，UserMapper（接口），UserMapper.xml（实现接口的配置文件）,UserMapperTest(测试类)

#### 	1.UserMapper

​	新增一个抽象方法就可以了

```java
/**
     * 用map键值对的方式去传入对应的属性来进行修改用户对应的信息，
     * @param map 第一个参数是参数名，如userId，第二个参数是参数值，如4
     * @return 返回修改成功的条数
     */
    int updateUserMap(Map<String, Object> map);
```

#### 	2.UserMapper.xml

​		与上面一样，新增一个对应的id(接口的方法名)，和parameterType(接口方法名的形参类型)

```xml
<!--用map的方式专注去修改用户的密码-->
<update id="updateUserMap" parameterType="map">
    /*传入的值就是我们在测试类中的map中的键名，但是被赋值给pwd字段的是键名所对应的值哦*/
    update mybatis.user set pwd = #{userPassword} where id = #{userId} ;
</update>
```

#### 	3.UserMapperTest

```java
/**
     * 我不用new什么对象了，直接用map的形式来具体修改用户的密码
     */
    @Test
    public void updateUserMap(){
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        Map<String, Object> map = new HashMap<>();
        //键就是我们在UserMapper.xml中设置的键名，值就是用户在前端页面输入的数据
        //可以看到，我们这时候就不用去创建那个1个id，1个pwd的构造方法了，这就很方便了
        map.put("userId",4); 
        map.put("userPassword",987214);
        int res = mapper.updateUserMap(map);
        if (res > 0){
            System.out.println("用map修改用户信息成功");
            sqlSession.commit();
        }
        sqlSession.close();
    }
```

#### 4.总结

##### 	(1)Map传递参数 parameterType="map"

​	直接在userMapper.xml中的sql语句中 取出在Test类中设置的键名(即pwd = #{userPassword})就可以了，不需要和实体类相关了，只是含义要相同，比如user对象里面的pwd字段，你用map的话你可以设置键名为map.put("userPassword".4214112) 。可以看出，pwd和userPassword的含义是相同的。这样看实体类就没什么多大存在的必要了

##### 	(2)user对象传递参数 parameterType="com.kuang.pojo.实体类"

​	需要在userMapper.xml中的sql语句中取出实体类对应的属性值，且不能不同，你user实体类中的成员变量是要和表的字段名保持一致的吧，那么你在sql语句中就必须要取一样的名字，即 你在test类通过全参构造来设置参数什么name，pwd，然后你在userMapper.xml中的sql语句就要这样取 xxxxxx where name = #{name} and pwd = #{pwd}
​	这就暴露出缺点了，你字段名一多，假设用户也只是想改个密码啥的，你还要很累的去创建一个新的构造方法，要么去一个个set，，， 

##### (3)只有一个基本数据类型参数传递参数 parameterType="int"

​	最基本的一种，即在标签中 parameterType="int",注意！这时候形参只能有一个，如：方法 User getUserById(int id);
当然了，这个标签中的parameterType="int"可以去掉，都不影响，当然最好不要去掉,保证可读性

##### (4)多个参数用Map(比user对象好当然用map了)或者**注解**

​	注解没学到，之后会补充

### 技巧：模糊查询

**模糊查询like语句该怎么写?**

第1种：在Java代码中添加sql通配符。

```java
string wildcardname = "%王%";
list<name> names = mapper.selectlike(wildcardname);
/*这下面就是在UserMapper.xml中的配置*/
<select id=”selectlike”>
select * from foo where bar like #{value}
</select>
```

第2种：在sql语句中拼接通配符，我们用了#{}就不会引起sql注入了,业务中多用此种

```java
string wildcardname = "王";
list<name> names = mapper.selectlike(wildcardname);
/*这下面就是在UserMapper.xml中的配置*/
<select id=”selectlike”>
    select * from foo where bar like "%"#{value}"%"
</select>
```

## 4. 配置解析

### 	4.1 核心配置文件 mybatis-config.xml 

- mybatis-config.xml 系统核心配置文件
- MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。
- 能配置的内容如下：

```
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

### 	4.2 环境配置(environments)

​	MyBatis 可以配置成适应多种环境
​	**不过需要记住：尽管可以配置多个环境，但每个SqlSessionFactory实例只能选择一种环境.**

​	**目的**:学会使用配置多套运行环境

​	Mybatis默认的事务管理器就是JDBC(transactionManager type="JDBC")，
还有一个事务管理器叫Managed(不常用)，面试问都要记得答

​	Mybatis连接池默认:**POOLED**(dataSource type="POOLED")有连接池的意思,还有其他两个属性**UNPOOLED**(没有连接池的意思)，**JNDI**(正常连接)池的概念(连接完不着急关闭，而是回收到池子中，等待下次使用，使用起来速度更快)

### 4.3 属性(properties)

我们可以通过properties属性实现引用配置文件

这些属性都是可外部配置且可动态替换的，既可以在典型的java属性文件中配置，亦可通过properties元素的子元素来传递.

#### **编写一个配置文件**

##### 第一步：在资源目录下新建一个db.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=true&characterEncoding=utf8
username=root
password=123456
```

##### 第二步：在核心配置文件 mybatis-config.xml 中引入properties标签

```xml
<!--
        ps:我们直接在resources文件夹下，就不用写路径了
        作用:引入外部配置文件
    -->
    <properties resource="db.properties">
        <!--
        这里面也可以写db.properties的属性,这样写了db.properties里面的username和password键值对就可以删了
            首先读取在 properties 元素体内指定的属性。

            然后根据 properties 元素中的 resource 属性读取类路径下属性文件
            或根据 url 属性指定的路径读取属性文件，并覆盖之前读取过的同名属性。

            最后读取作为方法参数传递的属性，并覆盖之前读取过的同名属性。

            总结：也就是先读取的是properties元素体内指定的属性，如果没有再读db.properties的属性,
            如果属性两个地方都存在，那还是得听db.properties的属性，所以db.properties里面的属性千万不能错，
            这db.properties错了，你里面的属性对了也没用！因为会被db.properties的错误属性给覆盖掉
        -->
        <property name="username" value="root"/>
        <property name="password" value="123456"/>
    </properties>
```

​	老师的总结:

- 可以直接引入外部配置文件(db.properties)
- 可以在properties标签内部添加一些属性配置
- 如果两个文件有同一个字段，优先使用外部配置文件的!

### 4.4 类型别名(typeAliases)优化

​	**单词**:Aliases ==> 别名

​	**背景**:

- 类型名就是resultType="com.kuang.pojo.User" 中的com.kuang.pojo.User，是不是很长很不舒服，所以我们想取个别名，例如 User，这样返回值类型就好写很多了。
- 类型别名是为 Java 类型设置一个短的名字。它只和 XML 配置有关，存在的意义仅在于用来减少类完全限定名的冗余。

#### (1) 给实体类取别名

```xml
<typeAliases>
    <!--
        给实体类取别名
            好处:一劳永逸,以后你想用这个类型直接写别名就好了
            当这样配置时，User可以用在任何使用com.kuang.pojo.User的地方。
    -->
	<typeAlias alias="User" type="com.kuang.pojo.User"></typeAlias>
</typeAliases>
```

#### (2) 给一个包取别名

MyBatis 会在包名下面搜索需要的 Java Bean，比如:

```xml
<typeAliases>
	<package name="com.kuang.pojo"/>
</typeAliases>
```

**每一个**在包 `com.kuang.pojo` 中的 Java Bean(如User)，在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名(user)，当然了你大写(User)作为它的别名也是可以的。

#### (3) 总结

​	那到底怎么选择用哪种呢?
​	答:在实体类比较少的时候，使用第一种(给指定的实体类取别名),如果实体类十分多，建议使用第二种(给一个包取别名)
​	小区别:第一种可以DIY(自定义)别名，第二种则不行，如果你用第二种非要改别名，需要在对应的实体类上增加注解

```java
/*如 这样加了注解之后，你那个UserMapper.xml里面的returnType就要是hello了,但是其他没有加注解的类，它的别名就是实体类的首字母小写,当然我们上面也说了，首字母也可以大写
*/
@Alias("hello")
public class User(){
	...
}
```

### 4.5 设置(settings)

​	这时MyBatis中极为重要的调整设置，他们会改变MyBatis运行时的行为。

#### 以下三个设置需要牢记

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\设置图片1.png)

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\设置图片2.png)

- 设置（settings）相关 => 查看帮助文档，上面截图分别是

- - 日志实现
  - 缓存开启关闭
  - 懒加载

- 一个配置完整的 settings 元素的示例如下：

  ```xml
  <settings>
   <setting name="cacheEnabled" value="true"/>
   <setting name="lazyLoadingEnabled" value="true"/>
   <setting name="multipleResultSetsEnabled" value="true"/>
   <setting name="useColumnLabel" value="true"/>
   <setting name="useGeneratedKeys" value="false"/>
   <setting name="autoMappingBehavior" value="PARTIAL"/>
   <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
   <setting name="defaultExecutorType" value="SIMPLE"/>
   <setting name="defaultStatementTimeout" value="25"/>
   <setting name="defaultFetchSize" value="100"/>
   <setting name="safeRowBoundsEnabled" value="false"/>
   <setting name="mapUnderscoreToCamelCase" value="false"/>
   <setting name="localCacheScope" value="SESSION"/>
   <setting name="jdbcTypeForNull" value="OTHER"/>
   <setting name="lazyLoadTriggerMethods" value="equals,clone,hashCode,toString"/>
  </settings>
  ```

### 4.6 其他配置

#### **类型处理器【了解即可】**

- 无论是 MyBatis 在预处理语句（PreparedStatement）中设置一个参数时，还是从结果集中取出一个值时， 都会用类型处理器将获取的值以合适的方式转换成 Java 类型。
- 你可以重写类型处理器或创建你自己的类型处理器来处理不支持的或非标准的类型。【了解即可】

#### **对象工厂**【了解即可】

- MyBatis 每次创建结果对象的新实例时，它都会使用一个对象工厂（ObjectFactory）实例来完成。
- 默认的对象工厂需要做的仅仅是实例化目标类，要么通过默认构造方法，要么在参数映射存在的时候通过有参构造方法来实例化。
- 如果想覆盖对象工厂的默认行为，则可以通过创建自己的对象工厂来实现。【了解即可】

#### **plugins插件【了解即可】**

- mybatis-generator-core
- mybatis-plus
- 通用mapper

### 4.7 映射器(mappers)

MapperRegistry:注册绑定我们的Mapper文件(即那个接口)

#### 	方法一：通过resource进行绑定【推荐】

```xml
<mappers>
    <!--每一个Mapper.xml都需要在Mybatis核心配置文件中去注册-->
    <mapper resource="com/kuang/dao/UserMapper.xml"/>
</mappers>
```

#### 方法二：通过class文件绑定注册

```xml
<mappers>
    <!--每一个Mapper.xml都需要在Mybatis核心配置文件中去注册-->
    <!--通过接口进行绑定注册-->
    <mapper class="com.kuang.dao.UserMapper"/>
</mappers>
```

#### 	方法三：通过扫描包进行绑定

```xml
<mappers>
    <!--每一个Mapper.xml都需要在Mybatis核心配置文件中去注册-->
    <package name="com.kuang.dao"/>
</mappers>
```

#### 	方法二和方法三注意点:

- 接口和它的Mapper.xml 配置文件必须同名(如接口：UserMapper，配置文件名：UserMapper.xml)
- 接口和它的Mapper.xml 配置文件必须放在同一个包下!

### 4.8 生命周期和作用域

**作用域（Scope）和生命周期**

理解我们目前已经讨论过的不同作用域和生命周期类是至关重要的，因为错误的使用会导致非常严重的并发问题。

我们可以先画一个流程图，分析一下Mybatis的执行过程！

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\Mybatis的执行过程.png)

**作用域理解**

- SqlSessionFactoryBuilder 的作用在于创建 SqlSessionFactory，创建成功后，SqlSessionFactoryBuilder 就失去了作用，所以它只能存在于创建 SqlSessionFactory 的方法中，而不要让其长期存在。
  因此 **SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域**（也就是局部方法变量）。
- SqlSessionFactory 可以被认为是一个数据库连接池，它的作用是创建 SqlSession 接口对象。因为 MyBatis 的本质就是 Java 对数据库的操作，所以 SqlSessionFactory 的生命周期存在于整个 MyBatis 的应用之中，所以一旦创建了 SqlSessionFactory，就要长期保存它，直至不再使用 MyBatis 应用，所以可以认为 SqlSessionFactory 的生命周期就等同于 MyBatis 的应用周期。
- 由于 SqlSessionFactory 是一个对数据库的连接池，所以它占据着数据库的连接资源。如果创建多个 SqlSessionFactory，那么就存在多个数据库连接池，这样不利于对数据库资源的控制，也会导致数据库连接资源被消耗光，出现系统宕机等情况，所以尽量避免发生这样的情况。
- 因此在一般的应用中我们往往希望 SqlSessionFactory 作为一个单例，让它在应用中被共享。所以说 **SqlSessionFactory 的最佳作用域是应用作用域（整个应用程序，全局变量）。**
- 如果说 SqlSessionFactory 相当于数据库连接池，那么 SqlSession 就相当于一个数据库连接（Connection 对象），你可以在一个事务里面执行多条 SQL，然后通过它的 commit、rollback 等方法，提交或者回滚事务。所以它应该存活在一个业务请求中，处理完整个请求后，应该关闭这条连接，让它归还给 SqlSessionFactory，否则数据库资源就很快被耗费精光，系统就会瘫痪，所以用 try...catch...finally... 语句来保证其正确关闭。
- **所以 SqlSession 的最佳的作用域是请求或方法作用域。**

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\sqlSession系关系图.webp)

这里面的每一个Mapper就相当于一个具体的业务。如获取所有用户信息业务，根据id查询用户业务 等....

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\sqlSession基本业务流程.png)

可以看到，我们这mapper就是一个业务，查询所有用户。当然也可以用这一个sqlSession获取其他接口的Mapper，如一些多表联查，有可能还要什么工资接口（payMapper）根据主键id来多表查，最后整合在一起，这些后期都会讲到。我们这里主要明白，一个sqlSession可以获取多个接口(xxxMapper),就是那个getMapper操作

## 5. 重点:属性名和字段名不一致解决方法（resultMap）

环境：新建一 个项目，将之前的项目拷贝过来

### 问题背景：

#### 1、查看之前的数据库的字段名

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\(5)数据库字段.png)

​	

#### 2、Java中的实体类设计

```java
public class User {
    private Integer id;
    private String name;
    /*
    注意:现在password成员变量和数据库字段名(pwd)不一致了
     */
    private String password;
	//无参
   
    //有参
    
    //get 和 set方法
    
    //toString方法
}
```

#### 3、接口

```java
//根据id查询用户
User selectUserById(int id);
```

#### 4、mapper映射文件

```xml
<select id="selectUserById" resultType="user">
  select * from user where id = #{id}
</select>
```

#### 5、测试

```java
@Test
public void testSelectUserById() {
   SqlSession session = MybatisUtils.getSession();  //获取SqlSession连接
   UserMapper mapper = session.getMapper(UserMapper.class);
   User user = mapper.selectUserById(1);
   System.out.println(user);
   session.close();
}
```

**结果:**

- User{id=1, name='狂神', password='null'}
- 查询出来发现 password 为空 . 说明出现了问题！

**分析：**

- select * from user where id = #{id} 可以看做

  select  id,name,pwd  from user where id = #{id}

- mybatis会根据这些查询的列名(会将列名转化为小写,数据库不区分大小写) , 去对应的实体类中查找相应列名的set方法设值 , 由于找不到setPwd() , 所以password返回null ; 【自动映射】

### 解决方法:

#### 方案一：为列名指定别名 , 别名和java实体类的属性名一致 .

```xml
<select id="selectUserById" resultType="User">
  select id , name , pwd as password from user where id = #{id}
</select>
```

#### **方案二：使用结果集映射->ResultMap** 【推荐】

```xml
<!--
	新知识点:结果集映射
		id对应select标签中resultMap的值，你想取啥名都可以,type就是你想要映射成什么类型的结果集
-->
<resultMap id="UserMap" type="User">
   <!-- id为主键 -->
   <!-- column是数据库表的列名 , property是对应实体类的成员变量名 -->
   <!--
    我们只需要去映射实体类中成员变量名和我们数据库中的字段不一样的属性就可以了,一样的映射个寂寞
    <result column="id" property="id"/>
    <result column="name" property="name"/>
    -->
   <result column="pwd" property="password"/>
</resultMap>

<select id="selectUserById" resultMap="UserMap">
  select id , name , pwd from user where id = #{id}
</select>
```

​	总结：

​		问题产生原因和此解决方案的解析:

```java
/*
        原始格式：没有给字段取别名，没有弄resultMap的情况下输出:
        User{id=4, name='赵六', password='null'}
        可以看到，当我们的实体类的成员变量名(password)和数据库的字段名(pwd)不对应的时候,我们的密码为空了！
        原因:
        select * 查出pwd有值，映射到User，找不到pwd（只有private password）,
        所以不调用setPassword进行赋值，输出时，password为pojo中得默认值。
        属性不一致，可以查询出来的，但它会根据set方法来给对应的属性赋值，然后再通过toString表达
        也就是本来查的是pwd字段吧，然后映射到User中，发现并没有pwd成员变量名，当然也就没有对应的set方法了
        所以也就获得不到对应的pwd的值了，因为设置不进去嘛
*/
```

### **自动映射**

- `resultMap` 元素是 MyBatis 中最重要最强大的元素。它可以让你从 90% 的 JDBC `ResultSets` 数据提取代码中解放出来。
- 实际上，在为一些比如连接的复杂语句编写映射代码的时候，一份 `resultMap` 能够代替实现同等功能的长达数千行的代码。
- ResultMap 的设计思想是，对于简单的语句根本不需要配置显式的结果映射，而对于复杂一点的语句只需要描述它们的关系就行了。

你已经见过简单映射语句的示例了，但并没有显式指定 `resultMap`。比如：

```xml
<select id="selectUserById" resultType="map">
select id , name , pwd
  from user
  where id = #{id}
</select>
```

上述语句只是简单地将所有的列映射到 `HashMap` 的键上，这由 `resultType` 属性指定。虽然在大部分情况下都够用，但是 HashMap 不是一个很好的模型。你的程序更可能会使用 JavaBean 或 POJO（Plain Old Java Objects，普通老式 Java 对象）作为模型。

`ResultMap` 最优秀的地方在于，虽然你已经对它相当了解了，但是根本就不需要显式地用到他们。

### **手动映射**

#### 	1、返回值类型为resultMap

```sql
<select id="selectUserById" resultMap="UserMap">
  select id , name , pwd from user where id = #{id}
</select>
```

#### 	2、编写resultMap，实现手动映射！

```xml
<resultMap id="UserMap" type="User">
   <!-- id为主键 -->
   <id column="id" property="id"/>
   <!-- column是数据库表的列名 , property是对应实体类的属性名 -->
   <result column="name" property="name"/>
   <result column="pwd" property="password"/>
</resultMap>
```

如果世界总是这么简单就好了。但是肯定不是的，数据库中，存在一对多，多对一的情况，我们之后会使用到一些高级的结果集映射，association，collection这些，我们将在之后讲解，今天你们需要把这些知识都消化掉才是最重要的！理解结果集映射的这个概念！

## 6. 日志(企业级应用)

### 	6.1 日志工厂

思考：我们在测试SQL的时候，要是能够在控制台输出 SQL 的话，是不是就能够有更快的排错效率？

如果一个 数据库相关的操作出现了问题，我们可以根据输出的SQL语句快速排查问题。

对于以往的开发过程，我们会经常使用到debug模式来调节，跟踪我们的代码执行过程。但是现在使用Mybatis是基于接口，配置文件的源代码执行过程。因此，我们必须选择日志工具来作为我们开发，调节程序的工具。

Mybatis内置的日志工厂提供日志功能，具体的日志实现有以下几种工具：

- SLF4J
- Apache Commons Logging
- Log4j 2
- Log4j 【掌握】
- JDK logging
- STDOUT_LOGGING【掌握】
- NO_LOGGING

具体选择哪个日志实现工具由MyBatis的内置日志工厂确定。它会使用最先找到的（按上文列举的顺序查找）。如果一个都未找到，日志功能就会被禁用。

#### 	**STDOUT_LOGGING标准日志实现**

​	以后我们想用其他日志工具，只要改value就好了

```xml
<settings>
    <!--标注的日志工厂实现-->
       <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

测试，可以看到控制台有大量的输出！我们可以通过这些输出来判断程序到底哪里出了Bug

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\(6)日志信息.png)

>  解析:
> 		1.打开JDBC连接
> 		2.创建连接对象
> 		3.你没有设置提交所以为false，因为我们这里是查询语句，只有增删改才需要提交事务
> 		4.预编译的sql语句
> 		5.sql传入的参数值
> 		6.查询出来的字段
> 		7.查询出来的记录
> 		8.查询出来的记录数
> 		9.sout输出的结果
> 		10-13:结果集的设置，关闭连接，将连接对象回收到连接池中

### 6.2 LOG4J

#### 	1.使用一个外部类，需要在maven中导入依赖jar包

```xml
<!--log4j-->
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

#### 	2.添加配置文件log4j.properties

```properties
#将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
log4j.rootLogger=DEBUG,console,file

#控制台输出的相关设置
log4j.appender.console = org.apache.log4j.ConsoleAppender
#输出用我们的输出语句输出
log4j.appender.console.Target = System.out
log4j.appender.console.Threshold=DEBUG
log4j.appender.console.layout = org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=【%c】-%m%n

#文件输出的相关设置
log4j.appender.file = org.apache.log4j.RollingFileAppender
log4j.appender.file.File=./log/kuang.log
#一个log文件的大小，超过这个文件大小就又会生成1个日志;KB MB GB
log4j.appender.file.MaxFileSize=10mb
log4j.appender.file.Threshold=DEBUG
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern= %p【%d{yy-MM-dd}】【%c】%m%n

#日志输出级别,只有DEBUG才会输出
log4j.logger.org.mybatis=DEBUG
log4j.logger.java.sql=DEBUG
log4j.logger.java.sql.Statement=DEBUG
log4j.logger.java.sql.ResultSet=DEBUG
log4j.logger.java.sql.PreparedStatement=DEBUG
```

#### 	3.配置log4j为日志实现

```xml
<settings>
    <!--标注的日志工厂实现-->
    <!--<setting name="logImpl" value="STDOUT_LOGGING"/>-->
    
    <!--配置LOG4J为日志实现-->
    <setting name="logImpl" value="LOG4J"/>
</settings>
```

#### 	4.log4j测试，直接进行刚才的查询

​	输出以下内容

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\(6)log4j日志信息.png)

> #### 简单使用
>
> 1. 在要使用log4j的类中，导入包 import org.apache.log4j.Logger;
>
> 2. 日志对象,参数为当前类的class
>
>    ```java
>    //当前类是UserMapperTest类
>    static Logger logger  = Logger.getLogger(UserMapperTest.class);
>    ```
>
> 3. 日志级别
>
>    ```java
>    @Test
>    public void  testLog4j(){
>        logger.info("info:进入了testLog4j方法");
>        logger.debug("debug:进入了testLog4j方法");
>        logger.error("error:进入了testLog4j方法");
>    }
>    ```
>
>    在项目下，会新建一个log文件夹，里面放着kuang.log，里面有
>
>    ```log
>    [INFO][21-06-06][com.kuang.dao.UserMapperTest]info:进入了testLog4j方法
>    [DEBUG][21-06-06][com.kuang.dao.UserMapperTest]debug:进入了testLog4j方法
>    [ERROR][21-06-06][com.kuang.dao.UserMapperTest]error:进入了testLog4j方法
>    ```
>
>    

## 7. 分页

###  		7.1 分页作用

> **思考：为什么需要分页？**
>
> ​	在学习mybatis等持久层框架的时候，会经常对数据进行增删改查操作，使用最多的是对数据库进行查询操作，如果查询大量数据的时候，我们往往使用分页进行查询，也就是每次处理小部分数据，这样对数据库压力就在可控范围内。

### 	7.2 用Limit分页

```sql
#语法
SELECT * FROM table LIMIT stratIndex，pageSize

SELECT * FROM table LIMIT 5,10; // 检索记录行 6-15  

#为了检索从某一个偏移量到记录集的结束所有的记录行，可以指定第二个参数为 -1：   
SELECT * FROM table LIMIT 95,-1; // 检索记录行 96-last.  

#如果只给定一个参数，它表示返回最大的记录行数目：   
SELECT * FROM table LIMIT 5; //检索前 5 个记录行  

#换句话说，LIMIT n 等价于 LIMIT 0,n。 
```

#### 		任务实操

​	我们来做一个小任务，分页查询user表，每页查3条记录，就查从0下标开始的吧

> User表完整数据
>
> ​		id    name       pwd     
>
> ------  ------  --------
> ```sql
>  1  张三      123455  
>  2  李四      112421  
>  3  王虎      129876  
>  4  赵六      987214  
>  5  王五      516567 
> ```

> **1.UserMapper**
> 	我们缕下思路，肯定要在UserMapper中添加抽象方法的，然后查出来的数据是不是可能会多条，所以返回值类型要List<User>,方法名就叫getUserByLimit吧，参数类型的话：我们要查出指定的记录，肯定要两个值，一个startIndex（查询出的页面(假设是页面)的第一条记录的下标），一个pageSize（每页显示的条数）,我们知道parameterType如果是基本类型，只能查一个，如parameterType("int")，但是我们这里有两个参数，用User对象做参数更加不行，因为实体类对象中根本没有这两个startIndex，pageSize成员变量。所以我们只能用万能map了,即参数就是个Map集合，键我们就设置为String类型，值的话随便都行，当然了最好是Integer。
> 设计如下:
>
> ```java
> List<User> getUserByLimit(Map<String,Object> map);
> ```
>
> **2.UserMapper.xml**
>
> ​	这就相当于之前的实现类，我们肯定要配置下的吧。我们这是查询，所以是**select标签**，**id**就是我们的抽象方法名getUserByLimit,**parameterType**当然就是我们map类型了，**返回**的话我们就像仔细想想了，因为我们这里有个大问题，**数据库字段和实体类成员变量名不一致**，所以就要用**结果集映射**了(ps:当然，如果我们把成员变量名改的跟数据库字段名一样就没啥事，但是我们就是要按最难的来 - -)，所以**就是resultMap = "UserMap"**了，不是普通的resultType = "User"了，我们再来思考：UserMap就是个结果集映射标签(resultMap)的id，那它的映射类型是谁呢？这肯定是User嘛，跟实体类User的成员变量去映射,然后就是**标签体内容**了，这肯定都是可以就是select * from mybatis.user limit #{startIndex},#{pageSize}了，startIndex是我们要在测试类中存的键，
> #{startIndex}就是键所对应的值，剩下一个同理。
>
> **3.UserMapperTest**
>
> ​	方法名就去跟抽象方法名相同的就好了，先获取连接池对象sqlSession，再获取接口对象mapper，这之后就需要去创造我们的map集合了，这里为了测试就是自己的数据了，在实际开发中应用多是从前端返回的用户点击对应页码的数据，放入对应键值对，注意键名一定要和我们在配置文件中写的名字一样( #{startIndex},#{pageSize})，就调用接口对应的方法就好了getUserByLimit,里面参数就是我们设置的map，再就干你想干的，实际开发应用应该是给用户返回对应页面内容

​			源码:

##### 				1.UserMapper

```java
/**
     * 分页查询
     * 我们知道，查询分页至少需要两个参数，第一个是查询的页码下标(从0开始)
     * 第二个就是分页查询的条数,
     * 特殊:select * from user limit 3;就是从0开始查3条记录
     * @return
     */
    List<User> getUserByLimit(Map<String,Object> map);
```

##### 			2.UserMapper.xml

```xml
<!--
        新知识点:结果集映射
            id对应select标签中resultMap的值，你想取啥名都可以,相当于给这个resultMap标签起了个名字 ,方便其他地方引用吧
            type就是你想要映射成什么类型的结果集,因为我们这里用了别名，就不用再写 com.kuang.pojo.User 了
    -->
    <resultMap id="UserMap" type="User">
        <!--
        column:代表列，即数据库中的字段，
        property:代表实体类中的属性
        我们只需要去映射实体类中成员变量名和我们数据库中的字段不一样的属性就可以了,一样的映射个寂寞
        <result column="id" property="id"/>
        <result column="name" property="name"/>
        相当于后台帮做了user.setPassword(resultset.getString("pwd"));
        getString():这个适用取数据用的，前台传过来的和数据库查出来的都可以，要是单独的一个字符串就可以往里面赋值
        比如前端传过来一个 ID，经过解析完成后，getString（“ID”）就可以得到，在定义一个对象，就可以传值了
        -->
        <result column="pwd" property="password"/>
    </resultMap>

    <select id="getUserByLimit" parameterType="map" resultMap="UserMap">
        select * from mybatis.user limit #{startIndex},#{pageSize}
    </select>
```

##### 		3.UserMapperTest

```java
@Test
    public void getUserByLimit(){
        //获取连接池对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        //获取接口对象,之前说过就跟UserDao dao = new UserDaoImpl();类似
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        Map<String, Object> map = new HashMap<>();
        map.put("startIndex",0);
        map.put("pageSize",3);
        //{startIndex=0, pageSize=3}
        System.out.println(map);
        List<User> userByLimit = mapper.getUserByLimit(map);
        for (User user : userByLimit) {
            System.out.println(user);
            /*
            我这里是没有用resultMap，用resultType = "User"的时候的情况
            User{id=1, name='张三', password='null'}
            User{id=2, name='李四', password='null'}
            User{id=3, name='王虎', password='null'}
            password查询不到了，你可以在那两个方法里面选一个
            一个直接 as
            一个用resultMap,字段和成员变量进行映射
            我这里为了增加难度，用了第二种
            User{id=1, name='张三', password='123455'}
            User{id=2, name='李四', password='112421'}
            User{id=3, name='王虎', password='129876'}
            成功!
             */
        }
        sqlSession.close();
    }
```

### 7.3 RowBounds分页(了解)

​	我们除了使用Limit在SQL层面实现分页，也可以使用RowBounds在Java代码层面实现分页，当然此种方式作为**了解**即可。我们来看下如何实现的！

**步骤：**

#### 1、mapper接口

```java
//选择全部用户RowBounds实现分页
List<User> getUserByRowBounds();
```

#### 2、mapper文件

```xml
<select id="getUserByRowBounds" resultType="user">
select * from user
</select>
```

#### 3、测试类

在这里，我们需要使用RowBounds类

```java
@Test
public void testUserByRowBounds() {
   SqlSession session = MybatisUtils.getSession();

   int currentPage = 2;  //第几页
   int pageSize = 2;  //每页显示几个
   RowBounds rowBounds = new RowBounds((currentPage-1)*pageSize,pageSize);

   //通过session.**方法进行传递rowBounds，[此种方式现在已经不推荐使用了]
   List<User> users = session.selectList("com.kuang.mapper.UserMapper.getUserByRowBounds", null, rowBounds);

   for (User user: users){
       System.out.println(user);
  }
   session.close();
}
```

### 7.4 分页插件(了解)

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\(7)MyBatis分页插件.png)

> 官方网站：https://pagehelper.github.io/
> 如果是大公司，架构师说要用，你需要清楚这是什么东西

## 8. 使用注解开发(了解即可)

### 	8.1 面向接口编程

> - 大家之前都学过面向对象编程，也学习过接口，但在真正的开发中，很多时候我们会选择面向接口编程
> - **根本原因 :  解耦 , 可拓展 , 提高复用 , 分层开发中 , 上层不用管具体的实现 , 大家都遵守共同的标准 , 使得开发变得容易 , 规范性更好**
> - 在一个面向对象的系统中，系统的各种功能是由许许多多的不同对象协作完成的。在这种情况下，各个对象内部是如何实现自己的,对系统设计人员来讲就不那么重要了；
> - 而各个对象之间的协作关系则成为系统设计的关键。小到不同类之间的通信，大到各模块之间的交互，在系统设计之初都是要着重考虑的，这也是系统设计的主要工作内容。面向接口编程就是指按照这种思想来编程。

​		**关于接口的理解**

> - 接口从更深层次的理解，应是定义（规范，约束）与实现（名实分离的原则）的分离。
>
> - 接口的本身反映了系统设计人员对系统的抽象理解。
>
> - 接口应有两类：
>
> - - 第一类是对一个个体的抽象，它可对应为一个抽象体(abstract class)；
>   - 第二类是对一个个体某一方面的抽象，即形成一个抽象面（interface）；
>
> - 一个体有可能有多个抽象面。抽象体与抽象面是有区别的。



​		**三个面向区别**

> - 面向对象是指，我们考虑问题时，以对象为单位，考虑它的属性及方法 .
> - 面向过程是指，我们考虑问题时，以一个具体的流程（事务过程）为单位，考虑它的实现 .
> - 接口设计与非接口设计是针对复用技术而言的，与面向对象（过程）不是一个问题.更多的体现就是对系统整体的架构

### 	8.2 使用注解开发(核心：反射,难度：大)

​	**注意**:持久层Sql语句比较复杂，XML配置更好，**Mybatis不推荐注解**，维护成本高，spring， springMVC，SpingBoot用注解。

- mybatis最初配置信息是基于 XML ,映射语句(SQL)也是定义在 XML 中的。而到MyBatis 3提供了新的基于注解的配置。不幸的是，Java 注解的的表达力和灵活性十分有限。最强大的 MyBatis 映射并不能用注解来构建

- sql 类型主要分成 :

- - @select ()
  - @update ()
  - @Insert ()
  - @delete ()

**注意：**利用注解开发就不需要mapper.xml映射文件了 .

#### 1、我们在我们的接口中添加注解

```java
//查询全部用户
@Select("select id,name,pwd as password from user")
public List<User> getAllUser();
```

#### 2、在mybatis的核心配置文件中注入

```xml
<!--使用class绑定接口-->
<mappers>
   <mapper class="com.kuang.mapper.UserMapper"/>
</mappers>
```

#### 3、我们去进行测试

```java
@Test
public void testGetAllUser() {
   SqlSession session = MybatisUtils.getSession();
   //本质上利用了jvm的动态代理机制
   UserMapper mapper = session.getMapper(UserMapper.class);
   List<User> users = mapper.getAllUser();
   for (User user : users){
       System.out.println(user);
  }
   session.close();
}
```

#### 4、利用Debug查看本质

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\(8)注解流程.webp)

#### 5、本质上利用了jvm的动态代理机制

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\(8)jvm的动态代理机制.png)

#### 6、Mybatis详细的执行流程

​	这里看不懂没关系，以后学完了，练习了几个项目之后再结合博客慢慢深究

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\(8)Mybatis详细的执行流程.png)

### 	8.3 CRUD

> 我们可以在工具类创建的时候实现自动提交事务！
> **好处**:这样我们以后做增删改语句就不用再在测试类里面提交事务(sqlSession.commit())了
>
> ```java
> public static SqlSession getSqlSession(){
>     //设置为true之后，你以后增删改就不用手动提交事务了,设置为true，自动提交事务
>     SqlSession sqlSession = sqlSessionFactory.openSession(true);
>     /*
>     当然也可以直接return整合到一句，这里为了清晰就不弄了
>     作用：返回一个连接对象
>      */
>     return sqlSession;
> } 
> ```

#### 	1. 编写接口，增加注解

```java
package com.kuang.dao;

import com.kuang.pojo.User;
import org.apache.ibatis.annotations.*;

import java.util.List;

/**
 * @author <作者>
 * @version <版本>
 * @since <JDK版本>
 */
public interface UserMapper {
    /**
     * 注解就是给类加的注释
     * 然后元注解规定运行环境在runtime
     * 那么注解信息就可以在运行时获取
     * 而反射需要Class路径去instance实例对象
     * 反射可以获得这个类的方法属性还可以创建对象，执行方法
     * 加在一起就无敌了
     * @return
     */
    @Select("select id,name,pwd as password from user")
    List<User> getUsers();

    /**
     * 如果参数是这样，直接加上个@Param("参数名"),不管有多少个都可以，参数名你可以自定
     * 注意  where id = #{id} and name = #{name}
     * 这#{}里面的值都是根据@Param("参数名")里的参数名还决定的
     * 就是你Param里的参数名一定要和sql语句里面的参数要一致
     * @param id
     * @param name
     * @return
     */
    @Select("select id,name,pwd as password from user where id = #{id} and name = #{name}")
    User getUserById(@Param("id") int id,@Param("name") String name);

    /**
     * 键是pwd，但是设置进去的值是password的，所以不会为null
     * @param user
     * @return
     */
    @Insert("insert into user(id,name,pwd) values (#{id},#{name},#{password})")
    int addUser(User user);

    @Update("update user set name=#{name},pwd=#{password} where id=#{id}")
    int updateUser(User user);

    @Delete("delete from user where id=#{uid} ")
    int deleteUser(@Param("uid") int id);
}

```

> ##### 		@Param解析:其实这个是用于当接口(映射器)的方法需要多个参数时，@Param注解可以被应用于接口方法参数来给每个参数取个名字，否则，多参数默认将会以它们的顺序位置和sql语句中表达式进行映射
>
> - 基本类型的参数或者String类型，需要加上
> - 引用类型不用加
> - 如果只有一个基本类型的话，可以忽略，但是建议大家都加上
> - 我们在SQL中引用的就是我们这里的@Param("uid")中设定的属性名!

#### 	2.编写测试类

​		【**注意**:我们必须要将接口注册绑定到我们的核心配置文件中!】

```xml
<!--我们用了注解之后，不用xml文件对接口进行配置了，但是我们依然要绑定接口的-->
<mappers>
    <mapper class="com.kuang.dao.UserMapper"/>
</mappers>
```

## 9. Lombok

### 	1. 什么是lombok插件

> Lombok 是一种 Java™ 实用工具，可用来帮助开发人员消除 Java 的冗长，尤其是对于简单的 Java 对象（POJO）。它通过注解实现这一目的。就是你不用再alt+insert去添加get/set等方法了，直接加行注解即可
>
> 官方回答:Project Lombok 是一个 Java 库，可自动插入您的编辑器并构建工具，为您的 Java 增添方便。
> 永远不要再编写另一个 getter 或 equals 方法，通过一个注释，您的类就有一个功能齐全的构建器，自动化您的日志变量等等
>
> ​	Lombok提供注解方式来提高代码的简洁性,常用注解有:
>
> - @Data  (重要!作用于类上，是以下注解的集合：@ToString @EqualsAndHashCode @Getter @Setter @RequiredArgsConstructor)
> - @NoArgsConstructor (一个无参构造，作用于类上)
> - @AllArgsConstructor (一个全参构造，作用于类上)
> - @Setter @Getter (生成set/get方法,**作用于类**上是类中**所有**成员变量的get/set，**作用于指定的成员变量**上是生成对应的get/set方法)
> - @NonNull
> - @Synchronized
> - @ToString
> - @EqualsAndHashCode
> - @Cleanup
> - @SneakyThrows



### 2.使用lombok

#### 		1、IDEA安装Lombok插件(IDEA2020版及之后都内置此插件了)

#### 		2、引入Maven依赖

```xml
<dependencies>
    <!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.20</version>
    </dependency>
</dependencies>
```

#### 	3、在代码中增加注解

```java
@Data //@Data：作用于类上，是以下注解的集合：@ToString @EqualsAndHashCode @Getter @Setter @RequiredArgsConstructor
@NoArgsConstructor
@AllArgsConstructor
public class Teacher {
   private int id;
   private String name;
}
```

#### 		建议:

> 我觉的以后不推荐使用lombok，原因有下:以下是CSDN 风水道人博主的观点。
>
> 1. JDK版本问题
>     当我想要将现有项目的JDK从Java 8升级到Java 11时，我发现Lombok不能正常工作了。于是我不得不将所有的Lombok注解从项目源代码中清除，并使用IDE自带的功能生成getter/setter，equals，hashCode，toString以及构造器等方法，你也可以使用Delombok工具完成这一过程。但这终究会消耗你很多的时间。
> 2. 胁迫使用
>     当你的源代码中使用了Lombok，恰好你的代码又被其他的人所使用，那么依赖你代码的人，也必须安装Lombok插件(不管他们喜不喜欢)，同时还要花费时间去了解Lombok注解的使用情况，如果不那么做，代码将无法正常运行。使用过Lombok之后，我发现这是一种很流氓的行为。
> 3. 可读性差
>     Lombok隐藏了JavaBean封装的细节，如果你使用@AllArgsConstructor注解，它将提供一个巨型构造器，让外界有机会在初始化对象时修改类中所有的属性。首先，这是极其不安全的，因为类中某系属性我们是不希望被修改的；另外，如果某个类中有几十个属性存在，就会有一个包含几十个参数的构造器被Lombok注入到类中，这是不理智的行为；其次，构造器参数的顺序完全由Lombok所控制，我们并不能操控，只有当你需要调试时才发现有一个奇怪的“小强”在等着你；最后，在运行代码之前，所有JavaBean中的方法你只能想象他们长什么样子，你并不能看见。
> 4. 代码耦合度增加
>     当你使用Lombok来编写某一个模块的代码后，其余依赖此模块的其他代码都需要引入Lombok依赖，同时还需要在IDE中安装Lombok的插件。虽然Lombok的依赖包并不大，但就因为其中一个地方使用了Lombok，其余所有的依赖方都要强制加入Lombok的Jar包，这是一种入侵式的耦合，如果再遇上JDK版本问题，这将是一场灾难。
> 5. 得不偿失
>     使用Lombok，一时觉得很爽，但它却污染了你的代码，破坏了Java代码的完整性，可读性和安全性，同时还增加的团队的技术债务，这是一种弊大于利，得不偿失的操作。如果你确实想让自己的代码更加精炼，同时又兼顾可读性和编码效率，不妨使用主流的Scala或Kotlin这一基于JVM的语言。
>     ————————————————
>     版权声明：本文为CSDN博主「风水道人」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
>     原文链接：https://blog.csdn.net/jcmj123456/article/details/109194089

## 10. 多对一的处理

​	多对一的理解：

> - 多个学生对应一个老师
> - **如果对于学生来说**，就是一个多对一的现象，即多个学生这边关联一个老师！

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\(10)多对一数据库表.webp)

```sql
#表的搭建
CREATE TABLE `teacher` (
`id` INT(10) NOT NULL,
`name` VARCHAR(30) DEFAULT NULL,
PRIMARY KEY (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO teacher(`id`, `name`) VALUES (1, '秦老师');

CREATE TABLE `student` (
`id` INT(10) NOT NULL,
`name` VARCHAR(30) DEFAULT NULL,
`tid` INT(10) DEFAULT NULL,
PRIMARY KEY (`id`),
KEY `fktid` (`tid`),
CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
) ENGINE=INNODB DEFAULT CHARSET=utf8


INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('1', '小明', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('2', '小红', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('3', '小张', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('4', '小李', '1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES ('5', '小王', '1');
```

### 搭建简单测试环境

#### 1、IDEA安装Lombok插件

#### 2、引入Maven依赖

```xml
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
 <groupId>org.projectlombok</groupId>
 <artifactId>lombok</artifactId>
 <version>1.18.20</version>
</dependency>
```

#### 3、新建实体类Teacher,Student(注解)

```java
@Data //GET,SET,ToString，有参，无参构造
public class Teacher {
   private int id;
   private String name;
}
@Data
public class Student {
   private int id;
   private String name;
   //多个学生可以是同一个老师，即多对一
   private Teacher teacher;
}
```

#### 4、编写实体类对应的Mapper接口 【两个】

- **无论有没有需求，都应该写上，以备后来之需！**

```java
public interface StudentMapper {
}
public interface TeacherMapper {
}
```

#### 5、编写Mapper接口对应的 mapper.xml配置文件 【两个】

- **无论有没有需求，都应该写上，以备后来之需！**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
       PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.kuang.mapper.StudentMapper">

</mapper>
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
       PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.kuang.mapper.TeacherMapper">

</mapper>
```

#### 6、在核心配置文件中(mybatis-config.xml)中绑定注册我们的Mapper接口或者xml文件!【方式很多】

```xml
<mappers>
    <!--第一种绑定方法，指定xml-->
    <!--<mapper resource="com/kuang/dao/TeacherMapper.xml"/>-->
    <!--第二种绑定方法，指定接口-->
    <!--<mapper class="com.kuang.dao.TeacherMapper"/>-->
    <!--<mapper class="com.kuang.dao.StudentMapper"/>-->
    <!--第三种绑定方法，写出包名即可-->
    <package name="com.kuang.dao"/>
</mappers>
```

#### 7、测试查询是否能搭建成功

##### 	1. 在接口中编写对应的测试型抽象方法

​	Teacher接口好测试，所以用Thacher接口

```java
public interface TeacherMapper {

    /**
     * 测试搭建环境:获取指定教师
     * @param id
     * @return
     */
    public Teacher getTeacher(int id);
}
```

##### 	2. 编写对应的接口映射器.xml

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.kuang.dao.TeacherMapper">
    <select id="getTeacher" resultType="Teacher" parameterType="int">
        select * from mybatis.teacher where id = #{id}
    </select>
</mapper>
```

##### 	3. 创建测试类

```java
import com.kuang.dao.TeacherMapper;
import com.kuang.pojo.Teacher;
import com.kuang.utils.MybatisUtils;
import org.apache.ibatis.session.SqlSession;

/**
 * @author <作者>
 * @version <版本>
 * @since <JDK版本>
 */
public class MyTest {
    public static void main(String[] args) {
        //1.创建一个连接对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        //2.获取接口的映射器
        TeacherMapper mapper = sqlSession.getMapper(TeacherMapper.class);
        //3.执行
        Teacher teacher = mapper.getTeacher(1);
        System.out.println(teacher);
        sqlSession.close();
    }
}
```



### 按查询嵌套处理

#### 1、给StudentMapper接口增加方法

```java
//获取所有学生及对应老师的信息
public List<Student> getStudents();
```

#### 2、编写对应的Mapper文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
       PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.kuang.mapper.StudentMapper">

   <!--
   需求：获取所有学生及对应老师的信息
   思路：
       1. 获取所有学生的信息
       2. 根据获取的学生信息的老师ID->获取该老师的信息
       3. 思考问题，这样学生的结果集中应该包含老师，该如何处理呢，数据库中我们一般使用关联查询？
           1. 做一个结果集映射：StudentTeacher
           2. StudentTeacher结果集的类型为 Student
           3. 学生类中老师的属性为teacher，对应数据库中为tid。
              多个 [1,...）学生关联一个老师=> 多对一
           4. 查看官网找到：association – 一个复杂类型的关联；使用它来处理关联查询
   -->
   <select id="getStudents" resultMap="StudentTeacher">
       /*
       	这里就是查询所有学生的信息，同时啊，这个学生类中应该也包含着老师表的数据，因为我们想要知道每一位学生对		   应的是哪个老师，所以这学生类的teacher属性就很重要了，它映射的是学生表的tid字段，那我们就要通过tid查		    询出来的数据（就是一条条 每个学生对应的老师信息），放入teacher中，这样就形成了每个学生对应一位查出来         的老师（teacher成员变量）
       
       流程图：<-->表示映射，-->表示被赋值 如：a --> b 等价于 a=b;
       teacher成员变量 <--> tid 学生表的字段 --> 通过 select * from teacher where id = \#{tid}查询出来的老师信息
       */
    select * from student
   </select>
   <resultMap id="StudentTeacher" type="Student">
       <!--
		association关联属性 property属性名 javaType属性类型 column在多的一方的表中的列名
		解析：为什么后面要跟一个javaType 和 select呢？
            javaType:
                因为我们知道这个属性(property="teacher")是一个引用类型(Teacher)
                所以就是说明这个属性是什么类型的，因为这个成员变量teacher是一个对象
                所以要给这个对象设置一个类型
            select:
                我们的目的是什么？就是将teacher的信息给查询出来，那么就需要根据column的值即tid的值
                就是获得学生类里的tid然后用这个tid去老师表里再查返回一个teacher对象

		-->
       <association property="teacher"  column="tid" javaType="Teacher" select="getTeacher"/>
   </resultMap>
   <!--
   这里传递过来的id，只有一个属性的时候，#{}里面可以写任何值
   association中column多参数配置：
       column="{key=value,key=value}"
       其实就是键值对的形式，key是传给下个sql的取值名称，value是片段一中sql查询的字段名。
   -->
   <select id="getTeacher" resultType="Teacher">
      select * from teacher where id = #{tid}
   </select>

</mapper>
```

#### 3、编写完毕去Mybatis配置文件中，注册Mapper！(导包模式最简单)

```xml
<!--如下-->
<mappers>
    <package name="com.kuang.dao"/>
</mappers>
```

#### 4、注意点说明：

```xml
<resultMap id="StudentTeacher" type="Student">
   <!--association关联属性 property属性名 javaType属性类型 column在多的一方的表中的列名-->
   <association property="teacher"  column="{id=tid,name=tid}" javaType="Teacher" select="getTeacher"/>
</resultMap>
<!--
这里传递过来的id，只有一个属性的时候，下面可以写任何值
association中column多参数配置：
   column="{key=value,key=value}"
   其实就是键值对的形式，key是传给下个sql的取值名称，value是片段一中sql查询的字段名。
-->
<select id="getTeacher" resultType="teacher">
  select * from teacher where id = #{id} and name = #{name}
</select>
```

#### 5、测试

```java
@Test
public void testGetStudents(){
   SqlSession session = MybatisUtils.getSession();
   StudentMapper mapper = session.getMapper(StudentMapper.class);

   List<Student> students = mapper.getStudents();

   for (Student student : students){
       System.out.println(
               "学生名:"+ student.getName()
                       +"\t老师:"+student.getTeacher().getName());
  }
}
```



### 按结果嵌套处理(简单)

​	除了上面这种方式，还有其他思路吗？

​	我们还可以按照结果进行嵌套处理；

#### 1、接口方法编写

```java
public List<Student> getStudents2();
```

#### 2、编写对应的mapper文件

```xml
<!--
按查询结果嵌套处理
思路：
   1. 直接查询出结果，进行结果集的映射
-->
<select id="getStudents2" resultMap="StudentTeacher2" >
  select s.id sid, s.name sname , t.name tname
  from student s,teacher t
  where s.tid = t.id
</select>

<resultMap id="StudentTeacher2" type="Student">
   <id property="id" column="sid"/>
   <result property="name" column="sname"/>
   <!--关联对象property 关联对象在Student实体类中的属性-->
   <association property="teacher" javaType="Teacher">
       <result property="name" column="tname"/>
   </association>
</resultMap>
```

​	这是自己的注释及代码(能查询出老师的id)

```xml
<select id="getStudent2" resultMap="StudentTeacher2">
        /*
            1.取别名可不写AS
            2.此处查询后输出3个字段，有两个不同表的字段同名，取别名区分
        */
        select student.id as sid,student.name as sname,teacher.name  as tname,teacher.id as tid
        from student,teacher
        where student.tid = teacher.id;
    </select>
    <resultMap id="StudentTeacher2" type="Student">
        <!--查询出来的字段取的是别名，所以是sid，sname，tname-->
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <!--
            这个结果类型就应该是个老师Teacher类吧
            这里的就是说在Student类里面，有一个teacher成员变量，类型是Teacher类的
        -->
        <association property="teacher" javaType="Teacher">
            <!--
                解析：
                    property是在Teacher类型里面的成员变量名
                    column是在查询语句中命名的字段名（这里我们将字段重命名了）
                这里的name是指Teacher类中的name

                问题:
                    这种查出来的结果  老师的id一直为0
                    ID等于0或者null的，因为没有对ID进行查询(现在代码已经补全，已经解决了)
                    就是 select student.id as sid,student.name as sname,teacher.name as tname
                    里面没有查teacher.id
            -->
            <result property="name" column="tname"/>
            <result property="id" column="tid"/>
        </association>

    </resultMap>
```



#### 3、去mybatis-config文件中注入【此处应该处理过了】

#### 4、测试

```java
@Test
public void testGetStudents2(){
   SqlSession session = MybatisUtils.getSession();
   StudentMapper mapper = session.getMapper(StudentMapper.class);

   List<Student> students = mapper.getStudents2();

   for (Student student : students){
       System.out.println(
               "学生名:"+ student.getName()
                       +"\t老师:"+student.getTeacher().getName());
  }
}
```

### 小结

> 按照查询进行嵌套处理就像SQL中的子查询
>
> 按照结果进行嵌套处理就像SQL中的联表查询



## 11. 一对多的处理

> ​	对应mybatis-07项目
> ​	比如：一个老师拥有多个学生,**对于老师而言**，就是一对多，如：一个老师管理多个学生
>
> 一对多的理解：
>
> - 一个老师拥有多个学生
> - 如果对于老师这边，就是一个一对多的现象，即从一个老师下面拥有一群学生（集合）！

### 	环境搭建

```java
@Data
public class Student {
   private int id;
   private String name;
   private int tid;
}

@Data
public class Teacher {
   private int id;
   private String name;
   //一个老师多个学生
   private List<Student> students;
}
```

​	和之前一样，搭建测试的环境！

### 按结果嵌套处理(简单)

#### 1、TeacherMapper接口编写方法

```java
//获取指定老师，及老师下的所有学生
public Teacher getTeacher(int id);
```

#### 2、编写接口对应的Mapper配置文件

```xml
<select id="getTeacher" resultMap="TeacherStudent">
        /*
        这些字段就是我们所想查询出来的结果,\#{tid}就是我们测试类中传入的参数值,是我们想要查的老师的id值
          这样就能根据老师的id精确查到某一个老师下有多少个学生了
        注意：
        t.id as tid 是将teacher表的id重新命名，
        t.id = \#{tid} 是将传入进来的参数赋值给t.id
        */
    
        select s.id as sid,s.name as sname,t.name as tname,t.id as tid
        from student s,teacher t
        where s.tid = t.id and t.id = #{tid}
    </select>
    <resultMap id="TeacherStudent" type="Teacher">
        <!--
         这里的就是老师类里面的属性了
            为什么要映射呢？因为我们Teacher类的基本类型的属性名是id，name，
            而查询出来的字段名我们又重命名为了tid，tname，为了识别度高，更好区分
            所以想让老师类中的id，name正确的被赋值，所以就要映射了

            ps：tid就是我们在测试类里面输入的参数值，tname就是查询出来的对应的值
        -->
        <result property="id" column="tid"/>
        <result property="name" column="tname"/>
        <!--
            新知识点：ofType
                 javaType：是某个指定属性的类型
                 （如Teacher，那么这标签里面的属性(property)就是Teacher类里面的成员变量名了）
                 ofType：获取集合中的泛型类型，如List<Teacher> 就：ofType="Teacher",
                 说来说去还是获取指定成员变量的类型嘛，只是这个成员变量类型是集合里的泛型类
        -->
        <collection property="students" ofType="Student">
            <!--
            那么这标签里面的属性(property)就是Teacher类里面的成员变量名了
                为什么还需要映射呢？
                    也是一样的原理，类中属性名和查询出来的字段名不一样，需要映射
            -->
             <result property="id" column="sid"/>
             <result property="name" column="sname"/>
             <result property="tid" column="tid"/>
        </collection>
    </resultMap>
```

#### 3、将Mapper文件注册到MyBatis-config文件中

```xml
<!--如下，这种最简单，要查看全的在测试环境中有讲-->
<mappers>
    <package name="com.kuang.dao"/>
</mappers>
```

#### 4、测试

```java
@Test
public void testGetTeacher(){
   SqlSession session = MybatisUtils.getSession();
   TeacherMapper mapper = session.getMapper(TeacherMapper.class);
   Teacher teacher = mapper.getTeacher(1);
   System.out.println(teacher.getName());
   System.out.println(teacher.getStudents());
}
```

### 按查询嵌套处理

#### 1、TeacherMapper接口编写方法

```java
public Teacher getTeacher2(int id);
```

#### 2、编写接口对应的Mapper配置文件

```xml
<!--第二个方法查询嵌套-->
    <select id="getTeacherTwo" resultMap="TeacherStudentTwo">
        /*这个\#{tid}是Test类中传入进来的参数*/
        select * from mybatis.teacher where id = #{tid}
    </select>
    <resultMap id="TeacherStudentTwo" type="Teacher">
        <!--column的id就是老师表中的id-->
        <result property="id" column="id"/>
        <collection property="students" ofType="Student" column="id" select="getStudentById"/>
    </resultMap>
    <select id="getStudentById" resultType="Student">
        /*
        括号里的tid就是collection标签中column的值,这column就是Teacher类的id成员变量,如 1
        */
        select * from student where tid=#{tid}
    </select>
```

#### 3、将Mapper文件注册到MyBatis-config文件中

```xml
<!--如下-->
<mappers>
    <package name="com.kuang.dao"/>
</mappers>
```

#### 4、测试

```java
@Test
public void testGetTeacher2(){
   SqlSession session = MybatisUtils.getSession();
   TeacherMapper mapper = session.getMapper(TeacherMapper.class);
   Teacher teacher = mapper.getTeacher2(1);
   System.out.println(teacher.getName());
   System.out.println(teacher.getStudents());
}
```

### 小结:

> 1、关联-association
>
> 2、集合-collection
>
> 3、所以association是用于一对一和多对一，而collection是用于一对多的关系
>
> 4、JavaType和ofType都是用来指定对象类型的
>
> - JavaType是用来指定pojo中属性的类型
> - ofType指定的是映射到list集合属性中pojo的类型。

### **注意说明：**

> 1、保证SQL的可读性，尽量通俗易懂
>
> 2、根据实际要求，尽量编写性能更高的SQL语句
>
> 3、注意属性名和字段不一致的问题
>
> 4、注意一对多和多对一 中：字段和属性对应的问题
>
> 5、尽量使用Log4j，通过日志来查看自己的错误
>
> ​	子查询会产生中间结果表，mysql优化尽量使用联表查询

### 面试高频:

> 以下是高频问点：	
> 	Mysql引擎
> 	InnoDB底层原理
> 	索引
> 	索引优化



## 12. 动态sql

​	==什么是动态SQL：**动态SQL指的是根据不同的查询条件 , 生成不同的Sql语句.**==

```
官网描述：
MyBatis 的强大特性之一便是它的动态 SQL。如果你有使用 JDBC 或其它类似框架的经验，你就能体会到根据不同条件拼接 SQL 语句的痛苦。例如拼接时要确保不能忘记添加必要的空格，还要注意去掉列表最后一个列名的逗号。利用动态 SQL 这一特性可以彻底摆脱这种痛苦。
虽然在以前使用动态 SQL 并非一件易事，但正是 MyBatis 提供了可以被用在任意 SQL 映射语句中的强大的动态 SQL 语言得以改进这种情形。
动态 SQL 元素和 JSTL 或基于类似 XML 的文本处理器相似。在 MyBatis 之前的版本中，有很多元素需要花时间了解。MyBatis 3 大大精简了元素种类，现在只需学习原来一半的元素便可。MyBatis 采用功能强大的基于 OGNL 的表达式来淘汰其它大部分元素。

  -------------------------------
  - if
  - choose (when, otherwise)
  - trim (where, set)
  - foreach
  -------------------------------
```

我们之前写的 SQL 语句都比较简单，如果有比较复杂的业务，我们需要写复杂的 SQL 语句，往往需要拼接（append），而拼接 SQL ，稍微不注意，由于引号，空格等缺失可能都会导致错误。

那么怎么去解决这个问题呢？这就要使用 mybatis 动态SQL，通过 if, choose, when, otherwise, trim, where, set, foreach等标签，可组合成非常灵活的SQL语句，从而在提高 SQL 语句的准确性的同时，也大大提高了开发人员的效率。

### 基本环境

#### 	1. 搭配基本环境( MyBatis工具类、IDutil类、建立sql表、依赖包、核心配置文件、实体类)

> ​	创建一个基础工程
>
> MyBatis工具类
>
> ```java
> package com.kuang.utils;
> 
> import org.apache.ibatis.io.Resources;
> import org.apache.ibatis.session.SqlSession;
> import org.apache.ibatis.session.SqlSessionFactory;
> import org.apache.ibatis.session.SqlSessionFactoryBuilder;
> 
> import java.io.IOException;
> import java.io.InputStream;
> 
> /**作用:SqlSessionFactory ---> sqlSession 从工厂中拿出一个sqlSession
>  * 即读取配置文件，获取工厂
>  * @author <作者>
>  * @version <版本>
>  * @since <JDK版本>
>  */
> public class MybatisUtils {
>     private static SqlSessionFactory sqlSessionFactory;
>     static {
>         try {
>             //使用MyBatis第一步：获取sqlSessionFactory对象
>             String source = "mybatis-config.xml";
>             InputStream inputStream = Resources.getResourceAsStream(source);
>             //通过配置文件流建造一个工厂
>             sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
>         } catch (IOException e) {
>             e.printStackTrace();
>         }
>     }
>     //要获取数据库对象就用这个类就可以了
>     public static SqlSession getSqlSession(){
>         //设置为true之后，你以后增删改就不用手动提交事务了,设置为true，自动提交事务
>         SqlSession sqlSession = sqlSessionFactory.openSession(true);
>         /*
>         当然也可以直接return整合到一句，这里为了清晰就不弄了
>         作用：返回一个连接对象
>          */
>         return sqlSession;
>     }
> 
> }
> 
> ```
>
> ​	Sql语句
>
> ```sql
> /*
> COMMENT 是备注、注释的意思，
> 写上COMMENT 'xxxx'之后，在建表信息里可以看到添加的备注信息。
> */
> CREATE TABLE `blog`(
> `id` VARCHAR(50) NOT NULL COMMENT '博客id',
> `title` VARCHAR(100) NOT NULL COMMENT '博客标题',
> `author` VARCHAR(30) NOT NULL COMMENT '博客作者',
> `create_time` DATETIME NOT NULL COMMENT '创建时间',
> `views` INT(30) NOT NULL COMMENT '浏览量'
> )ENGINE=INNODB DEFAULT CHARSET=utf8
> ```
>
> 
>
> 		1. 在Maven里面导入依赖包
>
> ```xml
> <!--父工程-->
> <!--导入依赖-->
>     <dependencies>
>         <!--mysql驱动-->
>         <dependency>
>             <groupId>mysql</groupId>
>             <artifactId>mysql-connector-java</artifactId>
>             <version>5.1.47</version>
>         </dependency>
>         <!--mybatis-->
>         <dependency>
>             <groupId>org.mybatis</groupId>
>             <artifactId>mybatis</artifactId>
>             <version>3.5.2</version>
>         </dependency>
>         <!--junit-->
>         <dependency>
>             <groupId>junit</groupId>
>             <artifactId>junit</artifactId>
>             <version>4.11</version>
>             <scope>test</scope>
>         </dependency>
>     </dependencies>
> ```
>
> ```xml
> <!--子工程-->
> <dependencies>
>     <dependency>
>         <groupId>org.projectlombok</groupId>
>         <artifactId>lombok</artifactId>
>         <version>RELEASE</version>
>     </dependency>
> </dependencies>
> ```
>
> 2.编写配置文件
>
> ```xml
> <?xml version="1.0" encoding="utf-8" ?>
> <!DOCTYPE configuration
>         PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
>         "http://mybatis.org/dtd/mybatis-3-config.dtd">
> <!--configuration：核心配置文件-->
> <configuration>
>     <!--
>         ps:db.properties直接在resources文件夹下，就不用写路径了
>         作用:引入外部配置文件
>     -->
>     <properties resource="db.properties">
>         <!--
>         这里面也可以写db.properties的配置信息,这样写了db.properties里面的username和password键值对就可以删了
>             首先读取在 properties 元素体内指定的属性。
> 
>             然后根据 properties 元素中的 resource 属性读取类路径下属性文件
>             或根据 url 属性指定的路径读取属性文件，并覆盖之前读取过的同名属性。
> 
>             最后读取作为方法参数传递的属性，并覆盖之前读取过的同名属性。
> 
>             总结：也就是先读取的是properties 元素体内指定的属性，如果没有再读db.properties的属性,
>             如果属性两个地方都存在，那还是得听db.properties的属性，所以db.properties里面的属性千万不能错，
>             这db.properties错了，你里面的属性对了也没用！因为会被db.properties的错误属性给覆盖掉
>         -->
>     </properties>
> 
>     <settings>
>         <!--标注的日志工厂实现-->
>         <setting name="logImpl" value="STDOUT_LOGGING"/>
>         <!--
>         是否开启自动驼峰命名规则(数据库字段名有下划线的 允许成员变量名驼峰自动转换)
>         很常用的这个 基本大部分命名规则都是这样的 可以减少resultMap的编写量
>         -->
>         <setting name="mapUnderscoreToCamelCase" value="true"/>
>     </settings>
> 
> 
>     <!--
>         给类型取别名
>             好处:一劳永逸,以后你想用这个类型直接写别名就好了
>             当这样配置时，User可以用在任何使用com.kuang.pojo.User的地方。
>     -->
>     <typeAliases>
>         <!--每一个在包 com.kuang.pojo 中的 Java Bean(如User)，
>         在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名(user)，
>         当然了你大写(User)作为它的别名也是可以的。-->
>         <package name="com.kuang.pojo"/>
> <!--        <typeAlias alias="User" type="com.kuang.pojo.User"/>-->
>     </typeAliases>
>     <environments default="development">
>         <environment id="development">
>             <transactionManager type="JDBC"/>
>             <dataSource type="POOLED">
> <!--                <property name="driver" value="com.mysql.jdbc.Driver"/>-->
>                 <property name="driver" value="${driver}"/>
>                 <!--&amp;就相当于 & 符号，只是在xml中需要些转义-->
> <!--                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false&amp;useUnicode=true&amp;characterEncoding=utf8"/>-->
>                 <property name="url" value="${url}"/>
>                 <property name="username" value="${username}"/>
>                 <property name="password" value="${password}"/>
>             </dataSource>
>         </environment>
> 
>        <!--
>        我们如果想自定义一个test环境也是可以的，再将environments的default设置为test
>        <environment id="test">
>             <transactionManager type="JDBC"/>
>             <dataSource type="POOLED">
>                 <property name="driver" value="com.mysql.jdbc.Driver"/>
>                 <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=utf8"/>
>                 <property name="username" value="root"/>
>                 <property name="password" value="123456"/>
>             </dataSource>
>         </environment>
>         -->
>     </environments>
> 
> 
>     <mappers>
>         <!--第一种绑定方法，指定xml-->
> <!--        <mapper resource="com/kuang/dao/TeacherMapper.xml"/>-->
>         <!--第二种绑定方法，指定接口-->
> <!--        <mapper class="com.kuang.dao.TeacherMapper"/>-->
> <!--        <mapper class="com.kuang.dao.StudentMapper"/>-->
>         <!--第三种绑定方法，写出包名即可-->
>         <package name="com.kuang.dao"/>
>     </mappers>
> 
> </configuration>
> ```
>
> 3.编写实体类
>
> ```java
> package com.kuang.pojo;
> 
> import lombok.AllArgsConstructor;
> import lombok.Data;
> import lombok.NoArgsConstructor;
> 
> import java.util.Date;
> 
> /**Blog实体类
>  * @author <作者>
>  * @version <版本>
>  * @since <JDK版本>
>  */
> @Data
> @AllArgsConstructor
> @NoArgsConstructor
> public class Blog {
>     /**
>      * 博客id
>      */
>     private String id;
>     /**
>      * 博客标题
>      */
>     private String title;
>     /**
>      * 博客作者
>      */
>     private String author;
>     /**
>      * 创建时间 属性名和字段名不一样
>      */
>     private Date createTime;
>     /**
>      * 浏览量
>      */
>     private int views;
> }
> 
> ```
>
> 4. IDutil工具类( 用于自动生成用户id)
>
>
> ```java
> package com.kuang.utils; 
> import org.junit.Test;
> 
> import java.util.UUID;
> 
> /**作用:生成用户id
>     * @author <作者>
>     * @version <版本>
>     * @since <JDK版本>
>     */
> @SuppressWarnings("all")//镇压所有警告，就不会再报什么警告了
> public class IDUtils {
>     public static String getID(){
>         /*
>            使用Uuid，来随产随机用户id，以保证用户id的唯一性
>                但是不建议使用uuid
>                UUID是基于当前时间、计数器（counter）和硬件标识（通常为无线网卡的MAC地址）
>                等数据计算生成的
>                mysql官方都推荐使用自增id，为了保持索引的连续性，
>                uuid做主键性能贼差，实际开发基本没企业这样用过
> replaceAll("-","")是指将产生的字符串中的-替换为空
>         */
>         return UUID.randomUUID().toString().replaceAll("-","");
>     }
>     /*
> 测试是否有用
> */
>     @Test
>     public void Test(){
>         /*
>        04b40a057fcd438c8f2d84ca63546eab
>        a53bcbfe9b744b1a916042f1f09ba787
>        add9e97a60a443adaa65d90384708135
>         */
>         System.out.println(IDUtils.getID());
>         System.out.println(IDUtils.getID());
>         System.out.println(IDUtils.getID());
>     }
> }
> ```

#### 	2. 编写实体类对应的Mapper和Mapper.xml文件

##### 		1. Mapper接口

```java
public interface BlogMapper {

}
```

##### 		2. xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
       PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.kuang.mapper.BlogMapper">
</mapper>
```

#### 	3. mybatis核心配置文件，下划线驼峰自动转换

```xml
<settings>
        <!--标注的日志工厂实现-->
        <setting name="logImpl" value="STDOUT_LOGGING"/>
        <!--
        是否开启自动驼峰命名规则(数据库字段名有下划线的 允许成员变量名驼峰自动转换)
        很常用的这个 基本大部分命名规则都是这样的 可以减少resultMap的编写量
        -->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
<!--注册Mapper.xml-->
<mappers>
    <!--第一种绑定方法，指定xml-->
    <!--        <mapper resource="com/kuang/dao/TeacherMapper.xml"/>-->
    <!--第二种绑定方法，指定接口-->
    <!--        <mapper class="com.kuang.dao.TeacherMapper"/>-->
    <!--        <mapper class="com.kuang.dao.StudentMapper"/>-->
    <!--第三种绑定方法，写出包名即可-->
    <package name="com.kuang.dao"/>
</mappers>
```

#### 4. 插入初始数据

​	编写接口的方法

```java
//新增一个博客
int addBlog(Blog blog);
```

​	sql配置文件

```xml
<insert id="addBlog" parameterType="blog">
  insert into blog (id, title, author, create_time, views)
  values (#{id},#{title},#{author},#{createTime},#{views});
</insert>
```

​	初始化博客方法

```java
@Test
public void addInitBlog(){
   SqlSession session = MybatisUtils.getSession();
   BlogMapper mapper = session.getMapper(BlogMapper.class);

   Blog blog = new Blog();
   blog.setId(IDUtil.genId());
   blog.setTitle("Mybatis如此简单");
   blog.setAuthor("狂神说");
   blog.setCreateTime(new Date());
   blog.setViews(9999);

   mapper.addBlog(blog);

   blog.setId(IDUtil.genId());
   blog.setTitle("Java如此简单");
   mapper.addBlog(blog);

   blog.setId(IDUtil.genId());
   blog.setTitle("Spring如此简单");
   mapper.addBlog(blog);

   blog.setId(IDUtil.genId());
   blog.setTitle("微服务如此简单");
   mapper.addBlog(blog);

   session.close();
}
```

初始化数据完毕！



### 	1. if语句

> ​	思考：我们需要修改哪些文件?
> ​	答: 需要修改Mapper文件，因为我们需要新增一个方法来实现这个语句的功能，和Mapper.xml文件（**关键**），新增测试类中的一个测试方法 

```java
//Mapper文件
/**
* 查询博客
* @param map 用户存放的查询条件
* @return 查询出来的用户数据
*/
List<Blog> queryBlogIF(Map map);
```

​	

```xml
<!--Mapper.xml文件-->
<!--
    我们这里加1=1，作用主要是进行拼接（就是把where标签写上去）
    不然如果用户不想通过某个条件查，你还要判断啥的，再加where，很麻烦
    比如不加1=1的情况
    select * from mybatis.blog
    <if test="title != null">
        where title LIKE  #{title};
    </if>
    然后你用其他条件的时候，就要看看了,还要判断title不能为空，不然where没有会报错
    这难道有where 1=1香吗?
    <if test="author != null and title != null">
        and author LIKE  #{author};
    </if>
    -->
    <select id="queryBlogIF" resultType="Blog" parameterType="Map">
        /*
         注意分号 ';' 放在最后一句！
         */
        select * from mybatis.blog where 1 = 1
        /*
         这里的title就是我们的map里的键名
         */
        <if test="title != null">
            and title LIKE  #{title}
        </if>

        <if test="author != null">
            and author LIKE  #{author};
        </if>
    </select>
```

```java
//测试类
/**
     * 根据select标签内嵌一个if标签进行查询对应数据
     */
    @Test
    public void queryBlogIF(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
        //这就是我们想查询的条件，你想通过什么查，你就放进去就好了
        Map<String,Object> map = new HashMap<>();
        /*map.put("title","Java");
        map.put("author","狂神说");*/
        List<Blog> blogList = mapper.queryBlogIF(map);
        for (Blog blog : blogList) {
            System.out.println(blog);
        }
        sqlSession.close();
    }
```

### 	2. choose语句

> ​	简介：即只会从多个when标签中从上至下，选取其中一个最先满足条件的when标签进行对应的相应操作
> ​	**与if语句区别**：if语句可以进行拼接，可以选取多个条件；而where语句只能进行单个条件的选取，不用拼接(and)

```java
//BlogMapper文件
/**
     * 根据指定的那某一项条件进行查询
     * @param map 用户存放的查询条件
     * @return  查询出来的用户数据
     */
List<Blog> queryBlogChoose(Map map);
```

```xml
<!--BlogMapper.xml-->
<select id="queryBlogChoose" resultType="Blog" parameterType="Map">
        select * from mybatis.blog
            /*
    		第一个when：
                当文档标题不为空的时候，满足就执行这个标签下的语句
            第二个when:
                当文档标题和作者同时不为空，我们才会执行第二个标签下的语句
            otherwise；
                如果都没有符合的，那就执行这语句
            思考:肯定是执行一个为什么不全把and 和where标签省了
            网友:这里的所有条件只会有一个成立，所以and where 都不用加

            自己：我这里是sql语句是 select * from mybatis.blog 所以where必须要加，and确实不用加,
            如果你sql语句是 select * from mybatis.blog where 1 = 1，那where和and都不用加了
              */
        <where>
            <choose>
                <when test="title != null">
                     title = #{title}
                </when>
                <when test="title != null and author != null">
                    author = #{author}
                </when>
                <otherwise>
                /*这个就是如果前面多个语句都不满足，那么就查询这条语句，即通过浏览量来查询,当然用户Test类也必须为浏览量属性设置值*/
                    views = #{views};
                </otherwise>
            </choose>
        </where>
    </select>
```

```java
/**Test类
     * 根据select标签内嵌一个where 和 choose标签进行查询对应数据
     */
    @Test
    public void queryBlogChoose(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
        //这就是我们想查询的条件，你想通过什么查，你就放进去就好了
        Map<String,Object> map = new HashMap<>();
        map.put("title","Java");
        map.put("author","狂神说");
//        map.put("views",1000);
        List<Blog> blogList = mapper.queryBlogChoose(map);
        for (Blog blog : blogList) {
            System.out.println(blog);
            /*
            这个choose标签就相当于java中的switch语句，一个when标签就相当于是case，同时自带break
            只要有一个when语句满足，就执行这条，然后退出choose语句
            输出：Blog(id=8152338653544e1f884efef7320720a7, title=Java, author=狂神说, createTime=null, views=9999)

             */
        }
        sqlSession.close();
    }
```





### 	3. Trim(where语句、set语句)

#### 		1. where语句

​		用于增添处理条件

```xml
<select id="queryBlogIF" resultType="Blog" parameterType="Map">
        <!--/*
         注意分号 ';' 放在最后一句！
         */
        select * from mybatis.blog where 1 = 1
        /*
         这里的title就是我们的map里的键名
         */
        <if test="title != null">
            and title LIKE  #{title}
        </if>

        <if test="author != null">
            and author LIKE  #{author};
        </if>-->
   		<!--
        还有另外一种方式，就是where标签来处理
            区别：不用再加where 1=1了,它会自动帮你添加where关键字
		   为什么不要1=1：1=1的话 如果程序员把and关键字打成or就会出问题，
	    where可以避免这个问题,总之就是更加安全
            特殊点：你就算在拼接sql语句中第一句添加了and,这在普通sql语句里
        是不被允许的，但是我们这where标签会判断如果你第一个拼接语句如果成功
        拼接，会自动帮你把and去掉，你就不用管了，当然了为了一个良好的编程习惯
        还是建议删掉最先添加的sql语句前的 and关键字
     	-->
        <where>
            /*
            注意分号 ';' 放在最后一句！
            */
            select * from mybatis.blog
            <if test="title != null">
                title LIKE  #{title}
            </if>
            <if test="author != null">
                and author LIKE  #{author};
            </if>
        </where>
    </select>
```

#### 	2. set语句

​		用于更新数据

```xml
<update id="updateBlog" parameterType="Map">
        update mybatis.blog
        <set>
            /*
              这里强调一下，
              mybatis你的语句里多写了逗号，and他会帮你删，
              但是少写了直接报错
            */
            <if test="title != null">
                title = #{title},
            </if>
            <if test="author != null">
                author = #{author}
            </if>
            where id = #{id}
        </set>
    </update>
```



### 4. Sql片段

> ​	作用：就是提高我们sql语句中逻辑代码的复用性，就是将那些if语句，choose语句等等，如果你需要经常使用他们，那么就可以将他们封装起来，方便复用。

####  步骤:		

​	 (1) 使用Sql标签抽取公共部分(即你想要复用的部分)

​		为了有更好的对比，我们将原本的拿来

```xml
<select id="queryBlogIF" resultType="Blog" parameterType="Map">
    select * from mybatis.blog
    <where>
        <if test="title != null">
            title LIKE  #{title}
        </if>
        <if test="author != null">
            and author LIKE  #{author};
        </if>
    </where>
</select>
```

​		抽取之后的样子

```xml
<!--sql标签是和select标签等其他标签平级的-->
<sql id="if-title-author">
    <if test="title != null">
        title LIKE  #{title}
    </if>
    <if test="author != null">
        and author LIKE  #{author};
    </if>
</sql>
```

(2)  在需要使用这个逻辑代码的地方通过include标签引用即可

```xml
<!--我们在Mapper里面新建了一个方法来实现这个功能，所以id变了-->
<select id="queryBlogForSql" resultType="Blog" parameterType="Map">
    select * from mybatis.blog
    <where>
        /*refid就是reference id 引用id的意思*/
        <include refid="if-title-author"/>
    </where>
</select>
```



```java
//Test类
@Test
public void queryBlogForSql(){
    BlogMapper mapper = MybatisUtils.getSqlSession().getMapper(BlogMapper.class);
    Map<String, Object> map = new HashMap<>();
    map.put("title","Java真不错");
    map.put("author","狂神小说");
    map.put("id","8152338653544e1f884efef7320720a7");
    List<Blog> blogs = mapper.queryBlogForSql(map);
    for (Blog blog : blogs) {
        System.out.println(blog);
        //Blog(id=8152338653544e1f884efef7320720a7, title=Java真不错, author=狂神小说, createTime=null, views=9999)
    }
}
```

#### 注意：

##### 	①、最好基于 单表来定义 sql 片段，提高片段的可重用性

> ​		也就是sql片段里面不要做什么太复杂的东西，因为这sql片段作用只是为了帮我们引入一些很简单的，不想再写一遍的逻辑代码。越复杂的代码重复性一般就越低

##### 	②、在 sql 片段中不要包括 where

> 也就是说，如果你把where丢进去，那跟直接写就没有什么区别了，就是我们sql片段进行包含的时候，尽量选一些if判断就可以了



### 5.  Foreach

​	之后都是那三个编辑顺序，这里就不一一赘述了

```java
/**
     * 查询第1-2-3号记录的博客
     * @param map id集合
     * @return
     */
List<Blog> queryBlogForEach(Map map);
```

```xml
<select id="queryBlogForEach" resultType="Blog" parameterType="Map">
    select * from mybatis.blog
    /*
    foreach:其实就是遍历collection，将元素拼接成一个条件串
    分析：
        collection就是我们存放进去的list集合名称
        item就是遍历出来每一项的名称，我们要拼的就是它
        open就是从1=1后面开始拼
        注意：
            这里我们open直接写and没有关系，因为where标签会自动帮我们处理，如果你and多出来的话会帮你删掉
    */
    <where>
        <foreach collection="ids" item="id" open="and (" close=")" separator="or">
            id = #{id}
        </foreach>
    </where>
</select>
```

```java
/**
     * 不想写这两句话的，
     * 可以在Test类里面定义@After和@Before标签，
     * 每个test方法开始或结束都会执行一次
     */
//把得到sqlSession这两句放到before里面，就不用每次都写了
@Test()
public void queryBlogForEach(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);
    Map map = new HashMap();
    ArrayList<Integer> ids = new ArrayList<Integer>();
    ids.add(1);
    ids.add(2);
    ids.add(3);
    map.put("ids",ids);
    List<Blog> blogs = mapper.queryBlogForEach(map);
    for (Blog blog : blogs) {
        System.out.println(blog);
        //Blog(id=8152338653544e1f884efef7320720a7, title=Java真不错, author=狂神小说, createTime=null, views=9999)
    }
    sqlSession.close();
}
```

建议：

- 先在MySql中写出完整的Sql语句，再对应的去修改成为我们的动态Sql实现通用即可！

### 总结：

> - ==所谓的动态Sql，本质上其实还是sql语句，只是我们可以在SQL层面，去执行一个逻辑代码(就是那些判断)
>   比我们之前在Dao层里面写什么字符串拼接舒服很多，降低了耦合度，更容易修改数据,条理清晰。==	
> - ==其实动态 sql 语句的编写往往就是一个拼接的问题，为了保证拼接准确，我们最好首先要写原生的 sql 语句出来，然后在通过 mybatis 动态sql 对照着改，防止出错。多在实践中使用才是熟练掌握它的技巧。==	
> - ==动态SQL总结就是拼接SQL语句，我们只要保证SQL的正确性，按照SQL的格式，去排列组合就可以了==



## 13. 缓存(了解)

### 	1. 简介

#### 		1、什么是缓存 [ Cache ]？

> - 存在内存中的临时数据。
>
> - 将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上(关系型数据库数据文件)查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题。
>
>   

#### 		2、为什么使用缓存？

> - 减少和数据库的交互次数，减少系统开销，提高系统效率。

#### 		3、什么样的数据能使用缓存？

> - 经常查询并且不经常改变的数据。【可以使用缓存】如：网页排版文件(黑马旅游网中的旅游图片)
> - 不经常查询或者经常改变(写)的数据。【**不推荐**使用缓存】如：实时排行榜、账户、购物车(经常改变)等

### 	2. Mybatis缓存

- MyBatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大的提升查询效率。

- MyBatis系统中默认定义了两级缓存：**一级缓存**和**二级缓存**

- - 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也称为本地缓存）
  - 二级缓存需要手动开启和配置，他是基于namespace级别的缓存。
  - 为了提高扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存



#### 		1. 一级缓存

​			一级缓存也叫本地缓存(sqlSession级别)：

> - 与数据库同一次会话期间查询到的数据会放在本地缓存中。
> - 以后如果需要获取相同的数据，直接从缓存中拿，没必须再去查询数据库；

##### 	1. 测试

###### 			1、创建核心配置文件

###### 			2、创建实体类

​			注意：因为下面需要进行对象比较，就没有用注解了，因为@Data覆盖了@ToString方法

```java
package com.kuang.pojo;

/**
 * @author <作者>
 * @version <版本>
 * @since <JDK版本>
 */

public class User {

    private Integer id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(Integer id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

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
    public String getPwd() {
        return pwd;
    }
    public void setPwd(String pwd) {
        this.pwd = pwd;
    }
}
```

###### 			3、创建工具类(Mybatis)

```java
package com.kuang.utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

/**作用:SqlSessionFactory ---> sqlSession 从工厂中拿出一个sqlSession
 * 即读取配置文件，获取工厂
 * @author <作者>
 * @version <版本>
 * @since <JDK版本>
 */
public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    static {
        try {
            //使用MyBatis第一步：获取sqlSessionFactory对象
            String source = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(source);
            //通过配置文件流建造一个工厂
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    //要获取数据库对象就用这个类就可以了
    public static SqlSession getSqlSession(){
        //设置为true之后，你以后增删改就不用手动提交事务了,设置为true，自动提交事务
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        /*
        当然也可以直接return整合到一句，这里为了清晰就不弄了
        作用：返回一个连接对象
         */
        return sqlSession;
    }
}
```



###### 			4、编写接口方法

```java
//根据id查询用户
User queryUserById(@Param("id") int id);
```

###### 			5.、接口对应的Mapper文件

```xml
<select id="queryUserById" resultType="user">
  select * from user where id = #{id}
</select>
```

###### 			6.、测试

```java
@Test
    public void queryUserById(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = mapper.queryUserById(4);
        //com.kuang.pojo.User@205d38da
        System.out.println(user);

        User user2 = mapper.queryUserById(3);
        //com.kuang.pojo.User@6dd7b5a3
        System.out.println(user2);
        //false:访问不同的记录，当然就不同的缓存了
        System.out.println(user == user2);

        User user3 = mapper.queryUserById(4);
        //com.kuang.pojo.User@205d38da
        System.out.println(user3);
        //true:如果查询相同记录，内存中缓存相同
        System.out.println(user == user3);

        sqlSession.close();
    }
```

###### 			7、结果分析

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\(13)缓存分析.png)



##### 2.一级缓存失效的五种情况

> ​	一级缓存是SqlSession级别的缓存，是一直开启的，我们关闭不了它；
>
> ​	一级缓存失效情况：没有使用到当前的一级缓存，效果就是，还需要再向数据库中发起一次查询请求！

###### 		1、sqlSession不同

```java
@Test
public void testQueryUserById(){
   SqlSession session = MybatisUtils.getSession();
   SqlSession session2 = MybatisUtils.getSession();
   UserMapper mapper = session.getMapper(UserMapper.class);
   UserMapper mapper2 = session2.getMapper(UserMapper.class);

   User user = mapper.queryUserById(1);
   System.out.println(user);
   User user2 = mapper2.queryUserById(1);
   System.out.println(user2);
   System.out.println(user==user2);

   session.close();
   session2.close();
}
```

​		观察结果：发现发送了两条SQL语句！

​		结论：**每个sqlSession中的缓存相互独立**

###### 		2、sqlSession相同，查询条件不同

```java
@Test
public void testQueryUserById(){
   SqlSession session = MybatisUtils.getSession();
   UserMapper mapper = session.getMapper(UserMapper.class);
   UserMapper mapper2 = session.getMapper(UserMapper.class);

   User user = mapper.queryUserById(1);
   System.out.println(user);
   User user2 = mapper2.queryUserById(2);
   System.out.println(user2);
   System.out.println(user==user2);

   session.close();
}
```

​		观察结果：发现发送了两条SQL语句！很正常的理解

​		结论：**当前缓存中，不存在这个数据**

###### 		3、sqlSession相同，两次查询之间执行了增删改操作！

​		增加方法

```java
//修改用户
int updateUser(Map map);
```

​		编写SQL

```xml
<update id="updateUser" parameterType="map">
  update user set name = #{name} where id = #{id}
</update>
```

​		测试

```java
@Test
    public void queryUserById(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        User user = mapper.queryUserById(4);//
        //第一种普通情况com.kuang.pojo.User@205d38da
        //第二种中间添加了更新语句的情况 com.kuang.pojo.User@2663e964
        System.out.println(user);

        User user2 = mapper.queryUserById(3);
        //第一种普通情况com.kuang.pojo.User@6dd7b5a3
        //第二种中间添加了更新语句的情况 com.kuang.pojo.User@189cbd7c
        System.out.println(user2);
        //第一种普通情况false:访问不同的记录，当然就不同的缓存了
        //第二种中间添加了更新语句的情况false:
        System.out.println(user == user2);


        //现在我们中间插入一条更新语句看看怎么样
        mapper.updateUser(new User(3,"李四","142616"));

        User user3 = mapper.queryUserById(4);
        //第一种普通情况 com.kuang.pojo.User@205d38da
        //第二种中间添加了更新语句的情况 com.kuang.pojo.User@5e21e98f
        System.out.println(user3);
        //第一种普通情况.true:如果查询相同记录，内存中缓存相同
        /*第二种中间添加了更新语句的情况 false：
        重点思考:
            为什么中间加了一句更新语句，怎么导致缓存失效了呢

         */
        System.out.println(user == user3);

        sqlSession.close();
    }
```

​		观察结果：查询在中间执行了增删改操作后，重新执行了

​		结论：==**因为增删改操作可能会对当前数据产生影响，所以会重新查一次**==

###### 		4、sqlSession相同，手动清除一级缓存

```java
@Test
public void testQueryUserById(){
   SqlSession session = MybatisUtils.getSession();
   UserMapper mapper = session.getMapper(UserMapper.class);

   User user = mapper.queryUserById(1);
   System.out.println(user);

   session.clearCache();//手动清除缓存

   User user2 = mapper.queryUserById(1);
   System.out.println(user2);

   System.out.println(user==user2);

   session.close();
}
```

​		一级缓存就是一个map

###### 		5、查询不同的Mapper

​		这个连二级缓存都不存在，更别说一级缓存了

##### 3.总结：

> 一级缓存默认是开启的，只在一次sqlSession中有效，也就是拿到连接到关闭连接这个区间中有效！
>
> 一级缓存没什么太大的作用，也就只要查询同一条记录的时候，能更方便一点罢了。不然其他操作都会再给你开辟新的内存空间。

#### 2. 二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存

- 基于namespace级别的缓存，一个名称空间，对应一个二级缓存；

- 工作机制

- - 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；

  - 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是我们想要的是，==**会话关闭了，一级缓存中的数据被保存到二级缓存中**==；

  - 新的会话查询信息，就可以从二级缓存中获取内容；

  - 不同的mapper查出的数据会放在自己对应的缓存（map）中；

    

#####       1.使用步骤

###### 						1、开启全局缓存 【mybatis-config.xml】

```xml
<!--设置开启二级缓存，虽然默认是开的，但是我们还是最好写出来-->
<setting name="cacheEnabled" value="ture"/>
```

###### 						2、去每个mapper.xml中配置使用二级缓存【xxxMapper.xml】

```xml
<!--最简单格式，但是没有什么用这样-->
<cache/>
================================================================================
<!--
这个才是正常格式
	配置解析：
            eviction
                LRU-最近最少使用:移除最长时间不被使用的对象。
                FIFO-先进先出:按对象进入缓存的顺序来移除它们。
                SOFT-软引用:基于垃圾回收器状态和软引用规则移除对象。
                WEAK-弱引用:更积极地基于垃圾收集器状态和弱引用规则移除对象。
            flushInterval(刷新间隔)
                属性可以被设置为任意的正整数,设置的值应该是一个以亳秒为单位的合理时间量。
                默认情况是不设置,也就是没有刷新间隔,缓存仅仅会在调用语句时刷新。
            size(引用数目)
                属性可以被设置为任意正整数,
                要注意欲缓存对象的大小和运行环境中可用的内存资源。
                默认值是1024。
            readonly(只读)
                属性可以被设置为true或false。
                只读的緩存会给所有调用者返回缓存对象的相同实例。
                因此这些对象不能被修改。这就提供了可观的性能提升。
                而可读写的缓存会(通过序列化)返回缓存对象的拷贝。
                速度上会慢一些,但是更安全,因此默认值是 false
			   readOnly为True时是读相同实例，false时是读拷贝值，相对安全，所以true会出现比较时出现false
	解析:
        这个更高级的配置创建了一个 FIFO 缓存，每隔 60 秒刷新，
        最多可以存储结果对象或列表的 512 个引用，而且返回的对象被认为是只读的，
        因此对它们进行修改可能会在不同线程中的调用者产生冲突。
	
-->
<cache
 eviction="FIFO"
 flushInterval="60000"
 size="512"
 readOnly="true"/>
```

###### 				3.注意

​	如果你是要<cache/> 那么就需要将实体类进行序列化操作（将堆内存中的java对象，通过某种方式将对象存储到磁盘的文件中，或者传递给其他的网络节点( 网络传输)，**总结**：将对象转为二进制，用于保存或者网络传输 ）

​	操作就是让实体类实现Serializable接口，即可

##### 2.小结

- 只要开启了二级缓存，我们在同一个Mapper中的查询，可以在二级缓存中拿到数据

- 查出的数据都会被默认先放在一级缓存中

- 只有会话提交或者关闭以后，一级缓存中的数据才会转到二级缓存中

	

##### 3.二级缓存没有的两种情况

> 网友疑问：如果二级缓存都没有，那么一级缓存那不是更不可能有？直接查数据库不好吗？
>
> 答：二级缓存是在一级缓存关闭时保存一级缓存的缓存数据，二级缓存没有，有两个可能，
>
> ​	一个可能是session没关闭，缓存在一级，还 没有保存到二级缓存中
> ​	另一个可能是一级缓存也没有，需要从数据库查询

#### 3. 缓存执行原理

​	会先去二级缓存查找，没有的话再去查找一级缓存，再没有就查数据库，先缓存到一级等到连接关闭就缓存到二级缓存中去

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\(13)缓存执行原理.png)



#### 4. 自定义缓存--EhCache

```
EhCache是一种广泛使用的开源的java分布式缓存，主要面向通用缓存。
PS：现在redis逐步成为主流，这个ehcache就用的越来越少了
```

 要在应用程序中使用Ehcache，需要引入依赖的jar包

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis.caches/mybatis-ehcache -->
<dependency>
   <groupId>org.mybatis.caches</groupId>
   <artifactId>mybatis-ehcache</artifactId>
   <version>1.1.0</version>
</dependency>
```

在mapper.xml中使用对应的缓存即可

```xml
<mapper namespace = “org.acme.FooMapper” >
   <cache type = “org.mybatis.caches.ehcache.EhcacheCache” />
</mapper>
```

编写ehcache.xml文件，如果在加载时未找到/ehcache.xml资源或出现问题，则将使用默认配置。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
        updateCheck="false">
   <!--
      diskStore：为缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。参数解释如下：
      user.home – 用户主目录
      user.dir – 用户当前工作目录
      java.io.tmpdir – 默认临时文件路径
    -->
   <diskStore path="./tmpdir/Tmp_EhCache"/>
   
   <defaultCache
           eternal="false"
           maxElementsInMemory="10000"
           overflowToDisk="false"
           diskPersistent="false"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="259200"
           memoryStoreEvictionPolicy="LRU"/>

   <cache
           name="cloud_user"
           eternal="false"
           maxElementsInMemory="5000"
           overflowToDisk="false"
           diskPersistent="false"
           timeToIdleSeconds="1800"
           timeToLiveSeconds="1800"
           memoryStoreEvictionPolicy="LRU"/>
   <!--
      defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
    -->
   <!--
     name:缓存名称。
     maxElementsInMemory:缓存最大数目
     maxElementsOnDisk：硬盘最大缓存个数。
     eternal:对象是否永久有效，一但设置了，timeout将不起作用。
     overflowToDisk:是否保存到磁盘，当系统当机时
     timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
     timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
     diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
     diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
     diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
     memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。你可以设置为FIFO（先进先出）或是LFU（较少使用）。
     clearOnFlush：内存数量最大时是否清除。
     memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
     FIFO，first in first out，这个是大家最熟的，先进先出。
     LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个hit属性，hit值最小的将会被清出缓存。
     LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
  -->

</ehcache>
```



## N.书上知识点

### 		1.#与$取值的区别

> ​		我们以下面一句代码作为例句
>
> ```sql
> update user set name=#{name},pwd=#{password} where id=#{id}
> ```
>
> #### 		#{}格式
>
> ​	将传入的数据都当成一个字符串，会对自动传入的数据加一个双引号，具体如下
> ​	如果name传入"张三"，password传入"242144"，id传入1，则sql会解析为
>
> ```sql
> update user set name="张三",pwd="242144" where id="1"
> ```
>
> #### 		${}格式
>
> ​	将传入的数据直接显示生成到sql中，具体如下
> ​	如果name传入"张三"，password传入"242144"，id传入1，则sql会解析为
>
> ```sql
> update user set name=张三,pwd=242144 where id=1
> ```
>
> #### 

> #### 	区别:
>
> ​	#方式能够很大程度的防止sql注入问题，$方式无法防止sql注入,
> ​	所以一般建议采用#,而不是$

​	

## 新学知识点(老师没说):

### 	1. COMMENT(Sql系)

> ​		就是你加了COMMENT只是为了给字段一个提示，让开发者更快明白是代表什么意思
>
> ```sql
> /*
> COMMENT 是备注、注释的意思，
> 写上COMMENT 'xxxx'之后，在建表信息里可以看到添加的备注信息。
> */
> CREATE TABLE `blog`(
> `id` VARCHAR(50) NOT NULL COMMENT '博客id',
> `title` VARCHAR(100) NOT NULL COMMENT '博客标题',
> `author` VARCHAR(30) NOT NULL COMMENT '博客作者',
> `create_time` DATETIME NOT NULL COMMENT '创建时间',
> `views` INT(30) NOT NULL COMMENT '浏览量'
> )ENGINE=INNODB DEFAULT CHARSET=utf8
> ```
>
> 

### 2.CRUD标签返回值类型什么时候写

> 只有查询的时候有返回值类型，增删改 没有resultType属性 默认就是int
>
> ```xml
> <select id="queryBlogChoose" resultType="Blog" parameterType="Map"></select>
> ```
>
> ```xml
> <update id="updateBlog" parameterType="Map"></update>
> ```
>
> 

## 数据库知识点:

### 	1.读写分离(缓存数据库解决如：redis)，主从复制

![](E:\软件工作室\MD文档(第二阶段)\01-MyBatisImage\数据库知识点.png)

