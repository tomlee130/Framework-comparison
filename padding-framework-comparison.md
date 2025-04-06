# 六大框架 Padding 组件比较

本文档对比Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue六大框架中Padding（内边距）组件的实现和特性，从以下维度进行分析：

- 内边距设置
- 边距计算方式
- 与父容器交互
- 动态边距支持
- 对布局影响

每个框架部分包含平台特性、兼容性分析和完整示例代码。

## 目录

1. [Flutter Padding](#flutter-padding)
2. [Android Jetpack Compose Padding](#android-jetpack-compose-padding)
3. [iOS UIKit NSDirectionalEdgeInsets](#ios-uikit-nsdirectionaledgeinsets)
4. [iOS SwiftUI Padding](#ios-swiftui-padding)
5. [鸿蒙 ArkUI Padding](#鸿蒙-arkui-padding)
6. [Vue Padding](#vue-padding)
7. [总结对比](#总结对比)

## Flutter Padding

Flutter的`Padding`是一个专用于为子组件添加内边距的基础布局组件。它通过`EdgeInsets`类来定义各个方向的内边距。

### 内边距设置

Flutter通过`EdgeInsets`类提供多种设置内边距的方式：

```dart
// 四周均匀内边距
Padding(
  padding: EdgeInsets.all(16.0),
  child: Text('四周均匀内边距'),
)

// 水平和垂直分别设置
Padding(
  padding: EdgeInsets.symmetric(
    horizontal: 16.0, 
    vertical: 8.0
  ),
  child: Text('水平和垂直分别设置'),
)

// 分别设置四个方向
Padding(
  padding: EdgeInsets.fromLTRB(16.0, 8.0, 16.0, 24.0),
  child: Text('左、上、右、下分别设置'),
)

// 使用only方法个别方向设置
Padding(
  padding: EdgeInsets.only(
    left: 16.0,
    bottom: 24.0,
  ),
  child: Text('只设置左侧和底部'),
)
```

`EdgeInsets`还支持多种构造方式组合使用：

```dart
// 组合设置方式
Padding(
  padding: EdgeInsets.only(top: 8.0) + EdgeInsets.symmetric(horizontal: 16.0),
  child: Text('组合设置方式'),
)
```

### 边距计算方式

Flutter的边距计算采用逻辑像素（Logical Pixels）单位，遵循以下规则：

- **单位**: 使用逻辑像素（dp/dip），在不同设备上保持视觉一致性
- **计算**: 边距值会被直接添加到子组件尺寸上
- **总尺寸**: Padding组件总尺寸 = 子组件尺寸 + 四周内边距总和
- **负值处理**: 支持负值内边距，但通常不推荐使用

Flutter根据设备像素比（Device Pixel Ratio）将逻辑像素转换为实际像素：

```dart
// 总宽度 = 100 + 16 + 16 = 132逻辑像素
Padding(
  padding: EdgeInsets.symmetric(horizontal: 16.0),
  child: Container(
    width: 100.0,
    height: 50.0,
    color: Colors.blue,
  ),
)
```

### 与父容器交互

Padding组件在Flutter布局系统中的交互行为：

- **尺寸传递**: 向父组件报告尺寸为：子组件尺寸 + 四周边距
- **约束传递**: 向子组件传递的约束会减小内边距的值
- **溢出处理**: 子组件超出Padding范围不会被裁剪，需配合ClipRect使用
- **层级关系**: 仅影响子组件的位置，不添加额外视觉层级

```dart
// 示范Padding与父容器的交互
Container(
  width: 200.0,
  height: 100.0,
  color: Colors.grey[300],
  child: Padding(
    padding: EdgeInsets.all(16.0),
    child: Container(
      color: Colors.blue,
      // 内部容器会受到Padding的约束，最大宽度为168，最大高度为68
    ),
  ),
)
```

### 动态边距支持

Flutter的Padding支持动态调整内边距：

```dart
// 使用状态管理动态改变内边距
class DynamicPaddingExample extends StatefulWidget {
  @override
  _DynamicPaddingExampleState createState() => _DynamicPaddingExampleState();
}

class _DynamicPaddingExampleState extends State<DynamicPaddingExample> {
  double paddingValue = 16.0;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Padding(
          padding: EdgeInsets.all(paddingValue),
          child: Container(
            color: Colors.blue,
            height: 100.0,
            width: double.infinity,
          ),
        ),
        Slider(
          value: paddingValue,
          min: 0.0,
          max: 32.0,
          onChanged: (value) {
            setState(() {
              paddingValue = value;
            });
          },
        ),
        Text('当前内边距: ${paddingValue.toStringAsFixed(1)}'),
      ],
    );
  }
}
```

Flutter还支持动画过渡的内边距：

```dart
// 动画过渡内边距
class AnimatedPaddingExample extends StatefulWidget {
  @override
  _AnimatedPaddingExampleState createState() => _AnimatedPaddingExampleState();
}

class _AnimatedPaddingExampleState extends State<AnimatedPaddingExample> {
  double paddingValue = 16.0;

  void _togglePadding() {
    setState(() {
      paddingValue = paddingValue == 16.0 ? 32.0 : 16.0;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        AnimatedPadding(
          padding: EdgeInsets.all(paddingValue),
          duration: Duration(milliseconds: 500),
          curve: Curves.easeInOut,
          child: Container(
            color: Colors.blue,
            height: 100.0,
            width: double.infinity,
          ),
        ),
        ElevatedButton(
          onPressed: _togglePadding,
          child: Text('切换内边距'),
        ),
      ],
    );
  }
}
```

### 对布局影响

Padding对Flutter布局的影响：

1. **尺寸增加**: 子组件尺寸 + 内边距 = Padding组件的尺寸
2. **位置偏移**: 子组件会相对于Padding的边界向内偏移
3. **渲染性能**: 作为轻量级布局组件，性能开销极小
4. **嵌套使用**: 可以与其他布局组件自由组合使用
5. **自适应布局**: 支持在自适应和响应式布局中动态调整内边距

```dart
// 示范Padding对布局的影响
Column(
  children: [
    Text('标题文本'),
    Padding(
      padding: EdgeInsets.only(top: 8.0, bottom: 16.0),
      child: Text('内容文本'),
    ),
    Padding(
      padding: EdgeInsets.symmetric(horizontal: 16.0),
      child: ElevatedButton(
        onPressed: () {},
        child: Text('确认按钮'),
      ),
    ),
  ],
)
```

### 平台特性与兼容性

Flutter的Padding组件具有以下特性：

- **跨平台一致性**: 在所有支持的平台上表现一致
- **像素精确渲染**: 严格按照逻辑像素计算并渲染
- **嵌套使用**: 可以嵌套使用多个Padding实现复杂布局
- **方向适配**: 支持TextDirection实现RTL（从右到左）布局
- **高性能实现**: 使用RenderPadding的轻量级渲染对象实现

### 完整示例代码

以下是Flutter中Padding的完整示例代码，展示了各种常见用法：

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Padding Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: PaddingDemoPage(),
    );
  }
}

class PaddingDemoPage extends StatefulWidget {
  @override
  _PaddingDemoPageState createState() => _PaddingDemoPageState();
}

class _PaddingDemoPageState extends State<PaddingDemoPage> {
  double _dynamicPadding = 16.0;
  bool _expandedPadding = false;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Padding 示例'),
      ),
      body: SingleChildScrollView(
        padding: EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            // 1. 基本内边距设置
            sectionTitle('1. 基本内边距设置'),
            
            Container(
              color: Colors.grey[200],
              child: Padding(
                padding: EdgeInsets.all(16.0),
                child: Container(
                  height: 50.0,
                  color: Colors.blue,
                  child: Center(
                    child: Text(
                      '四周均匀内边距',
                      style: TextStyle(color: Colors.white),
                    ),
                  ),
                ),
              ),
            ),
            
            SizedBox(height: 16.0),
            
            Container(
              color: Colors.grey[200],
              child: Padding(
                padding: EdgeInsets.symmetric(horizontal: 16.0, vertical: 8.0),
                child: Container(
                  height: 50.0,
                  color: Colors.green,
                  child: Center(
                    child: Text(
                      '水平和垂直分别设置',
                      style: TextStyle(color: Colors.white),
                    ),
                  ),
                ),
              ),
            ),
            
            SizedBox(height: 16.0),
            
            Container(
              color: Colors.grey[200],
              child: Padding(
                padding: EdgeInsets.fromLTRB(16.0, 8.0, 16.0, 24.0),
                child: Container(
                  height: 50.0,
                  color: Colors.orange,
                  child: Center(
                    child: Text(
                      '左、上、右、下分别设置',
                      style: TextStyle(color: Colors.white),
                    ),
                  ),
                ),
              ),
            ),
            
            SizedBox(height: 16.0),
            
            Container(
              color: Colors.grey[200],
              child: Padding(
                padding: EdgeInsets.only(left: 16.0, bottom: 24.0),
                child: Container(
                  height: 50.0,
                  color: Colors.purple,
                  child: Center(
                    child: Text(
                      '只设置左侧和底部',
                      style: TextStyle(color: Colors.white),
                    ),
                  ),
                ),
              ),
            ),
            
            // 2. 动态内边距
            SizedBox(height: 32.0),
            sectionTitle('2. 动态内边距'),
            
            Container(
              color: Colors.grey[200],
              padding: EdgeInsets.all(8.0),
              child: Column(
                children: [
                  AnimatedPadding(
                    padding: EdgeInsets.all(_dynamicPadding),
                    duration: Duration(milliseconds: 300),
                    curve: Curves.easeInOut,
                    child: Container(
                      height: 100.0,
                      color: Colors.teal,
                      child: Center(
                        child: Text(
                          '动态内边距: ${_dynamicPadding.toStringAsFixed(1)}',
                          style: TextStyle(color: Colors.white),
                        ),
                      ),
                    ),
                  ),
                  
                  SizedBox(height: 16.0),
                  
                  Slider(
                    value: _dynamicPadding,
                    min: 0.0,
                    max: 32.0,
                    divisions: 32,
                    label: _dynamicPadding.toStringAsFixed(1),
                    onChanged: (value) {
                      setState(() {
                        _dynamicPadding = value;
                      });
                    },
                  ),
                  
                  Center(
                    child: ElevatedButton(
                      onPressed: () {
                        setState(() {
                          _expandedPadding = !_expandedPadding;
                          _dynamicPadding = _expandedPadding ? 32.0 : 8.0;
                        });
                      },
                      child: Text(_expandedPadding ? '缩小内边距' : '扩大内边距'),
                    ),
                  ),
                ],
              ),
            ),
            
            // 3. 边距计算示例
            SizedBox(height: 32.0),
            sectionTitle('3. 边距计算示例'),
            
            Container(
              color: Colors.grey[200],
              padding: EdgeInsets.all(8.0),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text('内部容器宽度: 200.0, 高度: 50.0'),
                  Text('内边距: 左右各 16.0, 上下各 8.0'),
                  Text('总宽度 = 200 + 16 + 16 = 232.0'),
                  Text('总高度 = 50 + 8 + 8 = 66.0'),
                  
                  SizedBox(height: 16.0),
                  
                  Container(
                    decoration: BoxDecoration(
                      border: Border.all(color: Colors.black, width: 1.0),
                    ),
                    child: Padding(
                      padding: EdgeInsets.symmetric(horizontal: 16.0, vertical: 8.0),
                      child: Container(
                        width: 200.0,
                        height: 50.0,
                        color: Colors.amber,
                        child: Center(
                          child: Text('内部容器 200×50'),
                        ),
                      ),
                    ),
                  ),
                ],
              ),
            ),
            
            // 4. 组合使用示例
            SizedBox(height: 32.0),
            sectionTitle('4. 组合使用示例'),
            
            Container(
              color: Colors.grey[200],
              child: Padding(
                padding: EdgeInsets.all(16.0),
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    Text(
                      'Padding与其他组件组合',
                      style: TextStyle(
                        fontSize: 18.0,
                        fontWeight: FontWeight.bold,
                      ),
                    ),
                    Padding(
                      padding: EdgeInsets.only(top: 8.0, bottom: 16.0),
                      child: Text(
                        '这里是标题下方的说明文本，与标题和按钮之间有不同的间距。',
                        style: TextStyle(fontSize: 14.0),
                      ),
                    ),
                    Padding(
                      padding: EdgeInsets.only(bottom: 8.0),
                      child: TextField(
                        decoration: InputDecoration(
                          border: OutlineInputBorder(),
                          labelText: '带有内边距的输入框',
                        ),
                      ),
                    ),
                    Padding(
                      padding: EdgeInsets.symmetric(vertical: 16.0),
                      child: ElevatedButton(
                        onPressed: () {},
                        child: Text('确认按钮'),
                      ),
                    ),
                  ],
                ),
              ),
            ),
            
            // 5. 父容器交互示例
            SizedBox(height: 32.0),
            sectionTitle('5. 父容器交互示例'),
            
            Container(
              color: Colors.grey[200],
              padding: EdgeInsets.all(8.0),
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text('固定宽度的父容器: 300.0'),
                  SizedBox(height: 8.0),
                  Container(
                    width: 300.0,
                    color: Colors.grey[300],
                    child: Padding(
                      padding: EdgeInsets.all(16.0),
                      child: Container(
                        height: 50.0,
                        color: Colors.indigo,
                        child: Center(
                          child: Text(
                            'Padding内的子容器',
                            style: TextStyle(color: Colors.white),
                          ),
                        ),
                      ),
                    ),
                  ),
                  SizedBox(height: 16.0),
                  Text('子容器会受到Padding的约束:'),
                  Text('子容器最大宽度 = 父容器宽度 - 左右内边距'),
                  Text('300 - 16 - 16 = 268.0'),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }

  Widget sectionTitle(String title) {
    return Padding(
      padding: EdgeInsets.only(bottom: 8.0),
      child: Text(
        title,
        style: TextStyle(
          fontSize: 18.0,
          fontWeight: FontWeight.bold,
        ),
      ),
    );
  }
}

