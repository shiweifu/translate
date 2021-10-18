翻译自：[V1.3: Forms | Hanami Guides (hanamirb.org)](https://guides.hanamirb.org/v1.3/helpers/forms/)



表单帮助方法



#### 特性



表单帮助方法，提供了一组给力的 Ruby 接口，用于帮助我们定义 HTML5 表单，同时可在视图和模板中使用。支持以下特性：



- 无需复杂标记
- 自动 HTML5 标签闭合
- 视图局部变量
- HTTP 方法覆盖（因为 PUT/PATCH/DELETE 等 HTTP 动词，浏览器并不能理解）
- 为 input 控件，自动生成相应的 HTML 属性，比如：`id`，`name`，`value` 等
- 覆盖自动生成的 HTML 属性
- 从请求参数和/或给定实体中读取值，以自动填充值属性
- 自动选择当前值的单选按钮和选择输入
- CSRF 保护
- 无限嵌套字段
- 不可知ORM



### 技术笔记



这个特性的语法与其他具有相同目的的Ruby gems相似，但是与Rails或Padrino相比，它有不同的用法。



那些框架的语法如下：

```erb
<%= form_for :book do |f| %>
  <div>
    <%= f.text_field :title %>
  </div>
<% end %>
```



上面的代码不是一个有效的ERB模板。为了使其工作，这些框架使用自定义ERB处理程序，并依赖第三方gems来实现其他模板引擎。



### 模板引擎独立



因为我们支持大量的模板引擎，所以我们希望保持简单:使用ERB已经提供的。这意味着我们可以使用Slim、HAML或ERB，同时保持相同的Ruby语法。



#### 一个输出块



上述原则的技术折衷是在唯一的输出块中使用表单构建器。



```erb
<%=
  form_for :book, routes.books_path do
    text_field :title

    submit 'Create'
  end
%>
```



这将输出



```html
<form action="/books" id="book-form" method="POST">
  <input type="hidden" name="_csrf_token" value="0a800d6a8fc3c24e7eca319186beb287689a91c2a719f1cbb411f721cacd79d4">
  <input type="text" name="book[title]" id="book-id" value="">
  <button type="submit">Create</button>
</form>
```





