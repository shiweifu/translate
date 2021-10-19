翻译自：[V1.3: Routing | Hanami Guides (hanamirb.org)](https://guides.hanamirb.org/v1.3/helpers/routing/)



### 路由帮助方法



路由帮助方法，由一个公共方法（#routes）组成，在 actions，views，以及 templates 中，都可以使用。他通过工厂函数，生成具名路由相对地址和绝对路径。



> 比如给定一个路由的名字为 `:home`，我们可以使用 `home_path` 或者 `home_url` 来访问他的相对路径和绝对地址。



### 使用



假设我们的应用，由下面的路由定义：



```
# apps/web/config/routes.rb
root        to: 'home#index'
get '/foo', to: 'foo#index'

resources :books
```



### 相关 URLs



我们可以这样使用：

```
<ul>
  <li><a href="<%= routes.root_path %>">Home</a></li>
  <li><a href="<%= routes.books_path %>">Books</a></li>
</ul>
```



这将生成：



```
<ul>
  <li><a href="/">Home</a></li>
  <li><a href="/books">Books</a></li>
</ul>
```



我们不能连接到 /foo，因为它不是一个命名路由（缺少 :as 选项）。













