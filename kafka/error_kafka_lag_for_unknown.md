# Error: Exception in thread "..." java.lang.IllegalStateException: Tried to lookup lag for unknown task 2_0

## Error: Exception in thread "..." java.lang.IllegalStateException: Tried to lookup lag for unknown task 2_0
Kafka Stream 실행 시 위의 에러가 발생하는 경우가 있다. 이는 해당 groupId 혹은 appliationId 의 offset 정보의 불일치로 인하여 발생된한 것으로 아래와 같이 해결할 수 있다.
* Kafka 의 server.properties 의 offsets.retention.minutes 설정을 변경한다.
    + 기본 값: 1440(24시간)
* Kafka Client 의 groupId 혹은 applicationId 를 변경한다.
