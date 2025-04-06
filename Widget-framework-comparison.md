# 主流框架 Widget API 对比分析（2024）

## 目录
- [基础概述](#基础概述)
- [核心组件对比](#核心组件对比)
- [路由管理](#路由管理)
- [参数传递](#参数传递)
- [平台特性](#平台特性)
- [兼容性](#兼容性)
- [完整示例](#完整示例)

## 基础概述

### Flutter (v3.19)
- 框架特点：跨平台、单一代码库
- 渲染引擎：自研 Skia
- 编程语言：Dart
- Widget 特性：一切皆 Widget，声明式 UI

### Android Jetpack Compose (v1.6)
- 框架特点：原生 Android 现代 UI 工具包
- 渲染引擎：Android 渲染引擎
- 编程语言：Kotlin
- 组件特性：完全声明式，Composable 函数

### UIKit (iOS)
- 框架特点：iOS 传统界面框架
- 渲染引擎：Core Animation
- 编程语言：Objective-C/Swift
- 组件特性：命令式 UI，视图控制器模式

### SwiftUI (iOS 17+)
- 框架特点：现代 iOS UI 框架
- 渲染引擎：Core Animation
- 编程语言：Swift
- 组件特性：声明式 UI，数据驱动

### 鸿蒙 ArkUI (v4.0)
- 框架特点：声明式 UI 范式
- 渲染引擎：ArkUI 引擎
- 编程语言：ArkTS
- 组件特性：声明式开发范式，类 Swift UI

### Vue (v3.4)
- 框架特点：渐进式 JavaScript 框架
- 渲染引擎：Virtual DOM
- 编程语言：JavaScript/TypeScript
- 组件特性：组件化，响应式数据流

## 核心组件对比

### 布局组件

| 功能 | Flutter | Compose | UIKit | SwiftUI | ArkUI | Vue |
|------|---------|----------|--------|----------|-------|-----|
| 线性布局 | Row/Column | Row/Column | UIStackView | HStack/VStack | Row/Column | flex/grid |
| 层叠布局 | Stack | Box | UIView | ZStack | Stack | position |
| 列表 | ListView | LazyColumn | UITableView | List | List | v-for |
| 网格 | GridView | LazyVerticalGrid | UICollectionView | LazyVGrid | Grid | grid |

### 基础组件

| 功能 | Flutter | Compose | UIKit | SwiftUI | ArkUI | Vue |
|------|---------|----------|--------|----------|-------|-----|
| 文本 | Text | Text | UILabel | Text | Text | \<span> |
| 按钮 | ElevatedButton | Button | UIButton | Button | Button | \<button> |
| 图片 | Image | Image | UIImageView | Image | Image | \<img> |
| 输入框 | TextField | TextField | UITextField | TextField | TextInput | \<input> |

## 路由管理

### Flutter
```dart
Navigator.push(
  context,
  MaterialPageRoute(builder: (context) => SecondRoute()),
);
```

### Jetpack Compose
```kotlin
NavHost(navController, startDestination = "home") {
    composable("home") { HomeScreen() }
    composable("detail") { DetailScreen() }
}
```

### UIKit
```swift
navigationController?.pushViewController(viewController, animated: true)
```

### SwiftUI
```swift
NavigationLink(destination: DetailView()) {
    Text("Navigate")
}
```

### ArkUI
```typescript
Navigator() {
  Push({ uri: 'pages/Detail' })
}
```

### Vue
```javascript
const router = createRouter({
  routes: [
    { path: '/', component: Home },
    { path: '/about', component: About },
  ]
})
```

## 参数传递

### 状态管理对比

| 框架 | 本地状态 | 全局状态 | 响应式 |
|------|----------|----------|--------|
| Flutter | setState | Provider/Bloc | Stream |
| Compose | MutableState | ViewModel | Flow |
| UIKit | Property | NotificationCenter | KVO |
| SwiftUI | @State | @StateObject | Combine |
| ArkUI | @State | AppStorage | 状态管理机制 |
| Vue | ref/reactive | Vuex/Pinia | Composition API |

## 平台特性

### 原生功能访问

| 框架 | 原生 API 访问 | 平台特定代码 | 性能优化 |
|------|--------------|-------------|----------|
| Flutter | Platform Channels | if Platform.isIOS | AOT 编译 |
| Compose | Android API | Android Only | JIT/AOT |
| UIKit | 直接访问 | iOS Only | 原生性能 |
| SwiftUI | 直接访问 | iOS Only | 原生性能 |
| ArkUI | 系统能力 | HarmonyOS | AOT 编译 |
| Vue | Web API | 浏览器 API | Tree-shaking |

## 兼容性

### 最低支持版本

- Flutter: Android 4.1+, iOS 11+
- Compose: Android 5.0+
- UIKit: iOS 12+
- SwiftUI: iOS 13+
- ArkUI: HarmonyOS 3.0+
- Vue: 现代浏览器

### 跨平台支持

| 框架 | Android | iOS | Web | Desktop | 其他 |
|------|----------|-----|-----|----------|------|
| Flutter | ✓ | ✓ | ✓ | ✓ | Linux |
| Compose | ✓ | - | - | ✓ | - |
| UIKit | - | ✓ | - | - | - |
| SwiftUI | - | ✓ | - | ✓ | - |
| ArkUI | - | - | - | - | HarmonyOS |
| Vue | ✓ | ✓ | ✓ | ✓ | PWA |

## 完整示例

### Flutter 计数器示例
```dart
class CounterWidget extends StatefulWidget {
  @override
  _CounterWidgetState createState() => _CounterWidgetState();
}

class _CounterWidgetState extends State<CounterWidget> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('$_counter'),
        ElevatedButton(
          onPressed: _incrementCounter,
          child: Text('Increment'),
        ),
      ],
    );
  }
}
```

### Jetpack Compose 计数器示例
```kotlin
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }
    
    Column {
        Text(text = "$count")
        Button(onClick = { count++ }) {
            Text("Increment")
        }
    }
}
```

### SwiftUI 计数器示例
```swift
struct CounterView: View {
    @State private var count = 0
    
    var body: some View {
        VStack {
            Text("\(count)")
            Button("Increment") {
                count += 1
            }
        }
    }
}
```

### ArkUI 计数器示例
```typescript
@Entry
@Component
struct Counter {
  @State count: number = 0

  build() {
    Column() {
      Text(`${this.count}`)
      Button('Increment')
        .onClick(() => {
          this.count++
        })
    }
  }
}
```

### Vue 计数器示例
```vue
<template>
  <div>
    <p>{{ count }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)
const increment = () => count.value++
</script>
``` 

总结：
该文档包含以下主要内容：
- 基础概述：包含各个框架的版本、特点和基本信息
核心组件对比：详细对比了布局组件和基础组件
- 路由管理：展示了各框架的路由实现方式
- 参数传递：比较了状态管理方案
- 平台特性：分析了原生功能访问能力
- 兼容性：包含最低支持版本和跨平台支持情况
- 完整示例：提供了每个框架的计数器示例代码

文档采用了表格和代码示例相结合的方式，便于直观对比各个框架的异同。