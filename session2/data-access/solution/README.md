## Pongoの起動

```shell
cd kong-plugin
pongo up
```

異なるバージョン、イメージ、license_dataを指定するには環境変数を設定します。

```shell
KONG_VERSION=2.3.x pongo up
POSTGRES=10 KONG_VERSION=2.3.x pongo up
POSTGRES=10 KONG_LICENSE_DATA=<your_license_data> pongo up
```

## Serviceの公開

```shell
pongo expose
```

## Kongイメージをシェルで起動してアタッチ

```shell
pongo shell
```

以下のコマンドは Kong シェルから実行する必要があります。

## データベースの初期化

```shell
kong migrations bootstrap --force
kong start
```

## Serviceの追加

```shell
http POST :8001/services name=example-service url=http://httpbin.org
```

## RouteをServiceに追加

```shell
http POST :8001/services/example-service/routes name=example-route paths:='["/echo"]'
```

### テスト

プラグインを有効にしアクセスし、ServiceとRouteの一覧が取得できるか確認します。

```shell
http -f :8001/services/example-service/plugins name=myplugin
http :8000/echo/anything
```

Response:

```shell
HTTP/1.1 200 OK
Bye-World: this is on the response
Connection: keep-alive
Content-Length: 690
Content-Type: application/json; charset=utf-8
Date: Wed, 30 Jun 2021 07:36:14 GMT
Server: kong/2.4.1
X-Kong-Response-Latency: 11

{
    "routes": {
        "example-route": {
            "created_at": 1625038553,
            "https_redirect_status_code": 426,
            "id": "7c71aec7-2e8e-4d14-84ff-6edd4b69907b",
            "name": "example-route",
            "path_handling": "v0",
            "paths": [
                "/echo"
            ],
            "preserve_host": false,
            "protocols": [
                "http",
                "https"
            ],
            "regex_priority": 0,
            "request_buffering": true,
            "response_buffering": true,
            "service": {
                "id": "811d04e2-965d-40c1-b875-adda27530233"
            },
            "strip_path": true,
            "updated_at": 1625038553
        }
    },
    "services": {
        "example-service": {
            "connect_timeout": 60000,
            "created_at": 1625038546,
            "host": "httpbin.org",
            "id": "811d04e2-965d-40c1-b875-adda27530233",
            "name": "example-service",
            "port": 80,
            "protocol": "http",
            "read_timeout": 60000,
            "retries": 5,
            "updated_at": 1625038546,
            "write_timeout": 60000
        }
    }
}

```

# Clean up

シェルから抜けます。

```shell
exit
```

Pongoを停止します。

```shell
pongo down
```
