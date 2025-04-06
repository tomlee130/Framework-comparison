# 六大主流框架路由机制对比

本文比较Flutter、Android（Jetpack Compose）、UIKit、SwiftUI（iOS）、鸿蒙（ArkUI）和Vue六大框架的路由机制，从以下维度进行分析：

- 路由定义方式
- 路由传参机制
- 路由栈管理
- 路由转场动画
- 深层链接支持

同时提供各框架的平台特性、兼容性和完整实例代码。

## 目录

- [六大主流框架路由机制对比](#六大主流框架路由机制对比)
  - [目录](#目录)
  - [Flutter](#flutter)
    - [路由定义方式](#路由定义方式)
    - [路由传参机制](#路由传参机制)
    - [路由栈管理](#路由栈管理)
    - [路由转场动画](#路由转场动画)
    - [深层链接支持](#深层链接支持)
    - [平台特性与兼容性](#平台特性与兼容性)
    - [完整实例代码](#完整实例代码)
  - [Android (Jetpack Compose)](#android-jetpack-compose)
    - [路由定义方式](#路由定义方式-1)
    - [路由传参机制](#路由传参机制-1)
    - [路由栈管理](#路由栈管理-1)
    - [路由转场动画](#路由转场动画-1)
    - [深层链接支持](#深层链接支持-1)
    - [平台特性与兼容性](#平台特性与兼容性-1)
    - [完整实例代码](#完整实例代码-1)
  - [UIKit (iOS)](#uikit-ios)
    - [路由定义方式](#路由定义方式-2)
    - [路由传参机制](#路由传参机制-2)
    - [路由栈管理](#路由栈管理-2)
    - [路由转场动画](#路由转场动画-2)
    - [深层链接支持](#深层链接支持-2)
    - [平台特性与兼容性](#平台特性与兼容性-2)
    - [完整实例代码](#完整实例代码-2)
  - [SwiftUI (iOS)](#swiftui-ios)
    - [路由定义方式](#路由定义方式-3)
    - [路由传参机制](#路由传参机制-3)
    - [路由栈管理](#路由栈管理-3)
    - [路由转场动画](#路由转场动画-3)
    - [深层链接支持](#深层链接支持-3)
    - [平台特性与兼容性](#平台特性与兼容性-3)
    - [完整实例代码](#完整实例代码-3)
  - [鸿蒙 (ArkUI)](#鸿蒙-arkui)
    - [路由定义方式](#路由定义方式-4)
    - [路由传参机制](#路由传参机制-4)
    - [路由栈管理](#路由栈管理-4)
    - [路由转场动画](#路由转场动画-4)
    - [深层链接支持](#深层链接支持-4)
    - [平台特性与兼容性](#平台特性与兼容性-4)
    - [完整实例代码](#完整实例代码-4)
  - [Vue](#vue)
    - [路由定义方式](#路由定义方式-5)
    - [路由传参机制](#路由传参机制-5)
    - [路由栈管理](#路由栈管理-5)
    - [路由转场动画](#路由转场动画-5)
    - [深层链接支持](#深层链接支持-5)
    - [平台特性与兼容性](#平台特性与兼容性-5)
    - [完整实例代码](#完整实例代码-5)
  - [框架对比总结](#框架对比总结)
    - [路由定义方式](#路由定义方式-6)
    - [路由传参机制](#路由传参机制-6)
    - [路由栈管理](#路由栈管理-6)
    - [路由转场动画](#路由转场动画-6)
    - [深层链接支持](#深层链接支持-6)
    - [平台与生态特性](#平台与生态特性)
    - [总体评价](#总体评价)
    - [选择建议](#选择建议)

## Flutter

### 路由定义方式

Flutter提供了两种主要的路由系统：

1. **命名式路由**：在`MaterialApp`或`CupertinoApp`中通过`routes`属性定义路由映射
2. **基于Navigator的命令式路由**：直接使用`Navigator.push()`等方法

更现代的应用程序通常使用第三方路由库，如`go_router`或`auto_route`来简化复杂路由管理。

### 路由传参机制

1. **命名式路由传参**：
   - 通过`Navigator.pushNamed(context, '/details', arguments: data)`传递参数
   - 在目标页面通过`ModalRoute.of(context)!.settings.arguments`获取参数

2. **命令式路由传参**：
   - 直接在构造页面时传递参数：`Navigator.push(context, MaterialPageRoute(builder: (context) => DetailsPage(item: item)))`

### 路由栈管理

Flutter的`Navigator`提供了一系列方法来管理路由栈：

- `push()`：添加新路由到栈顶
- `pop()`：移除栈顶路由
- `pushReplacement()`：替换当前路由
- `popUntil()`：弹出路由直到满足条件
- `pushAndRemoveUntil()`：推入新路由并移除满足条件的旧路由

还支持嵌套导航器，以实现更复杂的导航结构。

### 路由转场动画

Flutter提供了多种内置页面转场动画：

- `MaterialPageRoute`：提供符合Material Design规范的转场动画
- `CupertinoPageRoute`：提供符合iOS设计规范的转场动画
- `PageRouteBuilder`：允许自定义转场动画

例如自定义转场动画：

```dart
Navigator.push(
  context,
  PageRouteBuilder(
    pageBuilder: (context, animation, secondaryAnimation) => DetailsPage(),
    transitionsBuilder: (context, animation, secondaryAnimation, child) {
      var begin = const Offset(1.0, 0.0);
      var end = Offset.zero;
      var curve = Curves.ease;
      var tween = Tween(begin: begin, end: end).chain(CurveTween(curve: curve));
      
      return SlideTransition(
        position: animation.drive(tween),
        child: child,
      );
    },
  ),
);
```

### 深层链接支持

Flutter支持通过以下方式处理深层链接：

1. **原生集成**：在Android和iOS原生代码中配置深层链接，然后传递给Flutter
2. **uni_links包**：简化深层链接处理
3. **go_router**：内置支持深层链接和路径匹配

### 平台特性与兼容性

- **跨平台**：同一套路由代码可在Android、iOS、Web和桌面平台使用
- **适配性**：可以根据平台自动选择合适的动画样式
- **可扩展性**：可以通过自定义路由实现特定平台的行为

### 完整实例代码

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter路由示例',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      // 命名路由定义
      initialRoute: '/',
      routes: {
        '/': (context) => const HomePage(),
        '/details': (context) => const DetailsPage(),
        '/settings': (context) => const SettingsPage(),
      },
      // 未定义的路由处理
      onGenerateRoute: (settings) {
        if (settings.name?.startsWith('/product/') ?? false) {
          final productId = settings.name!.split('/').last;
          return MaterialPageRoute(
            builder: (context) => ProductPage(productId: productId),
            settings: settings,
          );
        }
        return null;
      },
      // 默认的"未找到"页面
      onUnknownRoute: (settings) {
        return MaterialPageRoute(
          builder: (context) => const NotFoundPage(),
        );
      },
    );
  }
}

class HomePage extends StatelessWidget {
  const HomePage({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('首页')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            // 命名路由跳转（带参数）
            ElevatedButton(
              onPressed: () {
                Navigator.pushNamed(
                  context,
                  '/details',
                  arguments: {'id': '1', 'title': '商品详情'},
                );
              },
              child: const Text('查看详情（命名路由）'),
            ),
            const SizedBox(height: 20),
            // 命令式路由跳转
            ElevatedButton(
              onPressed: () {
                Navigator.push(
                  context,
                  MaterialPageRoute(
                    builder: (context) => const SettingsPage(),
                  ),
                );
              },
              child: const Text('打开设置（命令式路由）'),
            ),
            const SizedBox(height: 20),
            // 使用自定义动画过渡
            ElevatedButton(
              onPressed: () {
                Navigator.push(
                  context,
                  PageRouteBuilder(
                    pageBuilder: (context, animation, secondaryAnimation) => 
                        const SettingsPage(),
                    transitionsBuilder: (context, animation, secondaryAnimation, child) {
                      return FadeTransition(
                        opacity: animation,
                        child: child,
                      );
                    },
                    transitionDuration: const Duration(milliseconds: 500),
                  ),
                );
              },
              child: const Text('淡入淡出效果'),
            ),
          ],
        ),
      ),
    );
  }
}

class DetailsPage extends StatelessWidget {
  const DetailsPage({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    // 获取传递的参数
    final args = ModalRoute.of(context)!.settings.arguments as Map<String, dynamic>?;
    
    return Scaffold(
      appBar: AppBar(title: Text(args?['title'] ?? '详情')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('ID: ${args?['id'] ?? '未知'}'),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                Navigator.pop(context);
              },
              child: const Text('返回'),
            ),
          ],
        ),
      ),
    );
  }
}

class SettingsPage extends StatelessWidget {
  const SettingsPage({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('设置')),
      body: Center(
        child: ElevatedButton(
          onPressed: () {
            // 返回到首页（弹出所有路由直到首页）
            Navigator.popUntil(context, ModalRoute.withName('/'));
          },
          child: const Text('返回首页'),
        ),
      ),
    );
  }
}

class ProductPage extends StatelessWidget {
  final String productId;
  
  const ProductPage({Key? key, required this.productId}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('商品页')),
      body: Center(
        child: Text('商品ID: $productId'),
      ),
    );
  }
}

class NotFoundPage extends StatelessWidget {
  const NotFoundPage({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('页面未找到')),
      body: const Center(
        child: Text('404 - 请求的页面不存在'),
      ),
    );
  }
}
```

使用`go_router`的现代路由示例：

```dart
import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  MyApp({Key? key}) : super(key: key);

  // 定义路由配置
  final _router = GoRouter(
    initialLocation: '/',
    routes: [
      GoRoute(
        path: '/',
        builder: (context, state) => const HomePage(),
      ),
      GoRoute(
        path: '/details/:id',
        builder: (context, state) {
          // 获取路径参数
          final id = state.pathParameters['id']!;
          // 获取查询参数
          final title = state.queryParameters['title'] ?? '详情';
          
          return DetailsPage(id: id, title: title);
        },
      ),
      GoRoute(
        path: '/settings',
        builder: (context, state) => const SettingsPage(),
      ),
    ],
    // 处理错误路由
    errorBuilder: (context, state) => const NotFoundPage(),
  );

  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      title: 'Flutter Go Router示例',
      theme: ThemeData(primarySwatch: Colors.blue),
      // 使用路由配置
      routerConfig: _router,
    );
  }
}

class HomePage extends StatelessWidget {
  const HomePage({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('首页')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: () {
                // 导航到详情页
                context.go('/details/123?title=商品详情');
              },
              child: const Text('查看详情'),
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                // 导航到设置页
                context.push('/settings');
              },
              child: const Text('打开设置'),
            ),
          ],
        ),
      ),
    );
  }
}

class DetailsPage extends StatelessWidget {
  final String id;
  final String title;
  
  const DetailsPage({Key? key, required this.id, required this.title}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(title)),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('ID: $id'),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                context.pop();
              },
              child: const Text('返回'),
            ),
          ],
        ),
      ),
    );
  }
}

class SettingsPage extends StatelessWidget {
  const SettingsPage({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('设置')),
      body: Center(
        child: ElevatedButton(
          onPressed: () {
            // 返回到首页
            context.go('/');
          },
          child: const Text('返回首页'),
        ),
      ),
    );
  }
}

class NotFoundPage extends StatelessWidget {
  const NotFoundPage({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('页面未找到')),
      body: const Center(
        child: Text('404 - 请求的页面不存在'),
      ),
    );
  }
}
```

## Android (Jetpack Compose)

### 路由定义方式

Jetpack Compose使用Navigation Compose库来管理路由，主要通过以下方式定义路由：

1. **NavHost和NavController**：NavHost是路由的容器，NavController控制导航逻辑
2. **NavGraph**：声明式地定义导航图，包含目的地和导航行为

### 路由传参机制

Jetpack Compose导航有三种传递参数的方式：

1. **路径参数**：通过URL路径部分传递
   ```kotlin
   NavHost(navController, startDestination = "home") {
       composable("details/{itemId}") { backStackEntry ->
           val itemId = backStackEntry.arguments?.getString("itemId")
           DetailsScreen(itemId = itemId)
       }
   }
   // 导航时
   navController.navigate("details/$itemId")
   ```

2. **查询参数**：通过URL查询字符串传递
   ```kotlin
   composable(
       "search?query={searchQuery}",
       arguments = listOf(
           navArgument("searchQuery") { defaultValue = "" }
       )
   ) { backStackEntry ->
       val searchQuery = backStackEntry.arguments?.getString("searchQuery") ?: ""
       SearchScreen(query = searchQuery)
   }
   // 导航时
   navController.navigate("search?query=$searchTerm")
   ```

3. **参数对象**：使用parcelize标记的参数类
   ```kotlin
   @Parcelize
   data class UserData(val userId: String, val userName: String) : Parcelable

   // 导航时传递对象
   navController.currentBackStackEntry?.arguments?.putParcelable("userData", userData)
   navController.navigate("profile")

   // 在目标界面获取
   val userData = navController.previousBackStackEntry?.arguments?.getParcelable<UserData>("userData")
   ```

### 路由栈管理

Navigation Compose提供了丰富的栈管理API：

- `navigate()`：导航到目的地
- `navigateUp()`：返回上一级
- `popBackStack()`：弹出当前目的地
- `popBackStack(route, inclusive, saveState)`：弹出到指定路由
- `navigate(route) { popUpTo(startRoute) { inclusive = true } }`：导航时弹出指定路由
- `navigate(route) { launchSingleTop = true }`：单例模式，防止重复目的地

Navigation Compose还支持：
- 返回栈保存与恢复
- 嵌套导航图

### 路由转场动画

Compose Navigation支持自定义转场动画：

```kotlin
NavHost(
    navController = navController,
    startDestination = "home",
    enterTransition = { initial, target -> 
        slideIntoContainer(AnimatedContentTransitionScope.SlideDirection.Left, animationSpec = tween(300)) 
    },
    exitTransition = { initial, target -> 
        slideOutOfContainer(AnimatedContentTransitionScope.SlideDirection.Left, animationSpec = tween(300)) 
    },
    popEnterTransition = { initial, target -> 
        slideIntoContainer(AnimatedContentTransitionScope.SlideDirection.Right, animationSpec = tween(300)) 
    },
    popExitTransition = { initial, target -> 
        slideOutOfContainer(AnimatedContentTransitionScope.SlideDirection.Right, animationSpec = tween(300)) 
    }
) {
    // 导航目的地定义
}
```

也可以为单个路由定义专门的动画：

```kotlin
composable(
    route = "details/{itemId}",
    arguments = listOf(navArgument("itemId") { type = NavType.StringType }),
    enterTransition = { 
        slideIntoContainer(AnimatedContentTransitionScope.SlideDirection.Up, animationSpec = tween(300)) 
    },
    exitTransition = {
        slideOutOfContainer(AnimatedContentTransitionScope.SlideDirection.Down, animationSpec = tween(300))
    }
) { backStackEntry ->
    val itemId = backStackEntry.arguments?.getString("itemId")
    DetailsScreen(itemId = itemId)
}
```

### 深层链接支持

Navigation Compose支持两种深层链接：

1. **显式深层链接**：系统自动触发的链接
   ```kotlin
   // 在AndroidManifest.xml中注册
   <activity android:name=".MainActivity">
       <intent-filter>
           <action android:name="android.intent.action.VIEW" />
           <category android:name="android.intent.category.DEFAULT" />
           <category android:name="android.intent.category.BROWSABLE" />
           <data android:scheme="https" android:host="example.com" />
       </intent-filter>
   </activity>
   
   // 在NavHost中定义
   NavHost(navController, startDestination = "home") {
       composable(
           route = "product/{productId}",
           arguments = listOf(navArgument("productId") { type = NavType.StringType }),
           deepLinks = listOf(navDeepLink { 
               uriPattern = "https://example.com/product/{productId}" 
           })
       ) { backStackEntry ->
           val productId = backStackEntry.arguments?.getString("productId")
           ProductScreen(productId = productId)
       }
   }
   ```

2. **隐式深层链接**：通过代码直接触发的链接
   ```kotlin
   // 创建一个PendingIntent以便稍后使用
   val pendingIntent = navController.createDeepLink()
       .setDestination("profile/user123")
       .setGraph(navController.graph)
       .createPendingIntent()
   ```

### 平台特性与兼容性

- **原生集成**：完全集成Android生命周期和系统组件
- **单平台**：仅适用于Android设备，不跨平台
- **扩展性**：可整合其他Jetpack库，如ViewModel和Hilt
- **版本兼容性**：通过AndroidX向后兼容到旧版本Android

### 完整实例代码

```kotlin
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
import androidx.navigation.*
import androidx.navigation.compose.*
import androidx.navigation.navArgument

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            AppTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    NavigationApp()
                }
            }
        }
    }
}

@Composable
fun NavigationApp() {
    // 创建NavController
    val navController = rememberNavController()
    
    // 定义导航图
    NavHost(
        navController = navController,
        startDestination = "home"
    ) {
        // 首页路由
        composable("home") {
            HomeScreen(
                onNavigateToDetails = { itemId ->
                    navController.navigate("details/$itemId")
                },
                onNavigateToSettings = {
                    navController.navigate("settings")
                }
            )
        }
        
        // 详情页路由（带路径参数）
        composable(
            route = "details/{itemId}",
            arguments = listOf(
                navArgument("itemId") { type = NavType.StringType }
            ),
            // 自定义转场动画
            enterTransition = {
                slideIntoContainer(AnimatedContentTransitionScope.SlideDirection.Left, animationSpec = tween(300))
            },
            exitTransition = {
                slideOutOfContainer(AnimatedContentTransitionScope.SlideDirection.Left, animationSpec = tween(300))
            },
            popEnterTransition = {
                slideIntoContainer(AnimatedContentTransitionScope.SlideDirection.Right, animationSpec = tween(300))
            },
            popExitTransition = {
                slideOutOfContainer(AnimatedContentTransitionScope.SlideDirection.Right, animationSpec = tween(300))
            },
            // 定义深层链接
            deepLinks = listOf(
                navDeepLink { 
                    uriPattern = "https://example.com/details/{itemId}" 
                }
            )
        ) { backStackEntry ->
            // 获取路径参数
            val itemId = backStackEntry.arguments?.getString("itemId")
            DetailsScreen(
                itemId = itemId,
                onNavigateBack = {
                    navController.popBackStack()
                }
            )
        }
        
        // 设置页路由
        composable("settings") {
            SettingsScreen(
                onNavigateBack = {
                    navController.popBackStack()
                },
                onNavigateToHome = {
                    // 弹出所有栈直到首页
                    navController.navigate("home") {
                        popUpTo("home") {
                            inclusive = true
                        }
                    }
                }
            )
        }
        
        // 带查询参数的搜索页
        composable(
            route = "search?query={searchQuery}",
            arguments = listOf(
                navArgument("searchQuery") {
                    type = NavType.StringType
                    defaultValue = ""
                    nullable = true
                }
            )
        ) { backStackEntry ->
            val searchQuery = backStackEntry.arguments?.getString("searchQuery") ?: ""
            SearchScreen(
                query = searchQuery,
                onNavigateBack = {
                    navController.popBackStack()
                }
            )
        }
    }
}

@Composable
fun HomeScreen(
    onNavigateToDetails: (String) -> Unit,
    onNavigateToSettings: () -> Unit
) {
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("首页") },
                actions = {
                    IconButton(onClick = onNavigateToSettings) {
                        Icon(imageVector = Icons.Default.Settings, contentDescription = "设置")
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
            verticalArrangement = Arrangement.Center,
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            Button(onClick = { onNavigateToDetails("123") }) {
                Text("查看详情")
            }
            
            Spacer(modifier = Modifier.height(16.dp))
            
            Button(onClick = { navController.navigate("search?query=初始搜索") }) {
                Text("搜索")
            }
        }
    }
}

@Composable
fun DetailsScreen(
    itemId: String?,
    onNavigateBack: () -> Unit
) {
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("详情") },
                navigationIcon = {
                    IconButton(onClick = onNavigateBack) {
                        Icon(imageVector = Icons.Default.ArrowBack, contentDescription = "返回")
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
            verticalArrangement = Arrangement.Center,
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            Text("商品ID: $itemId", style = MaterialTheme.typography.headlineMedium)
        }
    }
}

@Composable
fun SettingsScreen(
    onNavigateBack: () -> Unit,
    onNavigateToHome: () -> Unit
) {
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("设置") },
                navigationIcon = {
                    IconButton(onClick = onNavigateBack) {
                        Icon(imageVector = Icons.Default.ArrowBack, contentDescription = "返回")
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
            verticalArrangement = Arrangement.Center,
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            Button(onClick = onNavigateToHome) {
                Text("返回首页")
            }
        }
    }
}

@Composable
fun SearchScreen(
    query: String,
    onNavigateBack: () -> Unit
) {
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("搜索") },
                navigationIcon = {
                    IconButton(onClick = onNavigateBack) {
                        Icon(imageVector = Icons.Default.ArrowBack, contentDescription = "返回")
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
            verticalArrangement = Arrangement.Center,
            horizontalAlignment = Alignment.CenterHorizontally
        ) {
            Text("搜索关键词: $query", style = MaterialTheme.typography.headlineMedium)
        }
    }
}

// 主题设置（简化版）
@Composable
fun AppTheme(content: @Composable () -> Unit) {
    MaterialTheme(
        colorScheme = lightColorScheme(),
        content = content
    )
}
```

## UIKit (iOS)

### 路由定义方式

UIKit提供了几种主要的路由方式：

1. **Storyboard与Segue**：通过Interface Builder可视化定义界面间的关系和跳转
2. **代码方式**：使用`UINavigationController`、`UITabBarController`和`UIViewController`的呈现方法
3. **自定义协调器模式(Coordinator)**：解耦视图控制器，集中管理导航逻辑

### 路由传参机制

UIKit传参主要有以下几种方式：

1. **通过属性传递**：
   ```swift
   // 创建目标视图控制器
   let detailsVC = DetailsViewController()
   // 设置参数
   detailsVC.itemId = "123"
   // 跳转
   navigationController?.pushViewController(detailsVC, animated: true)
   ```

2. **通过Segue传递**：
   ```swift
   // 在prepareForSegue方法中传递参数
   override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
       if segue.identifier == "showDetails" {
           if let detailsVC = segue.destination as? DetailsViewController {
               detailsVC.itemId = "123"
           }
       }
   }
   ```

3. **通过初始化方法传递**：
   ```swift
   // 使用自定义初始化方法
   let detailsVC = DetailsViewController(itemId: "123")
   navigationController?.pushViewController(detailsVC, animated: true)
   ```

4. **通过依赖注入**：
   ```swift
   // 使用依赖注入容器或服务
   let detailsVC = DIContainer.shared.resolve(DetailsViewController.self, arguments: "123")
   navigationController?.pushViewController(detailsVC, animated: true)
   ```

### 路由栈管理

UIKit通过`UINavigationController`提供了完整的栈管理功能：

- **入栈**：`pushViewController(_:animated:)`
- **出栈**：`popViewController(animated:)`
- **返回根视图**：`popToRootViewController(animated:)`
- **返回指定视图**：`popToViewController(_:animated:)`
- **设置视图控制器栈**：`setViewControllers(_:animated:)`

模态呈现也有类似的管理方法：
- **呈现**：`present(_:animated:completion:)`
- **关闭**：`dismiss(animated:completion:)`

### 路由转场动画

UIKit支持多种转场动画定制：

1. **系统预设动画**：通过`UIModalTransitionStyle`和`UIModalPresentationStyle`选择

   ```swift
   detailsVC.modalTransitionStyle = .flipHorizontal
   detailsVC.modalPresentationStyle = .fullScreen
   present(detailsVC, animated: true)
   ```

2. **自定义转场动画**：实现`UIViewControllerAnimatedTransitioning`协议

   ```swift
   class CustomTransition: NSObject, UIViewControllerAnimatedTransitioning {
       func transitionDuration(using transitionContext: UIViewControllerContextTransitioning?) -> TimeInterval {
           return 0.5
       }
       
       func animateTransition(using transitionContext: UIViewControllerContextTransitioning) {
           guard let toVC = transitionContext.viewController(forKey: .to) else { return }
           let containerView = transitionContext.containerView
           let finalFrame = transitionContext.finalFrame(for: toVC)
           
           // 设置初始位置
           toVC.view.frame = finalFrame.offsetBy(dx: containerView.bounds.width, dy: 0)
           containerView.addSubview(toVC.view)
           
           // 执行动画
           UIView.animate(withDuration: transitionDuration(using: transitionContext),
                          animations: {
               toVC.view.frame = finalFrame
           }, completion: { finished in
               transitionContext.completeTransition(!transitionContext.transitionWasCancelled)
           })
       }
   }
   
   // 使用自定义转场
   let detailsVC = DetailsViewController()
   detailsVC.transitioningDelegate = self
   present(detailsVC, animated: true)
   
   // 在transitioningDelegate中
   func animationController(forPresented presented: UIViewController, presenting: UIViewController, source: UIViewController) -> UIViewControllerAnimatedTransitioning? {
       return CustomTransition()
   }
   ```

3. **交互式转场**：结合`UIPercentDrivenInteractiveTransition`实现手势驱动的转场

### 深层链接支持

UIKit的深层链接通常通过以下方式实现：

1. **URL Schemes**：在Info.plist中注册自定义URL Scheme
   ```xml
   <key>CFBundleURLTypes</key>
   <array>
       <dict>
           <key>CFBundleURLName</key>
           <string>com.example.myapp</string>
           <key>CFBundleURLSchemes</key>
           <array>
               <string>myapp</string>
           </array>
       </dict>
   </array>
   ```

   在AppDelegate中处理URL打开：
   ```swift
   func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey: Any] = [:]) -> Bool {
       // 解析URL并执行导航
       let path = url.path
       let components = URLComponents(url: url, resolvingAgainstBaseURL: true)
       let params = components?.queryItems?.reduce(into: [String: String]()) { result, item in
           result[item.name] = item.value
       }
       
       // 执行导航逻辑
       if path == "/details" {
           let itemId = params?["id"] ?? ""
           let detailsVC = DetailsViewController(itemId: itemId)
           navigationController?.pushViewController(detailsVC, animated: true)
       }
       
       return true
   }
   ```

2. **Universal Links**：通过网站关联支持从网页直接跳转到应用
   
   在网站上放置apple-app-site-association文件：
   ```json
   {
       "applinks": {
           "apps": [],
           "details": [{
               "appID": "TEAM_ID.com.example.myapp",
               "paths": ["/details/*", "/settings/*"]
           }]
       }
   }
   ```
   
   在AppDelegate中处理Universal Link：
   ```swift
   func application(_ application: UIApplication, continue userActivity: NSUserActivity, restorationHandler: @escaping ([UIUserActivityRestoring]?) -> Void) -> Bool {
       if userActivity.activityType == NSUserActivityTypeBrowsingWeb,
          let url = userActivity.webpageURL,
          let components = URLComponents(url: url, resolvingAgainstBaseURL: true) {
            
           // 处理导航
           let path = components.path
           
           if path.hasPrefix("/details/") {
               let itemId = path.replacingOccurrences(of: "/details/", with: "")
               let detailsVC = DetailsViewController(itemId: itemId)
               navigationController?.pushViewController(detailsVC, animated: true)
           }
           
           return true
       }
       return false
   }
   ```

### 平台特性与兼容性

- **原生集成**：完全集成iOS系统和生命周期
- **性能优势**：直接使用原生组件，性能和内存管理最优
- **单平台**：仅适用于iOS设备
- **生态系统**：可与其他UIKit和iOS特性无缝集成，如3D Touch、Context Menus等
- **向后兼容**：Apple保持良好的向后兼容性

### 完整实例代码

```swift
import UIKit

// MARK: - App Delegate
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?
    var navigationController: UINavigationController?
    
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        window = UIWindow(frame: UIScreen.main.bounds)
        
        let homeVC = HomeViewController()
        navigationController = UINavigationController(rootViewController: homeVC)
        
        window?.rootViewController = navigationController
        window?.makeKeyAndVisible()
        
        return true
    }
    
    // 处理深层链接
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey: Any] = [:]) -> Bool {
        // 解析URL
        let components = URLComponents(url: url, resolvingAgainstBaseURL: true)
        let path = url.path
        
        // 根据URL执行导航
        if path == "/details" {
            let itemId = components?.queryItems?.first(where: { $0.name == "id" })?.value ?? ""
            let detailsVC = DetailsViewController(itemId: itemId)
            navigationController?.pushViewController(detailsVC, animated: true)
            return true
        }
        
        return false
    }
    
    // 处理Universal Links
    func application(_ application: UIApplication, continue userActivity: NSUserActivity, restorationHandler: @escaping ([UIUserActivityRestoring]?) -> Void) -> Bool {
        if userActivity.activityType == NSUserActivityTypeBrowsingWeb,
           let url = userActivity.webpageURL,
           let components = URLComponents(url: url, resolvingAgainstBaseURL: true) {
            
            let path = components.path
            
            if path.hasPrefix("/details/") {
                let itemId = path.replacingOccurrences(of: "/details/", with: "")
                let detailsVC = DetailsViewController(itemId: itemId)
                navigationController?.pushViewController(detailsVC, animated: true)
                return true
            }
        }
        return false
    }
}

// MARK: - 主页
class HomeViewController: UIViewController {
    
    private let detailsButton = UIButton(type: .system)
    private let settingsButton = UIButton(type: .system)
    private let customAnimationButton = UIButton(type: .system)
    
    override func viewDidLoad() {
        super.viewDidLoad()
        title = "首页"
        view.backgroundColor = .white
        setupUI()
    }
    
    private func setupUI() {
        detailsButton.setTitle("查看详情", for: .normal)
        detailsButton.addTarget(self, action: #selector(showDetails), for: .touchUpInside)
        
        settingsButton.setTitle("打开设置", for: .normal)
        settingsButton.addTarget(self, action: #selector(showSettings), for: .touchUpInside)
        
        customAnimationButton.setTitle("自定义动画", for: .normal)
        customAnimationButton.addTarget(self, action: #selector(showWithCustomAnimation), for: .touchUpInside)
        
        [detailsButton, settingsButton, customAnimationButton].forEach {
            $0.translatesAutoresizingMaskIntoConstraints = false
            view.addSubview($0)
        }
        
        NSLayoutConstraint.activate([
            detailsButton.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            detailsButton.centerYAnchor.constraint(equalTo: view.centerYAnchor, constant: -50),
            
            settingsButton.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            settingsButton.topAnchor.constraint(equalTo: detailsButton.bottomAnchor, constant: 20),
            
            customAnimationButton.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            customAnimationButton.topAnchor.constraint(equalTo: settingsButton.bottomAnchor, constant: 20)
        ])
    }
    
    @objc private func showDetails() {
        // 创建并传递参数
        let detailsVC = DetailsViewController(itemId: "123")
        navigationController?.pushViewController(detailsVC, animated: true)
    }
    
    @objc private func showSettings() {
        let settingsVC = SettingsViewController()
        let navigationController = UINavigationController(rootViewController: settingsVC)
        present(navigationController, animated: true)
    }
    
    @objc private func showWithCustomAnimation() {
        let detailsVC = DetailsViewController(itemId: "456")
        detailsVC.transitioningDelegate = self
        detailsVC.modalPresentationStyle = .custom
        present(detailsVC, animated: true)
    }
}

// 自定义转场动画代理
extension HomeViewController: UIViewControllerTransitioningDelegate {
    func animationController(forPresented presented: UIViewController, presenting: UIViewController, source: UIViewController) -> UIViewControllerAnimatedTransitioning? {
        return CustomTransitionAnimation()
    }
    
    func animationController(forDismissed dismissed: UIViewController) -> UIViewControllerAnimatedTransitioning? {
        return CustomDismissAnimation()
    }
}

// MARK: - 详情页
class DetailsViewController: UIViewController {
    
    private let itemId: String
    private let infoLabel = UILabel()
    private let backButton = UIButton(type: .system)
    
    // 通过构造函数传参
    init(itemId: String) {
        self.itemId = itemId
        super.init(nibName: nil, bundle: nil)
    }
    
    required init?(coder: NSCoder) {
        self.itemId = ""
        super.init(coder: coder)
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        title = "详情"
        view.backgroundColor = .white
        setupUI()
    }
    
    private func setupUI() {
        infoLabel.text = "商品ID: \(itemId)"
        infoLabel.textAlignment = .center
        infoLabel.font = .boldSystemFont(ofSize: 20)
        
        backButton.setTitle("返回", for: .normal)
        backButton.addTarget(self, action: #selector(goBack), for: .touchUpInside)
        
        [infoLabel, backButton].forEach {
            $0.translatesAutoresizingMaskIntoConstraints = false
            view.addSubview($0)
        }
        
        NSLayoutConstraint.activate([
            infoLabel.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            infoLabel.centerYAnchor.constraint(equalTo: view.centerYAnchor),
            
            backButton.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            backButton.topAnchor.constraint(equalTo: infoLabel.bottomAnchor, constant: 20)
        ])
    }
    
    @objc private func goBack() {
        if navigationController != nil {
            navigationController?.popViewController(animated: true)
        } else {
            dismiss(animated: true)
        }
    }
}

// MARK: - 设置页
class SettingsViewController: UIViewController {
    
    private let homeButton = UIButton(type: .system)
    
    override func viewDidLoad() {
        super.viewDidLoad()
        title = "设置"
        view.backgroundColor = .white
        setupUI()
    }
    
    private func setupUI() {
        homeButton.setTitle("返回首页", for: .normal)
        homeButton.addTarget(self, action: #selector(goToHome), for: .touchUpInside)
        
        homeButton.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(homeButton)
        
        NSLayoutConstraint.activate([
            homeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            homeButton.centerYAnchor.constraint(equalTo: view.centerYAnchor)
        ])
        
        // 添加关闭按钮
        navigationItem.leftBarButtonItem = UIBarButtonItem(
            barButtonSystemItem: .close,
            target: self,
            action: #selector(dismiss(_:))
        )
    }
    
    @objc private func goToHome() {
        // 关闭所有模态视图，回到根视图
        UIApplication.shared.windows.first?.rootViewController?.dismiss(animated: true)
    }
    
    @objc private func dismiss(_ sender: UIBarButtonItem) {
        dismiss(animated: true)
    }
}

// MARK: - 自定义转场动画
class CustomTransitionAnimation: NSObject, UIViewControllerAnimatedTransitioning {
    func transitionDuration(using transitionContext: UIViewControllerContextTransitioning?) -> TimeInterval {
        return 0.5
    }
    
    func animateTransition(using transitionContext: UIViewControllerContextTransitioning) {
        guard let toVC = transitionContext.viewController(forKey: .to) else { return }
        let containerView = transitionContext.containerView
        let finalFrame = transitionContext.finalFrame(for: toVC)
        
        // 初始位置 - 从底部滑入
        toVC.view.frame = finalFrame.offsetBy(dx: 0, dy: containerView.bounds.height)
        containerView.addSubview(toVC.view)
        
        // 动画
        UIView.animate(
            withDuration: transitionDuration(using: transitionContext),
            delay: 0,
            usingSpringWithDamping: 0.8,
            initialSpringVelocity: 0.5,
            options: [],
            animations: {
                toVC.view.frame = finalFrame
            },
            completion: { finished in
                transitionContext.completeTransition(!transitionContext.transitionWasCancelled)
            }
        )
    }
}

class CustomDismissAnimation: NSObject, UIViewControllerAnimatedTransitioning {
    func transitionDuration(using transitionContext: UIViewControllerContextTransitioning?) -> TimeInterval {
        return 0.3
    }
    
    func animateTransition(using transitionContext: UIViewControllerContextTransitioning) {
        guard let fromVC = transitionContext.viewController(forKey: .from) else { return }
        let containerView = transitionContext.containerView
        let initialFrame = transitionContext.initialFrame(for: fromVC)
        
        // 从当前位置移动到底部
        UIView.animate(
            withDuration: transitionDuration(using: transitionContext),
            animations: {
                fromVC.view.frame = initialFrame.offsetBy(dx: 0, dy: containerView.bounds.height)
            },
            completion: { finished in
                transitionContext.completeTransition(!transitionContext.transitionWasCancelled)
            }
        )
    }
}
```

## SwiftUI (iOS)

### 路由定义方式

SwiftUI使用声明式导航系统，主要有以下几种路由方式：

1. **NavigationStack/NavigationView**：构建导航层次结构
2. **NavigationDestination/NavigationLink**：定义导航链接和目标
3. **TabView**：创建标签式导航
4. **以程序方式通过NavigationPath控制**：集中管理导航状态

自iOS 16起，SwiftUI引入了新的导航API，包括`NavigationStack`和`NavigationSplitView`，替代了旧的`NavigationView`，提供了更强大的路由控制能力。

### 路由传参机制

SwiftUI传参有以下主要方式：

1. **直接传参**：在创建导航链接时直接传递数据
   ```swift
   NavigationLink {
       DetailView(item: item)
   } label: {
       Text(item.name)
   }
   ```

2. **通过标识符传参**：使用`navigationDestination(for:)`注册类型处理
   ```swift
   NavigationStack {
       List(items) { item in
           NavigationLink(value: item) {
               Text(item.name)
           }
       }
       .navigationDestination(for: Item.self) { item in
           DetailView(item: item)
       }
   }
   ```

3. **环境变量和环境对象**：通过`.environmentObject`和`.environment`传递共享数据
   ```swift
   // 在根视图
   ContentView()
       .environmentObject(UserData())
   
   // 在目标视图
   @EnvironmentObject var userData: UserData
   ```

4. **路径参数**：使用NavigationPath处理导航栈，支持不同类型的值
   ```swift
   @State private var path = NavigationPath()
   
   var body: some View {
       NavigationStack(path: $path) {
           VStack {
               Button("Push String") {
                   path.append("String Value")
               }
               Button("Push Int") {
                   path.append(42)
               }
           }
           .navigationDestination(for: String.self) { stringValue in
               Text("String: \(stringValue)")
           }
           .navigationDestination(for: Int.self) { intValue in
               Text("Int: \(intValue)")
           }
       }
   }
   ```

### 路由栈管理

SwiftUI提供了多种路由栈管理方式：

1. **使用NavigationPath**：
   ```swift
   @State private var path = NavigationPath()
   
   var body: some View {
       NavigationStack(path: $path) {
           // 内容
           Button("回到根视图") {
               path.removeLast(path.count) // 清空导航栈
           }
       }
   }
   ```

2. **可编码导航状态**：持久化导航状态
   ```swift
   struct NavigationState: Codable {
       var routes: [Route]
       
       enum Route: Codable {
           case details(id: String)
           case settings
           case profile(username: String)
       }
   }
   
   @SceneStorage("navigation") private var navigationData: Data?
   @State private var navigationState = NavigationState(routes: [])
   ```

3. **以编程方式控制**：
   ```swift
   Button("前进") {
       path.append(nextItem)
   }
   
   Button("返回") {
       path.removeLast()
   }
   
   Button("回到特定页面") {
       // 弹出到指定深度
       let targetDepth = 1
       if path.count > targetDepth {
           path.removeLast(path.count - targetDepth)
       }
   }
   ```

### 路由转场动画

SwiftUI提供了多种转场动画方式：

1. **使用内置转场**：
   ```swift
   NavigationLink(destination: DetailView()) {
       Text("查看详情")
   }
   .buttonStyle(.borderedProminent)
   .navigationTransition(.slide) // iOS 17+
   ```

2. **自定义转场效果**：
   ```swift
   NavigationLink {
       DetailView()
           .transition(.asymmetric(
               insertion: .move(edge: .trailing),
               removal: .move(edge: .leading)
           ))
   } label: {
       Text("自定义转场")
   }
   .buttonStyle(.bordered)
   ```

3. **匹配几何效果**：
   ```swift
   @Namespace private var animation
   @State private var showDetail = false
   
   var body: some View {
       if !showDetail {
           RoundedRectangle(cornerRadius: 20)
               .fill(.blue)
               .frame(width: 100, height: 100)
               .matchedGeometryEffect(id: "shape", in: animation)
               .onTapGesture {
                   withAnimation(.spring()) {
                       showDetail = true
                   }
               }
       } else {
           RoundedRectangle(cornerRadius: 20)
               .fill(.blue)
               .frame(width: 300, height: 200)
               .matchedGeometryEffect(id: "shape", in: animation)
               .onTapGesture {
                   withAnimation(.spring()) {
                       showDetail = false
                   }
               }
       }
   }
   ```

### 深层链接支持

SwiftUI深层链接实现主要通过以下方式：

1. **URL处理**：使用`onOpenURL`处理URL回调
   ```swift
   @State private var path = NavigationPath()
   
   var body: some View {
       NavigationStack(path: $path) {
           HomeView()
               .navigationDestination(for: Product.self) { product in
                   ProductDetailView(product: product)
               }
       }
       .onOpenURL { url in
           // 处理URL并更新导航路径
           handleDeepLink(url: url)
       }
   }
   
   private func handleDeepLink(url: URL) {
       guard let components = URLComponents(url: url, resolvingAgainstBaseURL: true),
             let host = components.host else {
           return
       }
       
       switch host {
       case "product":
           if let productId = components.queryItems?.first(where: { $0.name == "id" })?.value {
               let product = Product(id: productId, name: "从链接加载")
               path.append(product)
           }
       case "settings":
           path.append("settings")
       default:
           break
       }
   }
   ```

2. **场景配置**：在Info.plist和SceneDelegate中配置URL处理
   ```xml
   <!-- Info.plist -->
   <key>CFBundleURLTypes</key>
   <array>
       <dict>
           <key>CFBundleURLName</key>
           <string>com.example.myapp</string>
           <key>CFBundleURLSchemes</key>
           <array>
               <string>myapp</string>
           </array>
       </dict>
   </array>
   ```

3. **集成AppClip和Universal Links**：遵循与UIKit类似的系统配置

### 平台特性与兼容性

- **声明式API**：与SwiftUI整体声明式风格一致
- **状态驱动**：路由建立在SwiftUI的状态管理系统之上
- **跨苹果平台**：同样的路由代码适用于iOS、iPadOS、macOS、watchOS和tvOS
- **版本差异**：因SwiftUI快速迭代，各版本API存在差异
- **iOS版本要求**：基本功能要求iOS 13+，现代导航API要求iOS 16+
- **集成能力**：可与UIKit导航协调器结合使用（通过UIHostingController）

### 完整实例代码

```swift
import SwiftUI

// 数据模型
struct Product: Identifiable, Hashable {
    let id: String
    let name: String
    let description: String
    
    init(id: String, name: String, description: String = "产品描述") {
        self.id = id
        self.name = name
        self.description = description
    }
}

// 数据管理类
class DataModel {
    private static instance: DataModel;
    private products: Array<Product> = [];
    
    private constructor() {
        // 初始化示例数据
        self.products = [
            new Product('1', '产品一', '这是产品一的详细描述'),
            new Product('2', '产品二', '这是产品二的详细描述'),
            new Product('3', '产品三', '这是产品三的详细描述')
        ];
    }
    
    static getInstance(): DataModel {
        if (!DataModel.instance) {
            DataModel.instance = new DataModel();
        }
        return DataModel.instance;
    }
    
    getProducts(): Array<Product> {
        return this.products;
    }
    
    getProductById(id: string): Product | undefined {
        return this.products.find(product => product.id === id);
    }
}

// 主应用结构
@main
struct NavigationDemoApp: App {
    @StateObject private var deepLinkHandler = DeepLinkHandler()
    @StateObject private var userData = UserData()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(userData)
                .environmentObject(deepLinkHandler)
                .onOpenURL { url in
                    deepLinkHandler.handleDeepLink(url: url)
                }
        }
    }
}

// 内容视图
struct ContentView: View {
    @EnvironmentObject private var deepLinkHandler: DeepLinkHandler
    @State private var selectedTab = 0
    
    var body: some View {
        TabView(selection: $selectedTab) {
            // 首页标签 - 使用NavigationStack
            NavigationStack(path: $deepLinkHandler.navigationPath) {
                HomeView()
                    .navigationDestination(for: Product.self) { product in
                        ProductDetailView(product: product)
                    }
                    .navigationDestination(for: String.self) { route in
                        switch route {
                        case "settings":
                            SettingsView()
                        default:
                            Text("未知路由: \(route)")
                        }
                    }
            }
            .tabItem {
                Label("首页", systemImage: "house")
            }
            .tag(0)
            
            // 个人资料标签
            NavigationStack {
                ProfileView()
            }
            .tabItem {
                Label("我的", systemImage: "person")
            }
            .tag(1)
        }
    }
}

// 首页
struct HomeView: View {
    @EnvironmentObject private var deepLinkHandler: DeepLinkHandler
    
    // 示例数据
    let products = [
        Product(id: "1", name: "产品一", description: "这是产品一的详细描述"),
        Product(id: "2", name: "产品二", description: "这是产品二的详细描述"),
        Product(id: "3", name: "产品三", description: "这是产品三的详细描述")
    ]
    
    var body: some View {
        List {
            Section(header: Text("产品列表")) {
                ForEach(products) { product in
                    // 使用navigationDestination注册的路由
                    NavigationLink(value: product) {
                        HStack {
                            RoundedRectangle(cornerRadius: 8)
                                .fill(.blue.opacity(0.2))
                                .frame(width: 50, height: 50)
                            VStack(alignment: .leading) {
                                Text(product.name)
                                    .font(.headline)
                                Text("产品ID: \(product.id)")
                                    .font(.caption)
                                    .foregroundColor(.secondary)
                            }
                        }
                    }
                }
            }
            
            Section(header: Text("导航示例")) {
                // 进入设置页
                Button("打开设置") {
                    deepLinkHandler.navigationPath.append("settings")
                }
                
                // 示例：深层链接模拟
                Button("模拟深层链接:产品") {
                    // 构造一个URL并处理它
                    if let url = URL(string: "myapp://product?id=99") {
                        deepLinkHandler.handleDeepLink(url: url)
                    }
                }
                
                // 清空导航栈
                Button("返回首页(清空栈)") {
                    deepLinkHandler.navigationPath.removeLast(deepLinkHandler.navigationPath.count)
                }
                
                // 使用自定义转场效果
                NavigationLink {
                    CustomAnimationView()
                } label: {
                    Text("自定义转场动画示例")
                }
            }
        }
        .navigationTitle("SwiftUI导航示例")
    }
}

// 产品详情页
struct ProductDetailView: View {
    let product: Product
    @EnvironmentObject private var deepLinkHandler: DeepLinkHandler
    
    var body: some View {
        VStack(spacing: 20) {
            RoundedRectangle(cornerRadius: 12)
                .fill(.blue.opacity(0.3))
                .frame(height: 200)
                .overlay(
                    Text(product.name)
                        .font(.title)
                        .bold()
                )
            
            Text("产品ID: \(product.id)")
                .font(.headline)
            
            Text(product.description)
                .padding()
                .background(RoundedRectangle(cornerRadius: 8).fill(.gray.opacity(0.1)))
            
            Spacer()
            
            // 示例：多层导航
            Button("查看相关产品") {
                let relatedProduct = Product(id: "related-\(product.id)", name: "相关产品", description: "与\(product.name)相关的产品")
                deepLinkHandler.navigationPath.append(relatedProduct)
            }
            .buttonStyle(.borderedProminent)
        }
        .padding()
        .navigationTitle(product.name)
        .navigationBarTitleDisplayMode(.inline)
    }
}

// 设置页面
struct SettingsView: View {
    @EnvironmentObject private var userData: UserData
    
    var body: some View {
        Form {
            Section(header: Text("用户信息")) {
                TextField("用户名", text: $userData.username)
                
                Toggle("登录状态", isOn: $userData.isLoggedIn)
            }
            
            Section(header: Text("应用设置")) {
                NavigationLink("隐私设置") {
                    Text("隐私设置内容")
                        .navigationTitle("隐私设置")
                }
                
                NavigationLink("通知设置") {
                    Text("通知设置内容")
                        .navigationTitle("通知设置")
                }
            }
        }
        .navigationTitle("设置")
    }
}

// 个人资料页面
struct ProfileView: View {
    @EnvironmentObject private var userData: UserData
    
    var body: some View {
        VStack(spacing: 20) {
            Image(systemName: "person.circle")
                .resizable()
                .scaledToFit()
                .frame(width: 100, height: 100)
                .foregroundColor(.blue)
            
            Text(userData.username)
                .font(.title)
            
            Text(userData.isLoggedIn ? "已登录" : "未登录")
                .foregroundColor(userData.isLoggedIn ? .green : .red)
            
            Spacer()
        }
        .padding()
        .navigationTitle("个人资料")
    }
}

// 自定义转场动画示例
struct CustomAnimationView: View {
    @State private var showDetail = false
    @Namespace private var animation
    
    var body: some View {
        VStack {
            Text("自定义转场动画")
                .font(.title)
                .padding()
            
            if !showDetail {
                // 初始视图
                RoundedRectangle(cornerRadius: 20)
                    .fill(.blue.gradient)
                    .frame(width: 200, height: 200)
                    .matchedGeometryEffect(id: "shape", in: animation)
                    .overlay(
                        Text("点击展开")
                            .foregroundColor(.white)
                            .matchedGeometryEffect(id: "text", in: animation)
                    )
                    .onTapGesture {
                        withAnimation(.spring(response: 0.6, dampingFraction: 0.8)) {
                            showDetail = true
                        }
                    }
            } else {
                // 展开的视图
                VStack {
                    RoundedRectangle(cornerRadius: 20)
                        .fill(.blue.gradient)
                        .frame(height: 300)
                        .matchedGeometryEffect(id: "shape", in: animation)
                        .overlay(
                            Text("点击关闭")
                                .foregroundColor(.white)
                                .matchedGeometryEffect(id: "text", in: animation)
                        )
                }
                .padding()
                .onTapGesture {
                    withAnimation(.spring(response: 0.6, dampingFraction: 0.8)) {
                        showDetail = false
                    }
                }
            }
            
            Spacer()
        }
        .navigationTitle("动画示例")
        .navigationBarTitleDisplayMode(.inline)
    }
}
```

## 鸿蒙 (ArkUI)

### 路由定义方式

鸿蒙HarmonyOS的ArkUI提供了以下几种路由机制：

1. **页面路由（Page Routing）**：在同一个Ability内的页面跳转
   - `Navigator`组件：用于不同页面间的切换
   - `RouterLink`：类似超链接的导航方式

2. **Ability路由**：跨应用组件的跳转
   - 使用`AbilityRouter`在不同Ability间导航

3. **自定义路由**：通过`Router`类实现自定义的导航控制器

### 路由传参机制

ArkUI提供了多种传参方式：

1. **页面路由传参**：通过`Navigator`和`router.pushUrl()`

   ```typescript
   // 页面路由跳转并传参
   router.pushUrl({
     url: 'pages/Detail',
     params: {
       id: '123',
       name: '产品名称'
     }
   });
   
   // 在目标页面获取参数
   const params = router.getParams();
   const id = params['id'];
   const name = params['name'];
   ```

2. **Ability间传参**：使用`AbilityContext`传递数据

   ```typescript
   // 启动另一个Ability并传参
   let context = this.context;
   let want = {
     bundleName: 'com.example.myapplication',
     abilityName: 'MainAbility',
     parameters: {
       data: '要传递的数据'
     }
   };
   context.startAbility(want);
   
   // 在目标Ability中获取参数
   onAbilityCreated(want, launchParam) {
     const parameters = want.parameters;
     const data = parameters.data;
   }
   ```

3. **全局状态管理**：使用AppStorage或PersistentStorage

   ```typescript
   // 在源页面设置数据
   AppStorage.SetOrCreate('userData', { id: '123', name: '用户名' });
   
   // 在目标页面读取数据
   @StorageLink('userData') userData: any = {};
   ```

### 路由栈管理

ArkUI提供了完整的路由栈管理API：

1. **基本导航操作**：
   - `router.push()`：向前导航到新页面
   - `router.pop()`：返回上一页
   - `router.back()`：返回上一页或指定页面
   - `router.clear()`：清空路由栈
   - `router.replace()`：替换当前页面

2. **路由历史**：
   ```typescript
   // 获取导航栈的长度
   const length = router.getLength();
   
   // 获取当前路由状态
   const state = router.getState();
   
   // 导航到指定索引的页面
   router.showAlertBeforeBackPage({
     message: '确定要返回吗？',
     onConfirm: () => {
       router.back({ index: 1 });
     }
   });
   ```

3. **路由拦截器**：支持全局拦截和页面级拦截
   ```typescript
   // 注册全局路由拦截器
   router.beforeRouteMove((to, from, next) => {
     // 权限检查
     if (to.path === 'pages/Protected' && !isLoggedIn()) {
       // 重定向到登录页
       next({ path: 'pages/Login' });
     } else {
       // 正常导航
       next();
     }
   });
   ```

### 路由转场动画

ArkUI支持多种页面转场动画：

1. **系统预置动画**：
   ```typescript
   // 使用预设动画
   router.pushUrl({
     url: 'pages/Detail',
     params: { id: '123' },
     animation: {
       type: RouteAnimationType.SLIDE, // 滑动动画
       duration: 300 // 持续时间
     }
   });
   ```

   常见的动画类型包括：
   - `SLIDE`：滑动
   - `FADE`：淡入淡出
   - `NONE`：无动画
   - `TRANSLATE`：平移
   - `SCALE`：缩放

2. **自定义转场动画**：
   ```typescript
   // 自定义动画
   router.pushUrl({
     url: 'pages/Detail',
     params: { id: '123' },
     animation: {
       type: RouteAnimationType.CUSTOM,
       enterAnimation: {
         // 页面进入的动画
         translate: { x: '100%', y: 0 },
         opacity: 0,
         scale: { x: 0.8, y: 0.8, z: 1.0 },
         duration: 300,
         delay: 0,
         curve: Curve.EaseOut
       },
       exitAnimation: {
         // 当前页面退出的动画
         translate: { x: '-30%', y: 0 },
         opacity: 0,
         scale: { x: 0.8, y: 0.8, z: 1.0 },
         duration: 300,
         delay: 0,
         curve: Curve.EaseIn
       }
     }
   });
   ```

3. **转场动画组件**：使用页面转场组件
   ```typescript
   @Entry
   @Component
   struct TransitionExample {
     @State isShow: boolean = false;
     
     build() {
       Column() {
         Button('切换页面')
           .onClick(() => {
             this.isShow = !this.isShow;
           })
         
         if (this.isShow) {
           DetailPage()
             .transition({ type: TransitionType.TRANSLATE, translate: { x: '100%', y: 0 } })
         } else {
           HomePage()
             .transition({ type: TransitionType.TRANSLATE, translate: { x: '100%', y: 0 } })
         }
       }
     }
   }
   ```

### 深层链接支持

鸿蒙OS支持通过以下方式实现深层链接：

1. **URI Scheme**：在`module.json5`中注册URI
   ```json
   {
     "abilities": [{
       "name": "MainAbility",
       "uris": [
         {
           "scheme": "scheme://example.com",
           "path": "/detail/*"
         }
       ]
     }]
   }
   ```

2. **Intent过滤器**：声明Intent Filter
   ```json
   {
     "ability": {
       "actions": [
         "com.example.action.VIEW"
       ],
       "entities": [
         "entity.system.browsable"
       ],
       "uris": [
         {
           "scheme": "https",
           "host": "example.com",
           "path": "/detail"
         }
       ]
     }
   }
   ```

3. **处理深层链接**：在Ability中接收和处理
   ```typescript
   onWant(want) {
     if (want.uri) {
       const uri = new URL(want.uri);
       const path = uri.pathname;
       const query = uri.search; // 解析查询参数
       
       // 处理deep link
       if (path === '/detail') {
         const productId = uri.searchParams.get('id');
         router.pushUrl({
           url: 'pages/Detail',
           params: {
             productId: productId,
             fromDeepLink: true
           }
         });
       } else if (path === '/settings') {
         router.pushUrl({
           url: 'pages/Settings'
         });
       }
     }
   }
   ```

### 平台特性与兼容性

- **HarmonyOS专属**：仅适用于鸿蒙系统设备
- **分布式能力**：支持跨设备间的路由和页面迁移
- **生态优势**：与华为生态系统深度集成
- **国产化**：符合国内自主可控要求
- **跨平台限制**：不能直接在iOS/Android上运行，但有部分技术兼容
- **多设备适配**：一套代码可适配手机、平板、智能穿戴、智慧屏等设备

### 完整实例代码

```typescript
// 定义一个基础模型
class Product {
  id: string;
  name: string;
  description: string;
  
  constructor(id: string, name: string, description: string = '产品描述') {
    this.id = id;
    this.name = name;
    this.description = description;
  }
}

// 数据管理类
class DataModel {
  private static instance: DataModel;
  private products: Array<Product> = [];
  
  private constructor() {
    // 初始化示例数据
    this.products = [
      new Product('1', '产品一', '这是产品一的详细描述'),
      new Product('2', '产品二', '这是产品二的详细描述'),
      new Product('3', '产品三', '这是产品三的详细描述')
    ];
  }
  
  static getInstance(): DataModel {
    if (!DataModel.instance) {
      DataModel.instance = new DataModel();
    }
    return DataModel.instance;
  }
  
  getProducts(): Array<Product> {
    return this.products;
  }
  
  getProductById(id: string): Product | undefined {
    return this.products.find(product => product.id === id);
  }
}

// 首页
@Entry
@Component
struct HomePage {
  @State products: Array<Product> = DataModel.getInstance().getProducts();
  
  build() {
    Column() {
      Navigation() {
        List() {
          ForEach(this.products, (product: Product) => {
            ListItem() {
              // 商品项，点击跳转到详情页
              Row() {
                Image($r('app.media.product_icon'))
                  .width(50)
                  .height(50)
                  .margin({ right: 10 })
                
                Column() {
                  Text(product.name)
                    .font(.headline)
                    .fontWeight(FontWeight.Bold)
                  
                  Text("产品ID: \(product.id)")
                    .font(.caption)
                    .foregroundColor(.secondary)
                }
                .alignItems(HorizontalAlign.Start)
                .layoutWeight(1)
              }
              .width('100%')
              .padding(10)
              .borderRadius(8)
              .backgroundColor(Color.White)
              .onClick(() => {
                // 使用路由跳转并传参
                router.pushUrl({
                  url: 'pages/DetailPage',
                  params: {
                    productId: product.id
                  },
                  animation: {
                    type: RouteAnimationType.SLIDE,
                    duration: 300
                  }
                });
              })
            }
            .margin({ bottom: 10 })
          })
        }
        .divider({ strokeWidth: 1, color: '#eeeeee' })
        .padding(15)
        .backgroundColor('#f5f5f5')
        
        // 底部操作区
        Row() {
          Button('跳转到设置页')
            .onClick(() => {
              router.pushUrl({
                url: 'pages/SettingsPage'
              });
            })
          
          Button('模拟深层链接')
            .margin({ left: 20 })
            .onClick(() => {
              // 模拟处理深层链接
              router.pushUrl({
                url: 'pages/DetailPage',
                params: {
                  productId: '99',
                  fromDeepLink: true
                }
              });
            })
        }
        .width('100%')
        .justifyContent(FlexAlign.Center)
        .padding({ top: 20, bottom: 20 })
      }
      .title('鸿蒙路由示例')
      .titleMode(NavigationTitleMode.Full)
      .mode(NavigationMode.Stack)
      .backgroundColor('#f5f5f5')
    }
  }
  
  // 页面生命周期
  onPageShow() {
    console.info('HomePage: onPageShow');
  }
  
  onBackPress(): boolean {
    console.info('HomePage: onBackPress');
    // 返回键处理
    return false; // 返回true表示自己处理，false表示继续默认行为
  }
}

// 详情页
@Entry
@Component
struct DetailPage {
  // 接收传递的参数
  private productId: string = router.getParams()['productId'] || '';
  private fromDeepLink: boolean = router.getParams()['fromDeepLink'] || false;
  
  @State product: Product | undefined = undefined;
  
  aboutToAppear() {
    // 根据ID获取产品信息
    this.product = DataModel.getInstance().getProductById(this.productId);
    
    // 如果找不到产品，创建一个临时产品
    if (!this.product) {
      this.product = new Product(
        this.productId,
        this.fromDeepLink ? '从深层链接加载的产品' : '未知产品'
      );
    }
  }
  
  build() {
    Column() {
      Navigation() {
        Column() {
          if (this.product) {
            // 产品图片区域
            Image($r('app.media.product_detail'))
              .width('100%')
              .height(200)
              .objectFit(ImageFit.Cover)
            
            // 产品信息区域
            Column() {
              Text(this.product.name)
                .font(.title)
                .bold()
                .margin({ top: 16, bottom: 8 })
              
              Text(`产品ID: ${this.product.id}`)
                .font(.caption)
                .foregroundColor(.secondary)
              
              Text(this.product.description)
                .font(.body)
                .margin({ top: 16 })
                .padding(16)
                .background(RoundedRectangle(cornerRadius: 8).fill(.gray.opacity(0.1)))
            }
            .width('100%')
            .padding(16)
            
            // 操作按钮
            Row() {
              Button('相关商品')
                .onClick(() => {
                  // 跳转到另一个详情页，模拟多级导航
                  router.pushUrl({
                    url: 'pages/DetailPage',
                    params: {
                      productId: 'related-' + this.productId,
                      name: '相关产品'
                    }
                  });
                })
              
              Button('返回首页')
                .margin({ left: 16 })
                .onClick(() => {
                  // 清空导航栈并返回首页
                  router.clear();
                  router.pushUrl({
                    url: 'pages/HomePage'
                  });
                })
            }
            .width('100%')
            .justifyContent(FlexAlign.Center)
            .margin({ top: 32 })
          } else {
            // 产品不存在的情况
            Text('产品不存在')
              .font(.title)
              .fontColor(Color.Red)
          }
        }
        .width('100%')
      }
      .title(this.product?.name || '产品详情')
      .titleMode(NavigationTitleMode.Full)
      .mode(NavigationMode.Stack)
      .hideBackButton(false)
      .onTitleModeChange((titleMode: NavigationTitleMode) => {
        console.info('Navigation title mode changed to: ' + titleMode);
      })
    }
  }
  
  // 页面生命周期
  onPageShow() {
    console.info('DetailPage: onPageShow with productId: ' + this.productId);
  }
  
  onBackPress(): boolean {
    console.info('DetailPage: onBackPress');
    
    if (this.fromDeepLink) {
      // 如果是深层链接打开的，返回时直接回到首页
      router.clear();
      router.pushUrl({
        url: 'pages/HomePage'
      });
      return true; // 返回true表示已处理
    }
    
    return false; // 执行默认返回行为
  }
}

// 设置页面
@Entry
@Component
struct SettingsPage {
  @State darkMode: boolean = false;
  @State fontSize: number = 16;
  
  build() {
    Column() {
      Navigation() {
        List() {
          ListItem() {
            Row() {
              Text('深色模式')
                .fontSize(16)
              
              Toggle({ type: ToggleType.Switch, isOn: this.darkMode })
                .onChange((isOn: boolean) => {
                  this.darkMode = isOn;
                })
            }
            .width('100%')
            .justifyContent(FlexAlign.SpaceBetween)
            .padding({ left: 16, right: 16, top: 12, bottom: 12 })
          }
          
          ListItem() {
            Row() {
              Text('字体大小')
                .fontSize(16)
              
              Slider({
                value: this.fontSize,
                min: 12,
                max: 24,
                step: 1
              })
                .width(200)
                .onChange((value: number) => {
                  this.fontSize = value;
                })
              
              Text(this.fontSize.toFixed(0))
                .fontSize(16)
            }
            .width('100%')
            .justifyContent(FlexAlign.SpaceBetween)
            .padding({ left: 16, right: 16, top: 12, bottom: 12 })
          }
          
          ListItem() {
            Button('返回首页并清空导航栈')
              .width('100%')
              .height(40)
              .onClick(() => {
                router.clear();
                router.pushUrl({
                  url: 'pages/HomePage'
                });
              })
          }
          .padding(16)
          
          ListItem() {
            Button('拦截器测试')
              .width('100%')
              .height(40)
              .onClick(() => {
                // 展示拦截器功能
                router.showAlertBeforeBackPage({
                  message: '确定要返回吗？未保存的更改将会丢失。',
                  onConfirm: () => {
                    router.back();
                  }
                });
              })
          }
          .padding(16)
          
          ListItem() {
            Button('自定义动画跳转')
              .width('100%')
              .height(40)
              .onClick(() => {
                router.pushUrl({
                  url: 'pages/AnimationPage',
                  animation: {
                    type: RouteAnimationType.CUSTOM,
                    enterAnimation: {
                      translate: { x: 0, y: '100%' },
                      opacity: 0,
                      scale: { x: 0.8, y: 0.8, z: 1.0 },
                      duration: 500,
                      curve: Curve.ElasticOut
                    },
                    exitAnimation: {
                      translate: { x: 0, y: 0 },
                      opacity: 0.5,
                      duration: 300,
                      curve: Curve.EaseIn
                    }
                  }
                });
              })
          }
          .padding(16)
        }
        .width('100%')
        .padding({ top: 10, bottom: 10 })
        .backgroundColor('#f5f5f5')
      }
      .title('设置')
      .titleMode(NavigationTitleMode.Full)
      .mode(NavigationMode.Stack)
      .hideBackButton(false)
    }
  }
}

// 动画展示页面
@Entry
@Component
struct AnimationPage {
  @State isExpanded: boolean = false;
  
  build() {
    Column() {
      Navigation() {
        Column() {
          Text('点击卡片查看动画效果')
            .fontSize(16)
            .margin({ top: 20, bottom: 20 })
          
          // 动画示例组件
          Stack() {
            Column()
              .width(this.isExpanded ? '100%' : '80%')
              .height(this.isExpanded ? 400 : 200)
              .backgroundColor('#007DFF')
              .borderRadius(16)
              .opacity(this.isExpanded ? 1 : 0.8)
              .shadow({ radius: 8, color: 'rgba(0, 0, 0, 0.1)' })
              .alignItems(HorizontalAlign.Center)
              .justifyContent(FlexAlign.Center)
              .animation({
                duration: 500,
                curve: Curve.ElasticOut,
                iterations: 1,
                playMode: PlayMode.Normal
              })
              .onClick(() => {
                this.isExpanded = !this.isExpanded;
              })
            
            Text(this.isExpanded ? '点击收起' : '点击展开')
              .fontSize(20)
              .fontColor(Color.White)
          }
          .width('100%')
          .height(400)
          .alignContent(Alignment.Center)
          
          Button('返回')
            .margin({ top: 40 })
            .onClick(() => {
              router.back();
            })
        }
        .width('100%')
        .padding(16)
      }
      .title('动画示例')
      .titleMode(NavigationTitleMode.Full)
      .mode(NavigationMode.Stack)
    }
  }
}

// 示例入口类
export default class EntryAbility extends Ability {
  onCreate(want, launchParam) {
    // 应用创建时调用
    console.info('EntryAbility onCreate');
    
    // 注册全局路由拦截器
    router.beforeRouteMove((to, from, next) => {
      console.info(`路由拦截: 从 ${from} 到 ${to}`);
      
      // 可以进行权限检查等操作
      // 如果需要继续导航，调用next()
      // 如果需要终止导航，调用next({cancel: true})
      // 如果需要重定向，调用next({url: 'pages/Login'})
      next();
    });
  }
  
  onDestroy() {
    // 应用销毁时调用
    console.info('EntryAbility onDestroy');
  }
  
  onWindowStageCreate(windowStage) {
    // 设置主窗口
    windowStage.setUIContent('pages/HomePage');
  }
  
  onWant(want) {
    // 处理外部调用，包括深层链接
    console.info('EntryAbility onWant');
    
    if (want.uri) {
      const uri = new URL(want.uri);
      const path = uri.pathname;
      
      // 处理不同的深层链接路径
      if (path === '/detail') {
        const productId = uri.searchParams.get('id');
        
        if (productId) {
          router.pushUrl({
            url: 'pages/Detail',
            params: {
              productId: productId,
              fromDeepLink: true
            }
          });
        }
      } else if (path === '/settings') {
        router.pushUrl({
          url: 'pages/Settings'
        });
      }
    }
  }
}
```

## Vue

### 路由定义方式

Vue使用Vue Router库进行路由管理，主要有以下定义方式：

1. **路由配置对象**：集中定义路由表
   ```javascript
   const routes = [
     { path: '/', component: Home },
     { path: '/about', component: About },
     { path: '/user/:id', component: User }
   ]
   
   const router = createRouter({
     history: createWebHistory(),
     routes
   })
   ```

2. **嵌套路由**：通过children属性定义父子关系的路由
   ```javascript
   const routes = [
     {
       path: '/user',
       component: User,
       children: [
         { path: 'profile', component: UserProfile },
         { path: 'posts', component: UserPosts }
       ]
     }
   ]
   ```

3. **命名视图**：在同一页面同时显示多个视图
   ```javascript
   const routes = [
     {
       path: '/settings',
       components: {
         default: UserSettings,
         sidebar: Sidebar,
         navbar: Navbar
       }
     }
   ]
   ```

4. **路由懒加载**：使用动态导入优化性能
   ```javascript
   const routes = [
     { 
       path: '/about', 
       component: () => import('./views/About.vue') 
     }
   ]
   ```

### 路由传参机制

Vue Router提供多种传参方式：

1. **动态路由参数**：通过路径传递参数
   ```javascript
   // 路由定义
   { path: '/user/:id', component: User }
   
   // 导航
   router.push('/user/123')
   
   // 在组件中获取
   const userId = this.$route.params.id
   // 或在组件选项中
   props: ['id']
   ```

2. **查询参数**：通过URL查询字符串传递
   ```javascript
   // 导航
   router.push('/search?query=vue&sort=desc')
   
   // 在组件中获取
   const query = this.$route.query.query
   const sort = this.$route.query.sort
   ```

3. **路由属性传递**：将路由参数作为组件属性传递
   ```javascript
   // 路由定义
   { 
     path: '/user/:id', 
     component: User,
     props: true  // 将路由参数作为props传递
   }
   
   // 在组件中获取
   props: ['id']
   ```

4. **命名路由与参数**：结合命名路由传递对象参数
   ```javascript
   // 路由定义
   { 
     path: '/user/:id', 
     name: 'user',
     component: User 
   }
   
   // 导航
   router.push({ 
     name: 'user', 
     params: { id: '123' },
     query: { plan: 'premium' }
   })
   ```

5. **路由元信息传递**：通过meta属性传递固定配置
   ```javascript
   // 路由定义
   { 
     path: '/admin', 
     component: Admin,
     meta: { requiresAuth: true }
   }
   
   // 在导航守卫中使用
   router.beforeEach((to, from, next) => {
     if (to.meta.requiresAuth && !isAuthenticated) {
       next('/login')
     } else {
       next()
     }
   })
   ```

### 路由栈管理

Vue Router提供多种栈管理API：

1. **基本导航**：
   - `router.push()`：导航到新位置
   - `router.replace()`：替换当前位置
   - `router.go()`：前进或后退指定步数
   - `router.back()`：后退一步
   - `router.forward()`：前进一步

2. **导航守卫**：拦截和控制导航流程
   ```javascript
   // 全局前置守卫
   router.beforeEach((to, from, next) => {
     // 验证、拦截或重定向
     next()
   })
   
   // 全局解析守卫
   router.beforeResolve((to, from, next) => {
     // 在所有组件内守卫和异步路由组件被解析后调用
     next()
   })
   
   // 全局后置钩子
   router.afterEach((to, from) => {
     // 不会影响导航，常用于分析、更改页面标题等
   })
   
   // 路由独享守卫
   {
     path: '/admin',
     component: Admin,
     beforeEnter: (to, from, next) => {
       // 仅对该路由有效的守卫
       next()
     }
   }
   
   // 组件内守卫
   {
     beforeRouteEnter(to, from, next) {
       // 组件实例尚未创建，不能访问this
       next(vm => {
         // 通过vm访问组件实例
       })
     },
     beforeRouteUpdate(to, from, next) {
       // 当前路由改变，但组件被复用时调用
       // 可以访问this
       next()
     },
     beforeRouteLeave(to, from, next) {
       // 导航离开该组件的对应路由时调用
       // 可以访问this
       next()
     }
   }
   ```

3. **导航状态管理**：
   ```javascript
   // 获取当前路由
   const currentRoute = router.currentRoute.value
   
   // 监听路由变化
   watch(() => router.currentRoute.value, (newRoute) => {
     // 处理路由变化
   })
   ```

### 路由转场动画

Vue Router结合Vue的过渡系统提供多种动画方式：

1. **基本转场效果**：
   ```vue
   <template>
     <router-view v-slot="{ Component }">
       <transition name="fade">
         <component :is="Component" />
       </transition>
     </router-view>
   </template>
   
   <style>
   .fade-enter-active,
   .fade-leave-active {
     transition: opacity 0.5s ease;
   }
   
   .fade-enter-from,
   .fade-leave-to {
     opacity: 0;
   }
   </style>
   ```

2. **根据路由动态过渡**：
   ```vue
   <template>
     <router-view v-slot="{ Component, route }">
       <transition :name="route.meta.transition || 'fade'">
         <component :is="Component" />
       </transition>
     </router-view>
   </template>
   
   <script>
   // 在路由定义
   const routes = [
     {
       path: '/users',
       component: UserList,
       meta: { transition: 'slide-left' }
     },
     {
       path: '/users/:id',
       component: UserDetail,
       meta: { transition: 'slide-right' }
     }
   ]
   </script>
   
   <style>
   .slide-left-enter-active,
   .slide-left-leave-active,
   .slide-right-enter-active,
   .slide-right-leave-active {
     transition: transform 0.5s ease;
   }
   
   .slide-left-enter-from,
   .slide-right-leave-to {
     transform: translateX(100%);
   }
   
   .slide-left-leave-to,
   .slide-right-enter-from {
     transform: translateX(-100%);
   }
   </style>
   ```

3. **第三方动画库集成**：集成Animate.css等库
   ```vue
   <template>
     <router-view v-slot="{ Component }">
       <transition
         enter-active-class="animate__animated animate__fadeIn"
         leave-active-class="animate__animated animate__fadeOut"
       >
         <component :is="Component" />
       </transition>
     </router-view>
   </template>
   ```

### 深层链接支持

Vue Router在Web环境中通过以下方式支持深层链接：

1. **History模式**：支持真实URL路径的SPA
   ```javascript
   const router = createRouter({
     history: createWebHistory(),
     routes: [...]
   })
   ```

2. **Hash模式**：通过URL哈希部分实现导航
   ```javascript
   const router = createRouter({
     history: createWebHashHistory(),
     routes: [...]
   })
   ```

3. **服务端配置**：需要配置服务器将所有请求重定向到index.html

4. **移动应用集成**：通过Capacitor或Cordova集成深层链接
   ```javascript
   // Capacitor示例
   import { App } from '@capacitor/app';
   
   App.addListener('appUrlOpen', (data) => {
     // 示例: myapp://page/settings
     const slug = data.url.split("myapp://").pop();
     
     if (slug) {
       router.push('/' + slug);
     }
   });
   ```

### 平台特性与兼容性

- **Web平台**：主要针对浏览器环境开发
- **轻量级**：核心库非常小（约24KB min+gzip）
- **跨平台支持**：通过PWA、Capacitor或Cordova可用于移动应用
- **SEO支持**：结合SSR（服务端渲染）可提供良好的SEO支持
- **IDE支持**：提供TypeScript类型定义和VSCode插件
- **浏览器兼容性**：支持所有现代浏览器及IE11（需要polyfill）
- **生态系统**：丰富的周边库和插件支持

### 完整实例代码

```javascript
// 文件: src/router/index.js
import { createRouter, createWebHistory } from 'vue-router'
import HomeView from '../views/HomeView.vue'
import NotFound from '../views/NotFound.vue'

// 路由配置
const routes = [
  {
    path: '/',
    name: 'home',
    component: HomeView,
    meta: { title: '首页', transition: 'fade' }
  },
  {
    path: '/about',
    name: 'about',
    // 路由懒加载
    component: () => import('../views/AboutView.vue'),
    meta: { title: '关于', transition: 'slide-left' }
  },
  {
    path: '/products',
    name: 'products',
    component: () => import('../views/ProductList.vue'),
    meta: { title: '产品列表', transition: 'fade' }
  },
  {
    path: '/products/:id',
    name: 'product-detail',
    component: () => import('../views/ProductDetail.vue'),
    // 将路由参数作为props传给组件
    props: true,
    meta: { title: '产品详情', transition: 'slide-left' }
  },
  {
    path: '/user',
    component: () => import('../views/UserLayout.vue'),
    // 需要认证的嵌套路由
    meta: { requiresAuth: true },
    children: [
      {
        path: '',
        name: 'user-profile',
        component: () => import('../views/UserProfile.vue'),
        meta: { title: '用户资料' }
      },
      {
        path: 'settings',
        name: 'user-settings',
        component: () => import('../views/UserSettings.vue'),
        meta: { title: '用户设置' }
      }
    ]
  },
  {
    path: '/login',
    name: 'login',
    component: () => import('../views/LoginView.vue'),
    meta: { title: '登录', transition: 'fade' }
  },
  {
    // 处理多视图路由
    path: '/dashboard',
    components: {
      default: () => import('../views/Dashboard.vue'),
      sidebar: () => import('../components/Sidebar.vue'),
      header: () => import('../components/Header.vue')
    },
    meta: { requiresAuth: true, title: '控制台' }
  },
  { 
    path: '/:pathMatch(.*)*', 
    name: 'not-found', 
    component: NotFound,
    meta: { title: '页面未找到' }
  }
]

const router = createRouter({
  history: createWebHistory(),
  routes,
  // 自定义滚动行为
  scrollBehavior(to, from, savedPosition) {
    if (savedPosition) {
      return savedPosition
    } else {
      return { top: 0 }
    }
  }
})

// 全局导航守卫 - 权限控制
router.beforeEach((to, from, next) => {
  // 更新页面标题
  document.title = `${to.meta.title || '应用'} - Vue路由示例`
  
  // 模拟的认证状态
  const isAuthenticated = localStorage.getItem('user')
  
  // 检查路由是否需要认证
  if (to.matched.some(record => record.meta.requiresAuth)) {
    if (!isAuthenticated) {
      next({
        name: 'login',
        query: { redirect: to.fullPath }
      })
    } else {
      next()
    }
  } else {
    next()
  }
})

// 全局后置钩子 - 分析追踪
router.afterEach((to, from) => {
  // 记录路由变化
  console.log(`从 ${from.path} 导航到 ${to.path}`)
  
  // 这里可以添加分析代码
  // analytics.logEvent('page_view', { page_path: to.path })
})

export default router

// 文件: src/App.vue
<template>
  <div class="app">
    <header>
      <nav>
        <router-link to="/">首页</router-link> |
        <router-link to="/about">关于</router-link> |
        <router-link to="/products">产品</router-link> |
        <router-link to="/user">用户</router-link> |
        <router-link to="/dashboard">控制台</router-link>
      </nav>
    </header>
    
    <main>
      <!-- 使用命名视图 -->
      <router-view name="header" />
      <div class="content-container">
        <router-view name="sidebar" />
        <!-- 主视图包含过渡效果 -->
        <router-view v-slot="{ Component, route }">
          <transition :name="route.meta.transition || 'fade'" mode="out-in">
            <component :is="Component" />
          </transition>
        </router-view>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: 'App'
}
</script>

<style>
.app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 0;
  padding: 0;
}

nav {
  padding: 30px;
  text-align: center;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
  text-decoration: none;
  margin: 0 10px;
}

nav a.router-link-exact-active {
  color: #42b983;
}

.content-container {
  display: flex;
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

/* 转场动画 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.slide-left-enter-active,
.slide-left-leave-active,
.slide-right-enter-active,
.slide-right-leave-active {
  transition: transform 0.35s ease;
}

.slide-left-enter-from,
.slide-right-leave-to {
  transform: translateX(30px);
  opacity: 0;
}

.slide-left-leave-to,
.slide-right-enter-from {
  transform: translateX(-30px);
  opacity: 0;
}
</style>

// 文件: src/views/HomeView.vue
<template>
  <div class="home">
    <h1>Vue Router示例</h1>
    <p>这是一个演示Vue Router功能的示例应用</p>
    
    <div class="action-buttons">
      <button @click="goToProducts">浏览产品</button>
      <button @click="goToProductWithParams">查看特定产品</button>
      <button @click="simulateDeepLink">模拟深层链接</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HomeView',
  methods: {
    goToProducts() {
      this.$router.push('/products')
    },
    goToProductWithParams() {
      // 使用命名路由和参数导航
      this.$router.push({
        name: 'product-detail',
        params: { id: '123' },
        query: { source: 'home' }
      })
    },
    simulateDeepLink() {
      // 模拟一个深层链接处理
      const url = '/products/999?source=deeplink'
      this.$router.push(url)
    }
  }
}
</script>

<style scoped>
.home {
  text-align: center;
  padding: 20px;
}

.action-buttons {
  margin-top: 30px;
}

button {
  background-color: #42b983;
  color: white;
  border: none;
  padding: 10px 20px;
  margin: 0 10px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 16px;
}

button:hover {
  background-color: #3aa876;
}
</style>

// 文件: src/views/ProductList.vue
<template>
  <div class="product-list">
    <h1>产品列表</h1>
    
    <div class="products">
      <div 
        v-for="product in products" 
        :key="product.id"
        class="product-card"
        @click="viewProduct(product)"
      >
        <div class="product-image" :style="{ backgroundColor: product.color }"></div>
        <div class="product-info">
          <h3>{{ product.name }}</h3>
          <p>{{ product.description }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductList',
  data() {
    return {
      products: [
        { id: '1', name: '产品一', description: '产品一描述', color: '#42b983' },
        { id: '2', name: '产品二', description: '产品二描述', color: '#35495e' },
        { id: '3', name: '产品三', description: '产品三描述', color: '#ff7e67' }
      ]
    }
  },
  methods: {
    viewProduct(product) {
      this.$router.push({
        name: 'product-detail',
        params: { id: product.id }
      })
    }
  }
}
</script>

<style scoped>
.product-list {
  padding: 20px;
}

.products {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
  margin-top: 20px;
}

.product-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  overflow: hidden;
  transition: transform 0.3s, box-shadow 0.3s;
  cursor: pointer;
}

.product-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 20px rgba(0,0,0,0.1);
}

.product-image {
  height: 150px;
}

.product-info {
  padding: 15px;
}
</style>

// 文件: src/views/ProductDetail.vue
<template>
  <div class="product-detail">
    <div v-if="loading">加载中...</div>
    <div v-else-if="error">加载失败: {{ error }}</div>
    <div v-else class="product-content">
      <div class="product-header" :style="{ backgroundColor: product.color || '#42b983' }">
        <h1>{{ product.name }}</h1>
      </div>
      
      <div class="product-body">
        <p class="product-id">产品ID: {{ id }}</p>
        <p>{{ product.description }}</p>
        
        <div v-if="source" class="source-info">
          来源: {{ source }}
        </div>
        
        <div class="actions">
          <button @click="goBack">返回</button>
          <button @click="viewRelatedProduct">查看相关产品</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductDetail',
  props: {
    id: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      loading: true,
      error: null,
      product: {}
    }
  },
  computed: {
    source() {
      return this.$route.query.source
    }
  },
  methods: {
    fetchProduct() {
      this.loading = true
      
      // 模拟API调用
      setTimeout(() => {
        // 通常这里会有一个实际的API调用
        if (this.id === '999') {
          this.product = {
            id: this.id,
            name: '深层链接产品',
            description: '这个产品通过深层链接访问',
            color: '#ff7e67'
          }
        } else {
          this.product = {
            id: this.id,
            name: `产品 ${this.id}`,
            description: `这是产品 ${this.id} 的详细描述。`,
            color: '#42b983'
          }
        }
        
        this.loading = false
      }, 500)
    },
    goBack() {
      this.$router.back()
    },
    viewRelatedProduct() {
      const relatedId = (parseInt(this.id) + 1).toString()
      this.$router.push({
        name: 'product-detail',
        params: { id: relatedId },
        query: { source: 'related' }
      })
    }
  },
  created() {
    this.fetchProduct()
  },
  // 当路由参数变化但组件被复用时，需要重新获取数据
  beforeRouteUpdate(to, from, next) {
    // 路由参数变化时重新加载数据
    this.id = to.params.id
    this.fetchProduct()
    next()
  },
  // 离开前的确认
  beforeRouteLeave(to, from, next) {
    if (this.source === 'deeplink' && to.name !== 'home') {
      if (confirm('您确定要离开当前页面吗？')) {
        next()
      } else {
        next(false)
      }
    } else {
      next()
    }
  }
}
</script>

<style scoped>
.product-detail {
  max-width: 800px;
  margin: 0 auto;
}

.product-header {
  padding: 30px;
  color: white;
  text-align: center;
  border-radius: 8px 8px 0 0;
}

.product-body {
  padding: 20px;
  background-color: #f8f8f8;
  border-radius: 0 0 8px 8px;
}

.product-id {
  color: #666;
  font-size: 14px;
}

.source-info {
  margin-top: 20px;
  padding: 10px;
  background-color: #e9f7ef;
  border-radius: 4px;
}

.actions {
  margin-top: 30px;
  display: flex;
  gap: 10px;
}

button {
  background-color: #42b983;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
}
</style>

// 文件: src/views/LoginView.vue
<template>
  <div class="login">
    <h1>登录</h1>
    
    <form @submit.prevent="login">
      <div class="form-group">
        <label for="username">用户名</label>
        <input type="text" id="username" v-model="username" required>
      </div>
      
      <div class="form-group">
        <label for="password">密码</label>
        <input type="password" id="password" v-model="password" required>
      </div>
      
      <button type="submit" :disabled="loading">
        {{ loading ? '登录中...' : '登录' }}
      </button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'LoginView',
  data() {
    return {
      username: '',
      password: '',
      loading: false
    }
  },
  methods: {
    login() {
      this.loading = true
      
      // 模拟登录过程
      setTimeout(() => {
        // 存储用户信息
        localStorage.setItem('user', JSON.stringify({
          username: this.username
        }))
        
        // 登录后导航
        const redirectPath = this.$route.query.redirect || '/'
        this.$router.replace(redirectPath)
        
        this.loading = false
      }, 1000)
    }
  }
}
</script>

<style scoped>
.login {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
}

input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

button {
  background-color: #42b983;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  width: 100%;
}

button:disabled {
  background-color: #a0cfbb;
  cursor: not-allowed;
}
</style>

// 文件: src/main.js
import { createApp } from 'vue'
import App from './App.vue'
import router from './router'
import store from './store'

const app = createApp(App)

app.use(router)
app.use(store)

// 全局错误处理
app.config.errorHandler = (err, vm, info) => {
  console.error('Vue错误:', err)
  console.info('组件:', vm)
  console.info('信息:', info)
}

// 初始化应用
app.mount('#app')

// 处理深层链接（示例）
// 这在实际Web应用中可能是不同的实现方式
window.addEventListener('load', () => {
  const url = new URL(window.location.href)
  const path = url.pathname
  const query = {}
  
  // 解析查询参数
  for (const [key, value] of url.searchParams.entries()) {
    query[key] = value
  }
  
  if (path !== '/' && !path.startsWith('/login')) {
    console.log(`检测到深层链接: ${path}`)
    
    // 这里可以添加特殊的深层链接处理逻辑
    // 例如登录状态检查等
  }
})
```

## 框架对比总结

### 路由定义方式

| 框架 | 路由定义特点 | 灵活性 | 复杂度 |
|------|------------|--------|--------|
| Flutter | 命名式与命令式并存，支持第三方库增强 | 高 | 中 |
| Android (Jetpack Compose) | 声明式导航图，NavHost/NavController模式 | 高 | 中 |
| UIKit (iOS) | 命令式为主，基于ViewController，支持Storyboard可视化 | 中 | 高 |
| SwiftUI (iOS) | 声明式，状态驱动，NavigationStack/NavigationLink模式 | 高 | 低 |
| 鸿蒙 (ArkUI) | 类似Flutter，命名与命令式混合，支持Ability间路由 | 高 | 中 |
| Vue | 中央化路由表配置，支持嵌套路由和命名视图 | 高 | 低 |

### 路由传参机制

| 框架 | 传参方式 | 类型安全 | 数据持久化 |
|------|--------|---------|----------|
| Flutter | 构造函数、arguments、路径参数 | 中（Dart类型系统） | 支持（通过状态管理） |
| Android (Jetpack Compose) | Safe Args、路径参数、查询参数、对象传递 | 高（强类型） | 支持（ViewModel、SavedState） |
| UIKit (iOS) | 属性传递、构造函数、prepareForSegue | 高（强类型） | 支持（通过多种状态方案） |
| SwiftUI (iOS) | 构造函数、环境对象、NavigationPath | 高（强类型） | 支持（@SceneStorage、@AppStorage） |
| 鸿蒙 (ArkUI) | params对象、AppStorage、URL参数 | 中高 | 支持（PersistentStorage） |
| Vue | 路径参数、查询参数、props、meta | 低（JavaScript） | 支持（Vuex、localStorage） |

### 路由栈管理

| 框架 | 栈操作 | 拦截机制 | 状态保存 |
|------|-------|---------|---------|
| Flutter | 完整栈操作API，支持嵌套导航器 | Navigator Observers | 支持 |
| Android (Jetpack Compose) | 强大栈管理，支持单一活动多返回栈 | Navigation Actions | 自动支持 |
| UIKit (iOS) | 完整栈管理，支持嵌套导航控制器 | 自定义转场代理 | 支持 |
| SwiftUI (iOS) | NavigationPath管理，可编码状态 | onAppear/onDisappear | 可以保存（iOS 16+） |
| 鸿蒙 (ArkUI) | push/pop/clear API，支持全局拦截 | 路由拦截器 | 支持 |
| Vue | 命令式API，完备导航守卫系统 | 全局前置/后置守卫，组件内守卫 | 支持（可存储在Vuex） |

### 路由转场动画

| 框架 | 内置动画 | 自定义能力 | 交互动画 |
|------|---------|----------|---------|
| Flutter | 丰富（Material/Cupertino） | 高度自定义 | 支持 |
| Android (Jetpack Compose) | 中等，扩展性强 | 全面自定义支持 | 支持 |
| UIKit (iOS) | 基础预设，可扩展 | 完全自定义支持 | 支持交互式转场 |
| SwiftUI (iOS) | 基础预设，可扩展 | 支持自定义（但不如UIKit灵活） | 部分支持 |
| 鸿蒙 (ArkUI) | 丰富预设，类型多样 | 支持完全自定义 | 支持 |
| Vue | 基础CSS过渡 | 结合CSS/JS的高度自定义 | 通过第三方库支持 |

### 深层链接支持

| 框架 | 支持级别 | 实现复杂度 | 平台限制 |
|------|---------|----------|---------|
| Flutter | 强（支持原生集成） | 中（需平台配置） | 遵循各平台规则 |
| Android (Jetpack Compose) | 强（原生支持） | 低（框架内置） | Android平台 |
| UIKit (iOS) | 强（原生支持） | 中（需配置） | iOS平台 |
| SwiftUI (iOS) | 强（原生支持） | 低（框架内置） | iOS平台 |
| 鸿蒙 (ArkUI) | 强（原生支持） | 低（框架内置） | 鸿蒙平台 |
| Vue | 中（需要额外配置） | 高（需服务端和应用配合） | Web平台，移动需桥接 |

### 平台与生态特性

| 框架 | 跨平台能力 | 社区支持 | 学习曲线 | 成熟度 |
|------|----------|---------|---------|-------|
| Flutter | 高（移动、Web、桌面） | 活跃 | 中高 | 高 |
| Android (Jetpack Compose) | 低（仅Android） | 强（Google支持） | 中 | 中高（相对较新） |
| UIKit (iOS) | 低（仅iOS） | 强（Apple支持） | 高 | 极高（成熟稳定） |
| SwiftUI (iOS) | 中（Apple生态） | 强（Apple支持） | 中 | 中（较新，快速发展） |
| 鸿蒙 (ArkUI) | 中低（鸿蒙生态） | 中（华为支持，国内活跃） | 中 | 中（新兴平台） |
| Vue | 高（Web为主，可扩展） | 极活跃 | 低 | 高（Web领域成熟） |

### 总体评价

1. **Flutter**：最全面的跨平台路由解决方案，适合构建统一体验的多平台应用，但学习曲线较陡。

2. **Android (Jetpack Compose)**：现代化的声明式导航，与Android生态深度集成，类型安全，但仅限于Android平台。

3. **UIKit (iOS)**：成熟可靠的命令式导航系统，控制精细，但代码量较大，耦合度较高。

4. **SwiftUI (iOS)**：简洁的声明式导航，状态驱动，但仍处于发展中，新API需要较高iOS版本。

5. **鸿蒙 (ArkUI)**：结合了Flutter和原生思想，支持分布式能力，但生态相对较新，主要面向国内市场。

6. **Vue**：极其灵活的Web路由系统，拥有全面的导航守卫机制，但在移动原生场景需要额外桥接。

### 选择建议

- **全平台统一体验**：选择Flutter
- **Android性能与体验**：选择Jetpack Compose
- **iOS稳定性与完整控制**：选择UIKit
- **iOS快速开发与现代化**：选择SwiftUI
- **面向鸿蒙生态**：选择ArkUI
- **Web应用为主**：选择Vue
- **混合开发**：根据主要平台选择，通过桥接支持次要平台