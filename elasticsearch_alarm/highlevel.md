# Elasticsearch Java High Level Rest Client

## Document
[https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high.html](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high.html)

## Elasticsearch Java High Level Rest Client
elasticsearch 에서 제공하는 Java 기반의 rest client.
Log Level Rest Client 를 기반으로 인덱스의 생성이나 조회 등 kibana 에서 제공하는 기능처럼 쉽게 사용할 수 있도록 래핑된 클라이언트이다.
단, 위의 기능들을 제공하기 위해 많은 라이브러리의 추가가 필요하다.

### Dependency
아래와 같은 라이브러리를 추가하여야 한다.

* elasticsearch-core-7.3.1.jar
* elasticsearch-7.3.1.jar
* elasticsearch-x-content-7.3.1.jar
* elasticsearch-rest-high-level-client-7.3.1.jar
* elasticsearch-rest-client-7.3.1.jar
* lang-mustache-client-7.3.1.jar
* rank-eval-client-7.3.1.jar
* aggs-matrix-stats-client-7.3.1.jar
* parent-join-client-7.3.1.jar
* httpasyncclient-4.1.4.jar
* httpcore-nio-4.4.12.jar
* httpclient-4.5.10.jar
* httpcore-4.4.12.jar
* jackson-databind-2.8.11.4.jar
* jackson-annotations-2.8.11.jar
* jackson-core-2.8.11.jar
* lucene-core-8.1.0.jar
* lucene-analyzers-common-8.1.0.jar
* lucene-backward-codecs-8.1.0.jar
* lucene-grouping-8.1.0.jar
* lucene-highlighter-8.1.0.jar
* lucene-join-8.1.0.jar
* lucene-memory-8.1.0.jar
* lucene-misc-8.1.0.jar
* lucene-queries-8.1.0.jar
* lucene-queryparser-8.1.0.jar
* lucene-sandbox-8.1.0.jar
* lucene-spatial-8.1.0.jar
* lucene-spatial-extras-8.1.0.jar
* lucene-spatial3d-8.1.0.jar
* lucene-suggest-8.1.0.jar
* hppc-0.8.1.jar

### Sample
```
package com.test.messenger.api.service;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.apache.http.HttpHost;
import org.apache.http.auth.AuthScope;
import org.apache.http.auth.UsernamePasswordCredentials;
import org.apache.http.client.CredentialsProvider;
import org.apache.http.impl.client.BasicCredentialsProvider;
import org.apache.http.impl.nio.client.HttpAsyncClientBuilder;
import org.elasticsearch.action.get.GetRequest;
import org.elasticsearch.action.get.GetResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestClientBuilder.HttpClientConfigCallback;
import org.elasticsearch.client.RestHighLevelClient;
import org.springframework.stereotype.Service;

@Service
public class ElasticsearchService {

    private final Log logger = LogFactory.getLog(getClass());
    public void testElasticsearch() throws Exception {
        CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
        credentialsProvider.setCredentials(AuthScope.ANY, new UsernamePasswordCredentials("ACCOUNT_ID", "ACCESS_TOKEN"));

        RestHighLevelClient client = new RestHighLevelClient(RestClient.builder(new HttpHost("127.0.0.1", 80, "http")).setHttpClientConfigCallback(new HttpClientConfigCallback() {

            @Override
            public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpAsyncClientBuilder) {
                return httpAsyncClientBuilder.setDefaultCredentialsProvider(credentialsProvider);
            }
        }));

        GetRequest request = new GetRequest("logstash_2019-09-24", "ID");

        GetResponse response = client.get(request, RequestOptions.DEFAULT);

        logger.debug(response.isExists() ? "Y" : "N");

        client.close();
    }

}
```