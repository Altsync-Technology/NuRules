# 开发准则
本仓库用于存放各个语言的开发准则，可以自由查看。

开发本组织产品时，必须大部分遵守这些准则。

**必看 Java.md**

# 必看

要添加新功能或者修复bug，现在要用分支。每一个分支维护一个主题，直到这个主题做完再合并

分支名称格式
[类型]/[具体需求]

```
例如
bugfix/修复首页无法滚动的问题
feature/个人页面
update/更新到Java 21
```

当分支工作完毕，你需要归档分支。用 tag 来归档分支。
```bash
# 如果你的分支在本地
git tag archive/功能实现/历史记录 功能实现/历史记录

# 如果分支在远程，那么指定的分支前面还需要加 remote/
git tag archive/功能实现/历史记录 remotes/origin/功能实现/历史记录

# 不要忘记推送标签和删除分支
git push --tags
git push origin --delete 功能实现/历史记录
git branch -d 功能实现/历史记录
```

参见 [Github 工作流](https://docs.github.com/zh/get-started/using-github/github-flow)

# 注意事项
* 为了方便开发，后端统一使用 UTC+8，前端按需处理


