---
title: Authentication
description: 
published: true
date: 2024-02-17T13:44:10.773Z
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
1. 占用服务器内存资源，用户量很大时，内容消耗不起
2. 正常情况下，session在不同的服务器进程中无法共享，比如A 网站和 B 网站是同一家公司的关联服务。现在要求，用户只要在其中一个网站登录，再访问另一个网站就会自动登录。如果要实现，这要求每个进程都能共享session（可以通过session持久化的方式解决）
## Token认证
![5090b39b41454a7a88251509559b4436~tplv-k3u1fbpfcp-zoom-in-crop-mark_1512_0_0_0.webp](/5090b39b41454a7a88251509559b4436~tplv-k3u1fbpfcp-zoom-in-crop-mark_1512_0_0_0.webp)
步骤分解

浏览器首次登录，服务器收到请求后去数据库校验用户名及密码
校验通过后，从数据库查询对应的用户信息
服务器将用户信息通过密钥生成令牌（Access Token），并返回给浏览器
浏览器拿到令牌之后，可以保存到cookie或者localStorage、sessionStorage中
浏览器下次请求接口，将Access Token放到请求头header中
服务端拿到请求头的token，然后通过密钥解密，校验通过后执行后续操作。反之，权限校验失败
