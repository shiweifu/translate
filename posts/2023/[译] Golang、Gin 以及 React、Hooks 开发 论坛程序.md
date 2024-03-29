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

```
        ```mkdir forum && cd forum```
```

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

```
package models

import (
    "errors"
    "html"
    "strings"
    "time"

    "github.com/jinzhu/gorm"
)

type Post struct {
    ID        uint64    `gorm:"primary_key;auto_increment" json:"id"`
    Title     string    `gorm:"size:255;not null;unique" json:"title"`
    Content   string    `gorm:"text;not null;" json:"content"`
    Author    User      `json:"author"`
    AuthorID  uint32    `gorm:"not null" json:"author_id"`
    CreatedAt time.Time `gorm:"default:CURRENT_TIMESTAMP" json:"created_at"`
    UpdatedAt time.Time `gorm:"default:CURRENT_TIMESTAMP" json:"updated_at"`
}

func (p *Post) Prepare() {
    p.Title = html.EscapeString(strings.TrimSpace(p.Title))
    p.Content = html.EscapeString(strings.TrimSpace(p.Content))
    p.Author = User{}
    p.CreatedAt = time.Now()
    p.UpdatedAt = time.Now()
}

func (p *Post) Validate() map[string]string {

    var err error

    var errorMessages = make(map[string]string)

    if p.Title == "" {
        err = errors.New("Required Title")
        errorMessages["Required_title"] = err.Error()

    }
    if p.Content == "" {
        err = errors.New("Required Content")
        errorMessages["Required_content"] = err.Error()

    }
    if p.AuthorID < 1 {
        err = errors.New("Required Author")
        errorMessages["Required_author"] = err.Error()
    }
    return errorMessages
}

func (p *Post) SavePost(db *gorm.DB) (*Post, error) {
    var err error
    err = db.Debug().Model(&Post{}).Create(&p).Error
    if err != nil {
        return &Post{}, err
    }
    if p.ID != 0 {
        err = db.Debug().Model(&User{}).Where("id = ?", p.AuthorID).Take(&p.Author).Error
        if err != nil {
            return &Post{}, err
        }
    }
    return p, nil
}

func (p *Post) FindAllPosts(db *gorm.DB) (*[]Post, error) {
    var err error
    posts := []Post{}
    err = db.Debug().Model(&Post{}).Limit(100).Order("created_at desc").Find(&posts).Error
    if err != nil {
        return &[]Post{}, err
    }
    if len(posts) > 0 {
        for i, _ := range posts {
            err := db.Debug().Model(&User{}).Where("id = ?", posts[i].AuthorID).Take(&posts[i].Author).Error
            if err != nil {
                return &[]Post{}, err
            }
        }
    }
    return &posts, nil
}

func (p *Post) FindPostByID(db *gorm.DB, pid uint64) (*Post, error) {
    var err error
    err = db.Debug().Model(&Post{}).Where("id = ?", pid).Take(&p).Error
    if err != nil {
        return &Post{}, err
    }
    if p.ID != 0 {
        err = db.Debug().Model(&User{}).Where("id = ?", p.AuthorID).Take(&p.Author).Error
        if err != nil {
            return &Post{}, err
        }
    }
    return p, nil
}

func (p *Post) UpdateAPost(db *gorm.DB) (*Post, error) {

    var err error

    err = db.Debug().Model(&Post{}).Where("id = ?", p.ID).Updates(Post{Title: p.Title, Content: p.Content, UpdatedAt: time.Now()}).Error
    if err != nil {
        return &Post{}, err
    }
    if p.ID != 0 {
        err = db.Debug().Model(&User{}).Where("id = ?", p.AuthorID).Take(&p.Author).Error
        if err != nil {
            return &Post{}, err
        }
    }
    return p, nil
}

func (p *Post) DeleteAPost(db *gorm.DB) (int64, error) {

    db = db.Debug().Model(&Post{}).Where("id = ?", p.ID).Take(&Post{}).Delete(&Post{})
    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}

func (p *Post) FindUserPosts(db *gorm.DB, uid uint32) (*[]Post, error) {

    var err error
    posts := []Post{}
    err = db.Debug().Model(&Post{}).Where("author_id = ?", uid).Limit(100).Order("created_at desc").Find(&posts).Error
    if err != nil {
        return &[]Post{}, err
    }
    if len(posts) > 0 {
        for i, _ := range posts {
            err := db.Debug().Model(&User{}).Where("id = ?", posts[i].AuthorID).Take(&posts[i].Author).Error
            if err != nil {
                return &[]Post{}, err
            }
        }
    }
    return &posts, nil
}

//When a user is deleted, we also delete the post that the user had
func (c *Post) DeleteUserPosts(db *gorm.DB, uid uint32) (int64, error) {
    posts := []Post{}
    db = db.Debug().Model(&Post{}).Where("author_id = ?", uid).Find(&posts).Delete(&posts)
    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}
```

