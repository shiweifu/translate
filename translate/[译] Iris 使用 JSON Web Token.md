翻译自：[JSON Web Tokens - Iris (iris-go.com)](https://docs.iris-go.com/iris/security/jwt)



### 介绍



JSON Web Token（JWT） 是一种紧凑的，URL 安全的手段，用于双方之间的信息传输。JWT 的主体，被编码为 JSON 对象，该对象作用于 JSON Web Signature（JWS）结构或者基于普通文本的 JSON Web Encryption（JWE） 结构，确保传输的数字签名或者加密信息的完整性。



### 何时使用 JSON Web token？



下面是一些 JSON  Web Tokens 的使用场景：



Authorization：权限验证是最常见的 JWT 使用场景。当用户登陆后，每个请求都带有 JWT，允许用户访问被 Token 保护的路由，服务和资源。单点登录通过 JWT，是当今使用非常普遍的一个特性，因为它的开销小，而且可以很容易的跨域使用。

