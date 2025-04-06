# 六大框架上下文与环境比较

本文档对比Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue六大框架中上下文与环境的实现和特性，从以下维度进行分析：

- 上下文定义与概念
- 上下文获取方式
- 上下文传递机制
- 上下文作用域
- 依赖注入支持
- 生命周期绑定

每个框架部分包含平台特性、兼容性分析和完整实例代码。

## 目录

1. [Flutter 上下文与环境](#flutter-上下文与环境)
2. [Android Jetpack Compose 上下文与环境](#android-jetpack-compose-上下文与环境)
3. [iOS UIKit 上下文与环境](#ios-uikit-上下文与环境)
4. [iOS SwiftUI 上下文与环境](#ios-swiftui-上下文与环境)
5. [鸿蒙 ArkUI 上下文与环境](#鸿蒙-arkui-上下文与环境)
6. [Vue 上下文与环境](#vue-上下文与环境)
7. [总结对比](#总结对比)

## Flutter 上下文与环境

### 上下文定义与概念

Flutter中的`BuildContext`是一个关键概念，它代表了Widget树中特定Widget的位置：

- `BuildContext`是一个接口，实际上是Element（Widget的运行时表示）的抽象
- 它包含了Widget树的位置信息和对祖先Widget的访问能力
- 通过`BuildContext`可获取主题、媒体查询、本地化等应用级数据
- 用于创建和定位Widgets、处理导航、访问状态等
- 每个Widget的`build`方法都会接收一个`BuildContext`参数
- `BuildContext`还存储了Widget的尺寸、约束等布局信息
- 它是连接Widget层和RenderObject层的桥梁

### 上下文获取方式

在Flutter中，获取上下文的主要方式包括：

- 在`build`方法中直接使用传入的`context`参数
- 通过`StatefulWidget`的`State`类中的`context`属性获取
- 通过`GlobalKey`间接获取特定Widget的`BuildContext`
- 在回调函数中捕获并存储`context`引用
- 使用`Builder`Widget创建新的`BuildContext`
- 通过`of`静态方法（如`Theme.of(context)`）访问祖先提供的数据
- 使用`BuildContext`的扩展方法获取特定功能

### 上下文传递机制

Flutter中的上下文传递机制主要通过以下方式：

- 通过Widget树自上而下传递`BuildContext`
- 使用`InheritedWidget`实现数据的向下传递
- 通过`Provider`、`Riverpod`等状态管理库包装`InheritedWidget`简化传递
- `MediaQuery.of(context)`、`Theme.of(context)`等静态方法获取上下文相关数据
- 使用`Navigator.of(context)`等获取特定功能控制器
- `BuildContext`的扩展方法如`context.read<T>()`、`context.watch<T>()`
- 利用`ElementTree`递归向上查找特定类型的Widget

### 上下文作用域

Flutter中的上下文作用域具有以下特点：

- Widget树形成了自然的作用域层次结构
- 每个`BuildContext`都有其独立的作用域
- `InheritedWidget`定义了数据的可访问范围（子树可访问）
- 可以使用`Builder`、`StatefulBuilder`等创建新的作用域
- `context.dependOnInheritedWidgetOfExactType<T>()`会在Widget重建时订阅变化
- `context.getElementForInheritedWidgetOfExactType<T>()`只获取不订阅
- 使用`Provider`可以精确控制状态的作用域范围

### 依赖注入支持

Flutter通过多种方式支持依赖注入：

- 使用`InheritedWidget`作为基础依赖注入机制
- `Provider`包提供了更完善的依赖注入方案
- `GetIt`、`injectable`等专用依赖注入库
- 支持基于服务定位器模式的依赖注入
- `Riverpod`提供了类型安全的依赖注入方案
- 可通过`BuildContext`获取注入的依赖
- 支持单例、工厂、懒加载等依赖注入模式

### 生命周期绑定

Flutter中的上下文与生命周期的绑定包括：

- `BuildContext`与Widget的生命周期紧密相连
- 通过`WidgetsBindingObserver`监听应用生命周期
- `StatefulWidget`的`State`对象提供完整生命周期回调
- `didChangeDependencies`在`InheritedWidget`变化时调用
- 使用`FocusNode`、`ScrollController`等需手动管理生命周期
- 可以通过`context`订阅Widget树重建事件
- `LifecycleWatcher`扩展可以监听更细粒度的生命周期事件

### 平台特性与兼容性

Flutter上下文机制的特性与兼容性包括：

- 跨平台一致的上下文API，无需针对不同平台调整
- 支持Flutter运行的所有平台（iOS、Android、Web、Windows、macOS、Linux）
- 完全Flutter原生实现，不依赖平台特定功能
- 通过`MethodChannel`可以与平台原生代码通信
- `BuildContext`性能高效，适合频繁重建的UI场景
- 支持热重载，上下文变化快速反映到UI
- 特定平台功能可通过`dart:io`等库进行条件访问

### 完整示例代码

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';

// 1. 创建数据模型
class CounterModel extends ChangeNotifier {
  int _count = 0;
  int get count => _count;
  
  void increment() {
    _count++;
    notifyListeners();
  }
}

// 2. 创建主题数据
class ThemeModel extends ChangeNotifier {
  bool _isDark = false;
  bool get isDark => _isDark;
  ThemeMode get themeMode => _isDark ? ThemeMode.dark : ThemeMode.light;
  
  void toggleTheme() {
    _isDark = !_isDark;
    notifyListeners();
  }
}

// 3. 创建自定义InheritedWidget
class AppConfig extends InheritedWidget {
  final String appName;
  final String apiUrl;
  
  const AppConfig({
    Key? key,
    required this.appName,
    required this.apiUrl,
    required Widget child,
  }) : super(key: key, child: child);
  
  static AppConfig of(BuildContext context) {
    final AppConfig? result = context.dependOnInheritedWidgetOfExactType<AppConfig>();
    assert(result != null, 'No AppConfig found in context');
    return result!;
  }
  
  // 决定何时通知依赖此widget的订阅者
  @override
  bool updateShouldNotify(AppConfig oldWidget) {
    return appName != oldWidget.appName || apiUrl != oldWidget.apiUrl;
  }
}

void main() {
  runApp(
    // 4. 使用Provider包装应用，提供依赖注入
    MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (_) => CounterModel()),
        ChangeNotifierProvider(create: (_) => ThemeModel()),
      ],
      child: const MyApp(),
    ),
  );
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    // 5. 使用上下文获取主题模式
    final themeModel = context.watch<ThemeModel>();
    
    return AppConfig(
      appName: 'Context Demo',
      apiUrl: 'https://api.example.com',
      child: MaterialApp(
        title: 'Flutter Context Demo',
        theme: ThemeData.light(useMaterial3: true),
        darkTheme: ThemeData.dark(useMaterial3: true),
        themeMode: themeModel.themeMode,
        home: const HomePage(),
      ),
    );
  }
}

class HomePage extends StatefulWidget {
  const HomePage({Key? key}) : super(key: key);

  @override
  State<HomePage> createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> with WidgetsBindingObserver {
  late BuildContext _screenContext;
  
  @override
  void initState() {
    super.initState();
    // 6. 注册生命周期观察者
    WidgetsBinding.instance.addObserver(this);
  }
  
  @override
  void dispose() {
    // 7. 移除生命周期观察者
    WidgetsBinding.instance.removeObserver(this);
    super.dispose();
  }
  
  // 8. 生命周期方法
  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    print('应用生命周期状态: $state');
    if (state == AppLifecycleState.resumed) {
      // 可以在这里处理特定生命周期事件
    }
  }
  
  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    // 9. 依赖变化回调
    print('依赖发生变化');
    
    // 存储屏幕上下文引用，用于后续使用
    _screenContext = context;
  }

  @override
  Widget build(BuildContext context) {
    // 10. 通过上下文获取屏幕尺寸
    final screenSize = MediaQuery.of(context).size;
    
    // 11. 访问InheritedWidget提供的数据
    final appConfig = AppConfig.of(context);
    
    // 12. 使用Provider获取状态
    final counter = context.watch<CounterModel>();
    final themeModel = context.watch<ThemeModel>();
    
    return Scaffold(
      appBar: AppBar(
        title: Text(appConfig.appName),
        actions: [
          IconButton(
            icon: Icon(themeModel.isDark ? Icons.light_mode : Icons.dark_mode),
            onPressed: () => themeModel.toggleTheme(),
          ),
        ],
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              '屏幕宽度: ${screenSize.width.toStringAsFixed(1)}',
              style: Theme.of(context).textTheme.titleLarge,
            ),
            const SizedBox(height: 20),
            Text(
              '计数器值: ${counter.count}',
              style: Theme.of(context).textTheme.headlineMedium,
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () => counter.increment(),
              child: const Text('增加计数'),
            ),
            const SizedBox(height: 40),
            
            // 13. 使用Builder创建新的BuildContext
            Builder(
              builder: (builderContext) {
                // builderContext是一个新的BuildContext
                return ElevatedButton(
                  onPressed: () {
                    // 14. 使用上下文导航到新页面
                    Navigator.of(builderContext).push(
                      MaterialPageRoute(
                        builder: (context) => DetailPage(
                          // 传递上下文获取的数据
                          apiUrl: appConfig.apiUrl,
                        ),
                      ),
                    );
                  },
                  child: const Text('进入详情页'),
                );
              },
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          // 15. 显示带上下文的Snackbar
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text('当前API地址: ${appConfig.apiUrl}')),
          );
        },
        child: const Icon(Icons.info),
      ),
    );
  }
}

