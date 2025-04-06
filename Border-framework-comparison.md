# 六大框架Border组件比较

## 目录
- [边框样式配置](#边框样式配置)
- [边框绘制机制](#边框绘制机制)
- [圆角与边框结合](#圆角与边框结合)
- [响应式边框](#响应式边框)
- [多边框支持](#多边框支持)
- [平台特性与兼容性](#平台特性与兼容性)
- [完整实例代码](#完整实例代码)

## 边框样式配置

| 框架 | 配置方式 | 支持样式 |
|------|---------|---------|
| Flutter | BoxDecoration 属性 | 颜色、宽度、样式(实线、虚线等)、单边配置 |
| Jetpack Compose | border 修饰符 | 颜色、宽度、样式(实线、虚线等)、笔触连接方式 |
| UIKit | layer.border 属性 | 颜色、宽度、CAShapeLayer自定义 |
| SwiftUI | overlay/border 修饰符 | 颜色、宽度、渐变边框、自定义形状边框 |
| ArkUI (鸿蒙) | border 属性 | 颜色、宽度、样式、单边配置 |
| Vue | CSS border 属性 | 颜色、宽度、样式、单边配置、CSS变量支持 |

## 边框绘制机制

| 框架 | 绘制原理 | 特点 |
|------|---------|------|
| Flutter | 使用 Canvas 渲染 | 高效渲染，支持自定义绘制，边框在内容区域之外 |
| Jetpack Compose | Skia 图形库绘制 | 组合式API，基于路径绘制，可以精确控制边框位置 |
| UIKit | CoreAnimation 绘制 | layer层级渲染，性能高，边框绘制在视图边界上 |
| SwiftUI | 声明式边框修饰符 | 基于Shape协议，可组合多种效果，边框可内可外 |
| ArkUI | 声明式属性渲染 | 基于属性进行渲染，支持多种预设样式，与组件系统集成 |
| Vue | 浏览器渲染引擎 | 标准CSS盒模型，边框在内容和内边距之外 |

## 圆角与边框结合

| 框架 | 结合方式 | 特点 |
|------|---------|------|
| Flutter | BoxDecoration统一配置 | 同一容器内统一设置，支持不同角不同圆角半径 |
| Jetpack Compose | 统一使用RoundedCornerShape | 支持设置四个角不同的圆角，边框会自动跟随圆角 |
| UIKit | cornerRadius + masksToBounds | 边框和圆角需要分别设置，圆角裁剪内容需配置masksToBounds |
| SwiftUI | 组合使用clipShape和overlay | 允许高度自定义，可以单独定义边框和圆角 |
| ArkUI | 统一border配置 | 直接支持border和borderRadius属性搭配使用 |
| Vue | CSS border-radius 搭配使用 | 标准CSS属性，支持各种圆角单位和单独配置四个角 |

## 响应式边框

| 框架 | 响应方式 | 特点 |
|------|---------|------|
| Flutter | 状态管理 + 动画 | 使用AnimatedContainer或自定义动画控制边框变化 |
| Jetpack Compose | 状态观察和动画API | 依赖状态变化自动重组UI，支持animateContentSize等API |
| UIKit | UIView动画 | 使用动画块改变layer属性，支持弹簧动画等多种效果 |
| SwiftUI | 动画修饰符 | 使用animation()修饰符实现平滑过渡，支持各种动画曲线 |
| ArkUI | 状态驱动和动画API | 使用stateStyles或animateTo方法实现状态响应 |
| Vue | CSS过渡/动画 + 响应式数据 | 使用v-bind绑定样式，搭配transition实现动画效果 |

## 多边框支持

| 框架 | 支持方式 | 特点 |
|------|---------|------|
| Flutter | 嵌套多个Container | 需要嵌套多个带边框的容器，实现略复杂 |
| Jetpack Compose | 多个border修饰符叠加 | 可以叠加多个border()修饰符实现多重边框 |
| UIKit | 多层视图或CALayer | 需要添加子视图或子层实现多边框 |
| SwiftUI | 多重overlay叠加 | 可以叠加多个overlay或border修饰符实现多边框 |
| ArkUI | 组件嵌套 | 需要通过嵌套组件的方式实现多边框效果 |
| Vue | 多重边框或伪元素 | 可使用outline+border或:before/:after伪元素实现多边框 |

## 平台特性与兼容性

### Flutter
- **平台支持**: Android, iOS, Web, Windows, macOS, Linux
- **优势**: 统一API跨平台，边框渲染一致性高
- **局限性**: 复杂边框效果需自定义画布实现

### Jetpack Compose
- **平台支持**: Android, 桌面(实验阶段)
- **优势**: 与现代Android设计语言完美融合
- **局限性**: 仅限Android生态，低版本Android可能存在兼容性问题

### UIKit
- **平台支持**: iOS, iPadOS
- **优势**: 性能优化好，与系统完美集成
- **局限性**: 简单边框易实现，复杂边框需要CAShapeLayer自定义

### SwiftUI
- **平台支持**: iOS 13+, iPadOS, macOS, watchOS, tvOS
- **优势**: 声明式语法简洁，边框与其它效果组合灵活
- **局限性**: 需要较新的iOS版本支持，某些特性在旧版本不可用

### ArkUI (鸿蒙)
- **平台支持**: 鸿蒙OS设备
- **优势**: 针对鸿蒙多设备形态优化，流畅过渡支持
- **局限性**: 仅限鸿蒙生态，市场份额相对较小

### Vue
- **平台支持**: 各类浏览器平台
- **优势**: 基于标准CSS，灵活性极高，可利用丰富的Web特效
- **局限性**: 不同浏览器渲染差异需处理，移动端原生体验需额外适配 

## 完整实例代码

### Flutter

```dart
import 'package:flutter/material.dart';

class BorderDemoPage extends StatefulWidget {
  @override
  _BorderDemoPageState createState() => _BorderDemoPageState();
}

class _BorderDemoPageState extends State<BorderDemoPage> {
  bool _isHovered = false;
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Flutter Border 示例')),
      body: SingleChildScrollView(
        padding: EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            // 1. 基本边框样式
            Text('基本边框样式', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
            SizedBox(height: 10),
            Row(
              mainAxisAlignment: MainAxisAlignment.spaceEvenly,
              children: [
                // 实线边框
                Container(
                  width: 100,
                  height: 100,
                  decoration: BoxDecoration(
                    color: Colors.white,
                    border: Border.all(
                      color: Colors.blue,
                      width: 2.0,
                    ),
                  ),
                  child: Center(child: Text('实线边框')),
                ),
                
                // 虚线边框
                Container(
                  width: 100,
                  height: 100,
                  decoration: BoxDecoration(
                    color: Colors.white,
                    border: Border.all(
                      color: Colors.red,
                      width: 2.0,
                    ),
                  ),
                  foregroundDecoration: BoxDecoration(
                    border: Border.all(
                      color: Colors.red,
                      width: 2.0,
                      style: BorderStyle.none, // Flutter不直接支持虚线，需要自定义画布
                    ),
                  ),
                  child: Center(
                    child: CustomPaint(
                      size: Size(96, 96),
                      painter: DashedBorderPainter(),
                    ),
                  ),
                ),
                
                // 单边边框
                Container(
                  width: 100,
                  height: 100,
                  decoration: BoxDecoration(
                    color: Colors.white,
                    border: Border(
                      top: BorderSide(color: Colors.green, width: 4.0),
                      right: BorderSide(color: Colors.yellow, width: 2.0),
                      bottom: BorderSide(color: Colors.orange, width: 6.0),
                      left: BorderSide(color: Colors.purple, width: 3.0),
                    ),
                  ),
                  child: Center(child: Text('单边边框')),
                ),
              ],
            ),
            
            SizedBox(height: 30),
            
            // 2. 圆角边框
            Text('圆角边框', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
            SizedBox(height: 10),
            Row(
              mainAxisAlignment: MainAxisAlignment.spaceEvenly,
              children: [
                // 统一圆角边框
                Container(
                  width: 100,
                  height: 100,
                  decoration: BoxDecoration(
                    color: Colors.white,
                    border: Border.all(
                      color: Colors.blue,
                      width: 2.0,
                    ),
                    borderRadius: BorderRadius.circular(16.0),
                  ),
                  child: Center(child: Text('统一圆角')),
                ),
                
                // 不同圆角
                Container(
                  width: 100,
                  height: 100,
                  decoration: BoxDecoration(
                    color: Colors.white,
                    border: Border.all(
                      color: Colors.purple,
                      width: 2.0,
                    ),
                    borderRadius: BorderRadius.only(
                      topLeft: Radius.circular(24.0),
                      topRight: Radius.circular(8.0),
                      bottomLeft: Radius.circular(4.0),
                      bottomRight: Radius.circular(16.0),
                    ),
                  ),
                  child: Center(child: Text('不同圆角')),
                ),
                
                // 圆形边框
                Container(
                  width: 100,
                  height: 100,
                  decoration: BoxDecoration(
                    color: Colors.white,
                    border: Border.all(
                      color: Colors.orange,
                      width: 2.0,
                    ),
                    shape: BoxShape.circle,
                  ),
                  child: Center(child: Text('圆形边框')),
                ),
              ],
            ),
            
            SizedBox(height: 30),
            
            // 3. 响应式边框
            Text('响应式边框', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
            SizedBox(height: 10),
            Center(
              child: MouseRegion(
                onEnter: (_) => setState(() => _isHovered = true),
                onExit: (_) => setState(() => _isHovered = false),
                child: AnimatedContainer(
                  duration: Duration(milliseconds: 300),
                  width: _isHovered ? 220 : 200,
                  height: _isHovered ? 120 : 100,
                  decoration: BoxDecoration(
                    color: Colors.white,
                    border: Border.all(
                      color: _isHovered ? Colors.red : Colors.blue,
                      width: _isHovered ? 4.0 : 2.0,
                    ),
                    borderRadius: BorderRadius.circular(_isHovered ? 20.0 : 8.0),
                    boxShadow: _isHovered
                        ? [BoxShadow(
                            color: Colors.grey.withOpacity(0.5),
                            spreadRadius: 3,
                            blurRadius: 7,
                            offset: Offset(0, 3),
                          )]
                        : [],
                  ),
                  child: Center(
                    child: Text(
                      '悬停我',
                      style: TextStyle(
                        fontSize: _isHovered ? 20 : 16,
                      ),
                    ),
                  ),
                ),
              ),
            ),
            
            SizedBox(height: 30),
            
            // 4. 多边框
            Text('多边框', style: TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
            SizedBox(height: 10),
            Center(
              child: Container(
                width: 150,
                height: 150,
                decoration: BoxDecoration(
                  color: Colors.white,
                  border: Border.all(
                    color: Colors.red,
                    width: 5.0,
                  ),
                  borderRadius: BorderRadius.circular(20.0),
                ),
                padding: EdgeInsets.all(8.0),
                child: Container(
                  decoration: BoxDecoration(
                    color: Colors.white,
                    border: Border.all(
                      color: Colors.green,
                      width: 4.0,
                    ),
                    borderRadius: BorderRadius.circular(12.0),
                  ),
                  padding: EdgeInsets.all(8.0),
                  child: Container(
                    decoration: BoxDecoration(
                      color: Colors.white,
                      border: Border.all(
                        color: Colors.blue,
                        width: 3.0,
                      ),
                      borderRadius: BorderRadius.circular(4.0),
                    ),
                    child: Center(child: Text('多层边框')),
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

// 自定义绘制虚线边框
class DashedBorderPainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.red
      ..strokeWidth = 2.0
      ..style = PaintingStyle.stroke
      ..strokeCap = StrokeCap.round;
    
    final dashWidth = 5.0;
    final dashSpace = 5.0;
    
    double startX = 0;
    double startY = 0;
    
    // 绘制上边框
    while (startX < size.width) {
      canvas.drawLine(
        Offset(startX, startY),
        Offset(startX + dashWidth, startY),
        paint,
      );
      startX += dashWidth + dashSpace;
    }
    
    // 绘制右边框
    startX = size.width;
    startY = 0;
    while (startY < size.height) {
      canvas.drawLine(
        Offset(startX, startY),
        Offset(startX, startY + dashWidth),
        paint,
      );
      startY += dashWidth + dashSpace;
    }
    
    // 绘制下边框
    startX = size.width;
    startY = size.height;
    while (startX > 0) {
      canvas.drawLine(
        Offset(startX, startY),
        Offset(startX - dashWidth, startY),
        paint,
      );
      startX -= dashWidth + dashSpace;
    }
    
    // 绘制左边框
    startX = 0;
    startY = size.height;
    while (startY > 0) {
      canvas.drawLine(
        Offset(startX, startY),
        Offset(startX, startY - dashWidth),
        paint,
      );
      startY -= dashWidth + dashSpace;
    }
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}
```

### Jetpack Compose

```kotlin
import androidx.compose.animation.animateColorAsState
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.foundation.BorderStroke
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.CornerSize
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.Card
import androidx.compose.material.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.drawBehind
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.graphics.PathEffect
import androidx.compose.ui.graphics.drawscope.Stroke
import androidx.compose.ui.platform.LocalDensity
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun BorderDemoScreen() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        // 1. 基本边框样式
        Text(
            text = "基本边框样式",
            fontSize = 18.sp,
            fontWeight = FontWeight.Bold
        )
        
        Spacer(modifier = Modifier.height(10.dp))
        
        Row(
            modifier = Modifier.fillMaxWidth(),
            horizontalArrangement = Arrangement.SpaceEvenly
        ) {
            // 实线边框
            Box(
                modifier = Modifier
                    .size(100.dp)
                    .background(Color.White)
                    .border(
                        width = 2.dp,
                        color = Color.Blue
                    ),
                contentAlignment = Alignment.Center
            ) {
                Text(text = "实线边框")
            }
            
            // 虚线边框
            Box(
                modifier = Modifier
                    .size(100.dp)
                    .background(Color.White)
                    .drawBehind {
                        drawRect(
                            color = Color.Red,
                            style = Stroke(
                                width = 4f,
                                pathEffect = PathEffect.dashPathEffect(
                                    floatArrayOf(10f, 10f), 0f
                                )
                            )
                        )
                    },
                contentAlignment = Alignment.Center
            ) {
                Text(text = "虚线边框")
            }
            
            // 自定义边框颜色
            Box(
                modifier = Modifier
                    .size(100.dp)
                    .background(Color.White)
                    .border(
                        width = 4.dp,
                        color = Color(0xFF9C27B0) // 紫色
                    ),
                contentAlignment = Alignment.Center
            ) {
                Text(text = "自定义颜色")
            }
        }
        
        Spacer(modifier = Modifier.height(30.dp))
        
        // 2. 圆角边框
        Text(
            text = "圆角边框",
            fontSize = 18.sp,
            fontWeight = FontWeight.Bold
        )
        
        Spacer(modifier = Modifier.height(10.dp))
        
        Row(
            modifier = Modifier.fillMaxWidth(),
            horizontalArrangement = Arrangement.SpaceEvenly
        ) {
            // 统一圆角边框
            Box(
                modifier = Modifier
                    .size(100.dp)
                    .background(Color.White)
                    .border(
                        width = 2.dp,
                        color = Color.Blue,
                        shape = RoundedCornerShape(16.dp)
                    ),
                contentAlignment = Alignment.Center
            ) {
                Text(text = "统一圆角")
            }
            
            // 不同圆角
            Box(
                modifier = Modifier
                    .size(100.dp)
                    .background(Color.White)
                    .border(
                        width = 2.dp,
                        color = Color.Red,
                        shape = RoundedCornerShape(
                            topStart = 24.dp,
                            topEnd = 8.dp,
                            bottomStart = 4.dp,
                            bottomEnd = 16.dp
                        )
                    ),
                contentAlignment = Alignment.Center
            ) {
                Text(text = "不同圆角")
            }
            
            // 圆形边框
            Box(
                modifier = Modifier
                    .size(100.dp)
                    .background(Color.White)
                    .border(
                        width = 2.dp,
                        color = Color.Green,
                        shape = CircleShape
                    ),
                contentAlignment = Alignment.Center
            ) {
                Text(text = "圆形边框")
            }
        }
        
        Spacer(modifier = Modifier.height(30.dp))
        
        // 3. 响应式边框
        Text(
            text = "响应式边框",
            fontSize = 18.sp,
            fontWeight = FontWeight.Bold
        )
        
        Spacer(modifier = Modifier.height(10.dp))
        
        var isPressed by remember { mutableStateOf(false) }
        
        val borderColor by animateColorAsState(
            targetValue = if (isPressed) Color.Red else Color.Blue,
            label = "borderColor"
        )
        
        val borderWidth by animateDpAsState(
            targetValue = if (isPressed) 4.dp else 2.dp,
            label = "borderWidth"
        )
        
        val cornerRadius by animateDpAsState(
            targetValue = if (isPressed) 20.dp else 8.dp,
            label = "cornerRadius"
        )
        
        Box(
            modifier = Modifier.fillMaxWidth(),
            contentAlignment = Alignment.Center
        ) {
            Card(
                modifier = Modifier
                    .width(200.dp)
                    .height(100.dp)
                    .padding(4.dp),
                elevation = if (isPressed) 8.dp else 4.dp,
                shape = RoundedCornerShape(cornerRadius),
                border = BorderStroke(borderWidth, borderColor),
                backgroundColor = Color.White,
                onClick = { isPressed = !isPressed }
            ) {
                Box(
                    modifier = Modifier.fillMaxSize(),
                    contentAlignment = Alignment.Center
                ) {
                    Text(
                        text = "点击我",
                        fontSize = if (isPressed) 20.sp else 16.sp
                    )
                }
            }
        }
        
        Spacer(modifier = Modifier.height(30.dp))
        
        // 4. 多边框
        Text(
            text = "多边框",
            fontSize = 18.sp,
            fontWeight = FontWeight.Bold
        )
        
        Spacer(modifier = Modifier.height(10.dp))
        
        Box(
            modifier = Modifier.fillMaxWidth(),
            contentAlignment = Alignment.Center
        ) {
            // 外层边框
            Box(
                modifier = Modifier
                    .size(150.dp)
                    .border(
                        width = 5.dp,
                        color = Color.Red,
                        shape = RoundedCornerShape(20.dp)
                    )
                    .padding(8.dp),
                contentAlignment = Alignment.Center
            ) {
                // 中层边框
                Box(
                    modifier = Modifier
                        .fillMaxSize()
                        .border(
                            width = 4.dp,
                            color = Color.Green,
                            shape = RoundedCornerShape(12.dp)
                        )
                        .padding(8.dp),
                    contentAlignment = Alignment.Center
                ) {
                    // 内层边框
                    Box(
                        modifier = Modifier
                            .fillMaxSize()
                            .border(
                                width = 3.dp,
                                color = Color.Blue,
                                shape = RoundedCornerShape(4.dp)
                            ),
                        contentAlignment = Alignment.Center
                    ) {
                        Text(text = "多层边框")
                    }
                }
            }
        }
    }
}

@Preview
@Composable
fun BorderDemoScreenPreview() {
    BorderDemoScreen()
}
```

### UIKit

```swift
import UIKit

class BorderDemoViewController: UIViewController {
    
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
        title = "UIKit Border 示例"
        
        setupUI()
    }
    
    private func setupUI() {
        let scrollView = UIScrollView()
        scrollView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(scrollView)
        
        let contentView = UIView()
        contentView.translatesAutoresizingMaskIntoConstraints = false
        scrollView.addSubview(contentView)
        
        // 自动布局约束
        NSLayoutConstraint.activate([
            scrollView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
            scrollView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            scrollView.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            scrollView.bottomAnchor.constraint(equalTo: view.bottomAnchor),
            
            contentView.topAnchor.constraint(equalTo: scrollView.topAnchor),
            contentView.leadingAnchor.constraint(equalTo: scrollView.leadingAnchor),
            contentView.trailingAnchor.constraint(equalTo: scrollView.trailingAnchor),
            contentView.bottomAnchor.constraint(equalTo: scrollView.bottomAnchor),
            contentView.widthAnchor.constraint(equalTo: scrollView.widthAnchor)
        ])
        
        // 设置内容
        var lastAnchor = contentView.topAnchor
        let padding: CGFloat = 16.0
        
        // 1. 基本边框样式
        let basicBordersLabel = createSectionLabel(text: "基本边框样式")
        contentView.addSubview(basicBordersLabel)
        
        NSLayoutConstraint.activate([
            basicBordersLabel.topAnchor.constraint(equalTo: lastAnchor, constant: padding),
            basicBordersLabel.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            basicBordersLabel.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding)
        ])
        
        lastAnchor = basicBordersLabel.bottomAnchor
        
        // 基本边框示例容器
        let basicBorderContainer = UIStackView()
        basicBorderContainer.axis = .horizontal
        basicBorderContainer.distribution = .fillEqually
        basicBorderContainer.spacing = 16
        basicBorderContainer.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(basicBorderContainer)
        
        NSLayoutConstraint.activate([
            basicBorderContainer.topAnchor.constraint(equalTo: lastAnchor, constant: 10),
            basicBorderContainer.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            basicBorderContainer.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding),
            basicBorderContainer.heightAnchor.constraint(equalToConstant: 100)
        ])
        
        // 实线边框
        let solidBorderView = createBasicView(text: "实线边框")
        solidBorderView.layer.borderColor = UIColor.blue.cgColor
        solidBorderView.layer.borderWidth = 2.0
        basicBorderContainer.addArrangedSubview(solidBorderView)
        
        // 虚线边框
        let dashedBorderView = createBasicView(text: "虚线边框")
        basicBorderContainer.addArrangedSubview(dashedBorderView)
        
        // 添加虚线边框
        let dashedBorder = CAShapeLayer()
        dashedBorder.strokeColor = UIColor.red.cgColor
        dashedBorder.lineDashPattern = [5, 5]
        dashedBorder.frame = dashedBorderView.bounds
        dashedBorder.fillColor = nil
        dashedBorder.lineWidth = 2
        dashedBorder.path = UIBezierPath(rect: dashedBorderView.bounds).cgPath
        dashedBorderView.layer.addSublayer(dashedBorder)
        
        // 自定义边框(使用CAShapeLayer)
        let customBorderView = createBasicView(text: "自定义边框")
        basicBorderContainer.addArrangedSubview(customBorderView)
        
        // 添加自定义边框(每边不同颜色)
        let topBorder = CAShapeLayer()
        topBorder.strokeColor = UIColor.green.cgColor
        topBorder.lineWidth = 4
        let topPath = UIBezierPath()
        topPath.move(to: CGPoint(x: 0, y: 0))
        topPath.addLine(to: CGPoint(x: customBorderView.frame.width, y: 0))
        topBorder.path = topPath.cgPath
        customBorderView.layer.addSublayer(topBorder)
        
        let rightBorder = CAShapeLayer()
        rightBorder.strokeColor = UIColor.yellow.cgColor
        rightBorder.lineWidth = 2
        let rightPath = UIBezierPath()
        rightPath.move(to: CGPoint(x: customBorderView.frame.width, y: 0))
        rightPath.addLine(to: CGPoint(x: customBorderView.frame.width, y: customBorderView.frame.height))
        rightBorder.path = rightPath.cgPath
        customBorderView.layer.addSublayer(rightBorder)
        
        let bottomBorder = CAShapeLayer()
        bottomBorder.strokeColor = UIColor.orange.cgColor
        bottomBorder.lineWidth = 6
        let bottomPath = UIBezierPath()
        bottomPath.move(to: CGPoint(x: customBorderView.frame.width, y: customBorderView.frame.height))
        bottomPath.addLine(to: CGPoint(x: 0, y: customBorderView.frame.height))
        bottomBorder.path = bottomPath.cgPath
        customBorderView.layer.addSublayer(bottomBorder)
        
        let leftBorder = CAShapeLayer()
        leftBorder.strokeColor = UIColor.purple.cgColor
        leftBorder.lineWidth = 3
        let leftPath = UIBezierPath()
        leftPath.move(to: CGPoint(x: 0, y: customBorderView.frame.height))
        leftPath.addLine(to: CGPoint(x: 0, y: 0))
        leftBorder.path = leftPath.cgPath
        customBorderView.layer.addSublayer(leftBorder)
        
        lastAnchor = basicBorderContainer.bottomAnchor
        
        // 2. 圆角边框
        let roundedBordersLabel = createSectionLabel(text: "圆角边框")
        contentView.addSubview(roundedBordersLabel)
        
        NSLayoutConstraint.activate([
            roundedBordersLabel.topAnchor.constraint(equalTo: lastAnchor, constant: 30),
            roundedBordersLabel.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            roundedBordersLabel.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding)
        ])
        
        lastAnchor = roundedBordersLabel.bottomAnchor
        
        // 圆角边框示例容器
        let roundedBorderContainer = UIStackView()
        roundedBorderContainer.axis = .horizontal
        roundedBorderContainer.distribution = .fillEqually
        roundedBorderContainer.spacing = 16
        roundedBorderContainer.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(roundedBorderContainer)
        
        NSLayoutConstraint.activate([
            roundedBorderContainer.topAnchor.constraint(equalTo: lastAnchor, constant: 10),
            roundedBorderContainer.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            roundedBorderContainer.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding),
            roundedBorderContainer.heightAnchor.constraint(equalToConstant: 100)
        ])
        
        // 统一圆角边框
        let uniformRoundedView = createBasicView(text: "统一圆角")
        uniformRoundedView.layer.borderColor = UIColor.blue.cgColor
        uniformRoundedView.layer.borderWidth = 2.0
        uniformRoundedView.layer.cornerRadius = 16.0
        uniformRoundedView.layer.masksToBounds = true
        roundedBorderContainer.addArrangedSubview(uniformRoundedView)
        
        // 使用贝塞尔曲线创建不同圆角
        let customRoundedView = createBasicView(text: "不同圆角")
        roundedBorderContainer.addArrangedSubview(customRoundedView)
        
        let customRoundedBorder = CAShapeLayer()
        customRoundedBorder.strokeColor = UIColor.purple.cgColor
        customRoundedBorder.fillColor = UIColor.white.cgColor
        customRoundedBorder.lineWidth = 2
        
        let customRoundedPath = UIBezierPath(roundedRect: customRoundedView.bounds, 
                                            byRoundingCorners: [.topLeft, .bottomRight],
                                            cornerRadii: CGSize(width: 24.0, height: 24.0))
        customRoundedBorder.path = customRoundedPath.cgPath
        customRoundedView.layer.mask = customRoundedBorder
        customRoundedView.layer.borderColor = UIColor.purple.cgColor
        customRoundedView.layer.borderWidth = 2.0
        
        // 圆形边框
        let circleView = createBasicView(text: "圆形边框")
        circleView.layer.borderColor = UIColor.orange.cgColor
        circleView.layer.borderWidth = 2.0
        circleView.layer.cornerRadius = 50.0
        circleView.layer.masksToBounds = true
        roundedBorderContainer.addArrangedSubview(circleView)
        
        lastAnchor = roundedBorderContainer.bottomAnchor
        
        // 3. 响应式边框
        let responsiveBorderLabel = createSectionLabel(text: "响应式边框")
        contentView.addSubview(responsiveBorderLabel)
        
        NSLayoutConstraint.activate([
            responsiveBorderLabel.topAnchor.constraint(equalTo: lastAnchor, constant: 30),
            responsiveBorderLabel.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            responsiveBorderLabel.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding)
        ])
        
        lastAnchor = responsiveBorderLabel.bottomAnchor
        
        // 响应式边框容器
        let responsiveContainer = UIView()
        responsiveContainer.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(responsiveContainer)
        
        NSLayoutConstraint.activate([
            responsiveContainer.topAnchor.constraint(equalTo: lastAnchor, constant: 10),
            responsiveContainer.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            responsiveContainer.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding),
            responsiveContainer.heightAnchor.constraint(equalToConstant: 120)
        ])
        
        // 添加一个可点击的按钮
        let animatedButton = UIButton(type: .system)
        animatedButton.setTitle("点击我", for: .normal)
        animatedButton.backgroundColor = .white
        animatedButton.layer.borderColor = UIColor.blue.cgColor
        animatedButton.layer.borderWidth = 2.0
        animatedButton.layer.cornerRadius = 8.0
        animatedButton.translatesAutoresizingMaskIntoConstraints = false
        animatedButton.addTarget(self, action: #selector(animateBorder), for: .touchUpInside)
        responsiveContainer.addSubview(animatedButton)
        
        NSLayoutConstraint.activate([
            animatedButton.centerXAnchor.constraint(equalTo: responsiveContainer.centerXAnchor),
            animatedButton.centerYAnchor.constraint(equalTo: responsiveContainer.centerYAnchor),
            animatedButton.widthAnchor.constraint(equalToConstant: 200),
            animatedButton.heightAnchor.constraint(equalToConstant: 50)
        ])
        
        lastAnchor = responsiveContainer.bottomAnchor
        
        // 4. 多边框
        let multipleBordersLabel = createSectionLabel(text: "多边框")
        contentView.addSubview(multipleBordersLabel)
        
        NSLayoutConstraint.activate([
            multipleBordersLabel.topAnchor.constraint(equalTo: lastAnchor, constant: 30),
            multipleBordersLabel.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            multipleBordersLabel.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding)
        ])
        
        lastAnchor = multipleBordersLabel.bottomAnchor
        
        // 多边框容器
        let multipleBordersContainer = UIView()
        multipleBordersContainer.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(multipleBordersContainer)
        
        NSLayoutConstraint.activate([
            multipleBordersContainer.topAnchor.constraint(equalTo: lastAnchor, constant: 10),
            multipleBordersContainer.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            multipleBordersContainer.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding),
            multipleBordersContainer.heightAnchor.constraint(equalToConstant: 160),
            multipleBordersContainer.bottomAnchor.constraint(equalTo: contentView.bottomAnchor, constant: -padding)
        ])
        
        // 创建多层嵌套视图实现多边框
        let outerBorderView = UIView()
        outerBorderView.backgroundColor = .white
        outerBorderView.layer.borderColor = UIColor.red.cgColor
        outerBorderView.layer.borderWidth = 5.0
        outerBorderView.layer.cornerRadius = 20.0
        outerBorderView.translatesAutoresizingMaskIntoConstraints = false
        multipleBordersContainer.addSubview(outerBorderView)
        
        let middleBorderView = UIView()
        middleBorderView.backgroundColor = .white
        middleBorderView.layer.borderColor = UIColor.green.cgColor
        middleBorderView.layer.borderWidth = 4.0
        middleBorderView.layer.cornerRadius = 12.0
        middleBorderView.translatesAutoresizingMaskIntoConstraints = false
        outerBorderView.addSubview(middleBorderView)
        
        let innerBorderView = UIView()
        innerBorderView.backgroundColor = .white
        innerBorderView.layer.borderColor = UIColor.blue.cgColor
        innerBorderView.layer.borderWidth = 3.0
        innerBorderView.layer.cornerRadius = 4.0
        innerBorderView.translatesAutoresizingMaskIntoConstraints = false
        middleBorderView.addSubview(innerBorderView)
        
        let centerLabel = UILabel()
        centerLabel.text = "多层边框"
        centerLabel.textAlignment = .center
        centerLabel.translatesAutoresizingMaskIntoConstraints = false
        innerBorderView.addSubview(centerLabel)
        
        NSLayoutConstraint.activate([
            outerBorderView.centerXAnchor.constraint(equalTo: multipleBordersContainer.centerXAnchor),
            outerBorderView.centerYAnchor.constraint(equalTo: multipleBordersContainer.centerYAnchor),
            outerBorderView.widthAnchor.constraint(equalToConstant: 150),
            outerBorderView.heightAnchor.constraint(equalToConstant: 150),
            
            middleBorderView.topAnchor.constraint(equalTo: outerBorderView.topAnchor, constant: 8),
            middleBorderView.leadingAnchor.constraint(equalTo: outerBorderView.leadingAnchor, constant: 8),
            middleBorderView.trailingAnchor.constraint(equalTo: outerBorderView.trailingAnchor, constant: -8),
            middleBorderView.bottomAnchor.constraint(equalTo: outerBorderView.bottomAnchor, constant: -8),
            
            innerBorderView.topAnchor.constraint(equalTo: middleBorderView.topAnchor, constant: 8),
            innerBorderView.leadingAnchor.constraint(equalTo: middleBorderView.leadingAnchor, constant: 8),
            innerBorderView.trailingAnchor.constraint(equalTo: middleBorderView.trailingAnchor, constant: -8),
            innerBorderView.bottomAnchor.constraint(equalTo: middleBorderView.bottomAnchor, constant: -8),
            
            centerLabel.centerXAnchor.constraint(equalTo: innerBorderView.centerXAnchor),
            centerLabel.centerYAnchor.constraint(equalTo: innerBorderView.centerYAnchor)
        ])
    }
    
    @objc private func animateBorder(_ sender: UIButton) {
        UIView.animate(withDuration: 0.3) {
            if sender.layer.borderColor == UIColor.blue.cgColor {
                sender.layer.borderColor = UIColor.red.cgColor
                sender.layer.borderWidth = 4.0
                sender.layer.cornerRadius = 20.0
                sender.transform = CGAffineTransform(scaleX: 1.1, y: 1.1)
            } else {
                sender.layer.borderColor = UIColor.blue.cgColor
                sender.layer.borderWidth = 2.0
                sender.layer.cornerRadius = 8.0
                sender.transform = .identity
            }
        }
    }
    
    private func createSectionLabel(text: String) -> UILabel {
        let label = UILabel()
        label.text = text
        label.font = UIFont.systemFont(ofSize: 18, weight: .bold)
        label.translatesAutoresizingMaskIntoConstraints = false
        return label
    }
    
    private func createBasicView(text: String) -> UIView {
        let view = UIView()
        view.backgroundColor = .white
        
        let label = UILabel()
        label.text = text
        label.textAlignment = .center
        label.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(label)
        
        NSLayoutConstraint.activate([
            label.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            label.centerYAnchor.constraint(equalTo: view.centerYAnchor)
        ])
        
        return view
    }
}
```

### SwiftUI

```swift
import SwiftUI

struct BorderDemoView: View {
    @State private var isAnimated = false
    
    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 30) {
                // 1. 基本边框样式
                Text("基本边框样式")
                    .font(.headline)
                
                HStack(spacing: 16) {
                    // 实线边框
                    VStack {
                        Text("实线边框")
                            .frame(width: 100, height: 100)
                            .border(Color.blue, width: 2)
                    }
                    
                    // 虚线边框
                    VStack {
                        Text("虚线边框")
                            .frame(width: 100, height: 100)
                            .overlay(
                                Rectangle()
                                    .strokeBorder(style: StrokeStyle(
                                        lineWidth: 2,
                                        dash: [5, 5]
                                    ))
                                    .foregroundColor(.red)
                            )
                    }
                    
                    // 渐变边框
                    VStack {
                        Text("渐变边框")
                            .frame(width: 100, height: 100)
                            .overlay(
                                RoundedRectangle(cornerRadius: 0)
                                    .strokeBorder(
                                        LinearGradient(
                                            gradient: Gradient(colors: [.purple, .blue, .green]),
                                            startPoint: .topLeading,
                                            endPoint: .bottomTrailing
                                        ),
                                        lineWidth: 2
                                    )
                            )
                    }
                }
                
                // 2. 圆角边框
                Text("圆角边框")
                    .font(.headline)
                
                HStack(spacing: 16) {
                    // 统一圆角边框
                    VStack {
                        Text("统一圆角")
                            .frame(width: 100, height: 100)
                            .overlay(
                                RoundedRectangle(cornerRadius: 16)
                                    .stroke(Color.blue, lineWidth: 2)
                            )
                    }
                    
                    // 不同圆角
                    VStack {
                        Text("不同圆角")
                            .frame(width: 100, height: 100)
                            .overlay(
                                CustomCornerShape(
                                    topLeft: 24,
                                    topRight: 8,
                                    bottomLeft: 4,
                                    bottomRight: 16
                                )
                                .stroke(Color.purple, lineWidth: 2)
                            )
                    }
                    
                    // 圆形边框
                    VStack {
                        Text("圆形边框")
                            .frame(width: 100, height: 100)
                            .overlay(
                                Circle()
                                    .stroke(Color.orange, lineWidth: 2)
                            )
                    }
                }
                
                // 3. 响应式边框
                Text("响应式边框")
                    .font(.headline)
                
                VStack {
                    Button(action: {
                        withAnimation(.spring()) {
                            isAnimated.toggle()
                        }
                    }) {
                        Text("点击我")
                            .font(.system(size: isAnimated ? 20 : 16))
                            .frame(width: isAnimated ? 220 : 200, height: isAnimated ? 60 : 50)
                            .foregroundColor(.primary)
                            .background(Color.white)
                            .cornerRadius(isAnimated ? 20 : 8)
                            .overlay(
                                RoundedRectangle(cornerRadius: isAnimated ? 20 : 8)
                                    .stroke(isAnimated ? Color.red : Color.blue, 
                                           lineWidth: isAnimated ? 4 : 2)
                            )
                            .shadow(
                                color: isAnimated ? Color.gray.opacity(0.5) : Color.clear,
                                radius: isAnimated ? 5 : 0,
                                x: 0,
                                y: isAnimated ? 3 : 0
                            )
                    }
                    .buttonStyle(PlainButtonStyle())
                }
                .frame(maxWidth: .infinity)
                
                // 4. 多边框
                Text("多边框")
                    .font(.headline)
                
                VStack {
                    // 外层边框
                    ZStack {
                        RoundedRectangle(cornerRadius: 20)
                            .fill(Color.white)
                            .frame(width: 150, height: 150)
                            .overlay(
                                RoundedRectangle(cornerRadius: 20)
                                    .stroke(Color.red, lineWidth: 5)
                            )
                        
                        // 中层边框
                        RoundedRectangle(cornerRadius: 12)
                            .fill(Color.white)
                            .padding(8)
                            .overlay(
                                RoundedRectangle(cornerRadius: 12)
                                    .stroke(Color.green, lineWidth: 4)
                                    .padding(8)
                            )
                        
                        // 内层边框
                        RoundedRectangle(cornerRadius: 4)
                            .fill(Color.white)
                            .padding(24)
                            .overlay(
                                RoundedRectangle(cornerRadius: 4)
                                    .stroke(Color.blue, lineWidth: 3)
                                    .padding(24)
                            )
                        
                        Text("多层边框")
                    }
                }
                .frame(maxWidth: .infinity)
                
                Spacer()
            }
            .padding()
        }
        .navigationTitle("SwiftUI Border 示例")
    }
}

// 自定义圆角形状
struct CustomCornerShape: Shape {
    var topLeft: CGFloat = 0
    var topRight: CGFloat = 0
    var bottomLeft: CGFloat = 0
    var bottomRight: CGFloat = 0
    
    func path(in rect: CGRect) -> Path {
        var path = Path()
        
        let topRight = min(min(self.topRight, rect.width/2), rect.height/2)
        let topLeft = min(min(self.topLeft, rect.width/2), rect.height/2)
        let bottomLeft = min(min(self.bottomLeft, rect.width/2), rect.height/2)
        let bottomRight = min(min(self.bottomRight, rect.width/2), rect.height/2)
        
        path.move(to: CGPoint(x: rect.minX + topLeft, y: rect.minY))
        path.addLine(to: CGPoint(x: rect.maxX - topRight, y: rect.minY))
        path.addArc(center: CGPoint(x: rect.maxX - topRight, y: rect.minY + topRight),
                    radius: topRight,
                    startAngle: Angle(degrees: -90),
                    endAngle: Angle(degrees: 0),
                    clockwise: false)
        
        path.addLine(to: CGPoint(x: rect.maxX, y: rect.maxY - bottomRight))
        path.addArc(center: CGPoint(x: rect.maxX - bottomRight, y: rect.maxY - bottomRight),
                    radius: bottomRight,
                    startAngle: Angle(degrees: 0),
                    endAngle: Angle(degrees: 90),
                    clockwise: false)
        
        path.addLine(to: CGPoint(x: rect.minX + bottomLeft, y: rect.maxY))
        path.addArc(center: CGPoint(x: rect.minX + bottomLeft, y: rect.maxY - bottomLeft),
                    radius: bottomLeft,
                    startAngle: Angle(degrees: 90),
                    endAngle: Angle(degrees: 180),
                    clockwise: false)
        
        path.addLine(to: CGPoint(x: rect.minX, y: rect.minY + topLeft))
        path.addArc(center: CGPoint(x: rect.minX + topLeft, y: rect.minY + topLeft),
                    radius: topLeft,
                    startAngle: Angle(degrees: 180),
                    endAngle: Angle(degrees: 270),
                    clockwise: false)
        
        path.closeSubpath()
        
        return path
    }
}

struct BorderDemoView_Previews: PreviewProvider {
    static var previews: some View {
        BorderDemoView()
    }
}
```

### ArkUI (鸿蒙)

```typescript
@Entry
@Component
struct BorderDemoPage {
  @State isPressed: boolean = false;
  
  build() {
    Scroll() {
      Column({ space: 30 }) {
        // 1. 基本边框样式
        Text('基本边框样式')
          .fontSize(18)
          .fontWeight(FontWeight.Bold)
        
        Row({ space: 16 }) {
          // 实线边框
          Column() {
            Text('实线边框')
              .width(100)
              .height(100)
              .textAlign(TextAlign.Center)
              .border({
                width: 2,
                color: Color.Blue,
                style: BorderStyle.Solid
              })
          }
          
          // 虚线边框
          Column() {
            Text('虚线边框')
              .width(100)
              .height(100)
              .textAlign(TextAlign.Center)
              .border({
                width: 2,
                color: Color.Red,
                style: BorderStyle.Dashed
              })
          }
          
          // 点线边框
          Column() {
            Text('点线边框')
              .width(100)
              .height(100)
              .textAlign(TextAlign.Center)
              .border({
                width: 2,
                color: Color.Green,
                style: BorderStyle.Dotted
              })
          }
        }
        .width('100%')
        .justifyContent(FlexAlign.SpaceEvenly)
        
        // 2. 单边边框
        Text('单边边框')
          .fontSize(18)
          .fontWeight(FontWeight.Bold)
        
        Row({ space: 16 }) {
          // 上边框
          Column() {
            Text('上边框')
              .width(100)
              .height(100)
              .textAlign(TextAlign.Center)
              .borderWidth({
                top: 4
              })
              .borderColor({
                top: Color.Blue
              })
          }
          
          // 右边框
          Column() {
            Text('右边框')
              .width(100)
              .height(100)
              .textAlign(TextAlign.Center)
              .borderWidth({
                right: 4
              })
              .borderColor({
                right: Color.Red
              })
          }
          
          // 多边配置
          Column() {
            Text('多边配置')
              .width(100)
              .height(100)
              .textAlign(TextAlign.Center)
              .borderWidth({
                top: 4,
                right: 2,
                bottom: 6,
                left: 3
              })
              .borderColor({
                top: Color.Blue,
                right: Color.Red,
                bottom: Color.Green,
                left: Color.Orange
              })
          }
        }
        .width('100%')
        .justifyContent(FlexAlign.SpaceEvenly)
        
        // 3. 圆角边框
        Text('圆角边框')
          .fontSize(18)
          .fontWeight(FontWeight.Bold)
        
        Row({ space: 16 }) {
          // 统一圆角
          Column() {
            Text('统一圆角')
              .width(100)
              .height(100)
              .textAlign(TextAlign.Center)
              .border({
                width: 2,
                color: Color.Blue,
                radius: 16,
                style: BorderStyle.Solid
              })
          }
          
          // 不同圆角
          Column() {
            Text('不同圆角')
              .width(100)
              .height(100)
              .textAlign(TextAlign.Center)
              .border({
                width: 2,
                color: Color.Purple,
                radius: {
                  topLeft: 24,
                  topRight: 8,
                  bottomLeft: 4,
                  bottomRight: 16
                },
                style: BorderStyle.Solid
              })
          }
          
          // 圆形边框
          Column() {
            Text('圆形边框')
              .width(100)
              .height(100)
              .textAlign(TextAlign.Center)
              .border({
                width: 2,
                color: Color.Orange,
                radius: 50,
                style: BorderStyle.Solid
              })
          }
        }
        .width('100%')
        .justifyContent(FlexAlign.SpaceEvenly)
        
        // 4. 响应式边框
        Text('响应式边框')
          .fontSize(18)
          .fontWeight(FontWeight.Bold)
        
        Row() {
          Button('点击我')
            .width(this.isPressed ? 220 : 200)
            .height(this.isPressed ? 60 : 50)
            .fontSize(this.isPressed ? 20 : 16)
            .backgroundColor(Color.White)
            .border({
              width: this.isPressed ? 4 : 2,
              color: this.isPressed ? Color.Red : Color.Blue,
              radius: this.isPressed ? 20 : 8
            })
            .onClick(() => {
              this.isPressed = !this.isPressed;
              // 也可以使用animateTo实现动画过渡
            })
        }
        .width('100%')
        .justifyContent(FlexAlign.Center)
        
        // 5. 多重边框
        Text('多重边框')
          .fontSize(18)
          .fontWeight(FontWeight.Bold)
        
        Row() {
          Column() {
            Column() {
              Column() {
                Text('多层边框')
                  .width('100%')
                  .height('100%')
                  .textAlign(TextAlign.Center)
              }
              .width('100%')
              .height('100%')
              .border({
                width: 3,
                color: Color.Blue,
                radius: 4
              })
            }
            .width('80%')
            .height('80%')
            .border({
              width: 4,
              color: Color.Green,
              radius: 12
            })
          }
          .width(150)
          .height(150)
          .border({
            width: 5,
            color: Color.Red,
            radius: 20
          })
        }
        .width('100%')
        .justifyContent(FlexAlign.Center)
        .margin({ bottom: 20 })
        
        // 6. 状态响应边框
        Text('状态响应边框')
          .fontSize(18)
          .fontWeight(FontWeight.Bold)
        
        Row() {
          Button('悬停/按下效果')
            .width(200)
            .height(50)
            .backgroundColor(Color.White)
            .stateStyles({
              normal: {
                border: {
                  width: 2,
                  color: Color.Blue,
                  radius: 8
                }
              },
              pressed: {
                border: {
                  width: 4,
                  color: Color.Red,
                  radius: 8
                }
              },
              disabled: {
                border: {
                  width: 2,
                  color: Color.Gray,
                  radius: 8
                }
              }
            })
        }
        .width('100%')
        .justifyContent(FlexAlign.Center)
        .margin({ bottom: 40 })
      }
      .width('100%')
      .padding(16)
    }
    .backgroundColor('#F5F5F5')
  }
}
```

### Vue

```vue
<template>
  <div class="border-demo">
    <h1>Vue Border 示例</h1>
    
    <!-- 1. 基本边框样式 -->
    <h2>基本边框样式</h2>
    <div class="row">
      <!-- 实线边框 -->
      <div class="box solid-border">
        <span>实线边框</span>
      </div>
      
      <!-- 虚线边框 -->
      <div class="box dashed-border">
        <span>虚线边框</span>
      </div>
      
      <!-- 点线边框 -->
      <div class="box dotted-border">
        <span>点线边框</span>
      </div>
    </div>
    
    <!-- 2. 单边边框 -->
    <h2>单边边框</h2>
    <div class="row">
      <!-- 上边框 -->
      <div class="box top-border">
        <span>上边框</span>
      </div>
      
      <!-- 右边框 -->
      <div class="box right-border">
        <span>右边框</span>
      </div>
      
      <!-- 多边配置 -->
      <div class="box multi-sides-border">
        <span>多边配置</span>
      </div>
    </div>
    
    <!-- 3. 圆角边框 -->
    <h2>圆角边框</h2>
    <div class="row">
      <!-- 统一圆角 -->
      <div class="box uniform-rounded-border">
        <span>统一圆角</span>
      </div>
      
      <!-- 不同圆角 -->
      <div class="box custom-rounded-border">
        <span>不同圆角</span>
      </div>
      
      <!-- 圆形边框 -->
      <div class="box circle-border">
        <span>圆形边框</span>
      </div>
    </div>
    
    <!-- 4. 响应式边框 -->
    <h2>响应式边框</h2>
    <div class="row">
      <button 
        class="responsive-border" 
        :class="{ active: isActive }"
        @click="toggleActive"
      >
        点击我
      </button>
    </div>
    
    <!-- 5. 渐变边框 -->
    <h2>渐变边框</h2>
    <div class="row">
      <div class="box gradient-border">
        <span>渐变边框</span>
      </div>
      
      <!-- 使用CSS变量控制的边框 -->
      <div class="box variable-border" :style="variableBorderStyle">
        <span>CSS变量边框</span>
      </div>
      
      <!-- 动态边框效果 -->
      <div class="box animated-border">
        <span>动画边框</span>
      </div>
    </div>
    
    <!-- 6. 多重边框 -->
    <h2>多重边框</h2>
    <div class="row">
      <!-- 使用多重边框（border + outline） -->
      <div class="box multi-border-outline">
        <span>双重边框</span>
      </div>
      
      <!-- 使用box-shadow实现多重边框 -->
      <div class="box multi-border-shadow">
        <span>阴影边框</span>
      </div>
      
      <!-- 使用伪元素实现 -->
      <div class="box pseudo-border">
        <span>伪元素边框</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'BorderDemo',
  data() {
    return {
      isActive: false,
      borderWidth: '2px',
      borderColor: 'blue',
      borderRadius: '8px'
    }
  },
  computed: {
    variableBorderStyle() {
      return {
        '--border-width': this.borderWidth,
        '--border-color': this.borderColor,
        '--border-radius': this.borderRadius
      }
    }
  },
  methods: {
    toggleActive() {
      this.isActive = !this.isActive
      
      // 更新CSS变量边框
      this.borderWidth = this.isActive ? '4px' : '2px'
      this.borderColor = this.isActive ? 'red' : 'blue'
      this.borderRadius = this.isActive ? '20px' : '8px'
    }
  }
}
</script>

<style scoped>
.border-demo {
  max-width: 900px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

h1 {
  text-align: center;
  margin-bottom: 30px;
}

h2 {
  margin-top: 30px;
  margin-bottom: 15px;
  border-bottom: 1px solid #eee;
  padding-bottom: 5px;
}

.row {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
  flex-wrap: wrap;
}

.box {
  width: 100px;
  height: 100px;
  background-color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
  margin: 10px;
}

/* 基本边框样式 */
.solid-border {
  border: 2px solid blue;
}

.dashed-border {
  border: 2px dashed red;
}

.dotted-border {
  border: 2px dotted green;
}

/* 单边边框 */
.top-border {
  border-top: 4px solid blue;
}

.right-border {
  border-right: 4px solid red;
}

.multi-sides-border {
  border-top: 4px solid blue;
  border-right: 2px solid red;
  border-bottom: 6px solid green;
  border-left: 3px solid orange;
}

/* 圆角边框 */
.uniform-rounded-border {
  border: 2px solid blue;
  border-radius: 16px;
}

.custom-rounded-border {
  border: 2px solid purple;
  border-radius: 24px 8px 16px 4px; /* 左上 右上 右下 左下 */
}

.circle-border {
  border: 2px solid orange;
  border-radius: 50%;
}

/* 响应式边框 */
.responsive-border {
  width: 200px;
  height: 50px;
  background-color: white;
  border: 2px solid blue;
  border-radius: 8px;
  font-size: 16px;
  cursor: pointer;
  transition: all 0.3s ease;
  margin: 0 auto;
  display: block;
}

.responsive-border.active {
  border: 4px solid red;
  border-radius: 20px;
  width: 220px;
  height: 60px;
  font-size: 20px;
  box-shadow: 0 3px 7px rgba(0, 0, 0, 0.2);
}

/* 渐变边框 */
.gradient-border {
  position: relative;
  background: white;
  z-index: 1;
}

.gradient-border::before {
  content: '';
  position: absolute;
  top: -2px;
  left: -2px;
  right: -2px;
  bottom: -2px;
  background: linear-gradient(45deg, purple, blue, green);
  z-index: -1;
  border-radius: 5px;
}

/* CSS变量边框 */
.variable-border {
  border-width: var(--border-width);
  border-style: solid;
  border-color: var(--border-color);
  border-radius: var(--border-radius);
  transition: all 0.3s ease;
}

/* 动画边框 */
.animated-border {
  position: relative;
  background: white;
  overflow: hidden;
}

.animated-border::after {
  content: '';
  position: absolute;
  top: -2px;
  left: -2px;
  right: -2px;
  bottom: -2px;
  background: linear-gradient(45deg, red, blue, green, yellow);
  background-size: 400% 400%;
  z-index: -1;
  animation: gradient 3s ease infinite;
}

@keyframes gradient {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

/* 多重边框 */
.multi-border-outline {
  border: 5px solid red;
  outline: 3px solid blue;
  outline-offset: -15px;
}

.multi-border-shadow {
  background-color: white;
  box-shadow:
    0 0 0 5px red,
    0 0 0 10px white,
    0 0 0 15px green;
}

.pseudo-border {
  position: relative;
  border: 5px solid red;
}

.pseudo-border::after {
  content: '';
  position: absolute;
  top: 5px;
  left: 5px;
  right: 5px;
  bottom: 5px;
  border: 3px solid blue;
}

/* 响应式布局 */
@media (max-width: 768px) {
  .row {
    justify-content: center;
  }
}
</style> 