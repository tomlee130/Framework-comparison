# 六大框架SizeBox组件比较

本文档对比Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue六大框架中SizeBox（尺寸盒子）组件的实现和特性，从以下维度进行分析：

- 固定尺寸实现
- 最小/最大尺寸约束
- 子组件适配
- 空白区域创建
- 布局中的作用

每个框架部分包含平台特性、兼容性分析和完整实例代码。

## 目录

1. [Flutter SizeBox](#flutter-sizebox)
2. [Android Jetpack Compose Spacer与Box](#android-jetpack-compose-spacer与box)
3. [iOS UIKit UIView与NSLayoutConstraint](#ios-uikit-uiview与nslayoutconstraint)
4. [iOS SwiftUI Spacer与Frame](#ios-swiftui-spacer与frame)
5. [鸿蒙 ArkUI Blank与Box](#鸿蒙-arkui-blank与box)
6. [Vue v-spacer与div](#vue-v-spacer与div)
7. [总结对比](#总结对比)

## Flutter SizeBox

Flutter的`SizedBox`是一个基础布局组件，用于创建固定尺寸的盒子。它是Flutter框架中最简单且常用的布局组件之一。

### 固定尺寸实现

`SizedBox`可以通过以下方式创建固定尺寸的盒子：

```dart
// 固定宽度和高度
SizedBox(
  width: 100.0,
  height: 50.0,
  child: Container(color: Colors.blue),
)

// 仅固定宽度
SizedBox(
  width: 100.0,
  child: Container(color: Colors.red),
)

// 仅固定高度
SizedBox(
  height: 50.0,
  child: Container(color: Colors.green),
)
```

Flutter还提供了`SizedBox.square`构造函数，用于创建等宽等高的正方形盒子：

```dart
// 创建边长为100的正方形
SizedBox.square(
  dimension: 100.0,
  child: Container(color: Colors.yellow),
)
```

### 最小/最大尺寸约束

`SizedBox`本身不直接支持最小/最大尺寸约束，但可以与`ConstrainedBox`组合使用：

```dart
// 最小尺寸约束
ConstrainedBox(
  constraints: BoxConstraints(
    minWidth: 100.0,
    minHeight: 50.0,
  ),
  child: Container(color: Colors.orange),
)

// 最大尺寸约束
ConstrainedBox(
  constraints: BoxConstraints(
    maxWidth: 200.0,
    maxHeight: 100.0,
  ),
  child: Container(color: Colors.purple),
)

// 同时约束最小最大尺寸
ConstrainedBox(
  constraints: BoxConstraints(
    minWidth: 100.0,
    maxWidth: 200.0,
    minHeight: 50.0,
    maxHeight: 100.0,
  ),
  child: Container(color: Colors.teal),
)
```

### 子组件适配

`SizedBox`对子组件的约束方式如下：

- 如果指定了宽度/高度，子组件会被强制调整到指定尺寸
- 当子组件尺寸大于`SizedBox`指定尺寸时，子组件会被裁剪
- 当子组件尺寸小于`SizedBox`指定尺寸时，子组件不会自动拉伸，保持原有尺寸并居中显示

```dart
// 子组件被限制在100×50的区域内
SizedBox(
  width: 100.0,
  height: 50.0,
  child: Text(
    '这是一段很长很长的文本内容，会被SizedBox裁剪',
    style: TextStyle(fontSize: 18.0),
  ),
)

// 子组件小于SizedBox，不会被拉伸
SizedBox(
  width: 200.0,
  height: 100.0,
  child: Text('短文本'),
)
```

### 空白区域创建

`SizedBox`常用于创建固定尺寸的空白区域，相当于其他框架中的spacer：

```dart
// 创建宽度为20的水平空白
SizedBox(width: 20.0)

// 创建高度为16的垂直空白
SizedBox(height: 16.0)

// 创建16×16的空白区域
SizedBox(width: 16.0, height: 16.0)
```

Flutter还提供了预定义的空白尺寸常量：

```dart
// 预定义空白宽度
SizedBox.shrink() // 0×0的空白
SizedBox.expand() // 填满父容器的空白

// Flutter 2.0+提供的简写方式
const SizedBox.square(dimension: 50.0) // 50×50的正方形空白
```

### 布局中的作用

`SizedBox`在Flutter布局中有以下常见用途：

1. **创建间距**：在组件之间添加固定空白
2. **约束子组件尺寸**：限制子组件的宽高
3. **创建占位符**：在UI开发中作为临时占位
4. **强制父容器尺寸**：当父容器需要特定尺寸时使用
5. **控制可扩展组件的尺寸**：例如限制`ListView`高度

```dart
Column(
  children: [
    Text('标题文本'),
    // 垂直间距
    SizedBox(height: 8.0),
    Text('副标题文本'),
    // 较大垂直间距
    SizedBox(height: 16.0),
    // 约束按钮宽度
    SizedBox(
      width: double.infinity, // 宽度撑满
      child: ElevatedButton(
        onPressed: () {},
        child: Text('确认'),
      ),
    ),
  ],
)
```

### 平台特性与兼容性

Flutter的`SizedBox`具有以下特性：

- **跨平台一致性**：在所有支持的平台上表现一致
- **像素精确渲染**：严格按照指定尺寸渲染，不受设备分辨率影响
- **逻辑像素单位**：尺寸单位为逻辑像素，会根据设备像素比自动缩放
- **支持无限尺寸**：可以使用`double.infinity`表示尽可能占据最大空间
- **嵌套使用灵活**：可以与其他布局组件自由组合
- **高性能实现**：作为基础组件，经过高度优化

### 完整示例代码

下面是Flutter中`SizedBox`的完整实例代码，展示了各种常见用法：

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'SizedBox Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: SizedBoxDemoPage(),
    );
  }
}

class SizedBoxDemoPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('SizedBox 示例'),
      ),
      body: SingleChildScrollView(
        padding: EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            // 1. 固定尺寸盒子
            sectionTitle('1. 固定尺寸盒子'),
            Row(
              children: [
                // 固定宽高
                SizedBox(
                  width: 80.0,
                  height: 80.0,
                  child: Container(color: Colors.red),
                ),
                SizedBox(width: 16.0), // 间距
                // 固定宽度
                SizedBox(
                  width: 80.0,
                  child: Container(
                    height: 80.0,
                    color: Colors.green,
                  ),
                ),
                SizedBox(width: 16.0), // 间距
                // 固定高度
                SizedBox(
                  height: 80.0,
                  child: Container(
                    width: 80.0,
                    color: Colors.blue,
                  ),
                ),
              ],
            ),
            
            SizedBox(height: 32.0), // 部分间隔
            
            // 2. 正方形盒子
            sectionTitle('2. 正方形盒子'),
            SizedBox.square(
              dimension: 100.0,
              child: Container(color: Colors.amber),
            ),
            
            SizedBox(height: 32.0),
            
            // 3. 最小/最大尺寸约束
            sectionTitle('3. 尺寸约束（使用ConstrainedBox）'),
            Row(
              children: [
                // 最小尺寸
                Expanded(
                  child: Column(
                    children: [
                      Text('最小尺寸'),
                      SizedBox(height: 8.0),
                      ConstrainedBox(
                        constraints: BoxConstraints(
                          minWidth: 100.0,
                          minHeight: 50.0,
                        ),
                        child: Container(color: Colors.purple),
                      ),
                    ],
                  ),
                ),
                SizedBox(width: 16.0),
                // 最大尺寸
                Expanded(
                  child: Column(
                    children: [
                      Text('最大尺寸'),
                      SizedBox(height: 8.0),
                      ConstrainedBox(
                        constraints: BoxConstraints(
                          maxWidth: 100.0,
                          maxHeight: 50.0,
                        ),
                        child: Container(
                          width: 200.0, // 会被限制在100以内
                          height: 100.0, // 会被限制在50以内
                          color: Colors.orange,
                        ),
                      ),
                    ],
                  ),
                ),
              ],
            ),
            
            SizedBox(height: 32.0),
            
            // 4. 子组件适配
            sectionTitle('4. 子组件适配'),
            // 子组件被裁剪
            Container(
              color: Colors.grey[200],
              child: SizedBox(
                width: 150.0,
                height: 40.0,
                child: Container(
                  color: Colors.lightBlue[100],
                  child: Text(
                    '这是一段很长很长的文本内容，会被SizedBox裁剪',
                    style: TextStyle(fontSize: 18.0),
                  ),
                ),
              ),
            ),
            SizedBox(height: 16.0),
            // 子组件居中
            Container(
              color: Colors.grey[200],
              child: SizedBox(
                width: 150.0,
                height: 60.0,
                child: Container(
                  color: Colors.lightBlue[100],
                  child: Text('短文本'),
                ),
              ),
            ),
            
            SizedBox(height: 32.0),
            
            // 5. 空白区域创建
            sectionTitle('5. 空白区域创建'),
            Container(
              color: Colors.grey[200],
              child: Row(
                children: [
                  Container(width: 50, height: 50, color: Colors.red),
                  SizedBox(width: 20), // 水平间隔
                  Container(width: 50, height: 50, color: Colors.green),
                  SizedBox(width: 20), // 水平间隔
                  Container(width: 50, height: 50, color: Colors.blue),
                ],
              ),
            ),
            SizedBox(height: 16.0),
            Container(
              color: Colors.grey[200],
              child: Column(
                children: [
                  Container(width: 100, height: 30, color: Colors.red),
                  SizedBox(height: 10), // 垂直间隔
                  Container(width: 100, height: 30, color: Colors.green),
                  SizedBox(height: 10), // 垂直间隔
                  Container(width: 100, height: 30, color: Colors.blue),
                ],
              ),
            ),
            
            SizedBox(height: 32.0),
            
            // 6. 布局中的应用
            sectionTitle('6. 布局中的应用'),
            Container(
              color: Colors.grey[200],
              padding: EdgeInsets.all(16.0),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text(
                    '卡片标题',
                    style: TextStyle(
                      fontSize: 18.0,
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                  SizedBox(height: 8.0), // 标题和内容的间距
                  Text('这里是卡片内容，使用SizedBox控制间距。'),
                  SizedBox(height: 16.0), // 内容和按钮的间距
                  // 使用SizedBox限制按钮宽度为全宽
                  SizedBox(
                    width: double.infinity,
                    child: ElevatedButton(
                      onPressed: () {},
                      child: Text('确认'),
                    ),
                  ),
                ],
              ),
            ),
            
            SizedBox(height: 32.0),
            
            // 7. 特殊用法
            sectionTitle('7. 特殊用法'),
            Row(
              children: [
                // SizedBox.shrink() - 创建0×0的空白
                Expanded(
                  child: Column(
                    children: [
                      Text('SizedBox.shrink()'),
                      Container(
                        height: 50,
                        color: Colors.grey[300],
                        child: Center(
                          child: Container(
                            color: Colors.teal,
                            child: SizedBox.shrink(),
                          ),
                        ),
                      ),
                    ],
                  ),
                ),
                SizedBox(width: 16.0),
                // SizedBox.expand() - 填满父容器
                Expanded(
                  child: Column(
                    children: [
                      Text('SizedBox.expand()'),
                      Container(
                        height: 50,
                        color: Colors.grey[300],
                        child: SizedBox.expand(
                          child: Container(
                            color: Colors.teal[200],
                          ),
                        ),
                      ),
                    ],
                  ),
                ),
              ],
            ),
          ],
        ),
      ),
    );
  }

  Widget sectionTitle(String title) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text(
          title,
          style: TextStyle(
            fontSize: 16.0,
            fontWeight: FontWeight.bold,
          ),
        ),
        SizedBox(height: 8.0),
      ],
    );
  }
}

