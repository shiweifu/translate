使用你自己的ORM



Hanami 组件是相互解耦的。这种层级划分方式，允许您使用您所选择的ORM（数据层）。



下面是你要做的步骤：

1. 编辑 `Gemfile`：
   - 删除 `hanami-model`
   - 增加你要使用的 ORM 相应的 gem
2. 执行 `bundle install`
3. 删除不再需要的文件夹：
   - 删除 `lib/project_name/entities/` 和 `lib/projectname/repositories/`
   - 删除 `spec/project_name/entities/` 和 `spec/project_name/repositories/`
4. 编辑 `config/environment.rb`：
   - 删除 `require 'hanami/model'`
   - 删除 `require_relative '../lib/projectname'`
   - 删除 `model` block in `Hanami.configure`
5. 编辑 `Rakefile`：
   - 删除 `require 'hanami/rake_tasks'`

通常来说，`lib/project_name` 是一个存放跨 app 之间代码的好地方，因此，我们不建议完全删除它。那也是 `Hanami` 邮件服务的存放地点。我们建议，您将新的 ORM 代码放入该文件夹，但这并不强制，您可以随意将其放到你想放的地方，并完全摆脱`lib/` 的依赖（如果你愿意）。



需要注意的是，如果在项目中，删除了 `hanami-model`，像 `database commands` 和 `migrations` 这类数据库相关的命令，将不再可用。