class DetailPage extends StatelessWidget {
  final String apiUrl;
  
  const DetailPage({Key? key, required this.apiUrl}) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    // 16. 获取环境数据
    final brightness = MediaQuery.of(context).platformBrightness;
    final isPortrait = MediaQuery.of(context).orientation == Orientation.portrait;
    
    // 17. 上下文主题和本地化
    final textTheme = Theme.of(context).textTheme;
    
    return Scaffold(
      appBar: AppBar(
        title: const Text('详情页'),
        leading: IconButton(
          icon: const Icon(Icons.arrow_back),
          onPressed: () => Navigator.of(context).pop(),
        ),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              '平台亮度: $brightness',
              style: textTheme.titleMedium,
            ),
            const SizedBox(height: 10),
            Text(
              '屏幕方向: ${isPortrait ? "竖屏" : "横屏"}',
              style: textTheme.titleMedium,
            ),
            const SizedBox(height: 20),
            Text(
              'API地址: $apiUrl',
              style: textTheme.titleLarge,
            ),
            const SizedBox(height: 30),
            Consumer<CounterModel>(
              // 18. 使用Consumer创建特定Provider的上下文
              builder: (context, counter, child) {
                return Text(
                  '计数值: ${counter.count}',
                  style: textTheme.headlineSmall,
                );
              },
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                // 19. 使用Provider的读取方法（不监听变化）
                context.read<CounterModel>().increment();
              },
              child: const Text('增加计数'),
            ),
          ],
        ),
      ),
    );
  }
}