## Android Jetpack Compose Padding

Jetpack Compose使用修饰符（Modifier）系统实现内边距，而不是单独的组件。通过`Modifier.padding()`修饰符为任何可组合项添加内边距。

### 内边距设置

Jetpack Compose提供多种设置内边距的方式：

```kotlin
// 四周均匀内边距
Box(
    modifier = Modifier
        .padding(16.dp)
        .background(Color.LightGray),
    content = {
        Text("四周均匀内边距")
    }
)

// 水平和垂直分别设置
Box(
    modifier = Modifier
        .padding(horizontal = 16.dp, vertical = 8.dp)
        .background(Color.LightGray),
    content = {
        Text("水平和垂直分别设置")
    }
)

// 分别设置四个方向
Box(
    modifier = Modifier
        .padding(start = 16.dp, top = 8.dp, end = 16.dp, bottom = 24.dp)
        .background(Color.LightGray),
    content = {
        Text("左、上、右、下分别设置")
    }
)

// 使用PaddingValues对象设置
Box(
    modifier = Modifier
        .padding(PaddingValues(start = 16.dp, bottom = 24.dp))
        .background(Color.LightGray),
    content = {
        Text("使用PaddingValues对象")
    }
)
```

修饰符还可以链式组合使用：

```kotlin
// 组合使用多个padding修饰符
Box(
    modifier = Modifier
        .padding(horizontal = 16.dp)
        .background(Color.LightGray)
        .padding(vertical = 8.dp),
    content = {
        Text("组合使用内外边距")
    }
)
```

