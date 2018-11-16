# Gentics Mesh Docker Compose Stack 

This repository contains an example docker compose stack for the [Headless CMS Gentics Mesh](https://getmesh.io)

The stack contains the following components:

* Gentics Mesh
* Elasticsearch
* Keycloak

## Usage

```bash
git clone git@github.com:gentics/mesh-compose.git
cd mesh-compose
docker-compose up -d
```

## Keycloak

This compose stack can serve as an example on how to integrate Gentics Mesh with Keycloak.

The needed realm for Keycloak will automatically be imported during Keycloak startup.

1. Login Keycloak

Once the server is up and running you can authenticate against Keycloak server on port `8082` using the user that was listed in the imported `realm.json` file.


```
* Preparing request to http://localhost:8082/auth/realms/master-test/protocol/openid-connect/token
* Using libcurl/7.61.1 OpenSSL/1.1.0h zlib/1.2.11 nghttp2/1.29.0
* Enable automatic URL encoding
* Disable SSL validation
* Enable cookie sending with jar of 1 cookie
* Found bundle for host localhost: 0x2aa8c68d1780 [can pipeline]
*   Trying ::1...
* TCP_NODELAY set
* connect to ::1 port 8082 failed: Connection refused
*   Trying 127.0.0.1...
* TCP_NODELAY set
* Connected to localhost (127.0.0.1) port 8082 (#1)
> POST /auth/realms/master-test/protocol/openid-connect/token HTTP/1.1
> Host: localhost:8082
> User-Agent: insomnia/6.2.0
> Content-Type: application/x-www-form-urlencoded
> Accept: */*
> Content-Length: 120
| client_id=mesh&username=dummyuser&password=finger&grant_type=password&client_secret=9b65c378-5b4c-4e25-b5a1-a53a381b5fb4
* upload completely sent off: 120 out of 120 bytes
< HTTP/1.1 200 OK
< Connection: keep-alive
< Set-Cookie: KC_RESTART=; Version=1; Expires=Thu, 01-Jan-1970 00:00:10 GMT; Max-Age=0; Path=/auth/realms/master-test; HttpOnly
< Content-Type: application/json
< Content-Length: 2302
< Date: Fri, 16 Nov 2018 20:27:28 GMT

* Received 2.2 KB chunk
* Connection #1 to host localhost left intact
* Saved 1 cookie
```

Response:

```
{
  "access_token": "eyJhbGc … OKkVzyBgdA",
  "expires_in": 60,
  "refresh_expires_in": 1800,
  "refresh_token": "eyJhbGc … 8OeRbypkaw",
  "token_type": "bearer",
  "not-before-policy": 0,
  "session_state": "11525c4b-e498-432f-9e72-c76fe219e67f"
}
```

2. Use the `access_token`, set it to the `Authorization` header and invoke the request to Gentics Mesh on port `8081`. The user which was previously only listed in keycloak will be added to Gentics Mesh and updated every time a new access token is used.

```
* Preparing request to http://127.0.0.1:8081/api/v1/auth/me
* Using libcurl/7.61.1 OpenSSL/1.1.0h zlib/1.2.11 nghttp2/1.29.0
* Enable automatic URL encoding
* Disable SSL validation
* Enable cookie sending with jar of 2 cookies
* Found bundle for host 127.0.0.1: 0x2aa8c64f7c30 [can pipeline]
* Re-using existing connection! (#5) with host 127.0.0.1
* Connected to 127.0.0.1 (127.0.0.1) port 8081 (#5)
> GET /api/v1/auth/me HTTP/1.1
> Host: 127.0.0.1:8081
> User-Agent: insomnia/6.2.0
> Authorization: Bearer eyJhbGciOi … c3DhquIg
> Accept: */*
< HTTP/1.1 200 OK
< Content-Type: application/json; charset=utf-8
< Cache-Control: no-cache
< Content-Length: 732

* Received 732 B chunk
* Connection #5 to host 127.0.0.1 left intact
```

Response:

```
{
  "uuid": "084eeb766627427f8eeb766627d27f57",
  "creator": {
    "uuid": "3a0b0a2fbc4847168b0a2fbc48b7167b"
  },
  "created": "2018-11-16T20:36:18Z",
  "editor": {
    "uuid": "3a0b0a2fbc4847168b0a2fbc48b7167b"
  },
  "edited": "2018-11-16T20:36:18Z",
  "lastname": "User",
  "firstname": "Dummy",
  "username": "dummyuser",
  "emailAddress": "dummy@dummy.dummy",
  "enabled": true,
  "groups": [
    {
      "name": "group1",
      "uuid": "ecb60ff3a646447cb60ff3a646647c8b"
    },
    {
      "name": "group2",
      "uuid": "b5f11b7760554e88b11b7760553e88a4"
    }
  ],
  "permissions": {
    "create": false,
    "read": false,
    "update": false,
    "delete": false,
    "publish": false,
    "readPublished": false
  }
}
```

The configured `mapperscript.js` will be used to assign groups to the user via the given claims from the Keycloak access token. You can add additional claims to process more fields in the mapper script if you need to.


## Problems / Solutions

### Elasticsearch does not start.

On some system the Elasticsearch server does not start due to the `vm.max_map_count` kernel limit.

```
mesh-elasticsearch | [1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
```

Solution see: https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html#docker-cli-run-prod-mode

## Suggestions

Placing the Elasticsearch data directory on NFS should be avoided since using NFS with Elasticsearch can lead to an unstable and slow search server.