## Android Jetpack Compose 上下文与环境

### 上下文定义与概念

Jetpack Compose中的上下文概念与传统Android有所不同：

- 传统Android中的`Context`表示应用环境，提供资源访问、系统服务等
- Compose使用`CompositionLocal`作为轻量级上下文传递机制
- `LocalContext`是一个特殊的`CompositionLocal`，持有Android的`Context`对象
- Compose UI元素通过`Ambient`模式访问上下文数据
- 各种`LocalXXX`对象（如`LocalDensity`、`LocalTextStyle`）提供不同上下文信息
- Compose函数之间可以通过参数直接传递上下文
- `CoroutineContext`用于管理协程上下文和作用域

### 上下文获取方式

在Jetpack Compose中，获取上下文的主要方式包括：

- 使用`LocalContext.current`获取Android的`Context`对象
- 通过其他`LocalXXX.current`获取特定上下文数据
- 在可组合函数中接收作为参数传入的上下文
- 使用`@Composable`函数的接收者（`this`）访问当前组合范围
- 通过`rememberCoroutineScope()`获取协程作用域
- 使用`LocalLifecycleOwner.current`获取生命周期所有者
- 通过`LocalSavedStateRegistryOwner.current`获取状态保存注册表

### 上下文传递机制

Jetpack Compose中的上下文传递机制包括：

- 使用`CompositionLocalProvider`在组合树中提供新的上下文值
- 通过函数参数显式传递上下文数据
- 使用`remember`和`rememberSaveable`保存和恢复状态
- 通过`collectAsState`将Flow转换为State在组合中使用
- 使用`derivedStateOf`根据其他状态计算派生状态
- 通过`ViewModel`和`ViewModelProvider`跨组合共享数据
- 使用Hilt进行依赖注入和上下文管理

### 上下文作用域

Jetpack Compose中的上下文作用域具有以下特点：

- `CompositionLocal`的值在组合树中向下传递
- 每个`CompositionLocalProvider`定义的值只影响其子组合
- 协程作用域通过`rememberCoroutineScope`创建，绑定到组合的生命周期
- 可以使用`LaunchedEffect`定义与特定键关联的副作用
- `DisposableEffect`允许注册与组合生命周期绑定的清理操作
- `SideEffect`用于无需跟踪的每次重组执行的效果
- 通过嵌套组合创建隔离的上下文环境

### 依赖注入支持

Jetpack Compose与Android的依赖注入系统无缝集成：

- 支持使用Hilt进行依赖注入
- 通过`@HiltViewModel`和`hiltViewModel()`创建和获取ViewModel
- 使用`@AndroidEntryPoint`和`@HiltAndroidApp`配置应用入口
- 支持`@Inject`构造函数注入和`@Provides`方法提供依赖
- 与Dagger 2完全兼容，可以使用其全部特性
- 可以通过`CompositionLocalProvider`提供自定义依赖
- 支持测试时模拟和替换依赖

### 生命周期绑定

Jetpack Compose中的上下文与生命周期绑定包括：

- 使用`LocalLifecycleOwner.current`获取当前生命周期对象
- 通过`DisposableEffect`注册生命周期监听器
- 使用`rememberUpdatedState`捕获可能变化的值以在效果中使用
- `LaunchedEffect`会在组合进入树时启动，离开树时自动取消
- `rememberCoroutineScope`创建的作用域与组合生命周期绑定
- 可以使用`produceState`将非Compose状态源转换为Compose状态
- 通过`snapshotFlow`将Compose状态转换为Flow以用于协程处理

### 平台特性与兼容性

Jetpack Compose上下文机制的特性与兼容性包括：

- 专为Android平台设计，充分利用平台特性
- 支持Android 5.0（API 21）及以上版本
- 与传统View系统兼容，可以混合使用
- 通过`AndroidView`和`ComposeView`实现Compose与View的互操作
- 与Android架构组件（ViewModel、LiveData、Room等）无缝集成
- 支持Material Design 3和动态颜色系统
- 通过Kotlin协程和Flow提供响应式编程模型

### 完整示例代码

