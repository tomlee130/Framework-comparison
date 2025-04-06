# 主流框架按钮组件 API 对比分析（2024）

## 目录
- [概述](#概述)
- [Flutter Button](#flutter-button)
- [Android Jetpack Compose Button](#android-jetpack-compose-button)
- [iOS UIKit Button](#ios-uikit-button)
- [iOS SwiftUI Button](#ios-swiftui-button)
- [鸿蒙 ArkUI Button](#鸿蒙-arkui-button)
- [Vue Button](#vue-button)
- [跨平台对比总结](#跨平台对比总结)

## 概述

本文档对比了六大主流框架的按钮组件实现，基于 2024 年最新稳定版本：
- Flutter 3.19
- Jetpack Compose 1.6.0
- UIKit (iOS 17)
- SwiftUI (iOS 17)
- ArkUI (HarmonyOS 4.0)
- Vue 3.4

## Flutter Button

### 核心功能
```dart
ElevatedButton(
  onPressed: () {}, // 点击回调
  onLongPress: () {}, // 长按回调
  style: ElevatedButton.styleFrom(
    foregroundColor: Colors.white, // 文字颜色
    backgroundColor: Colors.blue, // 背景色
    disabledForegroundColor: Colors.grey, // 禁用时文字颜色
    disabledBackgroundColor: Colors.grey[300], // 禁用时背景色
    shadowColor: Colors.black, // 阴影颜色
    elevation: 4, // 阴影高度
    padding: EdgeInsets.all(16), // 内边距
    shape: RoundedRectangleBorder( // 形状
      borderRadius: BorderRadius.circular(8),
    ),
  ),
  child: Text('Button'), // 子组件
)
```

### 路由管理
```dart
ElevatedButton(
  onPressed: () {
    Navigator.push(
      context,
      MaterialPageRoute(builder: (context) => SecondScreen()),
    );
  },
  child: Text('Navigate'),
)
```

### 参数传递
```dart
ElevatedButton(
  onPressed: () {
    Navigator.push(
      context,
      MaterialPageRoute(
        builder: (context) => SecondScreen(data: 'Hello'),
      ),
    );
  },
  child: Text('Pass Data'),
)
```

### 平台特性
- Material Design 风格
- 支持自适应密度
- 支持触觉反馈
- 支持无障碍访问

### 完整示例
```dart
class MyButton extends StatelessWidget {
  final String text;
  final VoidCallback onPressed;

  const MyButton({
    Key? key,
    required this.text,
    required this.onPressed,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: onPressed,
      style: ElevatedButton.styleFrom(
        foregroundColor: Colors.white,
        backgroundColor: Theme.of(context).primaryColor,
        padding: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(8),
        ),
      ),
      child: Text(text),
    );
  }
}
```

## Android Jetpack Compose Button

### 核心功能
```kotlin
Button(
    onClick = { }, // 点击回调
    modifier = Modifier
        .fillMaxWidth()
        .height(48.dp)
        .padding(horizontal = 16.dp),
    enabled = true, // 是否启用
    colors = ButtonDefaults.buttonColors(
        containerColor = MaterialTheme.colorScheme.primary, // 背景色
        contentColor = MaterialTheme.colorScheme.onPrimary, // 内容颜色
        disabledContainerColor = MaterialTheme.colorScheme.onSurface.copy(alpha = 0.12f), // 禁用时背景色
        disabledContentColor = MaterialTheme.colorScheme.onSurface.copy(alpha = 0.38f), // 禁用时内容颜色
    ),
    shape = RoundedCornerShape(8.dp), // 形状
    contentPadding = PaddingValues(16.dp), // 内容边距
) {
    Text("Button")
}
```

### 路由管理
```kotlin
Button(
    onClick = {
        navController.navigate("second_screen")
    }
) {
    Text("Navigate")
}
```

### 参数传递
```kotlin
Button(
    onClick = {
        navController.navigate("second_screen/Hello")
    }
) {
    Text("Pass Data")
}
```

### 平台特性
- Material You 设计语言
- 支持 Ripple 效果
- 支持状态恢复
- 支持无障碍服务

### 完整示例
```kotlin
@Composable
fun CustomButton(
    text: String,
    onClick: () -> Unit,
    modifier: Modifier = Modifier,
    enabled: Boolean = true
) {
    Button(
        onClick = onClick,
        modifier = modifier,
        enabled = enabled,
        colors = ButtonDefaults.buttonColors(
            containerColor = MaterialTheme.colorScheme.primary,
            contentColor = MaterialTheme.colorScheme.onPrimary
        ),
        shape = RoundedCornerShape(8.dp)
    ) {
        Text(
            text = text,
            style = MaterialTheme.typography.labelLarge
        )
    }
}
```

## iOS UIKit Button

### 核心功能
```swift
let button = UIButton(type: .system)
button.setTitle("Button", for: .normal)
button.setTitleColor(.white, for: .normal)
button.setTitleColor(.gray, for: .disabled)
button.backgroundColor = .systemBlue
button.layer.cornerRadius = 8
button.contentEdgeInsets = UIEdgeInsets(top: 12, left: 16, bottom: 12, right: 16)
button.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside)
```

### 路由管理
```swift
let button = UIButton(type: .system)
button.addTarget(self, action: #selector(navigate), for: .touchUpInside)

@objc func navigate() {
    let viewController = SecondViewController()
    navigationController?.pushViewController(viewController, animated: true)
}
```

### 参数传递
```swift
let button = UIButton(type: .system)
button.addTarget(self, action: #selector(navigateWithData), for: .touchUpInside)

@objc func navigateWithData() {
    let viewController = SecondViewController()
    viewController.data = "Hello"
    navigationController?.pushViewController(viewController, animated: true)
}
```

### 平台特性
- iOS 原生风格
- 支持触觉反馈
- 支持动态字体
- 支持 VoiceOver

### 完整示例
```swift
class CustomButton: UIButton {
    init(title: String) {
        super.init(frame: .zero)
        setup(title: title)
    }
    
    required init?(coder: NSCoder) {
        super.init(coder: coder)
    }
    
    private func setup(title: String) {
        setTitle(title, for: .normal)
        setTitleColor(.white, for: .normal)
        backgroundColor = .systemBlue
        layer.cornerRadius = 8
        contentEdgeInsets = UIEdgeInsets(top: 12, left: 16, bottom: 12, right: 16)
        translatesAutoresizingMaskIntoConstraints = false
    }
}
```

## iOS SwiftUI Button

### 核心功能
```swift
Button(action: {
    // 点击回调
}) {
    Text("Button")
        .foregroundColor(.white)
        .padding()
        .frame(maxWidth: .infinity)
        .background(Color.blue)
        .cornerRadius(8)
}
.disabled(false) // 是否禁用
```

### 路由管理
```swift
NavigationLink(destination: SecondView()) {
    Text("Navigate")
        .foregroundColor(.white)
        .padding()
        .background(Color.blue)
        .cornerRadius(8)
}
```

### 参数传递
```swift
NavigationLink(destination: SecondView(data: "Hello")) {
    Text("Pass Data")
        .foregroundColor(.white)
        .padding()
        .background(Color.blue)
        .cornerRadius(8)
}
```

### 平台特性
- SwiftUI 原生风格
- 支持动态类型
- 支持深色模式
- 支持 VoiceOver

### 完整示例
```swift
struct CustomButton: View {
    let title: String
    let action: () -> Void
    
    var body: some View {
        Button(action: action) {
            Text(title)
                .foregroundColor(.white)
                .padding()
                .frame(maxWidth: .infinity)
                .background(Color.blue)
                .cornerRadius(8)
        }
        .buttonStyle(PlainButtonStyle())
    }
}
```

## 鸿蒙 ArkUI Button

### 核心功能
```typescript
Button('Button')
  .onClick(() => {}) // 点击回调
  .width('100%')
  .height(48)
  .backgroundColor('#007DFF')
  .borderRadius(8)
  .fontColor('#FFFFFF')
  .padding({ left: 16, right: 16 })
  .enabled(true) // 是否启用
```

### 路由管理
```typescript
Button('Navigate')
  .onClick(() => {
    router.pushUrl({ url: 'pages/SecondPage' });
  })
```

### 参数传递
```typescript
Button('Pass Data')
  .onClick(() => {
    router.pushUrl({
      url: 'pages/SecondPage',
      params: { data: 'Hello' }
    });
  })
```

### 平台特性
- HarmonyOS 设计语言
- 支持多设备自适应
- 支持触摸反馈
- 支持无障碍

### 完整示例
```typescript
@Component
struct CustomButton {
  private title: string
  private onClick: () => void
  
  build() {
    Button(this.title)
      .onClick(this.onClick)
      .width('100%')
      .height(48)
      .backgroundColor('#007DFF')
      .borderRadius(8)
      .fontColor('#FFFFFF')
      .padding({ left: 16, right: 16 })
  }
}
```

## Vue Button

### 核心功能
```vue
<template>
  <button
    class="button"
    :class="{ 'is-disabled': disabled }"
    :disabled="disabled"
    @click="onClick"
  >
    <slot></slot>
  </button>
</template>

<style scoped>
.button {
  padding: 8px 16px;
  border-radius: 8px;
  background-color: #007bff;
  color: white;
  border: none;
  cursor: pointer;
}

.button.is-disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>
```

### 路由管理
```vue
<template>
  <button @click="navigate">Navigate</button>
</template>

<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()
const navigate = () => {
  router.push('/second-page')
}
</script>
```

### 参数传递
```vue
<template>
  <button @click="navigateWithParams">Pass Data</button>
</template>

<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()
const navigateWithParams = () => {
  router.push({
    path: '/second-page',
    query: { data: 'Hello' }
  })
}
</script>
```

### 平台特性
- 支持响应式设计
- 支持组件复用
- 支持主题定制
- 支持 SSR

### 完整示例
```vue
<template>
  <button
    class="custom-button"
    :class="[type, size, { 'is-disabled': disabled }]"
    :disabled="disabled"
    @click="$emit('click')"
  >
    <slot></slot>
  </button>
</template>

<script setup>
defineProps({
  type: {
    type: String,
    default: 'primary'
  },
  size: {
    type: String,
    default: 'medium'
  },
  disabled: {
    type: Boolean,
    default: false
  }
})

defineEmits(['click'])
</script>

<style scoped>
.custom-button {
  border-radius: 8px;
  border: none;
  cursor: pointer;
  font-weight: 500;
  transition: all 0.3s;
}

.primary {
  background-color: #007bff;
  color: white;
}

.medium {
  padding: 8px 16px;
  font-size: 14px;
}

.is-disabled {
  opacity: 0.6;
  cursor: not-allowed;
}
</style>
```

## 跨平台对比总结

### 共同特点
1. 所有框架都支持基础的按钮功能：
   - 点击事件处理
   - 禁用状态
   - 自定义样式
   - 内容布局

2. 通用设计考虑：
   - 无障碍支持
   - 响应式设计
   - 主题定制
   - 状态管理

### 差异对比

1. **开发范式**
   - Flutter：声明式 UI，Widget 树
   - Jetpack Compose：声明式 UI，Composable 函数
   - UIKit：命令式 UI
   - SwiftUI：声明式 UI
   - ArkUI：声明式 UI，类 SwiftUI
   - Vue：声明式 UI，组件化

2. **样式处理**
   - Flutter：通过 style 属性统一配置
   - Jetpack Compose：Material You 设计系统
   - UIKit：通过属性和 layer 配置
   - SwiftUI：链式修饰符
   - ArkUI：链式修饰符
   - Vue：CSS/SCSS 样式

3. **路由管理**
   - Flutter：Navigator
   - Jetpack Compose：Navigation Component
   - UIKit：UINavigationController
   - SwiftUI：NavigationLink
   - ArkUI：router
   - Vue：Vue Router

4. **性能特点**
   - Flutter：自绘引擎，跨平台一致性好
   - Jetpack Compose：原生性能，仅支持 Android
   - UIKit/SwiftUI：原生性能，仅支持 iOS
   - ArkUI：原生性能，仅支持鸿蒙
   - Vue：依赖浏览器渲染，性能受平台影响

### 选择建议

1. **移动应用**
   - 跨平台：选择 Flutter
   - Android：选择 Jetpack Compose
   - iOS：选择 SwiftUI（新项目）或 UIKit（现有项目）
   - 鸿蒙：选择 ArkUI

2. **Web 应用**
   - 选择 Vue，特别适合现代 Web 应用开发

3. **混合开发**
   - 可以考虑 Flutter Web + 原生混合开发
   - 或使用 Vue + 原生混合开发 