### c. Like 模型

Posts 模型可以被 Like 或者 Unlike

一个 Like，可以：

1. 创建

2. 删除

创建 `Like.go` 文件：

`touch like.go`

```
package models

import (
    "errors"
    "fmt"
    "time"

    "github.com/jinzhu/gorm"
)

type Like struct {
    ID        uint64    `gorm:"primary_key;auto_increment" json:"id"`
    UserID    uint32    `gorm:"not null" json:"user_id"`
    PostID    uint64    `gorm:"not null" json:"post_id"`
    CreatedAt time.Time `gorm:"default:CURRENT_TIMESTAMP" json:"created_at"`
    UpdatedAt time.Time `gorm:"default:CURRENT_TIMESTAMP" json:"updated_at"`
}

func (l *Like) SaveLike(db *gorm.DB) (*Like, error) {

    // Check if the auth user has liked this post before:
    err := db.Debug().Model(&Like{}).Where("post_id = ? AND user_id = ?", l.PostID, l.UserID).Take(&l).Error
    if err != nil {
        if err.Error() == "record not found" {
            // The user has not liked this post before, so lets save incomming like:
            err = db.Debug().Model(&Like{}).Create(&l).Error
            if err != nil {
                return &Like{}, err
            }
        }
    } else {
        // The user has liked it before, so create a custom error message
        err = errors.New("double like")
        return &Like{}, err
    }
    return l, nil
}

func (l *Like) DeleteLike(db *gorm.DB) (*Like, error) {
    var err error
    var deletedLike *Like

    err = db.Debug().Model(Like{}).Where("id = ?", l.ID).Take(&l).Error
    if err != nil {
        return &Like{}, err
    } else {
        //If the like exist, save it in deleted like and delete it
        deletedLike = l
        db = db.Debug().Model(&Like{}).Where("id = ?", l.ID).Take(&Like{}).Delete(&Like{})
        if db.Error != nil {
            fmt.Println("cant delete like: ", db.Error)
            return &Like{}, db.Error
        }
    }
    return deletedLike, nil
}

func (l *Like) GetLikesInfo(db *gorm.DB, pid uint64) (*[]Like, error) {

    likes := []Like{}
    err := db.Debug().Model(&Like{}).Where("post_id = ?", pid).Find(&likes).Error
    if err != nil {
        return &[]Like{}, err
    }
    return &likes, err
}

//When a post is deleted, we also delete the likes that the post had
func (l *Like) DeleteUserLikes(db *gorm.DB, uid uint32) (int64, error) {
    likes := []Like{}
    db = db.Debug().Model(&Like{}).Where("user_id = ?", uid).Find(&likes).Delete(&likes)
    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}

//When a post is deleted, we also delete the likes that the post had
func (l *Like) DeletePostLikes(db *gorm.DB, pid uint64) (int64, error) {
    likes := []Like{}
    db = db.Debug().Model(&Like{}).Where("post_id = ?", pid).Find(&likes).Delete(&likes)
    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}
```

### Comment 模型

一个 Post 可以有评论。

评论可以被：

1. 创建

2. 更新

3. 删除

创建 `Comment.go` 文件

`touch Comment.go`

