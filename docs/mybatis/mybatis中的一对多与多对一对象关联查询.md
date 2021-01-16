## Mybatis中的一对多对象关联查询查询
模拟情景，商品与商品详情：一件商品可以对应多个商品详情信息，即从商品➡商品详情方向看，属于一对多。
在一对多关系中，需要在属于一的一方的实体类中添加多的一方的集合，一般为List<>类型</br>
```java
  //(省去了get和set的方法)
 public class Goods {
    private Integer goodsId ;
    private String title ;
    private String subTitle ;
    private Float originalCost ;
    private Float currentPrice ;
    private Float discount ;
    private Integer isFreeDelivery ;
    private Integer categoryId ;
    //在一对多关系中，在一方添加多的一方的集合
    private List<GoodsDetail> goodsDetailLists ;  
}
```
在"一方"实体类对应的```xml ```文件中，添加配置信息
```xml

<!--    OneToMany对象关联查询
        resultMap可用于说明一对多或者多对一的映射逻辑
        id 是resultMap属性引用的标志
        type 指向One的实体(Goods)
-->
    <resultMap id="rmGoods1" type="com.imooc.mybatis.entity.Goods">
<!--        映射goods对象的主键到goods_id字段-->
        <id column="goods_id" property="goodsId"></id>
<!--
        collection的含义是，在
        sql语句得到结果后，对所有Goods对象遍历得到goods_id字段值，
        并代入到goodsDetail命名空间的selectByGoodsId的sql中执行查询
        将得到的“商品详情”集合赋值给goodsDetailsList对象
-->
        <collection property="goodsDetailLists" select="goodsDetail.selectByGoodsId"
                    column="goods_id"></collection>
    </resultMap>
    <select id="selectOneToMany" resultMap="rmGoods1">
        select * from  t_goods limit 0 , 1
    </select>
```

在“多方”对应的```xml```文件中添加
```xml
<mapper namespace="goodsDetail">
    <select id="selectByGoodsId" parameterType="Integer"
            resultType="com.imooc.mybatis.entity.GoodsDetail">
        select * from t_goods_detail where goods_id = #{value}
    </select>
</mapper>
```

### 至此，关于商品到商品详情的一对多查询配置就完成了。

## 测试
```
 //OneToMany
    @Test
    public void selectOneToMany(){
        SqlSession sqlSession = null ;
        try{
            sqlSession = MybatisUtils.openSession() ;
            List<Goods> list = sqlSession.selectList("goods.selectOneToMany");
            for (Goods g : list){
            //输出商品和该商品的详情信息数量
                System.out.println(g.getTitle() + ":" + g.getGoodsDetailLists().size());
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            MybatisUtils.closeSession(sqlSession);
        }
    }
```

## Mybatis多对一对象关联查询
在上诉情景中，商品详情➡商品即为多对一的关系</br>
在多对一关系中，需要在多的一方的实体类中添加一的一方的实体对象

```java
public class GoodsDetail {
    private Integer gdId ;
    private Integer goodsId ;
    private String gdPicUrl ;
    private Integer gdOrder ;
    //多对一：在多的一方添加一的一方的实体
    private Goods goods ;
}
```
在多的一方```xml```文件中添加
```xml
<!--    多对一关系-->
    <resultMap id="rmGoodsDetail" type="com.imooc.mybatis.entity.GoodsDetail">
        <id column="gd_id" property="gdId"></id>
        <result column="goods_id" property="goodsId"></result>
        <!-- goods.selectById 为goods.xml根据主键id查找goods信息。-->
        <association property="goods" select="goods.selectById" column="goods_id"></association>
    </resultMap>
    <select id="selectManyToOne" resultMap="rmGoodsDetail">
        select * from t_goods_detail limit 0 , 1
    </select>

```

### 测试

```java
/**
     * 多对一对象关联映射
     * */
    @Test
    public void selectManyToOne(){
        SqlSession sqlSession = null ;
        try{
            sqlSession = MybatisUtils.openSession() ;
            List<GoodsDetail> list = sqlSession.selectList("goodsDetail.selectManyToOne");
            for (GoodsDetail gd : list){
                System.out.println(gd.getGdPicUrl() + ":" + gd.getGoods().getTitle());
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            MybatisUtils.closeSession(sqlSession);
        }
    }

```

