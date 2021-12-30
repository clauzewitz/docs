# Base64 문자열의 Url Safe for java

## Base64 문자열의 Url Safe
Base64 로 인코딩된 문자열을 Url 의 Query String 에 사용 시 브라우저에서 Url 인코딩으로 자동 변환되어 사용된다. 이 때 Base64 에서 사용되는 기타 특수 문자들은 문제가 없으나 하기의 특수 문자들인 경우 Url 디코딩 시에 문제가 발생한다.  
문제가 되는 특수 문자는 아래와 같다.
+ plus character(+)
+ slash caracter(/)

상기의 특수 문자들은 Url 인코딩 시 각각 %20, %2F 로 변환되며 이를 Url 디코딩하면 본래의 문자열로 치환되지 않는 문제가 발생한다.  
이를 해결하고자 다음과 같은 방법을 사용한다.
* java util
  + encoding
    - Base64.getEncoder().encodeToString() -> Base64.getUrlEncoder().encodeToString()
  + decoding
    - Base64.getDecoder().decode() -> Base64.getUrlDecoder().decode()
  + [참고문서](https://docs.oracle.com/javase/8/docs/api/java/util/Base64.html)
* apache commons
  + encoding
    - Base64.encodeBase64() -> Base64.encodeBase64URLSafe()
  + decoding
    - Base64.decodeBase64()
  + [참고문서](https://www.adobe.io/experience-manager/reference-materials/6-4/javadoc/org/apache/commons/codec/binary/Base64.html#encodeBase64URLSafe-byte:A-)
