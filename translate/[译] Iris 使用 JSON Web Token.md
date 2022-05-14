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



Iris 的 JWT 中间件，有两个版本的  [Blocklist](https://github.com/kataras/iris/blob/master/middleware/jwt/blocklist.go)：内存版本和 Redis 版本。



示例代码（请仔细阅读注释）：



```
package main

import (
    "time"

    "github.com/kataras/iris/v12"
    "github.com/kataras/iris/v12/middleware/jwt"
    "github.com/kataras/iris/v12/middleware/jwt/blocklist/redis"

    // Optionally to set token identifier.
    "github.com/google/uuid"
)

var (
    signatureSharedKey = []byte("sercrethatmaycontainch@r32length")

    signer   = jwt.NewSigner(jwt.HS256, signatureSharedKey, 15*time.Minute)
    verifier = jwt.NewVerifier(jwt.HS256, signatureSharedKey)
)

type userClaims struct {
    Username string `json:"username"`
}

func main() {
    app := iris.New()

    // IMPORTANT
    //
    // To use the in-memory blocklist just:
    // verifier.WithDefaultBlocklist()
    // To use a persistence blocklist, e.g. redis,
    // start your redis-server and:
    blocklist := redis.NewBlocklist()
    // To configure single client or a cluster one:
    // blocklist.ClientOptions.Addr = "127.0.0.1:6379"
    // blocklist.ClusterOptions.Addrs = []string{...}
    // To set a prefix for jwt ids:
    // blocklist.Prefix = "myapp-"
    //
    // To manually connect and check its error before continue:
    // err := blocklist.Connect()
    // By default the verifier will try to connect, if failed then it will throw http error.
    //
    // And then register it:
    verifier.Blocklist = blocklist
    verifyMiddleware := verifier.Verify(func() interface{} {
        return new(userClaims)
    })

    app.Get("/", authenticate)

    protectedAPI := app.Party("/protected", verifyMiddleware)
    protectedAPI.Get("/", protected)
    protectedAPI.Get("/logout", logout)

    // http://localhost:8080
    // http://localhost:8080/protected?token=$token
    // http://localhost:8080/logout?token=$token
    // http://localhost:8080/protected?token=$token (401)
    app.Listen(":8080")
}

func authenticate(ctx iris.Context) {
    claims := userClaims{
        Username: "kataras",
    }

    // Generate JWT ID.
    random, err := uuid.NewRandom()
    if err != nil {
        ctx.StopWithError(iris.StatusInternalServerError, err)
        return
    }
    id := random.String()

    // Set the ID with the jwt.ID.
    token, err := signer.Sign(claims, jwt.ID(id))

    if err != nil {
        ctx.StopWithError(iris.StatusInternalServerError, err)
        return
    }

    ctx.Write(token)
}

func protected(ctx iris.Context) {
    claims := jwt.Get(ctx).(*userClaims)

    // To the standard claims, e.g. the generated ID:
    // jwt.GetVerifiedToken(ctx).StandardClaims.ID

    ctx.WriteString(claims.Username)
}

func logout(ctx iris.Context) {
    ctx.Logout()

    ctx.Redirect("/", iris.StatusTemporaryRedirect)
}
```



默认情况下，通过 “jti”（Claims{ID}）检索唯一标识，如果它是空的，那么原始 Token 将用作映射。要改变这种行为，只需要简单的修改 `blocklist.GetKey` 字段即可。



#### JSON Web 算法



根据 JWA 算法规范（JSON Web 算法），有几种类型的签名算法可用。该规范要求所有符合规范的实现，都支持单一算法：



- HMAC 使用 SHA-256，在 JWA 规范中，被称为 `HS256`。

该规范也推荐了一些算法：



- RSASSA PKCS1 v1.5 使用 SHA-256，在 JWA 规范中，被称为 `RS256`
- ECDSA 使用 p-256 和 SHA-256，在 JWA 规范中，被称为 `ES256`



该实现支持以上所有的算法，额外还支持 `RSA-PSS` 和 新的 `Ed25519`，导航到 [alg.go](https://github.com/kataras/iris-book/tree/1f920bd9bec0010ff85d3d1652e53ebf3e8bea05/security/alg.go) 源码文件，查看更多信息。简而言之：



#### 选择正确的算法



最适合你的应用的算法，取决于你的选择，我的建议如下：



- 已经使用了 RSA 公钥和私钥？选择 RSA（RS256/RS384/RS512/PS256/PS384/PS512) (length of produced token characters is bigger）。

- 如果你需要拆分公钥和私钥，选择ECDSA（ES256/ES384/ES512）或者 EdDSA。ECDSA 和 EdDSA 产生令牌比 RSA 小一些。
- 如果你需要性能更佳以及更方便测试的算法，选择HMAC（HS256/HS384/HS512） - 最通用的算法。



对称算法和非对称算法之间的基本区别是，对称算法使用一个共享密钥对令牌进行签名和验证，而非对称算法使用私钥进行签名，使用公钥进行验证。一般来说，非对称数据更安全，因为它使用不同的密钥进行签名和验证，但比对称数据慢。



#### 使用你自己的算法



如果你需要使用你自己的 JSON Web 算法，只需要实现  [Alg](https://github.com/kataras/iris-book/tree/1f920bd9bec0010ff85d3d1652e53ebf3e8bea05/security/alg.go#L19-L28) 接口。传递 `jwt.Sign` 和 `jwt.Verify` 函数，然后就可以了。



#### 生成密钥



密钥使用 [OpenSSL](https://www.openssl.org) 进行生成，还有一个选择是使用 Go 的标准库。



```
import (
    "crypto/rand"
    "crypto/rsa"
    "crypto/elliptic"
    "crypto/ed25519"
)
```



```
// Generate HMAC
sharedKey := make([]byte, 32)
_, _ = rand.Read(sharedKey)

// Generate RSA
bitSize := 2048
privateKey, _ := rsa.GenerateKey(rand.Reader, bitSize)
publicKey := &privateKey.PublicKey

// Generace ECDSA
c := elliptic.P256()
privateKey, _ := ecdsa.GenerateKey(c, rand.Reader)
publicKey := &privateKey.PublicKey

// Generate EdDSA
publicKey, privateKey, _ := ed25519.GenerateKey(rand.Reader)
```



#### 载入和解析密钥



这个包包含加载和解析 PEM 格式密钥的全部依赖。



全部帮助方法：



```
// HMAC
MustLoadHMAC(filenameOrRaw string) []byte
```



```
// RSA
MustLoadRSA(privFile, pubFile string) (*rsa.PrivateKey, *rsa.PublicKey)
LoadPrivateKeyRSA(filename string) (*rsa.PrivateKey, error)
LoadPublicKeyRSA(filename string) (*rsa.PublicKey, error) 
ParsePrivateKeyRSA(key []byte) (*rsa.PrivateKey, error)
ParsePublicKeyRSA(key []byte) (*rsa.PublicKey, error)
```



```
// ECDSA
MustLoadECDSA(privFile, pubFile string) (*ecdsa.PrivateKey, *ecdsa.PublicKey)
LoadPrivateKeyECDSA(filename string) (*ecdsa.PrivateKey, error)
LoadPublicKeyECDSA(filename string) (*ecdsa.PublicKey, error) 
ParsePrivateKeyECDSA(key []byte) (*ecdsa.PrivateKey, error)
ParsePublicKeyECDSA(key []byte) (*ecdsa.PublicKey, error)
```



```
// EdDSA
MustLoadEdDSA(privFile, pubFile string) (ed25519.PrivateKey, ed25519.PublicKey)
LoadPrivateKeyEdDSA(filename string) (ed25519.PrivateKey, error)
LoadPublicKeyEdDSA(filename string) (ed25519.PublicKey, error)
ParsePrivateKeyEdDSA(key []byte) (ed25519.PrivateKey, error)
ParsePublicKeyEdDSA(key []byte) (ed25519.PublicKey, error)
```



示例代码：

```
import "github.com/kataras/iris/v12/middleware/jwt"
```

```
privateKey, publicKey := jwt.MustLoadEdDSA("./private_key.pem", "./public_key.pem")

signer := jwt.NewSigner(jwt.EdDSA, privateKey, 15*time.Minute)
verifier := jwt.NewVerifier(jwt.EdDSA, publicKey)
```



#### 刷新 Token



访问 token 用于识别用户，而无需访问数据。



刷新 Token 用于在 `access token` 过期之后，延长 token 的可访问时间。在 token 过期之后，生成新的访问和刷新令牌。



示例代码：



```
package main

import (
    "fmt"
    "time"

    "github.com/kataras/iris/v12"
    "github.com/kataras/iris/v12/middleware/jwt"
)

const (
    accessTokenMaxAge  = 10 * time.Minute
    refreshTokenMaxAge = time.Hour
)

var (
    privateKey, publicKey = jwt.MustLoadRSA("rsa_private_key.pem", "rsa_public_key.pem")

    signer   = jwt.NewSigner(jwt.RS256, privateKey, accessTokenMaxAge)
    verifier = jwt.NewVerifier(jwt.RS256, publicKey)
)

// UserClaims a custom access claims structure.
type UserClaims struct {
    ID string `json:"user_id"`
    // Do: `json:"username,required"` to have this field required
    // or see the Validate method below instead.
    Username string `json:"username"`
}

// GetID implements the partial context user's ID interface.
// Note that if claims were a map then the claims value converted to UserClaims
// and no need to implement any method.
//
// This is useful when multiple auth methods are used (e.g. basic auth, jwt)
// but they all share a couple of methods.
func (u *UserClaims) GetID() string {
    return u.ID
}

// GetUsername implements the partial context user's Username interface.
func (u *UserClaims) GetUsername() string {
    return u.Username
}

// Validate completes the middleware's custom ClaimsValidator.
// It will not accept a token which its claims missing the username field
// (useful to not accept refresh tokens generated by the same algorithm).
func (u *UserClaims) Validate() error {
    if u.Username == "" {
        return fmt.Errorf("username field is missing")
    }

    return nil
}

// For refresh token, we will just use the jwt.Claims
// structure which contains the standard JWT fields.

func main() {
    app := iris.New()
    app.OnErrorCode(iris.StatusUnauthorized, handleUnauthorized)

    app.Get("/authenticate", generateTokenPair)
    app.Get("/refresh", refreshToken)

    protectedAPI := app.Party("/protected")
    {
        verifyMiddleware := verifier.Verify(func() interface{} {
            return new(UserClaims)
        })

        protectedAPI.Use(verifyMiddleware)

        protectedAPI.Get("/", func(ctx iris.Context) {
            // Access the claims through: jwt.Get:
            // claims := jwt.Get(ctx).(*UserClaims)
            // ctx.Writef("Username: %s\n", claims.Username)
            //
            // OR through context's user (if at least one method was implement by our UserClaims):
            user := ctx.User()
            id, _ := user.GetID()
            username, _ := user.GetUsername()
            ctx.Writef("ID: %s\nUsername: %s\n", id, username)
        })
    }

    // http://localhost:8080/protected (401)
    // http://localhost:8080/authenticate (200) (response JSON {access_token, refresh_token})
    // http://localhost:8080/protected?token={access_token} (200)
    // http://localhost:8080/protected?token={refresh_token} (401)
    // http://localhost:8080/refresh?refresh_token={refresh_token}
    // OR http://localhost:8080/refresh (request JSON{refresh_token = {refresh_token}}) (200) (response JSON {access_token, refresh_token})
    // http://localhost:8080/refresh?refresh_token={access_token} (401)
    app.Listen(":8080")
}

func generateTokenPair(ctx iris.Context) {
    // Simulate a user...
    userID := "53afcf05-38a3-43c3-82af-8bbbe0e4a149"

    // Map the current user with the refresh token,
    // so we make sure, on refresh route, that this refresh token owns
    // to that user before re-generate.
    refreshClaims := jwt.Claims{Subject: userID}

    accessClaims := UserClaims{
        ID:       userID,
        Username: "kataras",
    }

    // Generates a Token Pair, long-live for refresh tokens, e.g. 1 hour.
    // First argument is the access claims,
    // second argument is the refresh claims,
    // third argument is the refresh max age.
    tokenPair, err := signer.NewTokenPair(accessClaims, refreshClaims, refreshTokenMaxAge)
    if err != nil {
        ctx.Application().Logger().Errorf("token pair: %v", err)
        ctx.StopWithStatus(iris.StatusInternalServerError)
        return
    }

    // Send the generated token pair to the client.
    // The tokenPair looks like: {"access_token": $token, "refresh_token": $token}
    ctx.JSON(tokenPair)
}

// There are various methods of refresh token, depending on the application requirements.
// In this example we will accept a refresh token only, we will verify only a refresh token
// and we re-generate a whole new pair. An alternative would be to accept a token pair
// of both access and refresh tokens, verify the refresh, verify the access with a Leeway time
// and check if its going to expire soon, then generate a single access token.
func refreshToken(ctx iris.Context) {
    // Assuming you have access to the current user, e.g. sessions.
    //
    // Simulate a database call against our jwt subject
    // to make sure that this refresh token is a pair generated by this user.
    // * Note: You can remove the ExpectSubject and do this validation later on by yourself.
    currentUserID := "53afcf05-38a3-43c3-82af-8bbbe0e4a149"

    // Get the refresh token from ?refresh_token=$token OR
    // the request body's JSON{"refresh_token": "$token"}.
    refreshToken := []byte(ctx.URLParam("refresh_token"))
    if len(refreshToken) == 0 {
        // You can read the whole body with ctx.GetBody/ReadBody too.
        var tokenPair jwt.TokenPair
        if err := ctx.ReadJSON(&tokenPair); err != nil {
            ctx.StopWithError(iris.StatusBadRequest, err)
            return
        }

        refreshToken = tokenPair.RefreshToken
    }

    // Verify the refresh token, which its subject MUST match the "currentUserID".
    _, err := verifier.VerifyToken(refreshToken, jwt.Expected{Subject: currentUserID})
    if err != nil {
        ctx.Application().Logger().Errorf("verify refresh token: %v", err)
        ctx.StatusCode(iris.StatusUnauthorized)
        return
    }

    /* Custom validation checks can be performed after Verify calls too:
    currentUserID := "53afcf05-38a3-43c3-82af-8bbbe0e4a149"
    userID := verifiedToken.StandardClaims.Subject
    if userID != currentUserID {
        ctx.StopWithStatus(iris.StatusUnauthorized)
        return
    }
    */

    // All OK, re-generate the new pair and send to client,
    // we could only generate an access token as well.
    generateTokenPair(ctx)
}

func handleUnauthorized(ctx iris.Context) {
    if err := ctx.GetErr(); err != nil {
        ctx.Application().Logger().Errorf("unauthorized: %v", err)
    }

    ctx.WriteString("Unauthorized")
}
```

