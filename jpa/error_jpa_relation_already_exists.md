# ERROR: relation ... already exists

## ERROR: relation ... already exists
JPA 를 사용하여 table 이나 index 를 추가 후 재기동 시에 위의 에러가 발생한다면 아래와 같이 해결할 수 있다.
* table 의 추가인 경우 application.yml 혹은 application.property 의 jpa.hibernate.ddl-auto 항목의 값을 확인한다.
    + create 인 경우
        - update 혹은 create-drop 으로 변경한다. 만약 DDL 변경이 필요없다면 none 으로 설정한다.
* index 의 추가인 경우 index 명이 너무 길어서 발생하는 경우이므로 index 명을 줄인다.