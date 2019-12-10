# Error: AWS authentication requires a valid Date or x-amz-date header

## # Error: AWS authentication requires a valid Date or x-amz-date header
S3 SDK 를 사용시에 위의 에러가 발생한다면 아래와 같이 해결할 수 있다.
* Java 1.8u60 이상을 사용한다면 하위 버전의 Java를 설치하면 해결된다.
* 프로젝트 내의 라이브러리 중 joda-time.jar의 버전이 2.8.1 미만이라면 2.8.1 이상의 버전으로 교체한다.

[참고문서 1](https://stackoverflow.com/questions/32058431/aws-java-sdk-aws-authentication-requires-a-valid-date-or-x-amz-date-header)  
[참고문서 2](https://github.com/aws/aws-sdk-java/issues/484)  
[참고문서 3](https://github.com/aws/aws-sdk-java/issues/444)