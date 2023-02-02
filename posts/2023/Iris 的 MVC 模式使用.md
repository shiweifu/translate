Iris 框架提供了 MVC 开发模式，因为 Go 语言语法简洁性，该特性在 Go 语言实现的 Web 开发框架中，并不常见。本文介绍该模式在实际开发中，如何使用。

## 前置知识

### 什么是 MVC 模式

MVC 模式是一种常见的开发模型，将程序分为三层：

- Model：数据层
- View：视图层
- Controller：控制器层

控制器层从数据层取出数据，渲染视图层，视图层不直接访问数据层。

MVC 是当前主流的 Web 开发模型，其出现是为了解耦数据，强类聚，弱耦合。

一个 MVC 流程图：

关于 MVC 的更多信息，可以参考维基百科：
https://zh.wikipedia.org/wiki/MVC

### 什么是依赖注入

假设我们创建一个服务实例类，这个类需要依赖其他对象，传统做法，我们可以在这个类中，创建所依赖的对象。这种做法对调用者是无感知的，直接调用就可以了。缺点耦合了，不方便自定义所依赖的对象，不够灵活。



也可以从该类外部，创建所依赖的对象，然后将对象传递给所调用的对象。这种写法被称为依赖注入。



传统写法：

```go
type DBConfig struct {
    dsn string
}

type Server struct {
    dbConfig *DBConfig
}

func NewServer() *server {
    return &Server{DBConfig{}}
}
```



依赖注入的写法：

```go
type DBConfig struct {
    dsn string
}

type Server struct {
    dbConfig *DBConfig
}

func NewServer(cfg *DBConfig) *server {
    if cfg == nil {
        return &Server{DBConfig{}}        
    }
    return &Server{cfg}
}
```



这个例子比较简单，实际应用中，可能会出现多个对象依赖的情况。



在 Iris 中，MVC 的路由和参数绑定，使用了依赖注入的方式：

- 将全局参数绑定在 MVC 对象

- 自动解析并绑定路由处理方法的参数



```go
// 获取某个物品
func (s *StuffController) GetBy(id uint) models.Response {
	if id != 0 {
		return models.NewResponse(nil, models.ResponseCodeSuccess)
	}
	err := fmt.Errorf("id is invalid")
	return models.NewResponse(err, models.ResponseCodeError)
}
```



假设我们这个 MVC 对象，绑定的路由是 `http://localhost/stuffs`，我们定义该方法后，此时直接请求 `http://localhost:/stuffs/1` 即可通过 id 参数，读取到请求的参数，而无需显示绑定路由。



## 上代码

Iris 是通过：github.com/kataras/iris/v12/mvc 代码包来实现 MVC 的，Golang 是编译型语言，接口并不漂亮，但能用。

### 一个最基本的 MVC 实现

### 一个复杂一点的实现

## 自定义路由

### 绑定参数

### 通过依赖注入访问全局变量

参考：

https://github.com/kataras/iris/wiki/dependency-injection

https://github.com/kataras/iris/wiki/MVC
