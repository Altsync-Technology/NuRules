# Java
> 查看此文档前，请先查看 [通用开发指南](../通用开发指南.md)

允许使用 Intellij Idea / Nvim / VSCODE 战斗进化 开发。

公司主要使用 Intellij Idea。

新手应使用 Intellij Idea。

## **概念处理**

- 类与结构体是一个东西
- 函数与方法是一个东西
- 使用成员函数来称呼方法（或函数）
- 使用属性或字段来称呼类里的有 `getter` 和 `setter` 的成员变量
- 对于返回的 `http` 结果，不使用 `ResultVo` 命名，而使用 `Res`
- 新的代码不要使用 POJO、BO、VO 这种弱智名称
- 与数据库关联的类被称呼为模型

## **基本**

- 尽可能使用使用 lombok
- 对于新增的功能，使用 @Nullable 来处理可能为 `null` 的字段，用 Optional 来包装可能为 null 的函数返回值。

## **语法与逻辑**

- 局部变量类型尽量使用 `var`
- 使用四格空格缩进

```java
<<<<<<< HEAD
public class SomeClass{
    public static void main(String[] args){
=======
public class SomeClass
{
    public static void main(String[] args)
    {
>>>>>>> 62e06321ae985da768a83380e459062b30bc97fc
        if(condition) {
            System.out.println("Hello World");
        }
    }
}
```
<<<<<<< HEAD

=======
- 使用k&r格式
```java
if () {

} else {

}
```
>>>>>>> 62e06321ae985da768a83380e459062b30bc97fc
- 不要嵌套过多

```java
<<<<<<< HEAD
// 错误示范public static void main(String[] args) {
=======
// 错误示范
public static void main(String[] args) 
{
>>>>>>> 62e06321ae985da768a83380e459062b30bc97fc
    if (condition) {
        doTask();
        doTask2();
        if(condition2){
            doTask3();
        }
    }
}
<<<<<<< HEAD
// 正确示范public static void main(String[] args) {
=======
// 正确示范
public static void main(String[] args) 
{
>>>>>>> 62e06321ae985da768a83380e459062b30bc97fc
    if (!condition) {
        return;
    }
    doTask();
    doTask2();
    if(!condition2){
        return;
    }
    doTask3();
}
```

- 尽量不要使用递归
- 函数签名必须使用 `throws` 注明可能会产生的异常

## **设计与设计模式**

- 不要过度使用设计模式
- 不同的服务、控制器、模型等类按功能分类，放到子包里去

```
例如我有两个积分相关的服务和一个图书服务，那么我可以这样组织
com.example.service
    - book
        - BookService.java
    - point
        - PointService.java
        - PointRecordService.java

```

- 不要在一开始给每个服务新建接口，直到出现多个实现的时候再提取成接口
- 几乎没有什么必要使用工厂模式和单例模式
- 历史原因，我们依旧使用 `@Autowired` 注入服务
- 尽量链式调用
- 使用 `BeanUtils.copyProperties` 来拷贝对象
- 如果A类与B类可以转化，但是需要额外的转化逻辑，那么转化的函数应该写在这两个类里面

```java
// 实际上，from 函数已经在接口  中定义了@Data
class UserModel {
     String name;
     Integer age;
     String realName;
     String token;
}

@Data
class UserInfoRes {
    String name;
    Integer age;
    String tokenInfo;
    public static UserInfoRes <T>from(T copyFrom) {
        var result = new UserInfoRes();
        BeanUtils.copyProperties(copyFrom, result);
        result.setTokenInfo(Utils.tokenToInfo(copyFrom.getToken()));
        return result;
    }
}

```

# **Spring Boot**

## **基本**

- 尽可能使用 `CommonResult` 包装所有返回
- 给请求体对象和响应体对象中，模糊不清的字段添加 OpenApi 注解，表明这个字段是做什么的 （如果你无法确定是否模糊不清，那么就是模糊不清的）

## **数据库 CRUD**

### **Mapper 命名**

- 新的 Mapper 应该都以 Mapper 结尾，而不是 Dao。

### **N + 1**

新的代码应避免 N + 1 查询。

```java
// 反例public List<ShitInfo> grabShitInfos(List<Integer> shitId) {
    var result = new List<ShitInfo>();

// 1次查询var shits = shitService.selectList(shitId);

// N 次查询for (var s : shits) {
        var info = shitInfoService.selectByShitId(s.id);
        shits.add(info);
    }
}

```

对于这种 A 对 B 的情况，也就是 A 模型有个 `bid` 指向 B，你可以使用表连接，但是你也可以先获取 `bid` 列表：

`通过 bid 列表获取到所有的 B，再把 B 的数据和 A 整合。`

虽然这样也会出现多次查询，但是总比 N + 1 查询来的好。而且比较简单。

### **表连接**

- 尽量不要使用 Mybatis-plus Join 库来进行表连接。
- 使用 xml 会相对简单。

## **控制器**

- 控制器使用 `@RestController` 注解
- 使用 `@ApiOperation(value = "接口描述")` 注解
- 使用 `@PathVariabl("传参名称"")` 注解
- 使用 `@Api(tags = "标签")` 注解
- 使用 `CommonResult` 类包装返回结果
- 记住，当接口有传参时，应当使用 @Requestbody 和 @RequestParam 来声明该传参需要从请求体中获取
- 为了简化代码，最好使用 @{Method}Mapping，而不是 @RequestMapping
- 使用 json 比使用表单更好
- 参数的 Swagger 文档注释可以使用 `@ApiParam`

