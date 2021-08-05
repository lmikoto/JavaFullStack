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
package io.github.lmikoto;

import org.junit.Before;
import org.junit.Test;
import org.testcontainers.elasticsearch.ElasticsearchContainer;

public class ESTest {

    private ElasticsearchContainer container;

    @Before
    public void startES(){
        container = new ElasticsearchContainer("docker.elastic.co/elasticsearch/elasticsearch:7.3.0");
        container.start();
    }
}

```

就这么简单就可以启动一个es服务了。该服务会绑定的一个随机的本地端口。通常我们配置文件中es的端口是固定的9200。当然Testcontainer也支持指定端口号。

```java

```





进行单元测试



## 参考

- https://www.testcontainers.org/modules/elasticsearch/