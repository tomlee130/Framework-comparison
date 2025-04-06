# 六大框架BoxDecoration组件比较

## 目录
- [装饰属性范围](#装饰属性范围)
- [多层装饰支持](#多层装饰支持)
- [渐变/阴影配置](#渐变阴影配置)
- [边框与圆角结合](#边框与圆角结合)
- [背景图像支持](#背景图像支持)
- [平台特性与兼容性](#平台特性与兼容性)
- [完整实例代码](#完整实例代码)

## 装饰属性范围

| 框架 | 装饰组件名称 | 支持属性 |
|------|------------|---------|
| Flutter | BoxDecoration | 颜色、渐变、边框、阴影、图像、形状、圆角、混合模式 |
| Jetpack Compose | Modifier组合 | 背景色、边框、形状、阴影、渐变、裁剪效果 |
| UIKit | CALayer属性 | 背景色、边框、阴影、内容图像、蒙版、变换 |
| SwiftUI | 各种修饰符组合 | 背景、前景、阴影、边框、圆角、混合模式、叠加效果、剪裁 |
| ArkUI (鸿蒙) | 声明式属性组合 | 背景色、阴影、渐变、边框、模糊效果 |
| Vue | CSS装饰属性 | 背景、边框、阴影、滤镜、变换、蒙版、剪裁路径 |

## 多层装饰支持

| 框架 | 多层支持方式 | 特点 |
|------|------------|------|
| Flutter | foregroundDecoration和decoration属性 | 支持前景和背景两层装饰，可以嵌套Container实现多层 |
| Jetpack Compose | 多个修饰符链式调用 | 支持无限层叠加修饰符，每个修饰符可以添加不同的视觉效果 |
| UIKit | sublayers和CALayer嵌套 | 通过添加子层可以实现复杂的多层效果，层级关系明确 |
| SwiftUI | 多个修饰符组合和ZStack | 可以通过多个修饰符叠加或使用ZStack实现复杂的多层视觉效果 |
| ArkUI | 多个装饰属性组合 | 支持多种装饰属性组合使用，可重叠应用不同效果 |
| Vue | CSS多重属性和伪元素 | 基于CSS的多层次渲染，可以使用::before和::after实现三层效果 |

## 渐变/阴影配置

| 框架 | 渐变类型 | 阴影配置 | 特点 |
|------|---------|---------|------|
| Flutter | 线性、径向、扫描渐变 | 可自定义偏移、模糊程度、扩散半径、颜色 | 渐变和阴影效果丰富，自定义程度高 |
| Jetpack Compose | 线性、径向、扫描渐变 | 可自定义高度、形状匹配、透明度、颜色 | 支持动态渐变和阴影动画，与Material Design紧密集成 |
| UIKit | CAGradientLayer支持线性和径向 | shadowOffset、shadowRadius、shadowOpacity、shadowPath | 性能优化好，支持复杂阴影路径 |
| SwiftUI | 线性、径向、角度渐变 | 支持内阴影、外阴影、颜色、半径、位置 | 声明式语法简洁，与系统主题无缝集成 |
| ArkUI | 线性、扫描渐变 | 支持高斯模糊、阴影方向、范围、颜色 | 针对不同设备形态优化，渲染效率高 |
| Vue | 丰富的CSS渐变 | 多重阴影、内外阴影、颜色过渡 | 基于CSS标准，浏览器兼容性好 |

## 边框与圆角结合

| 框架 | 边框类型 | 圆角配置 | 组合方式 |
|------|---------|---------|---------|
| Flutter | Border/BorderSide | BorderRadius | 在BoxDecoration中统一配置，支持单独设置四个角的圆角 |
| Jetpack Compose | Border修饰符 | RoundedCornerShape | 可以单独应用边框和形状，或使用border修饰符组合设置 |
| UIKit | layer.border属性 | layer.cornerRadius | 边框和圆角需分别设置，圆角裁剪内容需配置masksToBounds |
| SwiftUI | 边框修饰符 | 圆角修饰符 | 使用.border和.cornerRadius修饰符，或自定义Shape |
| ArkUI | border属性 | borderRadius属性 | 直接支持border和borderRadius一起配置 |
| Vue | CSS border属性 | border-radius属性 | 标准CSS属性，支持简写和详细配置 |

## 背景图像支持

| 框架 | 图像来源 | 填充模式 | 特殊效果 |
|------|---------|---------|---------|
| Flutter | 本地资源、网络、内存 | 填充、重复、适应、拉伸 | 支持混合模式、滤镜、位置调整 |
| Jetpack Compose | 本地资源、网络、Bitmap | 填充、裁剪、适应 | 支持ColorFilter、混合模式、滤镜效果 |
| UIKit | UIImage、模式图像 | 多种contentMode | 支持mask、CAFilter、Transform |
| SwiftUI | 本地资源、远程URL | 填充、适应、调整 | 支持resizable、aspectRatio、Frame调整 |
| ArkUI | 本地资源、网络资源 | 填充、重复、拉伸、等比缩放 | 支持mask、滤镜、背景模糊 |
| Vue | 本地、远程、Base64 | 丰富的background-size和position | 支持CSS滤镜、混合模式、蒙版 |

## 平台特性与兼容性

### Flutter
- **平台支持**: Android, iOS, Web, Windows, macOS, Linux
- **优势**: 统一API跨平台，装饰渲染一致性高
- **局限性**: 高度自定义装饰在低端设备可能有性能影响

### Jetpack Compose
- **平台支持**: Android, 桌面(实验阶段)
- **优势**: 与Android Material Design无缝集成，响应式UI更新
- **局限性**: 仅限Android生态，低版本Android支持有限

### UIKit
- **平台支持**: iOS, iPadOS
- **优势**: 原生性能，与Core Animation深度集成
- **局限性**: 复杂装饰需要组合多个CALayer，代码较冗长

### SwiftUI
- **平台支持**: iOS 13+, iPadOS, macOS, watchOS, tvOS
- **优势**: 声明式语法简洁，系统优化良好
- **局限性**: 需要较新的iOS版本，旧版装饰效果支持有限

### ArkUI (鸿蒙)
- **平台支持**: 鸿蒙OS设备
- **优势**: 针对多设备形态优化，流畅过渡支持
- **局限性**: 仅限鸿蒙生态，市场份额相对较小

### Vue
- **平台支持**: 各类浏览器平台
- **优势**: 基于标准CSS，装饰能力极其丰富
- **局限性**: 不同浏览器渲染差异需处理，复杂效果可能影响性能

## 完整实例代码

### Flutter

```dart
import 'package:flutter/material.dart';

class BoxDecorationDemo extends StatefulWidget {
  @override
  _BoxDecorationDemoState createState() => _BoxDecorationDemoState();
}

class _BoxDecorationDemoState extends State<BoxDecorationDemo> {
  bool _isHovered = false;
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Flutter BoxDecoration 示例')),
      body: SingleChildScrollView(
        padding: EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            // 1. 基础背景色装饰
            _buildSectionTitle('基础背景色'),
            SizedBox(height: 10),
            Container(
              width: double.infinity,
              height: 100,
              decoration: BoxDecoration(
                color: Colors.blue,
              ),
              child: Center(child: Text('纯色背景', style: TextStyle(color: Colors.white))),
            ),
            
            SizedBox(height: 30),
            
            // 2. 渐变装饰
            _buildSectionTitle('渐变效果'),
            SizedBox(height: 10),
            Row(
              children: [
                // 线性渐变
                Expanded(
                  child: Container(
                    height: 100,
                    decoration: BoxDecoration(
                      gradient: LinearGradient(
                        colors: [Colors.purple, Colors.blue],
                        begin: Alignment.topLeft,
                        end: Alignment.bottomRight,
                      ),
                    ),
                    child: Center(child: Text('线性渐变', style: TextStyle(color: Colors.white))),
                  ),
                ),
                SizedBox(width: 10),
                // 径向渐变
                Expanded(
                  child: Container(
                    height: 100,
                    decoration: BoxDecoration(
                      gradient: RadialGradient(
                        colors: [Colors.yellow, Colors.orange],
                        radius: 0.8,
                      ),
                    ),
                    child: Center(child: Text('径向渐变', style: TextStyle(color: Colors.white))),
                  ),
                ),
              ],
            ),
            SizedBox(height: 10),
            // 扫描渐变
            Container(
              width: double.infinity,
              height: 100,
              decoration: BoxDecoration(
                gradient: SweepGradient(
                  colors: [Colors.red, Colors.yellow, Colors.green, Colors.blue, Colors.purple, Colors.red],
                ),
              ),
              child: Center(child: Text('扫描渐变', style: TextStyle(color: Colors.white))),
            ),
            
            SizedBox(height: 30),
            
            // 3. 边框和圆角
            _buildSectionTitle('边框和圆角'),
            SizedBox(height: 10),
            Row(
              children: [
                // 基础边框
                Expanded(
                  child: Container(
                    height: 100,
                    decoration: BoxDecoration(
                      color: Colors.white,
                      border: Border.all(
                        color: Colors.blue,
                        width: 2.0,
                      ),
                    ),
                    child: Center(child: Text('基础边框')),
                  ),
                ),
                SizedBox(width: 10),
                // 圆角边框
                Expanded(
                  child: Container(
                    height: 100,
                    decoration: BoxDecoration(
                      color: Colors.white,
                      border: Border.all(
                        color: Colors.green,
                        width: 2.0,
                      ),
                      borderRadius: BorderRadius.circular(20.0),
                    ),
                    child: Center(child: Text('圆角边框')),
                  ),
                ),
              ],
            ),
            SizedBox(height: 10),
            // 不同圆角半径
            Container(
              width: double.infinity,
              height: 100,
              decoration: BoxDecoration(
                color: Colors.white,
                border: Border.all(
                  color: Colors.orange,
                  width: 2.0,
                ),
                borderRadius: BorderRadius.only(
                  topLeft: Radius.circular(30.0),
                  topRight: Radius.circular(10.0),
                  bottomLeft: Radius.circular(10.0),
                  bottomRight: Radius.circular(30.0),
                ),
              ),
              child: Center(child: Text('不同圆角半径')),
            ),
            
            SizedBox(height: 30),
            
            // 4. 阴影效果
            _buildSectionTitle('阴影效果'),
            SizedBox(height: 10),
            Container(
              width: double.infinity,
              height: 100,
              decoration: BoxDecoration(
                color: Colors.white,
                borderRadius: BorderRadius.circular(10.0),
                boxShadow: [
                  BoxShadow(
                    color: Colors.grey.withOpacity(0.5),
                    spreadRadius: 5,
                    blurRadius: 7,
                    offset: Offset(0, 3),
                  ),
                ],
              ),
              child: Center(child: Text('基础阴影')),
            ),
            SizedBox(height: 20),
            Container(
              width: double.infinity,
              height: 100,
              decoration: BoxDecoration(
                color: Colors.white,
                borderRadius: BorderRadius.circular(10.0),
                boxShadow: [
                  BoxShadow(
                    color: Colors.blue.withOpacity(0.3),
                    spreadRadius: 2,
                    blurRadius: 10,
                    offset: Offset(-5, -5),
                  ),
                  BoxShadow(
                    color: Colors.red.withOpacity(0.3),
                    spreadRadius: 2,
                    blurRadius: 10,
                    offset: Offset(5, 5),
                  ),
                ],
              ),
              child: Center(child: Text('多重阴影')),
            ),
            
            SizedBox(height: 30),
            
            // 5. 图像背景
            _buildSectionTitle('图像背景'),
            SizedBox(height: 10),
            Container(
              width: double.infinity,
              height: 150,
              decoration: BoxDecoration(
                image: DecorationImage(
                  image: NetworkImage('https://picsum.photos/800/400'),
                  fit: BoxFit.cover,
                ),
                borderRadius: BorderRadius.circular(10.0),
              ),
              child: Center(
                child: Text(
                  '覆盖模式',
                  style: TextStyle(
                    color: Colors.white,
                    fontSize: 20,
                    fontWeight: FontWeight.bold,
                    shadows: [
                      Shadow(
                        color: Colors.black,
                        offset: Offset(1, 1),
                        blurRadius: 3,
                      ),
                    ],
                  ),
                ),
              ),
            ),
            SizedBox(height: 10),
            Row(
              children: [
                // 重复平铺
                Expanded(
                  child: Container(
                    height: 150,
                    decoration: BoxDecoration(
                      image: DecorationImage(
                        image: NetworkImage('https://picsum.photos/100/100'),
                        repeat: ImageRepeat.repeat,
                      ),
                      borderRadius: BorderRadius.circular(10.0),
                    ),
                    child: Center(
                      child: Container(
                        padding: EdgeInsets.all(8),
                        color: Colors.black.withOpacity(0.5),
                        child: Text('平铺模式', style: TextStyle(color: Colors.white)),
                      ),
                    ),
                  ),
                ),
                SizedBox(width: 10),
                // 混合模式
                Expanded(
                  child: Container(
                    height: 150,
                    decoration: BoxDecoration(
                      color: Colors.yellow,
                      image: DecorationImage(
                        image: NetworkImage('https://picsum.photos/200/200'),
                        fit: BoxFit.cover,
                        colorFilter: ColorFilter.mode(
                          Colors.blue.withOpacity(0.5),
                          BlendMode.multiply,
                        ),
                      ),
                      borderRadius: BorderRadius.circular(10.0),
                    ),
                    child: Center(
                      child: Container(
                        padding: EdgeInsets.all(8),
                        color: Colors.black.withOpacity(0.5),
                        child: Text('混合模式', style: TextStyle(color: Colors.white)),
                      ),
                    ),
                  ),
                ),
              ],
            ),
            
            SizedBox(height: 30),
            
            // 6. 形状装饰
            _buildSectionTitle('形状装饰'),
            SizedBox(height: 10),
            Row(
              children: [
                // 圆形
                Expanded(
                  child: Container(
                    height: 150,
                    decoration: BoxDecoration(
                      color: Colors.green,
                      shape: BoxShape.circle,
                    ),
                    child: Center(child: Text('圆形', style: TextStyle(color: Colors.white))),
                  ),
                ),
                SizedBox(width: 10),
                // 矩形（有圆角）
                Expanded(
                  child: Container(
                    height: 150,
                    decoration: BoxDecoration(
                      color: Colors.purple,
                      borderRadius: BorderRadius.circular(20.0),
                    ),
                    child: Center(child: Text('圆角矩形', style: TextStyle(color: Colors.white))),
                  ),
                ),
              ],
            ),
            
            SizedBox(height: 30),
            
            // 7. 多层装饰
            _buildSectionTitle('多层装饰'),
            SizedBox(height: 10),
            Container(
              width: double.infinity,
              height: 150,
              decoration: BoxDecoration(
                gradient: LinearGradient(
                  colors: [Colors.blue.shade200, Colors.blue.shade800],
                  begin: Alignment.topLeft,
                  end: Alignment.bottomRight,
                ),
                borderRadius: BorderRadius.circular(10.0),
                boxShadow: [
                  BoxShadow(
                    color: Colors.blue.withOpacity(0.5),
                    spreadRadius: 3,
                    blurRadius: 7,
                    offset: Offset(0, 3),
                  ),
                ],
              ),
              foregroundDecoration: BoxDecoration(
                backgroundBlendMode: BlendMode.overlay,
                gradient: LinearGradient(
                  colors: [Colors.transparent, Colors.black.withOpacity(0.3)],
                  begin: Alignment.topCenter,
                  end: Alignment.bottomCenter,
                ),
                borderRadius: BorderRadius.circular(10.0),
              ),
              child: Center(
                child: Text(
                  '前景和背景装饰',
                  style: TextStyle(
                    color: Colors.white,
                    fontSize: 20,
                    fontWeight: FontWeight.bold,
                  ),
                ),
              ),
            ),
            
            SizedBox(height: 30),
            
            // 8. 响应式装饰
            _buildSectionTitle('响应式装饰'),
            SizedBox(height: 10),
            GestureDetector(
              onTap: () {
                setState(() {
                  _isHovered = !_isHovered;
                });
              },
              child: AnimatedContainer(
                duration: Duration(milliseconds: 300),
                width: double.infinity,
                height: 100,
                decoration: BoxDecoration(
                  color: _isHovered ? Colors.blue : Colors.white,
                  borderRadius: BorderRadius.circular(_isHovered ? 20.0 : 5.0),
                  border: Border.all(
                    color: _isHovered ? Colors.transparent : Colors.blue,
                    width: 2.0,
                  ),
                  boxShadow: _isHovered
                      ? [
                          BoxShadow(
                            color: Colors.blue.withOpacity(0.5),
                            spreadRadius: 3,
                            blurRadius: 10,
                            offset: Offset(0, 5),
                          )
                        ]
                      : [],
                ),
                child: Center(
                  child: Text(
                    '点击我',
                    style: TextStyle(
                      color: _isHovered ? Colors.white : Colors.blue,
                      fontSize: 18,
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
  
  Widget _buildSectionTitle(String title) {
    return Text(
      title,
      style: TextStyle(
        fontSize: 18,
        fontWeight: FontWeight.bold,
      ),
    );
  }
}
```

### Jetpack Compose

```kotlin
import androidx.compose.animation.animateColorAsState
import androidx.compose.animation.core.animateDpAsState
import androidx.compose.foundation.Image
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.Card
import androidx.compose.material.MaterialTheme
import androidx.compose.material.Surface
import androidx.compose.material.Text
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.draw.shadow
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.*
import androidx.compose.ui.graphics.painter.Painter
import androidx.compose.ui.layout.ContentScale
import androidx.compose.ui.res.painterResource
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import coil.compose.rememberImagePainter

@Composable
fun BoxDecorationDemo() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        // 1. 基础背景色
        SectionTitle(title = "基础背景色")
        Spacer(modifier = Modifier.height(10.dp))
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
                .background(color = Color.Blue),
            contentAlignment = Alignment.Center
        ) {
            Text(
                text = "纯色背景",
                color = Color.White
            )
        }
        
        Spacer(modifier = Modifier.height(30.dp))
        
        // 2. 渐变效果
        SectionTitle(title = "渐变效果")
        Spacer(modifier = Modifier.height(10.dp))
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
        ) {
            // 线性渐变
            Box(
                modifier = Modifier
                    .weight(1f)
                    .fillMaxHeight()
                    .background(
                        brush = Brush.linearGradient(
                            colors = listOf(Color(0xFF9C27B0), Color(0xFF2196F3)),
                            start = Offset(0f, 0f),
                            end = Offset(Float.POSITIVE_INFINITY, Float.POSITIVE_INFINITY)
                        )
                    ),
                contentAlignment = Alignment.Center
            ) {
                Text(
                    text = "线性渐变",
                    color = Color.White
                )
            }
            
            Spacer(modifier = Modifier.width(10.dp))
            
            // 径向渐变
            Box(
                modifier = Modifier
                    .weight(1f)
                    .fillMaxHeight()
                    .background(
                        brush = Brush.radialGradient(
                            colors = listOf(Color(0xFFFFEB3B), Color(0xFFFF9800)),
                            radius = 500f
                        )
                    ),
                contentAlignment = Alignment.Center
            ) {
                Text(
                    text = "径向渐变",
                    color = Color.White
                )
            }
        }
        
        Spacer(modifier = Modifier.height(10.dp))
        
        // 扫描渐变
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
                .background(
                    brush = Brush.sweepGradient(
                        colors = listOf(
                            Color.Red, Color.Yellow, Color.Green, 
                            Color.Blue, Color.Magenta, Color.Red
                        )
                    )
                ),
            contentAlignment = Alignment.Center
        ) {
            Text(
                text = "扫描渐变",
                color = Color.White
            )
        }
        
        Spacer(modifier = Modifier.height(30.dp))
        
        // 3. 边框和圆角
        SectionTitle(title = "边框和圆角")
        Spacer(modifier = Modifier.height(10.dp))
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
        ) {
            // 基础边框
            Box(
                modifier = Modifier
                    .weight(1f)
                    .fillMaxHeight()
                    .background(Color.White)
                    .border(2.dp, Color.Blue),
                contentAlignment = Alignment.Center
            ) {
                Text(text = "基础边框")
            }
            
            Spacer(modifier = Modifier.width(10.dp))
            
            // 圆角边框
            Box(
                modifier = Modifier
                    .weight(1f)
                    .fillMaxHeight()
                    .background(Color.White)
                    .clip(RoundedCornerShape(20.dp))
                    .border(2.dp, Color.Green, RoundedCornerShape(20.dp)),
                contentAlignment = Alignment.Center
            ) {
                Text(text = "圆角边框")
            }
        }
        
        Spacer(modifier = Modifier.height(10.dp))
        
        // 不同圆角半径
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
                .background(Color.White)
                .clip(
                    RoundedCornerShape(
                        topStart = 30.dp,
                        topEnd = 10.dp,
                        bottomStart = 10.dp,
                        bottomEnd = 30.dp
                    )
                )
                .border(
                    2.dp,
                    Color(0xFFFF9800),
                    RoundedCornerShape(
                        topStart = 30.dp,
                        topEnd = 10.dp,
                        bottomStart = 10.dp,
                        bottomEnd = 30.dp
                    )
                ),
            contentAlignment = Alignment.Center
        ) {
            Text(text = "不同圆角半径")
        }
        
        Spacer(modifier = Modifier.height(30.dp))
        
        // 4. 阴影效果
        SectionTitle(title = "阴影效果")
        Spacer(modifier = Modifier.height(10.dp))
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
                .shadow(
                    elevation = 10.dp,
                    shape = RoundedCornerShape(10.dp)
                )
                .background(
                    color = Color.White,
                    shape = RoundedCornerShape(10.dp)
                ),
            contentAlignment = Alignment.Center
        ) {
            Text(text = "基础阴影")
        }
        
        Spacer(modifier = Modifier.height(20.dp))
        
        // 自定义阴影（使用Surface实现）
        Surface(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp),
            shape = RoundedCornerShape(10.dp),
            color = Color.White,
            elevation = 10.dp
        ) {
            Box(
                contentAlignment = Alignment.Center
            ) {
                Text(text = "使用Surface的阴影")
            }
        }
        
        Spacer(modifier = Modifier.height(30.dp))
        
        // 5. 图像背景
        SectionTitle(title = "图像背景")
        Spacer(modifier = Modifier.height(10.dp))
        
        // 网络图像
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(150.dp)
                .clip(RoundedCornerShape(10.dp)),
            contentAlignment = Alignment.Center
        ) {
            Image(
                painter = rememberImagePainter(
                    data = "https://picsum.photos/800/400"
                ),
                contentDescription = "Cover Image",
                modifier = Modifier.fillMaxSize(),
                contentScale = ContentScale.Crop
            )
            Box(
                modifier = Modifier
                    .background(Color.Black.copy(alpha = 0.5f))
                    .padding(8.dp)
            ) {
                Text(
                    text = "覆盖模式",
                    color = Color.White,
                    fontSize = 20.sp,
                    fontWeight = FontWeight.Bold
                )
            }
        }
        
        Spacer(modifier = Modifier.height(30.dp))
        
        // 6. 多层装饰
        SectionTitle(title = "多层装饰")
        Spacer(modifier = Modifier.height(10.dp))
        
        // 多层修饰符的组合
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(150.dp)
                .shadow(
                    elevation = 10.dp,
                    shape = RoundedCornerShape(10.dp)
                )
                .clip(RoundedCornerShape(10.dp))
                .background(
                    brush = Brush.linearGradient(
                        colors = listOf(Color(0xFF90CAF9), Color(0xFF1565C0)),
                        start = Offset(0f, 0f),
                        end = Offset(Float.POSITIVE_INFINITY, Float.POSITIVE_INFINITY)
                    )
                )
                .border(
                    width = 2.dp,
                    color = Color.White.copy(alpha = 0.5f),
                    shape = RoundedCornerShape(10.dp)
                ),
            contentAlignment = Alignment.Center
        ) {
            Text(
                text = "多层修饰符组合",
                color = Color.White,
                fontSize = 18.sp,
                fontWeight = FontWeight.Bold
            )
        }
        
        Spacer(modifier = Modifier.height(30.dp))
        
        // 7. 响应式装饰
        SectionTitle(title = "响应式装饰")
        Spacer(modifier = Modifier.height(10.dp))
        
        var isHovered by remember { mutableStateOf(false) }
        
        val backgroundColor by animateColorAsState(
            targetValue = if (isHovered) Color.Blue else Color.White
        )
        
        val textColor by animateColorAsState(
            targetValue = if (isHovered) Color.White else Color.Blue
        )
        
        val cornerRadius by animateDpAsState(
            targetValue = if (isHovered) 20.dp else 5.dp
        )
        
        val elevation by animateDpAsState(
            targetValue = if (isHovered) 10.dp else 0.dp
        )
        
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .height(100.dp)
                .shadow(
                    elevation = elevation,
                    shape = RoundedCornerShape(cornerRadius)
                )
                .clip(RoundedCornerShape(cornerRadius))
                .background(backgroundColor)
                .border(
                    width = 2.dp,
                    color = if (isHovered) Color.Transparent else Color.Blue,
                    shape = RoundedCornerShape(cornerRadius)
                )
                .clickable {
                    isHovered = !isHovered
                },
            contentAlignment = Alignment.Center
        ) {
            Text(
                text = "点击我",
                color = textColor,
                fontSize = 18.sp
            )
        }
    }
}

@Composable
fun SectionTitle(title: String) {
    Text(
        text = title,
        fontSize = 18.sp,
        fontWeight = FontWeight.Bold
    )
}

@Preview(showBackground = true)
@Composable
fun PreviewBoxDecorationDemo() {
    MaterialTheme {
        BoxDecorationDemo()
    }
}
```

### UIKit

```swift
import UIKit

class BoxDecorationViewController: UIViewController {
    
    override func viewDidLoad() {
        super.viewDidLoad()
        view.backgroundColor = .white
        title = "UIKit BoxDecoration 示例"
        
        setupUI()
    }
    
    private func setupUI() {
        let scrollView = UIScrollView()
        scrollView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(scrollView)
        
        let contentView = UIView()
        contentView.translatesAutoresizingMaskIntoConstraints = false
        scrollView.addSubview(contentView)
        
        // 布局约束
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
        
        let padding: CGFloat = 16.0
        var lastView: UIView?
        
        // 1. 基础背景色
        let basicTitleLabel = createSectionTitle(text: "基础背景色")
        contentView.addSubview(basicTitleLabel)
        
        NSLayoutConstraint.activate([
            basicTitleLabel.topAnchor.constraint(equalTo: contentView.topAnchor, constant: padding),
            basicTitleLabel.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            basicTitleLabel.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding)
        ])
        
        let basicColorView = UIView()
        basicColorView.backgroundColor = .systemBlue
        basicColorView.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(basicColorView)
        
        let basicLabel = UILabel()
        basicLabel.text = "纯色背景"
        basicLabel.textColor = .white
        basicLabel.textAlignment = .center
        basicLabel.translatesAutoresizingMaskIntoConstraints = false
        basicColorView.addSubview(basicLabel)
        
        NSLayoutConstraint.activate([
            basicColorView.topAnchor.constraint(equalTo: basicTitleLabel.bottomAnchor, constant: 10),
            basicColorView.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            basicColorView.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding),
            basicColorView.heightAnchor.constraint(equalToConstant: 100),
            
            basicLabel.centerXAnchor.constraint(equalTo: basicColorView.centerXAnchor),
            basicLabel.centerYAnchor.constraint(equalTo: basicColorView.centerYAnchor)
        ])
        
        lastView = basicColorView
        
        // 2. 渐变效果
        let gradientTitleLabel = createSectionTitle(text: "渐变效果")
        contentView.addSubview(gradientTitleLabel)
        
        NSLayoutConstraint.activate([
            gradientTitleLabel.topAnchor.constraint(equalTo: lastView!.bottomAnchor, constant: 30),
            gradientTitleLabel.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            gradientTitleLabel.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding)
        ])
        
        let gradientContainer = UIView()
        gradientContainer.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(gradientContainer)
        
        NSLayoutConstraint.activate([
            gradientContainer.topAnchor.constraint(equalTo: gradientTitleLabel.bottomAnchor, constant: 10),
            gradientContainer.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            gradientContainer.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding),
            gradientContainer.heightAnchor.constraint(equalToConstant: 100)
        ])
        
        // 线性渐变
        let linearGradientView = UIView()
        linearGradientView.translatesAutoresizingMaskIntoConstraints = false
        gradientContainer.addSubview(linearGradientView)
        
        let linearGradientLayer = CAGradientLayer()
        linearGradientLayer.colors = [UIColor.systemPurple.cgColor, UIColor.systemBlue.cgColor]
        linearGradientLayer.startPoint = CGPoint(x: 0, y: 0)
        linearGradientLayer.endPoint = CGPoint(x: 1, y: 1)
        linearGradientView.layer.addSublayer(linearGradientLayer)
        
        let linearLabel = UILabel()
        linearLabel.text = "线性渐变"
        linearLabel.textColor = .white
        linearLabel.textAlignment = .center
        linearLabel.translatesAutoresizingMaskIntoConstraints = false
        linearGradientView.addSubview(linearLabel)
        
        // 径向渐变
        let radialGradientView = UIView()
        radialGradientView.translatesAutoresizingMaskIntoConstraints = false
        gradientContainer.addSubview(radialGradientView)
        
        let radialGradientLayer = CAGradientLayer()
        radialGradientLayer.type = .radial
        radialGradientLayer.colors = [UIColor.systemYellow.cgColor, UIColor.systemOrange.cgColor]
        radialGradientLayer.startPoint = CGPoint(x: 0.5, y: 0.5)
        radialGradientLayer.endPoint = CGPoint(x: 1, y: 1)
        radialGradientView.layer.addSublayer(radialGradientLayer)
        
        let radialLabel = UILabel()
        radialLabel.text = "径向渐变"
        radialLabel.textColor = .white
        radialLabel.textAlignment = .center
        radialLabel.translatesAutoresizingMaskIntoConstraints = false
        radialGradientView.addSubview(radialLabel)
        
        NSLayoutConstraint.activate([
            linearGradientView.leadingAnchor.constraint(equalTo: gradientContainer.leadingAnchor),
            linearGradientView.topAnchor.constraint(equalTo: gradientContainer.topAnchor),
            linearGradientView.bottomAnchor.constraint(equalTo: gradientContainer.bottomAnchor),
            linearGradientView.widthAnchor.constraint(equalTo: gradientContainer.widthAnchor, multiplier: 0.48),
            
            linearLabel.centerXAnchor.constraint(equalTo: linearGradientView.centerXAnchor),
            linearLabel.centerYAnchor.constraint(equalTo: linearGradientView.centerYAnchor),
            
            radialGradientView.trailingAnchor.constraint(equalTo: gradientContainer.trailingAnchor),
            radialGradientView.topAnchor.constraint(equalTo: gradientContainer.topAnchor),
            radialGradientView.bottomAnchor.constraint(equalTo: gradientContainer.bottomAnchor),
            radialGradientView.widthAnchor.constraint(equalTo: gradientContainer.widthAnchor, multiplier: 0.48),
            
            radialLabel.centerXAnchor.constraint(equalTo: radialGradientView.centerXAnchor),
            radialLabel.centerYAnchor.constraint(equalTo: radialGradientView.centerYAnchor)
        ])
        
        lastView = gradientContainer
        
        // 3. 边框与圆角
        let borderTitleLabel = createSectionTitle(text: "边框与圆角")
        contentView.addSubview(borderTitleLabel)
        
        NSLayoutConstraint.activate([
            borderTitleLabel.topAnchor.constraint(equalTo: lastView!.bottomAnchor, constant: 30),
            borderTitleLabel.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            borderTitleLabel.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding)
        ])
        
        // 基础边框
        let baseBorderView = UIView()
        baseBorderView.backgroundColor = .white
        baseBorderView.layer.borderWidth = 2
        baseBorderView.layer.borderColor = UIColor.systemBlue.cgColor
        baseBorderView.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(baseBorderView)
        
        let baseBorderLabel = UILabel()
        baseBorderLabel.text = "基础边框"
        baseBorderLabel.textAlignment = .center
        baseBorderLabel.translatesAutoresizingMaskIntoConstraints = false
        baseBorderView.addSubview(baseBorderLabel)
        
        // 圆角边框
        let roundedBorderView = UIView()
        roundedBorderView.backgroundColor = .white
        roundedBorderView.layer.borderWidth = 2
        roundedBorderView.layer.borderColor = UIColor.systemGreen.cgColor
        roundedBorderView.layer.cornerRadius = 20
        roundedBorderView.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(roundedBorderView)
        
        let roundedBorderLabel = UILabel()
        roundedBorderLabel.text = "圆角边框"
        roundedBorderLabel.textAlignment = .center
        roundedBorderLabel.translatesAutoresizingMaskIntoConstraints = false
        roundedBorderView.addSubview(roundedBorderLabel)
        
        NSLayoutConstraint.activate([
            baseBorderView.topAnchor.constraint(equalTo: borderTitleLabel.bottomAnchor, constant: 10),
            baseBorderView.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            baseBorderView.widthAnchor.constraint(equalTo: contentView.widthAnchor, multiplier: 0.45, constant: -padding),
            baseBorderView.heightAnchor.constraint(equalToConstant: 100),
            
            baseBorderLabel.centerXAnchor.constraint(equalTo: baseBorderView.centerXAnchor),
            baseBorderLabel.centerYAnchor.constraint(equalTo: baseBorderView.centerYAnchor),
            
            roundedBorderView.topAnchor.constraint(equalTo: borderTitleLabel.bottomAnchor, constant: 10),
            roundedBorderView.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding),
            roundedBorderView.widthAnchor.constraint(equalTo: contentView.widthAnchor, multiplier: 0.45, constant: -padding),
            roundedBorderView.heightAnchor.constraint(equalToConstant: 100),
            
            roundedBorderLabel.centerXAnchor.constraint(equalTo: roundedBorderView.centerXAnchor),
            roundedBorderLabel.centerYAnchor.constraint(equalTo: roundedBorderView.centerYAnchor)
        ])
        
        // 自定义圆角
        let customCornerView = UIView()
        customCornerView.backgroundColor = .white
        customCornerView.layer.borderWidth = 2
        customCornerView.layer.borderColor = UIColor.systemOrange.cgColor
        customCornerView.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(customCornerView)
        
        // 使用贝塞尔路径创建自定义圆角形状
        let customCornerPath = UIBezierPath(
            roundedRect: CGRect(x: 0, y: 0, width: 343, height: 100),
            byRoundingCorners: [.topLeft, .bottomRight],
            cornerRadii: CGSize(width: 30, height: 30)
        )
        let customCornerMask = CAShapeLayer()
        customCornerMask.path = customCornerPath.cgPath
        customCornerView.layer.mask = customCornerMask
        
        let customCornerLabel = UILabel()
        customCornerLabel.text = "自定义圆角"
        customCornerLabel.textAlignment = .center
        customCornerLabel.translatesAutoresizingMaskIntoConstraints = false
        customCornerView.addSubview(customCornerLabel)
        
        NSLayoutConstraint.activate([
            customCornerView.topAnchor.constraint(equalTo: baseBorderView.bottomAnchor, constant: 10),
            customCornerView.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            customCornerView.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding),
            customCornerView.heightAnchor.constraint(equalToConstant: 100),
            
            customCornerLabel.centerXAnchor.constraint(equalTo: customCornerView.centerXAnchor),
            customCornerLabel.centerYAnchor.constraint(equalTo: customCornerView.centerYAnchor)
        ])
        
        lastView = customCornerView
        
        // 4. 阴影效果
        let shadowTitleLabel = createSectionTitle(text: "阴影效果")
        contentView.addSubview(shadowTitleLabel)
        
        NSLayoutConstraint.activate([
            shadowTitleLabel.topAnchor.constraint(equalTo: lastView!.bottomAnchor, constant: 30),
            shadowTitleLabel.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            shadowTitleLabel.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding)
        ])
        
        // 基础阴影
        let shadowView = UIView()
        shadowView.backgroundColor = .white
        shadowView.layer.cornerRadius = 10
        shadowView.layer.shadowColor = UIColor.black.cgColor
        shadowView.layer.shadowOffset = CGSize(width: 0, height: 3)
        shadowView.layer.shadowRadius = 5
        shadowView.layer.shadowOpacity = 0.3
        shadowView.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(shadowView)
        
        let shadowLabel = UILabel()
        shadowLabel.text = "基础阴影"
        shadowLabel.textAlignment = .center
        shadowLabel.translatesAutoresizingMaskIntoConstraints = false
        shadowView.addSubview(shadowLabel)
        
        NSLayoutConstraint.activate([
            shadowView.topAnchor.constraint(equalTo: shadowTitleLabel.bottomAnchor, constant: 10),
            shadowView.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            shadowView.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding),
            shadowView.heightAnchor.constraint(equalToConstant: 100),
            
            shadowLabel.centerXAnchor.constraint(equalTo: shadowView.centerXAnchor),
            shadowLabel.centerYAnchor.constraint(equalTo: shadowView.centerYAnchor)
        ])
        
        // 复杂阴影效果
        let complexShadowView = UIView()
        complexShadowView.backgroundColor = .white
        complexShadowView.layer.cornerRadius = 20
        
        // 使用自定义阴影路径
        let shadowPath = UIBezierPath(roundedRect: CGRect(x: 0, y: 0, width: 343, height: 100), cornerRadius: 20)
        complexShadowView.layer.shadowPath = shadowPath.cgPath
        complexShadowView.layer.shadowColor = UIColor.systemBlue.cgColor
        complexShadowView.layer.shadowOffset = CGSize(width: 0, height: 10)
        complexShadowView.layer.shadowRadius = 15
        complexShadowView.layer.shadowOpacity = 0.5
        complexShadowView.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(complexShadowView)
        
        let complexShadowLabel = UILabel()
        complexShadowLabel.text = "自定义阴影路径"
        complexShadowLabel.textAlignment = .center
        complexShadowLabel.translatesAutoresizingMaskIntoConstraints = false
        complexShadowView.addSubview(complexShadowLabel)
        
        NSLayoutConstraint.activate([
            complexShadowView.topAnchor.constraint(equalTo: shadowView.bottomAnchor, constant: 20),
            complexShadowView.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            complexShadowView.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding),
            complexShadowView.heightAnchor.constraint(equalToConstant: 100),
            
            complexShadowLabel.centerXAnchor.constraint(equalTo: complexShadowView.centerXAnchor),
            complexShadowLabel.centerYAnchor.constraint(equalTo: complexShadowView.centerYAnchor)
        ])
        
        lastView = complexShadowView
        
        // 5. 背景图像
        let imageTitleLabel = createSectionTitle(text: "背景图像")
        contentView.addSubview(imageTitleLabel)
        
        NSLayoutConstraint.activate([
            imageTitleLabel.topAnchor.constraint(equalTo: lastView!.bottomAnchor, constant: 30),
            imageTitleLabel.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            imageTitleLabel.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding)
        ])
        
        // 图像背景视图
        let imageBackgroundView = UIView()
        imageBackgroundView.layer.cornerRadius = 10
        imageBackgroundView.layer.masksToBounds = true
        imageBackgroundView.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(imageBackgroundView)
        
        // 使用UIImageView作为背景
        let backgroundImageView = UIImageView()
        backgroundImageView.image = UIImage(named: "demo_image")
        backgroundImageView.contentMode = .scaleAspectFill
        backgroundImageView.translatesAutoresizingMaskIntoConstraints = false
        imageBackgroundView.addSubview(backgroundImageView)
        
        // 添加文本标签
        let imageLabel = UILabel()
        imageLabel.text = "图像背景"
        imageLabel.textColor = .white
        imageLabel.textAlignment = .center
        imageLabel.font = UIFont.boldSystemFont(ofSize: 18)
        imageLabel.translatesAutoresizingMaskIntoConstraints = false
        imageBackgroundView.addSubview(imageLabel)
        
        NSLayoutConstraint.activate([
            imageBackgroundView.topAnchor.constraint(equalTo: imageTitleLabel.bottomAnchor, constant: 10),
            imageBackgroundView.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            imageBackgroundView.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding),
            imageBackgroundView.heightAnchor.constraint(equalToConstant: 150),
            
            backgroundImageView.topAnchor.constraint(equalTo: imageBackgroundView.topAnchor),
            backgroundImageView.leadingAnchor.constraint(equalTo: imageBackgroundView.leadingAnchor),
            backgroundImageView.trailingAnchor.constraint(equalTo: imageBackgroundView.trailingAnchor),
            backgroundImageView.bottomAnchor.constraint(equalTo: imageBackgroundView.bottomAnchor),
            
            imageLabel.centerXAnchor.constraint(equalTo: imageBackgroundView.centerXAnchor),
            imageLabel.centerYAnchor.constraint(equalTo: imageBackgroundView.centerYAnchor)
        ])
        
        lastView = imageBackgroundView
        
        // 6. 混合模式
        let blendTitleLabel = createSectionTitle(text: "混合模式与蒙版")
        contentView.addSubview(blendTitleLabel)
        
        NSLayoutConstraint.activate([
            blendTitleLabel.topAnchor.constraint(equalTo: lastView!.bottomAnchor, constant: 30),
            blendTitleLabel.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            blendTitleLabel.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding)
        ])
        
        // 蒙版效果
        let maskView = UIView()
        maskView.backgroundColor = .systemBlue
        maskView.layer.cornerRadius = 10
        maskView.translatesAutoresizingMaskIntoConstraints = false
        contentView.addSubview(maskView)
        
        // 创建蒙版
        let maskLayer = CAShapeLayer()
        maskLayer.path = UIBezierPath(text: "MASK", font: UIFont.boldSystemFont(ofSize: 40))?.cgPath
        maskLayer.position = CGPoint(x: 171.5, y: 50)
        maskView.layer.mask = maskLayer
        
        NSLayoutConstraint.activate([
            maskView.topAnchor.constraint(equalTo: blendTitleLabel.bottomAnchor, constant: 10),
            maskView.leadingAnchor.constraint(equalTo: contentView.leadingAnchor, constant: padding),
            maskView.trailingAnchor.constraint(equalTo: contentView.trailingAnchor, constant: -padding),
            maskView.heightAnchor.constraint(equalToConstant: 100),
            maskView.bottomAnchor.constraint(equalTo: contentView.bottomAnchor, constant: -padding)
        ])
    }
    
    private func createSectionTitle(text: String) -> UILabel {
        let label = UILabel()
        label.text = text
        label.font = UIFont.boldSystemFont(ofSize: 18)
        label.translatesAutoresizingMaskIntoConstraints = false
        return label
    }
}

extension UIBezierPath {
    // 辅助方法：从文本创建路径
    convenience init?(text: String, font: UIFont) {
        let fontRef = CTFontCreateWithName(font.fontName as CFString, font.pointSize, nil)
        let attrString = NSAttributedString(string: text, attributes: [.font: fontRef])
        let line = CTLineCreateWithAttributedString(attrString)
        let runArray = CTLineGetGlyphRuns(line)
        
        self.init()
        
        for runIndex in 0..<CFArrayGetCount(runArray) {
            let run = unsafeBitCast(CFArrayGetValueAtIndex(runArray, runIndex), to: CTRun.self)
            let runFont = unsafeBitCast(CFDictionaryGetValue(CTRunGetAttributes(run), Unmanaged.passUnretained(kCTFontAttributeName).toOpaque()), to: CTFont.self)
            
            for runGlyphIndex in 0..<CTRunGetGlyphCount(run) {
                let glyph = UnsafeMutablePointer<CGGlyph>.allocate(capacity: 1)
                let position = UnsafeMutablePointer<CGPoint>.allocate(capacity: 1)
                
                CTRunGetGlyphs(run, CFRangeMake(runGlyphIndex, 1), glyph)
                CTRunGetPositions(run, CFRangeMake(runGlyphIndex, 1), position)
                
                if let path = CTFontCreatePathForGlyph(runFont, glyph.pointee, nil) {
                    let transform = CGAffineTransform(translationX: position.pointee.x, y: position.pointee.y)
                    append(UIBezierPath(cgPath: path), transform: transform)
                }
                
                glyph.deallocate()
                position.deallocate()
            }
        }
        
        return self.isEmpty ? nil : self
    }
}
```

### SwiftUI

```swift
import SwiftUI

struct BoxDecorationDemo: View {
    @State private var isAnimating = false
    
    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 30) {
                // 标题
                Text("SwiftUI 装饰示例")
                    .font(.largeTitle)
                    .fontWeight(.bold)
                
                // 1. 基础背景色
                sectionTitle("基础背景色")
                RoundedRectangle(cornerRadius: 0)
                    .fill(Color.blue)
                    .frame(height: 100)
                    .overlay(
                        Text("纯色背景")
                            .foregroundColor(.white)
                    )
                
                // 2. 渐变效果
                sectionTitle("渐变效果")
                HStack {
                    // 线性渐变
                    RoundedRectangle(cornerRadius: 0)
                        .fill(
                            LinearGradient(
                                gradient: Gradient(colors: [.purple, .blue]),
                                startPoint: .topLeading,
                                endPoint: .bottomTrailing
                            )
                        )
                        .frame(height: 100)
                        .overlay(
                            Text("线性渐变")
                                .foregroundColor(.white)
                        )
                    
                    // 径向渐变
                    RoundedRectangle(cornerRadius: 0)
                        .fill(
                            RadialGradient(
                                gradient: Gradient(colors: [.yellow, .orange]),
                                center: .center,
                                startRadius: 0,
                                endRadius: 100
                            )
                        )
                        .frame(height: 100)
                        .overlay(
                            Text("径向渐变")
                                .foregroundColor(.white)
                        )
                }
                
                // 角度渐变
                RoundedRectangle(cornerRadius: 0)
                    .fill(
                        AngularGradient(
                            gradient: Gradient(colors: [.red, .yellow, .green, .blue, .purple, .red]),
                            center: .center
                        )
                    )
                    .frame(height: 100)
                    .overlay(
                        Text("角度渐变")
                            .foregroundColor(.white)
                    )
                
                // 3. 边框和圆角
                sectionTitle("边框和圆角")
                HStack {
                    // 基础边框
                    RoundedRectangle(cornerRadius: 0)
                        .fill(Color.white)
                        .frame(height: 100)
                        .overlay(
                            RoundedRectangle(cornerRadius: 0)
                                .stroke(Color.blue, lineWidth: 2)
                        )
                        .overlay(
                            Text("基础边框")
                        )
                    
                    // 圆角边框
                    RoundedRectangle(cornerRadius: 20)
                        .fill(Color.white)
                        .frame(height: 100)
                        .overlay(
                            RoundedRectangle(cornerRadius: 20)
                                .stroke(Color.green, lineWidth: 2)
                        )
                        .overlay(
                            Text("圆角边框")
                        )
                }
                
                // 不规则圆角
                customCornerShape
                    .fill(Color.white)
                    .frame(height: 100)
                    .overlay(
                        customCornerShape
                            .stroke(Color.orange, lineWidth: 2)
                    )
                    .overlay(
                        Text("不同圆角半径")
                    )
                
                // 4. 阴影效果
                sectionTitle("阴影效果")
                RoundedRectangle(cornerRadius: 15)
                    .fill(Color.white)
                    .frame(height: 100)
                    .shadow(color: .gray.opacity(0.7), radius: 10, x: 0, y: 5)
                    .overlay(
                        Text("基础阴影")
                    )
                
                // 立体阴影
                RoundedRectangle(cornerRadius: 15)
                    .fill(
                        LinearGradient(
                            gradient: Gradient(colors: [.blue.opacity(0.8), .purple.opacity(0.8)]),
                            startPoint: .top,
                            endPoint: .bottom
                        )
                    )
                    .frame(height: 100)
                    .shadow(color: .blue.opacity(0.3), radius: 5, x: -5, y: -5)
                    .shadow(color: .purple.opacity(0.5), radius: 5, x: 5, y: 5)
                    .overlay(
                        Text("立体阴影")
                            .foregroundColor(.white)
                    )
                
                // 5. 背景图像
                sectionTitle("背景图像")
                ZStack {
                    Image("background_image") // 请替换为实际图像资源
                        .resizable()
                        .aspectRatio(contentMode: .fill)
                        .frame(height: 150)
                        .clipped()
                    
                    Text("背景图像")
                        .font(.title)
                        .fontWeight(.bold)
                        .foregroundColor(.white)
                        .padding()
                        .background(Color.black.opacity(0.5))
                        .cornerRadius(10)
                }
                
                // 图像混合模式
                ZStack {
                    Image("texture") // 请替换为实际图像资源
                        .resizable()
                        .aspectRatio(contentMode: .fill)
                        .frame(height: 150)
                        .blendMode(.overlay)
                    
                    RoundedRectangle(cornerRadius: 0)
                        .fill(
                            LinearGradient(
                                gradient: Gradient(colors: [.blue, .purple]),
                                startPoint: .leading,
                                endPoint: .trailing
                            )
                        )
                        .opacity(0.8)
                    
                    Text("图像混合模式")
                        .font(.title2)
                        .fontWeight(.bold)
                        .foregroundColor(.white)
                }
                
                // 6. 多层装饰
                sectionTitle("多层装饰")
                Text("多层装饰效果")
                    .font(.headline)
                    .padding()
                    .background(
                        RoundedRectangle(cornerRadius: 10)
                            .fill(Color.white)
                    )
                    .overlay(
                        RoundedRectangle(cornerRadius: 10)
                            .stroke(
                                LinearGradient(
                                    gradient: Gradient(colors: [.blue, .purple]),
                                    startPoint: .leading,
                                    endPoint: .trailing
                                ),
                                lineWidth: 2
                            )
                    )
                    .shadow(color: .gray.opacity(0.5), radius: 5, x: 0, y: 2)
                    .padding()
                    .background(
                        RoundedRectangle(cornerRadius: 20)
                            .fill(Color.gray.opacity(0.1))
                    )
                    .padding(.bottom, 50)
            }
            .padding()
        }
    }
    
    // 辅助视图
    func sectionTitle(_ title: String) -> some View {
        Text(title)
            .font(.headline)
            .padding(.vertical, 5)
    }
    
    // 自定义不规则圆角形状
    var customCornerShape: some Shape {
        RoundedCornerShape(
            topLeft: 30,
            topRight: 10,
            bottomLeft: 10,
            bottomRight: 30
        )
    }
}

// 自定义不规则圆角形状定义
struct RoundedCornerShape: Shape {
    var topLeft: CGFloat = 0
    var topRight: CGFloat = 0
    var bottomLeft: CGFloat = 0
    var bottomRight: CGFloat = 0
    
    func path(in rect: CGRect) -> Path {
        var path = Path()
        
        let width = rect.size.width
        let height = rect.size.height
        
        // 左上角
        path.move(to: CGPoint(x: 0, y: topLeft))
        path.addArc(center: CGPoint(x: topLeft, y: topLeft),
                    radius: topLeft,
                    startAngle: Angle(degrees: 180),
                    endAngle: Angle(degrees: 270),
                    clockwise: false)
        
        // 右上角
        path.addLine(to: CGPoint(x: width - topRight, y: 0))
        path.addArc(center: CGPoint(x: width - topRight, y: topRight),
                    radius: topRight,
                    startAngle: Angle(degrees: 270),
                    endAngle: Angle(degrees: 0),
                    clockwise: false)
        
        // 右下角
        path.addLine(to: CGPoint(x: width, y: height - bottomRight))
        path.addArc(center: CGPoint(x: width - bottomRight, y: height - bottomRight),
                    radius: bottomRight,
                    startAngle: Angle(degrees: 0),
                    endAngle: Angle(degrees: 90),
                    clockwise: false)
        
        // 左下角
        path.addLine(to: CGPoint(x: bottomLeft, y: height))
        path.addArc(center: CGPoint(x: bottomLeft, y: height - bottomLeft),
                    radius: bottomLeft,
                    startAngle: Angle(degrees: 90),
                    endAngle: Angle(degrees: 180),
                    clockwise: false)
        
        // 封闭路径
        path.closeSubpath()
        
        return path
    }
}

struct BoxDecorationDemo_Previews: PreviewProvider {
    static var previews: some View {
        BoxDecorationDemo()
    }
}
```

### ArkUI (鸿蒙)

```java
import com.huawei.arkui.layout.Box;
import com.huawei.arkui.layout.BoxDecoration;
import com.huawei.arkui.layout.Border;
import com.huawei.arkui.layout.BorderRadius;
import com.huawei.arkui.layout.Gradient;
import com.huawei.arkui.layout.Image;
import com.huawei.arkui.layout.LinearGradient;
import com.huawei.arkui.layout.RadialGradient;
import com.huawei.arkui.layout.RoundedCornerShape;
import com.huawei.arkui.layout.Shadow;
import com.huawei.arkui.layout.SweepGradient;
import com.huawei.arkui.layout.Text;
import com.huawei.arkui.layout.TextStyle;
import com.huawei.arkui.layout.background.Background;
import com.huawei.arkui.layout.background.BackgroundBlendMode;
import com.huawei.arkui.layout.background.BackgroundImage;
import com.huawei.arkui.layout.background.BackgroundRepeat;
import com.huawei.arkui.layout.background.BackgroundSize;
import com.huawei.arkui.layout.background.ColorFilter;
import com.huawei.arkui.layout.background.ImageRepeat;
import com.huawei.arkui.layout.background.LinearGradient;
import com.huawei.arkui.layout.background.RadialGradient;
import com.huawei.arkui.layout.background.SweepGradient;
import com.huawei.arkui.layout.background.background.Background;
## 完整实例代码 