```
package models

import (
    "errors"
    "fmt"
    "html"
    "strings"
    "time"

    "github.com/jinzhu/gorm"
)

type Comment struct {
    ID        uint64    `gorm:"primary_key;auto_increment" json:"id"`
    UserID    uint32    `gorm:"not null" json:"user_id"`
    PostID    uint64    `gorm:"not null" json:"post_id"`
    Body      string    `gorm:"text;not null;" json:"body"`
    User      User      `json:"user"`
    CreatedAt time.Time `gorm:"default:CURRENT_TIMESTAMP" json:"created_at"`
    UpdatedAt time.Time `gorm:"default:CURRENT_TIMESTAMP" json:"updated_at"`
}

func (c *Comment) Prepare() {
    c.ID = 0
    c.Body = html.EscapeString(strings.TrimSpace(c.Body))
    c.User = User{}
    c.CreatedAt = time.Now()
    c.UpdatedAt = time.Now()
}

func (c *Comment) Validate(action string) map[string]string {
    var errorMessages = make(map[string]string)
    var err error

    switch strings.ToLower(action) {
    case "update":
        if c.Body == "" {
            err = errors.New("Required Comment")
            errorMessages["Required_body"] = err.Error()
        }
    default:
        if c.Body == "" {
            err = errors.New("Required Comment")
            errorMessages["Required_body"] = err.Error()
        }
    }
    return errorMessages
}

func (c *Comment) SaveComment(db *gorm.DB) (*Comment, error) {
    err := db.Debug().Create(&c).Error
    if err != nil {
        return &Comment{}, err
    }
    if c.ID != 0 {
        err = db.Debug().Model(&User{}).Where("id = ?", c.UserID).Take(&c.User).Error
        if err != nil {
            return &Comment{}, err
        }
    }
    return c, nil
}

func (c *Comment) GetComments(db *gorm.DB, pid uint64) (*[]Comment, error) {

    comments := []Comment{}
    err := db.Debug().Model(&Comment{}).Where("post_id = ?", pid).Order("created_at desc").Find(&comments).Error
    if err != nil {
        return &[]Comment{}, err
    }
    if len(comments) > 0 {
        for i, _ := range comments {
            err := db.Debug().Model(&User{}).Where("id = ?", comments[i].UserID).Take(&comments[i].User).Error
            if err != nil {
                return &[]Comment{}, err
            }
        }
    }
    return &comments, err
}

func (c *Comment) UpdateAComment(db *gorm.DB) (*Comment, error) {

    var err error

    err = db.Debug().Model(&Comment{}).Where("id = ?", c.ID).Updates(Comment{Body: c.Body, UpdatedAt: time.Now()}).Error
    if err != nil {
        return &Comment{}, err
    }

    fmt.Println("this is the comment body: ", c.Body)
    if c.ID != 0 {
        err = db.Debug().Model(&User{}).Where("id = ?", c.UserID).Take(&c.User).Error
        if err != nil {
            return &Comment{}, err
        }
    }
    return c, nil
}

func (c *Comment) DeleteAComment(db *gorm.DB) (int64, error) {

    db = db.Debug().Model(&Comment{}).Where("id = ?", c.ID).Take(&Comment{}).Delete(&Comment{})

    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}

//When a user is deleted, we also delete the comments that the user had
func (c *Comment) DeleteUserComments(db *gorm.DB, uid uint32) (int64, error) {
    comments := []Comment{}
    db = db.Debug().Model(&Comment{}).Where("user_id = ?", uid).Find(&comments).Delete(&comments)
    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}

//When a post is deleted, we also delete the comments that the post had
func (c *Comment) DeletePostComments(db *gorm.DB, pid uint64) (int64, error) {
    comments := []Comment{}
    db = db.Debug().Model(&Comment{}).Where("post_id = ?", pid).Find(&comments).Delete(&comments)
    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}
```

### c. Like Model

日志可以被标记喜欢或者不喜欢。

一个喜欢，可以被：

1. 创建

2. 删除

创建 `Like.go` 文件：

`touch Like.go`

