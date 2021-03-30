# Kong API Gateway with Keycloak

## Document
[https://docs.konghq.com/hub/kong-inc/openid-connect/](https://docs.konghq.com/hub/kong-inc/openid-connect/)  
[https://coda.io/d/Kong-Authentication-and-Authorization_dKDtP8L6Ozw/Introduction_su0lJ#_lu0X6](https://coda.io/d/Kong-Authentication-and-Authorization_dKDtP8L6Ozw/Introduction_su0lJ#_lu0X6)

## Keycloak 설정

### Realm 생성
keycloak 에 Kong API Gateway 용 Realm 을 생성한다.

### Client 생성
앞서 생성한 Realm 에 Kong API Gateway 에서 사용할 Client 를 생성한다.

* Client를 생성한다.
    + ![kong01.png](kong01.png)
    + Client Id: kong_test
    + Client Protocol: openid-connect
    + Access Type: confidential
* 생성된 Client 에서 ‘Credential’ 탭을 선택한다.
    + ![kong02.png](kong02.png)
    + Client Authenticator: Client Id and Secret 선택
    + Secret 항목의 값 확인

### Role 생성
Kong API Gateway 를 통과시키기 위한 Role(권한) 을 생성한다. 생성된 Role 은 Client 및 Group 에 사용된다.

* Role 을 생성한다.
    + ![kong03.png](kong03.png)
    + Role Name: kong_role

### Client Scope 생성
앞서 생성한 Role 을 Client 에 설정하기 위해 Scope 를 생성한다.

* Client Scope 를 생성한다.
    + ![kong04.png](kong04.png)
    + Scope Name: kong_access
    + Protocol: openid-connect
* ‘Scope’ 탭을 선택한다.
    + ![kong05.png](kong05.png)
    + Realm Roles 항목에서 ‘Available Roles' 에서 kong_role 을 선택한 후 ‘Add selected’ 로 'Assigned Roles’ 로 넘긴다.

### Client 에 Client Scope 설정
kong_test Client 에 4번 항목에서 생성한 Scope 를 설정한다.

* 'Client Scope' 탭을 선택한다.
    + ![kong06.png](kong06.png)
    + 'Default Client Scopes' 항목의 ‘Available Client Scopes' 에서 kong_access 를 선택한 후 ‘Add selected’ 로 'Assigned Default Client Scopes’ 로 넘긴다.

### Group 생성
사용자 그룹화를 위한 Group 을 생성한다.

* Group 을 생성한다.
    + ![kong07.png](kong07.png)
    + Group Name: konger
* 'Role Mappings' 탭을 선택한다.
    + ![kong08.png](kong08.png)
    + 'Realm Roles' 항목의 ‘Available Roles' 에서 kong_role 를 선택한 후 ‘Add selected’ 로 'Assigned Roles’ 로 넘긴다.

### 사용자 생성
사용자를 생성한다.

* ![kong09.png](kong09.png)
* 생성된 사용자의 ‘Groups’ 탭을 선택한다.
    + ![kong10.png](kong10.png)
    + ‘Available Gorup' 에서 'konger’ 를 선택한다.

## Kong API Gateway 설정

### OpenID Connect Plugin 생성
keycloak 연동을 위한 OpenID Connect 플러그인을 생성한다.

* Kong API Gateway 관리자 페이지에서 플러그인을 생성한다.
    + ![kong11.png](kong11.png)
    + API Gateway > Plugins > New Plugin > OpenID Connect
* Scoped 를 선택(Kong 에 등록된 전체가 아닌 특정 service 와 router 에 적용하기 위함)한다.
    + ![kong12.png](kong12.png)
    + Service 와 Route 항목에 적용하고자 하는 service 및 router 를 입력한다.
* 기타 항목은 기본값으로 두되 아래의 항목에 keycloak 에서 생성한 client 정보를 입력한다.
    + Config.Client Id: client id
    + Config.Client Secret: client secret
    + Config.Issuer: http(s)://{keycloak 도메인}/auth/realms/{client 를 생성한 Realm}/
        - 예: https://test.example.com/auth/realms/TestUser
    + Config.Authorization Endpoint: http(s)://{keycloak 도메인}/auth/realms/{client 를 생성한 Realm}/protocol/openid-connet/auth
        - 예: https://test.example.com/auth/realms/TestUser/protocol/openid-connect/auth
    + Config.End Session Endpoint: http(s)://{keycloak 도메인}/auth/realms/{client 를 생성한 Realm}/protocol/openid-connet/logout
        - 예: https://test.example.com/auth/realms/TestUser/protocol/openid-connect/logout
    + Config.Extra Jwks Uris: http(s)://{keycloak 도메인}/auth/realms/{client 를 생성한 Realm}/protocol/openid-connet/certs
        - 예: https://test.example.com/auth/realms/TestUser/protocol/openid-connect/certs
    + Config.Introspection Endpoint: http(s)://{keycloak 도메인}/auth/realms/{client 를 생성한 Realm}/protocol/openid-connet/introspect
        - 예: https://test.example.com/auth/realms/TestUser/protocol/openid-connect/token/introspect
    + Config.Token Endpoint: http(s)://{keycloak 도메인}/auth/realms/{client 를 생성한 Realm}/protocol/openid-connet/token
        - 예: https://test.example.com/auth/realms/TestUser/protocol/openid-connect/token
    + Config.Userinfo Endpoint: http(s)://{keycloak 도메인}/auth/realms/{client 를 생성한 Realm}/protocol/openid-connet/userinfo
        - 예: https://test.example.com/auth/realms/TestUser/protocol/openid-connect/userinfo
    + Config.Scopes Required: kong_access
    + role 설정
        - OIDC plugin 만을 사용할 때
            * Config.Roles Claim: realm_access,roles
            * Config.Roles Required: kong_role
        - ACL plugin 을 연동할 때
            * Config.Authenticated Groups Claim: realme_access,roles
            * role 설정은 ACL plugin 에서 설정
    + Config.Consumer Optional: true
    + Config.Verify Signature: false
    + Config.SSL Verify: false

### ACL Plugin 생성
keycloak 의 role 확인을 위한 ACL 플러그인을 생성한다.

* Kong API Gateway 관리자 페이지에서 플러그인을 생성한다.
    + ![kong13.png](kong13.png)
    + API Gateway > Plugins > New Plugin > ACL
* Scoped 를 선택(Kong 에 등록된 전체가 아닌 특정 service 와 router 에 적용하기 위함)한다.
    + ![kong14.png](kong14.png)
    + Service 와 Route 항목에 적용하고자 하는 service 및 router 를 입력한다.
* 접근을 허용 또는 차단할 role 을 아래의 항목에 추가한다.
    + Config.Allow: 접근을 허용할 role
    + Config.Deny: 접근을 차단할 role

## Test

### Kong API Gateway without Keycloak

#### keycloak 인증 없이 바로 호출한 경우

###### Request
```
curl "https://kong.example.com/apitest/" \
-H "Accept: application/json" \
-H "Authorization: Bearer"
```

###### Response
```
{
    "message": "An unexpected error occurred"
}
```

#### Kong API Gateway with Keycloak

##### Keycloak 인증

###### Request - Keycloak Login
```
curl -s -X POST \
        -H "Content-Type: application/x-www-form-urlencoded" \
        -d "username=demouser" \
        -d "password=demouser" \
        -d 'grant_type=password' \
        -d "client_id=kong_test" \
        https://test.example.com/auth/realms/TestUser/protocol/openid-connect/token
```

###### Response
```
{
    "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJpcm1yX2FqemQ2SGdpcVp5d2JWaUJDbDB6M2k0UEJTU2RVQW5iUzUwUUZzIn0.eyJleHAiOjE2MTI5MzM4NTcsImlhdCI6MTYxMjkzMzU1NywianRpIjoiOTZiYjUwMTItYjg5MC00YmFkLWEzYmYtM2RlYzNiMWM2MGE2IiwiaXNzIjoiaHR0cHM6Ly9zc28uZGV2Lmt1cmx5LnNlcnZpY2VzL2F1dGgvcmVhbG1zL0t1cmx5VXNlciIsImF1ZCI6ImFjY291bnQiLCJzdWIiOiI2NmZjNjdkYy0xYTg0LTQ3ZTItYmY2ZS1lZTJkMTQwOTMyN2YiLCJ0eXAiOiJCZWFyZXIiLCJhenAiOiJrb25nX3Rlc3QiLCJzZXNzaW9uX3N0YXRlIjoiZThjN2QwZDgtMTMyZC00NzNkLWI3OWUtNjQzZWEyNTJiNzQzIiwiYWNyIjoiMSIsInJlYWxtX2FjY2VzcyI6eyJyb2xlcyI6WyJrb25nX3JvbGUiLCJvZmZsaW5lX2FjY2VzcyIsInVtYV9hdXRob3JpemF0aW9uIl19LCJyZXNvdXJjZV9hY2Nlc3MiOnsiYWNjb3VudCI6eyJyb2xlcyI6WyJtYW5hZ2UtYWNjb3VudCIsIm1hbmFnZS1hY2NvdW50LWxpbmtzIiwidmlldy1wcm9maWxlIl19fSwic2NvcGUiOiJrb25nX2FjY2VzcyBwcm9maWxlIGVtYWlsIiwiZW1haWxfdmVyaWZpZWQiOmZhbHNlLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJzdW5nanVuLmtpbV9iQGt1cmx5Y29ycC5jb20iLCJnaXZlbl9uYW1lIjoiIiwiZmFtaWx5X25hbWUiOiIifQ.IAzFIZqZ_bfvAnQGh2Qa_XkQdgatx4bRdGscXv4nGSOxmSsw9hNzLcorB_Fld4MI4z7BbbqS6slaeBkJnSsLViSC6hcev-iU_bdVdZ9cclHLsoPz3E-4GZnOvAwzMBHRoAyK6ZBtJ1P13G40UDdMEfXcEYtw0WS7wk-zCHkGQXsfXK_CH1MF0Pzlfipk1g7zqEZzsowq4BwAWMLPLlvDsce4dsZfSGZAgyuWWORM03Kux5TLOY8jBKniell1fJKPdkGNZQtzlTIXe-BvG6jvdYeg-NfpqQdIKlVlPQxhM12CIX1EeUscWpssxKQwD8COt1a_PmW_Kp6CRZk18rvIkA",
    "expires_in": 300,
    "refresh_expires_in": 3600,
    "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJkNjk3ZWQ3Yi04ZTJiLTQ1NDMtYjFiOC1kNTU3NWJiZjhkMzAifQ.eyJleHAiOjE2MTI5MzcxNTcsImlhdCI6MTYxMjkzMzU1NywianRpIjoiOTAzNjUwMTEtNDQwNi00ZDdhLWIxZWItZmMyODRhZjYxYjY2IiwiaXNzIjoiaHR0cHM6Ly9zc28uZGV2Lmt1cmx5LnNlcnZpY2VzL2F1dGgvcmVhbG1zL0t1cmx5VXNlciIsImF1ZCI6Imh0dHBzOi8vc3NvLmRldi5rdXJseS5zZXJ2aWNlcy9hdXRoL3JlYWxtcy9LdXJseVVzZXIiLCJzdWIiOiI2NmZjNjdkYy0xYTg0LTQ3ZTItYmY2ZS1lZTJkMTQwOTMyN2YiLCJ0eXAiOiJSZWZyZXNoIiwiYXpwIjoia29uZ190ZXN0Iiwic2Vzc2lvbl9zdGF0ZSI6ImU4YzdkMGQ4LTEzMmQtNDczZC1iNzllLTY0M2VhMjUyYjc0MyIsInNjb3BlIjoia29uZ19hY2Nlc3MgcHJvZmlsZSBlbWFpbCJ9.s1-uN7hkTM20LW4xV5QQzdLY3XWuDECEkfwCBwHEmS8",
    "token_type": "bearer",
    "not-before-policy": 1610329392,
    "session_state": "e8c7d0d8-132d-473d-b79e-643ea252b743",
    "scope": "kong_access profile email"
}
```

###### Request - Keycloak Refresh Token
```
curl -s -X POST \
        -H "Content-Type: application/x-www-form-urlencoded" \
        -d "refresh_token=eyJhbGciOiJIUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJkNjk3ZWQ3Yi04ZTJiLTQ1NDMtYjFiOC1kNTU3NWJiZjhkMzAifQ.eyJleHAiOjE2MTI5MzcxNTcsImlhdCI6MTYxMjkzMzU1NywianRpIjoiOTAzNjUwMTEtNDQwNi00ZDdhLWIxZWItZmMyODRhZjYxYjY2IiwiaXNzIjoiaHR0cHM6Ly9zc28uZGV2Lmt1cmx5LnNlcnZpY2VzL2F1dGgvcmVhbG1zL0t1cmx5VXNlciIsImF1ZCI6Imh0dHBzOi8vc3NvLmRldi5rdXJseS5zZXJ2aWNlcy9hdXRoL3JlYWxtcy9LdXJseVVzZXIiLCJzdWIiOiI2NmZjNjdkYy0xYTg0LTQ3ZTItYmY2ZS1lZTJkMTQwOTMyN2YiLCJ0eXAiOiJSZWZyZXNoIiwiYXpwIjoia29uZ190ZXN0Iiwic2Vzc2lvbl9zdGF0ZSI6ImU4YzdkMGQ4LTEzMmQtNDczZC1iNzllLTY0M2VhMjUyYjc0MyIsInNjb3BlIjoia29uZ19hY2Nlc3MgcHJvZmlsZSBlbWFpbCJ9.s1-uN7hkTM20LW4xV5QQzdLY3XWuDECEkfwCBwHEmS8" \
        -d 'grant_type=refresh_token' \
        -d "client_id=kong_test" \
        https://test.example.com/auth/realms/TestUser/protocol/openid-connect/token
```

###### Response
```
{
    "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJpcm1yX2FqemQ2SGdpcVp5d2JWaUJDbDB6M2k0UEJTU2RVQW5iUzUwUUZzIn0.eyJleHAiOjE2MTI5MzM4NTcsImlhdCI6MTYxMjkzMzU1NywianRpIjoiOTZiYjUwMTItYjg5MC00YmFkLWEzYmYtM2RlYzNiMWM2MGE2IiwiaXNzIjoiaHR0cHM6Ly9zc28uZGV2Lmt1cmx5LnNlcnZpY2VzL2F1dGgvcmVhbG1zL0t1cmx5VXNlciIsImF1ZCI6ImFjY291bnQiLCJzdWIiOiI2NmZjNjdkYy0xYTg0LTQ3ZTItYmY2ZS1lZTJkMTQwOTMyN2YiLCJ0eXAiOiJCZWFyZXIiLCJhenAiOiJrb25nX3Rlc3QiLCJzZXNzaW9uX3N0YXRlIjoiZThjN2QwZDgtMTMyZC00NzNkLWI3OWUtNjQzZWEyNTJiNzQzIiwiYWNyIjoiMSIsInJlYWxtX2FjY2VzcyI6eyJyb2xlcyI6WyJrb25nX3JvbGUiLCJvZmZsaW5lX2FjY2VzcyIsInVtYV9hdXRob3JpemF0aW9uIl19LCJyZXNvdXJjZV9hY2Nlc3MiOnsiYWNjb3VudCI6eyJyb2xlcyI6WyJtYW5hZ2UtYWNjb3VudCIsIm1hbmFnZS1hY2NvdW50LWxpbmtzIiwidmlldy1wcm9maWxlIl19fSwic2NvcGUiOiJrb25nX2FjY2VzcyBwcm9maWxlIGVtYWlsIiwiZW1haWxfdmVyaWZpZWQiOmZhbHNlLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJzdW5nanVuLmtpbV9iQGt1cmx5Y29ycC5jb20iLCJnaXZlbl9uYW1lIjoiIiwiZmFtaWx5X25hbWUiOiIifQ.IAzFIZqZ_bfvAnQGh2Qa_XkQdgatx4bRdGscXv4nGSOxmSsw9hNzLcorB_Fld4MI4z7BbbqS6slaeBkJnSsLViSC6hcev-iU_bdVdZ9cclHLsoPz3E-4GZnOvAwzMBHRoAyK6ZBtJ1P13G40UDdMEfXcEYtw0WS7wk-zCHkGQXsfXK_CH1MF0Pzlfipk1g7zqEZzsowq4BwAWMLPLlvDsce4dsZfSGZAgyuWWORM03Kux5TLOY8jBKniell1fJKPdkGNZQtzlTIXe-BvG6jvdYeg-NfpqQdIKlVlPQxhM12CIX1EeUscWpssxKQwD8COt1a_PmW_Kp6CRZk18rvIkA",
    "expires_in": 300,
    "refresh_expires_in": 3600,
    "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJkNjk3ZWQ3Yi04ZTJiLTQ1NDMtYjFiOC1kNTU3NWJiZjhkMzAifQ.eyJleHAiOjE2MTI5MzcxNTcsImlhdCI6MTYxMjkzMzU1NywianRpIjoiOTAzNjUwMTEtNDQwNi00ZDdhLWIxZWItZmMyODRhZjYxYjY2IiwiaXNzIjoiaHR0cHM6Ly9zc28uZGV2Lmt1cmx5LnNlcnZpY2VzL2F1dGgvcmVhbG1zL0t1cmx5VXNlciIsImF1ZCI6Imh0dHBzOi8vc3NvLmRldi5rdXJseS5zZXJ2aWNlcy9hdXRoL3JlYWxtcy9LdXJseVVzZXIiLCJzdWIiOiI2NmZjNjdkYy0xYTg0LTQ3ZTItYmY2ZS1lZTJkMTQwOTMyN2YiLCJ0eXAiOiJSZWZyZXNoIiwiYXpwIjoia29uZ190ZXN0Iiwic2Vzc2lvbl9zdGF0ZSI6ImU4YzdkMGQ4LTEzMmQtNDczZC1iNzllLTY0M2VhMjUyYjc0MyIsInNjb3BlIjoia29uZ19hY2Nlc3MgcHJvZmlsZSBlbWFpbCJ9.s1-uN7hkTM20LW4xV5QQzdLY3XWuDECEkfwCBwHEmS8",
    "token_type": "bearer",
    "not-before-policy": 1610329392,
    "session_state": "e8c7d0d8-132d-473d-b79e-643ea252b743",
    "scope": "kong_access profile email"
}
```

##### API 호출

###### Request - Kong API Gateway
```
curl "https://kong.example.com/apitest/" \
-H "Accept: application/json" \
-H "Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJpcm1yX2FqemQ2SGdpcVp5d2JWaUJDbDB6M2k0UEJTU2RVQW5iUzUwUUZzIn0.eyJleHAiOjE2MTI5MzM4NTcsImlhdCI6MTYxMjkzMzU1NywianRpIjoiOTZiYjUwMTItYjg5MC00YmFkLWEzYmYtM2RlYzNiMWM2MGE2IiwiaXNzIjoiaHR0cHM6Ly9zc28uZGV2Lmt1cmx5LnNlcnZpY2VzL2F1dGgvcmVhbG1zL0t1cmx5VXNlciIsImF1ZCI6ImFjY291bnQiLCJzdWIiOiI2NmZjNjdkYy0xYTg0LTQ3ZTItYmY2ZS1lZTJkMTQwOTMyN2YiLCJ0eXAiOiJCZWFyZXIiLCJhenAiOiJrb25nX3Rlc3QiLCJzZXNzaW9uX3N0YXRlIjoiZThjN2QwZDgtMTMyZC00NzNkLWI3OWUtNjQzZWEyNTJiNzQzIiwiYWNyIjoiMSIsInJlYWxtX2FjY2VzcyI6eyJyb2xlcyI6WyJrb25nX3JvbGUiLCJvZmZsaW5lX2FjY2VzcyIsInVtYV9hdXRob3JpemF0aW9uIl19LCJyZXNvdXJjZV9hY2Nlc3MiOnsiYWNjb3VudCI6eyJyb2xlcyI6WyJtYW5hZ2UtYWNjb3VudCIsIm1hbmFnZS1hY2NvdW50LWxpbmtzIiwidmlldy1wcm9maWxlIl19fSwic2NvcGUiOiJrb25nX2FjY2VzcyBwcm9maWxlIGVtYWlsIiwiZW1haWxfdmVyaWZpZWQiOmZhbHNlLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJzdW5nanVuLmtpbV9iQGt1cmx5Y29ycC5jb20iLCJnaXZlbl9uYW1lIjoiIiwiZmFtaWx5X25hbWUiOiIifQ.IAzFIZqZ_bfvAnQGh2Qa_XkQdgatx4bRdGscXv4nGSOxmSsw9hNzLcorB_Fld4MI4z7BbbqS6slaeBkJnSsLViSC6hcev-iU_bdVdZ9cclHLsoPz3E-4GZnOvAwzMBHRoAyK6ZBtJ1P13G40UDdMEfXcEYtw0WS7wk-zCHkGQXsfXK_CH1MF0Pzlfipk1g7zqEZzsowq4BwAWMLPLlvDsce4dsZfSGZAgyuWWORM03Kux5TLOY8jBKniell1fJKPdkGNZQtzlTIXe-BvG6jvdYeg-NfpqQdIKlVlPQxhM12CIX1EeUscWpssxKQwD8COt1a_PmW_Kp6CRZk18rvIkA"
```

###### Response
성공
```
TOMS API Application
```
실패  
인증 정보가 없는 경우
```
{
    "message": "An unexpected error occurred"
}
```
인증이 만료되었거나 잘못된 인증 정보인 경우
```
{
    "message": "Unauthorized"
}
```
권한이 없는 경우
```
{
    "message": "Forbidden"
}
```

## Issue

### Keycloak 로그인 호출 시 에러 발생
```
{
    "error": "unauthorized_client",
    "error_description": "Client secret not provided in request"
}
```
설정된 Client 의 Access Type 이 ‘public' 이 아닌 ‘confidential’ 또는 'Bearer only’ 로 설정되어 발생한 문제이다.
* 파라메터에 client_secret 를 같이 전달하면 해결되나 이는 적절치 않은 방법이다.
* 다른 방법으로는 keycloak 에 client 를 하나더 만들어 사용한다.
    + 첫번째 client 는 본 문서에 기술된대로 Kong API Gateway 를 위한 것으로 Kong API Gateway 에서 인증용으로 사용한다.
    + 두번째 client 는 Kong API Gateway 에 연결된 사용하고자 하는 프로젝트의 로그인에 사용되는 client 또는 새로운 client 를 생성하여 로그인에만 사용한다.

### keycloak 의 scope 와 role
scope 와 role 모두 특정 권한만을 위해 동작하는 것으로 scope 에 설정된 role 에 의해 더더욱 세밀한 접근제어가 가능하다.  
~~단, 현재 Kong API Gateway 에서는 하나의 plugin 에는 scope, role 각각 하나씩만 설정할 수 있다.~~  
scope 는 OpenID Connect plugin 에서만 설정이 가능하나, role 은 OIDC plugin 과 ACL plugin 을 연동하는 방법 2가지로 나뉜다

## Summary
* keycloak 에서는 1개 또는 2개의 client 가 존재하여야 한다.
    + 1개의 클라이언트를 갖는 경우
        - Kong API Gateway 의 OpenID Connect plugin 을 설정 및 로그인을 위한 client
            * client 의 Access Type 을 ‘confidential' 로 설정하고 OpenID Connect plugin 설정을 마치면 Access Type 을 'public’ 을 변경하여도 정상적으로 동작한다.
    + 2개의 클라이언트를 갖는 경우
        - Kong API Gateway 의 OpenID Connect plugin 을 설정하기 위한 client
            * Access Type: confidential
        - Kong API Gateway 에 연결된 서버의 권한을 갖기 위한 client
            * Access Type: public
* ~~Kong API Gateway 의 OpenID Connect plugin 에서는 scope 만 사용하도록 한다.~~
    + ~~Config.Scopes Required 항목에 특정 scope 를 입력하면 정상 동작함.~~
    + ~~Config.Roles Required 항목에 특정 role 을 입력하면 'Forbidden' 을 반환한다.~~
        - ~~이에 대해서는 추가 조사가 필요함~~
        - ~~ACL plugin 을 추가 생성하여 허용/차단 할 role 을 설정한다.~~
* 연동하는 클라이언트 측에서는 API 호출 전 keycloak 로그인 API 를 선행 호출 혹은 액세스 토큰을 재사용 해야한다.
    + Kong API Gateway Endpoint 호출 시에 access token 만을 이용한 갱신 혹은 keycloak 자동 인증은 지원하지 않는다.
        - 브라우저를 통한 keycloak 인증 후 access token 취득
        - rest API 를 통한 keycloak 인증 후 access token 취득
    + 액세스 토큰이 없거나 만료된 경우 재로그인 하는 추가 프로세스 구현 필요
        - 액세스 토큰이 만료된 경우 refresh token 을 사용하여 액세스 토큰을 갱신한다.
            * OpenID Connect plugin 설정 시 사용하는 Config.Refresh Token Param Name, Config.Refresh Token Param Type 은 현재 버그로 동작하지 않는다.
        - 액세스 토큰이 없는 경우 로그인 과정을 통해 액세스 토큰을 받아온다.