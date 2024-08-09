# Flutter
必须使用 Intellij 系列 ide 开发。

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

# 环境要求
* Flutter: 3.22.0
* Dart: 3.40
* Android Studio

# 逻辑
* 大部分与 java 一致
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

## 遇到过的问题
* 如果你在启动项目的时候项目白屏，请首先检查 C 盘有没有剩余 12 个 G，然后再到模拟器内把 app 的应用数据清了，最后再启动项目一次