```
package models

import (
    "errors"
    "fmt"
    "time"

    "github.com/jinzhu/gorm"
)

type Like struct {
    ID        uint64    `gorm:"primary_key;auto_increment" json:"id"`
    UserID    uint32    `gorm:"not null" json:"user_id"`
    PostID    uint64    `gorm:"not null" json:"post_id"`
    CreatedAt time.Time `gorm:"default:CURRENT_TIMESTAMP" json:"created_at"`
    UpdatedAt time.Time `gorm:"default:CURRENT_TIMESTAMP" json:"updated_at"`
}

func (l *Like) SaveLike(db *gorm.DB) (*Like, error) {

    // Check if the auth user has liked this post before:
    err := db.Debug().Model(&Like{}).Where("post_id = ? AND user_id = ?", l.PostID, l.UserID).Take(&l).Error
    if err != nil {
        if err.Error() == "record not found" {
            // The user has not liked this post before, so lets save incomming like:
            err = db.Debug().Model(&Like{}).Create(&l).Error
            if err != nil {
                return &Like{}, err
            }
        }
    } else {
        // The user has liked it before, so create a custom error message
        err = errors.New("double like")
        return &Like{}, err
    }
    return l, nil
}

func (l *Like) DeleteLike(db *gorm.DB) (*Like, error) {
    var err error
    var deletedLike *Like

    err = db.Debug().Model(Like{}).Where("id = ?", l.ID).Take(&l).Error
    if err != nil {
        return &Like{}, err
    } else {
        //If the like exist, save it in deleted like and delete it
        deletedLike = l
        db = db.Debug().Model(&Like{}).Where("id = ?", l.ID).Take(&Like{}).Delete(&Like{})
        if db.Error != nil {
            fmt.Println("cant delete like: ", db.Error)
            return &Like{}, db.Error
        }
    }
    return deletedLike, nil
}

func (l *Like) GetLikesInfo(db *gorm.DB, pid uint64) (*[]Like, error) {

    likes := []Like{}
    err := db.Debug().Model(&Like{}).Where("post_id = ?", pid).Find(&likes).Error
    if err != nil {
        return &[]Like{}, err
    }
    return &likes, err
}

//When a post is deleted, we also delete the likes that the post had
func (l *Like) DeleteUserLikes(db *gorm.DB, uid uint32) (int64, error) {
    likes := []Like{}
    db = db.Debug().Model(&Like{}).Where("user_id = ?", uid).Find(&likes).Delete(&likes)
    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}

//When a post is deleted, we also delete the likes that the post had
func (l *Like) DeletePostLikes(db *gorm.DB, pid uint64) (int64, error) {
    likes := []Like{}
    db = db.Debug().Model(&Like{}).Where("post_id = ?", pid).Find(&likes).Delete(&likes)
    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}
```

### d. Comment Model

一个 Post，可以包含 Comments。

Comment 有以下操作：

1. 创建

2. 更新

3. 删除

创建 Comment.go 文件

`touch Comment.go`

```
package models

import (
    "errors"
    "fmt"
    "html"
    "strings"
    "time"

    "github.com/jinzhu/gorm"
)

type Comment struct {
    ID        uint64    `gorm:"primary_key;auto_increment" json:"id"`
    UserID    uint32    `gorm:"not null" json:"user_id"`
    PostID    uint64    `gorm:"not null" json:"post_id"`
    Body      string    `gorm:"text;not null;" json:"body"`
    User      User      `json:"user"`
    CreatedAt time.Time `gorm:"default:CURRENT_TIMESTAMP" json:"created_at"`
    UpdatedAt time.Time `gorm:"default:CURRENT_TIMESTAMP" json:"updated_at"`
}

func (c *Comment) Prepare() {
    c.ID = 0
    c.Body = html.EscapeString(strings.TrimSpace(c.Body))
    c.User = User{}
    c.CreatedAt = time.Now()
    c.UpdatedAt = time.Now()
}

func (c *Comment) Validate(action string) map[string]string {
    var errorMessages = make(map[string]string)
    var err error

    switch strings.ToLower(action) {
    case "update":
        if c.Body == "" {
            err = errors.New("Required Comment")
            errorMessages["Required_body"] = err.Error()
        }
    default:
        if c.Body == "" {
            err = errors.New("Required Comment")
            errorMessages["Required_body"] = err.Error()
        }
    }
    return errorMessages
}

func (c *Comment) SaveComment(db *gorm.DB) (*Comment, error) {
    err := db.Debug().Create(&c).Error
    if err != nil {
        return &Comment{}, err
    }
    if c.ID != 0 {
        err = db.Debug().Model(&User{}).Where("id = ?", c.UserID).Take(&c.User).Error
        if err != nil {
            return &Comment{}, err
        }
    }
    return c, nil
}

func (c *Comment) GetComments(db *gorm.DB, pid uint64) (*[]Comment, error) {

    comments := []Comment{}
    err := db.Debug().Model(&Comment{}).Where("post_id = ?", pid).Order("created_at desc").Find(&comments).Error
    if err != nil {
        return &[]Comment{}, err
    }
    if len(comments) > 0 {
        for i, _ := range comments {
            err := db.Debug().Model(&User{}).Where("id = ?", comments[i].UserID).Take(&comments[i].User).Error
            if err != nil {
                return &[]Comment{}, err
            }
        }
    }
    return &comments, err
}

func (c *Comment) UpdateAComment(db *gorm.DB) (*Comment, error) {

    var err error

    err = db.Debug().Model(&Comment{}).Where("id = ?", c.ID).Updates(Comment{Body: c.Body, UpdatedAt: time.Now()}).Error
    if err != nil {
        return &Comment{}, err
    }

    fmt.Println("this is the comment body: ", c.Body)
    if c.ID != 0 {
        err = db.Debug().Model(&User{}).Where("id = ?", c.UserID).Take(&c.User).Error
        if err != nil {
            return &Comment{}, err
        }
    }
    return c, nil
}

func (c *Comment) DeleteAComment(db *gorm.DB) (int64, error) {

    db = db.Debug().Model(&Comment{}).Where("id = ?", c.ID).Take(&Comment{}).Delete(&Comment{})

    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}

//When a user is deleted, we also delete the comments that the user had
func (c *Comment) DeleteUserComments(db *gorm.DB, uid uint32) (int64, error) {
    comments := []Comment{}
    db = db.Debug().Model(&Comment{}).Where("user_id = ?", uid).Find(&comments).Delete(&comments)
    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}

//When a post is deleted, we also delete the comments that the post had
func (c *Comment) DeletePostComments(db *gorm.DB, pid uint64) (int64, error) {
    comments := []Comment{}
    db = db.Debug().Model(&Comment{}).Where("post_id = ?", pid).Find(&comments).Delete(&comments)
    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}
```

