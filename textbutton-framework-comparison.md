# 六大框架TextButton组件比较

本文档比较了Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue在TextButton组件实现上的异同点。

## 目录
- [基本样式配置](#基本样式配置)
- [状态管理](#状态管理)
- [事件处理](#事件处理)
- [无障碍支持](#无障碍支持)
- [自定义样式扩展](#自定义样式扩展)
- [平台特性与兼容性](#平台特性与兼容性)
- [完整实例代码](#完整实例代码)

## 基本样式配置

### Flutter
Flutter的TextButton提供了简洁的API来配置文本样式、背景色、边框等：
- `style`: 使用ButtonStyle定义按钮外观
- `child`: 通常是Text widget
- 支持文本颜色、字体、字号等配置

### Android (Jetpack Compose)
Compose的TextButton提供了Material Design风格的文本按钮：
- `colors`: 配置不同状态下的颜色
- `contentPadding`: 内容内边距
- `typography`: 文本排版样式

### UIKit
UIKit中的UIButton可以配置为文本按钮：
- `setTitle`: 设置按钮文本
- `titleLabel`: 配置文本标签属性
- `setTitleColor`: 设置文本颜色

### SwiftUI
SwiftUI的Button提供了声明式API：
- 使用Text视图作为内容
- `buttonStyle`: 设置预定义样式
- `foregroundColor`: 设置文本颜色

### 鸿蒙 (ArkUI)
ArkUI的Button组件：
- `type`: 可设置为Capsule/Circle/Normal等类型
- `fontColor`: 文本颜色
- `fontSize`: 文本大小

### Vue
Vue通常结合CSS来实现文本按钮：
- 使用`<button>`标签或自定义组件
- 可通过CSS和class绑定设置样式
- 支持scoped CSS和CSS module

## 状态管理

### Flutter
Flutter的TextButton支持多种状态：
- 通过ButtonStyle的stateLayerColor设置状态颜色
- 支持hovered, focused, pressed, dragged状态
- 可以使用MaterialStateProperty处理不同状态

### Android (Jetpack Compose)
Compose使用状态对象管理UI状态：
- 通过interactionSource跟踪交互状态
- 使用remember和mutableStateOf管理状态
- 支持enabled, pressed, focused, hovered等状态

### UIKit
UIKit使用控制状态(Control State)：
- `.normal`, `.highlighted`, `.disabled`, `.selected`
- 可为每种状态设置不同文本颜色和样式
- 通过`isEnabled`属性控制启用/禁用状态

### SwiftUI
SwiftUI基于状态驱动UI更新：
- 使用@State和@Binding管理按钮状态
- 通过.disabled()修饰符设置禁用状态
- 可以组合使用.buttonStyle和.foregroundStyle

### 鸿蒙 (ArkUI)
ArkUI利用状态变量：
- 使用`stateStyles`定义不同状态下的样式
- 支持pressed, disabled, focused, normal状态
- 通过@State装饰器响应状态变化

### Vue
Vue使用响应式数据系统：
- 通过v-bind:class绑定不同状态的类
- 可使用计算属性生成状态相关的样式
- 通过:disabled绑定禁用状态

## 事件处理

### Flutter
Flutter的事件处理：
- `onPressed`: 按钮点击事件
- `onLongPress`: 长按事件
- null值可禁用按钮并改变视觉样式

### Android (Jetpack Compose)
Compose的事件处理：
- `onClick`: 点击事件回调
- `modifier.pointerInput`: 自定义手势
- `interactionSource`: 跟踪交互状态

### UIKit
UIKit的Target-Action模式：
- `addTarget(_:action:for:)`: 添加事件处理
- `.touchUpInside`: 最常用的触摸事件类型
- 支持多个事件处理程序

### SwiftUI
SwiftUI的闭包式事件处理：
- Button初始化时传入action闭包
- 支持.onLongPressGesture修饰符
- 可结合手势识别器增强交互

### 鸿蒙 (ArkUI)
ArkUI提供事件方法：
- `onClick`: 点击事件处理
- `onTouch`: 触摸事件
- 支持事件冒泡和捕获机制

### Vue
Vue的事件系统：
- `@click`: 点击事件处理
- `@click.prevent`: 事件修饰符
- 支持自定义事件emit

## 无障碍支持

### Flutter
Flutter的无障碍功能：
- Semantics widget提供无障碍信息
- 支持屏幕阅读器兼容
- 通过tooltips提供附加信息

### Android (Jetpack Compose)
Compose的无障碍：
- semantics修饰符提供无障碍属性
- contentDescription设置描述文本
- 支持TalkBack和无障碍服务

### UIKit
UIKit的无障碍：
- `accessibilityLabel`: 设置无障碍标签
- `accessibilityHint`: 提供操作提示
- 与VoiceOver深度整合

### SwiftUI
SwiftUI的无障碍：
- .accessibilityLabel()修饰符
- .accessibilityHint()提供额外上下文
- 动态字体大小自适应

### 鸿蒙 (ArkUI)
ArkUI的无障碍：
- `accessibilityGroup`: 无障碍分组
- `accessibilityText`: 无障碍文本
- 支持屏幕阅读

### Vue
Vue的无障碍：
- 需手动添加ARIA属性
- 可结合辅助库改善无障碍支持
- 支持键盘导航

## 自定义样式扩展

### Flutter
Flutter的样式扩展：
- 通过继承ButtonStyle创建自定义样式
- 可使用Theme.of(context)继承主题样式
- 支持自定义MaterialStateProperty

### Android (Jetpack Compose)
Compose的样式扩展：
- 可创建自定义TextButton实现
- 通过CompositionLocal提供主题样式
- 支持使用装饰模式扩展功能

### UIKit
UIKit的样式扩展：
- 子类化UIButton自定义绘制
- 使用UIAppearance设置全局样式
- 可通过扩展添加自定义行为

### SwiftUI
SwiftUI的样式扩展：
- 创建自定义ButtonStyle
- 通过ViewModifier扩展视图功能
- 支持环境变量传递样式

### 鸿蒙 (ArkUI)
ArkUI的样式扩展：
- 支持自定义组件样式
- 提供stateStyles扩展不同状态样式
- 通过@Extend装饰器扩展已有组件

### Vue
Vue的样式扩展：
- 组件插槽允许内容自定义
- 支持CSS/SCSS/Less等预处理器
- 可使用styled-components等库扩展样式

## 平台特性与兼容性

### Flutter
- 跨平台一致性高
- 适应各平台的视觉设计
- 支持响应式尺寸适配

### Android (Jetpack Compose)
- 原生Material Design支持
- 与Android系统无缝集成
- 最低支持Android 5.0 (API 21)

### UIKit
- 与iOS系统深度整合
- 自动继承系统外观设置
- 支持iOS 9.0及以上版本

### SwiftUI
- 现代声明式UI框架
- 需要iOS 13及以上版本
- 与UIKit互通性好

### 鸿蒙 (ArkUI)
- 统一多设备开发
- 面向IoT全场景设计
- 分布式能力与多设备协同

### Vue
- 浏览器兼容性好
- 响应式设计支持各种设备
- 可通过打包工具适配不同环境

## 完整实例代码

### Flutter
```dart
import 'package:flutter/material.dart';

class MyTextButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return TextButton(
      style: TextButton.styleFrom(
        foregroundColor: Colors.blue,
        padding: EdgeInsets.symmetric(horizontal: 16, vertical: 8),
        textStyle: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(8),
        ),
      ),
      onPressed: () {
        print("按钮被点击");
      },
      onLongPress: () {
        print("按钮被长按");
      },
      child: Text("Flutter文本按钮"),
    );
  }
}
```

### Android (Jetpack Compose)
```kotlin
import androidx.compose.material3.TextButton
import androidx.compose.material3.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.foundation.layout.padding
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp

@Composable
fun MyTextButton() {
    TextButton(
        onClick = { println("按钮被点击") },
        enabled = true,
        modifier = Modifier.padding(8.dp)
    ) {
        Text(text = "Compose文本按钮")
    }
}

@Preview
@Composable
fun PreviewTextButton() {
    MyTextButton()
}
```

### UIKit
```swift
import UIKit

class TextButtonExample: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let button = UIButton(type: .system)
        button.setTitle("UIKit文本按钮", for: .normal)
        button.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .medium)
        button.setTitleColor(.systemBlue, for: .normal)
        button.setTitleColor(.lightGray, for: .disabled)
        button.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside)
        
        // 无障碍支持
        button.accessibilityLabel = "文本按钮"
        button.accessibilityHint = "点击执行操作"
        
        button.frame = CGRect(x: 100, y: 100, width: 200, height: 50)
        view.addSubview(button)
    }
    
    @objc func buttonTapped() {
        print("按钮被点击")
    }
}
```

### SwiftUI
```swift
import SwiftUI

struct TextButtonExample: View {
    var body: some View {
        Button(action: {
            print("按钮被点击")
        }) {
            Text("SwiftUI文本按钮")
                .font(.system(size: 16, weight: .medium))
                .padding(.horizontal, 16)
                .padding(.vertical, 8)
        }
        .buttonStyle(.plain)
        .foregroundColor(.blue)
        .accessibilityLabel("文本按钮")
        .accessibilityHint("点击执行操作")
    }
}

struct TextButtonExample_Previews: PreviewProvider {
    static var previews: some View {
        TextButtonExample()
    }
}
```

### 鸿蒙 (ArkUI)
```typescript
@Entry
@Component
struct TextButtonExample {
  build() {
    Flex({ direction: FlexDirection.Column, alignItems: ItemAlign.Center, justifyContent: FlexAlign.Center }) {
      Button('鸿蒙文本按钮', { type: ButtonType.Normal })
        .fontColor('#0D9FFB')
        .fontSize(16)
        .margin(10)
        .padding({ left: 16, right: 16, top: 8, bottom: 8 })
        .onClick(() => {
          console.info('按钮被点击')
        })
        .stateStyles({
          pressed: {
            .opacity(0.7)
          },
          disabled: {
            .opacity(0.4)
            .fontColor('#CCCCCC')
          }
        })
        .accessibilityText('文本按钮')
    }
    .width('100%')
    .height('100%')
  }
}
```

### Vue
```vue
<template>
  <button 
    class="text-button"
    :class="{ 'disabled': isDisabled }"
    @click="handleClick"
    :disabled="isDisabled"
    aria-label="文本按钮"
    :aria-disabled="isDisabled"
  >
    Vue文本按钮
  </button>
</template>

<script>
export default {
  name: 'TextButton',
  props: {
    isDisabled: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    handleClick() {
      if (!this.isDisabled) {
        this.$emit('click')
        console.log('按钮被点击')
      }
    }
  }
}
</script>

<style scoped>
.text-button {
  background: transparent;
  color: #0D9FFB;
  border: none;
  padding: 8px 16px;
  font-size: 16px;
  font-weight: 500;
  cursor: pointer;
  border-radius: 4px;
  transition: all 0.3s;
}

.text-button:hover {
  background-color: rgba(13, 159, 251, 0.1);
}

.text-button:active {
  background-color: rgba(13, 159, 251, 0.2);
}

.text-button.disabled {
  color: #CCCCCC;
  cursor: not-allowed;
  opacity: 0.5;
}
</style>
``` 