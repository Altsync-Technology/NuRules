# Nodejs
允许使用 Intellij Webstorm / Nvim / VSCODE 战斗进化 开发。

公司主要使用 Intellij Webstorm。

新手应使用 Intellij Webstorm。

# JavaScrpt 部分
* 不要奉 JavaScript 为神

## 基本
* 除了回调或者匿名函数，不要使用 Lambda 表达式。
```JavaScript
// 你如果这么写，你是个臭逼
let func = async (param) => { /* ... */ }

// 你应该
async function func(param) 
{
    /* ... */
}

let obj = 
{
    prop: 42,
    // 如果你这么写，你应该辞职
    doSomething: () => {
        this.prop++
    },

    // 正确写法
    doSomethingRight: function() {
        this.prop++
    }
}
```

## 逻辑
* 大部分与 Java 一样，例如避免嵌套
* 永远不要使用 `var`。局部变量使用 `let`，常数使用 `const`。
* 别他妈搞一大堆 Javascript 花样
```javascript

// 最糟糕的对象定义方法
function 函数是我亲爹() {}
函数是我亲爹.prop = 42

// 你应该
let 对象 = {
    prop: 42
}

```

## 格式
* 不要使用 Prettier
* 可以适当使用 Eslint
* 使用四格空格缩进
* 与 Java 一样，除了匿名函数怎么好读怎么来以外，其他地方大括号另起一行
* 尽量标注类型信息 (虽然我估计大部分情况下做不到)