## Android Jetpack Compose Spacer与Box

Jetpack Compose中没有直接对应Flutter `SizedBox`的组件，但可以通过`Spacer`、`Box`和尺寸修饰符组合实现类似功能。

### 固定尺寸实现

Jetpack Compose通过以下方式创建固定尺寸的盒子：

```kotlin
// 固定宽度和高度的Box
Box(
    modifier = Modifier
        .size(width = 100.dp, height = 50.dp)
        .background(Color.Blue)
)

// 仅固定宽度
Box(
    modifier = Modifier
        .width(100.dp)
        .background(Color.Red)
)

// 仅固定高度
Box(
    modifier = Modifier
        .height(50.dp)
        .background(Color.Green)
)

// 创建正方形
Box(
    modifier = Modifier
        .size(100.dp)
        .background(Color.Yellow)
)
```

### 最小/最大尺寸约束

Jetpack Compose提供了直接的尺寸约束修饰符：

```kotlin
// 最小尺寸约束
Box(
    modifier = Modifier
        .sizeIn(
            minWidth = 100.dp,
            minHeight = 50.dp
        )
        .background(Color.Orange)
)

// 最大尺寸约束
Box(
    modifier = Modifier
        .sizeIn(
            maxWidth = 200.dp,
            maxHeight = 100.dp
        )
        .background(Color.Purple)
)

// 同时约束最小最大尺寸
Box(
    modifier = Modifier
        .sizeIn(
            minWidth = 100.dp,
            maxWidth = 200.dp,
            minHeight = 50.dp,
            maxHeight = 100.dp
        )
        .background(Color.Teal)
)

// 也可以单独使用widthIn和heightIn
Box(
    modifier = Modifier
        .widthIn(min = 100.dp, max = 200.dp)
        .heightIn(min = 50.dp, max = 100.dp)
        .background(Color.Cyan)
)
```

