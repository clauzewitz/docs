# Elktail

## Document
[https://github.com/knes1/elktail](https://github.com/knes1/elktail)

## Elktail
cli 기반으로 elasticsearch 를 조회 및 tail 기능을 제공한다.

### 설치환경
elktail 은 go 언어가 설치되어 있어야한다. go 언어 설치는 아래 문서를 참고한다.  
[참고문서](https://golang.org/doc/install#install)

설치는 아래 명령어를 통해 설치한다.
```
$ go get github.com/knes1/elktail
```

실행은 아래 명령어를 통해 실행한다.
```
$ elktail --url "http://xxxx.example.com:9200" -f '%@timestamp %log'
```

실행에 필요한 자세한 명령어는 아래 문서를 참고한다.  
[참고문서](https://github.com/knes1/elktail#other-options)

### 활용 방안
elktail 만으로 alert 시스템을 구축할 수는 없다. elktail 은 단순히 elasticsearch 에 질의를 날려서 조회하는 기능만을 제공하는 cli 이기 때문이다.
하지만,이 기능을 이용하여 slack 이나 telegram 으로 알람을 보낼 수는 있다.

아래는 스크립트를 사용하여 elasticsearch 를 조회하고, 특정 값이 존재하면 slack 의 웹훅 알람 전송 API 를 호출하는 예시이다.
```
#!/bin/bash

export TZ="UTC"

After=`date +%Y-%m-%dT%H:%M -d '2 minute ago'`
Before=`date +%Y-%m-%dT%H:%M`

DIR="/root/.../elktail-log-check"

SLACK_URL="SLACK_WEB_HOOK_URL"

elktail --url "http://xxxx.example.com:9200" -a ${After} -b ${Before} -l -f '@timestamp %beat.hostname %event_id %message' -i "winlogbeat-[0-9].*" event_id:6005 OR event_id:6006 OR event_id:1074 OR event_id:6008 OR event_id:5120 >> $DIR/log/result.txt
     
    COUNT=`wc -l $DIR/log/result.txt | awk -F " " '{print $1}'`
    if ["$COUNT" -gt "2"]
    then
        Message=`cat $DIR/log/result.txt`
        curl -X POST --data "payload={\"text\":\"warning: ${Message}\"}" ${SLACK_URL}
    else
        echo ""
    fi

exit 0
```