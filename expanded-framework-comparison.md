# 六大框架 Expanded 组件比较

本文档对比Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue六大框架中Expanded组件的实现和特性，从以下维度进行分析：

- 空间分配机制
- 弹性因子控制
- 与非弹性组件交互
- 嵌套使用行为
- 溢出处理

每个框架部分包含平台特性、兼容性分析和完整示例代码。

## 目录

1. [Flutter Expanded](#flutter-expanded)
2. [Android Jetpack Compose Expanded](#android-jetpack-compose-expanded)
3. [iOS UIKit Expanded](#ios-uikit-expanded)
4. [iOS SwiftUI Expanded](#ios-swiftui-expanded)
5. [鸿蒙 ArkUI Expanded](#鸿蒙-arkui-expanded)
6. [Vue Expanded](#vue-expanded)
7. [总结对比](#总结对比)

## Flutter Expanded

Flutter的Expanded组件是封装了Flexible组件的一个便利组件，主要用于在Row、Column和Flex容器中按比例分配剩余空间。

### 空间分配机制

Expanded组件通过以下机制分配空间：

- **原理**：Expanded是Flexible组件的子类，默认`flex=1`，`fit=FlexFit.tight`
- **计算流程**：
  1. 首先计算Row/Column中所有非Expanded子组件所需的空间
  2. 剩余空间按照各Expanded组件的flex比例分配
  3. 强制Expanded子组件填充分配到的空间

```dart
Row(
  children: [
    // 固定宽度100
    Container(width: 100, height: 50, color: Colors.red),
    
    // 分配剩余空间
    Expanded(
      child: Container(height: 50, color: Colors.blue),
    ),
  ],
)
```

在上面的示例中，红色Container占用100宽度，蓝色Container将占用父容器剩余的所有宽度。

### 弹性因子控制

Expanded通过`flex`参数控制弹性：

- **flex参数**：正整数（默认为1），表示相对于其他Expanded组件的权重
- **比例计算**：每个Expanded获得的空间 = 剩余空间 × (自身flex / 所有Expanded的flex总和)

```dart
Row(
  children: [
    Expanded(
      flex: 1, // 1/3的剩余空间
      child: Container(height: 50, color: Colors.red),
    ),
    Expanded(
      flex: 2, // 2/3的剩余空间
      child: Container(height: 50, color: Colors.blue),
    ),
  ],
)
```

在上面的示例中，红色Container占用1/3的可用空间，蓝色Container占用2/3的可用空间。

### 与非弹性组件交互

Expanded与非弹性组件的交互规则：

- **优先级**：非Expanded子组件优先获得所需空间
- **强制约束**：Expanded组件会强制其子组件接受分配的空间
- **交互过程**：
  1. 非Expanded组件根据自身尺寸或约束确定大小
  2. Expanded组件分配剩余空间
  3. 如果剩余空间不足，按flex比例缩小

```dart
Row(
  children: [
    // 固定宽度组件
    Container(width: 100, height: 50, color: Colors.red),
    
    // 自适应宽度的文本
    Text('这是一些非扩展的文本'),
    
    // 弹性组件
    Expanded(
      child: Container(height: 50, color: Colors.blue),
    ),
    
    // 另一个固定宽度组件
    Container(width: 80, height: 50, color: Colors.green),
  ],
)
```

在这个例子中，固定宽度的红色和绿色Container以及自适应宽度的Text会先确定自己需要的空间，然后蓝色Container（Expanded）会占用所有剩余空间。

### 嵌套使用行为

Expanded可以在不同层级的Flex容器中嵌套使用：

- **单层嵌套**：同一个Row/Column中的多个Expanded
- **多层嵌套**：Expanded内部可以包含Row/Column，其中又有Expanded
- **计算原理**：每一层的Expanded都只作用于其直接父级的Flex容器

```dart
Row(
  children: [
    Expanded(
      flex: 1,
      child: Container(color: Colors.red),
    ),
    Expanded(
      flex: 2,
      child: Column(
        children: [
          Expanded(
            flex: 1,
            child: Container(color: Colors.green),
          ),
          Expanded(
            flex: 3,
            child: Container(color: Colors.blue),
          ),
        ],
      ),
    ),
  ],
)
```

在这个例子中：
1. 红色Container占水平方向1/3空间
2. 包含绿色和蓝色Container的Column占水平方向2/3空间
3. 在该Column内，绿色Container占垂直方向1/4空间，蓝色Container占垂直方向3/4空间

### 溢出处理

当空间不足时，Expanded的处理方式：

- **设计原则**：Expanded会尽量保持相对比例，但优先满足最小约束
- **溢出情况**：
  1. **子组件有最小尺寸要求**：如果分配空间小于最小尺寸，可能导致溢出
  2. **空间严重不足**：会根据flex比例缩小，但不会小于最小约束
  3. **完全无法满足**：父容器可能会溢出，Flutter会显示溢出警告

```dart
// 可能导致溢出的情况
Container(
  width: 200, // 有限的宽度
  child: Row(
    children: [
      Container(width: 100, color: Colors.red),
      Container(width: 150, color: Colors.green),
      Expanded(
        child: Container(
          constraints: BoxConstraints(minWidth: 100),
          color: Colors.blue,
        ),
      ),
    ],
  ),
)
```

在这个例子中，非Expanded组件已经占用了250宽度（超过父容器200宽度），Expanded组件还需要最小100宽度，这将导致溢出。

Flutter会通过以下方式报告溢出：
1. Debug模式下显示黄黑条纹溢出警告
2. 控制台输出溢出错误信息

### 平台特性与兼容性

Flutter的Expanded组件优势与特点：

- **跨平台一致性**：在所有支持平台上具有完全一致的行为
- **性能优化**：Flutter渲染引擎针对Flex布局做了优化
- **精确控制**：通过整数flex值提供精确的空间分配比例控制
- **构建模式**：采用声明式UI构建，易于理解和维护
- **开发体验**：通过Hot Reload快速预览和调整布局
- **调试友好**：提供清晰的溢出视觉提示和错误信息
- **无外部依赖**：核心组件，无需额外包

需要注意的事项：

- **运行时调整**：flex值不能在运行时直接动态修改（需重建组件）
- **使用限制**：仅在Row、Column或Flex中才有效
- **约束传递**：Expanded会给子组件传递严格的约束（紧约束）
- **嵌套复杂性**：深度嵌套的flex计算可能导致性能影响
- **溢出处理**：处理溢出情况需要谨慎设计布局

### 完整示例代码

```dart
import 'package:flutter/material.dart';

class ExpandedDemo extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Flutter Expanded示例')),
      body: Column(
        children: [
          // 基本用法 - 水平方向
          Container(
            height: 100,
            color: Colors.grey[200],
            child: Row(
              children: [
                Container(width: 80, color: Colors.red),
                Expanded(
                  child: Container(color: Colors.blue),
                ),
                Container(width: 80, color: Colors.green),
              ],
            ),
          ),
          SizedBox(height: 20),
          
          // flex比例分配 - 水平方向
          Container(
            height: 100,
            color: Colors.grey[200],
            child: Row(
              children: [
                Expanded(
                  flex: 1,
                  child: Container(color: Colors.red),
                ),
                Expanded(
                  flex: 2,
                  child: Container(color: Colors.blue),
                ),
                Expanded(
                  flex: 1,
                  child: Container(color: Colors.green),
                ),
              ],
            ),
          ),
          SizedBox(height: 20),
          
          // 与非弹性组件混合
          Container(
            height: 100,
            color: Colors.grey[200],
            child: Row(
              children: [
                Container(width: 80, color: Colors.red),
                Expanded(
                  flex: 2,
                  child: Container(color: Colors.blue),
                ),
                Expanded(
                  flex: 1,
                  child: Container(color: Colors.green),
                ),
                Text(' 固定文本 ', style: TextStyle(backgroundColor: Colors.yellow)),
              ],
            ),
          ),
          SizedBox(height: 20),
          
          // 嵌套使用
          Expanded(
            child: Row(
              children: [
                Expanded(
                  flex: 1,
                  child: Container(color: Colors.red[200]),
                ),
                Expanded(
                  flex: 2,
                  child: Column(
                    children: [
                      Expanded(
                        flex: 2,
                        child: Container(color: Colors.blue[200]),
                      ),
                      Expanded(
                        flex: 1,
                        child: Row(
                          children: [
                            Expanded(
                              flex: 1,
                              child: Container(color: Colors.green[200]),
                            ),
                            Expanded(
                              flex: 1,
                              child: Container(color: Colors.purple[200]),
                            ),
                          ],
                        ),
                      ),
                    ],
                  ),
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }
}
```

这个示例展示了Expanded组件的多种用法，包括基本空间分配、flex比例控制、与非弹性组件混合使用以及复杂的嵌套结构。 

## Android Jetpack Compose Expanded

Jetpack Compose中的Expanded是一个修饰符（Modifier），与Flutter不同，它不是独立的组件，而是应用于可组合项的修饰符，主要用于Row和Column布局中分配剩余空间。

### 空间分配机制

Compose的Expanded修饰符通过以下机制分配空间：

- **原理**：Expanded是Modifier.weight()的便捷封装，默认weight=1f
- **计算流程**：
  1. Row/Column首先测量所有未应用weight的子项所需空间
  2. 剩余空间按照各weight修饰符的权重比例分配
  3. 强制应用了weight修饰符的子项填充分配的空间

```kotlin
Row {
    // 固定宽度组件
    Box(
        modifier = Modifier
            .size(100.dp, 50.dp)
            .background(Color.Red)
    )
    
    // 分配剩余空间
    Box(
        modifier = Modifier
            .height(50.dp)
            .background(Color.Blue)
            .weight(1f) // 等同于使用Expanded()
    )
}
```

也可以使用Expanded修饰符的简写形式：

```kotlin
Row {
    // 固定宽度组件
    Box(
        modifier = Modifier
            .size(100.dp, 50.dp)
            .background(Color.Red)
    )
    
    // 分配剩余空间
    Box(
        modifier = Modifier
            .height(50.dp)
            .background(Color.Blue)
            .expanded() // 更简洁的写法，等同于weight(1f)
    )
}
```

### 弹性因子控制

Expanded通过weight参数控制弹性：

- **weight参数**：浮点数（默认为1f），表示相对权重
- **比例计算**：每个weight获得的空间 = 剩余空间 × (自身weight / 所有weight总和)
- **fill参数**：布尔值（默认为true），控制是否填充全部分配空间

```kotlin
Row {
    Box(
        modifier = Modifier
            .height(50.dp)
            .background(Color.Red)
            .weight(1f) // 1/3的剩余空间
    )
    Box(
        modifier = Modifier
            .height(50.dp)
            .background(Color.Blue)
            .weight(2f) // 2/3的剩余空间
    )
}
```

Compose的weight修饰符比Flutter的Expanded更灵活，它有一个额外的fill参数：

```kotlin
Row {
    Box(
        modifier = Modifier
            .size(100.dp, 50.dp)
            .background(Color.Red)
    )
    Box(
        modifier = Modifier
            .width(100.dp) // 当fill=false时，这个宽度会生效
            .fillMaxHeight()
            .weight(1f, fill = false) // 分配空间但不强制填充
            .background(Color.Blue)
    )
}
```

当fill=false时，组件会保持自己的首选尺寸，但仍会影响空间分配计算。

### 与非弹性组件交互

Expanded与非弹性组件的交互规则：

- **优先级**：未使用weight的子项优先获得所需空间
- **交互过程**：
  1. 非weight组件根据自身约束确定大小
  2. weight修饰符分配剩余空间
  3. 如果剩余空间不足，按weight比例缩小

```kotlin
Row {
    // 固定宽度组件
    Box(
        modifier = Modifier
            .size(100.dp, 50.dp)
            .background(Color.Red)
    )
    
    // 自适应宽度的文本
    Text(
        text = "这是一些非扩展的文本",
        modifier = Modifier.background(Color.Yellow)
    )
    
    // 弹性组件
    Box(
        modifier = Modifier
            .height(50.dp)
            .background(Color.Blue)
            .weight(1f)
    )
    
    // 另一个固定宽度组件
    Box(
        modifier = Modifier
            .size(80.dp, 50.dp)
            .background(Color.Green)
    )
}
```

### 嵌套使用行为

Expanded可以在嵌套的Row或Column中使用：

- **计算原理**：每一层的weight修饰符只作用于其直接父级的Row/Column
- **嵌套规则**：内外层的weight不会相互影响，各自计算各自层级的空间分配

```kotlin
Row {
    Box(
        modifier = Modifier
            .fillMaxHeight()
            .background(Color.Red)
            .weight(1f)
    )
    
    Column(
        modifier = Modifier
            .fillMaxHeight()
            .weight(2f)
    ) {
        Box(
            modifier = Modifier
                .background(Color.Green)
                .weight(1f)
        )
        Box(
            modifier = Modifier
                .background(Color.Blue)
                .weight(3f)
        )
    }
}
```

在这个例子中：
1. 红色Box占水平方向1/3空间
2. 包含绿色和蓝色Box的Column占水平方向2/3空间
3. 在该Column内，绿色Box占垂直方向1/4空间，蓝色Box占垂直方向3/4空间

### 溢出处理

当空间不足时，Compose的处理方式：

- **设计原则**：尽量保持weight相对比例，但优先满足最小约束
- **溢出情况**：
  1. 如果子项有minWidth/minHeight约束，weight分配可能导致溢出
  2. 当总空间小于所有最小约束总和时，布局会溢出
  3. Compose会提供可视化溢出指示器帮助调试

```kotlin
// 可能导致溢出的情况
Box(modifier = Modifier.width(200.dp)) {
    Row {
        Box(
            modifier = Modifier
                .size(100.dp, 50.dp)
                .background(Color.Red)
        )
        Box(
            modifier = Modifier
                .size(150.dp, 50.dp)
                .background(Color.Green)
        )
        Box(
            modifier = Modifier
                .heightIn(min = 50.dp)
                .widthIn(min = 100.dp)
                .background(Color.Blue)
                .weight(1f)
        )
    }
}
```

Compose提供开发模式下的溢出可视化：
1. 红色虚线指示溢出区域
2. 日志输出详细的约束违规信息

### 平台特性与兼容性

Jetpack Compose的Expanded优势与特点：

- **Android原生支持**：专为Android平台优化
- **声明式API**：与整个Compose框架保持一致的声明式风格
- **响应式设计**：与Compose的状态管理无缝集成
- **灵活性**：通过fill参数提供额外的灵活性
- **浮点精度**：支持浮点数weight，比Flutter更精细
- **便捷性**：weight修饰符作为通用Modifier可与其他修饰符链式组合
- **预览功能**：可使用@Preview直接预览不同配置的布局
- **主题集成**：与Material Design主题系统无缝集成
- **动画支持**：可以对weight值应用动画效果

需要注意的事项：

- **平台限制**：仅适用于Android平台
- **使用限制**：仅在Row、Column中才有效
- **空间计算**：与传统View系统的权重计算存在差异
- **兼容性问题**：与旧版本Android设备可能存在兼容性问题
- **内存管理**：复杂嵌套布局需要注意内存占用
- **重组影响**：weight修改会触发重组，需要考虑性能
- **调试复杂性**：嵌套weight问题的调试可能较为复杂

### 完整示例代码

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp

@Composable
fun ExpandedDemo() {
    Column(modifier = Modifier.fillMaxSize()) {
        // 基本用法 - 水平方向
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
                .background(Color.LightGray)
        ) {
            Box(
                modifier = Modifier
                    .width(80.dp)
                    .fillMaxHeight()
                    .background(Color.Red)
            )
            Box(
                modifier = Modifier
                    .fillMaxHeight()
                    .weight(1f)
                    .background(Color.Blue)
            )
            Box(
                modifier = Modifier
                    .width(80.dp)
                    .fillMaxHeight()
                    .background(Color.Green)
            )
        }
        
        Spacer(modifier = Modifier.height(20.dp))
        
        // weight比例分配 - 水平方向
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
                .background(Color.LightGray)
        ) {
            Box(
                modifier = Modifier
                    .fillMaxHeight()
                    .weight(1f)
                    .background(Color.Red)
            )
            Box(
                modifier = Modifier
                    .fillMaxHeight()
                    .weight(2f)
                    .background(Color.Blue)
            )
            Box(
                modifier = Modifier
                    .fillMaxHeight()
                    .weight(1f)
                    .background(Color.Green)
            )
        }
        
        Spacer(modifier = Modifier.height(20.dp))
        
        // 与非弹性组件混合
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
                .background(Color.LightGray)
        ) {
            Box(
                modifier = Modifier
                    .width(80.dp)
                    .fillMaxHeight()
                    .background(Color.Red)
            )
            Box(
                modifier = Modifier
                    .fillMaxHeight()
                    .weight(2f)
                    .background(Color.Blue)
            )
            Box(
                modifier = Modifier
                    .fillMaxHeight()
                    .weight(1f)
                    .background(Color.Green)
            )
            Text(
                text = " 固定文本 ",
                modifier = Modifier.background(Color.Yellow)
            )
        }
        
        Spacer(modifier = Modifier.height(20.dp))
        
        // fill参数演示
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
                .background(Color.LightGray)
        ) {
            Box(
                modifier = Modifier
                    .width(100.dp)
                    .fillMaxHeight()
                    .background(Color.Red)
            )
            Box(
                modifier = Modifier
                    .width(100.dp)  // 这个宽度会被考虑，因为fill=false
                    .fillMaxHeight()
                    .weight(1f, fill = false)
                    .background(Color.Blue)
            )
        }
        
        // 嵌套使用
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .weight(1f) // 占用剩余垂直空间
                .background(Color.LightGray)
        ) {
            Box(
                modifier = Modifier
                    .fillMaxHeight()
                    .weight(1f)
                    .background(Color(0xFFFFCDD2)) // 浅红色
            )
            
            Column(
                modifier = Modifier
                    .fillMaxHeight()
                    .weight(2f)
            ) {
                Box(
                    modifier = Modifier
                        .fillMaxWidth()
                        .weight(2f)
                        .background(Color(0xFFBBDEFB)) // 浅蓝色
                )
                
                Row(
                    modifier = Modifier
                        .fillMaxWidth()
                        .weight(1f)
                ) {
                    Box(
                        modifier = Modifier
                            .fillMaxHeight()
                            .weight(1f)
                            .background(Color(0xFFC8E6C9)) // 浅绿色
                    )
                    Box(
                        modifier = Modifier
                            .fillMaxHeight()
                            .weight(1f)
                            .background(Color(0xFFE1BEE7)) // 浅紫色
                    )
                }
            }
        }
    }
}

@Preview(showBackground = true)
@Composable
fun PreviewExpandedDemo() {
    ExpandedDemo()
}
```

这个示例展示了Jetpack Compose中weight修饰符的各种用法，包括基本空间分配、比例控制、fill参数使用以及复杂的嵌套结构。 

## iOS UIKit Expanded

UIKit中没有直接对应Expanded的组件，但通过Auto Layout和UIStackView可以实现类似的功能。主要有两种实现方式：UIStackView中设置distribution和UIView使用约束。

### 空间分配机制

UIKit实现Expanded功能的方式：

- **UIStackView方式**：通过设置`distribution = .fill`并配合`UIStackView.Distribution.fillProportionally`
- **Auto Layout方式**：使用`UILayoutPriority`设置视图拉伸优先级和权重

```swift
// 使用UIStackView实现类似Expanded
let stackView = UIStackView()
stackView.axis = .horizontal
stackView.distribution = .fill // 填充可用空间

// 固定宽度视图
let fixedView = UIView()
fixedView.backgroundColor = .red
fixedView.widthAnchor.constraint(equalToConstant: 100).isActive = true

// 可扩展视图
let expandedView = UIView()
expandedView.backgroundColor = .blue
// UIStackView中不设置宽度的视图会自动扩展

stackView.addArrangedSubview(fixedView)
stackView.addArrangedSubview(expandedView)
```

使用Auto Layout实现Expanded：

```swift
// 使用Auto Layout实现类似Expanded
let containerView = UIView()

let fixedView = UIView()
fixedView.backgroundColor = .red
fixedView.translatesAutoresizingMaskIntoConstraints = false

let expandedView = UIView()
expandedView.backgroundColor = .blue
expandedView.translatesAutoresizingMaskIntoConstraints = false

containerView.addSubview(fixedView)
containerView.addSubview(expandedView)

NSLayoutConstraint.activate([
    // 固定视图宽度
    fixedView.widthAnchor.constraint(equalToConstant: 100),
    fixedView.leadingAnchor.constraint(equalTo: containerView.leadingAnchor),
    fixedView.topAnchor.constraint(equalTo: containerView.topAnchor),
    fixedView.bottomAnchor.constraint(equalTo: containerView.bottomAnchor),
    
    // 扩展视图约束
    expandedView.leadingAnchor.constraint(equalTo: fixedView.trailingAnchor),
    expandedView.trailingAnchor.constraint(equalTo: containerView.trailingAnchor),
    expandedView.topAnchor.constraint(equalTo: containerView.topAnchor),
    expandedView.bottomAnchor.constraint(equalTo: containerView.bottomAnchor)
])
```

### 弹性因子控制

UIKit中可以通过以下方式控制弹性因子：

- **UIStackView方式**：使用`setCustomSpacing`和内容压缩阻力优先级
- **Auto Layout方式**：通过`UILayoutPriority`设置不同的拉伸优先级

```swift
// UIStackView中控制弹性因子
let stackView = UIStackView()
stackView.axis = .horizontal
stackView.distribution = .fillProportionally // 按比例填充

// 弹性视图1 - 1份空间
let view1 = UIView()
view1.backgroundColor = .red
view1.setContentHuggingPriority(.defaultLow, for: .horizontal)
view1.setContentCompressionResistancePriority(.defaultLow, for: .horizontal)

// 弹性视图2 - 2份空间
let view2 = UIView()
view2.backgroundColor = .blue
view2.setContentHuggingPriority(.defaultLow - 1, for: .horizontal) // 更低的抗拉伸性
view2.setContentCompressionResistancePriority(.defaultLow, for: .horizontal)

stackView.addArrangedSubview(view1)
stackView.addArrangedSubview(view2)
```

使用Auto Layout实现弹性因子控制：

```swift
// 使用Auto Layout实现弹性因子控制
let containerView = UIView()

let view1 = UIView()
view1.backgroundColor = .red
view1.translatesAutoresizingMaskIntoConstraints = false

let view2 = UIView()
view2.backgroundColor = .blue
view2.translatesAutoresizingMaskIntoConstraints = false

containerView.addSubview(view1)
containerView.addSubview(view2)

// 创建等宽约束，但优先级较低
let equalWidthConstraint = view1.widthAnchor.constraint(equalTo: view2.widthAnchor)
equalWidthConstraint.priority = UILayoutPriority(500) // 较低优先级
equalWidthConstraint.isActive = true

// 创建宽度比例约束（view2宽度是view1的2倍）
let proportionalConstraint = view2.widthAnchor.constraint(equalTo: view1.widthAnchor, multiplier: 2.0)
proportionalConstraint.priority = UILayoutPriority(750) // 较高优先级
proportionalConstraint.isActive = true

NSLayoutConstraint.activate([
    view1.leadingAnchor.constraint(equalTo: containerView.leadingAnchor),
    view1.topAnchor.constraint(equalTo: containerView.topAnchor),
    view1.bottomAnchor.constraint(equalTo: containerView.bottomAnchor),
    
    view2.leadingAnchor.constraint(equalTo: view1.trailingAnchor),
    view2.trailingAnchor.constraint(equalTo: containerView.trailingAnchor),
    view2.topAnchor.constraint(equalTo: containerView.topAnchor),
    view2.bottomAnchor.constraint(equalTo: containerView.bottomAnchor)
])
```

### 与非弹性组件交互

UIKit中弹性与非弹性组件交互：

- **UIStackView方式**：非弹性组件保持固定尺寸，弹性组件分配剩余空间
- **Auto Layout方式**：通过优先级控制非弹性和弹性组件的空间分配

```swift
// UIStackView中弹性与非弹性组件交互
let stackView = UIStackView()
stackView.axis = .horizontal
stackView.distribution = .fill
stackView.spacing = 10

// 非弹性组件1 - 固定宽度
let fixedView1 = UIView()
fixedView1.backgroundColor = .red
fixedView1.widthAnchor.constraint(equalToConstant: 80).isActive = true

// 弹性组件 - 占据剩余空间
let expandingView = UIView()
expandingView.backgroundColor = .blue
expandingView.setContentHuggingPriority(.defaultLow, for: .horizontal)

// 非弹性组件2 - 自然尺寸
let label = UILabel()
label.text = "固定文本"
label.backgroundColor = .yellow
label.setContentHuggingPriority(.required, for: .horizontal) // 防止被拉伸

stackView.addArrangedSubview(fixedView1)
stackView.addArrangedSubview(expandingView)
stackView.addArrangedSubview(label)
```

### 嵌套使用行为

UIKit中的嵌套弹性布局：

- **多层UIStackView**：可以嵌套使用，每层独立计算空间分配
- **复杂约束组合**：通过自上而下传递约束实现复杂嵌套布局

```swift
// 嵌套UIStackView
let outerStackView = UIStackView()
outerStackView.axis = .horizontal
outerStackView.distribution = .fill
outerStackView.spacing = 10

// 左侧视图 - 1/3宽度
let leftView = UIView()
leftView.backgroundColor = .red
leftView.setContentHuggingPriority(.defaultLow, for: .horizontal)

// 右侧容器 (2份宽度)
let rightContainer = UIStackView()
rightContainer.axis = .vertical
rightContainer.distribution = .fill
rightContainer.spacing = 8
rightContainer.setContentHuggingPriority(.defaultLow - 1, for: .horizontal)

// 右上视图 (2份高度)
let topView = UIView()
topView.backgroundColor = .green
topView.setContentHuggingPriority(.defaultLow, for: .vertical)

// 右下容器 (1份高度)
let bottomContainer = UIStackView()
bottomContainer.axis = .horizontal
bottomContainer.distribution = .fillEqually
bottomContainer.spacing = 8
bottomContainer.setContentHuggingPriority(.defaultLow, for: .vertical)

// 右下左视图
let bottomLeftView = UIView()
bottomLeftView.backgroundColor = .green

// 右下右视图
let bottomRightView = UIView()
bottomRightView.backgroundColor = .blue

// 构建嵌套结构
bottomContainer.addArrangedSubview(bottomLeftView)
bottomContainer.addArrangedSubview(bottomRightView)

rightContainer.addArrangedSubview(topView)
rightContainer.addArrangedSubview(bottomContainer)

outerStackView.addArrangedSubview(leftView)
outerStackView.addArrangedSubview(rightContainer)
```

### 溢出处理

UIKit处理溢出的方式：

- **压缩阻力**：通过`setContentCompressionResistancePriority`控制哪些视图可以被压缩
- **裁剪控制**：通过`clipsToBounds`属性控制是否裁剪溢出内容
- **滚动视图**：将内容放入UIScrollView以支持溢出滚动

```swift
// 处理溢出情况
let stackView = UIStackView()
stackView.axis = .horizontal
stackView.distribution = .fill
stackView.spacing = 5
stackView.clipsToBounds = true // 裁剪溢出内容

// 固定宽度视图1
let fixedView1 = UIView()
fixedView1.backgroundColor = .red
fixedView1.widthAnchor.constraint(equalToConstant: 100).isActive = true
fixedView1.setContentCompressionResistancePriority(.required, for: .horizontal) // 不允许压缩

// 固定宽度视图2
let fixedView2 = UIView()
fixedView2.backgroundColor = .green
fixedView2.widthAnchor.constraint(equalToConstant: 120).isActive = true
fixedView2.setContentCompressionResistancePriority(.defaultHigh, for: .horizontal) // 高压缩阻力

// 弹性视图
let flexView = UIView()
flexView.backgroundColor = .blue
flexView.setContentHuggingPriority(.defaultLow, for: .horizontal)
flexView.setContentCompressionResistancePriority(.defaultLow, for: .horizontal) // 低压缩阻力

stackView.addArrangedSubview(fixedView1)
stackView.addArrangedSubview(flexView)
stackView.addArrangedSubview(fixedView2)
```

### 平台特性与兼容性

UIKit的Expanded功能特点与限制：

优势与特点：
- **精细控制**：通过优先级机制提供非常精细的布局控制
- **动态响应**：自动适应内容变化和容器大小变化
- **高度定制**：可以通过约束系统实现复杂的弹性布局
- **原生性能**：作为系统原生API，性能经过优化
- **Interface Builder支持**：可通过可视化界面设置弹性布局
- **动画支持**：约束变更可以平滑动画
- **无障碍支持**：与iOS无障碍功能完全集成
- **本地化支持**：自动适应不同语言的文本长度变化

限制与注意事项：
- **语法冗长**：相比其他框架，设置较为复杂
- **调试困难**：约束冲突问题难以调试
- **仅限iOS平台**：不能跨平台使用
- **学习曲线陡峭**：Auto Layout系统需要时间掌握
- **优先级概念复杂**：需要理解内容压缩、抗拉伸等概念
- **性能考虑**：复杂约束系统可能影响性能
- **代码量大**：实现相同功能需要更多代码

### 完整示例代码

```swift
import UIKit

class ExpandedDemoViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
        
        setupBasicExample()
        setupProportionalExample()
        setupMixedContentExample()
        setupNestedExample()
    }
    
    // 基本用法示例
    func setupBasicExample() {
        let containerView = UIView()
        containerView.translatesAutoresizingMaskIntoConstraints = false
        containerView.backgroundColor = UIColor.lightGray.withAlphaComponent(0.3)
        view.addSubview(containerView)
        
        // 基本水平StackView
        let stackView = UIStackView()
        stackView.axis = .horizontal
        stackView.distribution = .fill
        stackView.spacing = 8
        stackView.translatesAutoresizingMaskIntoConstraints = false
        containerView.addSubview(stackView)
        
        // 固定宽度视图
        let fixedView = UIView()
        fixedView.backgroundColor = .systemRed
        fixedView.translatesAutoresizingMaskIntoConstraints = false
        fixedView.widthAnchor.constraint(equalToConstant: 80).isActive = true
        
        // 弹性视图
        let expandedView = UIView()
        expandedView.backgroundColor = .systemBlue
        expandedView.setContentHuggingPriority(.defaultLow, for: .horizontal)
        
        // 固定宽度视图2
        let fixedView2 = UIView()
        fixedView2.backgroundColor = .systemGreen
        fixedView2.translatesAutoresizingMaskIntoConstraints = false
        fixedView2.widthAnchor.constraint(equalToConstant: 80).isActive = true
        
        stackView.addArrangedSubview(fixedView)
        stackView.addArrangedSubview(expandedView)
        stackView.addArrangedSubview(fixedView2)
        
        // 布局约束
        NSLayoutConstraint.activate([
            containerView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 20),
            containerView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            containerView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            containerView.heightAnchor.constraint(equalToConstant: 80),
            
            stackView.topAnchor.constraint(equalTo: containerView.topAnchor, constant: 8),
            stackView.leadingAnchor.constraint(equalTo: containerView.leadingAnchor, constant: 8),
            stackView.trailingAnchor.constraint(equalTo: containerView.trailingAnchor, constant: -8),
            stackView.bottomAnchor.constraint(equalTo: containerView.bottomAnchor, constant: -8)
        ])
        
        // 标签
        let label = UILabel()
        label.text = "基本示例"
        label.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(label)
        
        NSLayoutConstraint.activate([
            label.bottomAnchor.constraint(equalTo: containerView.topAnchor, constant: -4),
            label.leadingAnchor.constraint(equalTo: containerView.leadingAnchor)
        ])
    }
    
    // 比例分配示例
    func setupProportionalExample() {
        let containerView = UIView()
        containerView.translatesAutoresizingMaskIntoConstraints = false
        containerView.backgroundColor = UIColor.lightGray.withAlphaComponent(0.3)
        view.addSubview(containerView)
        
        // 比例分配StackView
        let stackView = UIStackView()
        stackView.axis = .horizontal
        stackView.distribution = .fill
        stackView.spacing = 8
        stackView.translatesAutoresizingMaskIntoConstraints = false
        containerView.addSubview(stackView)
        
        // 1份空间
        let view1 = UIView()
        view1.backgroundColor = .systemRed
        view1.setContentHuggingPriority(.defaultLow, for: .horizontal)
        
        // 2份空间
        let view2 = UIView()
        view2.backgroundColor = .systemBlue
        view2.setContentHuggingPriority(.defaultLow - 1, for: .horizontal)
        
        // 1份空间
        let view3 = UIView()
        view3.backgroundColor = .systemGreen
        view3.setContentHuggingPriority(.defaultLow, for: .horizontal)
        
        stackView.addArrangedSubview(view1)
        stackView.addArrangedSubview(view2)
        stackView.addArrangedSubview(view3)
        
        // 设置view2的宽度为view1的2倍
        let proportionalConstraint = view2.widthAnchor.constraint(equalTo: view1.widthAnchor, multiplier: 2.0)
        proportionalConstraint.priority = .defaultHigh
        proportionalConstraint.isActive = true
        
        // 设置view3的宽度与view1相等
        let equalConstraint = view3.widthAnchor.constraint(equalTo: view1.widthAnchor)
        equalConstraint.priority = .defaultHigh
        equalConstraint.isActive = true
        
        // 布局约束
        NSLayoutConstraint.activate([
            containerView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 120),
            containerView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            containerView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            containerView.heightAnchor.constraint(equalToConstant: 80),
            
            stackView.topAnchor.constraint(equalTo: containerView.topAnchor, constant: 8),
            stackView.leadingAnchor.constraint(equalTo: containerView.leadingAnchor, constant: 8),
            stackView.trailingAnchor.constraint(equalTo: containerView.trailingAnchor, constant: -8),
            stackView.bottomAnchor.constraint(equalTo: containerView.bottomAnchor, constant: -8)
        ])
        
        // 标签
        let label = UILabel()
        label.text = "比例分配示例 (1:2:1)"
        label.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(label)
        
        NSLayoutConstraint.activate([
            label.bottomAnchor.constraint(equalTo: containerView.topAnchor, constant: -4),
            label.leadingAnchor.constraint(equalTo: containerView.leadingAnchor)
        ])
    }
    
    // 混合内容示例
    func setupMixedContentExample() {
        let containerView = UIView()
        containerView.translatesAutoresizingMaskIntoConstraints = false
        containerView.backgroundColor = UIColor.lightGray.withAlphaComponent(0.3)
        view.addSubview(containerView)
        
        // 混合内容StackView
        let stackView = UIStackView()
        stackView.axis = .horizontal
        stackView.distribution = .fill
        stackView.spacing = 8
        stackView.translatesAutoresizingMaskIntoConstraints = false
        containerView.addSubview(stackView)
        
        // 固定宽度视图
        let fixedView = UIView()
        fixedView.backgroundColor = .systemRed
        fixedView.translatesAutoresizingMaskIntoConstraints = false
        fixedView.widthAnchor.constraint(equalToConstant: 80).isActive = true
        
        // 弹性视图1 (2份)
        let expandedView1 = UIView()
        expandedView1.backgroundColor = .systemBlue
        expandedView1.setContentHuggingPriority(.defaultLow - 1, for: .horizontal)
        
        // 弹性视图2 (1份)
        let expandedView2 = UIView()
        expandedView2.backgroundColor = .systemGreen
        expandedView2.setContentHuggingPriority(.defaultLow, for: .horizontal)
        
        // 固定文本
        let label = UILabel()
        label.text = " 固定文本 "
        label.backgroundColor = .systemYellow
        label.setContentHuggingPriority(.required, for: .horizontal)
        
        stackView.addArrangedSubview(fixedView)
        stackView.addArrangedSubview(expandedView1)
        stackView.addArrangedSubview(expandedView2)
        stackView.addArrangedSubview(label)
        
        // 设置expandedView1宽度为expandedView2的2倍
        let proportionalConstraint = expandedView1.widthAnchor.constraint(equalTo: expandedView2.widthAnchor, multiplier: 2.0)
        proportionalConstraint.priority = .defaultHigh
        proportionalConstraint.isActive = true
        
        // 布局约束
        NSLayoutConstraint.activate([
            containerView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 220),
            containerView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            containerView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            containerView.heightAnchor.constraint(equalToConstant: 80),
            
            stackView.topAnchor.constraint(equalTo: containerView.topAnchor, constant: 8),
            stackView.leadingAnchor.constraint(equalTo: containerView.leadingAnchor, constant: 8),
            stackView.trailingAnchor.constraint(equalTo: containerView.trailingAnchor, constant: -8),
            stackView.bottomAnchor.constraint(equalTo: containerView.bottomAnchor, constant: -8)
        ])
        
        // 标签
        let titleLabel = UILabel()
        titleLabel.text = "混合内容示例"
        titleLabel.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(titleLabel)
        
        NSLayoutConstraint.activate([
            titleLabel.bottomAnchor.constraint(equalTo: containerView.topAnchor, constant: -4),
            titleLabel.leadingAnchor.constraint(equalTo: containerView.leadingAnchor)
        ])
    }
    
    // 嵌套布局示例
    func setupNestedExample() {
        let containerView = UIView()
        containerView.translatesAutoresizingMaskIntoConstraints = false
        containerView.backgroundColor = UIColor.lightGray.withAlphaComponent(0.3)
        view.addSubview(containerView)
        
        // 外层水平StackView
        let outerStackView = UIStackView()
        outerStackView.axis = .horizontal
        outerStackView.distribution = .fill
        outerStackView.spacing = 8
        outerStackView.translatesAutoresizingMaskIntoConstraints = false
        containerView.addSubview(outerStackView)
        
        // 左侧视图 (1份宽度)
        let leftView = UIView()
        leftView.backgroundColor = UIColor(red: 1.0, green: 0.8, blue: 0.8, alpha: 1.0) // 浅红色
        leftView.setContentHuggingPriority(.defaultLow, for: .horizontal)
        
        // 右侧容器 (2份宽度)
        let rightContainer = UIStackView()
        rightContainer.axis = .vertical
        rightContainer.distribution = .fill
        rightContainer.spacing = 8
        rightContainer.setContentHuggingPriority(.defaultLow - 1, for: .horizontal)
        
        // 右上视图 (2份高度)
        let topView = UIView()
        topView.backgroundColor = UIColor(red: 0.8, green: 0.9, blue: 1.0, alpha: 1.0) // 浅蓝色
        topView.setContentHuggingPriority(.defaultLow - 1, for: .vertical)
        
        // 右下容器 (1份高度)
        let bottomContainer = UIStackView()
        bottomContainer.axis = .horizontal
        bottomContainer.distribution = .fillEqually
        bottomContainer.spacing = 8
        bottomContainer.setContentHuggingPriority(.defaultLow, for: .vertical)
        
        // 右下左视图
        let bottomLeftView = UIView()
        bottomLeftView.backgroundColor = UIColor(red: 0.8, green: 1.0, blue: 0.8, alpha: 1.0) // 浅绿色
        
        // 右下右视图
        let bottomRightView = UIView()
        bottomRightView.backgroundColor = UIColor(red: 0.9, green: 0.8, blue: 1.0, alpha: 1.0) // 浅紫色
        
        // 构建嵌套结构
        bottomContainer.addArrangedSubview(bottomLeftView)
        bottomContainer.addArrangedSubview(bottomRightView)
        
        rightContainer.addArrangedSubview(topView)
        rightContainer.addArrangedSubview(bottomContainer)
        
        outerStackView.addArrangedSubview(leftView)
        outerStackView.addArrangedSubview(rightContainer)
        
        // 设置rightContainer宽度为leftView的2倍
        let proportionalConstraint = rightContainer.widthAnchor.constraint(equalTo: leftView.widthAnchor, multiplier: 2.0)
        proportionalConstraint.priority = .defaultHigh
        proportionalConstraint.isActive = true
        
        // 设置topView高度为bottomContainer的2倍
        let verticalConstraint = topView.heightAnchor.constraint(equalTo: bottomContainer.heightAnchor, multiplier: 2.0)
        verticalConstraint.priority = .defaultHigh
        verticalConstraint.isActive = true
        
        // 布局约束
        NSLayoutConstraint.activate([
            containerView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 320),
            containerView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            containerView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            containerView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor, constant: -20),
            
            outerStackView.topAnchor.constraint(equalTo: containerView.topAnchor, constant: 8),
            outerStackView.leadingAnchor.constraint(equalTo: containerView.leadingAnchor, constant: 8),
            outerStackView.trailingAnchor.constraint(equalTo: containerView.trailingAnchor, constant: -8),
            outerStackView.bottomAnchor.constraint(equalTo: containerView.bottomAnchor, constant: -8)
        ])
        
        // 标签
        let titleLabel = UILabel()
        titleLabel.text = "嵌套布局示例"
        titleLabel.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(titleLabel)
        
        NSLayoutConstraint.activate([
            titleLabel.bottomAnchor.constraint(equalTo: containerView.topAnchor, constant: -4),
            titleLabel.leadingAnchor.constraint(equalTo: containerView.leadingAnchor)
        ])
    }
}
```

这个UIKit示例实现了与Flutter和Jetpack Compose中Expanded组件类似的功能，通过UIStackView和Auto Layout约束系统灵活地处理弹性布局和空间分配。 

## iOS SwiftUI Expanded

SwiftUI没有明确命名的Expanded组件，但提供了等效功能的修饰符：`.frame(minWidth:idealWidth:maxWidth:minHeight:idealHeight:maxHeight:alignment:)`与`.layoutPriority(_:)`。

### 空间分配机制

SwiftUI通过以下机制实现弹性空间分配：

- **基本原理**：使用`frame`修饰符并设置最大宽度/高度为`.infinity`
- **Spacer组件**：专用于占用可用空间的组件
- **计算流程**：
  1. 布局容器计算所有子视图的理想尺寸
  2. 分配固定尺寸给具有明确尺寸要求的视图
  3. 按优先级和权重分配剩余空间

```swift
// 使用frame修饰符实现Expanded效果
HStack {
    // 固定宽度组件
    Rectangle()
        .fill(Color.red)
        .frame(width: 100, height: 50)
    
    // 等效于Expanded组件
    Rectangle()
        .fill(Color.blue)
        .frame(maxWidth: .infinity, height: 50)
}
```

使用Spacer实现两端对齐：

```swift
HStack {
    Text("左侧")
        .background(Color.red)
    
    Spacer() // 占据所有可用空间
    
    Text("右侧")
        .background(Color.blue)
}
```

### 弹性因子控制

SwiftUI通过以下方式控制弹性因子：

- **frame与权重**：使用比例划分空间
- **layoutPriority**：控制视图获取空间的优先级
- **Spacer的minLength**：控制Spacer的最小尺寸

```swift
// 使用权重分配空间
HStack {
    Rectangle()
        .fill(Color.red)
        .frame(maxWidth: .infinity, height: 50)
        .layoutPriority(1) // 1份
    
    Rectangle()
        .fill(Color.blue)
        .frame(maxWidth: .infinity, height: 50)
        .layoutPriority(2) // 2份
    
    Rectangle()
        .fill(Color.green)
        .frame(maxWidth: .infinity, height: 50)
        .layoutPriority(1) // 1份
}
```

或者使用GeometryReader更精确地控制比例：

```swift
HStack(spacing: 0) {
    GeometryReader { geometry in
        HStack(spacing: 0) {
            Rectangle()
                .fill(Color.red)
                .frame(width: geometry.size.width * 0.25) // 1/4宽度
            
            Rectangle()
                .fill(Color.blue)
                .frame(width: geometry.size.width * 0.5) // 1/2宽度
            
            Rectangle()
                .fill(Color.green)
                .frame(width: geometry.size.width * 0.25) // 1/4宽度
        }
    }
}
```

### 与非弹性组件交互

SwiftUI中弹性组件和非弹性组件交互规则：

- **优先级规则**：固定尺寸视图优先分配空间
- **布局流程**：非弹性视图获得所需空间后，弹性视图分享剩余空间
- **动态调整**：布局容器大小变化时自动重新计算

```swift
// 弹性和非弹性组件混合
HStack {
    // 固定宽度
    Rectangle()
        .fill(Color.red)
        .frame(width: 80, height: 50)
    
    // 弹性宽度（2/3剩余空间）
    Rectangle()
        .fill(Color.blue)
        .frame(maxWidth: .infinity, height: 50)
        .layoutPriority(2)
    
    // 弹性宽度（1/3剩余空间）
    Rectangle()
        .fill(Color.green)
        .frame(maxWidth: .infinity, height: 50)
        .layoutPriority(1)
    
    // 固定尺寸文本
    Text(" 固定文本 ")
        .background(Color.yellow)
}
```

### 嵌套使用行为

SwiftUI支持复杂的弹性布局嵌套：

- **多层嵌套**：每一层的弹性分配只影响当前层
- **布局传递**：外层容器将计算后的尺寸传递给内层容器
- **局部计算**：每个容器独立执行空间分配计算

```swift
// 嵌套弹性布局
HStack {
    // 左侧视图 - 1/3宽度
    Rectangle()
        .fill(Color.red.opacity(0.7))
        .frame(maxWidth: .infinity, maxHeight: .infinity)
        .layoutPriority(1)
    
    // 右侧嵌套布局 - 2/3宽度
    VStack {
        // 上部视图 - 2/3高度
        Rectangle()
            .fill(Color.blue.opacity(0.7))
            .frame(maxWidth: .infinity, maxHeight: .infinity)
            .layoutPriority(2)
        
        // 下部嵌套布局 - 1/3高度
        HStack {
            // 左下视图 - 1/2宽度
            Rectangle()
                .fill(Color.green.opacity(0.7))
                .frame(maxWidth: .infinity, maxHeight: .infinity)
            
            // 右下视图 - 1/2宽度
            Rectangle()
                .fill(Color.purple.opacity(0.7))
                .frame(maxWidth: .infinity, maxHeight: .infinity)
        }
        .frame(maxHeight: .infinity)
        .layoutPriority(1)
    }
    .frame(maxWidth: .infinity, maxHeight: .infinity)
    .layoutPriority(2)
}
```

### 溢出处理

SwiftUI处理溢出的方式：

- **非裁剪性**：默认情况下，SwiftUI不裁剪溢出内容
- **裁剪控制**：使用`.clipped()`修饰符显式裁剪溢出内容
- **最小尺寸**：通过设置`minWidth`和`minHeight`保证最小空间
- **优先级调整**：溢出时可通过提高优先级调整空间分配

```swift
// 溢出处理示例
HStack {
    // 固定宽度视图
    Rectangle()
        .fill(Color.red)
        .frame(width: 100, height: 50)
    
    // 固定宽度视图
    Rectangle()
        .fill(Color.green)
        .frame(width: 150, height: 50)
    
    // 弹性视图但有最小宽度
    Rectangle()
        .fill(Color.blue)
        .frame(minWidth: 100, maxWidth: .infinity, height: 50)
}
.frame(width: 300) // 容器宽度限制
.clipped() // 裁剪溢出内容
```

### 平台特性与兼容性

SwiftUI的弹性布局特点与限制：

优势与特点：
- **声明式语法**：简洁易读的声明式布局语法
- **自动更新**：状态变化时自动重新计算布局
- **动画支持**：布局变化可以自动添加动画
- **嵌套灵活**：支持复杂的嵌套结构
- **跨平台**：在所有苹果平台（iOS/iPadOS/macOS/watchOS/tvOS）统一表现
- **可组合性**：修饰符可以任意组合使用
- **优先级精确控制**：通过layoutPriority精确控制空间分配
- **预览支持**：SwiftUI预览功能方便调试布局
- **小部件支持**：可用于iOS小部件布局
- **无障碍支持**：与系统无障碍功能无缝集成

限制与注意事项：
- **仅限苹果平台**：不支持非苹果系统
- **较新技术**：在旧版iOS上不可用
- **性能考量**：复杂嵌套布局可能影响性能
- **布局理解**：需要理解SwiftUI的布局系统
- **学习曲线**：与UIKit相比概念有差异
- **调试工具有限**：布局问题调试工具还不够完善
- **嵌套深度**：过深的嵌套可能影响可读性和性能

### 完整示例代码

```swift
import SwiftUI

