# 六大框架逻辑宽高管理比较

## 目录
- [设备无关单位](#设备无关单位)
- [比例尺寸支持](#比例尺寸支持)
- [约束传递机制](#约束传递机制)
- [与实际像素关系](#与实际像素关系)
- [自适应布局支持](#自适应布局支持)
- [平台特性与兼容性](#平台特性与兼容性)
- [完整实例代码](#完整实例代码)

## 设备无关单位

| 框架 | 单位 | 说明 |
|------|------|------|
| Flutter | 逻辑像素 | 默认单位是设备无关的逻辑像素，通过 MediaQuery 可获取设备像素比 |
| Jetpack Compose | dp/sp | dp (density-independent pixels) 是密度无关像素，sp (scaled pixels) 用于字体大小 |
| UIKit | 点 (Point) | iOS的点是设备无关单位，CGPoint和CGSize使用点作为单位 |
| SwiftUI | 点 (Point) | 沿用UIKit的点单位系统，但提供了更多动态适配工具 |
| ArkUI (鸿蒙) | vp | 虚拟像素 (virtual pixels)，一种设备无关单位，根据设备密度自动换算 |
| Vue | 取决于实现 | Vue本身不提供单位，通常结合CSS单位如rem、vw、vh实现响应式布局 |

## 比例尺寸支持

| 框架 | 支持方式 | 特点 |
|------|----------|------|
| Flutter | Expanded, Flexible, FractionallySizedBox | 支持flex布局和百分比布局 |
| Jetpack Compose | fillMaxWidth/Height, weight | 支持权重分配和填充父容器的比例尺寸 |
| UIKit | NSLayoutConstraint multiplier | 通过约束的乘数关系设置比例尺寸 |
| SwiftUI | GeometryReader, frame修饰符 | 可获取父视图尺寸并设置比例关系 |
| ArkUI | 百分比单位 | 直接支持百分比布局，如width: '50%' |
| Vue | CSS百分比, flex, grid | 通过CSS的百分比、flex和grid布局实现比例尺寸 |

## 约束传递机制

| 框架 | 约束模型 | 工作方式 |
|------|----------|----------|
| Flutter | 双向约束模型 | 父widget向下传递约束，子widget向上报告尺寸 |
| Jetpack Compose | 单向约束流 | 父composable向子composable传递约束，子composable适应约束 |
| UIKit | Auto Layout | 通过NSLayoutConstraint建立视图间约束关系 |
| SwiftUI | 布局协议 | 视图在Layout协议中协商尺寸，父视图提供建议尺寸 |
| ArkUI | 弹性布局 | 通过容器组件传递约束，支持自适应与固定约束混合 |
| Vue | CSS盒模型 | 遵循Web的盒模型和CSS布局规则 |

## 与实际像素关系

| 框架 | 换算方式 | 描述 |
|------|----------|------|
| Flutter | devicePixelRatio | 逻辑像素 × devicePixelRatio = 物理像素 |
| Jetpack Compose | 密度转换 | dp × 设备密度 = 物理像素 |
| UIKit | scale | 点 × scale = 物理像素 (e.g. @2x, @3x) |
| SwiftUI | scale | 点 × scale = 物理像素，与UIKit一致 |
| ArkUI | 虚拟像素映射 | vp会根据设备分辨率自动映射到物理像素 |
| Vue | CSS像素转换 | 由浏览器处理，CSS像素经viewport缩放后映射到物理像素 |

## 自适应布局支持

| 框架 | 自适应技术 | 关键功能 |
|------|------------|----------|
| Flutter | MediaQuery, LayoutBuilder | 响应屏幕尺寸变化，支持不同屏幕方向适配 |
| Jetpack Compose | BoxWithConstraints, WindowSizeClass | 根据窗口大小调整布局，支持可折叠设备 |
| UIKit | Size Classes, Auto Layout | 横竖屏适配，iPad/iPhone差异化布局 |
| SwiftUI | GeometryReader, @Environment | 响应环境变化，支持动态适配不同设备 |
| ArkUI | 媒体查询, 响应式布局 | 通过媒体查询和栅格系统实现多设备适配 |
| Vue | CSS媒体查询, Vue响应式系统 | 结合媒体查询和Vue响应式特性实现自适应 |

## 平台特性与兼容性

### Flutter
- **平台支持**: Android, iOS, Web, Windows, macOS, Linux
- **特点**: 单一代码库跨平台，但需考虑不同平台的特殊处理
- **兼容性问题**: 在低端设备可能性能受限，自定义UI替代原生组件可能导致体验不一致

### Jetpack Compose
- **平台支持**: Android
- **特点**: 完全集成Android生态，可与传统View系统混合使用
- **兼容性问题**: 仅Android平台可用，API级别要求较高(API 21+)

### UIKit
- **平台支持**: iOS, iPadOS
- **特点**: 成熟稳定，与iOS系统深度集成
- **兼容性问题**: 仅限苹果平台，不同iOS版本API差异需要处理

### SwiftUI
- **平台支持**: iOS, iPadOS, macOS, watchOS, tvOS
- **特点**: 提供统一苹果平台开发体验，声明式UI
- **兼容性问题**: 需要iOS 13+，新特性通常要求更高系统版本

### ArkUI (鸿蒙)
- **平台支持**: 鸿蒙OS设备
- **特点**: 为多设备形态设计，支持流转体验
- **兼容性问题**: 生态相对较新，主要在华为设备上运行

### Vue
- **平台支持**: 跨浏览器, 可通过框架扩展到原生平台
- **特点**: 灵活性高，可与各种Web技术结合
- **兼容性问题**: 浏览器兼容性要考虑，移动端原生体验需额外框架支持 

## 完整实例代码

### Flutter

```dart
import 'package:flutter/material.dart';

class ResponsiveLayoutDemo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // 获取设备信息
    final mediaQuery = MediaQuery.of(context);
    final screenWidth = mediaQuery.size.width;
    final screenHeight = mediaQuery.size.height;
    final devicePixelRatio = mediaQuery.devicePixelRatio;
    
    return Scaffold(
      appBar: AppBar(title: Text('Flutter 逻辑宽高示例')),
      body: Column(
        children: [
          // 设备信息显示
          Padding(
            padding: const EdgeInsets.all(16.0),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text('屏幕逻辑宽度: $screenWidth'),
                Text('屏幕逻辑高度: $screenHeight'),
                Text('设备像素比: $devicePixelRatio'),
                Text('实际物理像素宽度: ${screenWidth * devicePixelRatio}'),
                Text('实际物理像素高度: ${screenHeight * devicePixelRatio}'),
              ],
            ),
          ),
          
          // 比例尺寸示例
          Expanded(
            child: Row(
              children: [
                // 占30%宽度
                Expanded(
                  flex: 3,
                  child: Container(color: Colors.red, child: Center(child: Text('30%'))),
                ),
                // 占70%宽度
                Expanded(
                  flex: 7,
                  child: Container(color: Colors.blue, child: Center(child: Text('70%'))),
                ),
              ],
            ),
          ),
          
          // 固定尺寸与百分比混合
          Container(
            height: 100,
            child: Row(
              children: [
                // 固定宽度100逻辑像素
                Container(
                  width: 100,
                  color: Colors.green,
                  child: Center(child: Text('100px')),
                ),
                // 剩余空间
                Expanded(
                  child: Container(
                    color: Colors.yellow,
                    child: Center(child: Text('自适应剩余空间')),
                  ),
                ),
              ],
            ),
          ),
          
          // 使用LayoutBuilder响应父容器约束
          Container(
            height: 100,
            color: Colors.grey[200],
            child: LayoutBuilder(
              builder: (context, constraints) {
                return Center(
                  child: Text(
                    '父容器约束: 最大宽度=${constraints.maxWidth}, 最大高度=${constraints.maxHeight}',
                  ),
                );
              },
            ),
          ),
        ],
      ),
    );
  }
}
```

### Jetpack Compose

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.platform.LocalDensity
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun ResponsiveLayoutDemo() {
    // 获取当前密度信息
    val density = LocalDensity.current
    
    Column(modifier = Modifier.fillMaxSize()) {
        // 设备信息
        Column(modifier = Modifier.padding(16.dp)) {
            Text("密度: ${density.density}")
            Text("字体缩放: ${density.fontScale}")
            Text("1dp 等于 ${with(density) { 1.dp.toPx() }} 像素")
        }
        
        // 比例尺寸示例
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .weight(1f)
        ) {
            // 占30%宽度
            Box(
                modifier = Modifier
                    .weight(0.3f)
                    .fillMaxHeight()
                    .background(Color.Red),
                contentAlignment = Alignment.Center
            ) {
                Text("30%", color = Color.White)
            }
            
            // 占70%宽度
            Box(
                modifier = Modifier
                    .weight(0.7f)
                    .fillMaxHeight()
                    .background(Color.Blue),
                contentAlignment = Alignment.Center
            ) {
                Text("70%", color = Color.White)
            }
        }
        
        // 固定尺寸与自适应混合
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
        ) {
            // 固定宽度100dp
            Box(
                modifier = Modifier
                    .width(100.dp)
                    .fillMaxHeight()
                    .background(Color.Green),
                contentAlignment = Alignment.Center
            ) {
                Text("100dp")
            }
            
            // 剩余空间
            Box(
                modifier = Modifier
                    .weight(1f)
                    .fillMaxHeight()
                    .background(Color.Yellow),
                contentAlignment = Alignment.Center
            ) {
                Text("自适应剩余空间")
            }
        }
        
        // 使用BoxWithConstraints响应父容器约束
        BoxWithConstraints(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
                .background(Color.LightGray),
            contentAlignment = Alignment.Center
        ) {
            Text(
                "父容器约束: 最大宽度=${maxWidth}, 最大高度=${maxHeight}",
                textAlign = TextAlign.Center
            )
        }
    }
}

@Preview
@Composable
fun PreviewResponsiveLayoutDemo() {
    ResponsiveLayoutDemo()
}
```

### UIKit

```swift
import UIKit

class ResponsiveLayoutViewController: UIViewController {
    
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
        setupUI()
    }
    
    func setupUI() {
        // 设备信息显示
        let infoLabel = UILabel()
        infoLabel.numberOfLines = 0
        infoLabel.text = """
            屏幕尺寸(点): \(UIScreen.main.bounds.size)
            屏幕缩放比例: \(UIScreen.main.scale)
            实际像素尺寸: \(UIScreen.main.bounds.size.width * UIScreen.main.scale) x \(UIScreen.main.bounds.size.height * UIScreen.main.scale)
            """
        infoLabel.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(infoLabel)
        
        // 比例布局容器
        let proportionalContainer = UIView()
        proportionalContainer.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(proportionalContainer)
        
        // 30%宽度视图
        let leftView = UIView()
        leftView.backgroundColor = .red
        leftView.translatesAutoresizingMaskIntoConstraints = false
        proportionalContainer.addSubview(leftView)
        
        let leftLabel = UILabel()
        leftLabel.text = "30%"
        leftLabel.textAlignment = .center
        leftLabel.textColor = .white
        leftLabel.translatesAutoresizingMaskIntoConstraints = false
        leftView.addSubview(leftLabel)
        
        // 70%宽度视图
        let rightView = UIView()
        rightView.backgroundColor = .blue
        rightView.translatesAutoresizingMaskIntoConstraints = false
        proportionalContainer.addSubview(rightView)
        
        let rightLabel = UILabel()
        rightLabel.text = "70%"
        rightLabel.textAlignment = .center
        rightLabel.textColor = .white
        rightLabel.translatesAutoresizingMaskIntoConstraints = false
        rightView.addSubview(rightLabel)
        
        // 混合布局容器
        let mixedContainer = UIView()
        mixedContainer.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(mixedContainer)
        
        // 固定宽度视图
        let fixedView = UIView()
        fixedView.backgroundColor = .green
        fixedView.translatesAutoresizingMaskIntoConstraints = false
        mixedContainer.addSubview(fixedView)
        
        let fixedLabel = UILabel()
        fixedLabel.text = "100pt"
        fixedLabel.textAlignment = .center
        fixedLabel.translatesAutoresizingMaskIntoConstraints = false
        fixedView.addSubview(fixedLabel)
        
        // 自适应宽度视图
        let flexView = UIView()
        flexView.backgroundColor = .yellow
        flexView.translatesAutoresizingMaskIntoConstraints = false
        mixedContainer.addSubview(flexView)
        
        let flexLabel = UILabel()
        flexLabel.text = "自适应剩余空间"
        flexLabel.textAlignment = .center
        flexLabel.translatesAutoresizingMaskIntoConstraints = false
        flexView.addSubview(flexLabel)
        
        // 设置约束
        NSLayoutConstraint.activate([
            // 信息标签约束
            infoLabel.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 20),
            infoLabel.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            infoLabel.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            
            // 比例容器约束
            proportionalContainer.topAnchor.constraint(equalTo: infoLabel.bottomAnchor, constant: 20),
            proportionalContainer.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            proportionalContainer.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            proportionalContainer.heightAnchor.constraint(equalToConstant: 100),
            
            // 左侧视图约束 (30%)
            leftView.topAnchor.constraint(equalTo: proportionalContainer.topAnchor),
            leftView.leadingAnchor.constraint(equalTo: proportionalContainer.leadingAnchor),
            leftView.bottomAnchor.constraint(equalTo: proportionalContainer.bottomAnchor),
            leftView.widthAnchor.constraint(equalTo: proportionalContainer.widthAnchor, multiplier: 0.3),
            
            // 左侧标签约束
            leftLabel.centerXAnchor.constraint(equalTo: leftView.centerXAnchor),
            leftLabel.centerYAnchor.constraint(equalTo: leftView.centerYAnchor),
            
            // 右侧视图约束 (70%)
            rightView.topAnchor.constraint(equalTo: proportionalContainer.topAnchor),
            rightView.leadingAnchor.constraint(equalTo: leftView.trailingAnchor),
            rightView.trailingAnchor.constraint(equalTo: proportionalContainer.trailingAnchor),
            rightView.bottomAnchor.constraint(equalTo: proportionalContainer.bottomAnchor),
            
            // 右侧标签约束
            rightLabel.centerXAnchor.constraint(equalTo: rightView.centerXAnchor),
            rightLabel.centerYAnchor.constraint(equalTo: rightView.centerYAnchor),
            
            // 混合容器约束
            mixedContainer.topAnchor.constraint(equalTo: proportionalContainer.bottomAnchor, constant: 20),
            mixedContainer.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            mixedContainer.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            mixedContainer.heightAnchor.constraint(equalToConstant: 100),
            
            // 固定宽度视图约束
            fixedView.topAnchor.constraint(equalTo: mixedContainer.topAnchor),
            fixedView.leadingAnchor.constraint(equalTo: mixedContainer.leadingAnchor),
            fixedView.bottomAnchor.constraint(equalTo: mixedContainer.bottomAnchor),
            fixedView.widthAnchor.constraint(equalToConstant: 100),
            
            // 固定宽度标签约束
            fixedLabel.centerXAnchor.constraint(equalTo: fixedView.centerXAnchor),
            fixedLabel.centerYAnchor.constraint(equalTo: fixedView.centerYAnchor),
            
            // 自适应宽度视图约束
            flexView.topAnchor.constraint(equalTo: mixedContainer.topAnchor),
            flexView.leadingAnchor.constraint(equalTo: fixedView.trailingAnchor),
            flexView.trailingAnchor.constraint(equalTo: mixedContainer.trailingAnchor),
            flexView.bottomAnchor.constraint(equalTo: mixedContainer.bottomAnchor),
            
            // 自适应宽度标签约束
            flexLabel.centerXAnchor.constraint(equalTo: flexView.centerXAnchor),
            flexLabel.centerYAnchor.constraint(equalTo: flexView.centerYAnchor)
        ])
    }
}
```

### SwiftUI

```swift
import SwiftUI

struct ResponsiveLayoutDemo: View {
    var body: some View {
        VStack(spacing: 20) {
            // 设备信息
            VStack(alignment: .leading) {
                Text("屏幕尺寸: \(UIScreen.main.bounds.size.width) x \(UIScreen.main.bounds.size.height)")
                Text("屏幕缩放: \(UIScreen.main.scale)")
                Text("真实像素: \(UIScreen.main.bounds.size.width * UIScreen.main.scale) x \(UIScreen.main.bounds.size.height * UIScreen.main.scale)")
            }
            .padding()
            .frame(maxWidth: .infinity, alignment: .leading)
            
            // 比例布局示例
            HStack(spacing: 0) {
                // 占30%
                Text("30%")
                    .frame(maxWidth: .infinity, maxHeight: .infinity)
                    .background(Color.red)
                    .foregroundColor(.white)
                    .layoutPriority(1)
                
                // 占70%
                Text("70%")
                    .frame(maxWidth: .infinity, maxHeight: .infinity)
                    .background(Color.blue)
                    .foregroundColor(.white)
                    .layoutPriority(2.33) // 70/30 = 2.33
            }
            .frame(height: 100)
            
            // 使用GeometryReader获取父容器尺寸
            GeometryReader { geometry in
                HStack(spacing: 0) {
                    // 占30% - 根据实际计算宽度
                    Text("30%")
                        .frame(width: geometry.size.width * 0.3, height: geometry.size.height)
                        .background(Color.orange)
                        .foregroundColor(.white)
                    
                    // 占70% - 根据实际计算宽度
                    Text("70%")
                        .frame(width: geometry.size.width * 0.7, height: geometry.size.height)
                        .background(Color.purple)
                        .foregroundColor(.white)
                }
            }
            .frame(height: 100)
            
            // 固定尺寸与自适应混合
            HStack(spacing: 0) {
                // 固定宽度100点
                Text("100pt")
                    .frame(width: 100, height: 100)
                    .background(Color.green)
                
                // 自适应剩余空间
                Text("自适应剩余空间")
                    .frame(maxWidth: .infinity, height: 100)
                    .background(Color.yellow)
            }
            
            // 显示父容器提供的尺寸
            GeometryReader { geometry in
                Text("可用空间: \(Int(geometry.size.width)) x \(Int(geometry.size.height))")
                    .frame(maxWidth: .infinity, maxHeight: .infinity)
                    .background(Color.gray.opacity(0.2))
            }
            .frame(height: 100)
            
            Spacer()
        }
        .padding()
    }
}

struct ResponsiveLayoutDemo_Previews: PreviewProvider {
    static var previews: some View {
        ResponsiveLayoutDemo()
    }
}
```

### ArkUI (鸿蒙)

```typescript
@Entry
@Component
struct ResponsiveLayoutDemo {
  build() {
    Column({ space: 20 }) {
      // 设备信息
      Column() {
        Text('屏幕宽度: ' + vp2px(px2vp(Display.width)) + 'px')
        Text('屏幕高度: ' + vp2px(px2vp(Display.height)) + 'px')
        Text('1vp = ' + (vp2px(1).toFixed(2)) + 'px')
        Text('密度: ' + Display.density)
      }
      .width('100%')
      .alignItems(HorizontalAlign.Start)
      .padding(16)
      
      // 比例尺寸示例
      Row() {
        // 占30%宽度
        Text('30%')
          .width('30%')
          .height(100)
          .textAlign(TextAlign.Center)
          .backgroundColor(Color.Red)
          .fontColor(Color.White)
        
        // 占70%宽度
        Text('70%')
          .width('70%')
          .height(100)
          .textAlign(TextAlign.Center)
          .backgroundColor(Color.Blue)
          .fontColor(Color.White)
      }
      .width('100%')
      
      // 固定尺寸与百分比混合
      Row() {
        // 固定宽度100vp
        Text('100vp')
          .width(100)
          .height(100)
          .textAlign(TextAlign.Center)
          .backgroundColor(Color.Green)
        
        // 自适应剩余空间
        Text('自适应剩余空间')
          .layoutWeight(1)
          .height(100)
          .textAlign(TextAlign.Center)
          .backgroundColor(Color.Yellow)
      }
      .width('100%')
      
      // 响应式布局 - 使用媒体查询
      MediaQuery({minWidth: 600}) {
        Text('宽度大于600vp的设备显示')
          .width('100%')
          .height(100)
          .textAlign(TextAlign.Center)
          .backgroundColor(Color.Purple)
          .fontColor(Color.White)
      }
      .width('100%')
      
      // 获取容器尺寸信息
      Row() {
        Text('获取尺寸信息示例')
          .width('100%')
          .height(100)
          .backgroundColor(Color.Gray)
          .fontColor(Color.White)
          .onAreaChange((oldValue, newValue) => {
            console.info('区域变化: 宽度=' + newValue.width + ', 高度=' + newValue.height)
          })
      }
      .width('100%')
    }
    .width('100%')
    .height('100%')
    .padding(20)
  }
}
```

### Vue

```vue
<template>
  <div class="responsive-layout-demo">
    <!-- 设备信息 -->
    <div class="info-panel">
      <div>窗口宽度: {{ windowWidth }}px</div>
      <div>窗口高度: {{ windowHeight }}px</div>
      <div>设备像素比: {{ devicePixelRatio }}</div>
      <div>CSS像素比例: 1rem = {{ remSize }}px</div>
    </div>
    
    <!-- 比例尺寸示例 -->
    <div class="proportion-container">
      <!-- 占30%宽度 -->
      <div class="red-box">
        30%
      </div>
      <!-- 占70%宽度 -->
      <div class="blue-box">
        70%
      </div>
    </div>
    
    <!-- 固定尺寸与自适应混合 -->
    <div class="mixed-container">
      <!-- 固定宽度100px -->
      <div class="fixed-width">
        100px
      </div>
      <!-- 自适应剩余空间 -->
      <div class="flexible-width">
        自适应剩余空间
      </div>
    </div>
    
    <!-- 媒体查询响应式示例 -->
    <div class="responsive-box">
      <div class="current-breakpoint">
        当前断点: {{ currentBreakpoint }}
      </div>
    </div>
    
    <!-- 使用vw/vh单位 -->
    <div class="viewport-units">
      宽度50vw x 高度10vh
    </div>
  </div>
</template>

<script>
export default {
  name: 'ResponsiveLayoutDemo',
  data() {
    return {
      windowWidth: window.innerWidth,
      windowHeight: window.innerHeight,
      devicePixelRatio: window.devicePixelRatio,
      remSize: parseFloat(getComputedStyle(document.documentElement).fontSize),
      breakpoints: {
        xs: 0,
        sm: 576,
        md: 768,
        lg: 992,
        xl: 1200
      }
    }
  },
  computed: {
    currentBreakpoint() {
      const width = this.windowWidth
      if (width < this.breakpoints.sm) return 'xs'
      if (width < this.breakpoints.md) return 'sm'
      if (width < this.breakpoints.lg) return 'md'
      if (width < this.breakpoints.xl) return 'lg'
      return 'xl'
    }
  },
  mounted() {
    window.addEventListener('resize', this.handleResize)
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.handleResize)
  },
  methods: {
    handleResize() {
      this.windowWidth = window.innerWidth
      this.windowHeight = window.innerHeight
      this.remSize = parseFloat(getComputedStyle(document.documentElement).fontSize)
    }
  }
}
</script>

<style scoped>
.responsive-layout-demo {
  font-family: Arial, sans-serif;
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.info-panel {
  background-color: #f5f5f5;
  padding: 15px;
  border-radius: 4px;
  margin-bottom: 20px;
}

/* 比例尺寸容器 */
.proportion-container {
  display: flex;
  height: 100px;
  margin-bottom: 20px;
}

.red-box {
  width: 30%;
  background-color: red;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
}

.blue-box {
  width: 70%;
  background-color: blue;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* 混合尺寸容器 */
.mixed-container {
  display: flex;
  height: 100px;
  margin-bottom: 20px;
}

.fixed-width {
  width: 100px;
  background-color: green;
  display: flex;
  justify-content: center;
  align-items: center;
}

.flexible-width {
  flex: 1;
  background-color: yellow;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* 响应式示例 */
.responsive-box {
  height: 100px;
  background-color: purple;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 20px;
}

/* 媒体查询示例 */
@media (max-width: 576px) {
  .responsive-box {
    background-color: red;
  }
}

@media (min-width: 576px) and (max-width: 768px) {
  .responsive-box {
    background-color: orange;
  }
}

@media (min-width: 768px) and (max-width: 992px) {
  .responsive-box {
    background-color: yellow;
    color: black;
  }
}

@media (min-width: 992px) and (max-width: 1200px) {
  .responsive-box {
    background-color: green;
  }
}

@media (min-width: 1200px) {
  .responsive-box {
    background-color: blue;
  }
}

/* 视口单位示例 */
.viewport-units {
  width: 50vw;
  height: 10vh;
  background-color: teal;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* 响应式布局 - 在小屏幕上改变布局方向 */
@media (max-width: 768px) {
  .proportion-container,
  .mixed-container {
    flex-direction: column;
    height: auto;
  }
  
  .red-box,
  .blue-box {
    width: 100%;
    height: 50px;
  }
  
  .fixed-width {
    width: 100%;
    height: 50px;
  }
  
  .viewport-units {
    width: 100%;
  }
}
</style> 