### 子组件适配

Compose中的Box对其子组件的适配行为：

```kotlin
// 子组件被限制在固定尺寸内
Box(
    modifier = Modifier
        .size(width = 100.dp, height = 50.dp)
        .background(Color.LightGray)
) {
    Text(
        text = "这是一段很长很长的文本内容，会被Box限制在指定尺寸内",
        fontSize = 18.sp,
        modifier = Modifier.background(Color.LightBlue.copy(alpha = 0.3f))
    )
}

// 使用BoxWithConstraints可以根据父容器约束调整布局
BoxWithConstraints(
    modifier = Modifier
        .size(width = 200.dp, height = 100.dp)
        .background(Color.LightGray)
) {
    // BoxWithConstraints提供maxWidth、maxHeight等约束属性
    if (maxWidth > 150.dp) {
        Text("宽度大于150dp")
    } else {
        Text("宽度小于等于150dp")
    }
}
```

Compose还提供了内容适配修饰符：

```kotlin
// 根据内容调整大小
Box(
    modifier = Modifier
        .wrapContentSize() // 根据内容确定尺寸
        .background(Color.LightGray)
) {
    Text("内容自适应")
}

// 显式指定对齐方式
Box(
    modifier = Modifier
        .size(100.dp)
        .background(Color.LightGray),
    contentAlignment = Alignment.Center // 内容居中对齐
) {
    Text("居中文本")
}
```