### e. ResetPassword Model

用户可能忘记了自己的密码。当这种情况发生时，他们可以请求更改为新的。将向他们的电子邮件地址发送通知，并附上创建新密码的说明。

在 `models` 目录，创建 `ResetPassword.go` 文件：

`touch ResetPassword.go`

```
package models

import (
    "html"

    "strings"

    "github.com/jinzhu/gorm"
)

type ResetPassword struct {
    gorm.Model
    Email string `gorm:"size:100;not null;" json:"email"`
    Token string `gorm:"size:255;not null;" json:"token"`
}

func (resetPassword *ResetPassword) Prepare() {
    resetPassword.Token = html.EscapeString(strings.TrimSpace(resetPassword.Token))
    resetPassword.Email = html.EscapeString(strings.TrimSpace(resetPassword.Email))
}

func (resetPassword *ResetPassword) SaveDatails(db *gorm.DB) (*ResetPassword, error) {
    var err error
    err = db.Debug().Create(&resetPassword).Error
    if err != nil {
        return &ResetPassword{}, err
    }
    return resetPassword, nil
}

func (resetPassword *ResetPassword) DeleteDatails(db *gorm.DB) (int64, error) {

    db = db.Debug().Model(&ResetPassword{}).Where("id = ?", resetPassword.ID).Take(&ResetPassword{}).Delete(&ResetPassword{})

    if db.Error != nil {
        return 0, db.Error
    }
    return db.RowsAffected, nil
}
```

## 步骤3：安全

### a. 密码安全

在User.go文件中观察到，在将密码保存到我们的数据库中之前，必须首先对其进行哈希处理。我们调用了一个函数来帮助我们做到这一点。让我们把它接线。

在api目录（路径：/forum/backend/api/）中，创建 `security` 目录：

在安全目录中，创建 `password.go` 文件：

`cd security && touch password.go`

```
package security

import "golang.org/x/crypto/bcrypt"

func Hash(password string) ([]byte, error) {

    return bcrypt.GenerateFromPassword([]byte(password), bcrypt.DefaultCost)
}

func VerifyPassword(hashedPassword, password string) error {

    return bcrypt.CompareHashAndPassword([]byte(hashedPassword), []byte(password))

}
```

### b. ResetPassword 创建 Token

这种情况是：当用户请求更改密码时，会向该用户的电子邮件发送一个令牌。编写了一个函数来对令牌进行散列。当我们连接ResetPassword控制器文件时，将使用此功能。

`touch tokenhash.go`

```
package security

import (
    "crypto/md5"
    "encoding/hex"
    "github.com/twinj/uuid"

)

func TokenHash(text string) string {

    hasher := md5.New()
    hasher.Write([]byte(text))
    theHash :=  hex.EncodeToString(hasher.Sum(nil))

    //also use uuid
    u := uuid.NewV4()
    theToken := theHash + u.String()

    return theToken
}
```

## 步骤4：Seeder

我认为有数据进行实验是个好主意。当我们最终连接数据库时，我们将对users和posts表进行种子设定。

在API目录（在路径中：/forum/api/）中，创建一个种子目录：

