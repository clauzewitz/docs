# ERROR: org.springframework.web.reactive.function.client.WebClientRequestException: readAddress(..) failed: Connection reset by peer

## ERROR: org.springframework.web.reactive.function.client.WebClientRequestException: readAddress(..) failed: Connection reset by peer
Spring Webflux 를 이용한 Webclient 사용 시 Connection reset by peer 발생
* Webclient 생성 시 connection pool 사용 대신 매번 새로운 connection 을 생성하도록 한다.
    + Webflux 1.1.0 미만인 경우
        - WebClient.builder()
             .clientConnector(new ReactorClientHttpConnector(HttpClient.from(TcpClient.newConnection())))
             .build();
    + Webflux 1.1.0 이상인 경우
        - WebClient.builder()
             .clientConnector(new ReactorClientHttpConnector(HttpClient.newConnection())))
             .build();
