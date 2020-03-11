# 이클립스에 Tomcat v8.X.X 연동 시 "installation is expected" 에러

## 이클립스에 Tomcat v8.X.X 연동 시 "tomcat 8.0 installation is expected" 에러
이클립스에 Tomcat v8.X.X 를 연동하려고 하면 **tomcat 8.0 installation is expected** 가 발생하는 경우가 있다.
이는 Tomcat v8.X.X 를 이클립스에서 인식을 하지 못하기때문에 발생하는 경우로 아래와 같은 방법들로 해결할 수 있다.
* v8.0.X 의 Tomcat 을 사용한다.
	+ v8.0.X 의 Tomcat 은 정상적으로 이클립스에서 인식한다.
* Tomcat 의 properties 를 수정한다.
	+ 로컬에 설치된 Tomcat 의 **catalina.jar** 를 압축 해제한다.
		- 경로 예시: c:\tomcat\apache-tomcat-8.5.43\lib\catalina.jar
	+ 압축 해제된 폴더에서 ServerInfo.properties 를 찾아 메모장 등으로 연다.
		- 경로 예시: c:\tomcat\apache-tomcat-8.5.43\lib\catalina\org\apache\catalina\util\ServerInfo.properties
	+ ServerInfo.properties 의 내용 중 **server.info** 항목의 값을 아래와 같이 수정한다.
		- 변경 전: server.info=Apache Tomcat/8.5.43
		- 변경 후: server.info=Apache Tomcat/8.0.5.43
	+ catalina.jar 를 압축해제 한 폴더를 다시 jar 로 압축한 뒤 이클립스에서 연동한다.