struct ExpandedDemo: View {
    var body: some View {
        VStack(spacing: 20) {
            // 基本用法示例
            VStack(alignment: .leading) {
                Text("基本用法")
                    .font(.headline)
                
                HStack {
                    Rectangle()
                        .fill(Color.red)
                        .frame(width: 80, height: 50)
                    
                    Rectangle()
                        .fill(Color.blue)
                        .frame(maxWidth: .infinity, height: 50)
                    
                    Rectangle()
                        .fill(Color.green)
                        .frame(width: 80, height: 50)
                }
                .background(Color.gray.opacity(0.2))
                .cornerRadius(8)
            }
            
            // 弹性因子控制示例
            VStack(alignment: .leading) {
                Text("弹性因子控制 (1:2:1)")
                    .font(.headline)
                
                HStack {
                    Rectangle()
                        .fill(Color.red)
                        .frame(maxWidth: .infinity, height: 50)
                        .layoutPriority(1)
                    
                    Rectangle()
                        .fill(Color.blue)
                        .frame(maxWidth: .infinity, height: 50)
                        .layoutPriority(2)
                    
                    Rectangle()
                        .fill(Color.green)
                        .frame(maxWidth: .infinity, height: 50)
                        .layoutPriority(1)
                }
                .background(Color.gray.opacity(0.2))
                .cornerRadius(8)
            }
            
            // 与非弹性组件交互示例
            VStack(alignment: .leading) {
                Text("与非弹性组件交互")
                    .font(.headline)
                
                HStack {
                    Rectangle()
                        .fill(Color.red)
                        .frame(width: 80, height: 50)
                    
                    Rectangle()
                        .fill(Color.blue)
                        .frame(maxWidth: .infinity, height: 50)
                        .layoutPriority(2)
                    
                    Rectangle()
                        .fill(Color.green)
                        .frame(maxWidth: .infinity, height: 50)
                        .layoutPriority(1)
                    
                    Text(" 固定文本 ")
                        .background(Color.yellow)
                }
                .background(Color.gray.opacity(0.2))
                .cornerRadius(8)
            }
            
            // 使用Spacer示例
            VStack(alignment: .leading) {
                Text("使用Spacer")
                    .font(.headline)
                
                HStack {
                    Text("左侧")
                        .padding(.horizontal, 8)
                        .background(Color.red)
                    
                    Spacer() // 等效于Expanded
                    
                    Text("右侧")
                        .padding(.horizontal, 8)
                        .background(Color.blue)
                }
                .padding(.horizontal)
                .background(Color.gray.opacity(0.2))
                .cornerRadius(8)
                .frame(height: 50)
            }
            
            // 嵌套布局示例
            VStack(alignment: .leading) {
                Text("嵌套布局")
                    .font(.headline)
                
                // 占用剩余空间
                HStack(spacing: 8) {
                    // 左侧视图 - 1/3宽度
                    Rectangle()
                        .fill(Color(red: 1, green: 0.8, blue: 0.8))
                        .frame(maxWidth: .infinity, maxHeight: .infinity)
                        .layoutPriority(1)
                    
                    // 右侧嵌套布局 - 2/3宽度
                    VStack(spacing: 8) {
                        // 上部视图 - 2/3高度
                        Rectangle()
                            .fill(Color(red: 0.8, green: 0.9, blue: 1))
                            .frame(maxWidth: .infinity, maxHeight: .infinity)
                            .layoutPriority(2)
                        
                        // 下部嵌套布局 - 1/3高度
                        HStack(spacing: 8) {
                            // 左下视图 - 1/2宽度
                            Rectangle()
                                .fill(Color(red: 0.8, green: 1, blue: 0.8))
                                .frame(maxWidth: .infinity, maxHeight: .infinity)
                            
                            // 右下视图 - 1/2宽度
                            Rectangle()
                                .fill(Color(red: 0.9, green: 0.8, blue: 1))
                                .frame(maxWidth: .infinity, maxHeight: .infinity)
                        }
                        .frame(maxHeight: .infinity)
                        .layoutPriority(1)
                    }
                    .frame(maxWidth: .infinity, maxHeight: .infinity)
                    .layoutPriority(2)
                }
                .background(Color.gray.opacity(0.2))
                .cornerRadius(8)
                .frame(height: 200)
            }
        }
        .padding()
        .navigationTitle("SwiftUI弹性布局")
    }
}

