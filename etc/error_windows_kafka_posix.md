# Error: Exception in thread "main" java.lang.UnsupportedOperationException

## Error: Exception in thread "main" java.lang.UnsupportedOperationException
Linux 계열의 OS 가 아닌 Windows OS 에서 Kafka Stream 설정 후 실행시 위의 에러가 발생한다면 아래와 같이 해결할 수 있다.
* Kafka 버전이 2.6.1 이거나 2.7.0 이라면 각각 2.6.2, 2.7.1 버전으로 교체하거나 2.8.0 이상의 버전으로 교체한다.
[https://issues.apache.org/jira/browse/KAFKA-12190](https://issues.apache.org/jira/browse/KAFKA-12190)