### 边距计算方式

Jetpack Compose的边距计算采用密度无关像素（dp）单位，遵循以下规则：

- **单位**: 使用dp（密度无关像素），根据屏幕密度自动缩放
- **计算**: 边距值会影响组件布局盒模型中的padding区域
- **总尺寸**: 应用padding后的组件总尺寸 = 内容尺寸 + 四周内边距总和
- **负值处理**: 不支持负值内边距，会导致运行时异常

Compose根据设备密度自动将dp转换为实际像素：

```kotlin
// 内容宽度100dp + 左右各16dp边距 = 总宽度132dp
Box(
    modifier = Modifier
        .padding(horizontal = 16.dp)
        .background(Color.LightGray),
    content = {
        Box(
            modifier = Modifier
                .size(width = 100.dp, height = 50.dp)
                .background(Color.Blue)
        )
    }
)
```

### 与父容器交互

Padding修饰符在Compose布局系统中的交互行为：

- **布局顺序**: 按修饰符链中的顺序应用，影响最终测量结果
- **约束传递**: 内边距会减小传递给子元素的最大约束
- **溢出处理**: 默认情况下，内容不会被裁剪，需要配合`clip()`使用
- **层级影响**: 修饰符应用顺序会影响背景和边框的视觉效果

```kotlin
// 示范Padding与父容器的交互
Box(
    modifier = Modifier
        .size(width = 200.dp, height = 100.dp)
        .background(Color.LightGray),
    content = {
        Box(
            modifier = Modifier
                .padding(16.dp)
                .fillMaxSize()
                .background(Color.Blue),
            // 内部容器会受到Padding约束，最大宽度为168dp，最大高度为68dp
        )
    }
)

// 修饰符顺序影响视觉效果
Box(
    modifier = Modifier
        .background(Color.Red)
        .padding(8.dp) // 背景包含内边距
        .background(Color.Blue),
    content = {
        Text("背景在内边距内")
    }
)

Box(
    modifier = Modifier
        .padding(8.dp) // 内边距在背景外
        .background(Color.Blue),
    content = {
        Text("背景在内边距内")
    }
)
```

