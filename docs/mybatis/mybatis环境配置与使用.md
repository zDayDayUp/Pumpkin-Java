# [mybatis 3 | 参考文档](https://mybatis.org/mybatis-3/zh/index.html)

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

## 一、mybatis环境配置
#### 1.通过maven的pom.xml文件引入mybatis需要的包</br>
 在其```<dependencies></dependencies>```标签中添加如下代码

```
 <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.1</version>
 </dependency>
```
### 2.在```src/main/resources```下新建```mybatis-config.xml```文件</br>
并进行```xml文件```和```config的dtd文件```的声明
    
```
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

```

### 3.在```mybatis-config.xml```文件中的```<configuration></configuration>```中对数据库进行配置
```
<configuration>
    <settings>
<!--        开启驼峰命名转换，若底层数据库表项为goods_ID,实体类为goodsId ，则自动转换-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    <!-- 设置默认指向的数据库 -->
    <environments default="dev">
        <environment id="dev">
            <!--  采用JDBC方式对数据库事务进行commit/rollback -->
            <transactionManager type="JDBC"></transactionManager>
            <!--  采用连接池的方式管理数据库连接   -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/babytun?useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```
