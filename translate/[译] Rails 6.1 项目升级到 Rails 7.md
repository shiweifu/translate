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