### 空白区域创建

Jetpack Compose提供了专用的`Spacer`组件来创建空白区域：

```kotlin
// 水平布局中的固定宽度空白
Row {
    Text("文本1")
    Spacer(modifier = Modifier.width(16.dp)) // 16dp的水平间距
    Text("文本2")
}

// 垂直布局中的固定高度空白
Column {
    Text("标题")
    Spacer(modifier = Modifier.height(8.dp)) // 8dp的垂直间距
    Text("内容")
}

// 创建占据剩余空间的弹性空白
Row {
    Text("左侧")
    Spacer(modifier = Modifier.weight(1f)) // 弹性空白，占据所有可用空间
    Text("右侧")
}
```

也可以使用`Box`创建空白区域：

```kotlin
// 创建16dp × 16dp的空白区域
Box(modifier = Modifier.size(16.dp))

// 只创建水平方向空白
Box(modifier = Modifier.width(16.dp))

// 只创建垂直方向空白
Box(modifier = Modifier.height(16.dp))
```

### 布局中的作用

Jetpack Compose中的尺寸组件在布局中主要用途：

1. **创建间距**：使用`Spacer`在组件之间添加固定空白
2. **控制元素大小**：使用尺寸修饰符限制组件尺寸
3. **创建占位区域**：使用`Box`作为UI布局中的占位
4. **强制容器尺寸**：使用尺寸修饰符固定父容器尺寸
5. **实现弹性布局**：使用`weight`修饰符分配可用空间

