# 六大框架NavigatorObserver组件比较

本文档比较了Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue在路由观察者实现上的异同点。

## 目录
- [观察者注册方式](#观察者注册方式)
- [可观察事件类型](#可观察事件类型)
- [路由生命周期监听](#路由生命周期监听)
- [自定义行为注入](#自定义行为注入)
- [与分析工具集成](#与分析工具集成)
- [平台特性与兼容性](#平台特性与兼容性)
- [完整实例代码](#完整实例代码)

## 观察者注册方式

### Flutter
Flutter通过MaterialApp或Navigator注册NavigatorObserver：
- 在MaterialApp的`navigatorObservers`参数中添加
- 可以注册多个观察者实例
- 自定义观察者需继承NavigatorObserver类
- 全局应用级别的监听

### Android (Jetpack Compose)
Compose使用NavController和NavHostController：
- 通过`navController.addOnDestinationChangedListener`添加
- 使用`NavigationHost.Builder.addNavigatorProvider`添加自定义导航器
- 依赖注入框架(如Hilt/Dagger)集成路由管理
- 组合式API设计

### UIKit
UIKit基于UINavigationControllerDelegate：
- 设置`UINavigationController.delegate`
- 使用`UIAdaptivePresentationControllerDelegate`监听模态展示
- 通过AppDelegate和SceneDelegate监听应用级导航
- 遵循委托模式

### SwiftUI
SwiftUI使用环境值和视图修饰符：
- `.onAppear`和`.onDisappear`生命周期修饰符
- 使用`EnvironmentObject`和`@Environment`传递导航状态
- 路由状态存储在`NavigationPath`或自定义ObservableObject中
- 基于状态的声明式导航

### 鸿蒙 (ArkUI)
ArkUI利用自定义路由控制：
- 通过Router模块的`onRouteChange`注册回调
- 使用`RouterDelegate`进行页面控制
- 应用级路由与组件级路由的分层管理
- 事件总线模式

### Vue
Vue Router提供导航守卫机制：
- 全局守卫：`router.beforeEach`、`router.afterEach`
- 路由守卫：`beforeEnter`
- 组件内守卫：`beforeRouteEnter`、`beforeRouteUpdate`
- 钩子函数串联处理

## 可观察事件类型

### Flutter
Flutter的NavigatorObserver事件：
- `didPush` - 路由入栈
- `didPop` - 路由出栈
- `didRemove` - 路由被移除
- `didReplace` - 路由被替换
- `didStartUserGesture` - 用户手势导航开始
- `didStopUserGesture` - 用户手势导航结束

### Android (Jetpack Compose)
Compose的导航事件：
- `onDestinationChanged` - 目标改变
- 导航动作完成回调
- 回退栈更改事件
- 深层链接处理事件
- 保存状态恢复事件

### UIKit
UIKit的导航控制器事件：
- `navigationController(_:willShow:animated:)`
- `navigationController(_:didShow:animated:)`
- `navigationControllerSupportedInterfaceOrientations(_:)`
- `navigationControllerPreferredInterfaceOrientationForPresentation(_:)`
- 转场协调器（Transition Coordinator）事件

### SwiftUI
SwiftUI的导航事件：
- 视图出现与消失
- 导航路径变化
- 导航栈深度变化
- 导航链接激活与停用
- 导航后退操作

### 鸿蒙 (ArkUI)
ArkUI的路由事件：
- 页面创建事件
- 页面销毁事件
- 页面显示与隐藏
- 路由参数变化
- 应用前后台切换

### Vue
Vue Router的导航事件：
- 导航开始前
- 路由解析阶段
- 导航确认阶段
- 视图更新前后
- 导航完成或失败

## 路由生命周期监听

### Flutter
Flutter的路由生命周期：
- 通过Route的`onEnter`、`onPop`、`onComplete`和`onDispose`
- 页面级Widget的`initState`和`dispose`
- 可通过`RouteAware`和`RouteObserver`细粒度监听
- 支持监听页面可见性变化

### Android (Jetpack Compose)
Compose的生命周期监听：
- 通过`LifecycleOwner`和`Lifecycle.Event`
- `DisposableEffect`用于清理资源
- `LaunchedEffect`响应导航键
- 使用`rememberUpdatedState`处理生命周期依赖值

### UIKit
UIKit的视图控制器生命周期：
- `viewDidLoad`, `viewWillAppear`, `viewDidAppear`
- `viewWillDisappear`, `viewDidDisappear`
- `viewWillLayoutSubviews`, `viewDidLayoutSubviews`
- 内存管理事件：`didReceiveMemoryWarning`

### SwiftUI
SwiftUI的视图生命周期：
- `.onAppear()`, `.onDisappear()`
- `@State`和`@Environment`状态变化
- `.task()`, `.onReceive()` for background operations
- 通过`@Environment(\.scenePhase)`监听应用状态

### 鸿蒙 (ArkUI)
ArkUI的组件生命周期：
- `aboutToAppear`, `onPageShow`
- `aboutToDisappear`, `onPageHide`
- `onBackPress`处理返回事件
- 装饰器`@StorageProp`, `@StorageLink`监听存储变化

### Vue
Vue的组件和路由生命周期：
- 组件：`created`, `mounted`, `updated`, `unmounted`
- 路由特有：`beforeRouteEnter`, `beforeRouteUpdate`, `beforeRouteLeave`
- 组合式API：`onBeforeRouteUpdate`, `onBeforeRouteLeave`
- 守卫钩子中访问路由实例和组件实例

## 自定义行为注入

### Flutter
Flutter的行为注入：
- 继承`NavigatorObserver`并重写方法
- 使用`RouteSettings`传递自定义参数
- 通过`Zone`拦截和修改导航行为
- `HeroController`实现跨页面动画

### Android (Jetpack Compose)
Compose的行为注入：
- 自定义NavController的导航行为
- 实现NavigatorContent提供自定义过渡
- 拦截器模式实现权限验证
- 使用CompositionLocal共享导航状态

### UIKit
UIKit的行为注入：
- 自定义UINavigationController子类
- 实现UIViewControllerAnimatedTransitioning自定义转场
- 通过UINavigationBar.delegate控制导航栏行为
- 拦截手势识别器修改交互行为

### SwiftUI
SwiftUI的行为注入：
- 自定义NavigationViewStyle
- 使用ViewModifier封装导航行为
- 环境值注入导航依赖
- 使用PreferenceKey向上传递导航事件

### 鸿蒙 (ArkUI)
ArkUI的行为注入：
- 路由拦截器实现前置处理
- 使用UIAbility生命周期控制
- 自定义Transition动画效果
- 分布式页面路由行为定制

### Vue
Vue的行为注入：
- 全局导航守卫中注入逻辑
- 元信息（meta）字段定制路由行为
- 路由实例方法扩展
- 插件系统集成自定义功能

## 与分析工具集成

### Flutter
Flutter的分析工具集成：
- 可与Firebase Analytics集成追踪页面浏览
- 支持自定义事件埋点
- 结合DevTools分析导航性能
- 用户行为跟踪与崩溃报告

### Android (Jetpack Compose)
Compose的分析集成：
- 与Firebase Performance Monitoring集成
- Android Jetpack Benchmark库测试导航性能
- Android Studio Profiler监控导航开销
- 可与GoogleAnalytics/FirebaseAnalytics等集成

### UIKit
UIKit的分析集成：
- 与App Store Connect Analytics集成
- 利用XCTest UI测试导航流程
- Instruments工具分析导航性能
- Apple的MetricKit框架收集性能数据

### SwiftUI
SwiftUI的分析集成：
- SwiftUI Inspector实时调试导航状态
- Time Profiler分析导航性能
- 与苹果隐私友好的分析系统集成
- 结合Combine框架进行事件追踪

### 鸿蒙 (ArkUI)
ArkUI的分析集成：
- 华为分析服务（Analytics Kit）集成
- DevEco Studio性能分析工具
- 分布式数据采集分析
- 应用市场质量中心反馈

### Vue
Vue的分析集成：
- Vue Devtools扩展监控路由变化
- Google Analytics, Matomo等集成
- Vuex与Router协同记录用户行为
- 性能指标（如导航时间）捕获与上报

## 平台特性与兼容性

### Flutter
- 跨平台一致性导航体验
- 适应不同平台导航手势
- 支持Web和移动端统一路由策略
- 深度链接与应用间通信支持

### Android (Jetpack Compose)
- Navigation Component与传统Fragment、Activity兼容
- 支持Safe Args类型安全的参数传递
- 深度链接与应用操作集成
- 与Material Design 3导航模式对齐

### UIKit
- 完全符合iOS人机界面指南
- 支持多种屏幕尺寸适配
- 深度集成系统功能（如回退手势）
- 兼容各iOS版本的导航行为差异

### SwiftUI
- 声明式导航与传统UIKit桥接
- 支持多平台部署（iOS、macOS、watchOS、tvOS）
- 动态适应系统导航行为变化
- 无障碍导航支持

### 鸿蒙 (ArkUI)
- 分布式场景下的无缝导航
- 多设备形态统一导航管理
- 超级链接跨设备路由能力
- 自动适配折叠屏等特殊设备

### Vue
- 浏览器历史API无缝集成
- 支持懒加载和代码分割
- 可配置哈希模式或历史模式
- 适应不同浏览器与渲染模式 

## 完整实例代码

### Flutter
```dart
import 'package:flutter/material.dart';

// 自定义路由观察者
class MyNavigatorObserver extends NavigatorObserver {
  // 路由被推入
  @override
  void didPush(Route<dynamic> route, Route<dynamic>? previousRoute) {
    print('路由推入: ${route.settings.name} 从 ${previousRoute?.settings.name}');
    
    // 可用于分析统计
    _sendAnalyticsEvent('page_view', {'screen_name': route.settings.name});
  }

  // 路由被弹出
  @override
  void didPop(Route<dynamic> route, Route<dynamic>? previousRoute) {
    print('路由弹出: ${route.settings.name} 回到 ${previousRoute?.settings.name}');
    
    if (previousRoute != null) {
      _sendAnalyticsEvent('page_view', {'screen_name': previousRoute.settings.name});
    }
  }

  // 路由被替换
  @override
  void didReplace({Route<dynamic>? newRoute, Route<dynamic>? oldRoute}) {
    print('路由替换: ${oldRoute?.settings.name} 被 ${newRoute?.settings.name} 替换');
    
    if (newRoute != null) {
      _sendAnalyticsEvent('page_view', {'screen_name': newRoute.settings.name});
    }
  }

  // 路由被移除
  @override
  void didRemove(Route<dynamic> route, Route<dynamic>? previousRoute) {
    print('路由移除: ${route.settings.name}');
  }

  // 用户开始手势导航
  @override
  void didStartUserGesture(Route<dynamic> route, Route<dynamic>? previousRoute) {
    print('开始手势导航: ${route.settings.name}');
  }

  // 用户结束手势导航
  @override
  void didStopUserGesture() {
    print('结束手势导航');
  }
  
  // 模拟发送分析事件
  void _sendAnalyticsEvent(String name, Map<String, dynamic> parameters) {
    // 实际项目中可能调用Firebase Analytics等
    print('分析事件: $name, 参数: $parameters');
  }
}

// 应用入口
void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'NavigatorObserver示例',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      // 注册观察者
      navigatorObservers: [
        MyNavigatorObserver(),
      ],
      initialRoute: '/',
      routes: {
        '/': (context) => HomePage(),
        '/detail': (context) => DetailPage(),
      },
    );
  }
}

class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('首页')),
      body: Center(
        child: ElevatedButton(
          child: Text('导航到详情页'),
          onPressed: () {
            Navigator.pushNamed(context, '/detail');
          },
        ),
      ),
    );
  }
}

class DetailPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('详情页')),
      body: Center(
        child: ElevatedButton(
          child: Text('返回首页'),
          onPressed: () {
            Navigator.pop(context);
          },
        ),
      ),
    );
  }
}
```

### Android (Jetpack Compose)
```kotlin
import androidx.compose.foundation.layout.Box
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding
import androidx.compose.material3.Button
import androidx.compose.material3.ExperimentalMaterial3Api
import androidx.compose.material3.Scaffold
import androidx.compose.material3.Text
import androidx.compose.material3.TopAppBar
import androidx.compose.runtime.Composable
import androidx.compose.runtime.remember
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.navigation.NavController
import androidx.navigation.NavDestination
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.compose.rememberNavController
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.navigation.NavDestination.Companion.hierarchy

// 导航目的地
sealed class Screen(val route: String, val title: String) {
    object Home : Screen("home", "首页")
    object Detail : Screen("detail", "详情页")
}

// 自定义导航观察者
class NavigationObserver {
    val destinations = mutableListOf<String>()
    
    // 目的地变化监听器
    val listener = NavController.OnDestinationChangedListener { controller, destination, arguments ->
        val route = destination.route ?: "unknown"
        println("导航到: $route")
        destinations.add(route)
        
        // 分析统计
        sendAnalyticsEvent("screen_view", mapOf("screen_name" to route))
    }
    
    // 模拟发送分析事件
    private fun sendAnalyticsEvent(name: String, params: Map<String, String>) {
        // 实际项目中集成Firebase Analytics等
        println("分析事件: $name, 参数: $params")
    }
}

class NavigationObserverActivity : ComponentActivity() {
    @OptIn(ExperimentalMaterial3Api::class)
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            val navController = rememberNavController()
            val navigationObserver = remember { NavigationObserver() }
            
            // 注册导航观察者
            remember(navController) {
                navController.addOnDestinationChangedListener(navigationObserver.listener)
                navController
            }
            
            NavHost(
                navController = navController,
                startDestination = Screen.Home.route
            ) {
                composable(Screen.Home.route) {
                    Scaffold(
                        topBar = { TopAppBar(title = { Text(Screen.Home.title) }) }
                    ) { paddingValues ->
                        Box(
                            modifier = Modifier
                                .fillMaxSize()
                                .padding(paddingValues),
                            contentAlignment = Alignment.Center
                        ) {
                            Button(onClick = { navController.navigate(Screen.Detail.route) }) {
                                Text("导航到详情页")
                            }
                        }
                    }
                }
                
                composable(Screen.Detail.route) {
                    Scaffold(
                        topBar = { TopAppBar(title = { Text(Screen.Detail.title) }) }
                    ) { paddingValues ->
                        Box(
                            modifier = Modifier
                                .fillMaxSize()
                                .padding(paddingValues),
                            contentAlignment = Alignment.Center
                        ) {
                            Button(onClick = { navController.popBackStack() }) {
                                Text("返回首页")
                            }
                        }
                    }
                }
            }
        }
    }
    
    override fun onDestroy() {
        super.onDestroy()
        // 清理资源
    }
}
```

### UIKit
```swift
import UIKit

// 导航观察者
class NavigationObserver: NSObject, UINavigationControllerDelegate {
    var previousViewController: UIViewController?
    
    // 导航控制器将要显示视图控制器
    func navigationController(_ navigationController: UINavigationController, 
                             willShow viewController: UIViewController, 
                             animated: Bool) {
        let screenName = String(describing: type(of: viewController))
        print("将要导航到: \(screenName)")
    }
    
    // 导航控制器已显示视图控制器
    func navigationController(_ navigationController: UINavigationController, 
                             didShow viewController: UIViewController, 
                             animated: Bool) {
        let fromScreen = previousViewController != nil ? 
            String(describing: type(of: previousViewController!)) : "未知"
        let toScreen = String(describing: type(of: viewController))
        
        print("已完成导航: 从 \(fromScreen) 到 \(toScreen)")
        
        // 记录当前视图控制器用于下次比较
        previousViewController = viewController
        
        // 发送分析事件
        sendAnalyticsEvent("screen_view", parameters: ["screen_name": toScreen])
    }
    
    // 模拟发送分析事件
    private func sendAnalyticsEvent(_ name: String, parameters: [String: String]) {
        // 实际项目中集成App Analytics等
        print("分析事件: \(name), 参数: \(parameters)")
    }
}

// 应用委托
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?
    let navigationObserver = NavigationObserver()
    
    func application(_ application: UIApplication, 
                    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
        let window = UIWindow(frame: UIScreen.main.bounds)
        let navController = UINavigationController(rootViewController: HomeViewController())
        
        // 设置导航观察者
        navController.delegate = navigationObserver
        
        window.rootViewController = navController
        window.makeKeyAndVisible()
        self.window = window
        
        return true
    }
}

// 首页
class HomeViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        title = "首页"
        view.backgroundColor = .white
        
        let button = UIButton(type: .system)
        button.setTitle("导航到详情页", for: .normal)
        button.addTarget(self, action: #selector(navigateToDetail), for: .touchUpInside)
        button.frame = CGRect(x: 0, y: 0, width: 200, height: 50)
        button.center = view.center
        
        view.addSubview(button)
    }
    
    @objc func navigateToDetail() {
        let detailVC = DetailViewController()
        navigationController?.pushViewController(detailVC, animated: true)
    }
}

// 详情页
class DetailViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        title = "详情页"
        view.backgroundColor = .white
        
        let button = UIButton(type: .system)
        button.setTitle("返回首页", for: .normal)
        button.addTarget(self, action: #selector(navigateBack), for: .touchUpInside)
        button.frame = CGRect(x: 0, y: 0, width: 200, height: 50)
        button.center = view.center
        
        view.addSubview(button)
    }
    
    @objc func navigateBack() {
        navigationController?.popViewController(animated: true)
    }
}
```

### SwiftUI
```swift
import SwiftUI

// 导航事件跟踪器
class NavigationEventTracker: ObservableObject {
    // 记录访问过的页面
    @Published var visitedScreens: [String] = []
    
    // 记录页面访问
    func trackScreen(_ screenName: String) {
        print("访问页面: \(screenName)")
        visitedScreens.append(screenName)
        
        // 发送分析事件
        sendAnalyticsEvent("screen_view", parameters: ["screen_name": screenName])
    }
    
    // 模拟发送分析事件
    private func sendAnalyticsEvent(_ name: String, parameters: [String: String]) {
        // 实际项目中集成App Analytics等
        print("分析事件: \(name), 参数: \(parameters)")
    }
}

// 应用入口
@main
struct NavigationObserverApp: App {
    // 创建导航事件跟踪器并作为环境对象
    @StateObject private var navigationTracker = NavigationEventTracker()
    
    var body: some Scene {
        WindowGroup {
            NavigationView {
                HomeView()
            }
            .environmentObject(navigationTracker)
        }
    }
}

// 首页
struct HomeView: View {
    @EnvironmentObject var navigationTracker: NavigationEventTracker
    @State private var isNavigatingToDetail = false
    
    var body: some View {
        VStack {
            Text("首页")
                .font(.largeTitle)
            
            Button("导航到详情页") {
                isNavigatingToDetail = true
            }
            .padding()
            
            NavigationLink(
                destination: DetailView(),
                isActive: $isNavigatingToDetail,
                label: { EmptyView() }
            )
        }
        .onAppear {
            navigationTracker.trackScreen("HomeView")
        }
        .navigationTitle("首页")
    }
}

// 详情页
struct DetailView: View {
    @EnvironmentObject var navigationTracker: NavigationEventTracker
    @Environment(\.presentationMode) var presentationMode
    
    var body: some View {
        VStack {
            Text("详情页")
                .font(.largeTitle)
            
            Button("返回首页") {
                presentationMode.wrappedValue.dismiss()
            }
            .padding()
        }
        .onAppear {
            navigationTracker.trackScreen("DetailView")
        }
        .navigationTitle("详情页")
    }
}
```

### 鸿蒙 (ArkUI)
```typescript
import router from '@ohos.router';
import { BusinessError } from '@ohos.base';

// 导航事件观察者
class RouterObserver {
  private static instance: RouterObserver = new RouterObserver();
  private visitedPages: string[] = [];

  private constructor() {
    // 注册路由事件回调
    router.onRouteChange((event) => {
      const fromPath = event.from || 'unknown';
      const toPath = event.to;
      console.log(`路由变化: 从 ${fromPath} 到 ${toPath}`);
      
      this.visitedPages.push(toPath);
      this.trackPageView(toPath);
    });
  }

  static getInstance(): RouterObserver {
    return RouterObserver.instance;
  }

  // 获取访问历史
  getVisitedPages(): string[] {
    return [...this.visitedPages];
  }

  // 记录页面访问
  private trackPageView(pageName: string): void {
    // 实际项目中可集成华为分析服务
    console.log(`页面访问记录: ${pageName}`);
  }
}

// 首页
@Entry
@Component
struct HomePage {
  @State message: string = '首页';
  private routerObserver = RouterObserver.getInstance();

  aboutToAppear() {
    console.log('首页即将显示');
  }

  build() {
    Column() {
      Text(this.message)
        .fontSize(50)
        .fontWeight(FontWeight.Bold)
      
      Button('导航到详情页')
        .onClick(() => {
          router.pushUrl({
            url: 'pages/DetailPage',
            params: {
              id: '123'
            }
          }).catch((err: BusinessError) => {
            console.error(`导航失败: ${err.message}`);
          });
        })
        .margin(20)
    }
    .width('100%')
    .height('100%')
    .justifyContent(FlexAlign.Center)
  }
}

// 详情页
@Entry
@Component
struct DetailPage {
  @State message: string = '详情页';
  private routerObserver = RouterObserver.getInstance();

  aboutToAppear() {
    console.log('详情页即将显示');
    const params = router.getParams();
    console.log(`收到参数: ${JSON.stringify(params)}`);
  }

  build() {
    Column() {
      Text(this.message)
        .fontSize(50)
        .fontWeight(FontWeight.Bold)
      
      Button('返回首页')
        .onClick(() => {
          router.back();
        })
        .margin(20)
    }
    .width('100%')
    .height('100%')
    .justifyContent(FlexAlign.Center)
  }
}
```

### Vue
```vue
<!-- 导航观察者示例 -->

<!-- main.js - 应用入口 -->
<script>
import { createApp } from 'vue'
import { createRouter, createWebHistory } from 'vue-router'
import App from './App.vue'
import Home from './components/Home.vue'
import Detail from './components/Detail.vue'

// 定义路由
const routes = [
  { path: '/', name: 'Home', component: Home },
  { path: '/detail/:id', name: 'Detail', component: Detail }
]

// 创建路由实例
const router = createRouter({
  history: createWebHistory(),
  routes
})

// 导航分析工具
class NavigationAnalytics {
  constructor() {
    this.visitedRoutes = []
  }
  
  // 记录路由访问
  trackRoute(to, from) {
    const fromPath = from.fullPath
    const toPath = to.fullPath
    const timestamp = new Date().toISOString()
    
    this.visitedRoutes.push({
      from: fromPath,
      to: toPath,
      timestamp
    })
    
    console.log(`导航: 从 ${fromPath} 到 ${toPath}`)
    
    // 可集成谷歌分析等服务
    this.sendAnalyticsEvent('page_view', {
      page_title: to.name,
      page_path: toPath
    })
  }
  
  // 获取访问历史
  getHistory() {
    return [...this.visitedRoutes]
  }
  
  // 发送分析事件
  sendAnalyticsEvent(eventName, params) {
    console.log(`分析事件: ${eventName}`, params)
    // 实际项目中可能调用 gtag 或其他分析服务
  }
}

// 创建分析工具实例
const navigationAnalytics = new NavigationAnalytics()

// 全局前置守卫
router.beforeEach((to, from, next) => {
  console.log('导航开始', to.path)
  // 可进行权限检查等操作
  next()
})

// 全局解析守卫
router.beforeResolve((to, from, next) => {
  console.log('导航解析', to.path)
  next()
})

// 全局后置钩子
router.afterEach((to, from) => {
  console.log('导航完成', to.path)
  navigationAnalytics.trackRoute(to, from)
})

// 创建Vue应用
const app = createApp(App)

// 为Vue实例提供路由和分析工具
app.use(router)
app.provide('navigationAnalytics', navigationAnalytics)

app.mount('#app')
</script>

<!-- App.vue - 应用根组件 -->
<template>
  <div class="app">
    <h1>导航观察者示例</h1>
    <nav>
      <router-link to="/">首页</router-link> |
      <router-link to="/detail/1">详情页</router-link>
    </nav>
    <router-view></router-view>
  </div>
</template>

<!-- Home.vue - 首页组件 -->
<template>
  <div class="home">
    <h2>首页</h2>
    <button @click="goToDetail">导航到详情页</button>
  </div>
</template>

<script>
export default {
  name: 'Home',
  inject: ['navigationAnalytics'],
  methods: {
    goToDetail() {
      this.$router.push('/detail/1')
    }
  },
  beforeRouteEnter(to, from, next) {
    next(vm => {
      console.log('Home组件-进入前')
    })
  },
  beforeRouteLeave(to, from, next) {
    console.log('Home组件-离开前')
    next()
  }
}
</script>

<!-- Detail.vue - 详情页组件 -->
<template>
  <div class="detail">
    <h2>详情页</h2>
    <p>ID: {{ $route.params.id }}</p>
    <button @click="goBack">返回首页</button>
    <div>
      <h3>导航历史</h3>
      <ul>
        <li v-for="(record, index) in navigationHistory" :key="index">
          {{ record.timestamp }}: {{ record.from }} → {{ record.to }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Detail',
  inject: ['navigationAnalytics'],
  computed: {
    navigationHistory() {
      return this.navigationAnalytics.getHistory()
    }
  },
  methods: {
    goBack() {
      this.$router.push('/')
    }
  },
  beforeRouteEnter(to, from, next) {
    next(vm => {
      console.log('Detail组件-进入前')
    })
  },
  beforeRouteLeave(to, from, next) {
    console.log('Detail组件-离开前')
    next()
  }
}
</script>
``` 