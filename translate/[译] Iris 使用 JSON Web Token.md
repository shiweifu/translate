翻译自：[JSON Web Tokens - Iris (iris-go.com)](https://docs.iris-go.com/iris/security/jwt)



### 介绍



JSON Web Token（JWT） 是一种紧凑的，URL 安全的手段，用于双方之间的信息传输。JWT 的主体，被编码为 JSON 对象，该对象作用于 JSON Web Signature（JWS）结构或者基于普通文本的 JSON Web Encryption（JWE） 结构，确保传输的数字签名或者加密信息的完整性。



### 何时使用 JSON Web token？



下面是一些 JSON  Web Tokens 的使用场景：



`Authorization`：权限验证是最常见的 JWT 使用场景。当用户登陆后，每个请求都带有 JWT，允许用户访问被 Token 保护的路由，服务和资源。单点登录通过 JWT，是当今使用非常普遍的一个特性，因为它的开销小，而且可以很容易的跨域使用。



`信息交换`：JSON Web tokens 是一种良好的在组件之间传输信息的方式。因为 JWT 可以包含签名 - 举个例子：使用公钥/私钥对 - 你可以确保发送什么就能收到什么。此外，签名是使用标头和 Payload 进行计算，你还可以验证内容尚未篡改。



更多信息，请访问：https://jwt.io/introduction



---



### 在 Iris 中，使用 JWT



Iris JWT 中间件，被设计为安全，性能，以及简化，它保护你的 Token 免受其他库可能发现的漏洞的侵害，它在 https://github.com/kataras/jwt 包中。



> 在此可以找到例子：https://github.com/kataras/iris/blob/master/_examples/auth/jwt



示例代码：



```
package main

import (
    "time"

    "github.com/kataras/iris/v12"
    "github.com/kataras/iris/v12/middleware/jwt"
)

var (
    secret = []byte("signature_hmac_secret_shared_key")
)

type fooClaims struct {
    Foo string `json:"foo"`
}

func main() {
    app := iris.New()

    signer := jwt.NewSigner(jwt.HS256, secret, 10*time.Minute)
    // Enable payload encryption with:
    // signer.WithEncryption(encKey, nil)
    app.Get("/", generateToken(signer))

    verifier := jwt.NewVerifier(jwt.HS256, secret)
    // Enable server-side token block feature (even before its expiration time):
    verifier.WithDefaultBlocklist()
    // Enable payload decryption with:
    // verifier.WithDecryption(encKey, nil)
    verifyMiddleware := verifier.Verify(func() interface{} {
        return new(fooClaims)
    })

    protectedAPI := app.Party("/protected")
    // Register the verify middleware to allow access only to authorized clients.
    protectedAPI.Use(verifyMiddleware)
    // ^ or UseRouter(verifyMiddleware) to disallow unauthorized http error handlers too.

    protectedAPI.Get("/", protected)
    // Invalidate the token through server-side, even if it's not expired yet.
    protectedAPI.Get("/logout", logout)

    app.Listen(":8080")
}

func generateToken(signer *jwt.Signer) iris.Handler {
    return func(ctx iris.Context) {
        claims := fooClaims{Foo: "bar"}

        token, err := signer.Sign(claims)
        if err != nil {
            ctx.StopWithStatus(iris.StatusInternalServerError)
            return
        }

        ctx.Write(token)
    }
}

func protected(ctx iris.Context) {
    // Get the verified and decoded claims.
    claims := jwt.Get(ctx).(*fooClaims)

    // Optionally, get token information if you want to work with them.
    // Just an example on how you can retrieve
    // all the standard claims (set by signer's max age, "exp").
    standardClaims := jwt.GetVerifiedToken(ctx).StandardClaims
    expiresAtString := standardClaims.ExpiresAt().
        Format(ctx.Application().ConfigurationReadOnly().GetTimeFormat())
    timeLeft := standardClaims.Timeleft()

    ctx.Writef("foo=%s\nexpires at: %s\ntime left: %s\n", claims.Foo, expiresAtString, timeLeft)
}

func logout(ctx iris.Context) {
    err := ctx.Logout()
    if err != nil {
        ctx.WriteString(err.Error())
    } else {
        ctx.Writef("token invalidated, a new token is required to access the protected API")
    }
}
```



```
http://localhost:8080
http://localhost:8080/protected?token=$token (or Authorization: Bearer $token)
http://localhost:8080/protected/logout?token=$token
http://localhost:8080/protected?token=$token (401)
```



#### 步骤



中间件包含两个结构，`Signer` 和 `Verifier`。`Signer` 用于生成 Token，`Verifier` 用于验证输入请求的 Token 是否有效。



1. 在代码中，导入相关包：`import "github.com/kataras/iris/v12/middleware/jwt"`
2. 在处理程序外部，初始化 Signer。有多种算法可供选择。让我们继续使用 `HMAC（HS256）`共享密钥，稍后我们将在验证器上使用：

```
signer := jwt.NewSigner(jwt.HS256, []byte("secret"), 15*time.Minute)
```



NewSigner 的参数：



1. 算法签名
2. 所使用的签名私钥
3. 过期时间



3. 声明自定义 `结构体`（可选，你也可以使用 `map`）：



```
// UserClaims a custom claims structure.
// * Fields should be exported in order
// to be able to decoded later on.
type UserClaims struct {
    Username string `json:"username"`
}
```



4. 根据签名，生成 token，发送给客户端：



```
app.Post("/signin", func(ctx iris.Context) {
    claims := UserClaims{
        Username: "kataras",
    }

    token, err := signer.Sign(claims)
    if err != nil {
        ctx.StopWithStatus(iris.StatusInternalServerError)
        return
    }

    ctx.Write(token)
})
```



- 要验证Token，初始化一个 `Verifier` 验证器，在处理函数外面：

```
verifier := jwt.NewVerifier(jwt.HS256, []byte("secret"))
```

- 创建中间件，在处理函数外面，指定一个用于生成 Token 的类型：

```
verifyMiddleware := verifier.Verify(func() interface{} {
    return new(UserClaims) // must return a pointer to the claims struct type.
})
```

- 注册中间件到 Party：

```
protected := app.Party("/protected")
protected.Use(verifyMiddleware)
```

- 或者注册中间件，给单个的路由：

```
app.Get("/todos", verifyMiddleware, getTodos)
```

- 或者在处理函数中，调用中间件（不推荐）

```
handler := func(ctx iris.Context) {
    ok := ctx.Proceed(verifyMiddleware)
    if !ok {
        ctx.StopWithStatus(iris.StatusUnauthorized)
        return
    }

    claims := jwt.Get(ctx).(*UserClaims)
}

app.Get("/protected", handler)
```

- 要在处理路由中，获取解密后的对象，使用 `jwt.Get` 方法：

```
protected.Get("/todos", func(ctx iris.Context) {
    claims := jwt.Get(ctx).(*UserClaims)
    ctx.WriteString(claims.Username)
})
```

- 当你的解密对象类型，实现了 [Context.User](https://github.com/kataras/iris/blob/7b6a8f1e26469ab3ae53cfe468d6e5202c75c2a8/context/context_user.go#L35) 接口的方法，可以直接得到用户：

```
user := ctx.User()
username, _ := user.GetUsername()
```

- 获取被验证的 Token 的信息：

```
verifiedToken := jwt.GetVerifiedToken(ctx)
verifiedToken.Token // the original request token.
```



`VerifiedToken` 看起来是这样的：

```
type VerifiedToken struct {
    Token          []byte // The original token.
    Header         []byte // The header (decoded) part.
    Payload        []byte // The payload (decoded) part.
    Signature      []byte // The signature (decoded) part.
    StandardClaims Claims // Any standard claims extracted from the payload.
}
```



#### Token 提取



默认情况下，一个新的 `Verifier` 可以从 `?token=$token` URL 参数或者请求 `Header` 的 `Authorization: Bearer $token` 中，提取到 Token。要改变这个行为，你可以修改验证器的 `Extractors []TokenExtractor` 字段。



`TokenExtractor` 类型看起来如下：

```
type TokenExtractor func(iris.Context) string
```



该中间件提供了三个 token 提取器帮助方法：



1. `FromHeader` - 从 `Authorization: Bearer {token}` 头提取 Token。
2. `FromQuery` - 提取 `URL 查询参数` 中的 Token 字段内容。
3.  FromJSON(jsonKey string) - 提取 token，从请求内容中，名为 `jsonKey` 的字段进行提取 token，例如，FromJSON("access_token") 将提取 token，从请求的内容：{"access_token": "$TOKEN", ...}。



`Verifier.Extractors` 字段（`NewVerifier` 返回的函数）默认行为：`[]TokenExtractor{FromHeader, FromQuery}`，它尝试从 `Authorization: Bearer` 头，读取 token，如果没有找到，它尝试去查找 `URL` 中的 `token`  查询参数。你可以自定义查找 Token 的行为，举例如下：



```
verifier := jwt.NewVerifier(jwt.HS256, []byte("secret"))
verifier.Extractors = append(verifier.Extractors, func(ctx iris.Context) string {
    // Return the raw token string from the request.
    //
    // For the sake of the example, we will try to
    // retrieve the token, if all previous extractors failed,
    // through a "X-Token" custom header:
    return ctx.GetHeader("X-Token")
})
```



当你想从 Authorization Header 中，展开 token 时：



```
verifier.Extractors = []jwt.TokenExtractor{jwt.FromHeader}
```





#### Token 结构体加密



[JWE](https://tools.ietf.org/html/rfc7516#section-3)（加密后的 JWT）超出了这个中间件的范围，提供了令牌有效负载来保护数据。如图应用需要传输一个持有私有数据的令牌，它需要在 Sign 上加密数据，在 Verify 上解密数据。`Signer.WithEncryption` 和 `Verifier.WithDecryption` 方法可以加密任何类型的数据。



中间件使用最流行和通用的方式，加密数据；`GCM` 模式 + AES cipher。我们使用最多研究推荐的 `encrypt-then-sign`  方式（它可以成功抵御边界攻击）。



```
signer := jwt.NewSigner(jwt.HS256, []byte("secret"), 15*time.Minute)
// The key argument should be the AES key,
// either 16, 24, or 32 bytes to select
// AES-128, AES-192, or AES-256.
//
// The second argument is the additional data, can be nil.
signer.WithEncryption([]byte("itsa16bytesecret"), nil)
```



在验证器中，我们解密加密的内容：



```
verifier := jwt.NewVerifier(jwt.HS256, []byte("secret"))
verifier.WithDecryption([]byte("itsa16bytesecret"), nil)
// [...]
```



#### 组织令牌



当用户登出后，客户端 app 将删除本地的 token。此后，客户端无法再发起带有登录状态的请求。但如果 token 仍然有效，其他人依旧可以使用它。因此，使其在服务端无效，是非常有必要的。当服务端收到登出请求后，从请求中取出令牌，然后将该令牌保存至 `blocklist`。每个请求验证时，调用 `Verifier.Verify` 将检查 `Blocklist`，来检查 token 是否失效。为了保持检查的范围变小，过期的令牌将会自动从 Blocklist 中删除。



