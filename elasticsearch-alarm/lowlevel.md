# Elasticsearch Java Low Level Rest Client

## Document
[https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-low.html](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-low.html)

## Elasticsearch Java Low Level Rest Client
elasticsearch 에서 공식적으로 제공하는 Java 용 rest client API.
디펜던시가 적어 간단하게 사용할 수 있으나 복잡한 쿼리문을 그대로 작성해야하는 등 불편함이 있다.

### Dependency
아래와 같은 라이브러리를 추가하여야 한다.

* httpcore-4.4.12.jar
* httpclient-4.5.10.jar
* httpcore-nio-4.4.12.jar
* httpasyncclient-4.1.4.jar
* elasticsearch-rest-client-7.3.1.jar
* elasticsearch-core-7.3.1.jar
* elasticsearch-x-content-7.3.1.jar
* elasticsearch-7.3.1.jar

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
import org.apache.http.util.EntityUtils;
import org.elasticsearch.client.Request;
import org.elasticsearch.client.Response;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestClientBuilder.HttpClientConfigCallback;
import org.springframework.stereotype.Service;
 
@Service
public class ElasticsearchService {

    private final Log logger = LogFactory.getLog(getClass()); 

    public void testLowLevelElasticsearch() throws Exception {
        CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
        credentialsProvider.setCredentials(AuthScope.ANY, new UsernamePasswordCredentials("ACCOUNT_ID", "ACCESS_TOKEN"));

        RestClient client = RestClient.builder(new HttpHost("127.0.0.1", 80, "http")).setHttpClientConfigCallback(new HttpClientConfigCallback() {

            @Override
            public HttpAsyncClientBuilder customizeHttpClient(HttpAsyncClientBuilder httpAsyncClientBuilder) {
                return httpAsyncClientBuilder.setDefaultCredentialsProvider(credentialsProvider);
            }
        }).build();

        Request request = new Request("GET", "/logstash_2019-09-24/_doc/ID");
        request.addParameter("pretty", "true");

        Response response = client.performRequest(request);

        String responseBody = EntityUtils.toString(response.getEntity());

        logger.debug(responseBody);

        client.close();
    }
}
```