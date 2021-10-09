Hanami Model



Hanami 中的 model 层，根据行为不同，被拆分为 entities （实体层）和持久层（repositories 和 数据库）。这种设计有助于保持我们对象对外暴露的接口较小，从而保持快速和可复用。



#### 基本使用



我们使用 PostgreSQL 数据库 来介绍基本使用。



第一步，我们生成模型：



```
$ bundle exec hanami generate model book
      create  lib/bookshelf/entities/book.rb
      create  lib/bookshelf/repositories/book_repository.rb
      create  db/migrations/20170406230335_create_books.rb
      create  spec/bookshelf/entities/book_spec.rb
      create  spec/bookshelf/repositories/book_repository_spec.rb
```



生成器返回一个 entity，一个 repository，一个合并文件，以及一个包含相关内容的测试文件。



#### Entity



刚刚生成的 entity：

```
class Book < Hanami::Entity
end
```



我们来编辑合并文件，使其的代码如下：



```
Hanami::Model.migration do
  change do
    create_table :books do
      primary_key :id
      column :title,      String
      column :created_at, DateTime
      column :updated_at, DateTime
    end
  end
end
```

