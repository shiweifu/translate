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
