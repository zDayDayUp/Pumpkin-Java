## 1.查找
```xml
 <select id="selectAll" resultType="com.imooc.mybatis.entity.Goods">
        select * from t_goods order by goods_id desc limit 10
    </select>
```

测试

```java
    @Test
   public void testSelectAll(){
        SqlSession sqlSession = null ;
        try{
            //通过MybatisUtils工具类构造全局唯一的SqlSession
            sqlSession = MybatisUtils.openSession() ;
            /**
             * @goods 为mapper的命名空间
             * @selectAll 为该sql语句的id
             */
            List<Goods> list = sqlSession.selectList("goods.selectAll");
            for(Goods g : list){
                System.out.println(g.getGoodsId());
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            MybatisUtils.closeSession(sqlSession);
        }
    }
```

## 2.带参数的sql(单个参数)
```parameterType``` - 指定参数的类型
```resultType``` - 指定查询返回的结果类型

```xml
  <select id="selectById" parameterType="Integer" resultType="com.imooc.mybatis.entity.Goods">
        select  * from t_goods where goods_id = #{value}   <!-- 参数可以随便命名 -->
  </select>
```

测试

```java
//    sql传参(单个参数)
    @Test
    public void testSelectById(){
        SqlSession sqlSession = null ;
        try{
            sqlSession = MybatisUtils.openSession() ;
            Goods good = sqlSession.selectOne("goods.selectById", 2676);
            System.out.println(good.getTitle());
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            MybatisUtils.closeSession(sqlSession);
        }
    }
```

## 3.带参数的sql(多个参数)

```xml
<!--    sql传参(多个参数)，使用parameterType指定map接口，sql中#{key}提取参数    -->
    <select id="selectByPriceRange" parameterType="java.util.Map" resultType="com.imooc.mybatis.entity.Goods">
        select * from t_goods
        where
            current_price between  #{min} and ${max}      <!-- 参数为map的键 -->
            order by current_price
            limit 0 , #{limit}
    </select>
```

测试

```java
//    sql传参(多个参数)，以map形式传入 ,map的键必须跟sql语句中的参数名相同
    @Test
    public void testSelectByPriceRange(){
        SqlSession sqlSession = null ;
        try{
            sqlSession = MybatisUtils.openSession() ;
            Map param = new HashMap();
            param.put("min" , 100) ;
            param.put("max" , 500) ;
            param.put("limit" , 10) ;
            List<Goods> goods = sqlSession.selectList("goods.selectByPriceRange", param);
            for(Goods g : goods){
                System.out.println(g.getCurrentPrice());
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            MybatisUtils.closeSession(sqlSession);
        }
    }
```

## 插入

```xml
<!--插入-->
    <insert id="insert" parameterType="com.imooc.mybatis.entity.Goods">
        INSERT INTO t_goods (title , sub_title , original_cost , current_price , discount , is_free_delivery , category_id)
        VALUES (#{title} , #{subTitle} , #{originalCost} , #{currentPrice} , #{discount} , #{isFreeDelivery} , #{categoryId})
--         主键回舔(完成插入操作后，将自动生成的主键(一般为自增的编号)自动写入给goods对象)
        <selectKey resultType="Integer" keyProperty="goodsId" order="AFTER">
            select last_insert_id()
        </selectKey>
    </insert>
```

测试

```java
 @Test
    public void testInsert(){
        SqlSession sqlSession = null ;
        try{
            sqlSession = MybatisUtils.openSession() ;
            Goods goods = new Goods() ;
            goods.setTitle("测试商品");
            goods.setSubTitle("测试子标题");
            goods.setOriginalCost(200f);
            goods.setCurrentPrice(100f);
            goods.setDiscount(0.5f);
            goods.setIsFreeDelivery(1);
            goods.setCategoryId(43);
            //insert()方法返回值代表本次成功插入的记录总数
            int num = sqlSession.insert("goods.insert" , goods);
            sqlSession.commit();   //提交事务
            System.out.println(goods.getGoodsId());
        }catch (Exception e){
            if(sqlSession != null){
                sqlSession.rollback();  //事务回滚
            }
            e.printStackTrace();
        }finally {
            MybatisUtils.closeSession(sqlSession);
        }
    }
```

