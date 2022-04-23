# Security General 安全

## OAuth 2.0

```text
// OAuth 大致流程，以 facebook 为例

1. User 在APP中想访问 自己 facebook的照片，App 会请求 facebook的 Auth Server 准备进行验证用户身份；
2. User 输入用户名密码进行登录；
  1. Client App 将用户登录信息请求到 Auth Server，Auth Server验证通过后发给 Client App Access Token;
  2. Client App 将 Access Token 发送给 Resource Server;
    1. Resource Server会向 Auth Server 验证token是否有效；
    2. 验证通过后会回复 Client App 它请求的资源；
  3. Client App 展示照片资源；
3. User 查看照片；
```

- 在 OAuth 流程中，User 是 Resource Owner, Auth Server 是认证服务器，Resource Server是资源服务器；
- 默认是使用 OAuth default token，不过可以更换成常见的 JWT token；

## JWT Token

```text
JWT的结构 Header.Claims.Signature
```

- Header 包含类型，算法；
- Claims 包含更多自定义的信息，比如 username，role，expire，etc. ；
- Signature 签名，是使用 私钥对 header&claims 进行的加密签名；
