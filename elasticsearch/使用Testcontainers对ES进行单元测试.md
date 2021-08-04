# 使用Testcontainers对ES进行单元测试

添加maven依赖

```xml
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>elasticsearch</artifactId>
    <version>1.15.3</version>
    <scope>test</scope>
</dependency>
```

启动es

```java


```

就这么简单就可以启动一个es服务了。该服务会绑定的一个随机的本地端口。通常我们配置文件中es的端口是固定的9200。当然Testcontainer也支持指定端口号。

```java

```





进行单元测试



## 参考

- https://www.testcontainers.org/modules/elasticsearch/