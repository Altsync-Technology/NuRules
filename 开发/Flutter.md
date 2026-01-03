# Flutter

> 查看此文档前，请先查看 [通用开发指南](../通用开发指南.md)

公司主要使用 Android Studio。

必须使用 fvm 进行 Flutter 版本管理。[简易教程](https://medium.com/@ahmedawwan/flutter-version-management-a-guide-to-using-fvm-dbe1d269f565)

# 氨醛 (安全)

* 使用安全的存储方式处理敏感数据
* 不要硬编码

# 结构与设计

## 规范

* 使用两格缩进
* 不要省略大括号
* 使用英文为变量命名
* 使用卫语句减少代码复杂度

## 代码结构
* `/.run` - 存放运行配置相关的文件
* `/android` - 存放与 Android 平台配置相关文件，以及 `PlatformChannel` 相关的代码
* `/assets` - 存放应用所需资源文件
* `/ios` - 存放与 iOS 平台配置相关文件
* `/lib` - 主要存放 Flutter 项目代码
  * `/common` 主要用于存放常量数据文件
  * `/component` 用于存放应用自定义组件文件
  * `/controller` 用于存放应用控制器相关代码
  * `/ext` 存放（包，类，函数等的）扩展文件
  * `/gen` 存放由代码生成工具生成的代码文件
  * `/pages` 存放页面文件
  * `/service` 存放应用服务相关文件
  * `/swagger` 存放由代码生成工具生成的 Api 相关代码文件
  * `/unclassified` 存放早期项目无法进行分类的代码文件
  * `/utils` 存放工具类相关的代码文件
  * `app_bindings.dart` 应用依赖管理相关文件
  * `controller_bindings.dart` 应用控制器管理相关文件
  * `service_bindings.dart` 应用服务管理相关文件
  * `route.dart` 应用路由相关配置文件
  * `main.dart` 应用主入口文件文件
* `/linux` - 存放 liunx 平台配置相关文件
* `/macos` - 存放 macOS 平台 的原生代码目录
* `/script` - 存放项目开发中所需使用的脚本文件
* `/tools`-  存放项目开发中所需使用的工具文件
* `/web` - 存放与 Web 平台配置相关文件
* `/windows` - 存放与 Windows 平台配置相关文件
* `analysis_options.yaml` - Dart Linter 配置文件
* `build.yaml` - Flutter Builder 代码生成的配置文件
* `devtools_options.yaml` - Dart & Flutter DevTools 的调试配置文件
* `fgen.yaml` - flutter_asset_generator 的代码生成配置文件
* `pubspec.yaml` - Flutter 项目核心配置文件
* `sync.toml` - SimpleSync 工具配置文件

## 页面样式

* 各个页面的样式应该在整体上保持统一并且美观
* 不要使用 Material 默认样式，也不要使页面与默认样式类似
* 关于页面的文字，应使用 FastWidget.text() 来显示文字，便于整个app统一文字字体
* 关于选色，必须使用 CommonStyles 里的主题色
* 对于页面的下拉刷新与上拉加载功能，应首选使用 EasyRefresh
* 对于 EasyRefresh 的 Header 和 Footer ，必须使用 common_list.dart 内的样式
* 对于一些非 EasyRefresh 的加载动画，优先使用 `AppScaffold` 中的加载动画实现，或 `AppLoading` 中的加载动画
* 如果使用预设的样式后不美观，可以考虑自定义一个
* 使用 EmptyWidget 来填充列表为空的页面
* 如非必要，使用 `AppScaffold` 的 AppBar 相关实现来实现页面内的顶部栏
* 在页面获取必要数据失败时，使用 `AppScaffold` 中的错误信息显示实现来显示错误信息

## 排版

* 页面中的元素不应该太多或者太杂，以传达信息易于接受和理解优先

## 页面结构

* 在二级三级以下的子页面，最好尽量让父页面把子页面需要的数据传进去，而不是直接让子页面自己获取
* 类似堆积木，将页面划分为多个组件，合理组织页面结构，按照布局**明确**分组

## 统一的组件和样式

### 常用组件

App 内常用或者封装后的组件在 `compoment` 文件夹里，以下是一些 App 内常用的组件的说明，具体使用方法请参考项目文件或者项目其他文件的使用方式
> 如无必要单独编写一个无法复用的组件，请使用项目内封装后的组件来保证项目内代码复用以及可维护性

* 页面骨架 `AppScaffold`  
  用于创建项目使用的页面骨架，用法与 `Sacffold()` 组件类似，但是支持**背景显示**，**加载动画**，以及**错误处理**
* 文字 `AppWidget.text()`  
  用于创建文本 Widget，使用方法与 Text() 组件基本一致，但是需要传入 style 来保证样式统一
* 输入框 `AppInputWidget()`  
  使用方法与 TextField() 一致，但是固定高度
* 搜索框 `AppWidget.buildSearchBar()`  
  用于创建搜索框
* 行与列空隙 `AppWidget.blankWidth()` 和 `AppWidget.blankHeight()`  
  用于在列表视图中创建空隙，非必要请使用边距来实现
* 网络图片 `AppWidget.getNetworkImage()`  
  用于获取网络图片，自带**加载动画**，**错误处理**，**图片缓存操作**，以及支持直接进入图片画廊
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

#### 使用规范

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

## 页面路由
本项目统一采用 [GetX](https://pub.dev/packages/get) 包进行页面路由，具体使用方式参考官方文档

### 可重载页面机制（推荐使用）
为了提升页面的可维护性和容错性，项目中引入了统一的「可完全重载页面机制」。该机制允许页面在运行时重新构建自身，常用于 获取必要数据时发生错误 或 因致命错误而必须重载整个页面 等场景。  
  
> 页面重载的作用:  
> 当页面出现加载失败等情况时，如果页面是通过 `Get.toReloadable()` 打开的，即可在 `AppScaffold` 错误处理组件中自动显示「重新加载」按钮来完全重载页面，或者手动调用 `Get.reloadThisPage()` 来进行处理。

建议统一使用以下方式路由页面来保证此机制可用：

* `Get.toReloadable(() => NewPage())`  
* `Get.offReloadable(() => NewPage())` 

其他信息：  

* `Get.canReload` 用于获取当前页面是否可以使用此机制  
* `Get.reloadThisPage()` 触发页面完全重载
* `Get.backToHomeWithTab()` 退出当前导航栈中所有页面并返回首页（的指定页面）

## 数据缓存/持久化

### 一般数据
项目内使用包 [SharedPreferences](https://pub.dev/packages/shared_preferences) 实现一般数据缓存和持久化操作。  

在 `DataCacheService` 内已封装好大部分常见数据类型的储存函数，并将统一存储键常量配置在 `CacheKeys` 内，具体使用方法请参考文件内的文档。

### 图片
项目内网络图片组件 `AppWidget.getNetworkImage` 使用包 [CachedNetworkImage](https://pub.dev/packages/cached_network_image) 来进行网络图片展示以及数据缓存操作的实现，并且使用了自定义 `DefaultImageCacheManager` 来覆写原包内相关的图片缓存行为

## 自适应

### 通过 ScreenUtil 包进行自适应

[官方文档](https://github.com/OpenFlutter/flutter_screenutil/blob/master/README_CN.md)
项目通过 ScreenUtil 来保证应用在各个设备上保持相近的显示效果
来品购 APP 的设计尺寸是 375*800 (单位: dp)，项目已经根据设计尺寸初始化了 ScreenUtil

```dart
  ///设计尺寸，配合 ScreenUtil 使用
  ///
  ///使用例: `Global.designSize.width.w`
  static Size designSize = Size(375, 800);
```

所以在创建一个屏幕宽度的组件时，例如一个高度为1的黑色长条，需要这么写

```dart
Container(
	width: 1.sw, //375.w 可以获得同样的效果，但是不利于代码长期维护
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

`get screenPadding => MediaQuery.of(context).padding; //获取屏幕安全区边距`  
例如，在挖孔屏设备里获取前置摄像头所占屏幕高度  
`get cameraHeight => MediaQuery.of(context).padding.top;`

或者使用 SafeArea() 组件，使用方式参照 Flutter 官方文档

## 目录
项目在 `DirectoryPaths` 统一配置目录相关的常量，并使用 `DirectoryUtil` 来统一获取目录对象  

例：
```dart
//获取临时文件目录对象
final tempDir = await DirectoryUtil.getTempDir();
... 后续使用目录对象进行操作

//获取图片缓存目录对象
final imageCacheDir = await DirectoryUtil.getImageCacheDir();
...
```

## 日志
使用项目 `util.dart` 内的 `Logger` 来显示信息，而非 `print`
```dart
final Logger log = Logger(printer: CustomLogPrinter());

extension LoggerExt on Logger {
  void err(dynamic e, [StackTrace? trace]) {
    this.e(e, stackTrace: trace);
  }
}
```

使用例
```dart
} on AppNetworkException catch(e) {
  Util.showToast(e.message);
  log.err(e); // 使用 Logger 记录日志
} catch(e, trace) {
  log.err(e, trace); // 使用 Logger 记录详细日志
}
```

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