```kotlin
Column(
    modifier = Modifier
        .padding(16.dp)
        .background(Color.LightGray)
) {
    Text(
        "标题文本",
        fontSize = 18.sp,
        fontWeight = FontWeight.Bold
    )
    // 垂直间距
    Spacer(modifier = Modifier.height(8.dp))
    Text("副标题文本")
    // 较大垂直间距
    Spacer(modifier = Modifier.height(16.dp))
    // 占满宽度的按钮
    Button(
        onClick = { /* 点击处理 */ },
        modifier = Modifier.fillMaxWidth() // 撑满宽度
    ) {
        Text("确认")
    }
}

// 使用Spacer实现两端对齐
Row(modifier = Modifier.fillMaxWidth()) {
    Text("左侧文本")
    Spacer(Modifier.weight(1f)) // 弹性占位
    Text("右侧文本")
}
```

### 平台特性与兼容性

Jetpack Compose的尺寸组件具有以下特性：

- **Android平台专属**：专为Android平台设计和优化
- **密度无关像素**：使用dp单位，在不同屏幕密度下保持一致视觉大小
- **自适应布局支持**：提供灵活的布局策略，适应不同屏幕尺寸
- **动态尺寸调整**：支持通过State动态改变组件尺寸
- **动画过渡**：支持尺寸变化的动画效果
- **Material Design**：与Material Design规范无缝集成
- **Compose多平台**：可通过Compose Multiplatform扩展到桌面平台

### 完整示例代码