`touch seeder.go`

```
package seed

import (
    "log"

    "github.com/jinzhu/gorm"
    "github.com/victorsteven/forum/api/models"
)

var users = []models.User{
    models.User{
        Username: "steven",
        Email:    "steven@example.com",
        Password: "password",
    },
    models.User{
        Username: "martin",
        Email:    "luther@example.com",
        Password: "password",
    },
}

var posts = []models.Post{
    models.Post{
        Title:   "Title 1",
        Content: "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum",
    },
    models.Post{
        Title:   "Title 2",
        Content: "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum",
    },
}

func Load(db *gorm.DB) {

    err := db.Debug().DropTableIfExists(&models.Post{}, &models.User{}, &models.Like{}, &models.Comment{}).Error
    if err != nil {
        log.Fatalf("cannot drop table: %v", err)
    }
    err = db.Debug().AutoMigrate(&models.User{}, &models.Post{}).Error
    if err != nil {
        log.Fatalf("cannot migrate table: %v", err)
    }

    err = db.Debug().Model(&models.Post{}).AddForeignKey("author_id", "users(id)", "cascade", "cascade").Error
    if err != nil {
        log.Fatalf("attaching foreign key error: %v", err)
    }

    for i, _ := range users {
        err = db.Debug().Model(&models.User{}).Create(&users[i]).Error
        if err != nil {
            log.Fatalf("cannot seed users table: %v", err)
        }
        posts[i].AuthorID = users[i].ID

        err = db.Debug().Model(&models.Post{}).Create(&posts[i]).Error
        if err != nil {
            log.Fatalf("cannot seed posts table: %v", err)
        }
    }
}
```





## 步骤5：使用 JWT 进行验证



这个应用程序需要进行身份验证，例如创建帖子、点赞帖子、更新个人资料、评论帖子等等。我们需要建立一个身份验证系统。



在api目录中，创建auth目录：



`mkdir auth`



在auth目录中，创建token.go文件：



`cd auth && touch token.go`



```
package auth

import (
	"encoding/json"
	"fmt"
	"log"
	"net/http"
	"os"
	"strconv"
	"strings"

	"github.com/dgrijalva/jwt-go"
)

func CreateToken(id uint32) (string, error) {
	claims := jwt.MapClaims{}
	claims["authorized"] = true
	claims["id"] = id
	token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
	return token.SignedString([]byte(os.Getenv("API_SECRET")))
}

func TokenValid(r *http.Request) error {
	tokenString := ExtractToken(r)
	token, err := jwt.Parse(tokenString, func(token *jwt.Token) (interface{}, error) {
		if _, ok := token.Method.(*jwt.SigningMethodHMAC); !ok {
			return nil, fmt.Errorf("Unexpected signing method: %v", token.Header["alg"])
		}
		return []byte(os.Getenv("API_SECRET")), nil
	})
	if err != nil {
		return err
	}
	if claims, ok := token.Claims.(jwt.MapClaims); ok && token.Valid {
		Pretty(claims)
	}
	return nil
}

func ExtractToken(r *http.Request) string {
	keys := r.URL.Query()
	token := keys.Get("token")
	if token != "" {
		return token
	}
	bearerToken := r.Header.Get("Authorization")
	if len(strings.Split(bearerToken, " ")) == 2 {
		return strings.Split(bearerToken, " ")[1]
	}
	return ""
}

func ExtractTokenID(r *http.Request) (uint32, error) {

	tokenString := ExtractToken(r)
	token, err := jwt.Parse(tokenString, func(token *jwt.Token) (interface{}, error) {
		if _, ok := token.Method.(*jwt.SigningMethodHMAC); !ok {
			return nil, fmt.Errorf("Unexpected signing method: %v", token.Header["alg"])
		}
		return []byte(os.Getenv("API_SECRET")), nil
	})
	if err != nil {
		return 0, err
	}
	claims, ok := token.Claims.(jwt.MapClaims)
	if ok && token.Valid {
		uid, err := strconv.ParseUint(fmt.Sprintf("%.0f", claims["id"]), 10, 32)
		if err != nil {
			return 0, err
		}
		return uint32(uid), nil
	}
	return 0, nil
}

//Pretty display the claims licely in the terminal
func Pretty(data interface{}) {
	b, err := json.MarshalIndent(data, "", " ")
	if err != nil {
		log.Println(err)
		return
	}

	fmt.Println(string(b))
}
```
