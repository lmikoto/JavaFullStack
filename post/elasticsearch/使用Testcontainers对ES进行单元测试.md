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

进行单元测试

```java
package io.github.lmikoto;

import com.github.dockerjava.api.model.ExposedPort;
import com.github.dockerjava.api.model.PortBinding;
import com.github.dockerjava.api.model.Ports;
import org.apache.http.HttpHost;
import org.elasticsearch.ElasticsearchStatusException;
import org.elasticsearch.action.admin.indices.delete.DeleteIndexRequest;
import org.elasticsearch.action.support.master.AcknowledgedResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.client.indices.CreateIndexRequest;
import org.elasticsearch.client.indices.CreateIndexResponse;
import org.elasticsearch.client.indices.GetIndexRequest;
import org.elasticsearch.client.indices.GetIndexResponse;
import org.elasticsearch.cluster.metadata.MappingMetaData;
import org.elasticsearch.common.xcontent.XContentBuilder;
import org.elasticsearch.common.xcontent.XContentFactory;
import org.junit.Assert;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.testcontainers.elasticsearch.ElasticsearchContainer;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

public class ESTest {

    private ElasticsearchContainer container;

    RestHighLevelClient client = new RestHighLevelClient(
            RestClient.builder(
                    new HttpHost("localhost", 9200, "http")));

    @BeforeEach
    public void startES(){
        container = new ElasticsearchContainer("docker.elastic.co/elasticsearch/elasticsearch:7.3.0")
                .withCreateContainerCmdModifier(cmd-> cmd.getHostConfig()
                        .withPortBindings(new PortBinding(Ports.Binding.bindPort(9200), new ExposedPort(9200))));
        container.start();
    }

    @Test
    public void testCreateIndex() throws IOException {
        String indexName = "test";
        CreateIndexRequest createIndex = new CreateIndexRequest(indexName);
        XContentBuilder builder = XContentFactory.jsonBuilder();
        builder.startObject();
        {
            builder.startObject("properties");
            {
                builder.startObject("message");
                {
                    builder.field("type", "text");
                }
                builder.endObject();
            }
            builder.endObject();
        }
        builder.endObject();
        createIndex.mapping(builder);
        CreateIndexResponse createIndexResponse = client.indices().create(createIndex, RequestOptions.DEFAULT);
        Assertions.assertTrue(createIndexResponse.isAcknowledged());

        GetIndexRequest getIndex = new GetIndexRequest(indexName);
        client.indices().get(getIndex, RequestOptions.DEFAULT);

        DeleteIndexRequest deleteRequest = new DeleteIndexRequest(indexName);
        AcknowledgedResponse deleteResponse = client.indices().delete(deleteRequest, RequestOptions.DEFAULT);

        Assertions.assertTrue(deleteResponse.isAcknowledged());

        Assertions.assertThrows(ElasticsearchStatusException.class,()->{
            client.indices().get(getIndex, RequestOptions.DEFAULT);
        });

    }
}

```



## 参考

- https://www.testcontainers.org/modules/elasticsearch/