### 动态边距支持

Jetpack Compose支持通过状态管理动态改变内边距：

```kotlin
// 使用状态管理动态改变内边距
@Composable
fun DynamicPaddingExample() {
    var paddingValue by remember { mutableStateOf(16.dp) }
    
    Column {
        Box(
            modifier = Modifier
                .padding(paddingValue)
                .background(Color.Blue)
                .size(100.dp)
        )
        
        Slider(
            value = paddingValue.value,
            onValueChange = { paddingValue = it.dp },
            valueRange = 0f..32f
        )
        
        Text("当前内边距: ${paddingValue.value}dp")
    }
}
```

Compose还支持动画过渡的内边距：

```kotlin
// 动画过渡内边距
@Composable
fun AnimatedPaddingExample() {
    var expanded by remember { mutableStateOf(false) }
    val padding by animateDpAsState(
        targetValue = if (expanded) 32.dp else 8.dp,
        animationSpec = spring(dampingRatio = 0.7f)
    )
    
    Column {
        Box(
            modifier = Modifier
                .padding(padding)
                .size(100.dp)
                .background(Color.Blue)
        )
        
        Button(
            onClick = { expanded = !expanded },
            modifier = Modifier.padding(top = 16.dp)
        ) {
            Text(if (expanded) "收缩" else "展开")
        }
    }
}
```

