# 基本
## 后台后端
a.sd9966.com

* [后台后端文档](https://a.sd9966.com/doc.html)

## 用户后端
u.sd9966.com

* [用户后端文档](https://u.sd9966.com/doc.html)

## 管理
adm.sd9966.com

## 商户
mer.sd9966.com

## 用户端（旧版）
app.sd9966.com

* 开发用 - 用户端万能验证码：649053

# 后端

* 为了方便开发，后端统一使用 UTC+8

## 后端四项

Common存放模型类，就是一堆没逻辑的类
Service存放主要逻辑，放的都是服务类（要么是 Utils，要么是注入到 Spring Boot 的组件或服务）
Admin存放与管理有关的控制器
User存放与用户端有关的控制器

## 依赖关系

Service依赖Common
Admin和User依赖Service以及Common
