# 六大框架导航组件 API 对比

本文对比了 Flutter、Android（Jetpack Compose）、UIKit、SwiftUI（iOS）、鸿蒙（ArkUI）、Vue 六大框架的导航组件 API，涵盖核心功能、路由管理、参数传递和平台特性，基于最新稳定版本（2024年）。

## 1. Flutter (Navigator 2.0)
### 核心导航 API
```dart
// 基础导航
Navigator.push(context, MaterialPageRoute(
  builder: (context) => SecondPage()
));

// 声明式导航 (Navigator 2.0)
Router(
  routerDelegate: MyRouterDelegate(),
  routeInformationParser: MyRouteInformationParser(),
)
```

### 路由管理
- 支持命名路由和路径路由
- 提供 `RouterDelegate` 进行完整的路由状态管理
- 可通过 `RouteInformationParser` 处理深层链接

### 参数传递
```dart
// 传递参数
Navigator.pushNamed(
  context,
  '/details',
  arguments: {'id': 123}
);

// 接收参数
final args = ModalRoute.of(context)!.settings.arguments as Map;
```

## 2. Android Jetpack Compose
### 核心导航 API
```kotlin
NavHost(
    navController = navController,
    startDestination = "home"
) {
    composable("home") { HomeScreen() }
    composable("profile/{userId}") { backStackEntry ->
        ProfileScreen(backStackEntry.arguments?.getString("userId"))
    }
}
```

### 路由管理
- 使用 `NavController` 控制导航
- 支持类型安全的参数传递
- 内置深层链接支持

### 参数传递
```kotlin
// 传递参数
navController.navigate("profile/123")

// 接收参数
@Composable
fun ProfileScreen(userId: String?) {
    // 使用 userId
}
```

## 3. UIKit (iOS)
### 核心导航 API
```swift
// UINavigationController
navigationController?.pushViewController(detailVC, animated: true)

// 故事板导航
performSegue(withIdentifier: "showDetail", sender: self)
```

### 路由管理
- 基于视图控制器的栈管理
- 支持故事板 segue
- 可自定义转场动画

### 参数传递
```swift
// 传递参数
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    if segue.identifier == "showDetail" {
        let detailVC = segue.destination as! DetailViewController
        detailVC.itemId = selectedId
    }
}
```

## 4. SwiftUI
### 核心导航 API
```swift
NavigationStack {
    List {
        NavigationLink("Go to Detail") {
            DetailView()
        }
    }
}
```

### 路由管理
- 声明式导航
- 支持 `NavigationStack` 和 `NavigationSplitView`
- 深度链接集成

### 参数传递
```swift
NavigationLink(value: item) {
    Text(item.title)
}
.navigationDestination(for: Item.self) { item in
    DetailView(item: item)
}
```

## 5. 鸿蒙 ArkUI
### 核心导航 API
```typescript
// 页面路由
router.pushUrl({
  url: 'pages/Detail',
  params: { id: '123' }
})

// 声明式导航
Navigator() {
  Column() {
    // 内容
  }
}
```

### 路由管理
- 支持显式和声明式导航
- 页面栈管理
- 自定义转场动画

### 参数传递
```typescript
// 传递参数
router.pushUrl({
  url: 'pages/Detail',
  params: {
    id: '123',
    title: '详情'
  }
})

// 接收参数
@Entry
@Component
struct DetailPage {
  @State params: any = router.getParams()
}
```

## 6. Vue Router
### 核心导航 API
```javascript
// 编程式导航
router.push('/user/123')

// 声明式导航
<router-link to="/user/123">用户详情</router-link>
```

### 路由管理
- 支持嵌套路由
- 动态路由匹配
- 导航守卫系统

### 参数传递
```javascript
// 传递参数
router.push({
  name: 'user',
  params: { userId: '123' },
  query: { plan: 'private' }
})

// 接收参数
const route = useRoute()
const userId = route.params.userId
const plan = route.query.plan
```

## 主要区别和特点

### 1. 声明式 vs 命令式
- SwiftUI 和 Vue 更倾向于声明式导航
- UIKit 完全采用命令式导航
- Flutter、Jetpack Compose 和 ArkUI 同时支持两种方式

### 2. 类型安全
- Jetpack Compose 和 SwiftUI 提供最强的类型安全
- Flutter 2.0 后通过 Navigator 2.0 提供了更好的类型安全
- Vue 的类型安全依赖于 TypeScript 的使用

### 3. 深层链接支持
- Android 和 iOS 原生框架（UIKit/SwiftUI）提供最完整的深层链接支持
- Flutter 和 ArkUI 需要额外配置
- Vue 需要借助第三方库实现完整的深层链接功能

### 4. 状态管理集成
- Vue Router 与 Vuex/Pinia 完美配合
- SwiftUI 与 Combine 框架紧密集成
- Flutter Navigator 2.0 可与各种状态管理解决方案配合

### 5. 性能考虑
- 原生框架（UIKit/SwiftUI/Jetpack Compose）性能最优
- Flutter 次之
- Web 框架（Vue）在复杂导航场景下可能需要额外优化

## 选择建议

这些框架各有特色，选择时需要考虑：
- 项目规模和复杂度
- 团队技术栈
- 性能要求
- 跨平台需求
- 维护成本 