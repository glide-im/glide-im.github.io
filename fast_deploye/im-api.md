# IM服务 + HTTP API 服务

这是最简单的部署方式, 需要部署 WebSocket 长连接消息服务用于聊天, 和走 HTTP 用于客户端 CURD 的 API 两个服务. IM 长连接服务称为 
**IM-Service**, HTTP API 称作 **HTTP-API**.

IM-Service 不依赖其他服务, 而 HTTP-API 依赖 IM-Service 提供的接口, 例如断开用户链接, 添加好友时给指定用户推送消息, HTTP-API 与 IM-Service 之间
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

## 调试

程序入口在 `./cmd` 下
