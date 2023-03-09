[译] Hanami 2.0 初探

*2022年 12月6日，Hanami 2.0.1已经发布。[了解更多关于Hanami 2.0.1 的信息](https://hanamirb.org/blog/2022/12/06/hanami-201/).*

Hanami 2于11月22日发布，结束了该版本四年的工作。它为 Ruby 的 web 开发社区带来了新鲜空气。2.0 不仅仅是一个增量升级。有人可能会说，这是一个重新编写的项目，从版本1，重建在一个坚实的dry.rb 库生态系统之上的聪明想法。

由于版本1.3没有明确的升级路径，在本文中，我们将重点关注该版本的一些关键方面，而不是比较两个版本。

我们将仔细研究以下内容：

- Slices（切片）

- 依赖管理

- 性能

让我们开始。

## Hanami 2 中的切片

Hanami 用于在不断增长的应用程序的不同上下文之间设置边界的首选解决方案是切片。通过提供单独的名称空间，片可以澄清哪些代码属于哪个区域以及跨越边界的时间。

注意，尽管切片很有用，但它们也是完全可选的。如果你的应用程序很简单，或者你还不知道是什么

切片将会出现，您只需将您的代码放在应用程序目录中的“ main”切片下。

为已经创建了的 Hanami 应用，创建新的切片，可以使用 Hanami 命令行进行生成：

```shell
> hanami generate slice accounts
Updated config/routes.rb
Created slices/accounts/
Created slices/accounts/action.rb
Created slices/accounts/actions/.keep
```

这样就产生了一个非常简单的结构，位于切片子目录下，带有一个 Account: : Action 超类和一个将创建帐户管理操作的目录。按照约定，这些类将从 Account: : Action 继承。通过这样做，它们获得特定于应用程序用户 user 区域的行为。

你也许好奇，"action" 到底是什么。它对应的是 Hanami 组织 Web 的一个端点。在Rails 中，”action“ 是在控制器类中进行组织的（明显的通过公共方法）。然而，在 Hanami，每个 ”action“ 只能创建一个类。因此，一个动作是一个独立的代码单元，可以单独测试，并且非常清楚地说明它做了什么。

让我们看一个 action 实例类：

```ruby
class Accounts::Actions::UpdatePassword < Accounts::Action
  include Accounts::Deps["commands.change_password"]
  before :require_authenticated

  params do
    required(:current_password).filled(:str?)
    required(:password).filled(:str?, min_size?: 8)
  end

  def handle(req, res)
    if !req.params.valid?
      res.status = 422
      res.body = req.params.errors.to_h.join(", ")
    elsif change_password.call(
        req.session[:current_user],
        req.params[:current_password],
        req.params[:password])
      res.status = 201
      res.body = "password updated"
    else
      res.status = 422
      res.body = "cannot update password"
    end
  end
end
```



这里汇聚了一些信息：



- 行为依赖 `change_password` 命令

- 它需要一个已经验证过的用户上下文

- 它需要传递两个参数(其中一个必须至少有8个字符长)

- 最后，我们有一个包含端点核心逻辑的 handle 方法: 检查参数的有效性、调用依赖项和设置适当的响应



您可能想知道这个语法是什么意思：



```
include Accounts::Deps["commands.change_password"]
```

这就把我们带到了关于依赖关系的下一节。



## 依赖



`Accounts::Deps` 是一个用于帐户切片的依赖混合第一类物件，Hanami 使不同类之间的依赖关系成为一种依赖关系。



与在代码中隐藏依赖关系不同，您可以(并且应该，如果您想要遵循 Hanami 哲学)在类的顶部定义它们。它不仅澄清了您所依赖的内容，而且还使类更容易测试。



在上面的示例中，“ change _ password”命令定义在存储区/帐户/命令/change _ password 中。命令: : ChangePassword 类。依赖项 Mixin 将使它的一个实例作为 change _ password 在您使用它的类中可用。这都是基于`dry-system`。



在测试中，您可以使用内置的存根方法轻松地存根依赖项：



```
Accounts::Container.stub("commands.change_password", FakePasswordChanger.new)
```



例如，如果您的原始命令使用了一个缓慢设计的哈希算法(比如 bcrypt) ，那么您可以在测试中将其与一些更快的算法进行交换。
