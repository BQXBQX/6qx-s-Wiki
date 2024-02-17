---
title: Authentication
description: 
published: true
date: 2024-02-17T14:04:30.191Z
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

## 详细介绍一下 `jwt`
![bg2018072304.jpg](/bg2018072304.jpg)
它是一个很长的字符串，中间用点（.）分隔成三个部分。注意，JWT 内部是没有换行的，这里只是为了便于展示，将它写成了几行。
JWT 的三个部分依次如下。
```
Header（头部）
Payload（负载）
Signature（签名）
```
写成一行，就是下面的样子。
```
Header.Payload.Signature
```
![bg2018072303.jpg](/bg2018072303.jpg)
### Header
Header 部分是一个 JSON 对象，描述 JWT 的元数据，通常是下面的样子。
```
{
  "alg": "HS256",
  "typ": "JWT"
}
```
上面代码中，alg属性表示签名的算法（algorithm），默认是 HMAC SHA256（写成 HS256）；typ属性表示这个令牌（token）的类型（type），JWT 令牌统一写为JWT。

最后，将上面的 JSON 对象使用 Base64URL 算法（详见后文）转成字符串。
### Payload
Payload 部分也是一个 JSON 对象，用来存放实际需要传递的数据。JWT 规定了7个官方字段，供选用。
```
iss (issuer)：签发人
exp (expiration time)：过期时间
sub (subject)：主题
aud (audience)：受众
nbf (Not Before)：生效时间
iat (Issued At)：签发时间
jti (JWT ID)：编号
```
除了官方字段，你还可以在这个部分定义私有字段，下面就是一个例子。
```
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true
}
```
注意，JWT 默认是不加密的，任何人都可以读到，所以不要把秘密信息放在这个部分。
这个 JSON 对象也要使用 Base64URL 算法转成字符串。
### Signature
Signature 部分是对前两部分的签名，防止数据篡改。

首先，需要指定一个密钥（secret）。这个密钥只有服务器才知道，不能泄露给用户。然后，使用 Header 里面指定的签名算法（默认是 HMAC SHA256），按照下面的公式产生签名。
```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
 ```
算出签名以后，把 Header、Payload、Signature 三个部分拼成一个字符串，每个部分之间用"点"（.）分隔，就可以返回给用户。
### Base64URL
前面提到，Header 和 Payload 串型化的算法是 Base64URL。这个算法跟 Base64 算法基本类似，但有一些小的不同。

JWT 作为一个令牌（token），有些场合可能会放到 URL（比如 [api.example.com/?token=xxx]）。Base64 有三个字符+、/和=，在 URL 里面有特殊含义，所以要被替换掉：=被省略、+替换成-，/替换成_ 。这就是 Base64URL 算法。

### JWT 的几个特点
1. JWT 默认是不加密，但也是可以加密的。生成原始 Token 以后，可以用密钥再加密一次。

2. JWT 不加密的情况下，不能将秘密数据写入 JWT。

3. JWT 不仅可以用于认证，也可以用于交换信息。有效使用 JWT，可以降低服务器查询数据库的次数。

4. JWT 的最大缺点是，由于服务器不保存 session 状态，因此无法在使用过程中废止某个 token，或者更改 token 的权限。也就是说，一旦 JWT 签发了，在到期之前就会始终有效，除非服务器部署额外的逻辑。

5. JWT 本身包含了认证信息，一旦泄露，任何人都可以获得该令牌的所有权限。为了减少盗用，JWT 的有效期应该设置得比较短。对于一些比较重要的权限，使用时应该再次对用户进行认证。

6. 为了减少盗用，JWT 不应该使用 HTTP 协议明码传输，要使用 HTTPS 协议传输。