```kotlin
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.platform.LocalDensity
import androidx.compose.ui.platform.LocalLifecycleOwner
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.dp
import androidx.lifecycle.Lifecycle
import androidx.lifecycle.LifecycleEventObserver
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewmodel.compose.viewModel
import androidx.navigation.NavController
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.compose.rememberNavController
import dagger.hilt.android.AndroidEntryPoint
import dagger.hilt.android.lifecycle.HiltViewModel
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.asStateFlow
import kotlinx.coroutines.launch
import javax.inject.Inject
import javax.inject.Singleton

// 1. 定义应用级别数据持有者
@Singleton
class AppConfig @Inject constructor() {
    val appName = "Compose Context Demo"
    val apiUrl = "https://api.example.com"
}

// 2. 创建ViewModel
@HiltViewModel
class MainViewModel @Inject constructor(
    private val appConfig: AppConfig
) : ViewModel() {
    // 状态数据
    private val _counterState = MutableStateFlow(0)
    val counterState: StateFlow<Int> = _counterState.asStateFlow()
    
    private val _themeState = MutableStateFlow(false)
    val themeState: StateFlow<Boolean> = _themeState.asStateFlow()
    
    // 获取App配置
    fun getAppName() = appConfig.appName
    fun getApiUrl() = appConfig.apiUrl
    
    // 更新状态
    fun incrementCounter() {
        _counterState.value++
    }
    
    fun toggleTheme() {
        _themeState.value = !_themeState.value
    }
}

// 3. 自定义CompositionLocal
private val LocalAppInfo = compositionLocalOf { AppInfo("未定义", "未定义") }

data class AppInfo(
    val versionName: String,
    val buildType: String
)

// 4. 主Activity
@AndroidEntryPoint
class MainActivity : ComponentActivity() {
    @Inject
    lateinit var appConfig: AppConfig
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        // 创建应用信息对象
        val appInfo = AppInfo(
            versionName = "1.0.0",
            buildType = "debug"
        )
        
        setContent {
            // 5. 提供自定义CompositionLocal值
            CompositionLocalProvider(
                LocalAppInfo provides appInfo
            ) {
                AppContent()
            }
        }
    }
}

@Composable
fun AppContent(
    viewModel: MainViewModel = viewModel()
) {
    // 6. 收集ViewModel状态
    val isDarkTheme by viewModel.themeState.collectAsState()
    
    // 7. 主题设置
    MaterialTheme(
        colorScheme = if (isDarkTheme) {
            darkColorScheme()
        } else {
            lightColorScheme()
        }
    ) {
        // 8. 创建导航控制器
        val navController = rememberNavController()
        
        // 9. 导航宿主
        NavHost(
            navController = navController,
            startDestination = "home"
        ) {
            composable("home") {
                HomeScreen(
                    viewModel = viewModel,
                    onNavigateToDetail = { navController.navigate("detail") }
                )
            }
            composable("detail") {
                DetailScreen(
                    viewModel = viewModel,
                    onNavigateBack = { navController.popBackStack() }
                )
            }
        }
    }
}

@Composable
fun HomeScreen(
    viewModel: MainViewModel,
    onNavigateToDetail: () -> Unit
) {
    // 10. 获取当前Android Context
    val context = LocalContext.current
    
    // 11. 获取当前密度
    val density = LocalDensity.current
    
    // 12. 获取自定义CompositionLocal值
    val appInfo = LocalAppInfo.current
    
    // 13. 收集状态
    val counter by viewModel.counterState.collectAsState()
    
    // 14. 创建协程作用域
    val coroutineScope = rememberCoroutineScope()
    
    // 15. 监听生命周期
    val lifecycleOwner = LocalLifecycleOwner.current
    DisposableEffect(lifecycleOwner) {
        val observer = LifecycleEventObserver { _, event ->
            if (event == Lifecycle.Event.ON_RESUME) {
                println("HomeScreen 恢复")
            }
        }
        lifecycleOwner.lifecycle.addObserver(observer)
        
        onDispose {
            lifecycleOwner.lifecycle.removeObserver(observer)
        }
    }
    
    // 16. 界面布局
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text(viewModel.getAppName()) },
                actions = {
                    IconButton(onClick = { viewModel.toggleTheme() }) {
                        Icon(
                            imageVector = if (viewModel.themeState.collectAsState().value) {
                                Icons.Filled.LightMode
                            } else {
                                Icons.Filled.DarkMode
                            },
                            contentDescription = "切换主题"
                        )
                    }
                }
            )
        }
    ) { paddingValues ->
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues)
                .padding(16.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.Center
        ) {
            // 显示设备密度信息
            Text(
                text = "设备密度: ${density.density}",
                style = MaterialTheme.typography.titleLarge
            )
            
            Spacer(modifier = Modifier.height(20.dp))
            
            // 显示应用信息
            Text(
                text = "版本: ${appInfo.versionName} (${appInfo.buildType})",
                style = MaterialTheme.typography.titleMedium
            )
            
            Spacer(modifier = Modifier.height(20.dp))
            
            // 计数器显示
            Text(
                text = "计数器值: $counter",
                style = MaterialTheme.typography.headlineMedium,
                textAlign = TextAlign.Center
            )
            
            Spacer(modifier = Modifier.height(20.dp))
            
            // 增加计数按钮
            Button(
                onClick = { viewModel.incrementCounter() },
                modifier = Modifier.width(200.dp)
            ) {
                Text("增加计数")
            }
            
            Spacer(modifier = Modifier.height(40.dp))
            
            // 查看详情按钮
            Button(
                onClick = onNavigateToDetail,
                modifier = Modifier.width(200.dp)
            ) {
                Text("进入详情页")
            }
            
            Spacer(modifier = Modifier.height(20.dp))
            
            // 演示通过协程作用域调用
            Button(
                onClick = {
                    coroutineScope.launch {
                        // 可以在这里执行协程操作
                        // 例如：延迟后显示Snackbar
                        viewModel.incrementCounter()
                    }
                },
                modifier = Modifier.width(200.dp)
            ) {
                Text("使用协程增加计数")
            }
        }
    }
}

@Composable
fun DetailScreen(
    viewModel: MainViewModel,
    onNavigateBack: () -> Unit
) {
    // 重新获取上下文环境
    val context = LocalContext.current
    val appInfo = LocalAppInfo.current
    val counter by viewModel.counterState.collectAsState()
    
    // 演示状态派生
    val isCounterEven by remember(counter) {
        derivedStateOf { counter % 2 == 0 }
    }
    
    // LaunchedEffect示例
    LaunchedEffect(Unit) {
        // 当进入这个Composable时执行一次，离开时自动取消
        println("DetailScreen进入")
    }
    
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("详情页面") },
                navigationIcon = {
                    IconButton(onClick = onNavigateBack) {
                        Icon(
                            imageVector = Icons.Filled.ArrowBack,
                            contentDescription = "返回"
                        )
                    }
                }
            )
        }
    ) { paddingValues ->
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues)
                .padding(16.dp),
            horizontalAlignment = Alignment.CenterHorizontally,
            verticalArrangement = Arrangement.Center
        ) {
            Text(
                text = "API地址: ${viewModel.getApiUrl()}",
                style = MaterialTheme.typography.titleLarge
            )
            
            Spacer(modifier = Modifier.height(20.dp))
            
            Text(
                text = "计数器值: $counter (${if (isCounterEven) "偶数" else "奇数"})",
                style = MaterialTheme.typography.headlineMedium
            )
            
            Spacer(modifier = Modifier.height(20.dp))
            
            Button(
                onClick = { viewModel.incrementCounter() },
                modifier = Modifier.width(200.dp)
            ) {
                Text("增加计数")
            }
            
            Spacer(modifier = Modifier.height(40.dp))
            
            Button(
                onClick = onNavigateBack,
                modifier = Modifier.width(200.dp)
            ) {
                Text("返回首页")
            }
        }
    }
} 

## iOS UIKit 上下文与环境

### 上下文定义与概念

UIKit中的上下文概念主要体现在几个层面：

- 没有类似Android的全局`Context`对象，而是分散在不同组件中
- `UIViewController`是主要的上下文载体，管理视图层次和生命周期
- `UIView`持有对其所属的`UIViewController`的引用
- `UIApplication`和`UIApplicationDelegate`提供应用级别的上下文
- `UIWindowScene`和`UISceneDelegate`（iOS 13+）管理场景级别上下文
- `UINib`和`Bundle`提供资源加载的上下文
- `UITraitCollection`保存环境特征（如尺寸类别、暗色模式等）

### 上下文获取方式

在UIKit中，获取上下文的主要方式包括：

- 通过视图层次访问，如`view.superview`或`view.window`
- 使用`UIViewController`的`navigationController`、`tabBarController`等属性
- 获取`UIApplication.shared`和`UIApplication.shared.delegate`
- 通过`UIView`的`next`属性遍历响应者链
- 使用`UIViewController`的`presentingViewController`和`presentedViewController`
- 访问`UIView`的`traitCollection`属性获取环境特征
- 在iOS 13+中，通过`UIWindowScene`获取场景相关信息

### 上下文传递机制

UIKit中的上下文传递机制包括：

- 通过初始化方法参数显式传递
- 使用`prepare(for:sender:)`方法在视图控制器间传递
- 通过属性设置（如delegate模式）传递
- 利用`UIResponder`的响应者链向上传递事件
- 使用`NotificationCenter`进行松散耦合的全局通信
- 通过`UITraitCollection`和`traitCollectionDidChange(_:)`传递环境变化
- iOS 13+引入的`UIScene`生命周期事件传递

### 上下文作用域

UIKit中的上下文作用域具有以下特点：

- 视图控制器形成自然的作用域容器
- 视图层次结构定义了视图的作用域
- 应用委托负责应用级别作用域
- 场景委托管理场景级别作用域
- `UINavigationController`、`UITabBarController`等创建容器作用域
- 通过`childViewControllers`建立父子作用域关系
- 弹出的视图控制器与其父视图控制器形成临时作用域

### 依赖注入支持

UIKit本身不提供依赖注入框架，但支持多种依赖注入方式：

- 通过初始化方法注入（构造器注入）
- 使用属性设置注入依赖（属性注入）
- 通过方法调用注入依赖（方法注入）
- 可以与第三方依赖注入框架集成（如Swinject、Dip）
- 工厂方法模式创建和注入依赖
- 通过`AppDelegate`或单例管理全局依赖
- `prepare(for:sender:)`方法用于Segue过程中的依赖注入

### 生命周期绑定

UIKit中的上下文与生命周期绑定包括：

- `UIViewController`提供完整的生命周期方法（如`viewDidLoad`、`viewWillAppear`）
- `UIView`提供`didMoveToSuperview`和`didMoveToWindow`回调
- `UIApplicationDelegate`处理应用级生命周期事件
- `UISceneDelegate`（iOS 13+）管理场景生命周期
- 通过`NotificationCenter`观察应用状态变化
- `deinit`方法用于对象析构时清理资源
- 可以使用`UIFocusEnvironment`处理焦点生命周期

### 平台特性与兼容性

UIKit上下文机制的特性与兼容性包括：

- 专为iOS/iPadOS平台设计，充分利用平台特性
- 支持iOS 7.0及以上版本，部分新功能需要更高版本
- 提供自适应布局支持，适应不同屏幕尺寸和方向
- 通过`UITraitCollection`适配不同环境特征
- 通过`UIUserInterfaceIdiom`识别设备类型
- 支持iPadOS的多窗口和拖放功能
- 提供辅助功能和本地化支持

### 完整示例代码

```swift
import UIKit

