Cuba 是一个基于 Rack 开发的 Ruby Web 微框架。它有以下部分：

- 独特的路由定义系统
- 插件系统
- 模板渲染
- 一些安全的特性支持

这些特性也都是一些微框架基本的特性，几乎所有的 Ruby Web 框架，都是基于 Rack 进行开发，区别就在于这些特性的实现和使用体验。

Cuba 的核心代码，只有不到 1000 行，其中 “魔法” 也不多，很适合学习。

## Rack 应用

Cuba 的主要代码，都是在外层的 `cuba.rb` 文件中。主要定义了两个类：

### Cuba

Cuba 是一个 Rack app 对象，在其中定义了 `Cuba#call` 成员方法。

### Cuba::Response
