# Docker desktop 3.0.0 업데이트 후 "Mounts denied: approving" 에러

## Docker desktop 3.0.0 업데이트 후 "Mounts denied: approving" 에러
최근 업데이트된 Docker desktop 3.0.0 버전에서 docker-compose.yml 에 설정된 volumes 항목을 읽지못하는 경우가 있다.
이는 새로이 추가된 gRPC 관련 설정으로 인한 오류로써 아래와 같은 방법으로 해결할 수 있다.
* Docker desktop 의 gRPC 설정을 비활성화한다.
	+ Preferences -> Experimental Features -> Use gRPC FUSE for file sharing 항목 비활성화한다.
	+ [참고문서](https://github.com/docker/for-mac/issues/5115)
