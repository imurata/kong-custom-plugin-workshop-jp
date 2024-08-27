## Introduction
.envをKong EEライセンス(必要な場合)、Kongのバージョン、Postgresのバージョンについて更新します。

localhostにKong Gatewayを立てない場合はKONG_PORTAL_GUI_URLとKONG_ADMIN_GUI_URLをホストIPに変更します。

```sh
sed -i 's/localhost/<Your Host IP>/g' docker-compose.yml
```
docker-composeを使用してKong GatewayとDBコンテナを起動します。docker-composeを起動する前に、データベースの初期化を実行する必要があります。

```shell
docker-compose run kong kong migrations bootstrap
docker-compose up -d
```

## コンテナの起動確認

```shell
$ docker ps 
CONTAINER ID   IMAGE                        COMMAND               CREATED              STATUS              PORTS                NAMES
50db1a0d3da8   postgres:13-alpine           "postgres"            About a minute ago   Up About a minute                        kong-database
dbac4b654807   kong/kong-gateway:3.4.3.12   "kong docker-start"   About a minute ago   Up About a minute   8000/tcp, 8443/tcp   kong
```

## Serviceの追加

```shell
http POST :8001/services name=example-service url=http://httpbin.org
```

## ServiceにRouteを追加

```shell
http POST :8001/services/example-service/routes name=transform-route paths:='["/transform"]'
```

## ServiceにPluginを追加

```shell
http -f POST :8001/services/example-service/plugins name=request-transformer config.remove.headers=accept config.remove.querystring=custId config.remove.body=custId
```
Request Transformer Pluginを有効にし、`accept` ヘッダ、クエリ文字列の `custId`、および本文の `custId` を削除します。
利用可能な設定値について以下を参照してください。
https://docs.konghq.com/hub/kong-inc/request-transformer/

## Test

```shell
http :8000/transform/anything custId==200 a==100 
```

Response:

```shell
HTTP/1.1 200 OK
Access-Control-Allow-Credentials: true
Access-Control-Allow-Origin: *
Connection: keep-alive
Content-Length: 588
Content-Type: application/json
Date: Fri, 23 Aug 2024 02:56:53 GMT
Server: gunicorn/19.9.0
Via: kong/3.4.3.12-enterprise-edition
X-Kong-Proxy-Latency: 1171
X-Kong-Request-Id: 7c38c95f9d4d0004974b741344ca6d39
X-Kong-Upstream-Latency: 528

{
    "args": {
        "a": "100"
    },
    "data": "",
    "files": {},
    "form": {},
    "headers": {
        "Accept-Encoding": "gzip, deflate",
        "Host": "httpbin.org",
        "User-Agent": "HTTPie/3.2.2",
        "X-Amzn-Trace-Id": "Root=1-66c7fa75-0b325b526e7df97a5fc47ead",
        "X-Forwarded-Host": "localhost",
        "X-Forwarded-Path": "/transform/anything",
        "X-Forwarded-Prefix": "/transform",
        "X-Kong-Request-Id": "7c38c95f9d4d0004974b741344ca6d39"
    },
    "json": null,
    "method": "GET",
    "origin": "192.168.127.1, 214.215.6.147",
    "url": "http://localhost/anything?a=100"
}
```

## Cleanup

```shell
docker-compose down --volumes
```
