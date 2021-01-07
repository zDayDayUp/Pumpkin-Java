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
### 4.通过SqlSessionFactory构建SqlSession会话实例
  每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例。

从 XML 文件中构建 SqlSessionFactory 的实例非常简单，建议使用类路径下的资源文件进行配置。 但也可以使用任意的输入流（InputStream）实例，比如用文件路径字符串或 file:// URL 构造的输入流。MyBatis 包含一个名叫 Resources 的工具类，它包含一些实用方法，使得从类路径或其它位置加载资源文件更加容易。
通常SqlSession是全局唯一的，通过MybatisUtils工具类减少重复代码
```java
public class MybatisUtils {
    //利用static静态 属于类而不属于对象 ， 保证全局唯一
    private static SqlSessionFactory sqlSessionFactory = null ;
    //利用静态块在初始化类时实力话SqlSessionFactory
    static {
        Reader reader = null;
        try {
            reader = Resources.getResourceAsReader("mybatis-config.xml");
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader) ;
        } catch (IOException e) {
            e.printStackTrace();
            //初始化过程中出现的异常
            throw  new ExceptionInInitializerError(e) ;
        }
    }

    /**
     * 创建一个新的sqlsession对象
     * @return  sqlsession对象
     * */

    public static SqlSession openSession(){
        return sqlSessionFactory.openSession() ;
    }

    public static void closeSession(SqlSession sqlSession){
        if(sqlSession != null ){
            sqlSession.close();
        }
    }

}
```
### 5.通过SqlSession对数据库进行操作
 在```entity```包下创建要操作的数据库表对应的实体类(必须使用驼峰命名和设置get和set方法),同时在```resources```目录下创建```mappers```目录,在```mappers```目录下创建与该实体类同名的```xml```文件,在对其进行```xml```声明和```mapper```dtd文件声明(注意与```mybatis-config.xml```的dtd文件声明的区别)
 ```
 <?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
 ```
 
 然后在```mybatis-config.xml```文件引入该实体与数据库的映射。即在其```<mappers></mappers>```标签中添加映射```xml```文件路径
 ```
  <mappers>
        <mapper resource="mappers/goods.xml" />
        <mapper resource="mappers/goods_detail.xml" />
  </mappers>
 ```
 当数据库列字段名为多单词且用"_"拼接时，还需在其```<configuration></configuration>```标签中开启驼峰命名转换，使Mybatis自动完成映射。
 ```
  <settings>
<!--        开启驼峰命名转换，若底层数据库表项为goods_ID,实体类为goodsId ，则自动转换-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
   </settings>
 ```
 
 #### 到此为止，mybatis需要的配置就已全部完成。
 
 
 
 