struct ExpandedDemo_Previews: PreviewProvider {
    static var previews: some View {
        ExpandedDemo()
    }
}
```

这个SwiftUI示例展示了如何使用frame修饰符、layoutPriority和Spacer实现与其他框架中Expanded组件等效的功能，包括基本空间分配、比例控制、与非弹性组件混合以及复杂的嵌套结构。

## 鸿蒙 ArkUI Expanded

鸿蒙ArkUI中使用Flex组件的`flexGrow`和`flexShrink`属性实现类似Expanded的功能，配合`weight`属性可以控制空间分配比例。

### 空间分配机制

ArkUI通过以下机制实现弹性空间分配：

- **Flex容器**：通过Row、Column等弹性容器组件管理布局
- **flexGrow属性**：控制组件扩展能力，默认值为0（不扩展）
- **weight属性**：用于设置空间分配比例，类似弹性因子
- **计算流程**：
  1. 计算所有子组件的最小尺寸需求
  2. 分配必要空间给非伸缩组件
  3. 按weight比例分配剩余空间给设置了flexGrow的组件

```typescript
// 基本用法 - 在Row中实现Expanded效果
Row() {
  // 固定宽度组件
  Text('固定宽度')
    .width(100)
    .height(50)
    .backgroundColor(Color.Red)
  
  // 等效于Expanded组件
  Text('弹性宽度')
    .height(50)
    .backgroundColor(Color.Blue)
    .flexGrow(1) // 分配剩余空间
}
.width('100%')
```

### 弹性因子控制

ArkUI通过以下方式控制弹性因子：

- **weight属性**：设置分配剩余空间的比例
- **flexBasis属性**：设置组件基准尺寸
- **flexShrink属性**：控制组件收缩能力

```typescript
// 使用weight控制弹性比例
Row() {
  // 1份弹性空间
  Text('1份空间')
    .height(50)
    .backgroundColor(Color.Red)
    .flexGrow(1)
    .weight(1) // 1份比重
  
  // 2份弹性空间
  Text('2份空间')
    .height(50)
    .backgroundColor(Color.Blue)
    .flexGrow(1)
    .weight(2) // 2份比重
  
  // 1份弹性空间
  Text('1份空间')
    .height(50)
    .backgroundColor(Color.Green)
    .flexGrow(1)
    .weight(1) // 1份比重
}
.width('100%')
```

### 与非弹性组件交互

ArkUI中弹性与非弹性组件交互规则：

- **优先级**：非弹性组件优先获得所需空间
- **空间分配**：剩余空间按比例分配给弹性组件
- **混合布局**：可以在同一容器中混合使用弹性和非弹性组件

```typescript
// 弹性与非弹性组件混合
Row() {
  // 固定宽度
  Text('固定')
    .width(80)
    .height(50)
    .backgroundColor(Color.Red)
  
  // 弹性宽度(2份)
  Text('弹性2')
    .height(50)
    .backgroundColor(Color.Blue)
    .flexGrow(1)
    .weight(2)
  
  // 弹性宽度(1份)
  Text('弹性1')
    .height(50)
    .backgroundColor(Color.Green)
    .flexGrow(1)
    .weight(1)
  
  // 自然宽度
  Text('固定文本')
    .height(50)
    .backgroundColor(Color.Yellow)
    .padding({left: 10, right: 10})
}
.width('100%')
```

### 嵌套使用行为

ArkUI支持复杂的弹性布局嵌套：

- **多层嵌套**：每一层的弹性分配只影响直接父容器
- **局部计算**：每个Flex容器独立计算其子元素的空间分配

```typescript
// 嵌套布局示例
Row() {
  // 左侧视图 - 1/3空间
  Column()
    .width('100%')
    .height('100%')
    .backgroundColor('#FFCDD2') // 浅红色
    .flexGrow(1)
    .weight(1)
  
  // 右侧嵌套容器 - 2/3空间
  Column() {
    // 上部视图 - 2/3高度
    Text('上部')
      .width('100%')
      .backgroundColor('#BBDEFB') // 浅蓝色
      .flexGrow(1)
      .weight(2)
    
    // 下部视图 - 1/3高度
    Row() {
      // 左下视图 - 1/2宽度
      Text('左下')
        .height('100%')
        .backgroundColor('#C8E6C9') // 浅绿色
        .flexGrow(1)
      
      // 右下视图 - 1/2宽度
      Text('右下')
        .height('100%')
        .backgroundColor('#E1BEE7') // 浅紫色
        .flexGrow(1)
    }
    .width('100%')
    .height('100%')
    .flexGrow(1)
    .weight(1)
  }
  .width('100%')
  .height('100%')
  .flexGrow(1)
  .weight(2)
}
.width('100%')
.height(300)
```

### 溢出处理

ArkUI处理溢出的方式：

- **默认行为**：超出部分会被裁剪
- **clip属性**：控制是否裁剪溢出内容
- **滚动容器**：嵌套在Scroll容器中支持滚动
- **处理策略**：当空间不足时，优先满足非弹性组件

```typescript
// 溢出处理示例
Row() {
  // 固定宽度组件1
  Text('固定1')
    .width(100)
    .height(50)
    .backgroundColor(Color.Red)
  
  // 固定宽度组件2
  Text('固定2')
    .width(150)
    .height(50)
    .backgroundColor(Color.Green)
  
  // 弹性组件(有最小宽度)
  Text('弹性最小100')
    .minWidth(100) // 最小宽度
    .height(50)
    .backgroundColor(Color.Blue)
    .flexGrow(1)
}
.width(300) // 容器宽度有限
.clip(true) // 裁剪溢出内容
```

### 平台特性与兼容性

鸿蒙ArkUI的弹性布局特点与限制：

优势与特点：
- **原生支持**：为鸿蒙平台专门优化
- **声明式API**：简洁易读的声明式布局语法
- **类Web标准**：与CSS Flexbox概念相似，降低学习曲线
- **性能优化**：底层实现针对鸿蒙设备优化
- **多设备适配**：支持手机、平板、可穿戴设备等多种形态
- **动画集成**：支持布局变化动画
- **精确控制**：提供多种属性精细控制弹性布局
- **开发工具支持**：DevEco Studio提供可视化布局预览
- **高效状态管理**：与鸿蒙UI状态管理系统集成

限制与注意事项：
- **仅支持鸿蒙**：仅适用于HarmonyOS平台
- **版本兼容性**：不同版本API可能有差异
- **生态局限**：第三方组件库相对其他框架较少
- **调试复杂性**：嵌套Flex布局的调试可能较复杂
- **文档完善度**：相比其他成熟框架文档资源较少
- **技术成熟度**：作为较新框架仍在快速发展中

### 完整示例代码

```typescript
@Entry
@Component
struct ExpandedDemo {
  build() {
    Column({ space: 20 }) {
      // 基本用法示例
      Column({ space: 8 }) {
        Text('基本用法')
          .fontSize(16)
          .fontWeight(FontWeight.Bold)
        
        Row() {
          Text('')
            .width(80)
            .height(50)
            .backgroundColor(Color.Red)
          
          Text('')
            .height(50)
            .backgroundColor(Color.Blue)
            .flexGrow(1)
          
          Text('')
            .width(80)
            .height(50)
            .backgroundColor(Color.Green)
        }
        .width('100%')
        .backgroundColor('#f5f5f5')
        .borderRadius(8)
        .padding(8)
      }
      .alignItems(HorizontalAlign.Start)
      
      // 弹性因子控制示例
      Column({ space: 8 }) {
        Text('弹性因子控制 (1:2:1)')
          .fontSize(16)
          .fontWeight(FontWeight.Bold)
        
        Row() {
          Text('')
            .height(50)
            .backgroundColor(Color.Red)
            .flexGrow(1)
            .weight(1)
          
          Text('')
            .height(50)
            .backgroundColor(Color.Blue)
            .flexGrow(1)
            .weight(2)
          
          Text('')
            .height(50)
            .backgroundColor(Color.Green)
            .flexGrow(1)
            .weight(1)
        }
        .width('100%')
        .backgroundColor('#f5f5f5')
        .borderRadius(8)
        .padding(8)
      }
      .alignItems(HorizontalAlign.Start)
      
      // 与非弹性组件交互示例
      Column({ space: 8 }) {
        Text('与非弹性组件交互')
          .fontSize(16)
          .fontWeight(FontWeight.Bold)
        
        Row() {
          Text('')
            .width(80)
            .height(50)
            .backgroundColor(Color.Red)
          
          Text('')
            .height(50)
            .backgroundColor(Color.Blue)
            .flexGrow(1)
            .weight(2)
          
          Text('')
            .height(50)
            .backgroundColor(Color.Green)
            .flexGrow(1)
            .weight(1)
          
          Text(' 固定文本 ')
            .height(50)
            .backgroundColor(Color.Yellow)
            .padding({left: 8, right: 8})
        }
        .width('100%')
        .backgroundColor('#f5f5f5')
        .borderRadius(8)
        .padding(8)
      }
      .alignItems(HorizontalAlign.Start)
      
      // 嵌套布局示例
      Column({ space: 8 }) {
        Text('嵌套布局')
          .fontSize(16)
          .fontWeight(FontWeight.Bold)
        
        Row() {
          // 左侧视图 - 1/3宽度
          Column()
            .width('100%')
            .height('100%')
            .backgroundColor('#FFCDD2') // 浅红色
            .flexGrow(1)
            .weight(1)
          
          // 右侧嵌套布局 - 2/3宽度
          Column() {
            // 上部视图 - 2/3高度
            Column()
              .width('100%')
              .height('100%')
              .backgroundColor('#BBDEFB') // 浅蓝色
              .flexGrow(1)
              .weight(2)
            
            // 下部嵌套布局 - 1/3高度
            Row() {
              // 左下视图 - 1/2宽度
              Column()
                .width('100%')
                .height('100%')
                .backgroundColor('#C8E6C9') // 浅绿色
                .flexGrow(1)
              
              // 右下视图 - 1/2宽度
              Column()
                .width('100%')
                .height('100%')
                .backgroundColor('#E1BEE7') // 浅紫色
                .flexGrow(1)
            }
            .width('100%')
            .height('100%')
            .flexGrow(1)
            .weight(1)
          }
          .width('100%')
          .height('100%')
          .flexGrow(1)
          .weight(2)
        }
        .width('100%')
        .height(200)
        .backgroundColor('#f5f5f5')
        .borderRadius(8)
        .padding(8)
      }
      .alignItems(HorizontalAlign.Start)
    }
    .width('100%')
    .padding(16)
  }
}
```

这个ArkUI示例展示了如何使用flexGrow和weight属性实现与其他框架中Expanded组件等效的功能，包括基本空间分配、比例控制、与非弹性组件混合以及复杂的嵌套结构。

## Vue Expanded

Vue本身没有内置的Expanded组件，但可通过CSS Flexbox或Grid实现同样的功能，特别是使用`flex-grow`属性来实现空间分配。

### 空间分配机制

Vue中实现Expanded功能的方式：

- **CSS Flexbox**：通过设置`display: flex`和`flex-grow`属性
- **Grid布局**：使用`grid-template-columns: auto 1fr auto`等
- **计算流程**：
  1. 布局容器计算所有子元素的最小尺寸需求
  2. 分配必要空间给固定尺寸元素
  3. 按照flex-grow值比例分配剩余空间

```vue
<template>
  <!-- 基本用法 -->
  <div class="flex-container">
    <!-- 固定宽度元素 -->
    <div class="fixed-width"></div>
    
    <!-- 相当于Expanded组件 -->
    <div class="expanded"></div>
  </div>
