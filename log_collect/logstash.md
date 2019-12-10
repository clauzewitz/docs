# Logstash

## Download
[https://www.elastic.co/kr/downloads/logstash](https://www.elastic.co/kr/downloads/logstash)

## Document
[https://www.elastic.co/guide/en/logstash/current/index.html](https://www.elastic.co/guide/en/logstash/current/index.html)

## Logstash
클라이언트 등에서 발생한 로그를 수집하여 가공 등을 거쳐 로그를 처리하는 곳으로 다시금 보내는 역할을 한다.

* 데이터 흐름을 위한 오픈 소스 중앙 처리 엔진
* 파이프라인을 구축하여 데이터의 변환 및 스트림 설정
* 다양한 데이터 자원의 접근
* 통합 처리를 위한 수많은 플러그인 제공
* 수평적인 스케일링

### 파이프라인
로그를 입력 받아 가공 등을 거쳐 목적지로 로그를 전송하는 역할을 담당한다.  
기본적으로 하나의 작은 파이프라인을 시작으로 여러 개의 파이프라인을 추가하여 나간다.

#### 구성
* input: 데이터를 수집하는 소스
	+ File
	+ Syslog
	+ SQL
	+ HTTP request
	+ Elasticsearch
	+ Beats
* filter: 입력받은 데이터를 가공
	+ 로그 파싱
	+ 데이터 확장
	+ 태그 추가
* output: 가공된 데이터를 전송
	+ Elasticsearch
	+ Data storage
* codec: input 혹은 output의 데이터 인코딩 혹은 디코딩을 담당
	+ JSON
	+ String

#### 주요 필터
* geoip: ip 를 기반으로 위치 정보를 가져온다
* mutate: 데이터의 필드를 추가/삭제 혹은 값을 다른 값으로 변환한다.
* translate: 데이터의 특정 필드를 다른 값으로 변환한다.
* grok: 정규식 등을 사용하여 데이터에서 특정 값을 추출한다.
	+ [https://github.com/logstash-plugins/logstash-patterns-core/blob/master/patterns/grok-patterns](https://github.com/logstash-plugins/logstash-patterns-core/blob/master/patterns/grok-patterns)
* kv: 데이터의 구조를 key/value 구조로 변경한다.
* drop: grok 필터의 match 설정을 통과하는 데이터 중 match 설정에 부합되지 않는 데이터도 넘어가는 버그로 인하여 grok 필터 적용시 반드시 사용한다.
* json: input 에서 전달받은 데이터가 json 형식일 때 데이터를 가공할 수 있다.

## Usage
설치된 경로에서 '\bin\logstash.bat' 를 실행시킨다.
* '-e' 옵션으로 파이프라인을 인자값으로 입력받는다.
	+ logstash.bat -e 'input { stdin { } } output { stdout { } }'
* '-f' 옵션으로 파이프라인이 설정된 파일의 경로를 인자값으로 입력받는다.
	+ logstash.bat -f ..\config\sample_test.config
* pipelines.yml 에 파이프라인 설정 파일의 경로를 설정한다.
	+ logstash.bat

## Setting
Logstash 를 실행할 때 pipelines.yml 에 파이프라인 설정 파일의 경로를 설정하면 실행 및 수정된 내용을 반영하기가 쉬워진다.

### logstash.yml
변경된 설정파일을 자동으로 갱신할지를 설정하는 부분이다.  
만약 해당 설정이 비활성화되어있다면 매번 Logstash 를 재실행하여야한다.
```
config.reload.automatic: true
config.reload.interval: 3s
```

### pipelines.yml
읽어들일 파이프라인의 설정파일을 설정한다.  
windows 환경에서는 '\' 을 '\\' 로 표현함에 주의한다.
```
- pipeline.id: sample_test
  path.config: "D:\\temp_project\\logstash-7.0.1\\config\\sample_test.config"
```

## Example
톰캣의 catalina.out 을 확인하여 error 로그를 수집하는 파이프라인
* catalina.out 을 대상으로 할 때 Logstash 는 기본적으로 1 라인씩 읽어들여서 데이터를 수집한다. 하지만 발생된 에러로그는 여러 줄이 모여서 하나의 로그를 만들어 내기 때문에 이것을 추출할 수 있는 filter 를 생성하여야한다.
* 에러가 발생하였을 시에 controller 로그 등을 참조하도록 한다.
* 추출하기 쉽도록 로그의 유형을 변경한다.
* 각 서버의 catalina.out 의 로그들을 로컬에 설치된 logstash 로 정제하여 별도의 로그 취합용 logstash 서버로 전송한다. 로그 취합용 logstash 에서는 모아진 log 들을 elasticsearch(kibana 를 이용한 시각화) 및 mongodb(로그 백업용) 에 저장한다. elasticsearch 를 모니터링하는 프로세스를 개발하여 에러 등의 로그가 수집될 때 메일 등을 활용하여 알람을 한다.

### input
```
input {
    file {
        id => "gbill"
        type => "tomcat"
        path => ["D:/JAVA_dev/tomcat/apache-tomcat-7.0.70/logs/catalina.out"]
        mode => "tail"
        start_position => "beginning"
        codec => multiline {
            negate => true
            pattern => "(^%{MONTH} %{MONTHDAY} %{HOUR}:?%{MINUTE}(?::?%{SECOND}) (?:AM|PM))"
            #pattern => "(^\d{1,2}\W \d{2} \d{1,2}:\d{2}:\d{2} \W{2} (WARN|ERROR))"
            what => "previous"
        }
    }
}
```

### filter
```
filter {
    if "_grokparsefailure" in [tags] {
        drop {
        }
    }
    grok {
        match => {
            "message" => "(^%{MONTH:aa1} %{MONTHDAY:aa2} %{HOUR:aa3}:?%{MINUTE:aa4}(?::?%{SECOND:aa5}) (?<aa6>(AM|PM)))"
        }
    }
    grok {
        match => {
            "message" => ".+%{IP:clientip}.+"
        }
    }
    mutate {
        add_field => {
            "logdate" => "%{aa1}/%{aa2} %{aa3}:%{aa4}:%{aa5} %{aa6}"
            "regdate" => "%{@timestamp}"
        }
        remove_field => ["@version"]
    }
    ruby {
        code => "event.set('logdate', DateTime.parse(event.get('logdate')).new_offset(0).strftime('%m-%d %H:%M:%S'))"
    }
    date {
        match => ["logdate", "MM-dd HH:mm:ss"]
    }
    if [clientip] and ([clientip] =~ ".+") {
        mutate {
            add_field => {
                "hostinfo" => "%{host}: %{clientip}"
            }
        }
        geoip {
            source => "clientip"
            fields => ["longitude", "latitude"]
        }
    } else {
        mutate {
            add_field => {
                "hostinfo" => "%{host}"
            }
            remove_field => ["clientip"]
        }
    }
}
```

### output
```
output {
    stdout {
        codec => "rubydebug"
        #codec => "json"
    }
    file {
        path => "D:/temp_project/logstash-7.0.1/input/output_%{+YYYYMMdd}.log"
    }
    #mongodb {
    #    uri => "mongodb://test:testuser@127.0.0.1:27017/test"
    #    database => "test"
    #    collection => "logstash%{+YYYYMMdd}"
    #    codec => "json"
    #}
}
```

### result
```
{
    "aa2" => "14",
    "tags" => [
        [0] "multiline"
    ],
    "aa1" => "May",
    "regdate" => "2019-05-17T07:52:38.324Z",
    "logdate" => "05-14 12:59:31",
    "type" => "tomcat",
    "@timestamp" => 2019-05-14T03:59:31.000Z,
    "test" => "Test PC: 127.0.0.1",
    "aa4" => "59",
    "message" => "May 14 12:59:31 PM ERROR - Example Error\r",
    "aa5" => "31",
    "aa6" => "PM",
    "geoip" => {
        "latitude" => 37.4056,
        "longitude" => -122.0775
    },
    "sampleip" => "127.0.0.1",
    "host" => "Test PC",
    "aa3" => "12",
    "clientip" => "127.0.0.1",
    "path" => "D:/temp_project/logstash-7.0.1/input/sample.txt"
}
```