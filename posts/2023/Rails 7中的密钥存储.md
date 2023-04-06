Rails 7，使用 `Encrypted Secrets` 来进行密钥存储，这是一种安全的方式，机密数据（如云服务的 key 等）均以加密方式进行存储。



在项目创建的时候，项目目录会有一个 `config/master.key` 文件，在产品阶段，你可以通过 `RAILS_MASTER_KEY` 环境变量来进行传递。这个文件十分重要，如果丢失，则加密的信息无法读取，只能重新创建。



### 相关命令



- `rails credentials:edit`：用于编辑 Encrypted Secrets 文件。运行这个命令会打开一个文本编辑器，让你编辑加密文件。如果是第一次运行这个命令，它会自动创建一个新的加密文件，并让你输入一个密码来加密文件。如果已经存在加密文件，它会让你输入密码来打开文件。
- `rails credentials:show`：用于查看 Encrypted Secrets 文件中的内容。运行这个命令会输出文件中的所有内容。
- `Rails.application.credentials`：用于访问 Encrypted Secrets 文件中的内容。可以使用 `Rails.application.credentials.xxx` 来访问文件中的 `xxx` 部分，例如 `Rails.application.credentials.aws[:access_key_id]`。
- `config/credentials.yml.enc`：Encrypted Secrets 的默认存储位置。在这个文件中存储了加密的敏感信息。