</template>

<style scoped>
.flex-container {
  display: flex;
  width: 100%;
  height: 50px;
}

.fixed-width {
  width: 100px;
  background-color: red;
}

.expanded {
  flex-grow: 1; /* 分配剩余所有空间 */
  background-color: blue;
}
</style>
```

### 弹性因子控制

Vue通过以下方式控制弹性因子：

- **flex-grow**：控制元素获得空间的比例
- **flex-basis**：设置元素的基础尺寸
- **flex简写**：`flex: <grow> <shrink> <basis>`

```vue
<template>
  <div class="flex-container">
    <!-- 1份空间 -->
    <div class="flex-item flex-1"></div>
    
    <!-- 2份空间 -->
    <div class="flex-item flex-2"></div>
    
    <!-- 1份空间 -->
    <div class="flex-item flex-1"></div>
  </div>
</template>

<style scoped>
.flex-container {
  display: flex;
  width: 100%;
  height: 50px;
}

.flex-item {
  height: 100%;
}

.flex-1 {
  flex-grow: 1;
  background-color: red;
}

.flex-2 {
  flex-grow: 2; /* 获得2倍空间 */
  background-color: blue;
}
</style>
```

### 与非弹性组件交互

Vue中弹性与非弹性组件交互规则：

- **优先级**：固定尺寸元素优先获得所需空间
- **剩余空间**：按flex-grow比例分配剩余空间
- **自适应内容**：无flex-grow的元素会基于内容确定尺寸

```vue
<template>
  <div class="flex-container">
    <!-- 固定宽度 -->
    <div class="fixed"></div>
    
    <!-- 弹性空间(2份) -->
    <div class="expanded flex-2"></div>
    
    <!-- 弹性空间(1份) -->
    <div class="expanded flex-1"></div>
    
    <!-- 自适应内容宽度 -->
    <div class="auto-width">固定文本</div>
  </div>