// 1. 应用配置和依赖
class AppConfig {
    static let shared = AppConfig()
    
    let appName = "UIKit Context Demo"
    let apiUrl = "https://api.example.com"
    
    private init() {}
}

// 2. 数据模型
class CounterModel {
    static let shared = CounterModel()
    
    private(set) var count = 0
    
    func increment() {
        count += 1
        // 发送通知
        NotificationCenter.default.post(name: .counterDidChange, object: self)
    }
    
    private init() {}
}

// 3. 主题管理
class ThemeManager {
    static let shared = ThemeManager()
    
    var isDarkMode = false {
        didSet {
            // 通知主题变化
            NotificationCenter.default.post(name: .themeDidChange, object: self)
        }
    }
    
    func toggleTheme() {
        isDarkMode = !isDarkMode
    }
    
    func applyTheme(to viewController: UIViewController) {
        viewController.view.backgroundColor = isDarkMode ? .black : .white
        
        // 递归更新所有子视图控制器
        for child in viewController.children {
            applyTheme(to: child)
        }
    }
    
    private init() {}
}

// 4. 通知扩展
extension Notification.Name {
    static let counterDidChange = Notification.Name("counterDidChange")
    static let themeDidChange = Notification.Name("themeDidChange")
}

// 5. 自定义上下文协议
protocol AppContextAware: AnyObject {
    var appConfig: AppConfig { get }
    var counterModel: CounterModel { get }
    var themeManager: ThemeManager { get }
}

