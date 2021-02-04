# [Redis官网下载](https://redis.io/download)
在linux下安装只需执行命令
```linux
$ wget https://download.redis.io/releases/redis-6.0.10.tar.gz
$ tar xzf redis-6.0.10.tar.gz
$ cd redis-6.0.10
$ make
```
若执行make命令报错，则是系统没有安装gcc
执行
```
yum install gcc 
```
在执行make命令即可完成redis的安装


##Redis的常用配置说明
![](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/Redis/redis1.png?raw=true)</br>
图上所有属性都在```Redis安装目录/redis.conf```文件中。(redis另外两个重要文件分别是```redis安装目录/src下的redis-server和redis-cli```)

## 设置当前目录为redis安装目录
### 将redis服务设置为后台运行
``` vim redis.conf
```
将第136行daemonize修改为yes (行号 然后输入gg 定位到行)

### 修改redis默认端口号
第92行 port后为redis端口号

### 修改日志文件
第171行

### 修改redis数据库总量
第186行

### 设置使用密码
507行 , 进入redis 需先使用```auth 密码 ``` 

## Redis常用命令
![](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/Redis/redis8.png?raw=true)
![](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/Redis/redis9.png?raw=true)
![](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/Redis/redis10.png?raw=true)
![](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/Redis/redis11.png?raw=true)
![](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/Redis/redis12.png?raw=true)
![](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/Redis/redis13.png?raw=true)
![](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/Redis/redis14.png?raw=true)

## 利用Jedis使用Redis
### 将拥有Redis服务器的保护模式关闭(便于连接redis)
![](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/Redis/redis15.png?raw=true)
将```redis.conf```文件第88行 protected 改为 no ，允许其他主机连接

执行命令
```
firewall-cmd --zone=public --add-port=6379/tcp --permanent
```
公共区域永久开放tcp协议的6379端口

![](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/Redis/redis16.png?raw=true)
重新加载

## 关闭Redis服务
```linux
 ./src/redis-cli shutdown
```

### 在项目中引入jedis依赖
```xml
 <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.9.0</version>
 </dependency>
```

测试类

```java
 public static void main(String[] args) {
        //host为开启redis服务的主机ip地址，port为redis的端口号
        Jedis jedis = new Jedis("192.168.157.131" , 6379) ;
        try {
            jedis.auth("12345");
            jedis.select(2);
            System.out.println("Redis连接成功");
            //字符串
            jedis.set("sn" , "7781-9938");
            String sn = jedis.get("sn");
            System.out.println(sn);
            jedis.mset(new String[]{"title" , "婴幼儿奶粉" , "num" , "20"});
            List<String> goods =  jedis.mget(new String[]{"sn" , "title" , "num"});
            System.out.println(goods);
            Long num = jedis.incr("num");
            System.out.println(num);
            //Hash
            jedis.hset("student:3312" , "name" , "张晓明");
            String name = jedis.hget("student:3312" , "name");
            System.out.println(name);
            Map<String,String> studentMap = new HashMap();
            studentMap.put("name", "李兰");
            studentMap.put("age", "18");
            studentMap.put("id", "3313");
            jedis.hmset("student:3313", studentMap);
            Map<String,String> smap =  jedis.hgetAll("student:3313");
            System.out.println(smap);
            //List
            jedis.del("letter");
            jedis.rpush("letter" , new String[]{"d" , "e" , "f"});
            jedis.lpush("letter" ,  new String[]{"c" , "b" , "a"});
            List<String> letter =  jedis.lrange("letter" , 0 , -1);
            jedis.lpop("letter");
            jedis.rpop("letter");
            letter = jedis.lrange("letter", 0, -1);
            System.out.println(letter);
        }catch(Exception e){
            e.printStackTrace();
        }finally {
            jedis.close();
        }
    }
```