</template>

<style scoped>
.flex-container {
  display: flex;
  width: 100%;
  height: 50px;
}

.fixed {
  width: 80px;
  background-color: red;
}

.expanded {
  height: 100%;
}

.flex-2 {
  flex-grow: 2;
  background-color: blue;
}

.flex-1 {
  flex-grow: 1;
  background-color: green;
}

.auto-width {
  padding: 0 10px;
  display: flex;
  align-items: center;
  background-color: yellow;
}
</style>
```

### 嵌套使用行为

Vue中支持复杂的弹性布局嵌套：

- **多层嵌套**：嵌套flex容器独立计算各自的空间分配
- **布局隔离**：每个flex容器形成独立的布局上下文
- **灵活嵌套**：可以混合行/列布局，构建复杂界面

```vue
<template>
  <div class="outer-container">
    <!-- 左侧区域 - 1/3宽度 -->
    <div class="left-area"></div>
    
    <!-- 右侧嵌套布局 - 2/3宽度 -->
    <div class="right-area">
      <!-- 上部区域 - 2/3高度 -->
      <div class="top-area"></div>
      
      <!-- 下部嵌套布局 - 1/3高度 -->
      <div class="bottom-container">
        <!-- 左下区域 - 1/2宽度 -->
        <div class="bottom-left"></div>
        
        <!-- 右下区域 - 1/2宽度 -->
        <div class="bottom-right"></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.outer-container {
  display: flex;
  width: 100%;
  height: 200px;
}

