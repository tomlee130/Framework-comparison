# 六大框架 TextOverflow 组件比较

本文档对比Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue六大框架中TextOverflow组件的实现和特性，从以下维度进行分析：

- 溢出处理策略
- 截断方式
- 省略号配置
- 多行溢出处理
- 自适应高度支持

每个框架部分包含平台特性、兼容性分析和完整实例代码。

## 目录

1. [Flutter TextOverflow](#flutter-textoverflow)
2. [Android Jetpack Compose TextOverflow](#android-jetpack-compose-textoverflow)
3. [iOS UIKit TextOverflow](#ios-uikit-textoverflow)
4. [iOS SwiftUI TextOverflow](#ios-swiftui-textoverflow)
5. [鸿蒙 ArkUI TextOverflow](#鸿蒙-arkui-textoverflow)
6. [Vue TextOverflow](#vue-textoverflow)
7. [总结对比](#总结对比)

## Flutter TextOverflow

### 溢出处理策略

Flutter通过`TextOverflow`枚举提供明确的文本溢出处理方式，主要包括：

- `TextOverflow.clip`：直接裁剪溢出部分，不显示任何提示
- `TextOverflow.fade`：将文本末尾淡出，产生渐变消失效果
- `TextOverflow.ellipsis`：在末尾显示省略号(...)
- `TextOverflow.visible`：不处理溢出，允许文本超出容器边界

这些策略应用于`Text`组件的`overflow`属性，或通过`DefaultTextStyle`应用于子树。

### 截断方式

- 截断发生在字符边界，不会在字符中间截断
- 可通过`maxLines`属性指定最大行数，控制截断发生的位置
- 可以通过`softWrap`属性控制是否自动换行，false时文本将不换行并在宽度限制处截断
- 不提供位置自定义（如中间或开头截断），仅支持末尾截断

### 省略号配置

- 省略号样式是固定的"..."，不可自定义文本或样式
- 省略号继承文本原有样式（颜色、字体等）
- 不支持自定义省略号位置（仅支持末尾）
- 可以通过RichText组件和自定义TextSpan组合实现更复杂的省略号效果

### 多行溢出处理

- 通过`maxLines`属性指定文本最大行数
- 当指定`maxLines`且文本超出时，根据`overflow`策略处理最后一行
- 多行文本中，只有最后一行会应用省略号或淡出效果
- 无法为不同行指定不同的溢出处理策略

### 自适应高度支持

- 可以通过不指定`maxLines`实现高度自适应，但会失去溢出控制
- 结合`FittedBox`可实现文本缩放以适应容器
- 通过`TextScaler`可以调整文本缩放比例适应不同设备
- 支持通过`strutStyle`精确控制文本行高

### 平台特性与兼容性

- 在所有Flutter支持的平台（Android、iOS、Web、桌面）上行为一致
- 在不同设备密度和分辨率下渲染一致
- 高性能实现，即使处理大量文本也保持流畅
- 支持国际化和不同书写系统（如RTL语言）
- Flutter 3.0后通过TextScaler替代textScaleFactor提供更好的文本缩放支持

### 完整示例代码

```dart
import 'package:flutter/material.dart';

class TextOverflowExample extends StatelessWidget {
  final String shortText = "这是一个简短的文本示例";
  final String longText = "这是一个非常长的文本示例，用于演示Flutter中的文本溢出处理功能。"
      "我们将看到不同的溢出处理策略，如裁剪、淡出和省略号。"
      "此外，还会展示多行文本溢出以及自适应高度的处理方式。"
      "通过这个例子，我们可以理解Flutter中TextOverflow的各种用法和场景。";

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Flutter TextOverflow 示例'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: SingleChildScrollView(
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              _buildSectionTitle('1. 溢出处理策略'),
              
              _buildLabel('TextOverflow.clip（裁剪）:'),
              _buildConstrainedBox(
                child: Text(
                  longText,
                  overflow: TextOverflow.clip,
                  maxLines: 1,
                ),
              ),
              
              SizedBox(height: 16),
              _buildLabel('TextOverflow.ellipsis（省略号）:'),
              _buildConstrainedBox(
                child: Text(
                  longText,
                  overflow: TextOverflow.ellipsis,
                  maxLines: 1,
                ),
              ),
              
              SizedBox(height: 16),
              _buildLabel('TextOverflow.fade（淡出）:'),
              _buildConstrainedBox(
                child: Text(
                  longText,
                  overflow: TextOverflow.fade,
                  maxLines: 1,
                ),
              ),
              
              SizedBox(height: 16),
              _buildLabel('TextOverflow.visible（可见）:'),
              Container(
                color: Colors.grey.withOpacity(0.2),
                width: 300,
                padding: EdgeInsets.all(8),
                child: Text(
                  longText,
                  overflow: TextOverflow.visible,
                  maxLines: 1,
                ),
              ),
              
              _buildSectionTitle('2. 截断方式'),
              
              _buildLabel('指定maxLines=2:'),
              _buildConstrainedBox(
                child: Text(
                  longText,
                  overflow: TextOverflow.ellipsis,
                  maxLines: 2,
                ),
              ),
              
              SizedBox(height: 16),
              _buildLabel('softWrap=false（不换行）:'),
              _buildConstrainedBox(
                child: Text(
                  longText,
                  overflow: TextOverflow.ellipsis,
                  softWrap: false,
                ),
              ),
              
              _buildSectionTitle('3. 省略号配置'),
              
              _buildLabel('默认省略号样式:'),
              _buildConstrainedBox(
                child: Text(
                  longText,
                  overflow: TextOverflow.ellipsis,
                  maxLines: 1,
                  style: TextStyle(
                    color: Colors.blue,
                    fontWeight: FontWeight.bold,
                  ),
                ),
              ),
              
              SizedBox(height: 16),
              _buildLabel('自定义省略号效果(使用RichText):'),
              _buildConstrainedBox(
                child: _buildCustomEllipsisText(),
              ),
              
              _buildSectionTitle('4. 多行溢出处理'),
              
              _buildLabel('多行省略号(maxLines=3):'),
              _buildConstrainedBox(
                height: 80,
                child: Text(
                  longText,
                  overflow: TextOverflow.ellipsis,
                  maxLines: 3,
                ),
              ),
              
              _buildSectionTitle('5. 自适应高度支持'),
              
              _buildLabel('自适应高度(不限制maxLines):'),
              _buildConstrainedBox(
                width: 300,
                height: null,
                child: Text(
                  longText,
                ),
              ),
              
              SizedBox(height: 16),
              _buildLabel('使用FittedBox缩放文本:'),
              Container(
                width: 200,
                height: 40,
                color: Colors.grey.withOpacity(0.2),
                child: FittedBox(
                  fit: BoxFit.scaleDown,
                  alignment: Alignment.centerLeft,
                  child: Text(longText),
                ),
              ),
              
              SizedBox(height: 16),
              _buildLabel('结合TextScaler调整文本大小:'),
              _buildConstrainedBox(
                child: Text(
                  longText,
                  maxLines: 2,
                  overflow: TextOverflow.ellipsis,
                  textScaler: TextScaler.linear(0.8),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }

  Widget _buildSectionTitle(String title) {
    return Padding(
      padding: const EdgeInsets.symmetric(vertical: 16.0),
      child: Text(
        title,
        style: TextStyle(
          fontSize: 18,
          fontWeight: FontWeight.bold,
        ),
      ),
    );
  }

  Widget _buildLabel(String label) {
    return Padding(
      padding: const EdgeInsets.only(bottom: 8.0),
      child: Text(
        label,
        style: TextStyle(
          fontWeight: FontWeight.w500,
        ),
      ),
    );
  }

  Widget _buildConstrainedBox({
    required Widget child,
    double width = 300,
    double? height,
  }) {
    return Container(
      width: width,
      height: height,
      padding: EdgeInsets.all(8),
      decoration: BoxDecoration(
        color: Colors.grey.withOpacity(0.2),
        borderRadius: BorderRadius.circular(4),
      ),
      child: child,
    );
  }

  // 自定义省略号文本
  Widget _buildCustomEllipsisText() {
    return LayoutBuilder(
      builder: (context, constraints) {
        TextPainter textPainter = TextPainter(
          text: TextSpan(text: longText),
          maxLines: 1,
          textDirection: TextDirection.ltr,
        );
        textPainter.layout(maxWidth: constraints.maxWidth);
        
        if (textPainter.didExceedMaxLines) {
          return RichText(
            overflow: TextOverflow.visible,
            maxLines: 1,
            text: TextSpan(
              style: TextStyle(color: Colors.black),
              children: [
                TextSpan(
                  text: longText.substring(0, longText.length ~/ 2),
                ),
                TextSpan(
                  text: ' ... ',
                  style: TextStyle(
                    fontWeight: FontWeight.bold,
                    color: Colors.red,
                  ),
                ),
                TextSpan(
                  text: longText.substring(longText.length - 10),
                ),
              ],
            ),
          );
        }
        return Text(longText);
      },
    );
  }
}

## Android Jetpack Compose TextOverflow

### 溢出处理策略

Jetpack Compose使用`TextOverflow`枚举来控制文本溢出行为，主要包括：

- `TextOverflow.Clip`：直接裁剪溢出文本，不显示任何提示
- `TextOverflow.Ellipsis`：在末尾显示省略号(...)
- `TextOverflow.Visible`：不处理溢出，允许文本超出容器边界

这些策略通过Text组件的`overflow`参数应用。相比Flutter，Compose不提供直接的淡出效果，但可以通过自定义装饰器实现。

### 截断方式

- 截断默认发生在单词边界，保证可读性
- 通过`maxLines`参数控制最大行数
- 可以使用`softWrap`参数控制是否换行，为false时强制单行
- 支持`textAlign`参数控制文本对齐方式，影响截断时的文本位置
- 提供`TextLayoutResult`可以获取更多关于截断的细节信息
- 不支持中间或开头截断，只能在末尾截断

### 省略号配置

- 省略号显示为标准的"..."，默认继承文本样式
- 不支持直接自定义省略号文本或样式
- 可以通过自定义文本绘制修饰器(drawBehind)实现特殊省略号效果
- 可以使用Modifier和Canvas自定义尾部渐变或特殊效果
- 不支持直接设置省略号位置，只能在文本末尾显示

### 多行溢出处理

- 使用`maxLines`参数限制最大行数
- 多行溢出时仅在最后一行末尾显示省略号
- 通过`onTextLayout`回调可以检测具体哪一行发生了溢出
- 可以使用`lineHeight`控制行高，影响多行溢出的视觉效果
- 不支持为不同行指定不同的溢出处理方式

### 自适应高度支持

- 默认状态下，Text组件会根据内容自适应高度
- 可以结合`height`参数和`wrapContentHeight`设置或约束高度
- 可以使用`fontSize`和`fontSizeRange`参数实现自适应字体大小
- 结合`BoxWithConstraints`可以响应容器约束实现更灵活的自适应
- 支持使用`fontScale`根据用户系统设置调整文本大小

### 平台特性与兼容性

- 专为Android平台优化，与Material Design风格一致
- 支持Android特有的字体渲染和排版特性
- 在不同Android设备和屏幕密度上表现一致
- 支持自动适应系统字体大小设置
- 支持夜间模式和动态主题变化
- 实验性的支持桌面平台（如Windows、macOS）
- 需要Android API 21 (Android 5.0)及以上版本

### 完整示例代码

```kotlin
import androidx.compose.foundation.background
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.drawWithContent
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.TextLayoutResult
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.text.style.TextOverflow
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

@Composable
fun TextOverflowExample() {
    val shortText = "这是一个简短的文本示例"
    val longText = "这是一个非常长的文本示例，用于演示Jetpack Compose中的文本溢出处理功能。" +
            "我们将看到不同的溢出处理策略，如裁剪和省略号。" +
            "此外，还会展示多行文本溢出以及自适应高度的处理方式。" +
            "通过这个例子，我们可以理解Compose中TextOverflow的各种用法和场景。"
    
    var isTextOverflowed by remember { mutableStateOf(false) }
    
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Jetpack Compose TextOverflow") }
            )
        }
    ) { paddingValues ->
        Column(
            modifier = Modifier
                .padding(paddingValues)
                .padding(16.dp)
                .fillMaxSize()
                .verticalScroll(rememberScrollState())
        ) {
            SectionTitle(title = "1. 溢出处理策略")
            
            SubtitleText(text = "TextOverflow.Clip（裁剪）:")
            Box(
                modifier = Modifier
                    .width(300.dp)
                    .background(Color.LightGray.copy(alpha = 0.3f))
                    .padding(8.dp)
            ) {
                Text(
                    text = longText,
                    maxLines = 1,
                    overflow = TextOverflow.Clip
                )
            }
            
            Spacer(modifier = Modifier.height(16.dp))
            SubtitleText(text = "TextOverflow.Ellipsis（省略号）:")
            Box(
                modifier = Modifier
                    .width(300.dp)
                    .background(Color.LightGray.copy(alpha = 0.3f))
                    .padding(8.dp)
            ) {
                Text(
                    text = longText,
                    maxLines = 1,
                    overflow = TextOverflow.Ellipsis
                )
            }
            
            Spacer(modifier = Modifier.height(16.dp))
            SubtitleText(text = "TextOverflow.Visible（可见）:")
            Box(
                modifier = Modifier
                    .width(300.dp)
                    .background(Color.LightGray.copy(alpha = 0.3f))
                    .padding(8.dp)
            ) {
                Text(
                    text = longText,
                    maxLines = 1,
                    overflow = TextOverflow.Visible
                )
            }
            
            Spacer(modifier = Modifier.height(16.dp))
            SubtitleText(text = "自定义淡出效果:")
            Box(
                modifier = Modifier
                    .width(300.dp)
                    .background(Color.LightGray.copy(alpha = 0.3f))
                    .padding(8.dp)
            ) {
                Text(
                    text = longText,
                    maxLines = 1,
                    modifier = Modifier.drawWithContent {
                        drawContent()
                        // 在内容右侧添加渐变淡出效果
                        drawRect(
                            brush = Brush.horizontalGradient(
                                colors = listOf(
                                    Color.Transparent,
                                    Color.White.copy(alpha = 0.8f)
                                ),
                                startX = size.width - 50f,
                                endX = size.width
                            ),
                            size = size.copy(width = 50f),
                            topLeft = Offset(size.width - 50f, 0f)
                        )
                    }
                )
            }
            
            SectionTitle(title = "2. 截断方式")
            
            SubtitleText(text = "指定maxLines=2:")
            Box(
                modifier = Modifier
                    .width(300.dp)
                    .background(Color.LightGray.copy(alpha = 0.3f))
                    .padding(8.dp)
            ) {
                Text(
                    text = longText,
                    maxLines = 2,
                    overflow = TextOverflow.Ellipsis
                )
            }
            
            Spacer(modifier = Modifier.height(16.dp))
            SubtitleText(text = "设置softWrap=false（不换行）:")
            Box(
                modifier = Modifier
                    .width(300.dp)
                    .background(Color.LightGray.copy(alpha = 0.3f))
                    .padding(8.dp)
            ) {
                Text(
                    text = longText,
                    softWrap = false,
                    overflow = TextOverflow.Ellipsis
                )
            }
            
            Spacer(modifier = Modifier.height(16.dp))
            SubtitleText(text = "检测溢出状态:")
            Box(
                modifier = Modifier
                    .width(300.dp)
                    .background(
                        if (isTextOverflowed) Color.Red.copy(alpha = 0.1f)
                        else Color.LightGray.copy(alpha = 0.3f)
                    )
                    .padding(8.dp)
            ) {
                Text(
                    text = longText,
                    maxLines = 1,
                    overflow = TextOverflow.Ellipsis,
                    onTextLayout = { textLayoutResult: TextLayoutResult ->
                        isTextOverflowed = textLayoutResult.hasVisualOverflow
                    }
                )
            }
            Text(
                text = "溢出状态: ${if (isTextOverflowed) "已溢出" else "未溢出"}",
                color = if (isTextOverflowed) Color.Red else Color.Green,
                modifier = Modifier.padding(top = 4.dp)
            )
            
            SectionTitle(title = "3. 省略号配置")
            
            SubtitleText(text = "默认省略号样式:")
            Box(
                modifier = Modifier
                    .width(300.dp)
                    .background(Color.LightGray.copy(alpha = 0.3f))
                    .padding(8.dp)
            ) {
                Text(
                    text = longText,
                    maxLines = 1,
                    overflow = TextOverflow.Ellipsis,
                    style = MaterialTheme.typography.bodyLarge.copy(
                        color = Color.Blue,
                        fontWeight = FontWeight.Bold
                    )
                )
            }
            
            Spacer(modifier = Modifier.height(16.dp))
            SubtitleText(text = "自定义省略号效果:")
            CustomEllipsisText(
                text = longText,
                modifier = Modifier
                    .width(300.dp)
                    .background(Color.LightGray.copy(alpha = 0.3f))
                    .padding(8.dp)
            )
            
            SectionTitle(title = "4. 多行溢出处理")
            
            SubtitleText(text = "多行省略号(maxLines=3):")
            Box(
                modifier = Modifier
                    .width(300.dp)
                    .height(80.dp)
                    .background(Color.LightGray.copy(alpha = 0.3f))
                    .padding(8.dp)
            ) {
                Text(
                    text = longText,
                    maxLines = 3,
                    overflow = TextOverflow.Ellipsis
                )
            }
            
            SectionTitle(title = "5. 自适应高度支持")
            
            SubtitleText(text = "自适应高度(不限制maxLines):")
            Box(
                modifier = Modifier
                    .width(300.dp)
                    .background(Color.LightGray.copy(alpha = 0.3f))
                    .padding(8.dp)
            ) {
                Text(text = longText)
            }
            
            Spacer(modifier = Modifier.height(16.dp))
            SubtitleText(text = "使用BoxWithConstraints适应容器:")
            BoxWithConstraints(
                modifier = Modifier
                    .width(300.dp)
                    .height(60.dp)
                    .background(Color.LightGray.copy(alpha = 0.3f))
                    .padding(8.dp)
            ) {
                // 根据容器高度计算可显示的行数
                val maxLines = (maxHeight / 20.dp).toInt().coerceAtLeast(1)
                Text(
                    text = longText,
                    maxLines = maxLines,
                    overflow = TextOverflow.Ellipsis
                )
            }
            
            Spacer(modifier = Modifier.height(16.dp))
            SubtitleText(text = "不同字体大小比例:")
            Box(
                modifier = Modifier
                    .width(300.dp)
                    .background(Color.LightGray.copy(alpha = 0.3f))
                    .padding(8.dp)
            ) {
                Text(
                    text = longText,
                    maxLines = 2,
                    overflow = TextOverflow.Ellipsis,
                    fontSize = 14.sp,
                    lineHeight = 18.sp,
                    fontWeight = FontWeight.Normal
                )
            }
        }
    }
}

@Composable
fun SectionTitle(title: String) {
    Text(
        text = title,
        style = MaterialTheme.typography.titleLarge,
        modifier = Modifier.padding(vertical = 16.dp)
    )
}

@Composable
fun SubtitleText(text: String) {
    Text(
        text = text,
        style = MaterialTheme.typography.bodyLarge.copy(
            fontWeight = FontWeight.Medium
        ),
        modifier = Modifier.padding(bottom = 8.dp)
    )
}

@Composable
fun CustomEllipsisText(
    text: String,
    modifier: Modifier = Modifier
) {
    var truncated by remember { mutableStateOf(false) }
    var lastCharIndex by remember { mutableStateOf(0) }
    
    Box(modifier = modifier) {
        if (!truncated) {
            // 第一次渲染，检测溢出状态和字符位置
            Text(
                text = text,
                maxLines = 1,
                overflow = TextOverflow.Clip,
                onTextLayout = { textLayoutResult ->
                    if (textLayoutResult.hasVisualOverflow) {
                        truncated = true
                        // 找到最后一个可见字符的索引
                        lastCharIndex = textLayoutResult.getLineEnd(0, visibleEnd = true)
                    }
                }
            )
        } else {
            // 溢出处理，创建自定义截断文本
            val displayText = if (lastCharIndex > 10) {
                // 取前半部分 + 中间自定义省略 + 后几个字符
                text.substring(0, lastCharIndex / 2) + 
                    " (省略) " + 
                    text.substring(text.length - 6)
            } else {
                // 文本太短，直接显示
                text
            }
            
            Text(
                text = displayText,
                maxLines = 1,
                overflow = TextOverflow.Clip
            )
        }
    }
}

## iOS UIKit TextOverflow

### 溢出处理策略

UIKit没有专门的TextOverflow组件，而是通过以下属性和类来控制文本溢出：

- `UILabel.lineBreakMode`：使用NSLineBreakMode枚举控制溢出行为
  - `.byTruncatingTail`：在末尾截断并显示省略号（最常用）
  - `.byTruncatingMiddle`：在中间截断并显示省略号
  - `.byTruncatingHead`：在开头截断并显示省略号
  - `.byClipping`：直接裁剪超出部分
  - `.byWordWrapping`：按单词换行（默认）
  - `.byCharWrapping`：按字符换行
- `NSAttributedString`：可以通过段落样式设置行截断方式
- `CATextLayer`：低级绘制层支持truncationMode属性

UIKit是六大框架中唯一原生支持文本中间和开头截断的框架。

### 截断方式

- 可精确控制截断发生的位置（头部、中间、尾部）
- 通过`numberOfLines`属性控制最大行数（0表示不限制）
- 截断发生在单词边界或字符边界，取决于lineBreakMode设置
- 使用`UITextView`时可以通过设置textContainer属性控制截断行为
- 可以使用`sizeToFit()`方法让标签自适应内容大小，避免截断

### 省略号配置

- 省略号显示为标准的"..."，继承文本样式
- 不能直接自定义省略号文本或样式
- 可以通过自定义的`NSAttributedString`和文本绘制实现特殊省略号
- 可以结合`NSAttributedString`和`NSLayoutManager`实现复杂的省略行为
- 支持RTL(从右到左)语言中的恰当省略号位置

### 多行溢出处理

- 通过`numberOfLines`控制最大行数
- 可以为多行文本指定不同的截断方式
- 支持多行文本中不同位置的截断（仅适用于attributedText）
- 可以使用UITextView实现更复杂的多行文本布局和溢出控制
- 结合NSAttributedString可以为溢出部分设置不同样式

### 自适应高度支持

- 可以使用`sizeToFit()`或`sizeThatFits(_:)`自动调整视图大小以适应内容
- 支持`adjustsFontSizeToFitWidth`属性实现文字大小自适应
- 可以设置`minimumScaleFactor`控制自适应时文字的最小缩放比例
- 支持使用自动布局（Auto Layout）进行动态高度调整
- 支持通过`UIFontMetrics`适应系统动态类型大小变化

### 平台特性与兼容性

- 专为iOS/iPadOS平台优化，与Apple设计规范一致
- 提供完整的无障碍支持，包括VoiceOver和动态类型
- 对国际化和本地化提供全面支持
- 针对苹果设备进行性能优化
- 与iOS系统控件和交互模式完美集成
- 支持跨应用的统一文本处理行为
- 需要iOS 7.0及以上版本

### 完整示例代码

```swift
import UIKit

class TextOverflowViewController: UIViewController {
    
    let shortText = "这是一个简短的文本示例"
    let longText = "这是一个非常长的文本示例，用于演示UIKit中的文本溢出处理功能。我们将看到不同的溢出处理策略，如裁剪、不同位置的省略号等。此外，还会展示多行文本溢出以及自适应高度的处理方式。通过这个例子，我们可以理解UIKit中文本溢出的各种用法和场景。"
    
    // 容器视图和滚动视图
    private let scrollView = UIScrollView()
    private let contentView = UIStackView()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        title = "UIKit TextOverflow"
        view.backgroundColor = .systemBackground
        
        setupScrollView()
        setupExamples()
    }
    
    private func setupScrollView() {
        // 设置滚动视图
        scrollView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(scrollView)
        NSLayoutConstraint.activate([
            scrollView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
            scrollView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            scrollView.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            scrollView.bottomAnchor.constraint(equalTo: view.bottomAnchor)
        ])
        
        // 设置内容视图
        contentView.translatesAutoresizingMaskIntoConstraints = false
        contentView.axis = .vertical
        contentView.spacing = 16
        contentView.alignment = .leading
        contentView.isLayoutMarginsRelativeArrangement = true
        contentView.layoutMargins = UIEdgeInsets(top: 16, left: 16, bottom: 16, right: 16)
        
        scrollView.addSubview(contentView)
        NSLayoutConstraint.activate([
            contentView.topAnchor.constraint(equalTo: scrollView.topAnchor),
            contentView.leadingAnchor.constraint(equalTo: scrollView.leadingAnchor),
            contentView.trailingAnchor.constraint(equalTo: scrollView.trailingAnchor),
            contentView.bottomAnchor.constraint(equalTo: scrollView.bottomAnchor),
            contentView.widthAnchor.constraint(equalTo: scrollView.widthAnchor)
        ])
    }
    
    private func setupExamples() {
        // 1. 溢出处理策略
        addSectionTitle("1. 溢出处理策略")
        
        // 尾部截断（最常用）
        addSubtitleLabel("byTruncatingTail（尾部截断）:")
        let tailTruncationLabel = createExampleLabel(longText)
        tailTruncationLabel.lineBreakMode = .byTruncatingTail
        addExampleContainer(tailTruncationLabel)
        
        // 中间截断
        addSubtitleLabel("byTruncatingMiddle（中间截断）:")
        let middleTruncationLabel = createExampleLabel(longText)
        middleTruncationLabel.lineBreakMode = .byTruncatingMiddle
        addExampleContainer(middleTruncationLabel)
        
        // 头部截断
        addSubtitleLabel("byTruncatingHead（头部截断）:")
        let headTruncationLabel = createExampleLabel(longText)
        headTruncationLabel.lineBreakMode = .byTruncatingHead
        addExampleContainer(headTruncationLabel)
        
        // 直接裁剪
        addSubtitleLabel("byClipping（直接裁剪）:")
        let clippingLabel = createExampleLabel(longText)
        clippingLabel.lineBreakMode = .byClipping
        addExampleContainer(clippingLabel)
        
        // 2. 截断方式
        addSectionTitle("2. 截断方式")
        
        // 指定最大行数
        addSubtitleLabel("指定numberOfLines=2:")
        let multilineLabel = createExampleLabel(longText)
        multilineLabel.numberOfLines = 2
        multilineLabel.lineBreakMode = .byTruncatingTail
        addExampleContainer(multilineLabel)
        
        // 按字符截断
        addSubtitleLabel("byCharWrapping（按字符换行）:")
        let charWrappingLabel = createExampleLabel(longText)
        charWrappingLabel.lineBreakMode = .byCharWrapping
        charWrappingLabel.numberOfLines = 2
        addExampleContainer(charWrappingLabel)
        
        // 3. 省略号配置
        addSectionTitle("3. 省略号配置")
        
        // 默认省略号样式
        addSubtitleLabel("默认省略号样式:")
        let defaultEllipsisLabel = createExampleLabel(longText)
        defaultEllipsisLabel.lineBreakMode = .byTruncatingTail
        defaultEllipsisLabel.textColor = .systemBlue
        defaultEllipsisLabel.font = .boldSystemFont(ofSize: 16)
        addExampleContainer(defaultEllipsisLabel)
        
        // 自定义省略号（使用NSAttributedString）
        addSubtitleLabel("自定义省略号效果:")
        let customEllipsisLabel = UILabel()
        customEllipsisLabel.translatesAutoresizingMaskIntoConstraints = false
        customEllipsisLabel.numberOfLines = 1
        
        // 创建富文本并添加自定义省略号效果
        let attributedText = NSMutableAttributedString(string: longText.prefix(30) + "")
        attributedText.append(NSAttributedString(string: " [更多]", attributes: [
            .foregroundColor: UIColor.systemRed,
            .font: UIFont.boldSystemFont(ofSize: 14)
        ]))
        customEllipsisLabel.attributedText = attributedText
        
        addExampleContainer(customEllipsisLabel)
        
        // 4. 多行溢出处理
        addSectionTitle("4. 多行溢出处理")
        
        // 多行省略号
        addSubtitleLabel("多行省略号（numberOfLines=3）:")
        let multilineEllipsisLabel = createExampleLabel(longText)
        multilineEllipsisLabel.numberOfLines = 3
        multilineEllipsisLabel.lineBreakMode = .byTruncatingTail
        addExampleContainer(multilineEllipsisLabel, height: 80)
        
        // 使用UITextView处理溢出
        addSubtitleLabel("使用UITextView处理多行:")
        let textView = UITextView()
        textView.translatesAutoresizingMaskIntoConstraints = false
        textView.text = longText
        textView.font = .systemFont(ofSize: 16)
        textView.isEditable = false
        textView.isScrollEnabled = false
        textView.textContainer.maximumNumberOfLines = 3
        textView.textContainer.lineBreakMode = .byTruncatingTail
        
        addExampleContainer(textView, height: 80)
        
        // 5. 自适应高度支持
        addSectionTitle("5. 自适应高度支持")
        
        // 自适应高度
        addSubtitleLabel("自适应高度（numberOfLines=0）:")
        let adaptiveHeightLabel = createExampleLabel(longText)
        adaptiveHeightLabel.numberOfLines = 0
        addExampleContainer(adaptiveHeightLabel, height: nil)
        
        // 字体大小自适应
        addSubtitleLabel("字体大小自适应（adjustsFontSizeToFitWidth）:")
        let fontAdjustLabel = createExampleLabel(longText)
        fontAdjustLabel.adjustsFontSizeToFitWidth = true
        fontAdjustLabel.minimumScaleFactor = 0.5
        fontAdjustLabel.numberOfLines = 1
        addExampleContainer(fontAdjustLabel)
        
        // 动态类型适配
        addSubtitleLabel("动态类型适配:")
        let dynamicTypeLabel = createExampleLabel("支持动态类型的文本")
        dynamicTypeLabel.font = UIFont.preferredFont(forTextStyle: .body)
        dynamicTypeLabel.adjustsFontForContentSizeCategory = true
        dynamicTypeLabel.numberOfLines = 0
        addExampleContainer(dynamicTypeLabel)
    }
    
    private func addSectionTitle(_ title: String) {
        let titleLabel = UILabel()
        titleLabel.translatesAutoresizingMaskIntoConstraints = false
        titleLabel.text = title
        titleLabel.font = .boldSystemFont(ofSize: 18)
        titleLabel.textColor = .label
        
        contentView.addArrangedSubview(titleLabel)
        contentView.setCustomSpacing(8, after: titleLabel)
    }
    
    private func addSubtitleLabel(_ subtitle: String) {
        let subtitleLabel = UILabel()
        subtitleLabel.translatesAutoresizingMaskIntoConstraints = false
        subtitleLabel.text = subtitle
        subtitleLabel.font = .systemFont(ofSize: 16, weight: .medium)
        subtitleLabel.textColor = .secondaryLabel
        
        contentView.addArrangedSubview(subtitleLabel)
        contentView.setCustomSpacing(8, after: subtitleLabel)
    }
    
    private func createExampleLabel(_ text: String) -> UILabel {
        let label = UILabel()
        label.translatesAutoresizingMaskIntoConstraints = false
        label.text = text
        label.font = .systemFont(ofSize: 16)
        label.numberOfLines = 1
        return label
    }
    
    private func addExampleContainer(_ view: UIView, height: CGFloat? = 40) {
        let containerView = UIView()
        containerView.translatesAutoresizingMaskIntoConstraints = false
        containerView.backgroundColor = .systemGray6
        containerView.layer.cornerRadius = 4
        
        containerView.addSubview(view)
        
        // 设置宽度约束固定为300
        NSLayoutConstraint.activate([
            containerView.widthAnchor.constraint(equalToConstant: 300),
            view.topAnchor.constraint(equalTo: containerView.topAnchor, constant: 8),
            view.leadingAnchor.constraint(equalTo: containerView.leadingAnchor, constant: 8),
            view.trailingAnchor.constraint(equalTo: containerView.trailingAnchor, constant: -8),
            view.bottomAnchor.constraint(equalTo: containerView.bottomAnchor, constant: -8)
        ])
        
        if let height = height {
            containerView.heightAnchor.constraint(equalToConstant: height).isActive = true
        }
        
        contentView.addArrangedSubview(containerView)
    }
}

// 用于模拟自定义省略号效果的NSLayoutManager子类
class CustomEllipsisLayoutManager: NSLayoutManager {
    
    let customEllipsis = " [更多]"
    
    override func drawGlyphs(forGlyphRange glyphsToShow: NSRange, at origin: CGPoint) {
        super.drawGlyphs(forGlyphRange: glyphsToShow, at: origin)
    }
}

## iOS SwiftUI TextOverflow

### 溢出处理策略

SwiftUI通过Text组件的修饰符控制文本溢出，主要包括：

- `.truncationMode(_:)`：设置截断模式，使用`Text.TruncationMode`枚举
  - `.tail`：在末尾截断（默认）
  - `.middle`：在中间截断
  - `.head`：在开头截断
- `.lineLimit(_:)`：限制文本的最大行数
- 没有直接等同于Flutter的`clip`或`fade`选项
- 不支持完全禁用截断（无`visible`选项），但可以通过不设置行数限制实现

这些修饰符可以组合使用，也可以通过容器视图（如VStack、HStack）间接影响文本溢出行为。

### 截断方式

- 截断发生在单词边界，避免单词被部分截断
- 通过`.lineLimit(_:)`控制最大行数
- 使用`.fixedSize(horizontal:vertical:)`可以控制文本是否允许换行
- 支持在头部、中间或尾部进行截断
- 不支持自定义截断算法或精确控制截断位置

### 省略号配置

- 省略号显示为标准的"..."，继承文本样式
- 不支持直接自定义省略号文本或样式
- 通过`Text`组件组合可以实现类似自定义省略号的效果
- 支持针对不同本地化调整省略号行为
- 省略号与文本主体共享相同的样式属性

### 多行溢出处理

- 使用`.lineLimit(_:)`设置最大行数
- 多行溢出时仅在最后截断行应用省略号
- 通过`.multilineTextAlignment(_:)`可以控制多行文本对齐方式
- 可以使用自定义视图替换溢出部分，如"查看更多"按钮
- 不支持为不同行指定不同的截断行为

### 自适应高度支持

- 默认情况下Text组件会自适应内容高度
- 可以通过`.frame(height:)`限制高度
- 使用`.minimumScaleFactor(_:)`实现字体大小自动缩放以适应空间
- 支持`.lineSpacing(_:)`控制行间距，间接影响总高度
- 完全支持动态类型（Dynamic Type）进行辅助功能适配

### 平台特性与兼容性

- 专为Apple平台优化，与iOS/iPadOS/macOS设计语言一致
- 自动支持动态类型和所有系统辅助功能
- 响应系统深色模式和外观变化
- 良好支持RTL语言和各种书写系统
- 与其他SwiftUI组件无缝集成
- 支持iOS 13+、iPadOS 13+、macOS 10.15+
- 利用Metal渲染引擎提供高性能文本显示

### 完整示例代码

```swift
import SwiftUI

struct TextOverflowExample: View {
    let shortText = "这是一个简短的文本示例"
    let longText = "这是一个非常长的文本示例，用于演示SwiftUI中的文本溢出处理功能。我们将看到不同的溢出处理策略，如不同位置的省略号等。此外，还会展示多行文本溢出以及自适应高度的处理方式。通过这个例子，我们可以理解SwiftUI中文本溢出的各种用法和场景。"
    
    @State private var expandedSections: Set<Int> = []
    @Environment(\.dynamicTypeSize) var dynamicTypeSize
    
    var body: some View {
        ScrollView {
            VStack(alignment: .leading, spacing: 16) {
                // 1. 溢出处理策略
                sectionTitle("1. 溢出处理策略")
                
                subtitleText("truncationMode(.tail)（尾部截断）:")
                exampleContainer {
                    Text(longText)
                        .lineLimit(1)
                        .truncationMode(.tail)
                }
                
                subtitleText("truncationMode(.middle)（中间截断）:")
                exampleContainer {
                    Text(longText)
                        .lineLimit(1)
                        .truncationMode(.middle)
                }
                
                subtitleText("truncationMode(.head)（头部截断）:")
                exampleContainer {
                    Text(longText)
                        .lineLimit(1)
                        .truncationMode(.head)
                }
                
                // 2. 截断方式
                sectionTitle("2. 截断方式")
                
                subtitleText("指定lineLimit(2):")
                exampleContainer {
                    Text(longText)
                        .lineLimit(2)
                }
                
                subtitleText("fixedSize(horizontal: false, vertical: true):")
                exampleContainer {
                    Text(longText)
                        .lineLimit(1)
                        .fixedSize(horizontal: false, vertical: true)
                }
                
                subtitleText("frame限制宽度:")
                exampleContainer {
                    Text(longText)
                        .frame(width: 250, alignment: .leading)
                        .lineLimit(1)
                }
                
                // 3. 省略号配置
                sectionTitle("3. 省略号配置")
                
                subtitleText("默认省略号样式:")
                exampleContainer {
                    Text(longText)
                        .lineLimit(1)
                        .foregroundColor(.blue)
                        .bold()
                }
                
                subtitleText("自定义「查看更多」效果:")
                exampleContainer {
                    ZStack(alignment: .topLeading) {
                        // 使用空Text占位，获取完整高度
                        Text(longText)
                            .lineLimit(1)
                            .opacity(0)
                        
                        VStack(alignment: .leading, spacing: 0) {
                            // 显示前半部分文本
                            Text(String(longText.prefix(40)))
                            
                            // 添加自定义的"查看更多"按钮
                            Button(action: {
                                // 展开/折叠逻辑
                                if expandedSections.contains(3) {
                                    expandedSections.remove(3)
                                } else {
                                    expandedSections.insert(3)
                                }
                            }) {
                                Text("查看更多")
                                    .font(.system(size: 14, weight: .bold))
                                    .foregroundColor(.red)
                            }
                            .padding(.top, 4)
                            
                            // 展开时显示完整文本
                            if expandedSections.contains(3) {
                                Text(longText)
                                    .padding(.top, 8)
                            }
                        }
                    }
                    .animation(.easeInOut, value: expandedSections)
                    .frame(maxWidth: .infinity, alignment: .leading)
                }
                
                // 4. 多行溢出处理
                sectionTitle("4. 多行溢出处理")
                
                subtitleText("多行省略号(lineLimit(3)):")
                exampleContainer(height: 80) {
                    Text(longText)
                        .lineLimit(3)
                }
                
                subtitleText("不同对齐方式的多行溢出:")
                exampleContainer(height: 80) {
                    VStack(alignment: .leading, spacing: 8) {
                        Text("左对齐（默认）:").font(.caption)
                        Text(longText)
                            .lineLimit(2)
                            .multilineTextAlignment(.leading)
                        
                        Text("居中:").font(.caption)
                        Text(longText)
                            .lineLimit(2)
                            .multilineTextAlignment(.center)
                    }
                }
                
                // 5. 自适应高度支持
                sectionTitle("5. 自适应高度支持")
                
                subtitleText("自适应高度(无lineLimit):")
                exampleContainer(height: nil) {
                    Text(longText)
                }
                
                subtitleText("minimumScaleFactor自适应字体大小:")
                exampleContainer {
                    Text(longText)
                        .lineLimit(1)
                        .minimumScaleFactor(0.5)
                }
                
                subtitleText("动态类型支持:")
                exampleContainer(height: nil) {
                    VStack(alignment: .leading, spacing: 4) {
                        Text("当前动态类型大小: \(dynamicTypeSize.debugDescription)")
                            .font(.caption)
                        
                        Text("自动适应动态类型")
                            .dynamicTypeSize(...DynamicTypeSize.xxxLarge)
                    }
                }
                
                subtitleText("控制行间距:")
                exampleContainer(height: 80) {
                    Text(longText)
                        .lineLimit(3)
                        .lineSpacing(8)
                }
            }
            .padding()
        }
        .navigationTitle("SwiftUI TextOverflow")
    }
    
    private func sectionTitle(_ title: String) -> some View {
        Text(title)
            .font(.headline)
            .padding(.vertical, 8)
    }
    
    private func subtitleText(_ text: String) -> some View {
        Text(text)
            .font(.subheadline)
            .foregroundColor(.secondary)
            .padding(.bottom, 4)
    }
    
    private func exampleContainer<Content: View>(height: CGFloat? = 40, @ViewBuilder content: () -> Content) -> some View {
        content()
            .frame(width: 300, height: height)
            .padding(8)
            .background(Color.gray.opacity(0.1))
            .cornerRadius(4)
    }
} 

## 鸿蒙 ArkUI TextOverflow

### 溢出处理策略

鸿蒙ArkUI通过`TextOverflow`枚举提供以下文本溢出处理策略：

- `TextOverflow.Clip`：直接裁剪溢出文本，不显示任何提示
- `TextOverflow.Ellipsis`：在尾部显示省略号
- `TextOverflow.None`：不进行特殊处理，允许文本溢出显示

这些策略可以通过Text组件的`textOverflow`属性设置，或者在全局样式中定义。ArkUI没有提供类似Flutter的淡出效果，也不支持中间或头部省略。

### 截断方式

- 截断默认在文本末尾进行
- 通过`maxLines`属性控制最大行数
- 可以使用`textAlign`控制文本对齐方式，影响截断效果
- 支持使用`textOverflow`和`maxLines`组合控制截断行为
- 可以结合`textAlign`、`textOverflow`等属性实现不同的截断效果
- 不支持中间或开头截断

### 省略号配置

- 省略号显示为标准的"..."，样式继承自文本
- 不支持直接自定义省略号文本或样式
- 可以通过自定义组件模拟特殊的省略号效果
- 不支持省略号位置自定义
- 省略号与主文本共享相同的样式

### 多行溢出处理

- 使用`maxLines`属性控制最大显示行数
- 多行文本溢出时，只在最后一行末尾显示省略号
- 支持`lineHeight`设置行高，影响多行文本的整体显示
- 不支持为不同行设置不同的溢出处理策略
- 可以使用`textAlign`控制多行文本对齐方式

### 自适应高度支持

- 默认情况下Text组件会自适应内容高度
- 通过不设置`maxLines`可实现完全自适应
- 可以使用`height`属性限制组件高度
- 支持通过`fontSize`和响应式布局实现不同设备适配
- 结合`fontWeight`和`fontFamily`可以实现更丰富的排版效果
- 支持通过响应式状态动态调整文本显示

### 平台特性与兼容性

- 专为鸿蒙操作系统优化，遵循鸿蒙设计语言
- 支持设备跨屏协同，在不同设备上保持一致体验
- 原生支持鸿蒙特有的分布式能力和超级终端
- 针对多设备形态（手机、手表、平板等）优化
- 支持响应式布局自动适配不同屏幕尺寸
- 与鸿蒙系统控件和交互模式无缝集成
- 需要HarmonyOS 3.0及以上版本

### 完整示例代码

```typescript
import promptAction from '@ohos.promptAction';

@Entry
@Component
struct TextOverflowExample {
  private shortText: string = "这是一个简短的文本示例";
  private longText: string = "这是一个非常长的文本示例，用于演示鸿蒙ArkUI中的文本溢出处理功能。我们将看到不同的溢出处理策略，如裁剪和省略号。此外，还会展示多行文本溢出以及自适应高度的处理方式。通过这个例子，我们可以理解ArkUI中TextOverflow的各种用法和场景。";

  @State expandedSection: number = -1;
  @State expandedText: boolean = false;
  @State fontSize: number = 16;

  build() {
    Column() {
      // 标题
      Text('鸿蒙 ArkUI TextOverflow')
        .width('100%')
        .fontSize(22)
        .fontWeight(FontWeight.Bold)
        .textAlign(TextAlign.Center)
        .margin({ top: 20, bottom: 20 })

      Scroll() {
        Column({ space: 16 }) {
          // 1. 溢出处理策略
          this.SectionTitle("1. 溢出处理策略")

          this.SubtitleText("TextOverflow.Clip（裁剪）:")
          this.ExampleContainer({
            child: Text(this.longText)
              .maxLines(1)
              .textOverflow({ overflow: TextOverflow.Clip })
              .fontSize(16)
          })

          this.SubtitleText("TextOverflow.Ellipsis（省略号）:")
          this.ExampleContainer({
            child: Text(this.longText)
              .maxLines(1)
              .textOverflow({ overflow: TextOverflow.Ellipsis })
              .fontSize(16)
          })

          this.SubtitleText("TextOverflow.None（无特殊处理）:")
          this.ExampleContainer({
            child: Text(this.longText)
              .maxLines(1)
              .textOverflow({ overflow: TextOverflow.None })
              .fontSize(16)
          })

          // 2. 截断方式
          this.SectionTitle("2. 截断方式")

          this.SubtitleText("指定maxLines=2:")
          this.ExampleContainer({
            child: Text(this.longText)
              .maxLines(2)
              .textOverflow({ overflow: TextOverflow.Ellipsis })
              .fontSize(16)
          })

          this.SubtitleText("不同对齐方式:")
          Column({ space: 10 }) {
            Text("左对齐:")
              .fontSize(14)
              .fontColor('#666')
            this.ExampleContainer({
              child: Text(this.longText)
                .maxLines(1)
                .textOverflow({ overflow: TextOverflow.Ellipsis })
                .textAlign(TextAlign.Start)
                .fontSize(16)
            })

            Text("居中:")
              .fontSize(14)
              .fontColor('#666')
            this.ExampleContainer({
              child: Text(this.longText)
                .maxLines(1)
                .textOverflow({ overflow: TextOverflow.Ellipsis })
                .textAlign(TextAlign.Center)
                .fontSize(16)
            })

            Text("右对齐:")
              .fontSize(14)
              .fontColor('#666')
            this.ExampleContainer({
              child: Text(this.longText)
                .maxLines(1)
                .textOverflow({ overflow: TextOverflow.Ellipsis })
                .textAlign(TextAlign.End)
                .fontSize(16)
            })
          }
          .width('100%')

          // 3. 省略号配置
          this.SectionTitle("3. 省略号配置")

          this.SubtitleText("默认省略号样式:")
          this.ExampleContainer({
            child: Text(this.longText)
              .maxLines(1)
              .textOverflow({ overflow: TextOverflow.Ellipsis })
              .fontColor(Color.Blue)
              .fontWeight(FontWeight.Bold)
              .fontSize(16)
          })

          this.SubtitleText("自定义「查看更多」效果:")
          this.ExampleContainer({
            height: this.expandedSection === 3 ? 150 : null,
            child: Column() {
              if (!this.expandedSection === 3) {
                Row() {
                  Text(this.longText.substring(0, 40) + "...")
                    .fontSize(16)
                    .maxLines(1)
                  Text(" 查看更多")
                    .fontSize(14)
                    .fontColor(Color.Red)
                    .fontWeight(FontWeight.Bold)
                    .onClick(() => {
                      this.expandedSection = 3;
                    })
                }
                .width('100%')
              } else {
                Column({ space: 8 }) {
                  Text(this.longText)
                    .fontSize(16)
                  Text("收起")
                    .fontSize(14)
                    .fontColor(Color.Red)
                    .fontWeight(FontWeight.Bold)
                    .onClick(() => {
                      this.expandedSection = -1;
                    })
                }
                .width('100%')
              }
            }
          })

          // 4. 多行溢出处理
          this.SectionTitle("4. 多行溢出处理")

          this.SubtitleText("多行省略号(maxLines=3):")
          this.ExampleContainer({
            height: 80,
            child: Text(this.longText)
              .maxLines(3)
              .textOverflow({ overflow: TextOverflow.Ellipsis })
              .fontSize(16)
          })

          this.SubtitleText("结合lineHeight:")
          this.ExampleContainer({
            height: 80,
            child: Text(this.longText)
              .maxLines(3)
              .textOverflow({ overflow: TextOverflow.Ellipsis })
              .lineHeight(24)
              .fontSize(16)
          })

          // 5. 自适应高度支持
          this.SectionTitle("5. 自适应高度支持")

          this.SubtitleText("自适应高度(不限制maxLines):")
          this.ExampleContainer({
            height: null,
            child: Text(this.longText)
              .fontSize(16)
          })

          this.SubtitleText("字体大小调整:")
          Column({ space: 10 }) {
            Slider({
              value: this.fontSize,
              min: 12,
              max: 24,
              step: 1
            })
              .width('90%')
              .onChange((value: number) => {
                this.fontSize = value;
              })
            Text(`当前字体大小: ${this.fontSize.toFixed(0)}`)
              .fontSize(14)
              .fontColor('#666')
            this.ExampleContainer({
              child: Text(this.longText)
                .maxLines(2)
                .textOverflow({ overflow: TextOverflow.Ellipsis })
                .fontSize(this.fontSize)
            })
          }
          .width('100%')
          
          // 设备适配示例
          this.SubtitleText("响应式布局自适应:")
          GridRow({
            breakpoints: {
              sm: 320,
              md: 600,
              lg: 820
            },
            columns: 12
          }) {
            GridCol({
              span: { sm: 12, md: 6, lg: 4 }
            }) {
              this.ExampleContainer({
                child: Text(this.longText)
                  .maxLines(1)
                  .textOverflow({ overflow: TextOverflow.Ellipsis })
                  .fontSize(16)
              })
            }
            GridCol({
              span: { sm: 12, md: 6, lg: 4 }
            }) {
              this.ExampleContainer({
                child: Text(this.longText)
                  .maxLines(2)
                  .textOverflow({ overflow: TextOverflow.Ellipsis })
                  .fontSize(16)
              })
            }
            GridCol({
              span: { sm: 12, md: 12, lg: 4 }
            }) {
              this.ExampleContainer({
                child: Text(this.longText)
                  .maxLines(3)
                  .textOverflow({ overflow: TextOverflow.Ellipsis })
                  .fontSize(16)
              })
            }
          }
          .width('100%')
        }
        .width('100%')
        .padding(16)
      }
      .width('100%')
      .scrollBar(BarState.Auto)
    }
    .width('100%')
    .height('100%')
  }

  @Builder
  SectionTitle(title: string) {
    Text(title)
      .fontSize(18)
      .fontWeight(FontWeight.Bold)
      .margin({ top: 16, bottom: 8 })
      .width('100%')
  }

  @Builder
  SubtitleText(text: string) {
    Text(text)
      .fontSize(16)
      .fontWeight(FontWeight.Medium)
      .fontColor('#666')
      .margin({ bottom: 8 })
      .width('100%')
  }

  @Builder
  ExampleContainer({ child, height = 40 }: { child: any, height?: number }) {
    Stack() {
      child
    }
    .width(300)
    .height(height)
    .backgroundColor('#f5f5f5')
    .borderRadius(4)
    .padding(8)
  }
} 

## Vue TextOverflow

### 溢出处理策略

Vue没有内置专门的TextOverflow组件，而是通过CSS属性控制文本溢出行为：

- `text-overflow: clip`：直接裁剪溢出文本，不显示提示
- `text-overflow: ellipsis`：在末尾显示省略号
- `text-overflow: " [...]"`：支持自定义省略字符（部分浏览器）
- 结合`overflow: hidden`和`white-space: nowrap`使用
- 可通过CSS3的`-webkit-line-clamp`实现多行省略
- 结合Javascript可以实现更复杂的溢出处理逻辑

作为Web框架，Vue能够利用现代CSS的所有文本溢出功能，并可以通过组件封装使其更易用。

### 截断方式

- 默认在文本末尾截断
- 使用`line-clamp`或`-webkit-line-clamp`控制最大行数
- 通过`white-space`属性控制文本换行行为
- 结合`text-align`控制文本对齐方式，影响截断显示
- 支持通过Javascript实现更精确的截断控制
- 不支持原生的中间或开头截断，但可以通过Javascript实现

### 省略号配置

- 默认省略号为"..."，继承文本样式
- 部分浏览器支持通过`text-overflow`设置自定义省略字符串
- 可以通过自定义组件和Javascript实现任意样式的省略号
- 支持不同位置（末尾、中间、开头）的省略号显示
- 可以结合Vue的条件渲染实现特殊省略效果
- 可以使用伪元素实现渐变省略效果

### 多行溢出处理

- 使用`-webkit-line-clamp`结合`display: -webkit-box`实现多行省略
- 通过`max-height`和`overflow: hidden`控制最大高度
- 支持现代浏览器的`line-clamp`CSS属性
- 可以结合Javascript实现更精确的多行溢出控制
- 支持使用IntersectionObserver检测文本是否溢出
- 可以通过计算行高实现精确的行数控制

### 自适应高度支持

- 默认情况下文本元素会自动根据内容调整高度
- 支持响应式设计，可以根据屏幕大小适应布局
- 可以使用CSS媒体查询调整不同设备上的文本显示
- 支持通过动态绑定样式实现文本动态大小调整
- 结合Vue的响应式系统可以实现复杂的自适应逻辑
- 支持使用CSS变量动态调整文本参数

### 平台特性与兼容性

- 作为Web框架，可以在任何支持现代浏览器的平台上运行
- 部分高级特性依赖特定浏览器支持（如`-webkit-line-clamp`）
- 可以使用polyfill或降级方案支持旧版浏览器
- 性能受限于浏览器的文本渲染能力
- 通过响应式设计可以适应从手机到桌面的各种设备
- 支持通过组件封装实现跨浏览器一致的文本溢出处理
- 结合SSR可提供更好的首屏文本渲染性能

### 完整示例代码

```vue
<template>
  <div class="text-overflow-example">
    <h1>Vue TextOverflow 示例</h1>
    
    <div class="scroll-container">
      <!-- 1. 溢出处理策略 -->
      <section>
        <h2>1. 溢出处理策略</h2>
        
        <div class="subtitle">text-overflow: clip（裁剪）:</div>
        <div class="example-container">
          <div class="text-clip">{{ longText }}</div>
        </div>
        
        <div class="subtitle">text-overflow: ellipsis（省略号）:</div>
        <div class="example-container">
          <div class="text-ellipsis">{{ longText }}</div>
        </div>
        
        <div class="subtitle">自定义省略字符（部分浏览器支持）:</div>
        <div class="example-container">
          <div class="text-custom-ellipsis">{{ longText }}</div>
        </div>
        
        <div class="subtitle">渐变淡出效果:</div>
        <div class="example-container">
          <div class="text-fade">{{ longText }}</div>
        </div>
      </section>
      
      <!-- 2. 截断方式 -->
      <section>
        <h2>2. 截断方式</h2>
        
        <div class="subtitle">指定最大行数（line-clamp: 2）:</div>
        <div class="example-container">
          <div class="text-line-clamp-2">{{ longText }}</div>
        </div>
        
        <div class="subtitle">不换行（white-space: nowrap）:</div>
        <div class="example-container">
          <div class="text-nowrap">{{ longText }}</div>
        </div>
        
        <div class="subtitle">JS控制截断（显示前N个字符）:</div>
        <div class="example-container">
          <div>{{ truncatedText }}</div>
        </div>
      </section>
      
      <!-- 3. 省略号配置 -->
      <section>
        <h2>3. 省略号配置</h2>
        
        <div class="subtitle">默认省略号样式:</div>
        <div class="example-container">
          <div class="text-ellipsis text-bold text-blue">{{ longText }}</div>
        </div>
        
        <div class="subtitle">自定义"查看更多"效果:</div>
        <div class="example-container" :style="{ height: customSection3 ? 'auto' : '40px' }">
          <div v-if="!customSection3" class="custom-more">
            {{ longText.substring(0, 40) }}
            <span class="read-more" @click="customSection3 = true">...查看更多</span>
          </div>
          <div v-else>
            {{ longText }}
            <div>
              <span class="read-less" @click="customSection3 = false">收起</span>
            </div>
          </div>
        </div>
        
        <div class="subtitle">使用伪元素的渐变省略效果:</div>
        <div class="example-container">
          <div class="text-gradient-mask">{{ longText }}</div>
        </div>
      </section>
      
      <!-- 4. 多行溢出处理 -->
      <section>
        <h2>4. 多行溢出处理</h2>
        
        <div class="subtitle">多行省略号（line-clamp: 3）:</div>
        <div class="example-container" style="height: 80px;">
          <div class="text-line-clamp-3">{{ longText }}</div>
        </div>
        
        <div class="subtitle">使用max-height限制高度:</div>
        <div class="example-container" style="height: 80px;">
          <div class="text-max-height">{{ longText }}</div>
        </div>
        
        <div class="subtitle">溢出检测与提示:</div>
        <div class="example-container" style="height: 80px;">
          <div ref="overflowDetectionEl" class="text-line-clamp-3">
            {{ longText }}
          </div>
          <div v-if="isTextOverflowing" class="overflow-indicator">文本已溢出</div>
        </div>
      </section>
      
      <!-- 5. 自适应高度支持 -->
      <section>
        <h2>5. 自适应高度支持</h2>
        
        <div class="subtitle">自适应高度（不限制高度）:</div>
        <div class="example-container" style="height: auto;">
          <div>{{ longText }}</div>
        </div>
        
        <div class="subtitle">字体大小调整:</div>
        <div class="control-panel">
          <input 
            type="range" 
            v-model="fontSize" 
            min="12" 
            max="24" 
            step="1"
          />
          <div>当前字体大小: {{ fontSize }}px</div>
        </div>
        <div class="example-container">
          <div class="text-ellipsis" :style="{ fontSize: `${fontSize}px` }">{{ longText }}</div>
        </div>
        
        <div class="subtitle">响应式布局:</div>
        <div class="responsive-container">
          <div class="responsive-item">
            <div class="text-ellipsis">{{ longText }}</div>
          </div>
          <div class="responsive-item">
            <div class="text-line-clamp-2">{{ longText }}</div>
          </div>
          <div class="responsive-item">
            <div class="text-line-clamp-3">{{ longText }}</div>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TextOverflowExample',
  data() {
    return {
      shortText: '这是一个简短的文本示例',
      longText: '这是一个非常长的文本示例，用于演示Vue中的文本溢出处理功能。我们将看到不同的溢出处理策略，如裁剪、省略号等。此外，还会展示多行文本溢出以及自适应高度的处理方式。通过这个例子，我们可以理解Vue中文本溢出的各种用法和场景。',
      customSection3: false,
      isTextOverflowing: false,
      fontSize: 16
    };
  },
  computed: {
    truncatedText() {
      return this.longText.substring(0, 50) + '...';
    }
  },
  mounted() {
    this.checkOverflow();
    window.addEventListener('resize', this.checkOverflow);
  },
  beforeUnmount() {
    window.removeEventListener('resize', this.checkOverflow);
  },
  methods: {
    checkOverflow() {
      if (this.$refs.overflowDetectionEl) {
        const element = this.$refs.overflowDetectionEl;
        this.isTextOverflowing = element.scrollHeight > element.clientHeight;
      }
    }
  }
};
</script>

<style scoped>
.text-overflow-example {
  font-family: 'Arial', sans-serif;
  line-height: 1.5;
  color: #333;
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

h1 {
  text-align: center;
  margin-bottom: 24px;
}

h2 {
  font-size: 18px;
  font-weight: bold;
  margin: 24px 0 16px;
}

.scroll-container {
  overflow-y: auto;
  max-height: calc(100vh - 100px);
}

section {
  margin-bottom: 24px;
}

.subtitle {
  font-size: 14px;
  font-weight: 500;
  color: #666;
  margin-bottom: 8px;
}

.example-container {
  width: 300px;
  min-height: 40px;
  background-color: rgba(0, 0, 0, 0.05);
  border-radius: 4px;
  padding: 8px;
  margin-bottom: 16px;
}

/* 1. 溢出处理策略 */
.text-clip {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: clip;
}

.text-ellipsis {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.text-custom-ellipsis {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: " [...]"; /* 部分浏览器支持 */
}

.text-fade {
  position: relative;
  white-space: nowrap;
  overflow: hidden;
}

.text-fade::after {
  content: "";
  position: absolute;
  right: 0;
  top: 0;
  width: 50px;
  height: 100%;
  background: linear-gradient(to right, transparent, rgba(255, 255, 255, 0.9) 80%);
}

/* 2. 截断方式 */
.text-line-clamp-2 {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
  line-clamp: 2;
}

.text-nowrap {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* 3. 省略号配置 */
.text-bold {
  font-weight: bold;
}

.text-blue {
  color: blue;
}

.custom-more {
  position: relative;
}

.read-more, .read-less {
  color: red;
  font-weight: bold;
  cursor: pointer;
  font-size: 14px;
}

.text-gradient-mask {
  position: relative;
  height: 24px;
  line-height: 24px;
  overflow: hidden;
  white-space: nowrap;
}

.text-gradient-mask::after {
  content: "...";
  position: absolute;
  right: 0;
  top: 0;
  background: white;
  padding-left: 20px;
  background: linear-gradient(to right, transparent, white 40%);
}

/* 4. 多行溢出处理 */
.text-line-clamp-3 {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
  line-clamp: 3;
}

.text-max-height {
  max-height: 64px;
  overflow: hidden;
  position: relative;
}

.text-max-height::after {
  content: "...";
  position: absolute;
  bottom: 0;
  right: 0;
  padding-left: 20px;
  background: linear-gradient(to right, transparent, white 40%);
}

.overflow-indicator {
  position: absolute;
  right: 8px;
  bottom: 8px;
  background: rgba(255, 0, 0, 0.1);
  color: red;
  font-size: 12px;
  padding: 2px 4px;
  border-radius: 2px;
}

/* 5. 自适应高度支持 */
.control-panel {
  margin-bottom: 12px;
}

.control-panel input {
  width: 100%;
  margin-bottom: 8px;
}

.responsive-container {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.responsive-item {
  flex: 1 1 300px;
  min-width: 0;
  background-color: rgba(0, 0, 0, 0.05);
  border-radius: 4px;
  padding: 8px;
}

/* 响应式调整 */
@media (max-width: 600px) {
  .example-container,
  .responsive-item {
    width: 100%;
  }

  .responsive-container {
    flex-direction: column;
  }
}
</style>