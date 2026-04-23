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
- 在 for 语句操作列表时，应当写明这是函数式里的 `Where`（过滤）还是 `Select`（映射/转换）

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

### 结构体与成员函数（方法）

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

## 命名规范

### 缩写词一致性

缩写词在命名中必须保持全大写或全小写，严禁将其视作普通单词仅首字母大写。

```go
// 错误
appId, jsonParser, HttpServer

// 正确
appID, JSONParser, HTTPServer // 公开
httpServer // 私有
```

### 接口命名

接口设计倾向于"小而精"：

- **单函数接口**：以 `er` 结尾。如 `Reader`, `Writer`, `Formatter`, `Scanner`
- **转换接口**：命名反映目标。如 `Stringer`（转换为字符串）
- **组合接口**：通过多个接口组合，采用名词描述。如 `ReadCloser`（组合了 `Reader` 和 `Closer`）

### 作用域决定长度

- **短作用域**（循环/分支）：单字母或简写。如 `i`（index）、`r`（reader）、`ctx`（context）
- **长作用域**（包级变量/全局配置）：必须使用描述性长名称。如 `MaxConnectionRetries` 优于 `Retries`

### 包名规范

包名是变量/函数全路径的一部分，必须简洁且具预测性：

- **名词性质**：单数形式名词（如 `encoding` 而非 `encodings`）
- **避免冗余**：包名不应与导出的函数名重复
- **目录依赖**：包名与目录名一致，全小写，不使用下划线或驼峰

```go
// 错误：冗余
http.HTTPServer

// 正确
http.Server
```

### Error 相关命名

- **自定义错误变量**：以 `Err` 开头。如 `ErrNotFound`, `ErrPermissionDenied`
- **自定义错误类型**：以 `Error` 结尾。如 `PathError`

### Getter / Setter

Go 不推荐在 Getter 方法名前加 `Get`：

```go
// 正确
user.Name()       // Getter
user.SetName(name string) // Setter

// 错误
user.GetName()
```

### 切片/数组命名

使用复数形式，而非 `List` 等后缀：

```go
users  // 正确
userList  // 错误
```

### 布尔值命名

以 `Is`, `Has`, `Can` 或 `Allow` 开头，使其读起来像断言：

```go
isFound, hasPermission, canRetry, allowOverwrite
```

### 函数类型后缀

当类型本质是函数签名时，后缀应体现其在程序流中的角色：

| 后缀 | 用途 | 示例 |
|------|------|------|
| `-Provider` | 初始化、资源创建、依赖注入 | `StorageProvider`, `TokenProvider` |
| `-Func` | 最通用的后缀，可直接实现接口 | `ValidatorFunc`, `HandlerFunc` |
| `-Handler` | 事件驱动或中间件逻辑 | `ErrHandler`, `NotifyHandler` |
| `-Predicate` | 返回布尔值的逻辑判定 | `MatchPredicate` |

其中 `Provider` 语义最为通用，明确表示"能够提供某实例的东西"。延迟加载场景有时也用 `Generator` 或 `Creator`。

禁止函数类型命名无后缀。

### 结构体后缀

Go 倾向于简洁，不推荐普通结构体加 `Struct`。但以下后缀具有明确指示意义：

| 后缀 | 用途 | 示例 |
|------|------|------|
| `-Opt` / `-Options` | 配置参数，配合函数选项模式 | `ServerOptions`, `DialOpt` |
| `-Spec` | 规格、元数据或未实例化的描述 | `ContainerSpec`, `JobSpec` |
| `-Metadata` | 非业务核心的辅助信息 | `ObjectMetadata` |

### 匿名函数/Lambda/闭包变量命名

在函数内部定义匿名函数变量时，需打破"名词对名词"的死板命名：

- **构建型**：使用 `make-`, `new-`, `gen-` 前缀。如 `newEvaluator` 而非 `modelEval`
- **检索型**：使用 `find-`, `pick-`, `resolve-` 前缀。如 `resolveProvider`
- **后缀区分**：为在视觉上区分值变量与函数变量，可使用 `-Fn` 后缀

最次的情况下也应当使用 -Fn 后缀。注意，**这里说的是变量的命名，而不是类型别名的命名！**

```go
authFn := func(...) { ... }
```

## 语法与逻辑

### 控制流

- 避免深层嵌套，使用 guard clauses
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

## Web 开发（如果使用）

### HTTP 框架

根据项目使用的框架（如 Gin、Echo）遵循对应最佳实践

### 路由组织

```go
// 应当按 API 版本和资源分组
v1 := router.Group("/api/v1")
{
    v1.GET("/users", userHandler.List)
    v1.GET("/users/:id", userHandler.Get)
    v1.POST("/users", userHandler.Create)
    v1.PUT("/users/:id", userHandler.Update)
    v1.DELETE("/users/:id", userHandler.Delete)
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
