翻译自：[How to upgrade Rails 6.1 to Rails 7 - DEV Community](https://dev.to/thomasvanholder/how-to-upgrade-rails-61-to-rails-7-33a3)





如何将现有应用程序升级到Rails 7.请注意，Rails 7需要Ruby 2.7.0或更新。



## 1. 升级 gemfile 中的 Rails 依赖

```
# old
gem "rails", "~> 6.1.4"

# new
gem "rails", "~> 7.0.0"
```



在终端中，执行：

```
bundle update
```



------

## 2. 更新 Rails 的前端依赖

在 **package.json** 文件中，找到所有 @rails 包，一个一个升级到新版本。如下所示：

```
yarn upgrade @rails/actioncable --latest
yarn upgrade @rails/activestorage --latest
yarn upgrade @rails/request.js --latest
```



------

## 3. 增加 sprockets 到 gemfile 文件中

当你使用 asset pipeline 时，执行 Rails 命令的时候，assets 的依赖错误会被显示出来。举个例子：

```
# - Don't know how to build task 'assets:precompile'
# - 'method_missing': undefined method `assets'
```



增加 Sprockets 到 gemfile 中，当前 Sprockets 是可选安装的。[optional depedency](https://edgeguides.rubyonrails.org/upgrading_ruby_on_rails.html#sprockets-is-now-an-optional-dependency).

```
gem "sprockets-rails"
```



然后执行：

```
bundle install
```



#### 4. 运行 rails task



```
bin/rails app:update
```



这将提示你一个文件一个文件地集成新的rails 7默认值。



例子：

```
Overwrite .../config/boot.rb? (enter "h" for help) [Ynaqdhm]
```



注意:按h查看菜单选项。



检查每个文件，检查rails 7的默认设置和当前配置之间的差异。



当选择merge (m)时，你可以在编辑器中打开文件进行比较。如果你遇到以下错误:Please specify merge tool to 'THOR merge ' env在指定编辑器的情况下，再次运行app:update命令。



例子：



```
THOR_MERGE=code bin/rails app:update
```



安装完成后运行rails db:migrate迁移新添加的活动存储迁移文件。如果您设置了其他环境，非默认环境，如登台。Rb也要确保更新那个文件。



❗️如果您已经设置了其他环境，则非默认环境，如staging.rb，请务必更新该文件。



#### 验证默认框架设置



完成执行 `bin/rails app:update` 后：从步骤4更新，Rails在Config / InitialIzers中创建一个new_framework_defaults_7.0.rb文件。此文件通过在多个部署中逐一翻转新的默认设置，帮助您更轻松地进行大升级。



application.rb文件的设置load_defaults指定哪个Rails版本的默认设置加载。这意味着您可以在运行Rails 7时暂时使用Rails 6.1中的默认配置设置。



```
# Initialize configuration defaults for originally generated Rails version.
config.load_defaults 6.1
```



然后，逐渐打开每个设置，并验证您的应用程序仍然按照预期工作。一旦你打开了所有的设置，你就可以删除新的框架默认7.0。Rb，并在应用程序中翻转加载默认版本。rb至7.0。



```
# Initialize configuration defaults for originally generated Rails version.
config.load_defaults 7.0
```



#### 验证已经废弃的方法



您的应用程序可能会使用其他被贬低的特性。通读Rails 6.1到Rails 7小节，以识别和修复不再支持的任何方法调用或设置。