.left-area {
  flex: 1; /* 1份空间 */
  background-color: rgba(255, 99, 71, 0.7); /* 半透明红色 */
}

.right-area {
  flex: 2; /* 2份空间 */
  display: flex;
  flex-direction: column;
}

.top-area {
  flex: 2; /* 2份高度 */
  background-color: rgba(100, 149, 237, 0.7); /* 半透明蓝色 */
}

.bottom-container {
  flex: 1; /* 1份高度 */
  display: flex;
}

.bottom-left {
  flex: 1; /* 1份宽度 */
  background-color: rgba(144, 238, 144, 0.7); /* 半透明绿色 */
}

.bottom-right {
  flex: 1; /* 1份宽度 */
  background-color: rgba(218, 112, 214, 0.7); /* 半透明紫色 */
}
</style>
```

### 溢出处理

Vue处理溢出的方式：

- **overflow属性**：控制内容溢出时的表现
- **min-width/height**：确保元素的最小尺寸
- **flex-shrink**：控制元素收缩比例
- **媒体查询**：在不同尺寸下调整布局

```vue
<template>
  <div class="container">
    <!-- 固定宽度元素 -->
    <div class="fixed-1"></div>
    
    <!-- 另一个固定宽度元素 -->
    <div class="fixed-2"></div>
    
    <!-- 弹性元素(有最小宽度) -->
    <div class="flexible"></div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  width: 300px; /* 有限宽度 */
  height: 50px;
  overflow: hidden; /* 裁剪溢出内容 */
}

