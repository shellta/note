# MyBatis

## 什么是MyBatis?
> [MyBatis](https://mybatis.org/mybatis-3/zh/index.html) 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生类型、接口和 Java 的 POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。


## 集成到Spring Boot中 
1. 添加依赖到 pom.xml
``` xml
<dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.0</version>
</dependency>
```

2. 配置Spring Boot `Application.yml`
``` yml
spring: 
    datasource:
      url: jdbc:mysql://127.0.0.1:3306/t?useUnicode=true&characterEncoding=UTF-8
      username: root
      password: password
      driver-class-name: com.mysql.cj.jdbc.Driver
mybatis: 
    mapper-locations: classpath:mapper/*.xml
```

3. 数据库创建一个表
``` sql
CREATE TABLE `user` (
    `id` INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
    `username` VARCHAR(50) NOT NULL COLLATE 'utf8mb4_unicode_ci',
    `password` VARCHAR(255) NOT NULL COLLATE 'utf8mb4_unicode_ci',
    PRIMARY KEY (`id`)
)
COLLATE='utf8mb4_unicode_ci'
ENGINE=InnoDB;
```
   

4. 为user表创建一个实体类 
``` java
    package org.example.demo.entity;

    import lombok.Data;

    @Data
    public class User {
        private Long id;

        private String username;

        private String password;
    }
```

5. 定义一个接口操作user表
``` java
    package org.example.demo.mapper;

    import org.apache.ibatis.annotations.Mapper;
    import org.example.demo.entity.User;

    @Mapper
    public interface UserMapper {
        int save(User user);
    }
```

6. 定义了接口后，需要创建一个具体操作数据库的mybatis xml文件 (resources/mapper/UserMapper.xml)
``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="org.example.demo.mapper.UserMapper">
      <resultMap id="BaseResultMap" type="org.example.demo.entity.User">
        <id column="id" jdbcType="BIGINT" property="id" />
        <result column="username" jdbcType="VARCHAR" property="username" />
        <result column="password" jdbcType="VARCHAR" property="password" />
      </resultMap>
      <insert id="save" parameterType="org.example.demo.entity.User">
        insert into user (username, password)
        values (#{username}, #{password})
      </insert>
    </mapper>
```

7. 创建一个测试类来测试UserMapper 中的save方法
``` java
    package org.example.demo.mapper;

    import org.example.demo.DemoApplication;
    import org.example.demo.entity.User;
    import org.junit.jupiter.api.Test;
    import org.junit.runner.RunWith;
    import org.springframework.boot.test.context.SpringBootTest;
    import org.springframework.test.context.junit4.SpringRunner;

    import javax.annotation.Resource;

    import static org.junit.jupiter.api.Assertions.*;

    @RunWith(SpringRunner.class)
    @SpringBootTest(classes = DemoApplication.class)
    class UserMapperTest {

        @Resource
        UserMapper userMapper;

        @Test
        void save() {
            User user = new User();
            user.setUsername("name");
            user.setPassword("password");
            int result = userMapper.save(user);

            assertEquals(1, result);
        }
    }
```

8. 执行UserMapperTest的save方法，测试通过。说明MyBatis已经和Spring Boot集成完毕。


### 说明
* MyBatis 一般分为3层。Entity层、Mapper层、以及定义的具体实现Mapper的XML文件。 
* Mapper层的方法会调用相应XML的mapper中的对应id的语句



### 更高效的XML
由于执行的操作实际定义在XML文件中，但是XML文件靠我们手写实际上不是很现实。别担心，MyBatis Generator插件会帮助我们。


## MyBatis Generator
1. 添加到pom.xml中
``` xml
    <plugin>
        <groupId>org.mybatis.generator</groupId>
        <artifactId>mybatis-generator-maven-plugin</artifactId>
        <version>1.3.7</version>
        <configuration>
            <configurationFile>src/main/resources/generatorConfig.xml</configurationFile>
            <verbose>true</verbose>
            <overwrite>true</overwrite>
        </configuration>

        <dependencies>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>8.0.13</version>
            </dependency>
        </dependencies>
    </plugin>
```

2. 添加resources/generatorCOnfig.xml文件
``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE generatorConfiguration
            PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
            "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

    <generatorConfiguration>
        <context id="mysql" targetRuntime="MyBatis3Simple">

            <!-- 生成的Java文件的编码 -->
            <property name="javaFileEncoding" value="UTF-8"/>
            <!-- 格式化java代码 -->
            <property name="javaFormatter" value="org.mybatis.generator.api.dom.DefaultJavaFormatter"/>
            <!-- 格式化XML代码 -->
            <property name="xmlFormatter" value="org.mybatis.generator.api.dom.DefaultXmlFormatter"/>
            <!-- 指明数据库的用于标记数据库对象名的符号，比如ORACLE就是双引号，MYSQL默认是`反引号       -->
            <property name="beginningDelimiter" value="`"/>
            <property name="endingDelimiter" value="`"/>

            <!--  为实体类实现Serializable接口    -->
            <plugin type="org.mybatis.generator.plugins.SerializablePlugin" />

            <commentGenerator>
                <!--  关闭自动生成的注释  -->
                <property name="suppressAllComments" value="true"/>
                <property name="suppressDate" value="false" />
            </commentGenerator>

            <!-- 配置数据库连接 -->
            <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                            connectionURL="jdbc:mysql://127.0.0.1:3306/demo"
                            userId="root"
                            password="wtf123456">

                <property name="serverTimezone" value="UTC"/>
                <property name="nullCatalogMeansCurrent" value="true"/>
            </jdbcConnection>

            <javaTypeResolver>
                <property name="forceBigDecimals" value="false" />
            </javaTypeResolver>

            <!--  指定生成的实体类位置       -->
            <javaModelGenerator targetPackage="org.example.demo.entity" targetProject="src/main/java">
                <property name="enableSubPackages" value="true" />
                <property name="trimStrings" value="true" />
            </javaModelGenerator>

            <!--  指定生成的Mapper位置       -->
            <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources">
                <property name="enableSubPackages" value="true" />
            </sqlMapGenerator>

            <!--  指定生成的Mapper XML位置       -->
            <javaClientGenerator type="XMLMAPPER" targetPackage="org.example.demo.mapper" targetProject="src/main/java">
                <property name="enableSubPackages" value="true" />
            </javaClientGenerator>
            
            <!-- 表设置        -->
            <table tableName="user">
                <property name="useActualColumnNames" value="false"/>
            </table>
        </context>
    </generatorConfiguration>
```

3. 执行 `mvn mybatis-generator:generate`  
插件将会自动为我们添加对应的Entity, Mapper, 以及 XML文件。包括常见的增删查改操作。
