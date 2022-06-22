# IM服务 + HTTP API 服务

这是最简单的部署方式, 需要部署 WebSocket 长连接消息服务用于聊天, 和走 HTTP 用于客户端 CURD 的 API 两个服务. IM-Service 不依赖其他服务, 而 API 依赖 IM-Service 提供的接口, 例如断开用户链接, 添加好友时给指定用户推送消息, HTTP-API 与 IM-Service 之间
通过 TCP-RPC 的方式通信, 需要先部署 IM-Service 再启动 HTTP-API.

IM-Service 仓库地址: (glide-im/im-service)[https://github.com/glide-im/im-service]
HTTP-API 仓库地址: (glide-im/api)[https://github.com/glide-im/api]

## 运行

现在各自的仓库下载最新的 release, 解压后同目录下 config.toml 为各自的配置文件.

注意: 两个服务中的 JwtSecret 配置必须保持一致, 否则 WebSocket 无法认证,  HTTP-API 配置的 IMRpcService.Name 必须和 IM-Service 中的 IMRpcServer.Name 一致, 否则 HTTP-API 无法访问 IM-Service.

按自己的环境配置好以后, 执行运行可执行文件即可启动服务, 启动时自动加载同目录下 `config.toml` 文件

启动 IM-Service
```shell
./im-service
```

启动 HTTP-API
```shell
./api
```

## nginx 配置

1. 配置 websocket 服务器, 根据实际情况修改端口号, 主机名, 路径等.
```
server {
    listen 80;
    server_name *;

    location / {
        # 允许跨域访问
        if ($request_method = 'OPTIONS') {
                add_header 'Access-Control-Allow-Origin' $http_origin;
                add_header 'Access-Control-Allow-Credentials' 'true';
                add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS';
                add_header 'Access-Control-Allow-Headers' 'DNT,web-token,app-token,Authorization,Accept,Origin,Keep-Alive,User-Agent,X-Mx-ReqToken,X-Data-Type,X-Auth-Token,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Range';
                add_header 'Access-Control-Expose-Headers' 'Content-Length,Content-Range';

                add_header 'Access-Control-Max-Age' 1728000;
                add_header 'Content-Type' 'text/plain; charset=utf-8';
                add_header 'Content-Length' 0;
                return 200;
        }
        proxy_http_version 1.1;
        proxy_connect_timeout 4s;
        proxy_read_timeout 60s;
        proxy_send_timeout 12s;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_pass http://127.0.0.1:8001;
    }
}
```

2. 配置 HTTP api 服务器, server_name 和端口请替换为自己的端口

```
server {
    listen 80;
    server_name *;

    location / {
        proxy_pass http://localhost:8001;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
} 
```

## 本地调试

程序入口在 `./cmd` 下, 在 `./config` 下配置好配置文件, 启动程序即可.
