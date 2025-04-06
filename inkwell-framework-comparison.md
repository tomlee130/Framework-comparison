# 六大框架 InkWell 组件比较

本文档对比Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue六大框架中InkWell组件的实现和特性，从以下维度进行分析：

- 水波纹效果实现
- 触摸反馈配置
- 事件处理
- 自定义形状支持
- 无障碍考虑

每个框架部分包含平台特性、兼容性分析和完整实例代码。

## 目录

1. [Flutter InkWell](#flutter-inkwell)
2. [Android Jetpack Compose InkWell](#android-jetpack-compose-inkwell)
3. [iOS UIKit InkWell](#ios-uikit-inkwell)
4. [iOS SwiftUI InkWell](#ios-swiftui-inkwell)
5. [鸿蒙 ArkUI InkWell](#鸿蒙-arkui-inkwell)
6. [Vue InkWell](#vue-inkwell)
7. [总结对比](#总结对比)

## Flutter InkWell

### 水波纹效果实现

Flutter的InkWell组件提供了原生Material Design风格的水波纹效果：

- 使用Material组件库中的`InkWell`类实现水波纹效果
- 基于`Ink`和`InkResponse`构建，提供完整的水波纹动画
- 水波纹从触摸点向外扩散，符合Material Design规范
- 支持多种类型的水波纹效果，如圆形和矩形
- 可配置水波纹颜色、高亮颜色和溅出范围
- 水波纹效果的动画曲线和持续时间基于Material设计规范
- 在暗色主题自动调整水波纹效果以提高可见性

### 触摸反馈配置

Flutter的InkWell提供丰富的触摸反馈配置选项：

- 通过`splashColor`属性自定义水波纹颜色
- 使用`highlightColor`属性设置按下时的高亮颜色
- `splashFactory`属性允许自定义水波纹的展开方式
- `radius`属性控制水波纹的半径大小
- `borderRadius`允许设置水波纹效果的边界圆角
- 通过`enableFeedback`属性启用或禁用触觉反馈
- `excludeFromSemantics`属性允许排除语义信息
- 支持设置`onFocusChange`和`onHover`回调实现更丰富的交互体验

### 事件处理

Flutter InkWell组件提供全面的事件处理能力：

- `onTap`回调处理点击事件
- `onDoubleTap`响应双击事件
- `onLongPress`处理长按事件
- `onTapDown`和`onTapUp`分别在按下和抬起时触发
- `onTapCancel`在点击取消时调用
- 支持`onHighlightChanged`监听高亮状态变化
- 提供`onHover`回调处理鼠标悬停事件
- 所有手势事件都有对应的详细手势信息参数

### 自定义形状支持

Flutter InkWell支持多种形状定制：

- 需要与`Material`组件结合使用来定制形状
- 通过`borderRadius`属性设置圆角矩形
- 结合`ClipPath`或`CustomClipper`实现任意复杂形状
- 可与`ShapeBorder`组合实现各种边框形状
- 支持通过`Material`的`shape`属性传递自定义形状
- 可利用`CustomPaint`创建完全自定义的形状
- 形状会影响水波纹的边界和溅出效果

### 无障碍考虑

Flutter InkWell的无障碍特性：

- 默认集成语义标签，供屏幕阅读器使用
- 通过`Semantics`组件可自定义辅助功能行为
- 支持`excludeFromSemantics`属性排除不必要的语义
- 适配高对比度模式，确保水波纹在各种情况下可见
- 支持键盘焦点和导航，实现完整的键盘可访问性
- 与Flutter的语义树集成，提供屏幕阅读器友好的交互
- 遵循Material Design的无障碍指南

### 平台特性与兼容性

Flutter InkWell的平台特性与兼容性：

- 在所有Flutter支持的平台上提供一致的体验（iOS、Android、Web、桌面等）
- 适配不同平台的输入方式，包括触摸屏、鼠标和键盘
- 在iOS上保持Material Design风格，而非采用iOS原生触摸反馈
- 在Web平台上通过CSS动画模拟水波纹效果
- 支持桌面平台的鼠标悬停和焦点状态
- 支持平板和大屏幕设备的各种显示密度
- 在低端设备上会自动优化动画性能

### 完整示例代码

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
      title: 'Flutter InkWell示例',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        useMaterial3: true,
      ),
      home: const InkWellDemo(),
    );
  }
}

class InkWellDemo extends StatefulWidget {
  const InkWellDemo({Key? key}) : super(key: key);

  @override
  State<InkWellDemo> createState() => _InkWellDemoState();
}

class _InkWellDemoState extends State<InkWellDemo> {
  bool _isPressed = false;
  String _lastAction = '无';
  
  void _updateAction(String action) {
    setState(() {
      _lastAction = action;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('InkWell 示例'),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            // 基本的InkWell实现
            Material(
              color: Colors.transparent,
              child: InkWell(
                onTap: () => _updateAction('点击'),
                onDoubleTap: () => _updateAction('双击'),
                onLongPress: () => _updateAction('长按'),
                onTapDown: (_) {
                  setState(() {
                    _isPressed = true;
                  });
                  _updateAction('按下');
                },
                onTapUp: (_) {
                  setState(() {
                    _isPressed = false;
                  });
                },
                onTapCancel: () {
                  setState(() {
                    _isPressed = false;
                  });
                  _updateAction('取消点击');
                },
                onHighlightChanged: (value) {
                  print('高亮状态改变: $value');
                },
                splashColor: Colors.blue.withOpacity(0.5),
                highlightColor: Colors.lightBlue.withOpacity(0.3),
                radius: 100,
                child: Ink(
                  decoration: BoxDecoration(
                    color: Colors.blue.shade100,
                    borderRadius: BorderRadius.circular(10),
                  ),
                  child: Container(
                    width: 200,
                    height: 100,
                    alignment: Alignment.center,
                    child: Text(
                      '点击我!',
                      style: TextStyle(
                        fontSize: 20,
                        color: _isPressed ? Colors.white : Colors.black87,
                        fontWeight: FontWeight.bold,
                      ),
                    ),
                  ),
                ),
              ),
            ),
            
            const SizedBox(height: 20),
            
            // 显示当前状态
            Text('最后动作: $_lastAction', style: const TextStyle(fontSize: 16)),
            
            const SizedBox(height: 40),
            
            // 自定义形状示例
            Material(
              color: Colors.transparent,
              child: InkWell(
                onTap: () => _updateAction('点击自定义形状'),
                splashColor: Colors.green.withOpacity(0.6),
                highlightColor: Colors.lightGreen.withOpacity(0.4),
                customBorder: const StarBorder(),
                child: Ink(
                  decoration: const ShapeDecoration(
                    color: Colors.lightGreen,
                    shape: StarBorder(),
                  ),
                  child: const SizedBox(
                    width: 150,
                    height: 150,
                    child: Center(
                      child: Text(
                        '自定义形状',
                        style: TextStyle(
                          color: Colors.white,
                          fontWeight: FontWeight.bold,
                        ),
                      ),
                    ),
                  ),
                ),
              ),
            ),
            
            const SizedBox(height: 40),
            
            // 圆形InkWell示例
            Material(
              color: Colors.transparent,
              child: InkWell(
                onTap: () => _updateAction('点击圆形'),
                splashColor: Colors.purple.withOpacity(0.6),
                highlightColor: Colors.purpleAccent.withOpacity(0.4),
                customBorder: const CircleBorder(),
                child: Ink(
                  decoration: const BoxDecoration(
                    shape: BoxShape.circle,
                    color: Colors.deepPurple,
                  ),
                  child: const SizedBox(
                    width: 80,
                    height: 80,
                    child: Icon(
                      Icons.touch_app,
                      color: Colors.white,
                      size: 40,
                    ),
                  ),
                ),
              ),
            ),
          ],
        ),
      ),
    );
  }
}

// 自定义星形边框
class StarBorder extends ShapeBorder {
  final BorderSide side;
  
  const StarBorder({this.side = BorderSide.none});
  
  @override
  EdgeInsetsGeometry get dimensions => EdgeInsets.all(side.width);
  
  @override
  Path getInnerPath(Rect rect, {TextDirection? textDirection}) {
    return getOuterPath(rect.deflate(side.width), textDirection: textDirection);
  }
  
  @override
  Path getOuterPath(Rect rect, {TextDirection? textDirection}) {
    final center = rect.center;
    final double radius = rect.shortestSide / 2;
    final double outerRadius = radius;
    final double innerRadius = radius * 0.4;
    
    final path = Path();
    final int pointCount = 5;
    final double rotationOffset = -pi / 2; // 从顶部开始
    
    for (int i = 0; i < pointCount * 2; i++) {
      final double currentRadius = i.isEven ? outerRadius : innerRadius;
      final double angle = i * pi / pointCount + rotationOffset;
      final double x = center.dx + currentRadius * cos(angle);
      final double y = center.dy + currentRadius * sin(angle);
      
      if (i == 0) {
        path.moveTo(x, y);
      } else {
        path.lineTo(x, y);
      }
    }
    
    path.close();
    return path;
  }
  
  @override
  void paint(Canvas canvas, Rect rect, {TextDirection? textDirection}) {
    if (side.width != 0.0 && side.style != BorderStyle.none) {
      final path = getOuterPath(rect, textDirection: textDirection);
      canvas.drawPath(path, side.toPaint());
    }
  }
  
  @override
  ShapeBorder scale(double t) {
    return StarBorder(side: side.scale(t));
  }
}

## Android Jetpack Compose InkWell

### 水波纹效果实现

Jetpack Compose提供了类似Flutter的水波纹效果实现：

- 通过`indication`参数实现水波纹效果，默认使用`rememberRipple()`
- 水波纹从触摸点开始扩散，遵循Material Design设计规范
- 基于Android原生的Ripple效果，具有平台一致性
- 支持无界和有界水波纹效果
- 水波纹动画速度和曲线基于Android系统级动画标准
- 会考虑深色主题，自动调整水波纹颜色和对比度
- 可结合`Modifier.clickable`和自定义Indication实现更多效果

### 触摸反馈配置

Jetpack Compose提供丰富的触摸反馈配置：

- 通过`rememberRipple()`的参数自定义水波纹效果
- `color`参数控制水波纹颜色
- `bounded`参数决定水波纹是否限制在组件边界内
- `radius`参数设置水波纹最大半径
- 结合`interactionSource`可监控交互状态
- 支持自定义`Indication`实现个性化触摸反馈
- 可使用`Modifier.indication`应用自定义触摸指示效果
- 通过`MutableInteractionSource`控制和监听交互状态

### 事件处理

Jetpack Compose的事件处理系统：

- 使用`onClick`参数处理点击事件
- 可通过`onLongClick`处理长按事件
- `onDoubleClick`支持双击交互
- `onPress`处理按下事件
- 提供`onRelease`监听释放事件
- 使用`InteractionSource`可监听交互状态变化
- 支持使用组合函数如`pointerInput`捕获更底层的触摸事件
- 可通过状态管理(如`remember`和`mutableStateOf`)跟踪交互状态

### 自定义形状支持

Jetpack Compose的形状支持：

- 通过`clip`修饰符将组件裁剪为任意形状
- 可使用预定义的`RoundedCornerShape`、`CircleShape`等
- 支持`GenericShape`创建完全自定义的形状
- 形状影响水波纹效果的扩散边界
- 可通过`drawBehind`和`Canvas` API绘制复杂形状
- 支持使用`Border`和`BorderStroke`添加边框
- 可组合多个形状创建复杂效果

### 无障碍考虑

Jetpack Compose的无障碍支持：

- 自动生成无障碍树，与Android原生无障碍框架集成
- 通过`semantics`修饰符自定义无障碍信息
- 支持`contentDescription`提供内容描述
- 可设置`Role`指定组件角色（如按钮）
- 支持与TalkBack和其他无障碍服务集成
- 符合Android无障碍设计规范
- 提供高对比度模式的适配
- 支持键盘导航和焦点管理

### 平台特性与兼容性

Jetpack Compose的平台特性与兼容性：

- 作为Android原生UI工具包，专为Android平台优化
- 支持Android 5.0 (API 21)及以上系统
- 完全集成Android系统级动画和触摸反馈
- 根据系统设置自动适配深色模式
- 遵循Material Design 3设计语言
- 支持自适应布局，适配不同屏幕尺寸
- 充分利用Android系统API提供原生体验
- 与传统View系统可以混合使用

### 完整示例代码

```kotlin
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.shape.GenericShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.RectangleShape
import androidx.compose.ui.graphics.Shape
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.semantics.Role
import androidx.compose.ui.semantics.contentDescription
import androidx.compose.ui.semantics.semantics
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import androidx.compose.material.ripple.rememberRipple
import androidx.compose.ui.geometry.Size
import androidx.compose.ui.graphics.Path
import androidx.compose.ui.graphics.Outline
import androidx.compose.ui.geometry.Rect
import androidx.compose.ui.unit.Density
import androidx.compose.ui.unit.LayoutDirection
import kotlin.math.PI
import kotlin.math.cos
import kotlin.math.sin

class StarShape : Shape {
    override fun createOutline(
        size: Size,
        layoutDirection: LayoutDirection,
        density: Density
    ): Outline {
        val path = Path().apply {
            val center = size.width / 2f to size.height / 2f
            val outerRadius = size.minDimension / 2f
            val innerRadius = outerRadius * 0.4f
            val pointCount = 5
            val angleStep = 2f * PI / (pointCount * 2)
            val rotationAngle = -PI / 2 // 从顶部开始

            for (i in 0 until pointCount * 2) {
                val radius = if (i % 2 == 0) outerRadius else innerRadius
                val angle = rotationAngle + i * angleStep
                val x = center.first + (radius * cos(angle)).toFloat()
                val y = center.second + (radius * sin(angle)).toFloat()

                if (i == 0) {
                    moveTo(x, y)
                } else {
                    lineTo(x, y)
                }
            }
            close()
        }
        return Outline.Generic(path)
    }
}

@Composable
fun InkWellDemo() {
    var lastAction by remember { mutableStateOf("无") }
    var isPressed by remember { mutableStateOf(false) }
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp),
        horizontalAlignment = Alignment.CenterHorizontally,
        verticalArrangement = Arrangement.spacedBy(24.dp)
    ) {
        // 标题
        Text(
            text = "Jetpack Compose InkWell 示例",
            fontWeight = FontWeight.Bold,
            fontSize = 24.sp,
            modifier = Modifier.padding(bottom = 16.dp)
        )

        // 基本的InkWell效果
        Surface(
            modifier = Modifier
                .size(width = 200.dp, height = 100.dp)
                .semantics { contentDescription = "可点击的卡片" },
            shape = RoundedCornerShape(10.dp),
            color = MaterialTheme.colorScheme.primaryContainer,
            border = if (isPressed) BorderStroke(2.dp, MaterialTheme.colorScheme.primary) else null
        ) {
            // Compose中使用Surface+clickable代替InkWell
            Box(
                modifier = Modifier
                    .fillMaxSize()
                    .clickable(
                        interactionSource = remember { MutableInteractionSource() },
                        indication = rememberRipple(
                            color = MaterialTheme.colorScheme.primary,
                            bounded = true
                        ),
                        role = Role.Button,
                        onClick = {
                            lastAction = "点击"
                            isPressed = true
                            // 模拟按下后释放的效果
                            android.os.Handler().postDelayed({ isPressed = false }, 200)
                        }
                    ),
                contentAlignment = Alignment.Center
            ) {
                Text(
                    text = "点击我!",
                    fontSize = 20.sp,
                    color = if (isPressed) 
                        MaterialTheme.colorScheme.onPrimary 
                    else 
                        MaterialTheme.colorScheme.onPrimaryContainer,
                    fontWeight = FontWeight.Bold
                )
            }
        }

        // 显示当前状态
        Text(
            text = "最后动作: $lastAction",
            fontSize = 16.sp,
            modifier = Modifier.padding(vertical = 16.dp)
        )

        // 自定义星形
        Surface(
            modifier = Modifier
                .size(150.dp)
                .semantics { contentDescription = "星形按钮" },
            shape = StarShape(),
            color = MaterialTheme.colorScheme.tertiary
        ) {
            Box(
                modifier = Modifier
                    .fillMaxSize()
                    .clickable(
                        interactionSource = remember { MutableInteractionSource() },
                        indication = rememberRipple(
                            color = Color.White,
                            bounded = true
                        ),
                        onClick = { lastAction = "点击自定义形状" }
                    ),
                contentAlignment = Alignment.Center
            ) {
                Text(
                    text = "自定义形状",
                    color = MaterialTheme.colorScheme.onTertiary,
                    fontWeight = FontWeight.Bold
                )
            }
        }

        // 圆形InkWell
        Surface(
            modifier = Modifier
                .size(80.dp)
                .semantics { contentDescription = "圆形按钮" },
            shape = CircleShape,
            color = MaterialTheme.colorScheme.secondary
        ) {
            Box(
                modifier = Modifier
                    .fillMaxSize()
                    .clickable(
                        interactionSource = remember { MutableInteractionSource() },
                        indication = rememberRipple(
                            color = Color.White,
                            bounded = false,
                            radius = 40.dp
                        ),
                        onClick = { lastAction = "点击圆形" }
                    ),
                contentAlignment = Alignment.Center
            ) {
                Icon(
                    imageVector = androidx.compose.material.icons.Icons.Default.TouchApp,
                    contentDescription = null,
                    tint = MaterialTheme.colorScheme.onSecondary,
                    modifier = Modifier.size(40.dp)
                )
            }
        }
        
        // 无边界水波纹示例
        Card(
            modifier = Modifier
                .padding(top = 16.dp)
                .size(width = 240.dp, height = 80.dp),
            shape = RoundedCornerShape(8.dp),
            elevation = CardDefaults.cardElevation(defaultElevation = 4.dp)
        ) {
            Box(
                modifier = Modifier
                    .fillMaxSize()
                    .clickable(
                        interactionSource = remember { MutableInteractionSource() },
                        indication = rememberRipple(
                            color = MaterialTheme.colorScheme.primary,
                            bounded = false  // 无边界水波纹
                        ),
                        onClick = { lastAction = "点击无边界水波纹" }
                    ),
                contentAlignment = Alignment.Center
            ) {
                Text(
                    text = "无边界水波纹",
                    fontSize = 16.sp,
                    fontWeight = FontWeight.Medium
                )
            }
        }
    }
}

@Preview(showBackground = true)
@Composable
fun InkWellDemoPreview() {
    MaterialTheme {
        InkWellDemo()
    }
}

## iOS UIKit InkWell

### 水波纹效果实现

iOS UIKit本身不提供类似Material Design的水波纹效果，但可以通过自定义实现：

- UIKit原生没有直接对应InkWell的水波纹组件
- 可以通过`CAShapeLayer`和`CABasicAnimation`自定义实现涟漪效果
- 传统做法是使用`UIButton`的高亮状态变化实现简单触摸反馈
- 可以借助第三方库如`Material`实现类似Android的水波纹效果
- 自定义实现通常使用Core Animation框架创建动画层
- 可以创建扩散的圆形动画模拟水波纹
- 需要手动处理触摸事件以确定水波纹起始位置

### 触摸反馈配置

UIKit的触摸反馈配置：

- 通过`UIControl`的`isHighlighted`属性跟踪触摸状态
- 可以使用`UIView.animate`在触摸时修改视图属性
- 支持配置触摸时的颜色、透明度和缩放变化
- 可以通过`UIControl.addTarget`监听触摸事件
- 支持使用`UIFeedbackGenerator`提供触觉反馈
- 可以通过`CALayer`的属性动画实现复杂的触摸视觉效果
- 自定义水波纹效果可配置颜色、大小、动画时长等参数

### 事件处理

UIKit的事件处理机制：

- 使用`UIControl`的`addTarget:action:forControlEvents:`方法处理事件
- 支持多种控制事件包括`.touchDown`、`.touchUpInside`、`.touchUpOutside`等
- 可以通过`UIGestureRecognizer`添加手势识别
- 支持`UILongPressGestureRecognizer`处理长按事件
- 可以通过`UITapGestureRecognizer`处理单击和双击
- 支持自定义手势识别器处理复杂触摸交互
- 提供完整的触摸事件传递链，包括hit-testing和响应链

### 自定义形状支持

UIKit的形状自定义支持：

- 通过`CAShapeLayer`和`UIBezierPath`创建任意形状
- 可以使用`masksToBounds`和`layer.mask`裁剪视图为特定形状
- 支持通过`cornerRadius`设置圆角
- 可以结合`layer.shadowPath`为自定义形状添加阴影
- 支持使用Core Graphics框架绘制复杂形状
- 可以通过`UIView`的`drawRect:`方法自定义绘图
- 触摸区域可通过重写`pointInside:withEvent:`方法自定义

### 无障碍考虑

UIKit的无障碍支持：

- 通过`UIAccessibility`协议提供完整的无障碍支持
- 支持设置`accessibilityLabel`和`accessibilityHint`
- 提供`accessibilityTraits`定义组件的角色(如按钮)
- 兼容VoiceOver、Switch Control等辅助技术
- 支持动态字体大小调整
- 可以使用`UIAccessibilityCustomAction`定义自定义操作
- 支持无障碍通知系统与辅助技术交互

### 平台特性与兼容性

UIKit的平台特性与兼容性：

- 专为iOS和iPadOS平台设计，与系统深度集成
- 遵循iOS设计语言，提供原生触摸体验
- 支持iOS 7.0及以上版本
- 完全适配各代iPhone和iPad设备
- 支持自动适应不同屏幕尺寸和方向
- 与iOS系统特性如Dark Mode、Dynamic Type兼容
- 触摸动画能根据系统设置自动调整

### 完整示例代码

```swift
import UIKit

class InkWellButton: UIControl {
    // 配置
    var highlightColor: UIColor = UIColor(white: 0, alpha: 0.1)
    var rippleColor: UIColor = UIColor(white: 0, alpha: 0.1)
    var rippleDuration: TimeInterval = 0.6
    var highlightDuration: TimeInterval = 0.1
    var rippleScaleFactor: CGFloat = 1.5
    
    // 内部变量
    private var rippleLayer: CAShapeLayer?
    private var backgroundLayer: CAShapeLayer?
    private var customContentView = UIView()
    private var labelView = UILabel()
    
    override init(frame: CGRect) {
        super.init(frame: frame)
        commonInit()
    }
    
    required init?(coder: NSCoder) {
        super.init(coder: coder)
        commonInit()
    }
    
    private func commonInit() {
        setupLayers()
        setupContentView()
        
        // 添加手势识别
        addTarget(self, action: #selector(touchDown), for: .touchDown)
        addTarget(self, action: #selector(touchUpInside), for: .touchUpInside)
        addTarget(self, action: #selector(touchUpOutside), for: .touchUpOutside)
        addTarget(self, action: #selector(touchCancel), for: .touchCancel)
        
        // 设置无障碍
        isAccessibilityElement = true
        accessibilityTraits = .button
    }
    
    private func setupLayers() {
        backgroundLayer = CAShapeLayer()
        backgroundLayer?.fillColor = UIColor.clear.cgColor
        layer.addSublayer(backgroundLayer!)
    }
    
    private func setupContentView() {
        addSubview(customContentView)
        customContentView.translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([
            customContentView.topAnchor.constraint(equalTo: topAnchor),
            customContentView.leadingAnchor.constraint(equalTo: leadingAnchor),
            customContentView.trailingAnchor.constraint(equalTo: trailingAnchor),
            customContentView.bottomAnchor.constraint(equalTo: bottomAnchor)
        ])
        
        // 添加标签
        labelView.translatesAutoresizingMaskIntoConstraints = false
        labelView.textAlignment = .center
        customContentView.addSubview(labelView)
        NSLayoutConstraint.activate([
            labelView.centerXAnchor.constraint(equalTo: customContentView.centerXAnchor),
            labelView.centerYAnchor.constraint(equalTo: customContentView.centerYAnchor)
        ])
    }
    
    // 设置文本内容
    func setTitle(_ title: String, font: UIFont = .systemFont(ofSize: 16), textColor: UIColor = .black) {
        labelView.text = title
        labelView.font = font
        labelView.textColor = textColor
        accessibilityLabel = title
    }
    
    // 设置背景色
    func setBackgroundColor(_ color: UIColor) {
        customContentView.backgroundColor = color
    }
    
    // 设置圆角
    func setCornerRadius(_ radius: CGFloat) {
        layer.cornerRadius = radius
        customContentView.layer.cornerRadius = radius
        setNeedsLayout()
    }
    
    override func layoutSubviews() {
        super.layoutSubviews()
        updateLayerFrames()
    }
    
    private func updateLayerFrames() {
        backgroundLayer?.frame = bounds
        
        let path = UIBezierPath(roundedRect: bounds, cornerRadius: layer.cornerRadius)
        backgroundLayer?.path = path.cgPath
    }
    
    // MARK: - 触摸事件处理
    
    @objc private func touchDown(_ sender: UIControl, event: UIEvent? = nil) {
        let touchPoint = event?.touches(for: sender)?.first?.location(in: self) ?? center
        showHighlight()
        showRippleEffect(at: touchPoint)
        
        // 触觉反馈
        if #available(iOS 10.0, *) {
            let generator = UIImpactFeedbackGenerator(style: .light)
            generator.prepare()
            generator.impactOccurred()
        }
    }
    
    @objc private func touchUpInside(_ sender: Any) {
        hideHighlight()
        sendActions(for: .valueChanged)
    }
    
    @objc private func touchUpOutside(_ sender: Any) {
        hideHighlight()
    }
    
    @objc private func touchCancel(_ sender: Any) {
        hideHighlight()
    }
    
    // MARK: - 视觉效果
    
    private func showHighlight() {
        backgroundLayer?.fillColor = highlightColor.cgColor
    }
    
    private func hideHighlight() {
        UIView.animate(withDuration: highlightDuration) {
            self.backgroundLayer?.fillColor = UIColor.clear.cgColor
        }
    }
    
    private func showRippleEffect(at point: CGPoint) {
        // 移除之前的波纹层
        rippleLayer?.removeFromSuperlayer()
        
        // 创建新的波纹层
        let rippleLayer = CAShapeLayer()
        self.rippleLayer = rippleLayer
        
        // 设置初始大小和位置
        let initialSize: CGFloat = 20
        let finalSize: CGFloat = max(bounds.width, bounds.height) * rippleScaleFactor
        
        rippleLayer.frame = CGRect(x: point.x - initialSize/2, y: point.y - initialSize/2, width: initialSize, height: initialSize)
        rippleLayer.path = UIBezierPath(ovalIn: CGRect(x: 0, y: 0, width: initialSize, height: initialSize)).cgPath
        rippleLayer.fillColor = rippleColor.cgColor
        rippleLayer.opacity = 0.5
        
        // 添加到视图层
        layer.insertSublayer(rippleLayer, above: backgroundLayer)
        
        // 创建动画
        let scaleAnimation = CABasicAnimation(keyPath: "transform.scale")
        scaleAnimation.fromValue = 1
        scaleAnimation.toValue = finalSize / initialSize
        
        let opacityAnimation = CABasicAnimation(keyPath: "opacity")
        opacityAnimation.fromValue = 0.5
        opacityAnimation.toValue = 0
        
        let animation = CAAnimationGroup()
        animation.animations = [scaleAnimation, opacityAnimation]
        animation.duration = rippleDuration
        animation.timingFunction = CAMediaTimingFunction(name: .easeOut)
        animation.isRemovedOnCompletion = false
        animation.fillMode = .forwards
        
        rippleLayer.add(animation, forKey: "rippleEffect")
    }
}

class InkWellViewController: UIViewController {
    private let stackView = UIStackView()
    private var lastActionLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
        
        setupStackView()
        setupLastActionLabel()
        addBasicInkWellButton()
        addCustomShapeButton()
        addCircleButton()
    }
    
    private func setupStackView() {
        stackView.axis = .vertical
        stackView.alignment = .center
        stackView.spacing = 24
        stackView.translatesAutoresizingMaskIntoConstraints = false
        
        view.addSubview(stackView)
        NSLayoutConstraint.activate([
            stackView.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            stackView.centerYAnchor.constraint(equalTo: view.centerYAnchor),
            stackView.widthAnchor.constraint(equalTo: view.widthAnchor, constant: -40)
        ])
    }
    
    private func setupLastActionLabel() {
        lastActionLabel.text = "最后动作: 无"
        lastActionLabel.font = UIFont.systemFont(ofSize: 16)
        lastActionLabel.textAlignment = .center
        
        stackView.addArrangedSubview(lastActionLabel)
        
        let titleLabel = UILabel()
        titleLabel.text = "UIKit InkWell 示例"
        titleLabel.font = UIFont.boldSystemFont(ofSize: 24)
        titleLabel.textAlignment = .center
        stackView.insertArrangedSubview(titleLabel, at: 0)
        
        // 添加间距
        let spacerView = UIView()
        spacerView.heightAnchor.constraint(equalToConstant: 20).isActive = true
        stackView.insertArrangedSubview(spacerView, at: 1)
    }
    
    private func addBasicInkWellButton() {
        let inkWellButton = InkWellButton(frame: .zero)
        inkWellButton.translatesAutoresizingMaskIntoConstraints = false
        inkWellButton.heightAnchor.constraint(equalToConstant: 100).isActive = true
        inkWellButton.widthAnchor.constraint(equalToConstant: 200).isActive = true
        
        inkWellButton.setTitle("点击我!", font: .boldSystemFont(ofSize: 20))
        inkWellButton.setBackgroundColor(UIColor(red: 0.9, green: 0.95, blue: 1.0, alpha: 1.0))
        inkWellButton.setCornerRadius(10)
        inkWellButton.addTarget(self, action: #selector(buttonTapped(_:)), for: .touchUpInside)
        
        stackView.addArrangedSubview(inkWellButton)
    }
    
    private func addCustomShapeButton() {
        let containerView = UIView()
        containerView.translatesAutoresizingMaskIntoConstraints = false
        containerView.heightAnchor.constraint(equalToConstant: 150).isActive = true
        containerView.widthAnchor.constraint(equalToConstant: 150).isActive = true
        
        // 创建星形路径
        let starPath = createStarPath(in: CGRect(x: 0, y: 0, width: 150, height: 150))
        
        // 创建自定义形状按钮
        let customShapeButton = StarShapeInkWellButton(frame: CGRect(x: 0, y: 0, width: 150, height: 150), starPath: starPath)
        customShapeButton.rippleColor = UIColor(red: 0.2, green: 0.7, blue: 0.3, alpha: 0.2)
        customShapeButton.highlightColor = UIColor(red: 0.2, green: 0.7, blue: 0.3, alpha: 0.1)
        customShapeButton.backgroundColor = UIColor(red: 0.4, green: 0.8, blue: 0.4, alpha: 1.0)
        customShapeButton.setTitle("自定义形状", textColor: .white)
        customShapeButton.tag = 1 // 使用tag区分按钮
        customShapeButton.addTarget(self, action: #selector(buttonTapped(_:)), for: .touchUpInside)
        
        containerView.addSubview(customShapeButton)
        stackView.addArrangedSubview(containerView)
    }
    
    private func addCircleButton() {
        let circleButton = InkWellButton(frame: .zero)
        circleButton.translatesAutoresizingMaskIntoConstraints = false
        circleButton.heightAnchor.constraint(equalToConstant: 80).isActive = true
        circleButton.widthAnchor.constraint(equalToConstant: 80).isActive = true
        
        circleButton.setTitle("", font: .boldSystemFont(ofSize: 20))
        circleButton.setBackgroundColor(UIColor(red: 0.6, green: 0.2, blue: 0.8, alpha: 1.0))
        circleButton.setCornerRadius(40)
        circleButton.rippleColor = UIColor(white: 1.0, alpha: 0.3)
        circleButton.tag = 2 // 使用tag区分按钮
        circleButton.addTarget(self, action: #selector(buttonTapped(_:)), for: .touchUpInside)
        
        // 添加图标
        let iconImageView = UIImageView(image: UIImage(systemName: "hand.tap.fill"))
        iconImageView.tintColor = .white
        iconImageView.contentMode = .scaleAspectFit
        iconImageView.translatesAutoresizingMaskIntoConstraints = false
        circleButton.addSubview(iconImageView)
        
        NSLayoutConstraint.activate([
            iconImageView.centerXAnchor.constraint(equalTo: circleButton.centerXAnchor),
            iconImageView.centerYAnchor.constraint(equalTo: circleButton.centerYAnchor),
            iconImageView.widthAnchor.constraint(equalToConstant: 40),
            iconImageView.heightAnchor.constraint(equalToConstant: 40)
        ])
        
        stackView.addArrangedSubview(circleButton)
    }
    
    @objc private func buttonTapped(_ sender: UIControl) {
        var actionText = "点击"
        
        switch sender.tag {
        case 1:
            actionText = "点击自定义形状"
        case 2:
            actionText = "点击圆形"
        default:
            actionText = "点击"
        }
        
        lastActionLabel.text = "最后动作: \(actionText)"
    }
    
    private func createStarPath(in rect: CGRect) -> UIBezierPath {
        let centerX = rect.width / 2
        let centerY = rect.height / 2
        
        let outerRadius = min(rect.width, rect.height) / 2
        let innerRadius = outerRadius * 0.4
        
        let path = UIBezierPath()
        let pointCount = 5
        
        for i in 0..<(pointCount * 2) {
            let radius = i % 2 == 0 ? outerRadius : innerRadius
            let angle = Double(i) * .pi / Double(pointCount) - .pi / 2
            
            let x = centerX + CGFloat(cos(angle)) * radius
            let y = centerY + CGFloat(sin(angle)) * radius
            
            if i == 0 {
                path.move(to: CGPoint(x: x, y: y))
            } else {
                path.addLine(to: CGPoint(x: x, y: y))
            }
        }
        
        path.closeSubpath()
        return path
    }
}

// 自定义星形按钮
class StarShapeInkWellButton: InkWellButton {
    private let starPath: UIBezierPath
    
    init(frame: CGRect, starPath: UIBezierPath) {
        self.starPath = starPath
        super.init(frame: frame)
        setupStarShape()
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    private func setupStarShape() {
        let shapeLayer = CAShapeLayer()
        shapeLayer.path = starPath.cgPath
        layer.mask = shapeLayer
    }
    
    override func layoutSubviews() {
        super.layoutSubviews()
        // 确保mask随视图尺寸变化
        if let shapeLayer = layer.mask as? CAShapeLayer {
            shapeLayer.frame = bounds
        }
    }
    
    // 覆盖点击区域检测，只在星形内部响应
    override func point(inside point: CGPoint, with event: UIEvent?) -> Bool {
        return starPath.contains(point)
    }
}

## iOS SwiftUI InkWell

### 水波纹效果实现

SwiftUI原生不提供Material Design风格的水波纹效果，但可以自定义实现：

- SwiftUI没有内置的水波纹效果组件
- 默认的按钮点击效果是简单的不透明度变化
- 可以通过自定义视图修饰符实现水波纹效果
- 使用`GeometryReader`、`Shape`和动画实现类似的效果
- 可基于`Canvas`或`TimelineView`实现更流畅的水波纹动画
- 需要结合`PreferenceKey`获取触摸位置信息
- 可以通过扩展`ButtonStyle`自定义含水波纹的按钮样式

### 触摸反馈配置

SwiftUI的触摸反馈配置：

- 使用`.buttonStyle`修饰符应用预定义或自定义按钮样式
- 通过`.hoverEffect`为交互元素添加悬停效果
- 可以使用`.scaleEffect`和`.opacity`配合`.animation`实现触摸反馈
- 支持通过UIKit桥接使用系统触感反馈
- 可自定义动画时长、曲线和反馈行为
- 通过`.simultaneousGesture`可以在触摸时触发额外效果
- 支持`.contentShape`修改点击检测区域

### 事件处理

SwiftUI的事件处理系统：

- 通过`onTapGesture`处理点击事件
- 支持`onLongPressGesture`处理长按
- 可以使用`gesture`修饰符添加复杂手势
- 提供`DragGesture`、`MagnificationGesture`等内置手势
- 支持使用`.simultaneousGesture`和`.sequenced`组合多个手势
- 通过`GestureState`跟踪手势状态
- 使用`.highPriorityGesture`和`.exclusively`处理手势冲突

### 自定义形状支持

SwiftUI的形状支持：

- 通过`Shape`协议创建自定义形状
- 内置多种形状如`RoundedRectangle`、`Circle`、`Capsule`
- 使用`.clipShape`将任何视图裁剪为特定形状
- 支持`.fill`和`.stroke`为形状添加填充和边框
- 可以通过`.background`或`.overlay`添加形状作为背景或前景
- 支持形状的布尔运算（如组合、相交、减去）
- 可通过`GeometryReader`创建适应容器大小的响应式形状

### 无障碍考虑

SwiftUI的无障碍支持：

- 与iOS的VoiceOver无缝集成
- 通过`.accessibility`修饰符添加无障碍标签和提示
- 支持`.accessibilityAddTraits`指定控件角色（如按钮）
- 自动适配动态字体大小和粗体文本设置
- 支持无障碍公告和通知
- 自动处理颜色对比度和高对比度显示模式
- 通过`.accessibilityAction`添加自定义无障碍操作

### 平台特性与兼容性

SwiftUI的平台特性与兼容性：

- 跨平台设计，支持iOS、iPadOS、macOS、tvOS和watchOS
- 自动适配不同平台的交互模式和设计规范
- 在iOS上遵循iOS设计语言，提供原生触摸体验
- 支持iOS 13及以上版本
- 自适应不同尺寸类别和设备方向
- 与系统特性如Dark Mode、Dynamic Type完全集成
- 支持SwiftUI 2.0+的新特性如LazyGrid和新动画系统

### 完整示例代码

```swift
import SwiftUI

// 自定义水波纹修饰符
struct RippleEffect: ViewModifier {
    let color: Color
    let speed: Double
    
    @State private var isAnimating = false
    @State private var rippleScale: CGFloat = 0.5
    @State private var opacity: Double = 0.6
    @State private var tapLocation: CGPoint = .zero
    
    func body(content: Content) -> some View {
        ZStack {
            content
            
            GeometryReader { geometry in
                ZStack {
                    // 水波纹效果
                    if isAnimating {
                        Circle()
                            .fill(color)
                            .opacity(opacity)
                            .scaleEffect(rippleScale)
                            .position(tapLocation)
                            .onAppear {
                                withAnimation(.easeOut(duration: speed)) {
                                    self.rippleScale = 2.0
                                    self.opacity = 0
                                }
                            }
                            .onDisappear {
                                self.rippleScale = 0.5
                                self.opacity = 0.6
                            }
                    }
                }
                .contentShape(Rectangle())
                .gesture(
                    DragGesture(minimumDistance: 0)
                        .onChanged { value in
                            if !isAnimating {
                                tapLocation = value.location
                                isAnimating = true
                            }
                        }
                        .onEnded { _ in
                            DispatchQueue.main.asyncAfter(deadline: .now() + speed) {
                                isAnimating = false
                            }
                        }
                )
            }
        }
    }
}

// 水波纹按钮样式
struct RippleButtonStyle: ButtonStyle {
    var color: Color = Color.black.opacity(0.2)
    var highlightColor: Color = Color.black.opacity(0.05)
    var cornerRadius: CGFloat = 10
    var rippleSpeed: Double = 0.5
    
    @State private var isPressed = false
    @State private var tapLocation: CGPoint = .zero
    @State private var isRippling = false
    
    func makeBody(configuration: Configuration) -> some View {
        GeometryReader { geometry in
            ZStack {
                // 背景
                RoundedRectangle(cornerRadius: cornerRadius)
                    .fill(configuration.isPressed ? highlightColor : Color.clear)
                
                // 水波纹
                if isRippling && !tapLocation.equalTo(.zero) {
                    Circle()
                        .fill(color)
                        .opacity(isRippling ? 0 : 0.5)
                        .scaleEffect(isRippling ? 2 : 0.5)
                        .position(tapLocation)
                        .animation(.easeOut(duration: rippleSpeed), value: isRippling)
                }
                
                // 内容
                configuration.label
                    .frame(maxWidth: .infinity, maxHeight: .infinity)
            }
            .clipped()
            .contentShape(RoundedRectangle(cornerRadius: cornerRadius))
            .simultaneousGesture(
                DragGesture(minimumDistance: 0)
                    .onChanged { value in
                        if !isRippling {
                            tapLocation = value.location
                            isRippling = true
                        }
                    }
                    .onEnded { _ in
                        DispatchQueue.main.asyncAfter(deadline: .now() + rippleSpeed) {
                            isRippling = false
                            tapLocation = .zero
                        }
                    }
            )
            .onChange(of: configuration.isPressed) { newValue in
                // 触感反馈
                if newValue {
                    UIImpactFeedbackGenerator(style: .light).impactOccurred()
                }
            }
        }
    }
}

// 星形路径生成
struct StarShape: Shape {
    func path(in rect: CGRect) -> Path {
        let center = CGPoint(x: rect.midX, y: rect.midY)
        let outerRadius = min(rect.width, rect.height) / 2
        let innerRadius = outerRadius * 0.4
        let pointCount = 5
        
        var path = Path()
        
        for i in 0..<(pointCount * 2) {
            let radius = i % 2 == 0 ? outerRadius : innerRadius
            let angle = Double(i) * .pi / Double(pointCount) - .pi / 2
            
            let x = center.x + CGFloat(cos(angle)) * radius
            let y = center.y + CGFloat(sin(angle)) * radius
            
            if i == 0 {
                path.move(to: CGPoint(x: x, y: y))
            } else {
                path.addLine(to: CGPoint(x: x, y: y))
            }
        }
        
        path.closeSubpath()
        return path
    }
}

// 主视图
struct InkWellDemoView: View {
    @State private var lastAction = "无"
    @State private var isPressed = false
    
    var body: some View {
        VStack(spacing: 24) {
            Text("SwiftUI InkWell 示例")
                .font(.system(size: 24, weight: .bold))
                .padding(.bottom, 16)
            
            // 基本水波纹按钮
            Button(action: {
                lastAction = "点击"
                isPressed = true
                DispatchQueue.main.asyncAfter(deadline: .now() + 0.2) {
                    isPressed = false
                }
            }) {
                Text("点击我!")
                    .font(.system(size: 20, weight: .bold))
                    .foregroundColor(isPressed ? .white : .black)
                    .frame(width: 200, height: 100)
                    .background(Color.blue.opacity(0.15))
                    .cornerRadius(10)
            }
            .buttonStyle(RippleButtonStyle(
                color: Color.blue.opacity(0.3),
                highlightColor: Color.blue.opacity(0.1),
                cornerRadius: 10
            ))
            
            // 显示当前状态
            Text("最后动作: \(lastAction)")
                .font(.system(size: 16))
                .padding(.vertical, 16)
            
            // 自定义星形按钮
            Button(action: {
                lastAction = "点击自定义形状"
            }) {
                Text("自定义形状")
                    .foregroundColor(.white)
                    .font(.system(size: 16, weight: .bold))
                    .frame(width: 150, height: 150)
            }
            .background(
                StarShape()
                    .fill(Color.green)
            )
            .clipShape(StarShape())
            .modifier(RippleEffect(color: Color.white.opacity(0.3), speed: 0.6))
            .contentShape(StarShape())
            
            // 圆形按钮
            Button(action: {
                lastAction = "点击圆形"
            }) {
                Image(systemName: "hand.tap.fill")
                    .font(.system(size: 30))
                    .foregroundColor(.white)
                    .frame(width: 80, height: 80)
            }
            .background(Circle().fill(Color.purple))
            .clipShape(Circle())
            .modifier(RippleEffect(color: Color.white.opacity(0.3), speed: 0.5))
            
            // 无边界水波纹示例
            Button(action: {
                lastAction = "点击无边界水波纹"
            }) {
                Text("无边界水波纹")
                    .font(.system(size: 16, weight: .medium))
                    .foregroundColor(.primary)
                    .frame(width: 240, height: 80)
                    .background(
                        RoundedRectangle(cornerRadius: 8)
                            .fill(Color(.systemBackground))
                            .shadow(color: Color.black.opacity(0.1), radius: 4, x: 0, y: 2)
                    )
            }
            .buttonStyle(RippleButtonStyle(
                color: Color.blue.opacity(0.2),
                cornerRadius: 8,
                rippleSpeed: 0.8
            ))
            .padding(.top, 16)
        }
        .padding()
        .background(Color(.systemBackground))
    }
}

// 预览
struct InkWellDemoView_Previews: PreviewProvider {
    static var previews: some View {
        InkWellDemoView()
            .preferredColorScheme(.light)
        
        InkWellDemoView()
            .preferredColorScheme(.dark)
    }
}

// 使用方法
struct ContentView: View {
    var body: some View {
        InkWellDemoView()
    }
}

## 鸿蒙 ArkUI InkWell

### 水波纹效果实现

鸿蒙ArkUI提供了原生的水波纹触摸反馈实现：

- 通过`stateEffect`或`ClickEffect`特性内置水波纹效果
- 支持`touchEffect`属性为组件添加触摸涟漪效果
- 水波纹效果遵循鸿蒙设计语言规范，从点击中心向外扩散
- 可通过`effectColor`自定义水波纹颜色
- 支持不同形状组件的水波纹适配
- 水波纹动画曲线和持续时间遵循鸿蒙动效设计规范
- 自动适配深色模式，调整水波纹对比度

### 触摸反馈配置

鸿蒙ArkUI的触摸反馈配置：

- 通过`stateStyles`完整配置不同状态下的组件样式
- 支持通过`hoverEffect`配置悬停效果
- 可通过`touchable`属性控制触摸响应能力
- 提供`pressedState`或`pressed`状态绑定触摸状态
- `responseRegion`允许自定义触摸响应区域
- 支持通过`vibrate`触发振动反馈
- 可通过动画API自定义触摸时的形变效果

### 事件处理

鸿蒙ArkUI的事件处理系统：

- 提供`onClick`处理点击事件
- 支持`onTouch`捕获原始触摸事件
- 通过`gesture`添加手势识别器
- 支持`LongPressGesture`处理长按事件
- 提供`PanGesture`、`TapGesture`等内置手势识别器
- 支持通过`priorityGesture`和`parallelGesture`处理手势优先级
- 可通过事件冒泡机制控制事件传递

### 自定义形状支持

鸿蒙ArkUI的形状支持：

- 支持通过`clip`属性将组件裁剪为指定形状
- 提供`Shape`组件用于绘制各种图形
- 支持自定义路径绘制复杂形状
- 可通过`Stack`和蒙版效果实现特殊形状
- 支持`borderRadius`配置圆角矩形
- 通过`mask`属性实现蒙版裁剪效果
- 自定义形状的水波纹效果会自动适配边界

### 无障碍考虑

鸿蒙ArkUI的无障碍支持：

- 通过`accessibilityGroup`和`accessibilityText`提供完整的无障碍支持
- 支持`accessibilityLevel`设置朗读优先级
- 可设置`accessibilityDescription`提供详细描述
- 内置对系统辅助功能的支持，如屏幕朗读和放大
- 支持`focusable`和`onFocus`处理焦点导航
- 提供高对比度设计自动适配
- 符合鸿蒙无障碍设计规范

### 平台特性与兼容性

鸿蒙ArkUI的平台特性与兼容性：

- 专为鸿蒙OS设计，与系统深度集成
- 支持鸿蒙OS的多种设备，包括手机、平板、智能手表等
- 自适应不同设备形态和屏幕尺寸
- 与鸿蒙系统主题和设计语言一致
- 支持多种输入方式，包括触摸、手写笔和键盘
- 提供性能优化的动画和交互实现
- 适配鸿蒙生态的分布式能力

### 完整示例代码

```typescript
// InkWellDemo.ets
import vibrator from '@ohos.vibrator';

@Entry
@Component
struct InkWellDemo {
  @State lastAction: string = '无';
  @State isPressed: boolean = false;
  
  // 触感反馈
  vibrate() {
    vibrator.vibrate(10, { id: 'click' });
  }
  
  // 处理点击动作
  handleAction(action: string) {
    this.lastAction = action;
    this.isPressed = true;
    this.vibrate();
    
    // 重置按压状态
    setTimeout(() => {
      this.isPressed = false;
    }, 200);
  }
  
  build() {
    Column({ space: 24 }) {
      Text('鸿蒙ArkUI InkWell 示例')
        .fontSize(24)
        .fontWeight(FontWeight.Bold)
        .margin({ bottom: 16 })
      
      // 基本水波纹按钮
      Column() {
        Text('点击我!')
          .fontSize(20)
          .fontWeight(FontWeight.Bold)
          .fontColor(this.isPressed ? Color.White : Color.Black)
      }
      .width(200)
      .height(100)
      .backgroundColor(0xE6F0FF)
      .borderRadius(10)
      .onClick(() => this.handleAction('点击'))
      .stateStyles({
        pressed: {
          backgroundColor: 0xD0E0FF,
        }
      })
      .touchEffect({ type: TouchType.Highlight, effectColor: 0x1A66CCFF })
      
      // 显示当前状态
      Text(`最后动作: ${this.lastAction}`)
        .fontSize(16)
        .margin({ vertical: 16 })
      
      // 自定义星形按钮
      Column() {
        Stack() {
          StarShape()
            .fill(0x4CAF50)
            .width(150)
            .height(150)
          
          Text('自定义形状')
            .fontSize(16)
            .fontWeight(FontWeight.Bold)
            .fontColor(Color.White)
        }
        .width(150)
        .height(150)
      }
      .onClick(() => this.handleAction('点击自定义形状'))
      .touchEffect({ type: TouchType.Highlight, effectColor: 0x80FFFFFF })
      
      // 圆形按钮
      Column() {
        Stack() {
          Circle()
            .fill(0x9C27B0)
            .width(80)
            .height(80)
          
          Image($r('app.media.touch_icon'))
            .width(40)
            .height(40)
            .fillColor(Color.White)
        }
        .width(80)
        .height(80)
      }
      .onClick(() => this.handleAction('点击圆形'))
      .touchEffect({ type: TouchType.Highlight, effectColor: 0x80FFFFFF })
      
      // 无边界水波纹按钮
      Button('无边界水波纹', { type: ButtonType.Normal })
        .width(240)
        .height(80)
        .fontSize(16)
        .fontWeight(FontWeight.Medium)
        .backgroundColor(0xFFFFFF)
        .borderRadius(8)
        .onClick(() => this.handleAction('点击无边界水波纹'))
        // 设置为非绑定水波纹效果
        .touchEffect({ type: TouchType.Highlight, effectColor: 0x332196F3 })
        .margin({ top: 16 })
        .shadow({ radius: 4, color: 0x1A000000, offsetY: 2 })
    }
    .width('100%')
    .height('100%')
    .padding(16)
    .backgroundColor(0xF5F5F5)
    .justifyContent(FlexAlign.Center)
  }
}

// 自定义星形组件
@Component
struct StarShape {
  @State pointCount: number = 5;
  @State outerRadius: number = 75;
  @State innerRadius: number = 30;
  private settings: RenderingContextSettings = new RenderingContextSettings(true);
  private context: CanvasRenderingContext2D = new CanvasRenderingContext2D(this.settings);
  
  build() {
    Canvas(this.context)
      .width(150)
      .height(150)
      .onReady(() => {
        this.drawStar();
      })
  }
  
  drawStar() {
    const centerX = 75;
    const centerY = 75;
    
    this.context.beginPath();
    this.context.fillStyle = '#4CAF50';
    
    // 绘制星形
    for (let i = 0; i < this.pointCount * 2; i++) {
      const radius = i % 2 === 0 ? this.outerRadius : this.innerRadius;
      const angle = (i * Math.PI / this.pointCount) - (Math.PI / 2);
      
      const x = centerX + radius * Math.cos(angle);
      const y = centerY + radius * Math.sin(angle);
      
      if (i === 0) {
        this.context.moveTo(x, y);
      } else {
        this.context.lineTo(x, y);
      }
    }
    
    this.context.closePath();
    this.context.fill();
  }
}

// 自定义触摸特效组件
@Component
struct CustomTouchEffect {
  @Prop isPressed: boolean = false;
  @State rippleScale: number = 0;
  @State rippleOpacity: number = 0.6;
  @State animating: boolean = false;
  @State tapX: number = 0;
  @State tapY: number = 0;
  
  build() {
    Column() {
      if (this.animating) {
        Circle()
          .fill(0x33FFFFFF)
          .width(this.rippleScale)
          .height(this.rippleScale)
          .opacity(this.rippleOpacity)
          .position({ x: this.tapX - this.rippleScale / 2, y: this.tapY - this.rippleScale / 2 })
      }
    }
    .width('100%')
    .height('100%')
    .onTouch((event) => {
      if (event.type === TouchType.Down) {
        this.tapX = event.touches[0].x;
        this.tapY = event.touches[0].y;
        this.rippleScale = 10;
        this.rippleOpacity = 0.6;
        this.animating = true;
        
        // 开始动画
        animateTo({ duration: 500, curve: Curve.Ease }, () => {
          this.rippleScale = 200;
          this.rippleOpacity = 0;
        }, () => {
          // 动画结束
          this.animating = false;
        })
      }
    })
  }
}

// 主题资源
export enum TouchType {
  Down = 0,
  Up = 1,
  Move = 2,
  Highlight = 3
}

// 自定义使用
@Component
struct CustomInkWellButton {
  @State isPressed: boolean = false;
  @Prop effectColor: number = 0x33000000;
  @Prop backgroundColor: number = 0xFFFFFF;
  private onClick: () => void;
  
  build() {
    Stack() {
      // 背景
      Rect()
        .fill(this.isPressed ? 0xE0E0E0 : this.backgroundColor)
        .width('100%')
        .height('100%')
        .borderRadius(8)
      
      // 内容插槽
      Flex({ direction: FlexDirection.Column, justifyContent: FlexAlign.Center, alignItems: ItemAlign.Center }) {
        slot('content')
      }
      .width('100%')
      .height('100%')
      
      // 水波纹效果
      CustomTouchEffect({ isPressed: this.isPressed })
    }
    .width('100%')
    .height('100%')
    .onTouch((event) => {
      if (event.type === TouchType.Down) {
        this.isPressed = true;
      } else if (event.type === TouchType.Up) {
        this.isPressed = false;
        this.onClick && this.onClick();
      }
    })
  }
}

## Vue InkWell

### 水波纹效果实现

Vue本身不提供水波纹效果，但可以通过多种方式实现：

- Vue没有内置水波纹组件，需要自定义实现
- 可以使用CSS `::after`伪元素结合动画实现水波纹效果
- 结合第三方UI框架如Vuetify或Quasar可使用现成的水波纹组件
- 可以使用Canvas或SVG实现更精细的水波纹控制
- 基于Web动画API实现流畅的水波纹效果
- 通过监听触摸或鼠标事件计算水波纹起点位置
- 支持结合Vue的响应式系统控制水波纹状态

### 触摸反馈配置

Vue的触摸反馈配置：

- 使用Vue的`v-bind`和CSS变量动态配置触摸样式
- 支持通过`:active`、`:hover`伪类实现基本触摸反馈
- 可以使用CSS `transition`或`animation`配置动画效果
- 通过自定义组件属性控制水波纹颜色、大小和速度
- 可结合Vue的计算属性和CSS模块实现不同状态的样式
- 支持通过`v-touch`指令（需第三方库）优化移动设备触摸
- Web平台下可使用浏览器的`Vibration API`实现触觉反馈

### 事件处理

Vue的事件处理系统：

- 使用`@click`指令处理点击事件
- 可以通过`@touchstart`、`@touchend`等处理原始触摸事件
- 支持通过`.stop`、`.prevent`修饰符控制事件传播
- 可以使用`@click.once`处理一次性点击
- 提供`@click.self`仅在事件源是元素本身时触发
- 可整合`Hammer.js`等库实现丰富的手势支持
- 支持自定义事件传递和事件总线系统

### 自定义形状支持

Vue的形状支持：

- 通过CSS `border-radius`实现基本形状如圆角矩形和圆形
- 可以使用`clip-path`属性创建复杂形状
- 支持SVG实现任意矢量形状
- 可通过CSS `mask`属性实现蒙版效果
- 结合Vue的计算样式动态生成形状
- 支持使用Canvas API绘制和交互
- 可使用CSS变量实现响应式形状调整

### 无障碍考虑

Vue的无障碍支持：

- 支持标准HTML无障碍属性如`aria-*`
- 可以使用`<transition>`组件的`prefers-reduced-motion`媒体查询适配
- 支持键盘导航和焦点管理
- 可集成屏幕阅读器兼容的标签和描述
- 通过指令或组件包装实现无障碍最佳实践
- 支持高对比度模式和暗色主题
- 可使用Vue的Teleport组件优化模态框和弹出层的无障碍性

### 平台特性与兼容性

Vue的平台特性与兼容性：

- 作为Web框架，可在所有现代浏览器上运行
- 支持响应式设计，适配桌面和移动设备
- 可以结合PWA技术实现类原生应用体验
- 支持服务端渲染(SSR)和静态站点生成(SSG)
- 通过Electron或Tauri可构建跨平台桌面应用
- 支持使用Vue Native或Capacitor构建移动应用
- 完全兼容现代Web标准和API

### 完整示例代码

```vue
<template>
  <div class="ink-well-demo">
    <h1 class="title">Vue InkWell 示例</h1>
    
    <!-- 基本水波纹按钮 -->
    <ink-well
      :style="{ background: '#e6f0ff' }"
      class="basic-button"
      @click="handleAction('点击')"
    >
      <span :class="{ 'text-white': isPressed }">点击我!</span>
    </ink-well>
    
    <!-- 显示当前状态 -->
    <div class="status">最后动作: {{ lastAction }}</div>
    
    <!-- 自定义星形按钮 -->
    <ink-well
      class="star-button"
      shape="star"
      :ripple-color="'rgba(255, 255, 255, 0.3)'"
      background-color="#4caf50"
      @click="handleAction('点击自定义形状')"
    >
      <span class="white-text">自定义形状</span>
    </ink-well>
    
    <!-- 圆形按钮 -->
    <ink-well
      class="circle-button"
      shape="circle"
      :ripple-color="'rgba(255, 255, 255, 0.3)'"
      background-color="#9c27b0"
      @click="handleAction('点击圆形')"
    >
      <i class="material-icons">touch_app</i>
    </ink-well>
    
    <!-- 无边界水波纹按钮 -->
    <ink-well
      class="unbounded-button"
      :bounded="false"
      :ripple-color="'rgba(33, 150, 243, 0.2)'"
      @click="handleAction('点击无边界水波纹')"
    >
      <span>无边界水波纹</span>
    </ink-well>
  </div>
</template>

<script>
// 水波纹组件
import InkWell from './components/InkWell.vue'

export default {
  name: 'InkWellDemo',
  components: {
    InkWell
  },
  data() {
    return {
      lastAction: '无',
      isPressed: false
    }
  },
  methods: {
    handleAction(action) {
      this.lastAction = action
      this.isPressed = true
      
      // 提供触觉反馈 (如果浏览器支持)
      if (window.navigator && window.navigator.vibrate) {
        window.navigator.vibrate(10)
      }
      
      // 重置按压状态
      setTimeout(() => {
        this.isPressed = false
      }, 200)
    }
  }
}
</script>

<style scoped>
.ink-well-demo {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  font-family: Arial, sans-serif;
  background-color: #f5f5f5;
  min-height: 100vh;
}

.title {
  font-size: 24px;
  font-weight: 700;
  margin-bottom: 24px;
}

.basic-button {
  width: 200px;
  height: 100px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 20px;
  font-weight: 700;
}

.status {
  margin: 24px 0;
  font-size: 16px;
}

.star-button {
  width: 150px;
  height: 150px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.circle-button {
  width: 80px;
  height: 80px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 24px 0;
}

.unbounded-button {
  width: 240px;
  height: 80px;
  border-radius: 8px;
  background-color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 16px;
  font-weight: 500;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  margin-top: 16px;
}

.white-text {
  color: white;
  font-weight: 700;
}

.text-white {
  color: white;
}

.material-icons {
  font-size: 40px;
  color: white;
}
</style>
```

## 总结对比

以下是六大框架InkWell组件的主要特性对比：

| 特性 | Flutter | Jetpack Compose | UIKit | SwiftUI | ArkUI | Vue |
|------|---------|-----------------|-------|---------|-------|-----|
| **原生水波纹支持** | ✅ 内置 | ✅ 内置 | ❌ 需自定义 | ❌ 需自定义 | ✅ 内置 | ❌ 需自定义 |
| **触摸反馈配置** | 丰富的属性集 | 完整的配置API | 基于UIControl | 自定义修饰符 | 内置状态样式 | CSS+响应式属性 |
| **事件系统** | 多种手势事件 | 完整手势API | 响应链机制 | 声明式手势API | 丰富事件API | DOM事件+指令 |
| **自定义形状** | 强大而灵活 | 全面支持 | 需手动实现 | 内置Shape协议 | 良好支持 | CSS+SVG实现 |
| **无障碍支持** | 内置语义层 | 与系统集成 | UIAccessibility | SwiftUI无障碍API | 原生支持 | ARIA属性支持 |
| **平台适配** | 跨平台一致 | 针对Android优化 | iOS专用 | 苹果生态系统 | 鸿蒙专用 | Web平台 |
| **性能表现** | 高效(自绘引擎) | 高效(Compose) | 原生性能 | 原生性能 | 原生性能 | 依赖浏览器 |
| **动画流畅度** | 非常流畅 | 流畅(系统级) | 依赖实现 | 流畅(SwiftUI动画) | 流畅(系统级) | 依赖CSS动画 |
| **开发便捷性** | 简单直接 | 声明式API | 代码量较大 | 声明式简洁 | 声明式API | 组件化简洁 |

### 主要差异

1. **原生支持**: Flutter、Jetpack Compose和鸿蒙ArkUI提供原生水波纹效果，而UIKit、SwiftUI和Vue需要自定义实现。

2. **实现方式**: 
   - Flutter: 使用Material Design规范的InkWell和InkResponse组件
   - Jetpack Compose: 通过Ripple indication实现Material Design风格水波纹
   - UIKit: 通过自定义CALayer动画模拟水波纹效果
   - SwiftUI: 需要通过自定义ViewModifier和动画实现
   - ArkUI: 通过touchEffect和stateEffect提供原生支持
   - Vue: 使用CSS动画和DOM操作实现水波纹效果

3. **形状支持**: Flutter和Jetpack Compose提供最完整的形状自定义能力，而其他框架需要不同程度的自定义代码。

4. **无障碍**: 所有框架都支持无障碍功能，但实现方式和集成度有所不同，Flutter和Jetpack Compose提供较为简便的无障碍支持。

5. **平台适配**: 
   - Flutter提供跨平台一致的体验
   - Jetpack Compose针对Android平台优化
   - UIKit和SwiftUI针对iOS/Apple平台优化
   - ArkUI针对鸿蒙系统优化
   - Vue适用于Web平台，可通过额外框架扩展到其他平台

### 最佳实践建议

- **跨平台应用**: 选择Flutter可提供最一致的水波纹体验
- **Android应用**: Jetpack Compose提供最原生的Material Design体验
- **iOS应用**: SwiftUI更现代，而UIKit更成熟，但都需要自定义水波纹
- **鸿蒙应用**: ArkUI提供与系统深度集成的水波纹实现
- **Web应用**: Vue通过自定义组件可实现跨浏览器的水波纹效果

无论选择哪种框架，水波纹效果作为一种重要的触摸反馈机制，都能显著提升用户体验，增强应用的交互质感。 