.fixed-1 {
  width: 100px;
  background-color: red;
  flex-shrink: 0; /* 不收缩 */
}

.fixed-2 {
  width: 150px;
  background-color: green;
  flex-shrink: 0; /* 不收缩 */
}

.flexible {
  min-width: 100px; /* 最小宽度 */
  flex-grow: 1; 
  flex-shrink: 1; /* 允许收缩 */
  background-color: blue;
}
</style>
```

### 平台特性与兼容性

Vue使用CSS实现弹性布局的特点与限制：

优势与特点：
- **Web标准**：基于标准CSS Flexbox，熟悉度高
- **广泛兼容**：现代浏览器普遍支持
- **轻量实现**：无需额外组件，直接使用CSS
- **样式分离**：结构与样式分离，符合前端最佳实践
- **响应式支持**：结合媒体查询实现响应式布局
- **过渡动画**：可配合Vue transition实现布局变化动画
- **生态成熟**：配合各种CSS框架使用（Tailwind、Bootstrap等）
- **调试工具**：浏览器开发工具提供完善调试支持
- **选择灵活**：可选择Flexbox或Grid实现相似效果

限制与注意事项：
- **兼容性考虑**：旧浏览器可能需要前缀或备选方案
- **浏览器差异**：不同浏览器Flexbox实现可能有细微差异
- **性能考量**：复杂嵌套flex布局可能影响渲染性能
- **调试复杂性**：多层嵌套时布局问题定位较困难
- **组件封装**：缺乏原生组件封装，需自行实现
- **细节控制**：某些高级布局可能需要附加JavaScript逻辑

### 完整示例代码

```vue
<template>
  <div class="expanded-demo">
    <h1>Vue弹性布局演示</h1>
    
    <!-- 基本用法示例 -->
    <div class="section">
      <h2>基本用法</h2>
      <div class="demo-container basic-demo">
        <div class="fixed-box"></div>
        <div class="expanded-box"></div>
        <div class="fixed-box"></div>
      </div>
    </div>
    
    <!-- 弹性因子控制示例 -->
    <div class="section">
      <h2>弹性因子控制 (1:2:1)</h2>
      <div class="demo-container flex-ratio-demo">
        <div class="flex-box flex-1"></div>
        <div class="flex-box flex-2"></div>
        <div class="flex-box flex-1"></div>
      </div>
    </div>
    
    <!-- 与非弹性组件交互示例 -->
    <div class="section">
      <h2>与非弹性组件交互</h2>
      <div class="demo-container mixed-demo">
        <div class="fixed-box"></div>
        <div class="expanded-box flex-2"></div>
        <div class="expanded-box flex-1"></div>
        <div class="text-box">固定文本</div>
      </div>
    </div>
    
    <!-- 嵌套布局示例 -->
    <div class="section">
      <h2>嵌套布局</h2>
      <div class="demo-container nested-demo">
        <div class="left-panel"></div>
        <div class="right-panel">
          <div class="top-area"></div>
          <div class="bottom-area">
            <div class="bottom-left"></div>
            <div class="bottom-right"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ExpandedDemo'
}
</script>