```java
@Slf4j
@RestController
@RequestMapping("api/front/article")
@Api(tags = "文章")
public class ArticleController{
    @Autowired
    private ArticleService articleService;

    @Autowired
    private CommunityFrontService communityFrontService;

    @ApiOperation("文章分类列表")
    @GetMapping("/category/list")
    public CommonResult<List<ArticleCategoryResponse>> getCategoryList(@ApiParam("分类组 id") Integer id) {
        return CommonResult.success(articleCategoryService.getFrontList(id));
    }

    @ApiOperation(value = "社区笔记发现列表")
    @GetMapping("/discover/list")
    public CommonPage<CommunityNoteFrontPageResponse> findDiscoverNoteList(@ModelAttribute @Validated CommunityNoteFrontDiscoverRequest request) {
        return CommonPage.restPage(communityFrontService.findDiscoverNoteList(request));
    }

}

```

## **注解地狱**

关于注解的用法可以通过全局搜索搜到，这里不多赘述

### **常用注解**

### **基本**

- @Slf4j: 自动生成 logger 对象

### **服务类**

- @Service: 标记一个类为服务类，由 Spring 容器管理
- @Autowired: 自动装配依赖的 bean
- @Transactional: 用于需要事务管理的服务类函数
- @Async: 标记该函数为异步执行
- @Cacheable: 缓存函数的返回值
- @Scheduled: 使该函数变成定时任务
- @Value: 注入配置文件中的值
- @ConfigurationProperties: 批量注入配置文件中的属性

### **数据类**

- `@EqualsAndHashCode(callSuper = boolean)`: 生成 `equals()` 和 `hashCode()`. 当 `callSuper` 为 `false` 时，生成的函数不调用父类函数
- `@Accessors(chain = true)`: 允许链式调用 `setter`
- `@ApiModel(value = "名称", description = "描述")`: Swagger 注解，描述该数据类
- `@ApiModelProperty("名称")`: Swagger 注解，描述数据类字段

注：如果某数据类与控制器参数无关，那么 Swagger 注解可以省略

### **控制器**

<<<<<<< HEAD
- @ResponseBody: 告诉 Spring 将函数的返回值直接写入 HTTP 响应体（response body），而不是寻找视图（view）
- @RestController: 组合注解，等同于 @Controller 和 @ResponseBody
- @RequestMapping: 映射 Web 请求，可用于类或函数级别 （大部分可用 @MethodMapping 简化）
- @GetMapping: 处理 GET 请求的映射
- @PostMapping: 处理 POST 请求的映射
- @PutMapping: 处理 PUT 请求的映射
- @DeleteMapping: 处理 DELETE 请求的映射
- @PathVariable: 获取 URL 中的动态参数
- @RequestParam: 获取请求参数的值
- @RequestBody: 标记在接口对象传参中，表明该传参是请求体中的 json 数据
- @ModelAttribute: 标记在接口对象传参中，表明该传参是请求体中的表单数据 (如 x-www-form-urlencoded)
- @Valid: 启用验证
- @ApiOperation: Swagger 注解，描述接口功能
- @ApiParam: Swagger 注解，描述接口参数

### **数据验证**

1. `@NotNull` 注解 作用类型 解释 任何类型 属性不能为 `null`
2. `@NotEmpty` 集合 集合不能为 `null`，且 `size` 大于 `0`
3. `@NotBlank` 只能作用在 `String` 上，不能为 `null`，而且调用 `trim()` 后，长度必须大于 `0`
4. `@AssertTrue` `Boolean、boolean` 布尔属性必须是 `true`
5. `@Min` 数字类型（原子和包装） 限定数字的最小值（整型）
6. `@Max` 同 `@Min` 限定数字的最大值（整型）
7. `@DecimalMin` 同 `@Min` 限定数字的最小值（字符串，可以是小数）
8. `@DecimalMax` 同 `@Min` 限定数字的最大值（字符串，可以是小数）
9. `@Range` 数字类型（原子和包装） 限定数字范围（长整型）
10. `@Length` 字符串 限定字符串长度
11. `@Size` 集合 限定集合大小
12. `@Past` 时间、日期 必须是一个过去的时间或日期
13. `@Future` 时期、时间 必须是一个未来的时间或日期
14. `@Email` 字符串 必须是一个邮箱格式
15. `@Pattern` 字符串、字符 正则匹配字符串
=======
#### 数据验证
1. `@NotNull`    注解	作用类型	解释	任何类型	属性不能为 `null`
2. `@NotEmpty`	集合	集合不能为 `null`，且 `size` 大于 `0`
3. `@NotBlank`	只能作用在 `String` 上，不能为 `null`，而且调用 `trim()` 后，长度必须大于 `0`
4. `@AssertTrue`	`Boolean、boolean`	布尔属性必须是 `true`
5. `@Min`	数字类型（原子和包装）	限定数字的最小值（整型）
6. `@Max`	同 `@Min`	限定数字的最大值（整型）
7. `@DecimalMin`	同 `@Min`	限定数字的最小值（字符串，可以是小数）
8. `@DecimalMax`	同 `@Min`	限定数字的最大值（字符串，可以是小数）
9. `@Range`	数字类型（原子和包装）	限定数字范围（长整型）
10. `@Length`	字符串	限定字符串长度
11. `@Size`	集合	限定集合大小
12. `@Past`	时间、日期	必须是一个过去的时间或日期
13. `@Future`	时期、时间	必须是一个未来的时间或日期
14. `@Email`	字符串	必须是一个邮箱格式
15. `@Pattern`	字符串、字符	正则匹配字符串
>>>>>>> 62e06321ae985da768a83380e459062b30bc97fc
