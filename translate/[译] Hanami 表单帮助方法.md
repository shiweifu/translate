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