// 默认实现
extension AppContextAware {
    var appConfig: AppConfig { return AppConfig.shared }
    var counterModel: CounterModel { return CounterModel.shared }
    var themeManager: ThemeManager { return ThemeManager.shared }
}

// 6. 应用委托
@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?
    
    // 注册应用级依赖
    lazy var serviceLocator: ServiceLocator = {
        let locator = ServiceLocator()
        locator.register(service: NetworkService())
        locator.register(service: LoggingService())
        return locator
    }()
    
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        print("App launched")
        return true
    }
    
    func applicationWillResignActive(_ application: UIApplication) {
        print("App will resign active")
    }
    
    func applicationDidEnterBackground(_ application: UIApplication) {
        print("App entered background")
    }
    
    func applicationWillEnterForeground(_ application: UIApplication) {
        print("App will enter foreground")
    }
    
    func applicationDidBecomeActive(_ application: UIApplication) {
        print("App became active")
    }
    
    func applicationWillTerminate(_ application: UIApplication) {
        print("App will terminate")
    }
}

// 7. 简单服务定位器
class ServiceLocator {
    private var services: [String: Any] = [:]
    
    func register<T>(service: T) {
        let key = String(describing: T.self)
        services[key] = service
    }
    
    func resolve<T>() -> T? {
        let key = String(describing: T.self)
        return services[key] as? T
    }
}

// 8. 服务定义
protocol NetworkServiceProtocol {
    func fetchData(completion: @escaping (Result<String, Error>) -> Void)
}

protocol LoggingServiceProtocol {
    func log(_ message: String)
}

class NetworkService: NetworkServiceProtocol {
    func fetchData(completion: @escaping (Result<String, Error>) -> Void) {
        // 模拟网络请求
        DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
            completion(.success("Sample Data"))
        }
    }
}

class LoggingService: LoggingServiceProtocol {
    func log(_ message: String) {
        print("LOG: \(message)")
    }
}

// 9. 主视图控制器
class MainViewController: UIViewController, AppContextAware {
    // 界面元素
    private let titleLabel = UILabel()
    private let counterLabel = UILabel()
    private let incrementButton = UIButton(type: .system)
    private let navigateButton = UIButton(type: .system)
    private let themeButton = UIButton(type: .system)
    
    // 通知观察者
    private var observers: [NSObjectProtocol] = []
    
    // 依赖属性（通过AppContextAware协议获取，也可以通过注入获取）
    private var networkService: NetworkServiceProtocol? {
        return (UIApplication.shared.delegate as? AppDelegate)?.serviceLocator.resolve()
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
        setupObservers()
        
        // 获取服务并使用
        if let networkService = networkService {
            networkService.fetchData { result in
                switch result {
                case .success(let data):
                    print("Data fetched: \(data)")
                case .failure(let error):
                    print("Error: \(error.localizedDescription)")
                }
            }
        }
    }
    
