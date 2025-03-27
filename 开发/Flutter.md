# Flutter

> 查看此文档前，请先查看 [通用开发指南](../通用开发指南.md)

公司主要使用 Android Studio。

必须使用 fvm 进行 Flutter 版本管理。[简易教程](https://medium.com/@ahmedawwan/flutter-version-management-a-guide-to-using-fvm-dbe1d269f565)

# 氨醛 (安全)

* 使用安全的存储方式处理敏感数据
* 不要硬编码

# 结构与设计

## 语法

* 使用两格缩进

## 代码结构

* 在 pubspec.yaml 添加要使用的库和文件
* 在 pages 文件夹添加页面文件
* 在 assets 文件夹添加本地资源
* 在 network 添加网络和状态相关的文件
* 在 widgets 文件夹中添加需复用的自定义组件
* 其中，response.dart 用来放置响应模型类，request.dart 放置请求模型类，api.dart 放置从后端获取信息的方法

## 页面样式

* 各个页面的样式应该在整体上保持统一并且美观
* 不要使用默认样式，也不要使页面与默认样式类似
* 关于页面的文字，应使用 FastWidget.getText() 来显示文字，便于整个app统一文字字体
* 关于选色，必须使用 CommonStyles 里的主题色
* 对于页面的下拉刷新与上拉加载功能，应首选使用 EasyRefresh
* 对于 EasyRefresh 的 Header 和 Footer ，必须使用 common_list.dart 内的样式
* 对于一些非 EasyRefresh 的加载动画，优先使用 MyLoading 中的加载动画
* 如果使用预设的样式后不美观，可以考虑自定义一个
* 使用 EmptyWidget 来填充列表为空的页面
* 对于 Appbar 的样式，应该使用 MyAppBar

## 排版

* 页面中的元素不应该太多或者太杂，以传达信息易于接受和理解优先

## 页面结构

* 在二级三级以下的子页面，最好尽量让父页面把子页面需要的数据传进去，而不是直接让子页面自己获取
* 类似堆积木，将页面划分为多个组件，合理组织页面结构，按照布局**明确**分组

## 统一的组件和样式

### 常用组件

App 内常用或者封装后的组件在 compoment 文件夹里，以下是一些 App 内常用的组件的说明，具体使用方法请参考项目文件

* 文字 `AppWidget.text()`
  用于创建文本 Widget，使用方法与 Text() 组件基本一致，但是需要传入 style 来保证样式统一
* 输入框 `AppInputWidget()`
  使用方法与 TextField() 一致，但是固定高度
* 搜索框 `AppWidget.buildSearchBar()`
  用于创建搜索框
* 行与列空隙 `AppWidget.blankWidth()` 和 `AppWidget.blankHeight()`
  用于在列表视图中创建空隙，非必要请使用边距来实现
* 网络图片 `AppWidget.getNetworkImage()`
  用于获取网络图片，自带加载动画和 errorBuilder，以及支持直接进入图片画廊
* 分割线 `AppWidget.buildDivider()`
  用于创建分割线组件
* Toast 轻提示 `Util.showToast`
  用于显示 Toast 提示
* 弹窗 `Util.showAlternativeDialog` 和 `Util.showMessageDialog`
  用于显示弹窗
* 列表选择器 `showListPicker`
  用于显列表示选择弹窗  
  ...  
* 更多组件请参考 `compoment` 文件夹下的文件

### 样式

App 内的常用样式主要定义在 `common_styles.dart` 文件中。

### 边距规范  

在 `common_styles.dart` 文件中，`AppMargin` 和 `AppPadding` 类定义了一系列常用的边距参数。  

#### 命名规则  
除了通用的 **页面水平边距** `pageMargin` 之外，其余边距参数的命名遵循以下格式：  
```
[AppMargin / AppPadding] + . + [all / horizontal / vertical / top / bottom] + [Margin / Padding] + [01 ~ 07]

//使用例
//水平外边距04
AppMargin.horizontalMargin04

//仅底部内边距02
AppPadding.bottomPadding02
```


- `01` 代表最小边距，`07` 代表最大边距，数字越大，边距越大。  
- **页面内通用的水平边距** 为：`AppMargin.horizontalPageMargin`。

####  使用规范
> **规范使用示例：**  
> 在创建滚动视图页面时，绝大部分情况下，应确保页面视图与组件的外边距统一：

```dart
ListView(
  padding: AppMargin.horizontalPageMargin, // 在最外层组件设置统一的页面内边距
  children: List.generate(20, (index) {
    return Container(
      height: 80.w,
      margin: AppMargin.bottomMargin03, // 仅设置底部外边距，保证间距统一
      decoration: BoxDecoration(
        color: Colors.white,
        borderRadius: AppBorderRadius.borderRadiusMedium,
      ),
    );
  }),
)
```

在少数情况下，若需要获取边距的具体数值，可通过 `AppMarginValue` 或 `AppPaddingValue` 获取，例如：  
* 通过 `AppMarginValue.pageMargin` 来获取 `AppMargin.horizontalPageMargin` 的具体数值
* 通过 `AppPaddingValue.padding01` 来获取 `AppPadding.allPadding01` 的具体数值

> **错误示例：**  
> 不应该为每个组件单独设置相同的 `margin`，这样会导致维护困难：

```dart
Container(
  margin: EdgeInsets.only(
    left: AppMarginValue.pageMargin,
    right: AppMarginValue.pageMargin,
    bottom: AppMarginValue.margin03,
  ),
)

```

### 圆角

圆角样式定义在 `AppBorderRadius` 和 `AppRadius` 类中，使用方式与边距样式一致。
#### 命名规则  
```dart
[AppBorderRadius / AppRadius] + . + [borderRadius / radius] + [01 ~ 05]
```
- **最常用**的圆角样式为 `AppBorderRadius.borderRadius03`  
- 其中，要使用 `AppBorderRadius` 还是 `AppRadius` 需要根据组件的需要的参数类型决定  
- 为了保证圆角样式统一，无论是 `AppBorderRadius` 还是 `AppRadius` ，如果末尾数字一致，那么 `AppBorderRadius` 和 `AppRadius` 的圆角大小也一致  

同样地，需要获取圆角样式的具体数值大小时，通过 `AppBorderRadiusValue` 获取
```dart
// 获取 AppBorderRadius.borderRadius03 的数值
AppBorderRadiusValue.borderRadius03

// 获取 AppRadius.radius05 的数值
AppBorderRadiusValue.borderRadius05
```

## 自适应


### 通过 ScreenUtil 包进行自适应

[官方文档](https://github.com/OpenFlutter/flutter_screenutil/blob/master/README_CN.md)
项目通过 ScreenUtil 来保证在各个设备上保持相近的显示效果
来品购 APP 的设计尺寸是 375*800 (单位: dp)，项目已经根据设计尺寸初始化了 ScreenUtil

```dart
...
child: ScreenUtilInit(
    designSize: const Size(375, 800),
...
```

所以在创建一个屏幕宽度的组件时，例如一个高度为1的黑色长条，需要这么写

```dart
Container(
	width: 375.w,
	height: 1,
	color: Colors.black
)
```

### 通过  Flexible 组件 在 Column 或 Row 中 按比例分配组件尺寸

在 Row 中，如果需要创建三个子组件按不同的比例分配空间：

```dart
Row(
  children: [
    Flexible(
      flex: 1, //占比 1
      child: Container(
        color: Colors.red,
      ),
    ),
    Flexible(
      flex: 2, //占比 2
      child: Container(
        color: Colors.green,
      ),
    ),
    Flexible(
      flex: 1, //占比 1
      child: Container(
        color: Colors.blue,
      ),
    ),
  ],
);
```

### （建议使用 Flexible）通过 Expanded 组件 在 Column 或 Row 中 按比例分配组件尺寸

Expanded 在确定高度的 Column 和 确定宽度的 Row 中 类似于 Flexible，它的默认 flex 参数的值为 1
在滚动视图中使用这种写法需要确保组件在滚动方向上确定组件的长度（建议配合 ScreenUtil 确定长度），否则会爆出尺寸未定义的错误
如果在一个 Row 中创建三个等分宽度，高度为10的黑色矩形，应该这么写

```dart
...
Row(                          
  children: [                 
    Expanded(                 
        child: Container(     
          height: 10.w,       
          color: Colors.black,
        )),                    
    Expanded(                 
        child: Container(     
          height: 10.w,       
          color: Colors.black,
        )),                    
    Expanded(                 
        child: Container(     
          height: 10.w,       
          color: Colors.black,
        )),                    
  ],                          
),
```

当在滚动视图中创建一个需要创建三个分别占 Column 高度比例为 2: 1: 1 的红绿蓝三色矩形的 Column 时应该这么写

```dart
SizedBox(                       
  height: 400,  //确定高度，防止在滚动视图中组件无法获取尺寸信息
  child: Column(                
    children: [                 
      Expanded(                 
          flex: 2,              
          child: Container(     
            color: Colors.red,  
          )),                    
      Expanded(                 
          flex: 1,              
          child: Container(     
            color: Colors.green,
          )),                    
      Expanded(                 
          flex: 1,              
          child: Container(     
            color: Colors.blue, 
          )),                    
    ],                          
  ),                             
),
```

### 使用 LayoutBuilder 进行自适应

LayoutBuilder 可以根据父组件的实际尺寸动态调整子组件的大小。这对于一些更加复杂的布局，或需要动态适应父容器尺寸的场景非常有用。

例如，根据父容器的宽度来生成一行四个动态尺寸的正方形组件：

```dart
LayoutBuilder(
  builder: (context, constraints) {
    double screenWidth = constraints.maxWidth;
    return Row(
      children: List.generate(4, (index) {
        return Container(
          color: Colors.white,
          width: screenWidth / 4,
          height: screenWidth / 4,
        );
      }).toList(),
    );
  },
);
```

### (不推荐) 通过 MediaQuery 获取屏幕信息进行自适应

在一些情况下可能不得不需要使用 MediaQuery. 如果非必要, 请根据 ScreenUtil 的写法来实现自适应.

通过MediaQuery.of(context).size 获取屏幕尺寸信息，例如

```dart
get screenWidth => MediaQuery.of(context).size.width; //获取屏幕宽度
get screenHeight => MediaQuery.of(context).size.height;//获取屏幕高度
```

通过屏幕尺寸进行换算来确定组件大小，例如一行有四个正方形的Row

```dart
//错误实现，固定尺寸导致屏幕宽度不够时会布局溢出，或者不能填满屏幕宽度
Row(
	children: List.generate(4, (index) {
		return Container(
			color: Colors.white,
			width: 200,
			height: 200,
		);
	}).toList()
)
```

```dart
//正确实现
get screenWidth => MediaQuery.of(context).size.width; //获取屏幕宽度

Row(
	children: List.generate(4, (index) {
		return Container(
			color: Colors.white,
			width: screenWidth / 4,
			height: screenWidth / 4,
		);
	}).toList()
)
```

## 安全区

get screenPadding => MediaQuery.of(context).padding; //获取屏幕安全区边距
例如，在挖孔屏设备里获取前置摄像头所占屏幕高度
get cameraHeight => MediaQuery.of(context).padding.top;

或者使用 SafeArea() 组件，使用方式参照 flutter 文档

# 逻辑

* 选择适合项目规模的状态管理方案（如Provider、Bloc、Riverpod等）
* 避免在Widget树中传递大量参数，使用依赖注入或InheritedWidget

```Dart
// 错误
class CounterWidget extends StatefulWidget
{
  @override
  _CounterWidgetState createState() => _CounterWidgetState();
}

class _CounterWidgetState extends State<CounterWidget>
{
  int _counter = 0;

  void _incrementCounter()
  {
    setState(()
    {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context)
  {
    return Column(
      children: [
        Text('Count: $_counter'),
        ElevatedButton(
          onPressed: _incrementCounter,
          child: Text('Increment'),
        ),
      ],
    );
  }
}

// 正确
// 使用Provider进行状态管理
class CounterModel extends ChangeNotifier
{
  int _counter = 0;
  int get counter => _counter;

  void increment()
  {
    _counter++;
    notifyListeners();
  }
}

class CounterWidget extends StatelessWidget
{
  @override
  Widget build(BuildContext context)
  {
    return Consumer<CounterModel>(
      builder: (context, model, child)
      {
        return Column(
          children: [
            Text('Count: ${model.counter}'),
            ElevatedButton(
              onPressed: model.increment,
              child: Text('Increment'),
            ),
          ],
        );
      },
    );
  }
}
```

* 避免通过多个widget层级传递数据或对象
* 提供一种在widget树中共享和访问数据的函数
* 使状态管理更加集中和可控

```Dart
// 购物车服务案例：假设我们有另一个购物应用，需要在多个页面中访问购物车数据

// 购物车服务
class CartService extends ChangeNotifier
{
  final List<String> _items = [];

  List<String> get items => _items;

  void addItem(String item)
  {
    _items.add(item);
    notifyListeners();
  }

  void removeItem(String item)
  {
    _items.remove(item);
    notifyListeners();
  }
}

// 在应用的顶层提供这个模型
import 'package:provider/provider.dart';

class MyApp extends StatelessWidget
{
  @override
  Widget build(BuildContext context)
  {
    return ChangeNotifierProvider(
      create: (context) => CartService(),
      child: MaterialApp(
        title: 'Shopping App',
        home: HomePage(),
      ),
    );
  }
}

// 其他组件使用的时候就比较方便了
class HomePage extends StatelessWidget
{
  @override
  Widget build(BuildContext context)
  {
    return Scaffold(
      appBar: AppBar(title: Text('Home')),
      body: Center(
        child: ElevatedButton(
          child: Text('Go to Cart'),
          onPressed: () => Navigator.push(
            context,
            MaterialPageRoute(builder: (context) => CartPage()),
          ),
        ),
      ),
      floatingActionButton: AddToCartButton(),
    );
  }
}

class AddToCartButton extends StatelessWidget
{
  @override
  Widget build(BuildContext context)
  {
    return FloatingActionButton(
      child: Icon(Icons.add),
      onPressed: () {
        // 使用 Provider 来访问 CartModel
        Provider.of<CartModel>(context, listen: false).addItem('New Item');
      },
    );
  }
}

class CartPage extends StatelessWidget
{
  @override
  Widget build(BuildContext context)
  {
    return Scaffold(
      appBar: AppBar(title: Text('Cart')),
      body: Consumer<CartModel>(
        builder: (context, cart, child)
        {
          return ListView.builder(
            itemCount: cart.items.length,
            itemBuilder: (context, index)
            {
              return ListTile(
                title: Text(cart.items[index]),
                trailing: IconButton(
                  icon: Icon(Icons.remove),
                  onPressed: () => cart.removeItem(cart.items[index]),
                ),
              );
            },
          );
        },
      ),
    );
  }
}
```

# 封装

* 如果你把 Widget 封装起来了，请你一定要写文档注释，至少把默认用法写上。

## 遇到过的问题

* 如果你在启动项目的时候项目白屏，请首先检查 C 盘有没有剩余 12 个 G，然后再到模拟器内把 app 的应用数据清了，最后再启动项目一次
  