以下是Jetpack Compose中实现类似`SizedBox`功能的完整示例：

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.Composable
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun SizeBoxDemoScreen() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        // 标题
        Text(
            "Jetpack Compose 尺寸盒子示例",
            fontSize = 20.sp,
            fontWeight = FontWeight.Bold,
            modifier = Modifier.padding(bottom = 16.dp)
        )
        
        // 1. 固定尺寸盒子
        SectionTitle("1. 固定尺寸盒子")
        Row(modifier = Modifier.padding(vertical = 8.dp)) {
            // 固定宽高
            Box(
                modifier = Modifier
                    .size(80.dp)
                    .background(Color.Red)
            )
            Spacer(modifier = Modifier.width(16.dp))
            // 固定宽度
            Box(
                modifier = Modifier
                    .width(80.dp)
                    .height(80.dp)
                    .background(Color.Green)
            )
            Spacer(modifier = Modifier.width(16.dp))
            // 固定高度
            Box(
                modifier = Modifier
                    .height(80.dp)
                    .width(80.dp)
                    .background(Color.Blue)
            )
        }
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // 2. 正方形盒子
        SectionTitle("2. 正方形盒子")
        Box(
            modifier = Modifier
                .size(100.dp)
                .background(Color(0xFFFFB300))
        )
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // 3. 尺寸约束
        SectionTitle("3. 尺寸约束")
        Row(
            modifier = Modifier.height(IntrinsicSize.Min)
        ) {
            // 最小尺寸约束
            Column(
                modifier = Modifier.weight(1f),
                horizontalAlignment = Alignment.CenterHorizontally
            ) {
                Text(
                    "最小尺寸",
                    modifier = Modifier.padding(bottom = 8.dp)
                )
                Box(
                    modifier = Modifier
                        .sizeIn(minWidth = 100.dp, minHeight = 50.dp)
                        .background(Color(0xFF9C27B0))
                )
            }
            
            Spacer(modifier = Modifier.width(16.dp))
            
            // 最大尺寸约束
            Column(
                modifier = Modifier.weight(1f),
                horizontalAlignment = Alignment.CenterHorizontally
            ) {
                Text(
                    "最大尺寸",
                    modifier = Modifier.padding(bottom = 8.dp)
                )
                Box(
                    modifier = Modifier
                        .sizeIn(maxWidth = 100.dp, maxHeight = 50.dp)
                        .background(Color(0xFFFF9800))
                        .padding(8.dp)
                ) {
                    // 这个Box会被限制在最大尺寸内
                    Box(
                        modifier = Modifier
                            .size(200.dp) // 尝试使用大尺寸，但会被约束
                            .background(Color(0xFFFFCC80))
                    )
                }
            }
        }
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // 4. 子组件适配
        SectionTitle("4. 子组件适配")
        Box(
            modifier = Modifier
                .background(Color.LightGray)
                .padding(8.dp)
        ) {
            // 子组件限制在固定尺寸内
            Box(
                modifier = Modifier
                    .size(width = 150.dp, height = 40.dp)
                    .background(Color(0xFFE1F5FE))
            ) {
                Text(
                    "这是一段很长很长的文本内容，会被Box限制在指定尺寸内",
                    fontSize = 16.sp
                )
            }
        }
        
        Spacer(modifier = Modifier.height(8.dp))
        
        Box(
            modifier = Modifier
                .background(Color.LightGray)
                .padding(8.dp)
        ) {
            // 居中内容的Box
            Box(
                modifier = Modifier
                    .size(width = 150.dp, height = 60.dp)
                    .background(Color(0xFFE1F5FE)),
                contentAlignment = Alignment.Center
            ) {
                Text("短文本")
            }
        }
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // 5. 空白区域创建
        SectionTitle("5. 空白区域创建")
        Box(
            modifier = Modifier
                .background(Color.LightGray)
                .padding(8.dp)
        ) {
            Row {
                Box(
                    modifier = Modifier
                        .size(50.dp)
                        .background(Color.Red)
                )
                Spacer(modifier = Modifier.width(20.dp)) // 水平间隔
                Box(
                    modifier = Modifier
                        .size(50.dp)
                        .background(Color.Green)
                )
                Spacer(modifier = Modifier.width(20.dp)) // 水平间隔
                Box(
                    modifier = Modifier
                        .size(50.dp)
                        .background(Color.Blue)
                )
            }
        }
        
        Spacer(modifier = Modifier.height(8.dp))
        
        Box(
            modifier = Modifier
                .background(Color.LightGray)
                .padding(8.dp)
        ) {
            Column {
                Box(
                    modifier = Modifier
                        .width(100.dp)
                        .height(30.dp)
                        .background(Color.Red)
                )
                Spacer(modifier = Modifier.height(10.dp)) // 垂直间隔
                Box(
                    modifier = Modifier
                        .width(100.dp)
                        .height(30.dp)
                        .background(Color.Green)
                )
                Spacer(modifier = Modifier.height(10.dp)) // 垂直间隔
                Box(
                    modifier = Modifier
                        .width(100.dp)
                        .height(30.dp)
                        .background(Color.Blue)
                )
            }
        }
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // 6. 布局中的应用
        SectionTitle("6. 布局中的应用")
        Box(
            modifier = Modifier
                .background(Color.LightGray)
                .padding(16.dp)
        ) {
            Column {
                Text(
                    "卡片标题",
                    fontSize = 18.sp,
                    fontWeight = FontWeight.Bold
                )
                Spacer(modifier = Modifier.height(8.dp)) // 标题和内容的间距
                Text("这里是卡片内容，使用Spacer控制间距。")
                Spacer(modifier = Modifier.height(16.dp)) // 内容和按钮的间距
                // 占满宽度的按钮
                Button(
                    onClick = { /* 点击事件 */ },
                    modifier = Modifier.fillMaxWidth()
                ) {
                    Text("确认")
                }
            }
        }
        
        Spacer(modifier = Modifier.height(24.dp))
        
        // 7. 弹性空间分配
        SectionTitle("7. 弹性空间分配")
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .background(Color.LightGray)
                .padding(8.dp)
        ) {
            Text("左侧")
            Spacer(modifier = Modifier.weight(1f)) // 弹性空白，占据所有可用空间
            Text("右侧")
        }
    }
}

@Composable
private fun SectionTitle(title: String) {
    Text(
        text = title,
        fontSize = 16.sp,
        fontWeight = FontWeight.Bold,
        modifier = Modifier.padding(bottom = 8.dp)
    )
}

@Preview(showBackground = true)
@Composable
fun PreviewSizeBoxDemoScreen() {
    MaterialTheme {
        Surface {
            SizeBoxDemoScreen()
        }
    }
} 