    override func viewWillAppear(_ animated: Bool) {
        super.viewWillAppear(animated)
        updateUI()
        
        // 获取环境特征
        let userInterfaceIdiom = traitCollection.userInterfaceIdiom
        print("Device type: \(userInterfaceIdiom == .phone ? "iPhone" : "iPad")")
    }
    
    // 视图控制器生命周期与上下文传递
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        if segue.identifier == "showDetail", 
           let detailVC = segue.destination as? DetailViewController {
            // 将数据传递给目标视图控制器
            detailVC.apiUrl = appConfig.apiUrl
        }
    }
    
    private func setupUI() {
        // 标题标签
        titleLabel.text = appConfig.appName
        titleLabel.font = .boldSystemFont(ofSize: 22)
        titleLabel.textAlignment = .center
        titleLabel.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(titleLabel)
        
        // 计数器标签
        counterLabel.text = "计数: \(counterModel.count)"
        counterLabel.font = .systemFont(ofSize: 18)
        counterLabel.textAlignment = .center
        counterLabel.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(counterLabel)
        
        // 增加按钮
        incrementButton.setTitle("增加计数", for: .normal)
        incrementButton.addTarget(self, action: #selector(incrementCounter), for: .touchUpInside)
        incrementButton.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(incrementButton)
        
        // 导航按钮
        navigateButton.setTitle("进入详情页", for: .normal)
        navigateButton.addTarget(self, action: #selector(navigateToDetail), for: .touchUpInside)
        navigateButton.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(navigateButton)
        
        // 主题切换按钮
        updateThemeButtonTitle()
        themeButton.addTarget(self, action: #selector(toggleTheme), for: .touchUpInside)
        themeButton.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(themeButton)
        
        // 约束布局
        NSLayoutConstraint.activate([
            titleLabel.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 20),
            titleLabel.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            titleLabel.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            
            counterLabel.centerYAnchor.constraint(equalTo: view.centerYAnchor, constant: -40),
            counterLabel.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            
            incrementButton.topAnchor.constraint(equalTo: counterLabel.bottomAnchor, constant: 20),
            incrementButton.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            
            navigateButton.topAnchor.constraint(equalTo: incrementButton.bottomAnchor, constant: 30),
            navigateButton.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            
            themeButton.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor, constant: -20),
            themeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor)
        ])
        
        // 应用当前主题
        themeManager.applyTheme(to: self)
    }
    
    private func setupObservers() {
        // 监听计数器变化
        let counterObserver = NotificationCenter.default.addObserver(
            forName: .counterDidChange,
            object: nil,
            queue: .main
        ) { [weak self] _ in
            self?.updateUI()
        }
        
        // 监听主题变化
        let themeObserver = NotificationCenter.default.addObserver(
            forName: .themeDidChange,
            object: nil,
            queue: .main
        ) { [weak self] _ in
            guard let self = self else { return }
            self.updateThemeButtonTitle()
            self.themeManager.applyTheme(to: self)
        }
        
        // 保存观察者引用以便在deinit中移除
        observers = [counterObserver, themeObserver]
    }
    
    private func updateUI() {
        counterLabel.text = "计数: \(counterModel.count)"
    }
    
    private func updateThemeButtonTitle() {
        themeButton.setTitle(themeManager.isDarkMode ? "切换到亮色模式" : "切换到深色模式", for: .normal)
    }
    
    @objc private func incrementCounter() {
        counterModel.increment()
    }
    
    @objc private func navigateToDetail() {
        // 编程式导航 - 依赖注入示例
        let detailVC = DetailViewController()
        // 注入数据
        detailVC.apiUrl = appConfig.apiUrl
        // 使用导航控制器推入
        navigationController?.pushViewController(detailVC, animated: true)
    }
    
    @objc private func toggleTheme() {
        themeManager.toggleTheme()
    }
    
    // 特征集合变化 - 环境变化回调
    override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
        super.traitCollectionDidChange(previousTraitCollection)
        
        // 响应环境特征变化，如尺寸类别、暗色模式等
        if traitCollection.horizontalSizeClass != previousTraitCollection?.horizontalSizeClass {
            print("水平尺寸类别已变化")
            // 适应新布局
        }
        
        if #available(iOS 12.0, *) {
            if traitCollection.userInterfaceStyle != previousTraitCollection?.userInterfaceStyle {
                print("界面样式已变化 (亮/暗)")
                // 适应新主题
            }
        }
    }
    
    deinit {
        // 移除所有观察者
        for observer in observers {
            NotificationCenter.default.removeObserver(observer)
        }
        print("MainViewController已释放")
    }
}

// 10. 详情视图控制器
class DetailViewController: UIViewController {
    // 通过依赖注入传入
    var apiUrl: String!
    
    private let apiLabel = UILabel()
    private let counterLabel = UILabel()
    private let backButton = UIButton(type: .system)
    
    // 通知观察者
    private var counterObserver: NSObjectProtocol?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        title = "详情页面"
        view.backgroundColor = .white
        setupUI()
        
        // 获取计数器并显示
        updateCounterDisplay()
        
