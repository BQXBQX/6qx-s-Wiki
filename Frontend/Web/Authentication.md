---
title: Authentication
description: 
published: true
date: 2024-02-17T13:53:29.634Z
tags: web, authentication, frontend
editor: markdown
dateCreated: 2024-02-17T13:44:10.773Z
---

# Authentication
## 什么是鉴权
鉴权也叫身份认证，指验证用户是否有系统的访问权限
## Session-Cookie 认证
利用服务端的 `session`（会话）和浏览器（客户端）的 `cookie` 来实现的前后端通信认证模式。
![c205db7e23e649249e70d751a7cc73dc~tplv-k3u1fbpfcp-zoom-in-crop-mark_1512_0_0_0.webp](/c205db7e23e649249e70d751a7cc73dc~tplv-k3u1fbpfcp-zoom-in-crop-mark_1512_0_0_0.webp)
### 步骤分解
1. 浏览器首次登录，向服务器发送`username`和`password`，服务器收到请求后去数据库校验用户名及密码，
	校验通过后，从数据库查询对应的用户信息。
2. 通过后，在服务器进程中创建session，并且把用户信息保存起来。
3. 设置响应体（Set-Cookie），把`sessionId`（一般是`userId`，因为`userId`比直接暴露`username`更安全）返回。
4. 浏览器下次请求接口，自动携带`cookie`信息在请求体中，其中包含了`sessionId`。
5. 服务器拿到请求体中的`sessionId`，根据先前创建的`session`判断`sessionId`对应的是哪个用户。
6. 查询到对应用户后，执行后续操作。反之，权限校验失败。
### 优点
1. 安全性较高，客户端拿到的只是`sessionId`，没有具体的用户信息。
2. 用户信息保存在服务器进程中，减少了查询数据库的次数。
3. 可以设置`session`的过期时间，不受`cookie`过期时间的影响。
4. 存储的数据类型不受限制。
### 缺点
1. 占用服务器内存资源，用户量很大时，内容消耗不起。
2. 正常情况下，`session`在不同的服务器进程中无法共享，比如A 网站和 B 网站是同一家公司的关联服务。现在要求，用户只要在其中一个网站登录，再访问另一个网站就会自动登录。如果要实现，这要求每个进程都能共享`session`（可以通过`session`持久化的方式解决）。
## Token认证
![5090b39b41454a7a88251509559b4436~tplv-k3u1fbpfcp-zoom-in-crop-mark_1512_0_0_0.webp](/5090b39b41454a7a88251509559b4436~tplv-k3u1fbpfcp-zoom-in-crop-mark_1512_0_0_0.webp)
### 步骤分解
1. 浏览器首次登录，服务器收到请求后去数据库校验用户名及密码。
2. 校验通过后，从数据库查询对应的用户信息。
3. 服务器将用户信息通过密钥生成令牌（Access Token），并返回给浏览器。
4. 浏览器拿到令牌之后，可以保存到`cookie`或者`localStorage`、`sessionStorage`中。
5. 浏览器下次请求接口，将Access Token放到请求头`header`中。
6. 服务端拿到请求头的token，然后通过密钥解密，校验通过后执行后续操作。反之，权限校验失败。
### 优点
1. 无状态可以减轻服务器压力，减少频繁查询数据库。
2. 没有同源策略的限制，方便第三方平台或者开发时的接口调用。
3. 安全性较高，`token`的解密密钥只有服务端知道，即使客户端暴露出来，别人也无法解密。
### 缺点
1. token过期时间较短，往往需要配合refresh token一起使用，refresh token是在access token过期时用来重新获取token的。
2. refresh token也有过期时间，且一般存储在数据库中，虽然不需要向 `session`一样一直保持在内存中以应对大量的请求，但也会增加一定次数的数据库查询。
## JWT认证
![2b77f1047a6a49c28113601ba14db6cc~tplv-k3u1fbpfcp-zoom-in-crop-mark_1512_0_0_0.webp](/2b77f1047a6a49c28113601ba14db6cc~tplv-k3u1fbpfcp-zoom-in-crop-mark_1512_0_0_0.webp)
### 步骤分解
1. 浏览器首次登录，服务器收到请求后去数据库校验用户名及密码。
2. 校验通过后，从数据库查询对应的用户信息。
3. 服务器将用户信息生成`jwt`，并返回给客户端。
4. 浏览器拿到`jwt`之后，可以保存到cookie或者localStorage、sessionStorage中。
5. 浏览器下次请求接口，将`jwt`放到请求头header中（默认格式Authorization: Bearer jwt）。
6. 服务端检查`jwt`的签名信息，从`jwt`中获取用户信息，并执行后续操作。反之，权限校验失败。

### 细节点
`jwt`默认是不加密的，本质是由（Header.Payload.Signature）组成。如需加密，可以和access token一样使用密钥加密。
### 优点
1. 安全性高，可以说是目前登录鉴权最优的方案，大部分公司都采用该方案。
2. 解决服务器压力，减少服务器查询。
3. 服务端也是无状态的。
4. 不需要查询数据库，即可获取用户信息，因为jwt包含了用户信息和加密的数据。

### 缺点
`jwt`不加密的情况下，不能将秘密数据写入`jwt`
### 总结:`jwt`和`token`的比较：
#### 相同点：
1. 都是访问资源的令牌。
2. 都可以记录用户的信息。
3. 都是使服务端无状态化。

### 不同点：
`token`需要验证是否过期，正常情况下，需要配合refresh token一起使用，相比于`jwt`比较麻烦。而且token一般是需要存储在`redis`或者数据库中，服务器拿到token信息需要去校验真伪，所以jwt的优势就出来了。