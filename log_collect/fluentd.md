# Fluentd

## Download
[https://www.fluentd.org/download](https://www.fluentd.org/download)

### Caution
* 기본 설치 경로 권장(설치 경로 변경 시 오류 발생)
* default_external already set to ascii-8bit (RuntimeError) 에러 발생 시
	+ C:\opt\td-agent\embedded\lib\ruby\gems\2.4.0\gems\fluentd-1.4.2\lib\fluent\supervisor.rb 파일
		- fluentd_spawn_cmd = [ServerEngine.ruby_bin_path, "-Eascii-8bit:ascii-8bit"] 을 fluentd_spawn_cmd = [ServerEngine.ruby_bin_path] 으로 수정
* 윈도우 환경에서 fluent-gem 을 통한 mongodb plugin 을 설치할 때 "ERROR: Failed to build gem native extension" 에러 발생 시
	+ 최신 버전에 윈도우용 plugin 이 존재하지 않아 발생되는 에러
	+ gem install fluent-plugin-mongo -v 0.7.16 으로 설치
* 입력받는 소스에는 'time' 이란 key 를 사용하는 value 의 유형은 'string' 또는 'number' 로만 제한하여야 한다. 플루언트에서는 'time' key 는 일종의 예약어로 'string' 또는 'number' 유형만 지원하기 때문

## Document
[https://docs.fluentd.org/](https://docs.fluentd.org/)

## Fluentd

### Event
Logstash 의 파이프라인과 유사한 개념

#### 구성
* source: 데이터를 수집하는 소스
	+ File
	+ Syslog
	+ SQL
	+ NoSQL
	+ HTTP request
	+ Elasticsearch
	+ BigQuery
* filter: 입력받은 데이터를 가공
	+ 로그 파싱
	+ 데이터 확장
	+ 태그 추가
* match: 가공된 데이터를 전송
	+ Elasticsearch
	+ Data storage
	+ File
	+ BigQuery
	+ 설정된 순서에 따라 적용됨에 주의
	+ tag 는 여러개를 설정할 수 있다.
		- <match a.** b.**>
		- <match a.{temp,test}.*>

#### 주요 필터
* geoip: ip 를 기반으로 위치 정보를 가져온다.
* record_transformer: 데이터에 특정 필드를 추가 혹은 수정한다.
* parser: 정규식 혹은 특정 필드 값만을 추출한다.
* grep: 정규식 등을 사용한 조건에 부합하는 데이터들만을 추출한다.

## Usage
윈도우 시작 메뉴에서 'Td-agent Command Prompt' 실행하고 'fluentd' 를 실행시킨다.
* '-c' 옵션으로 설정 파일의 경로를 인자값으로 입력받는다.
	+ fluentd -c etc\td-agent\td-agent.conf

### Fluent-Logger 사용
플루언트는 로그스태쉬와 다르게 logger 라이브러리를 이용하면 java 프로젝트 내에 로그를 표시하듯이 원하는 시점에 로깅이 가능하다.  
단, logger 을 사용하기 위해서는 플루언트를 설치 및 실행 중이어야 하며 설정 파일은 TCP 설정으로 하여야한다.  
본 문서에서는 취급하지 않는다.  
[참고문서](http://github.com/fluent/fluent-logger-java)

## Setting

### td-agent.conf
로그를 수집할 이벤트들을 설정한다.  
이벤트를 설정할 때 match 디렉티브는 설정된 순서에 따라 적용됨에 주의한다.  
서버의 CPU 프로세스에 따라 worker 를 생성하여 각 워커별로 이벤트를 등록하여 병렬처리가 가능하다. 단, 해당 프로세스를 종료시킬 때 에러가 발생.(좀 더 조사 필요)
```
2019-08-22 16:13:20 +0900 [info]: Worker 1 finished with status 0
Unexpected error Broken pipe
  C:/opt/td-agent/embedded/lib/ruby/gems/2.4.0/gems/serverengine-2.1.1/lib/serverengine/multi_spawn_server.rb:51:in `write'
  C:/opt/td-agent/embedded/lib/ruby/gems/2.4.0/gems/serverengine-2.1.1/lib/serverengine/multi_spawn_server.rb:51:in `stop'
  C:/opt/td-agent/embedded/lib/ruby/gems/2.4.0/gems/serverengine-2.1.1/lib/serverengine/server.rb:104:in `block (2 levels) in install_signal_handlers'
  C:/opt/td-agent/embedded/lib/ruby/gems/2.4.0/gems/serverengine-2.1.1/lib/serverengine/signal_thread.rb:96:in `main'
```

## Example
tomcat의 catalina 로그를 확인하여 수집하는 파이프라인
* 플루언트는 기본적으로 1 라인씩 읽어들여서 데이터를 수집한다.
* mongodb 등에서 검색 등이 용이하게 로그의 유형을 변경한다.
* 필터에서는 로그가 발생한 시간을 unixtime 으로 변환 및 json string 을 json 형식으로 변환시킨다.
* 로그를 파싱할 때 pos_file 설정은 하지 않음에 주의한다.
	+ catalina.out 은 logrotate 로 매일 백업 및 초기화 하기 때문에 매번 마지막 읽은 곳을 기억할 필요가 없음

### 정규식을 이용한 파싱 결과 테스트 사이트
[http://fluentular.herokuapp.com/](http://fluentular.herokuapp.com/)

### source
```
<source>
    @type tail
    <parse>
        @type multiline
        format_firstline /^([a-zA-Z가-힣0-9]{1,} \d{2} \d{1,2}:\d{2}:\d{2} [a-zA-Z가-힣]{2} [a-zA-Z]{4,})|^([a-zA-Z가-힣0-9]{1,} \d{2}, \d{4} \d{1,2}:\d{2}:\d{2} [a-zA-Z가-힣]{2} .+)/
        format1 /^(?<reg_date>[a-zA-Z가-힣0-9]{1,} \d{2} \d{1,2}:\d{2}:\d{2} [a-zA-Z가-힣]{2}) (?<log_stage>[a-zA-Z]{4,}) - (?<class>[a-zA-Z0-9$]*)\.(?<method>[a-zA-Z0-9$]*)\(\d{1,}\) \| (?<detail>.*)\n/
        format2 /(?<exception>[a-zA-Z0-9$.]*): (?<content>.*)/
        time_key reg_date
        time_type string
        time_format %b %d %H:%M:%S %p
        keep_time_key true
    </parse>
    path /storage/tomcat/logs/catalina.out
    read_from_head true
    tag tomcat.exception.test
    from_encoding utf-8
    encoding utf-8
</source>
```

### filter
```
<filter tomcat.exception.**>
    @type record_transformer
    enable_ruby true
    <record>
        reg_date ${time.strftime('%Y-%m-%d %H:%M:%S')}
    </record>
</filter>
```

### match
```
<match tomcat.exception.**>
    @type copy
    <store>
        @type stdout
        <format>
            @type json
        </format>
    </store>
</match>
```

### result
```
{
    "_id" : "5d5e3a310087d23a47000002",
    "reg_date" : 1565747364,
    "class" : "TestRepository",
	"method" : "addDocument",
    "exception" : "org.springframework.dao.DuplicateKeyException",
    "content" : "PreparedStatementCallback; SQL "
}
```