        // 监听计数器变化
        counterObserver = NotificationCenter.default.addObserver(
            forName: .counterDidChange,
            object: nil,
            queue: .main
        ) { [weak self] _ in
            self?.updateCounterDisplay()
        }
    }
    
    private func setupUI() {
        // API标签
        apiLabel.text = "API地址: \(apiUrl ?? "未设置")"
        apiLabel.font = .systemFont(ofSize: 16)
        apiLabel.textAlignment = .center
        apiLabel.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(apiLabel)
        
        // 计数器标签
        counterLabel.font = .boldSystemFont(ofSize: 18)
        counterLabel.textAlignment = .center
        counterLabel.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(counterLabel)
        
        // 返回按钮 (编程式导航时使用)
        backButton.setTitle("返回", for: .normal)
        backButton.addTarget(self, action: #selector(goBack), for: .touchUpInside)
        backButton.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(backButton)
        
        // 约束布局
        NSLayoutConstraint.activate([
            apiLabel.centerYAnchor.constraint(equalTo: view.centerYAnchor, constant: -40),
            apiLabel.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            apiLabel.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            
            counterLabel.topAnchor.constraint(equalTo: apiLabel.bottomAnchor, constant: 20),
            counterLabel.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            
            backButton.topAnchor.constraint(equalTo: counterLabel.bottomAnchor, constant: 30),
            backButton.centerXAnchor.constraint(equalTo: view.centerXAnchor)
        ])
    }
    
    private func updateCounterDisplay() {
        // 通过单例获取计数器值
        let count = CounterModel.shared.count
        counterLabel.text = "当前计数: \(count) (\(count % 2 == 0 ? "偶数" : "奇数"))"
    }
    
    @objc private func goBack() {
        navigationController?.popViewController(animated: true)
    }
    
    deinit {
        // 移除观察者
        if let observer = counterObserver {
            NotificationCenter.default.removeObserver(observer)
        }
        print("DetailViewController已释放")
    }
}
```

## iOS SwiftUI 上下文与环境

### 上下文定义与概念

SwiftUI中的上下文概念主要体现在几个层面：

- 没有类似Android/UIKit的全局`Context`对象，而是通过`Environment`和`@EnvironmentObject`传递
- `View`是主要的上下文载体，通过`@State`、`@Binding`、`@ObservedObject`等属性管理状态
- `ViewModifier`用于修改视图的外观和行为
- `EnvironmentValues`保存环境特征（如尺寸、颜色、本地化等）
- `View`的`body`函数接收上下文数据并返回视图
- `@Environment`属性包装器用于访问环境值

### 上下文获取方式

在SwiftUI中，获取上下文的主要方式包括：

- 使用`@Environment`属性包装器获取环境值
- 使用`@EnvironmentObject`属性包装器获取共享数据
- 使用`@State`属性包装器管理视图内部状态
- 使用`@Binding`属性包装器创建双向绑定
- 使用`@ObservedObject`属性包装器订阅可观察对象的变化
- 使用`@StateObject`属性包装器管理视图内部的可观察对象
- 使用`@Environment(\.colorScheme)`等访问特定环境值

### 上下文传递机制

SwiftUI中的上下文传递机制包括：

- 通过`View`的`body`函数参数传递上下文数据
- 使用`@Environment`属性包装器在视图层次结构中传递环境值
- 使用`@EnvironmentObject`属性包装器在视图层次结构中传递共享数据
- 使用`@Binding`属性包装器在视图间传递双向绑定的状态
- 使用`@ObservedObject`属性包装器在视图间传递可观察对象的变化
- 使用`ViewModifier`在视图层次结构中传递修改器
- 使用`@Environment`属性包装器在视图层次结构中传递环境值

### 上下文作用域

SwiftUI中的上下文作用域具有以下特点：

- 视图层次结构定义了上下文的作用域
- `@Environment`属性包装器提供了全局作用域的环境值
- `@EnvironmentObject`属性包装器提供了局部作用域的共享数据
- `@State`属性包装器提供了视图内部的状态管理
- `@Binding`属性包装器提供了双向绑定的状态传递
- `@ObservedObject`属性包装器提供了可观察对象的变化传递
- `@StateObject`属性包装器提供了视图内部的可观察对象管理

### 依赖注入支持

SwiftUI本身不提供依赖注入框架，但支持多种依赖注入方式：

- 使用`@EnvironmentObject`属性包装器注入共享数据
- 使用`@ObservedObject`属性包装器注入可观察对象
- 使用`@StateObject`属性包装器注入可观察对象
- 使用`@Environment`属性包装器注入环境值
- 使用`@State`属性包装器注入状态
- 使用`@Binding`属性包装器注入双向绑定的状态
- 使用`ViewModifier`注入修改器
- 使用`@Environment`属性包装器注入环境值

### 生命周期绑定

SwiftUI中的上下文与生命周期绑定包括：

- `View`的`body`函数在视图需要重新渲染时调用
- `onAppear`和`onDisappear`修改器用于处理视图的生命周期事件
- `@State`属性包装器管理视图内部状态的生命周期
- `@Binding`属性包装器管理双向绑定状态的生命周期
- `@ObservedObject`属性包装器管理可观察对象的生命周期