### 对布局影响

Padding修饰符对Compose布局的影响：

1. **尺寸影响**: 内容尺寸 + 内边距 = 应用padding后的尺寸
2. **位置影响**: 内容会相对于padding边界向内偏移
3. **渲染效率**: 作为轻量级修饰符，对性能影响极小
4. **嵌套使用**: 可以在修饰符链中多次应用，内外层叠加效果
5. **自适应布局**: 支持在自适应和响应式布局中动态调整内边距

```kotlin
// 示范Padding对布局的影响
Column(
    modifier = Modifier.padding(16.dp)
) {
    Text("标题文本")
    
    Text(
        text = "内容文本",
        modifier = Modifier.padding(vertical = 8.dp)
    )
    
    Button(
        onClick = { /* 点击处理 */ },
        modifier = Modifier
            .padding(top = 16.dp)
            .fillMaxWidth()
    ) {
        Text("确认按钮")
    }
}
```

### 平台特性与兼容性

Jetpack Compose的Padding修饰符具有以下特性：

- **Android平台专属**: 为Android平台优化，支持所有Android版本
- **密度适配**: 自动适应不同屏幕密度，保持一致的视觉体验
- **主题集成**: 与Material Design主题系统无缝集成
- **方向适配**: 支持LTR和RTL布局，start/end属性自动适配布局方向
- **无障碍支持**: 内边距不影响无障碍功能，与TalkBack兼容
- **高性能实现**: 基于Compose渲染引擎优化，避免不必要的重组

### 完整示例代码

以下是Jetpack Compose中Padding的完整示例代码：

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp

@Composable
fun PaddingDemoScreen() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        // 标题
        Text(
            "Jetpack Compose Padding示例",
            style = MaterialTheme.typography.headlineMedium,
            modifier = Modifier.padding(bottom = 16.dp)
        )
        
        // 1. 基本内边距设置
        SectionTitle("1. 基本内边距设置")
        
        // 四周均匀内边距
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .padding(vertical = 8.dp)
        ) {
            Box(
                modifier = Modifier
                    .background(Color.LightGray)
                    .fillMaxWidth()
            ) {
                Box(
                    modifier = Modifier
                        .padding(16.dp)
                        .height(50.dp)
                        .fillMaxWidth()
                        .background(Color.Blue),
                    contentAlignment = Alignment.Center
                ) {
                    Text(
                        "四周均匀内边距",
                        color = Color.White
                    )
                }
            }
        }
        
        // 水平和垂直分别设置
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .padding(vertical = 8.dp)
        ) {
            Box(
                modifier = Modifier
                    .background(Color.LightGray)
                    .fillMaxWidth()
            ) {
                Box(
                    modifier = Modifier
                        .padding(horizontal = 16.dp, vertical = 8.dp)
                        .height(50.dp)
                        .fillMaxWidth()
                        .background(Color.Green),
                    contentAlignment = Alignment.Center
                ) {
                    Text(
                        "水平和垂直分别设置",
                        color = Color.White
                    )
                }
            }
        }
        
        // 四个方向分别设置
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .padding(vertical = 8.dp)
        ) {
            Box(
                modifier = Modifier
                    .background(Color.LightGray)
                    .fillMaxWidth()
            ) {
                Box(
                    modifier = Modifier
                        .padding(start = 16.dp, top = 8.dp, end = 16.dp, bottom = 24.dp)
                        .height(50.dp)
                        .fillMaxWidth()
                        .background(Color.Red),
                    contentAlignment = Alignment.Center
                ) {
                    Text(
                        "四个方向分别设置",
                        color = Color.White
                    )
                }
            }
        }
        
        // 2. 动态内边距
        SectionTitle("2. 动态内边距")
        
        var paddingValue by remember { mutableStateOf(16f) }
        val animatedPadding by animateDpAsState(targetValue = paddingValue.dp)
        
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .background(Color.LightGray)
                .padding(8.dp)
        ) {
            Box(
                modifier = Modifier
                    .padding(animatedPadding)
                    .height(100.dp)
                    .fillMaxWidth()
                    .background(Color.Cyan),
                contentAlignment = Alignment.Center
            ) {
                Text("动态内边距: ${paddingValue.toInt()}dp")
            }
        }
        
        Slider(
            value = paddingValue,
            onValueChange = { paddingValue = it },
            valueRange = 0f..32f,
            steps = 31,
            modifier = Modifier.padding(top = 16.dp)
        )
        
        Button(
            onClick = { paddingValue = if (paddingValue > 16f) 0f else 32f },
            modifier = Modifier.padding(top = 8.dp)
        ) {
            Text(if (paddingValue > 16f) "减小内边距" else "增大内边距")
        }
        
        // 3. 修饰符顺序示例
        SectionTitle("3. 修饰符顺序示例")
        
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(vertical = 8.dp),
            horizontalArrangement = Arrangement.spacedBy(16.dp)
        ) {
            // 背景在内边距外
            Column(
                modifier = Modifier.weight(1f),
                horizontalAlignment = Alignment.CenterHorizontally
            ) {
                Text("背景在内边距外", modifier = Modifier.padding(bottom = 8.dp))
                Box(
                    modifier = Modifier
                        .size(100.dp)
                        .background(Color.LightGray)
                        .padding(16.dp)
                        .background(Color.Blue),
                    contentAlignment = Alignment.Center
                ) {
                    Box(
                        modifier = Modifier
                            .size(50.dp)
                            .background(Color.White)
                    )
                }
            }
            
            // 背景在内边距内
            Column(
                modifier = Modifier.weight(1f),
                horizontalAlignment = Alignment.CenterHorizontally
            ) {
                Text("背景在内边距内", modifier = Modifier.padding(bottom = 8.dp))
                Box(
                    modifier = Modifier
                        .size(100.dp)
                        .padding(16.dp)
                        .background(Color.Blue),
                    contentAlignment = Alignment.Center
                ) {
                    Box(
                        modifier = Modifier
                            .size(50.dp)
                            .background(Color.White)
                    )
                }
            }
        }
        
        // 4. 边距计算示例
        SectionTitle("4. 边距计算示例")
        
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .background(Color.LightGray)
                .padding(8.dp)
        ) {
            Column {
                Text("内部容器尺寸: 宽度200dp × 高度50dp")
                Text("内边距: 左右各16dp, 上下各8dp")
                Text("总尺寸 = 200 + 16 + 16 = 232dp × 50 + 8 + 8 = 66dp")
                
                Spacer(modifier = Modifier.height(8.dp))
                
                Box(
                    modifier = Modifier
                        .border(1.dp, Color.Black)
                        .padding(horizontal = 16.dp, vertical = 8.dp)
                ) {
                    Box(
                        modifier = Modifier
                            .size(width = 200.dp, height = 50.dp)
                            .background(Color.Yellow),
                        contentAlignment = Alignment.Center
                    ) {
                        Text("内部容器 200×50")
                    }
                }
            }
        }
    }
}

@Composable
private fun SectionTitle(title: String) {
    Text(
        text = title,
        style = MaterialTheme.typography.titleMedium,
        modifier = Modifier.padding(vertical = 16.dp)
    )
}

@Preview(showBackground = true)
@Composable
fun PreviewPaddingDemoScreen() {
    MaterialTheme {
        PaddingDemoScreen()
    }
}
``` 
## Vue Padding

Vue本身没有专门的Padding组件，而是通过CSS的padding属性实现内边距功能，可以使用内联样式、class或style绑定的方式应用内边距。
