<h1>MyBatis 笔记</h1>

<b>Maven</b>
<p>

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.16</version>
</dependency>
```
</p>
<p>
MyBatis 是一个将JDBC过程简化的框架简化原始的DAO->DAOImpl等一系列繁琐过程其工作逻辑为DAO -> MapperInterface 接口实现类为MyBatis进行动态代理实现调用对应的MapperInterface.xml文件进行sql语句拼装与执行
</p>