<style scoped>
.expanded-demo {
  font-family: Arial, sans-serif;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

h1 {
  font-size: 24px;
  margin-bottom: 20px;
}

.section {
  margin-bottom: 30px;
}

h2 {
  font-size: 18px;
  margin-bottom: 10px;
}

.demo-container {
  background-color: #f5f5f5;
  border-radius: 8px;
  overflow: hidden;
}

/* 基本示例样式 */
.basic-demo {
  display: flex;
  height: 50px;
}

.fixed-box {
  width: 80px;
  background-color: #f44336; /* 红色 */
}

.expanded-box {
  flex-grow: 1;
  background-color: #2196f3; /* 蓝色 */
}

/* 弹性因子示例样式 */
.flex-ratio-demo {
  display: flex;
  height: 50px;
}

.flex-box {
  height: 100%;
}

.flex-1 {
  flex-grow: 1;
  background-color: #f44336; /* 红色 */
}

.flex-2 {
  flex-grow: 2;
  background-color: #2196f3; /* 蓝色 */
}

/* 混合内容示例样式 */
.mixed-demo {
  display: flex;
  height: 50px;
}

.text-box {
  padding: 0 10px;
  display: flex;
  align-items: center;
  background-color: #ffeb3b; /* 黄色 */
}

/* 嵌套布局示例样式 */
.nested-demo {
  display: flex;
  height: 200px;
}

.left-panel {
  flex: 1;
  background-color: rgba(244, 67, 54, 0.7); /* 半透明红色 */
}

.right-panel {
  flex: 2;
  display: flex;
  flex-direction: column;
}

.top-area {
  flex: 2;
  background-color: rgba(33, 150, 243, 0.7); /* 半透明蓝色 */
}

.bottom-area {
  flex: 1;
  display: flex;
}

.bottom-left {
  flex: 1;
  background-color: rgba(76, 175, 80, 0.7); /* 半透明绿色 */
}

.bottom-right {
  flex: 1;
  background-color: rgba(156, 39, 176, 0.7); /* 半透明紫色 */
}
</style>
```

此示例展示了Vue中如何使用CSS Flexbox实现与其他框架Expanded组件相同的功能，包括基本空间分配、比例控制、与非弹性组件混合以及复杂的嵌套结构。Vue的优势在于直接使用Web标准技术，无需额外组件，同时保持了灵活性和跨平台兼容性。

## 总结对比

通过对比Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue六大框架中Expanded组件的实现和特性，我们可以总结以下要点：

### 空间分配机制

| 框架 | 实现方式 | 关键属性/组件 | 计算模型 |
|------|---------|-------------|---------|
| Flutter | Expanded封装Flexible | flex | 先分配非弹性，再按flex分配剩余空间 |
| Jetpack Compose | weight修饰符 | weight, fill | 先测量非weight元素，再按weight分配 |
| UIKit | AutoLayout+UIStackView | contentHuggingPriority, StackView.distribution | 优先级系统决定空间分配顺序 |
| SwiftUI | frame修饰符+layoutPriority | maxWidth: .infinity, layoutPriority | 按优先级分配，maxWidth控制扩展 |
| 鸿蒙ArkUI | flexGrow+weight | flexGrow, weight | Flex布局，按weight比例分配 |
| Vue | CSS Flexbox | flex-grow, flex | W3C Flexbox标准，按flex-grow比例分配 |

Flutter和Jetpack Compose提供了专用的组件/修饰符，而UIKit和SwiftUI则通过综合特性实现。鸿蒙ArkUI和Vue都基于类似CSS Flexbox的机制。

### 弹性因子控制

| 框架 | 优势 | 粒度 | 扩展控制 |
|------|------|------|---------|
| Flutter | 简单直观 | 整数值 | 仅正整数flex值 |
| Jetpack Compose | 更灵活，支持fill参数 | 浮点数 | weight + fill参数细粒度控制 |
| UIKit | 多维度控制 | 优先级系统 | 通过多种优先级属性精细控制 |
| SwiftUI | 声明式，支持优先级 | 整数+修饰符 | 扩展性好，可与其他修饰符组合 |
| 鸿蒙ArkUI | 类似CSS的控制方式 | weight+flexBasis | 灵活，可以组合使用多种属性 |
| Vue | 标准CSS控制 | 浮点数 | 完整支持CSS Flexbox所有特性 |

Jetpack Compose、SwiftUI和Vue提供最灵活的弹性因子控制，而UIKit的优先级系统则提供了最精细的弹性控制但代码量较大。

### 与非弹性组件交互

所有框架都遵循相似的规则：优先满足非弹性组件的空间需求，然后再分配剩余空间给弹性组件。但实现方式有差异：

- **Flutter/Compose**：最直观，非Expanded/weight组件优先获取空间
- **UIKit**：通过多级优先级系统精确控制交互关系
- **SwiftUI**：使用layoutPriority控制优先级，结构清晰
- **鸿蒙ArkUI**：类似CSS的模型，未设置flexGrow的组件优先获取空间
- **Vue**：完全遵循CSS Flexbox标准，灵活且可预测

### 嵌套使用行为

| 框架 | 嵌套规则 | 复杂度 | 独立性 |
|------|---------|-------|-------|
| Flutter | 每层独立计算 | 中等 | 完全独立 |
| Jetpack Compose | 每层独立计算 | 中等 | 完全独立 |
| UIKit | 通过约束传递 | 较高 | 相对独立，但可能产生约束交叉影响 |
| SwiftUI | 每层独立计算 | 中等 | 完全独立 |
| 鸿蒙ArkUI | Flex容器独立 | 中等 | 完全独立 |
| Vue | CSS Flex上下文 | 中等 | 符合CSS规范的独立计算 |

所有框架在嵌套使用时都遵循局部计算原则，但UIKit的约束系统在复杂嵌套时可能更难理解和调试。

### 溢出处理

| 框架 | 默认行为 | 控制方式 | 提示机制 |
|------|---------|---------|---------|
| Flutter | 显示溢出警告 | 通过约束控制 | 明显的黄黑条纹警告 |
| Jetpack Compose | 截断/溢出 | 使用Modifier控制 | 开发模式红色虚线 |
| UIKit | 根据clipsToBounds | 压缩阻力优先级 | 约束冲突日志 |
| SwiftUI | 默认不裁剪 | .clipped()修饰符 | 预览可见但无明显标记 |
| 鸿蒙ArkUI | 默认裁剪 | clip属性 | 预览可见 |
| Vue | 依赖overflow | CSS overflow属性 | 浏览器DevTools |

Flutter提供最友好的溢出警告机制，而其他框架则主要依赖于各自的开发工具进行调试。

### 平台与生态

| 框架 | 平台支持 | 开发语言 | 学习曲线 | 生态成熟度 |
|------|---------|---------|---------|-----------|
| Flutter | 跨平台 | Dart | 中等 | 成熟，大量第三方包 |
| Jetpack Compose | Android | Kotlin | 中等 | 快速成长，Google支持 |
| UIKit | iOS/macOS | Swift/Obj-C | 较陡 | 非常成熟，苹果官方 |
| SwiftUI | 苹果平台 | Swift | 中等 | 快速成长，苹果官方 |
| 鸿蒙ArkUI | 鸿蒙OS | TypeScript/ets | 中等 | 新兴，华为支持 |
| Vue | Web平台 | JavaScript | 平缓 | 非常成熟，庞大社区 |

Flutter提供了最广泛的平台支持，而特定平台的框架（如UIKit、SwiftUI、Jetpack Compose、鸿蒙ArkUI）则在各自平台上提供更深度的集成和性能优化。Vue作为Web框架有最大的开发者基础和最平缓的学习曲线。

### 总体推荐

1. **跨平台开发**：Flutter提供最一致的跨平台体验
2. **Android专用**：Jetpack Compose提供现代化声明式API
3. **iOS开发**：
   - 需要兼容旧设备：UIKit
   - 现代iOS应用：SwiftUI
4. **鸿蒙应用**：ArkUI是唯一选择
5. **Web开发**：Vue提供最成熟的弹性布局方案

每个框架都有其独特优势，选择应基于项目需求、目标平台、团队经验和长期维护考虑。大型项目可能会混合使用多种框架，如Web+移动端混合开发。