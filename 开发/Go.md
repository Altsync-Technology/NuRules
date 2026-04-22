# Go
允许使用 GoLand / Nvim / VSCODE 战斗进化 开发。

新手应使用 GoLand。

## 概念处理

- 结构体对应其他语言的类
- 方法就是结构体的成员函数
- 使用接收器 来称呼方法绑定
- 接口定义行为，不要过度设计
- 错误 是一个值，不是异常

## 基本

### 注释规范

- 勤写短注释
- 在 for 语句操作列表时，写明这是函数式里的 `Where`（过滤）还是 `Select`（映射/转换）

```go
// Where - 过滤出活跃用户
for _, user := range users {
    if user.IsActive {
        activeUsers = append(activeUsers, user)
    }
}

// Select - 提取用户 ID
for _, user := range users {
    userIDs = append(userIDs, user.ID)
}
```

### 导入管理

- 使用 `goimports` 或 IDE 自动导入
- 按标准库、第三方库、本地库的顺序组织
- 使用空白行分隔这三组

```go
import (
    "fmt"
    "net/http"

    "github.com/gin-gonic/gin"

    "internal/config"
    "internal/service"
)
```
### 错误处理

- **必须处理错误**，不要 `_` 掉
- 尽早返回错误，减少嵌套
- 使用 `errors.Is` 和 `errors.As` 检查错误
- 包装错误时提供上下文信息

```go
// 正确
func processData(data []byte) error {
    if len(data) == 0 {
        return errors.New("empty data")
    }

    result, err := parseData(data)
    if err != nil {
        return fmt.Errorf("parse failed: %w", err)
    }

    return saveResult(result)
}

// 错误 - 忽略错误
func badProcess(data []byte) {
    parseData(data)  // 错误被忽略
}
```

### 结构体与方法

- 结构体字段首字母大写表示导出
- 方法接收器使用指针修改状态
- 值接收器用于只读操作

```go
type User struct {
    ID       int64
    Name     string
    Password string
    CreatedAt time.Time
}

// 指针接收器 - 修改状态
func (u *User) UpdateName(name string) {
    u.Name = name
}

// 值接收器 - 只读
func (u User) IsValid() bool {
    return u.Name != ""
}
```

## 语法与逻辑

### 控制流

- 避免深层嵌套，使用 guard clauses
- 使用 `for` 循环，没有 `while`
- 使用 `range` 遍历切片、映射

```go
// 错误 嵌套过深
func processUser(user *User) error {
    if user != nil {
        if user.IsActive {
            if user.HasPermission {
                // 做事
            }
        }
    }
}

// 正确 guard clauses
func processUser(user *User) error {
    if user == nil {
        return errors.New("user is nil")
    }
    if !user.IsActive {
        return errors.New("user not active")
    }
    if !user.HasPermission {
        return errors.New("no permission")
    }
    // 做事
}
```

## 接口与设计

### 接口定义

- 接口应该小而专注
- 在使用者处定义接口，而非实现者
- 避免空接口 `interface{}` 除非确实需要

```go
// 定义在需要的地方
type DataProcessor interface {
    Process([]byte) error
}

// 实现
type JSONProcessor struct{}

func (j *JSONProcessor) Process(data []byte) error {
    // 实现
}
```

### 依赖注入

- 通过构造函数注入依赖
- 使用接口而非具体类型
- 便于测试和替换实现

```go
type UserService struct {
    repo   UserRepository
    logger Logger
}

func NewUserService(repo UserRepository, logger Logger) *UserService {
    return &UserService{
        repo:   repo,
        logger: logger,
    }
}
```

## 项目结构

### 目录布局

```
project/
├── cmd/
│   └── server/
│       └── main.go          # 应用入口
├── internal/
│   ├── config/              # 配置
│   ├── model/               # 数据模型
│   ├── repository/          # 数据访问
│   ├── service/             #   业务逻辑
│   └── handler/             # HTTP 处理器
├── pkg/                    # 公共库
├── api/                    # API 文档
├── go.mod
└── go.sum
```

## Web 开发（如果使用）

### HTTP 框架

根据项目使用的框架（如 Gin、Echo）遵循对应最佳实践

### 路由组织

```go
// 按 API 版本和资源分组
v1 := router.Group("/api/v1")
{
    v1.GET("/users", userHandler.List)
    v1.GET("/users/:id", userHandler.Get)
    v1.POST("/users", userHandler.Create)
    v1.PUT("/users/:id", userHandler.Update)
    v1.DELETE("/users/:id", userHandler.Delete)
}
```

### 请求处理

- 使用中间件处理横切关注点
- 正确设置状态码
- 统一错误响应格式

```go
// 中间件
func AuthMiddleware() gin.HandlerFunc {
    return func(c *gin.Context) {
        token := c.GetHeader("Authorization")
        if !validateToken(token) {
            c.JSON(401, gin.H{"error": "unauthorized"})
            c.Abort()
            return
        }
        c.Next()
    }
}
```

## 测试

### 单元测试

- 文件名以 `_test.go` 结尾
- 使用 `t.Errorf` 报告错误
- 使用表驱动测试

```go
func TestAdd(t *testing.T) {
    tests := []struct {
        name     string
        a, b     int
        expected int
    }{
        {"positive", 1, 2, 3},
        {"negative", -1, -2, -3},
        {"zero", 0, 0, 0},
    }

    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            result := Add(tt.a, tt.b)
            if result != tt.expected {
                t.Errorf("Add(%d, %d) = %d; want %d", tt.a, tt.b, result, tt.expected)
            }
        })
    }
}
```

### Mock 测试

- 使用接口定义依赖
- 手工实现 mock 或使用工具

```go
type MockRepository struct {
    users []User
    err   error
}

func (m *MockRepository) FindByID(id int64) (*User, error) {
    if m.err != nil {
        return nil, m.err
    }
    for _, u := range m.users {
        if u.ID == id {
            return &u, nil
        }
    }
    return nil, errors.New("not found")
}
```
