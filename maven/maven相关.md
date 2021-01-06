# Maven的安装与下载
## [maven官网](http://maven.apache.org/)</br>

![s](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/downloadMaven1.png?raw=true)
下载maven压缩包，maven的源文件结构非常简单，将解压得到的bin目录中的mvn.cmd配置到环境变量中即可

## 检查是否安装成功
打开cmd面板，输入mvn -v 命令
![cmd面板](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/maven2.png?raw=true)

# Maven在idea中的使用
在idea中选中setting，选择maven选项进行配置。
![maven在idea中的使用](https://github.com/zDayDayUp/Pumpkin-Java/blob/main/images/maven3.png?raw=true)

# 中央仓库的设置
maven默认会从国外的服务器下载需要的jar包，而对于国内来说，从国外的服务器下载比较耗费时间且对网络的要求较高。最好的办法是将中央仓库的地址改为阿里云提供的国内的仓库，阿里云仓库是免费的、国内最好的仓库。在maven配置中添加如下代码即可。只有当需要的包无法从阿里云仓库下载时，maven才会从国外服务器惊醒下载。</br>
[阿里云中央仓库](https://maven.aliyun.com/mvn/guide)
```
 <repositories>
        <repository>
            <id>aliyun</id>
            <name>aliyun</name>
            <url>https://maven.aliyun.com/repository/public</url>
        </repository>
    </repositories>
```

# 怎么知道需要的包的groupId及artifactId呢？
[maven依赖查询](https://search.maven.org/)
直接进行搜索即可。