关于插入时主键回添的方法一共有两种```selectKey```和```useGeneratedKeys```
```selectKey``` - 适用于任何关系型数据库，且标签需要明确编写获取最新主键的sql语句
```useGeneratedKeys``` - 只适用于支持“主键自增”的数据库,会自动根据驱动生成对应获取最新主键的sql语句

```selectKey```在mysql中的用法

```xml
  <insert id="insert" parameterType="com.imooc.mybatis.entity.Goods">
          INSERT的SQL语句
        <selectKey resultType="Integer" keyProperty="goodsId" order="AFTER">
            select last_insert_id()
        </selectKey>
    </insert>
```

```useGeneratedKeys```在mysql中的用法

```xml

  <insert id="insert" parameterType="com.imooc.mybatis.entity.Goods" useGeneratedKeys="true" keyProperty="goodsId" KeyColumn="goods_id" >
      INSERT 语句
    </insert>
```

## 更新

```xml
<!--    更新-->
    <update id="update" parameterType="com.imooc.mybatis.entity.Goods">
        UPDATE t_goods
        SET
            title = #{title}
        WHERE
            goods_id = #{goodsId}
    </update>
```

测试

```java
  @Test
    public void tetsUpdate(){
        SqlSession sqlSession = null ;
        try{
            sqlSession = MybatisUtils.openSession() ;
            Goods good = sqlSession.selectOne("selectById", 739);
            good.setTitle("更新测试商品");
            int update = sqlSession.update("update", good);
            sqlSession.commit();
            System.out.println(update);
        }catch (Exception e){
            if(sqlSession != null){
                sqlSession.rollback();
            }
            e.printStackTrace();
        }finally {
            MybatisUtils.closeSession(sqlSession);
        }
    }

```

## 删除

```xml
<!--    删除-->
    <delete id="delete" parameterType="Integer">
        delete from t_goods where goods_id = #{value}
    </delete>
```

测试

```java
   @Test
    public void testDelete(){
        SqlSession sqlSession = null ;
        try{
            sqlSession = MybatisUtils.openSession() ;
            int delete = sqlSession.delete("goods.delete", 739);
            sqlSession.commit();
            System.out.println(delete);
        }catch (Exception e){
            if(sqlSession != null){
                sqlSession.rollback();
            }
            e.printStackTrace();
        }finally {
            MybatisUtils.closeSession(sqlSession);
        }
    }
```

## 动态SQL - 参数的增减不需要改动SQL语句

```xml
<!--    动态sql-->
    <select id="dynamicSQl" parameterType="java.util.Map" resultType="com.imooc.mybatis.entity.Goods" >
        select * from t_goods
        <where>
            <if test="categoryId != null">
                and category_id = #{categoryId}
            </if>
            <if test="currentPrice != null">
                and current_price &lt; #{currentPrice}
            </if>
        </where>
    </select>

```

测试

```java
//    动态SQL
    @Test
    public void testDynamicSQL(){
        SqlSession sqlSession = null ;
        try{
            sqlSession = MybatisUtils.openSession() ;
            Map param = new HashMap() ;
            param.put("categoryId" , 44) ;
            param.put("currentPrice" , 500) ;
            List<Goods> list = sqlSession.selectList("goods.dynamicSQl", param);
            for (Goods g : list){
                System.out.println(g.getTitle() + ":" + g.getCategoryId() + ":" + g.getCurrentPrice());
            }

        }catch (Exception e){
            e.printStackTrace();
        }finally {
            MybatisUtils.closeSession(sqlSession);
        }
    }

```

## SQL的防注入攻击
SQL注入是指攻击者利用SQL漏洞，绕过系统约束，越权获取数据的攻击方式
```sql
SQL代码：
"select*from a where name="+name+""；
正常情况：name：张三->select * from a where name=张三；
SQL注入攻击：name:'or 1=1or1='
            select * from a where name="or 1=1or1="
```
  1=1时必然为true的，所以通过输入该参数，可以获取该表的全部信息，这是相当危险的。
### mybatis的两种传值方式
```${}``` ： 文本替换，不经任何处理对SQL文本替换</br>
```#{}``` ：预编译传值，使用预编译传值可以预防SQL注入攻击

## Mybatis工作流程
![Mybatis工作流程](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/MybatisLiucheng.png?raw=true)
