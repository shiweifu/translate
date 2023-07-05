翻译自：[Forum App with Golang/Gin and React/Hooks - DEV Community](https://dev.to/stevensunflash/real-world-app-with-golang-gin-and-react-hooks-44ph)

您是否一直期待使用 Golang 和 React 构建生产应用程序？这就是一个。

此应用程序有一个 API 后端和一个使用 API 的前端。

该应用程序分散在两个项目中：

- https://github.com/victorsteven/Forum-App-Go-Backend (后端)
- https://github.com/victorsteven/Forum-App-React-Frontend (前端)

这是应用程序的线上版本。你可以和它交互。

- [https://seamflow.com](https://seamflow.com/)

### 所用工具

后端工具：

- Golang
- Gin Framework
- GORM
- PostgreSQL/MySQL

前端工具：

- React
- React Hooks
- Redux

部署工具：

- Linux
- Nginx
- Docker

虽然以上内容可能看起来来势汹汹，但你会看到它们是如何协同工作的。

你可能还想在这里查看我关于 go、docker、kubernetes 的[其他文章](https://medium.com/@victorsteven)。

### 第 1 节：构建后端

这是与 Golang 连接的后端会话。

在这里，我将逐步介绍所做的工作。

#### 第一步：基本设置

##### a. 基础目录

在计算机中选择的任何路径上创建 `forum` 的目录，然后切换到该目录：

````
        ```mkdir forum && cd forum```
````

##### b. Go Modules

初始化 `go module`。这就考虑到了我们的依赖性管理。在根目录中运行：

`go mod init github.com/victorsteven/forum`

如您所见，我使用了 github url，我的用户名，和应用程序 root 目录名称。您可以遵循您的使用习惯。

### c. 基本安装

我们将在此应用中，使用第三方的安装包。如果你此前从未安装过它们，你可以执行以下命令：

```
go get github.com/badoux/checkmail
go get github.com/jinzhu/gorm
go get golang.org/x/crypto/bcrypt
go get github.com/dgrijalva/jwt-go
go get github.com/jinzhu/gorm/dialects/postgres
go get github.com/joho/godotenv
go get gopkg.in/go-playground/assert.v1
go get github.com/gin-contrib/cors
go get github.com/gin-gonic/contrib
go get github.com/gin-gonic/gin
go get github.com/aws/aws-sdk-go
go get github.com/sendgrid/sendgrid-go
go get github.com/stretchr/testify
go get github.com/twinj/uuid
github.com/matcornic/hermes/v2
```

### d. .env 文件

创建并设置一个 `.env` 文件，在根目录。

`touch .env`

这个 .env 文件包含数据库配置详情和其他一些内容，如你想设置的密钥。你可以使用 `.env.example` 文件（来自本 repo）的内容，作为参考。

这是一个示例 .env 文件：

```
APP_ENV=local
API_PORT=8888
DB_HOST=forum-postgres            # RUNNING THE APP WITH DOCKER
# DB_HOST=127.0.0.1                # RUNNING THE APP WITHOUT DOCKER
DB_DRIVER=postgres
API_SECRET=98hbun98h
DB_USER=steven
DB_PASSWORD=password
DB_NAME=forum_db
DB_PORT=5432


#TEST_DB_HOST=forum-postgres-test       # RUNNING THE TEST  WITH DOCKER
TEST_DB_HOST=127.0.0.1                  # RUNNING THE TEST  WITHOUT DOCKER
TEST_DB_DRIVER=postgres
TEST_API_SECRET=98hbun98h
TEST_DB_USER=steven
TEST_DB_PASSWORD=password
TEST_DB_NAME=forum_db_test
TEST_DB_PORT=5432
```

### e. api 和 tests 目录

在 root 目录下，创建 `api` 和 `tests` 文件夹。

`mkdir api && mkdir tests`

此时，我们的目录结构看起来应该如下：

```
forum
├── api
├── tests
├── .env
└── go.mod
```

### 步骤 2：编写模型

在此论坛 App 中，我们需要编写 5 个模型：

a. User  
b. Post  
c. Like  
d. Comment  
e. ResetPassword

#### a. User Model

在 API 目录中，创建 models 目录：

```
cd api && touch User.go
```

```
cd models && touch User.go
```

一个 User，可以：

- 注册

- 登录

- 更新信息

- 注销账号

```
package models

import (
	"errors"
	"html"
	"log"
	"os"
	"strings"
	"time"

	"github.com/victorsteven/forum/api/security"

	"github.com/badoux/checkmail"
	"github.com/jinzhu/gorm"
)

type User struct {
	ID         uint32    `gorm:"primary_key;auto_increment" json:"id"`
	Username   string    `gorm:"size:255;not null;unique" json:"username"`
	Email      string    `gorm:"size:100;not null;unique" json:"email"`
	Password   string    `gorm:"size:100;not null;" json:"password"`
	AvatarPath string    `gorm:"size:255;null;" json:"avatar_path"`
	CreatedAt  time.Time `gorm:"default:CURRENT_TIMESTAMP" json:"created_at"`
	UpdatedAt  time.Time `gorm:"default:CURRENT_TIMESTAMP" json:"updated_at"`
}

func (u *User) BeforeSave() error {
	hashedPassword, err := security.Hash(u.Password)
	if err != nil {
		return err
	}
	u.Password = string(hashedPassword)
	return nil
}

func (u *User) Prepare() {
	u.Username = html.EscapeString(strings.TrimSpace(u.Username))
	u.Email = html.EscapeString(strings.TrimSpace(u.Email))
	u.CreatedAt = time.Now()
	u.UpdatedAt = time.Now()
}

func (u *User) AfterFind() (err error) {
	if err != nil {
		return err
	}
	if u.AvatarPath != "" {
		u.AvatarPath = os.Getenv("DO_SPACES_URL") + u.AvatarPath
	}
	//dont return the user password
	// u.Password = ""
	return nil
}

func (u *User) Validate(action string) map[string]string {
	var errorMessages = make(map[string]string)
	var err error

	switch strings.ToLower(action) {
	case "update":
		if u.Email == "" {
			err = errors.New("Required Email")
			errorMessages["Required_email"] = err.Error()
		}
		if u.Email != "" {
			if err = checkmail.ValidateFormat(u.Email); err != nil {
				err = errors.New("Invalid Email")
				errorMessages["Invalid_email"] = err.Error()
			}
		}

	case "login":
		if u.Password == "" {
			err = errors.New("Required Password")
			errorMessages["Required_password"] = err.Error()
		}
		if u.Email == "" {
			err = errors.New("Required Email")
			errorMessages["Required_email"] = err.Error()
		}
		if u.Email != "" {
			if err = checkmail.ValidateFormat(u.Email); err != nil {
				err = errors.New("Invalid Email")
				errorMessages["Invalid_email"] = err.Error()
			}
		}
	case "forgotpassword":
		if u.Email == "" {
			err = errors.New("Required Email")
			errorMessages["Required_email"] = err.Error()
		}
		if u.Email != "" {
			if err = checkmail.ValidateFormat(u.Email); err != nil {
				err = errors.New("Invalid Email")
				errorMessages["Invalid_email"] = err.Error()
			}
		}
	default:
		if u.Username == "" {
			err = errors.New("Required Username")
			errorMessages["Required_username"] = err.Error()
		}
		if u.Password == "" {
			err = errors.New("Required Password")
			errorMessages["Required_password"] = err.Error()
		}
		if u.Password != "" && len(u.Password) < 6 {
			err = errors.New("Password should be atleast 6 characters")
			errorMessages["Invalid_password"] = err.Error()
		}
		if u.Email == "" {
			err = errors.New("Required Email")
			errorMessages["Required_email"] = err.Error()

		}
		if u.Email != "" {
			if err = checkmail.ValidateFormat(u.Email); err != nil {
				err = errors.New("Invalid Email")
				errorMessages["Invalid_email"] = err.Error()
			}
		}
	}
	return errorMessages
}

func (u *User) SaveUser(db *gorm.DB) (*User, error) {

	var err error
	err = db.Debug().Create(&u).Error
	if err != nil {
		return &User{}, err
	}
	return u, nil
}

// THE ONLY PERSON THAT NEED TO DO THIS IS THE ADMIN, SO I HAVE COMMENTED THE ROUTES, SO SOMEONE ELSE DONT VIEW THIS DETAILS.
func (u *User) FindAllUsers(db *gorm.DB) (*[]User, error) {
	var err error
	users := []User{}
	err = db.Debug().Model(&User{}).Limit(100).Find(&users).Error
	if err != nil {
		return &[]User{}, err
	}
	return &users, err
}

func (u *User) FindUserByID(db *gorm.DB, uid uint32) (*User, error) {
	var err error
	err = db.Debug().Model(User{}).Where("id = ?", uid).Take(&u).Error
	if err != nil {
		return &User{}, err
	}
	if gorm.IsRecordNotFoundError(err) {
		return &User{}, errors.New("User Not Found")
	}
	return u, err
}

func (u *User) UpdateAUser(db *gorm.DB, uid uint32) (*User, error) {

	if u.Password != "" {
		// To hash the password
		err := u.BeforeSave()
		if err != nil {
			log.Fatal(err)
		}

		db = db.Debug().Model(&User{}).Where("id = ?", uid).Take(&User{}).UpdateColumns(
			map[string]interface{}{
				"password":  u.Password,
				"email":     u.Email,
				"update_at": time.Now(),
			},
		)
	}

	db = db.Debug().Model(&User{}).Where("id = ?", uid).Take(&User{}).UpdateColumns(
		map[string]interface{}{
			"email":     u.Email,
			"update_at": time.Now(),
		},
	)
	if db.Error != nil {
		return &User{}, db.Error
	}

	// This is the display the updated user
	err := db.Debug().Model(&User{}).Where("id = ?", uid).Take(&u).Error
	if err != nil {
		return &User{}, err
	}
	return u, nil
}

func (u *User) UpdateAUserAvatar(db *gorm.DB, uid uint32) (*User, error) {
	db = db.Debug().Model(&User{}).Where("id = ?", uid).Take(&User{}).UpdateColumns(
		map[string]interface{}{
			"avatar_path": u.AvatarPath,
			"update_at":   time.Now(),
		},
	)
	if db.Error != nil {
		return &User{}, db.Error
	}
	// This is the display the updated user
	err := db.Debug().Model(&User{}).Where("id = ?", uid).Take(&u).Error
	if err != nil {
		return &User{}, err
	}
	return u, nil
}

func (u *User) DeleteAUser(db *gorm.DB, uid uint32) (int64, error) {

	db = db.Debug().Model(&User{}).Where("id = ?", uid).Take(&User{}).Delete(&User{})

	if db.Error != nil {
		return 0, db.Error
	}
	return db.RowsAffected, nil
}

func (u *User) UpdatePassword(db *gorm.DB) error {

	// To hash the password
	err := u.BeforeSave()
	if err != nil {
		log.Fatal(err)
	}

	db = db.Debug().Model(&User{}).Where("email = ?", u.Email).Take(&User{}).UpdateColumns(
		map[string]interface{}{
			"password":  u.Password,
			"update_at": time.Now(),
		},
	)
	if db.Error != nil {
		return db.Error
	}
	return nil
}
```

#### b. Post Model

一个 Post 可以：

- 创建

- 更新

- 删除

在 models 目录，创建 `Post.go` 文件：

```
touch Post.go
```
