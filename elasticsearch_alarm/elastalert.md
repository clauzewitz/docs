# Elastalert

## Document
[https://elastalert.readthedocs.io/en/latest/elastalert.html](https://elastalert.readthedocs.io/en/latest/elastalert.html)

## Elastalert
elasticsearch 에 쌓이는 로그의 패턴에 따라 알람을 제공해주는 프레임워크.
기본적으로 제공해주는 패턴은 아래와 같다.

* “Match where there are X events in Y time” (frequency type)
    + 일정 시간 동안 이벤트가 X 건 이상 발생되었을 때 알람을 보낼 수 있다.
* “Match when the rate of events increases or decreases” (spike type)
    + 일정 시간 동안 이벤트가 급증/급락 한 경우 알람을 보낼 수 있다.
* “Match when there are less than X events in Y time” (flatline type)
    + 일정 시간 동안 이벤트가 X 건 미만일 때 알람을 보낼 수 있다
* “Match when a certain field matches a blacklist/whitelist” (blacklist and whitelist type)
    + 블랙리스트 혹은 화이트리스트에 매핑되는 경우 알람을 보낼 수 있다.
* “Match on any event matching a given filter” (any type)
    + 사용자 정의 필터에 모두 맞는 이벤트가 발생되면 알람을 보낼 수 있다.
* “Match when a field has two different values within some time” (change type)
    + 일정 시간 동안 값이 변경된 이벤트가 존재하는 경우 알람을 보낼 수 있다.
* "Match when a never before seen term appears in a field" (new_term type)
    + 일정 기간 동안 발생된 이벤트 에 속하지 않는 이벤트가 발생한 경우 알람을 보낼 수 있다.
* "Match when the number of unique values for a field is above or below a threshold" (cardinality type)
    + 일정 기간 동안 이벤트의 값의 갯수가 설정값보다 높거나 낮은 경우 알람을 보낼 수 있다.

지원하는 알람으로는 아래와 같다.

* Email
* JIRA
* MS Teams
* Slack
* Telegram
* AWS SNS
* Zabbix

## 설치환경
elastalert 은 기본적으로 python 3.6 을 지원하며 python 2 에서도 현재 사용이 가능하지만 근시일내로 지원을 종료한다고 한다.

설치는 아래 명령어를 통해 설치한다.
```
$ pip install elastalert
```

실행은 아래 명령어를 통해 실행한다.
```
$ elastalert --config config.yaml
```

실행에 필요한 자세한 명령어는 아래 문서를 참고한다.  
[참고문서](https://elastalert.readthedocs.io/en/latest/elastalert.html#running-elastalert)

### Sample Rule
각 패턴에 맞는 규칙 파일은 아래의 주소를 참조한다.  
[참고문서](https://github.com/Yelp/elastalert/tree/master/example_rules)

각 규칙 파일들은 config.yaml 의 rules_folder 항목에 규칠 파일들이 위치한 디렉토리를 지정하여 복수 적용이 가능하게 할 수 있다.
```
$ vi config.yaml

# This is the folder that contains the rule yaml files
# Any .yaml file will be loaded as a rule
rules_folder: example_rules
```