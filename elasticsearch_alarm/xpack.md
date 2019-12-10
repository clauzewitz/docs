# X-pack

## X-pack
보안, 알림, 모니터링, 보고, 그래프 기능을 패키지로 구성한 elasticsearch 플러그인이다.  
elasticsearch 7.x.x 버전에는 기본적으로 포함되어 있어 별도의 설치가 필요없다.  
[참고문서](https://www.elastic.co/guide/en/elastic-stack-overview/current/introduction.html)

만약 5.x.x 버전을 사용 중이라면 아래 문서를 참고하여 버전에 맞는 x-pack 을 설치하여야 한다.  
[참고문서](https://www.elastic.co/guide/kr/x-pack/current/installing-xpack.html)

### 설정환경
해당 기능은 gold 라이센스 부터 지원한다.  
x-pack 을 이용하여 알람 기능을 사용하려면 x-pack 의 watcher 를 활성화시켜야 한다.

elasticsearch 의 설정 파일인 elasticsearch.yml 의 항목 중 xpack.watcher.enabled 를 true 로 변경 및 slack 웹훅 URL 을 설정한다.
```
$ vi elasticsearch.yml

xpack.watcher.enabled: true
xpack.notification.slack.secure_url: https://SLACK_WEB_HOOK_URL
```

### Sample
라이센스로 인한 테스트 불가로 다른 사용자의 문서로 대체한다.  
[참고문서](https://medium.com/@amimahloof/monitoring-elasticsearch-using-x-pack-with-slack-and-pagerduty-8117d4bf37bf)