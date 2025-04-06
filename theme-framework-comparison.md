# 主流框架主题管理系统对比分析（2024）

## 目录
- [概述](#概述)
- [Flutter Theme](#flutter-theme)
- [Android Jetpack Compose Theme](#android-jetpack-compose-theme)
- [iOS UIKit Theme](#ios-uikit-theme)
- [iOS SwiftUI Theme](#ios-swiftui-theme)
- [鸿蒙 ArkUI Theme](#鸿蒙-arkui-theme)
- [Vue Theme](#vue-theme)
- [跨平台对比总结](#跨平台对比总结)

## 概述

本文档对比了六大主流框架的主题管理实现，基于 2024 年最新稳定版本：
- Flutter 3.19
- Jetpack Compose 1.6.0
- UIKit (iOS 17)
- SwiftUI (iOS 17)
- ArkUI (HarmonyOS 4.0)
- Vue 3.4

每个框架的分析将从以下五个维度进行：
1. 配置方式
2. 全局/局部应用机制
3. 主题继承机制
4. 动态切换支持
5. 自定义主题扩展性

此外，还将提供相关平台特性、兼容性考虑以及完整的示例代码。

## Flutter Theme

### 配置方式

Flutter 使用 `ThemeData` 类来定义应用程序的主题，可以在 `MaterialApp` 的 `theme` 和 `darkTheme` 属性中配置。主题包含了颜色、字体、形状等多种样式属性：

```dart
final ThemeData lightTheme = ThemeData(
  primaryColor: Colors.blue,
  colorScheme: ColorScheme.light(
    primary: Colors.blue,
    secondary: Colors.blueAccent,
  ),
  appBarTheme: AppBarTheme(
    backgroundColor: Colors.blue,
    foregroundColor: Colors.white,
  ),
  textTheme: TextTheme(
    titleLarge: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
    bodyMedium: TextStyle(fontSize: 16),
  ),
);
```

### 全局/局部应用机制

**全局应用**：在 `MaterialApp` 中设置全局主题，将应用于所有子组件。

```dart
MaterialApp(
  theme: lightTheme,
  darkTheme: darkTheme,
  themeMode: ThemeMode.system, // 系统、亮色或暗色模式
  home: MyHomePage(),
)
```

**局部应用**：使用 `Theme` 组件可以在局部覆盖主题设置。

```dart
Theme(
  data: Theme.of(context).copyWith(
    colorScheme: ColorScheme.light(primary: Colors.red),
  ),
  child: Builder(
    builder: (context) {
      // 这里的子组件将使用修改后的主题
      return ElevatedButton(
        onPressed: () {},
        child: Text('按钮'),
      );
    },
  ),
)
```

### 主题继承机制

Flutter 通过 `copyWith()` 方法支持主题继承。可以基于现有主题创建新主题，只修改需要变更的属性：

```dart
// 继承自应用主题，仅修改部分属性
final customTheme = Theme.of(context).copyWith(
  cardTheme: CardTheme(
    color: Colors.amber,
    elevation: 8,
  ),
);
```

此外，Flutter 的主题系统遵循 Material Design 的主题继承原则，如 `colorScheme` 中定义的主要颜色会自动应用到相关组件。

### 动态切换支持

Flutter 支持运行时动态切换主题，可以根据用户操作或系统设置更改主题：

```dart
class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  ThemeMode _themeMode = ThemeMode.system;

  void toggleTheme() {
    setState(() {
      _themeMode = _themeMode == ThemeMode.light 
          ? ThemeMode.dark 
          : ThemeMode.light;
    });
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: lightTheme,
      darkTheme: darkTheme,
      themeMode: _themeMode,
      home: HomeScreen(toggleTheme: toggleTheme),
    );
  }
}
```

Flutter 的主题切换是即时的，无需重启应用，所有使用 `Theme.of(context)` 获取主题的组件都会自动响应变化。

### 自定义主题扩展性

Flutter 通过扩展 `ThemeExtension` 类允许开发者创建完全自定义的主题属性：

```dart
class CustomThemeExtension extends ThemeExtension<CustomThemeExtension> {
  final Color specialColor;
  final TextStyle specialTextStyle;

  CustomThemeExtension({
    required this.specialColor,
    required this.specialTextStyle,
  });

  @override
  ThemeExtension<CustomThemeExtension> copyWith({
    Color? specialColor,
    TextStyle? specialTextStyle,
  }) {
    return CustomThemeExtension(
      specialColor: specialColor ?? this.specialColor,
      specialTextStyle: specialTextStyle ?? this.specialTextStyle,
    );
  }

  @override
  ThemeExtension<CustomThemeExtension> lerp(
    covariant ThemeExtension<CustomThemeExtension>? other,
    double t,
  ) {
    if (other is! CustomThemeExtension) {
      return this;
    }
    return CustomThemeExtension(
      specialColor: Color.lerp(specialColor, other.specialColor, t)!,
      specialTextStyle: TextStyle.lerp(specialTextStyle, other.specialTextStyle, t)!,
    );
  }
}
```

应用自定义主题扩展：

```dart
final myLightTheme = ThemeData.light().copyWith(
  extensions: [
    CustomThemeExtension(
      specialColor: Colors.deepPurple,
      specialTextStyle: TextStyle(fontSize: 18, fontWeight: FontWeight.w500),
    ),
  ],
);

// 在组件中使用
final customTheme = Theme.of(context).extension<CustomThemeExtension>()!;
Text('Custom Text', style: customTheme.specialTextStyle);
```

### 平台特性与兼容性

**平台特性**：
- Flutter 的主题系统基于 Material Design，但可通过 `CupertinoApp` 实现 iOS 风格
- 支持响应式布局和不同设备密度
- 内置支持深色模式和系统主题跟随
- 支持 RTL（从右到左）布局

**兼容性**：
- 可在所有 Flutter 支持的平台上使用（Android、iOS、Web、桌面等）
- 低版本设备上某些新主题特性可能需要降级支持
- Material 3 特性在 Flutter 3.10+ 中全面支持

### 完整示例

下面是一个完整的 Flutter 主题管理示例，包含亮暗主题切换和自定义主题扩展：

```dart
import 'package:flutter/material.dart';

// 自定义主题扩展
class MyThemeExtension extends ThemeExtension<MyThemeExtension> {
  final Color cardBorderColor;
  final Radius cornerRadius;

  MyThemeExtension({
    required this.cardBorderColor,
    required this.cornerRadius,
  });

  @override
  MyThemeExtension copyWith({
    Color? cardBorderColor,
    Radius? cornerRadius,
  }) {
    return MyThemeExtension(
      cardBorderColor: cardBorderColor ?? this.cardBorderColor,
      cornerRadius: cornerRadius ?? this.cornerRadius,
    );
  }

  @override
  ThemeExtension<MyThemeExtension> lerp(
    covariant ThemeExtension<MyThemeExtension>? other,
    double t,
  ) {
    if (other is! MyThemeExtension) {
      return this;
    }
    return MyThemeExtension(
      cardBorderColor: Color.lerp(cardBorderColor, other.cardBorderColor, t)!,
      cornerRadius: Radius.lerp(cornerRadius, other.cornerRadius, t)!,
    );
  }
}

// 主题数据
class AppTheme {
  static final ThemeData lightTheme = ThemeData.light().copyWith(
    colorScheme: ColorScheme.light(
      primary: Colors.blue,
      secondary: Colors.lightBlue,
      surface: Colors.white,
      background: Color(0xFFF5F5F5),
    ),
    appBarTheme: AppBarTheme(
      backgroundColor: Colors.blue,
      foregroundColor: Colors.white,
      elevation: 2,
    ),
    cardTheme: CardTheme(
      color: Colors.white,
      elevation: 2,
      margin: EdgeInsets.all(8),
    ),
    textTheme: TextTheme(
      titleLarge: TextStyle(fontSize: 22, fontWeight: FontWeight.bold, color: Colors.black87),
      bodyMedium: TextStyle(fontSize: 16, color: Colors.black54),
    ),
    extensions: [
      MyThemeExtension(
        cardBorderColor: Colors.blue.shade200,
        cornerRadius: Radius.circular(12),
      ),
    ],
  );

  static final ThemeData darkTheme = ThemeData.dark().copyWith(
    colorScheme: ColorScheme.dark(
      primary: Colors.blueAccent,
      secondary: Colors.lightBlueAccent,
      surface: Color(0xFF303030),
      background: Color(0xFF121212),
    ),
    appBarTheme: AppBarTheme(
      backgroundColor: Color(0xFF1F1F1F),
      foregroundColor: Colors.white,
      elevation: 0,
    ),
    cardTheme: CardTheme(
      color: Color(0xFF2C2C2C),
      elevation: 4,
      margin: EdgeInsets.all(8),
    ),
    textTheme: TextTheme(
      titleLarge: TextStyle(fontSize: 22, fontWeight: FontWeight.bold, color: Colors.white),
      bodyMedium: TextStyle(fontSize: 16, color: Colors.white70),
    ),
    extensions: [
      MyThemeExtension(
        cardBorderColor: Colors.blueAccent.withOpacity(0.3),
        cornerRadius: Radius.circular(8),
      ),
    ],
  );
}

// 主应用
void main() {
  runApp(const MyApp());
}

class MyApp extends StatefulWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  ThemeMode _themeMode = ThemeMode.system;

  void _toggleTheme() {
    setState(() {
      _themeMode = _themeMode == ThemeMode.light
          ? ThemeMode.dark
          : ThemeMode.light;
    });
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Theme Demo',
      theme: AppTheme.lightTheme,
      darkTheme: AppTheme.darkTheme,
      themeMode: _themeMode,
      home: HomePage(toggleTheme: _toggleTheme),
    );
  }
}

// 首页
class HomePage extends StatelessWidget {
  final VoidCallback toggleTheme;

  const HomePage({Key? key, required this.toggleTheme}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final theme = Theme.of(context);
    final myExtension = theme.extension<MyThemeExtension>()!;
    
    return Scaffold(
      appBar: AppBar(
        title: Text('主题演示'),
        actions: [
          IconButton(
            icon: Icon(Icons.brightness_6),
            onPressed: toggleTheme,
            tooltip: '切换主题',
          ),
        ],
      ),
      body: Center(
        child: Padding(
          padding: const EdgeInsets.all(16.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Text(
                '主题示例',
                style: theme.textTheme.titleLarge,
              ),
              SizedBox(height: 20),
              // 使用自定义主题扩展
              Container(
                padding: EdgeInsets.all(16),
                decoration: BoxDecoration(
                  color: theme.cardTheme.color,
                  borderRadius: BorderRadius.all(myExtension.cornerRadius),
                  border: Border.all(color: myExtension.cardBorderColor, width: 2),
                ),
                child: Text(
                  '这是一个使用自定义主题扩展的容器',
                  style: theme.textTheme.bodyMedium,
                ),
              ),
              SizedBox(height: 20),
              // 标准组件
              ElevatedButton(
                onPressed: () {},
                child: Text('主题按钮'),
              ),
              SizedBox(height: 10),
              // 局部主题覆盖
              Theme(
                data: theme.copyWith(
                  colorScheme: theme.colorScheme.copyWith(
                    primary: Colors.amber,
                  ),
                ),
                child: ElevatedButton(
                  onPressed: () {},
                  child: Text('局部主题按钮'),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}

## Android Jetpack Compose Theme

### 配置方式

Jetpack Compose 使用 Material Design 3 的 `MaterialTheme` 组合函数来定义应用主题。主题通过三个主要参数配置：`colorScheme`、`typography` 和 `shapes`：

```kotlin
// 创建颜色方案
val LightColorScheme = lightColorScheme(
    primary = md_theme_light_primary,
    onPrimary = md_theme_light_onPrimary,
    secondary = md_theme_light_secondary,
    onSecondary = md_theme_light_onSecondary,
    tertiary = md_theme_light_tertiary,
    onTertiary = md_theme_light_onTertiary,
    background = md_theme_light_background,
    onBackground = md_theme_light_onBackground,
    surface = md_theme_light_surface,
    onSurface = md_theme_light_onSurface,
)

val DarkColorScheme = darkColorScheme(
    primary = md_theme_dark_primary,
    onPrimary = md_theme_dark_onPrimary,
    secondary = md_theme_dark_secondary,
    onSecondary = md_theme_dark_onSecondary,
    tertiary = md_theme_dark_tertiary,
    onTertiary = md_theme_dark_onTertiary,
    background = md_theme_dark_background,
    onBackground = md_theme_dark_onBackground,
    surface = md_theme_dark_surface,
    onSurface = md_theme_dark_onSurface,
)

// 自定义字体
val customTypography = Typography(
    bodyLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 16.sp,
        lineHeight = 24.sp,
        letterSpacing = 0.5.sp
    ),
    titleLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Bold,
        fontSize = 22.sp,
        lineHeight = 28.sp,
        letterSpacing = 0.sp
    ),
    // 其他文本样式...
)
```

### 全局/局部应用机制

**全局应用**：通过在应用顶层使用 `MaterialTheme` 组合函数：

```kotlin
@Composable
fun MyApp() {
    val darkTheme = isSystemInDarkTheme()
    val colorScheme = if (darkTheme) DarkColorScheme else LightColorScheme
    
    MaterialTheme(
        colorScheme = colorScheme,
        typography = customTypography,
        shapes = Shapes(
            small = RoundedCornerShape(4.dp),
            medium = RoundedCornerShape(8.dp),
            large = RoundedCornerShape(12.dp)
        )
    ) {
        // 应用内容
        AppNavigation()
    }
}
```

**局部应用**：通过嵌套 `MaterialTheme` 可以在局部区域覆盖主题设置：

```kotlin
MaterialTheme(
    // 基于当前主题，仅修改颜色方案
    colorScheme = MaterialTheme.colorScheme.copy(primary = Red500),
    shapes = MaterialTheme.shapes // 继承其他主题属性
) {
    // 这个区域中的组件将使用修改后的主题
    Button(onClick = { /* ... */ }) {
        Text("局部主题按钮")
    }
}
```

### 主题继承机制

Compose 通过 `copy()` 方法实现主题继承，可以获取当前主题的属性并创建修改后的版本：

```kotlin
// 继承当前颜色方案，修改部分属性
val customColorScheme = MaterialTheme.colorScheme.copy(
    primary = Purple700,
    secondary = Teal200
)

// 使用修改后的颜色方案
MaterialTheme(
    colorScheme = customColorScheme,
    typography = MaterialTheme.typography,
    shapes = MaterialTheme.shapes
) {
    // 使用继承的主题
}
```

Compose 允许创建的组件通过访问环境中的 `MaterialTheme` 对象来获取主题属性：

```kotlin
@Composable
fun MyButton(text: String, onClick: () -> Unit) {
    // 获取当前主题的颜色和形状
    val colors = MaterialTheme.colorScheme
    val shapes = MaterialTheme.shapes
    
    Button(
        onClick = onClick,
        colors = ButtonDefaults.buttonColors(
            containerColor = colors.primary,
            contentColor = colors.onPrimary
        ),
        shape = shapes.medium
    ) {
        Text(text)
    }
}
```

### 动态切换支持

Jetpack Compose 支持动态切换主题，可以基于系统设置或用户选择：

```kotlin
@Composable
fun ThemeAwareApp() {
    // 使用可记忆的状态存储主题模式
    var darkMode by remember { mutableStateOf(isSystemInDarkTheme()) }
    
    // 是否跟随系统
    var followSystem by remember { mutableStateOf(true) }
    
    // 根据状态确定颜色方案
    val colorScheme = when {
        followSystem -> if (isSystemInDarkTheme()) DarkColorScheme else LightColorScheme
        darkMode -> DarkColorScheme
        else -> LightColorScheme
    }
    
    MaterialTheme(
        colorScheme = colorScheme,
        typography = customTypography,
        shapes = customShapes
    ) {
        // 应用内容，包括主题切换控制
        Column {
            Switch(
                checked = followSystem,
                onCheckedChange = { followSystem = it }
            )
            Text("跟随系统主题")
            
            if (!followSystem) {
                Switch(
                    checked = darkMode,
                    onCheckedChange = { darkMode = it }
                )
                Text("深色模式")
            }
            
            // 其他UI内容
        }
    }
}
```

主题切换是即时生效的，所有使用 `MaterialTheme` 属性的组件会自动响应变化。

### 自定义主题扩展性

Jetpack Compose 通过创建自定义 `CompositionLocal` 来扩展主题系统：

```kotlin
// 1. 创建自定义主题数据类
data class CustomThemeValues(
    val specialBackground: Color,
    val customRadius: Dp,
    val brandGradient: List<Color>
)

// 2. 创建CompositionLocal提供者
val LocalCustomTheme = compositionLocalOf {
    CustomThemeValues(
        specialBackground = Color.Magenta,  // 默认值
        customRadius = 16.dp,
        brandGradient = listOf(Color.Blue, Color.Cyan)
    )
}

// 3. 扩展MaterialTheme，提供自定义主题访问器
val MaterialTheme.customTheme: CustomThemeValues
    @Composable
    get() = LocalCustomTheme.current

// 4. 创建包含自定义主题的应用主题
@Composable
fun AppTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit
) {
    // 根据主题模式选择颜色方案
    val colorScheme = if (darkTheme) DarkColorScheme else LightColorScheme
    
    // 创建自定义主题属性
    val customThemeValues = if (darkTheme) {
        CustomThemeValues(
            specialBackground = Color(0xFF2D2D2D),
            customRadius = 12.dp,
            brandGradient = listOf(Color(0xFF1E3B70), Color(0xFF5D9FFF))
        )
    } else {
        CustomThemeValues(
            specialBackground = Color(0xFFF3F3F3),
            customRadius = 16.dp,
            brandGradient = listOf(Color(0xFF0052D4), Color(0xFF65C7F7))
        )
    }
    
    // 提供自定义主题
    CompositionLocalProvider(
        LocalCustomTheme provides customThemeValues
    ) {
        // 提供Material主题
        MaterialTheme(
            colorScheme = colorScheme,
            typography = Typography,
            shapes = Shapes,
            content = content
        )
    }
}

// 5. 在组件中使用自定义主题
@Composable
fun CustomThemedCard(content: @Composable () -> Unit) {
    val customTheme = MaterialTheme.customTheme
    
    Surface(
        color = customTheme.specialBackground,
        shape = RoundedCornerShape(customTheme.customRadius),
        modifier = Modifier.padding(8.dp)
    ) {
        Box(
            modifier = Modifier
                .background(
                    brush = Brush.linearGradient(customTheme.brandGradient),
                    alpha = 0.1f
                )
                .padding(16.dp)
        ) {
            content()
        }
    }
}
```

### 平台特性与兼容性

**平台特性**：
- 基于 Material Design 3 (Material You)
- 动态颜色支持 (Android 12+)
- 适配系统字体缩放
- 支持RTL布局
- 兼容深色模式和系统主题

**兼容性**：
- 需要 Android API 21+ (Android 5.0 Lollipop)
- Material 3 动态颜色需要 Android 12+
- 所有 Compose 功能在较旧设备上都有合适的降级处理

### 完整示例

以下是一个完整的 Jetpack Compose 主题管理示例，包含自定义主题扩展和动态切换：

```kotlin
package com.example.composetheme

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.background
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Brush
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.Dp
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp

// 自定义主题数据
data class CustomThemeValues(
    val specialBackground: Color,
    val customRadius: Dp,
    val brandGradient: List<Color>,
    val customBorderColor: Color
)

// 创建自定义主题提供者
val LocalCustomTheme = compositionLocalOf {
    CustomThemeValues(
        specialBackground = Color.Magenta,
        customRadius = 16.dp,
        brandGradient = listOf(Color.Blue, Color.Cyan),
        customBorderColor = Color.Gray
    )
}

// 扩展MaterialTheme提供自定义主题访问
val MaterialTheme.customTheme: CustomThemeValues
    @Composable
    get() = LocalCustomTheme.current

// 颜色定义
private val md_theme_light_primary = Color(0xFF006E18)
private val md_theme_light_onPrimary = Color(0xFFFFFFFF)
private val md_theme_light_secondary = Color(0xFF516350)
private val md_theme_light_onSecondary = Color(0xFFFFFFFF)
private val md_theme_light_surface = Color(0xFFFCFDF6)
private val md_theme_light_onSurface = Color(0xFF1A1C19)
private val md_theme_light_background = Color(0xFFFCFDF6)
private val md_theme_light_onBackground = Color(0xFF1A1C19)

private val md_theme_dark_primary = Color(0xFF7FDA89)
private val md_theme_dark_onPrimary = Color(0xFF00390A)
private val md_theme_dark_secondary = Color(0xFFB6CCB5)
private val md_theme_dark_onSecondary = Color(0xFF243424)
private val md_theme_dark_surface = Color(0xFF121410)
private val md_theme_dark_onSurface = Color(0xFFE2E3DD)
private val md_theme_dark_background = Color(0xFF121410)
private val md_theme_dark_onBackground = Color(0xFFE2E3DD)

// 定义颜色方案
private val LightColorScheme = lightColorScheme(
    primary = md_theme_light_primary,
    onPrimary = md_theme_light_onPrimary,
    secondary = md_theme_light_secondary,
    onSecondary = md_theme_light_onSecondary,
    surface = md_theme_light_surface,
    onSurface = md_theme_light_onSurface,
    background = md_theme_light_background,
    onBackground = md_theme_light_onBackground
)

private val DarkColorScheme = darkColorScheme(
    primary = md_theme_dark_primary,
    onPrimary = md_theme_dark_onPrimary,
    secondary = md_theme_dark_secondary,
    onSecondary = md_theme_dark_onSecondary,
    surface = md_theme_dark_surface,
    onSurface = md_theme_dark_onSurface,
    background = md_theme_dark_background,
    onBackground = md_theme_dark_onBackground
)

// 应用主题组合函数
@Composable
fun AppTheme(
    darkTheme: Boolean = isSystemInDarkTheme(),
    content: @Composable () -> Unit
) {
    // 根据主题模式选择颜色方案
    val colorScheme = if (darkTheme) DarkColorScheme else LightColorScheme
    
    // 创建自定义主题属性
    val customThemeValues = if (darkTheme) {
        CustomThemeValues(
            specialBackground = Color(0xFF2D2D2D),
            customRadius = 12.dp,
            brandGradient = listOf(Color(0xFF1E3B70), Color(0xFF5D9FFF)),
            customBorderColor = Color(0xFF4D4D4D)
        )
    } else {
        CustomThemeValues(
            specialBackground = Color(0xFFF3F3F3),
            customRadius = 16.dp,
            brandGradient = listOf(Color(0xFF0052D4), Color(0xFF65C7F7)),
            customBorderColor = Color(0xFFDFDFDF)
        )
    }
    
    // 提供自定义主题
    CompositionLocalProvider(
        LocalCustomTheme provides customThemeValues
    ) {
        // 提供Material主题
        MaterialTheme(
            colorScheme = colorScheme,
            typography = Typography(
                bodyLarge = TextStyle(
                    fontWeight = FontWeight.Normal,
                    fontSize = 16.sp,
                    lineHeight = 24.sp,
                    letterSpacing = 0.5.sp
                ),
                titleLarge = TextStyle(
                    fontWeight = FontWeight.Bold,
                    fontSize = 22.sp,
                    lineHeight = 28.sp,
                    letterSpacing = 0.sp
                )
            ),
            shapes = Shapes(
                small = RoundedCornerShape(4.dp),
                medium = RoundedCornerShape(8.dp),
                large = RoundedCornerShape(12.dp)
            ),
            content = content
        )
    }
}

// 主活动
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            ThemeDemo()
        }
    }
}

// 主题演示
@Composable
fun ThemeDemo() {
    // 主题模式状态
    var isDarkMode by remember { mutableStateOf(isSystemInDarkTheme()) }
    var followSystem by remember { mutableStateOf(true) }
    
    // 计算实际使用的主题模式
    val effectiveDarkMode = if (followSystem) isSystemInDarkTheme() else isDarkMode
    
    AppTheme(darkTheme = effectiveDarkMode) {
        Surface(
            modifier = Modifier.fillMaxSize(),
            color = MaterialTheme.colorScheme.background
        ) {
            Column(
                modifier = Modifier
                    .fillMaxSize()
                    .padding(16.dp),
                verticalArrangement = Arrangement.spacedBy(16.dp)
            ) {
                Text(
                    text = "Compose 主题示例",
                    style = MaterialTheme.typography.titleLarge,
                    color = MaterialTheme.colorScheme.onBackground
                )
                
                // 主题切换选项
                Row(
                    verticalAlignment = Alignment.CenterVertically,
                    modifier = Modifier.fillMaxWidth()
                ) {
                    Text(
                        "跟随系统主题",
                        modifier = Modifier.weight(1f)
                    )
                    Switch(
                        checked = followSystem,
                        onCheckedChange = { followSystem = it }
                    )
                }
                
                if (!followSystem) {
                    Row(
                        verticalAlignment = Alignment.CenterVertically,
                        modifier = Modifier.fillMaxWidth()
                    ) {
                        Text(
                            "深色模式",
                            modifier = Modifier.weight(1f)
                        )
                        Switch(
                            checked = isDarkMode,
                            onCheckedChange = { isDarkMode = it }
                        )
                    }
                }
                
                Divider(
                    color = MaterialTheme.customTheme.customBorderColor,
                    thickness = 1.dp
                )
                
                // 标准Material组件
                Button(
                    onClick = { },
                    colors = ButtonDefaults.buttonColors(
                        containerColor = MaterialTheme.colorScheme.primary,
                        contentColor = MaterialTheme.colorScheme.onPrimary
                    )
                ) {
                    Text("标准按钮")
                }
                
                // 使用自定义主题的组件
                CustomThemedCard {
                    Text(
                        "这是使用自定义主题的卡片",
                        style = MaterialTheme.typography.bodyLarge,
                        color = MaterialTheme.colorScheme.onSurface
                    )
                }
                
                // 局部主题覆盖
                MaterialTheme(
                    colorScheme = MaterialTheme.colorScheme.copy(
                        primary = Color(0xFFFF5722),
                        onPrimary = Color.White
                    )
                ) {
                    Button(
                        onClick = { },
                        colors = ButtonDefaults.buttonColors(
                            containerColor = MaterialTheme.colorScheme.primary,
                            contentColor = MaterialTheme.colorScheme.onPrimary
                        )
                    ) {
                        Text("局部主题按钮")
                    }
                }
            }
        }
    }
}

// 自定义主题组件
@Composable
fun CustomThemedCard(content: @Composable () -> Unit) {
    val customTheme = MaterialTheme.customTheme
    
    Card(
        modifier = Modifier.fillMaxWidth(),
        shape = RoundedCornerShape(customTheme.customRadius),
        colors = CardDefaults.cardColors(
            containerColor = customTheme.specialBackground
        )
    ) {
        Box(
            modifier = Modifier
                .fillMaxWidth()
                .background(
                    brush = Brush.linearGradient(customTheme.brandGradient),
                    alpha = 0.1f
                )
                .padding(16.dp)
        ) {
            content()
        }
    }
}
```

## iOS UIKit Theme

### 配置方式

UIKit 从 iOS 13 开始原生支持深色模式，可以响应系统外观变化：

```swift
// 在视图控制器中响应外观变化
override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
    super.traitCollectionDidChange(previousTraitCollection)
    
    // 当外观（如暗色/亮色模式）发生变化时调用
    if traitCollection.hasDifferentColorAppearance(comparedTo: previousTraitCollection) {
        updateTheme()
    }
}

func updateTheme() {
    // 获取当前主题并应用
    let theme = ThemeManager.shared.currentTheme(for: traitCollection)
    ThemeManager.shared.apply(theme: theme, to: view)
    
    // 更新状态栏样式
    setNeedsStatusBarAppearanceUpdate()
}

// 强制特定模式（而不是跟随系统）
func setSpecificMode() {
    if #available(iOS 13.0, *) {
        // 注意：应用强制特定外观必须在窗口级别设置
        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.window?.overrideUserInterfaceStyle = .dark // 或 .light 或 .unspecified（跟随系统）
    }
}
```

还可以实现主题的手动切换：

```swift
// 在视图控制器中
@IBAction func toggleTheme(_ sender: UISwitch) {
    // 获取当前窗口
    guard let window = view.window else { return }
    
    // 切换主题
    if sender.isOn {
        window.overrideUserInterfaceStyle = .dark
    } else {
        window.overrideUserInterfaceStyle = .light
    }
    
    // 更新界面
    UIView.animate(withDuration: 0.3) {
        self.view.layoutIfNeeded()
    }
}
```

### 自定义主题扩展性

UIKit 没有官方的主题扩展系统，但可以通过自定义机制和扩展创建灵活的主题方案：

```swift
// 1. 创建扩展的主题协议
protocol ThemeProvider {
    var primaryColor: UIColor { get }
    var secondaryColor: UIColor { get }
    var backgroundColor: UIColor { get }
    var cardBackgroundColor: UIColor { get }
    var textPrimaryColor: UIColor { get }
    var textSecondaryColor: UIColor { get }
    var borderColor: UIColor { get }
    var cornerRadius: CGFloat { get }
    var defaultFont: UIFont { get }
    var titleFont: UIFont { get }
    var buttonHeight: CGFloat { get }
}

// 2. 创建主题实现
struct DefaultTheme: ThemeProvider {
    var userInterfaceStyle: UIUserInterfaceStyle
    
    var primaryColor: UIColor {
        return .systemBlue
    }
    
    var secondaryColor: UIColor {
        return .systemGreen
    }
    
    var backgroundColor: UIColor {
        return userInterfaceStyle == .dark ? .black : .white
    }
    
    var cardBackgroundColor: UIColor {
        return userInterfaceStyle == .dark ? UIColor(white: 0.15, alpha: 1.0) : UIColor(white: 0.95, alpha: 1.0)
    }
    
    var textPrimaryColor: UIColor {
        return userInterfaceStyle == .dark ? .white : .black
    }
    
    var textSecondaryColor: UIColor {
        return userInterfaceStyle == .dark ? .lightGray : .darkGray
    }
    
    var borderColor: UIColor {
        return userInterfaceStyle == .dark ? UIColor(white: 0.3, alpha: 1.0) : UIColor(white: 0.8, alpha: 1.0)
    }
    
    var cornerRadius: CGFloat {
        return 8.0
    }
    
    var defaultFont: UIFont {
        return UIFont.systemFont(ofSize: 16)
    }
    
    var titleFont: UIFont {
        return UIFont.boldSystemFont(ofSize: 20)
    }
    
    var buttonHeight: CGFloat {
        return 44.0
    }
}

// 3. 创建主题管理类
class ThemeService {
    static let shared = ThemeService()
    
    private(set) var currentTheme: ThemeProvider
    
    init() {
        // 初始化时根据系统设置选择主题
        let userInterfaceStyle = UITraitCollection.current.userInterfaceStyle
        currentTheme = DefaultTheme(userInterfaceStyle: userInterfaceStyle)
    }
    
    func updateTheme(for traitCollection: UITraitCollection) {
        // 当系统外观变化时更新主题
        currentTheme = DefaultTheme(userInterfaceStyle: traitCollection.userInterfaceStyle)
        NotificationCenter.default.post(name: NSNotification.Name("ThemeDidChangeNotification"), object: nil)
    }
}

// 4. 为UI组件添加快捷方法
extension UIView {
    func applyTheme() {
        let theme = ThemeService.shared.currentTheme
        backgroundColor = theme.backgroundColor
        layer.cornerRadius = theme.cornerRadius
    }
}

extension UILabel {
    func applyTheme(isPrimary: Bool = true) {
        let theme = ThemeService.shared.currentTheme
        textColor = isPrimary ? theme.textPrimaryColor : theme.textSecondaryColor
        font = theme.defaultFont
    }
    
    func applyTitleTheme() {
        let theme = ThemeService.shared.currentTheme
        textColor = theme.textPrimaryColor
        font = theme.titleFont
    }
}

extension UIButton {
    func applyPrimaryTheme() {
        let theme = ThemeService.shared.currentTheme
        backgroundColor = theme.primaryColor
        setTitleColor(.white, for: .normal)
        layer.cornerRadius = theme.cornerRadius
    }
}
```

### 平台特性与兼容性

**平台特性**：
- iOS 13+ 原生支持深色模式
- 通过 `UITraitCollection` 支持不同环境特性（如UI尺寸、显示比例等）
- 支持动态类型（可访问性文本大小）
- 支持RTL布局
- 支持系统颜色（自动适应深色/浅色模式）

**兼容性**：
- 深色模式需要 iOS 13+
- 自定义外观设置和动态类型支持在所有现代iOS版本上可用
- 较老的设备需要特殊处理以兼容新的主题特性

### 完整示例

下面是一个完整的 UIKit 主题管理示例，实现了自定义主题系统和动态切换：

```swift
import UIKit

// MARK: - 主题协议
protocol ThemeProvider {
    var primaryColor: UIColor { get }
    var secondaryColor: UIColor { get }
    var backgroundColor: UIColor { get }
    var surfaceColor: UIColor { get }
    var textPrimaryColor: UIColor { get }
    var textSecondaryColor: UIColor { get }
    var borderColor: UIColor { get }
    var dividerColor: UIColor { get }
    var cornerRadius: CGFloat { get }
    var bodyFont: UIFont { get }
    var titleFont: UIFont { get }
    var buttonFont: UIFont { get }
}

// MARK: - 默认主题实现
struct DefaultTheme: ThemeProvider {
    private let isDark: Bool
    
    init(isDark: Bool) {
        self.isDark = isDark
    }
    
    var primaryColor: UIColor {
        return .systemBlue
    }
    
    var secondaryColor: UIColor {
        return .systemGreen
    }
    
    var backgroundColor: UIColor {
        return isDark ? .black : .white
    }
    
    var surfaceColor: UIColor {
        return isDark ? UIColor(white: 0.15, alpha: 1.0) : UIColor(white: 0.97, alpha: 1.0)
    }
    
    var textPrimaryColor: UIColor {
        return isDark ? .white : .black
    }
    
    var textSecondaryColor: UIColor {
        return isDark ? .lightGray : .darkGray
    }
    
    var borderColor: UIColor {
        return primaryColor.withAlphaComponent(0.3)
    }
    
    var dividerColor: UIColor {
        return isDark ? UIColor(white: 0.3, alpha: 1.0) : UIColor(white: 0.9, alpha: 1.0)
    }
    
    var cornerRadius: CGFloat {
        return 8.0
    }
    
    var bodyFont: UIFont {
        return UIFont.systemFont(ofSize: 16)
    }
    
    var titleFont: UIFont {
        return UIFont.boldSystemFont(ofSize: 20)
    }
    
    var buttonFont: UIFont {
        return UIFont.systemFont(ofSize: 16, weight: .medium)
    }
}

// MARK: - 品牌主题实现
struct BrandTheme: ThemeProvider {
    private let isDark: Bool
    
    init(isDark: Bool) {
        self.isDark = isDark
    }
    
    var primaryColor: UIColor {
        return UIColor(red: 0.6, green: 0.2, blue: 0.8, alpha: 1.0) // 紫色
    }
    
    var secondaryColor: UIColor {
        return UIColor(red: 1.0, green: 0.5, blue: 0.0, alpha: 1.0) // 橙色
    }
    
    var backgroundColor: UIColor {
        return isDark ? UIColor(white: 0.1, alpha: 1.0) : UIColor(white: 0.98, alpha: 1.0)
    }
    
    var surfaceColor: UIColor {
        return isDark ? UIColor(white: 0.2, alpha: 1.0) : UIColor(white: 0.95, alpha: 1.0)
    }
    
    var textPrimaryColor: UIColor {
        return isDark ? .white : UIColor(white: 0.1, alpha: 1.0)
    }
    
    var textSecondaryColor: UIColor {
        return isDark ? UIColor(white: 0.7, alpha: 1.0) : UIColor(white: 0.3, alpha: 1.0)
    }
    
    var borderColor: UIColor {
        return primaryColor.withAlphaComponent(0.3)
    }
    
    var dividerColor: UIColor {
        return isDark ? UIColor(white: 0.3, alpha: 1.0) : UIColor(white: 0.9, alpha: 1.0)
    }
    
    var cornerRadius: CGFloat {
        return 12.0
    }
    
    var bodyFont: UIFont {
        return UIFont(name: "Avenir-Medium", size: 16) ?? .systemFont(ofSize: 16)
    }
    
    var titleFont: UIFont {
        return UIFont(name: "Avenir-Heavy", size: 20) ?? .boldSystemFont(ofSize: 20)
    }
    
    var buttonFont: UIFont {
        return UIFont(name: "Avenir-Black", size: 16) ?? .systemFont(ofSize: 16, weight: .bold)
    }
}

// MARK: - 主题管理器
class ThemeManager {
    static let shared = ThemeManager()
    
    // 主题变化通知
    static let themeChangedNotification = NSNotification.Name("ThemeChangedNotification")
    
    // 主题类型枚举
    enum ThemeType {
        case `default`
        case brand
    }
    
    // 当前主题类型
    private(set) var currentThemeType: ThemeType = .default
    
    // 当前主题提供者
    private(set) var currentTheme: ThemeProvider!
    
    // 初始化
    private init() {
        // 默认跟随系统设置
        updateTheme(for: UITraitCollection.current)
    }
    
    // 根据特性集合更新主题
    func updateTheme(for traitCollection: UITraitCollection) {
        let isDark = traitCollection.userInterfaceStyle == .dark
        
        switch currentThemeType {
        case .default:
            currentTheme = DefaultTheme(isDark: isDark)
        case .brand:
            currentTheme = BrandTheme(isDark: isDark)
        }
        
        // 通知主题变化
        NotificationCenter.default.post(name: ThemeManager.themeChangedNotification, object: nil)
    }
    
    // 切换主题类型
    func switchTheme(to themeType: ThemeType) {
        currentThemeType = themeType
        updateTheme(for: UITraitCollection.current)
    }
}

// MARK: - UI组件扩展
extension UIView {
    func applyTheme() {
        let theme = ThemeManager.shared.currentTheme
        backgroundColor = theme.backgroundColor
    }
}

extension UILabel {
    func applyThemeStyle(isTitle: Bool = false, isSecondary: Bool = false) {
        let theme = ThemeManager.shared.currentTheme
        font = isTitle ? theme.titleFont : theme.bodyFont
        textColor = isSecondary ? theme.textSecondaryColor : theme.textPrimaryColor
    }
}

extension UIButton {
    func applyPrimaryStyle() {
        let theme = ThemeManager.shared.currentTheme
        backgroundColor = theme.primaryColor
        setTitleColor(.white, for: .normal)
        layer.cornerRadius = theme.cornerRadius
    }
    
    func applySecondaryStyle() {
        let theme = ThemeManager.shared.currentTheme
        backgroundColor = .clear
        setTitleColor(theme.primaryColor, for: .normal)
        setTitleColor(theme.primaryColor.withAlphaComponent(0.7), for: .highlighted)
        setTitleColor(.gray, for: .disabled)
        titleLabel?.font = theme.buttonFont
        layer.cornerRadius = theme.cornerRadius
        layer.borderWidth = 1
        layer.borderColor = theme.primaryColor.cgColor
    }
}

// MARK: - 基础视图控制器
class BaseViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        applyTheme()
        
        // 注册主题变化通知
        NotificationCenter.default.addObserver(
            self,
            selector: #selector(themeDidChange),
            name: ThemeManager.themeChangedNotification,
            object: nil
        )
    }
    
    override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
        super.traitCollectionDidChange(previousTraitCollection)
        
        if traitCollection.hasDifferentColorAppearance(comparedTo: previousTraitCollection) {
            ThemeManager.shared.updateTheme(for: traitCollection)
        }
    }
    
    @objc func themeDidChange() {
        applyTheme()
    }
    
    func applyTheme() {
        view.applyTheme()
    }
    
    deinit {
        NotificationCenter.default.removeObserver(self)
    }
}

// MARK: - 示例视图控制器
class ThemeDemoViewController: BaseViewController {
    
    private let titleLabel = UILabel()
    private let descriptionLabel = UILabel()
    private let cardView = UIView()
    private let cardTitleLabel = UILabel()
    private let cardDescriptionLabel = UILabel()
    private let primaryButton = UIButton(type: .system)
    private let secondaryButton = UIButton(type: .system)
    private let themeSwitch = UISwitch()
    private let themeSwitchLabel = UILabel()
    private let divider = UIView()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupViews()
        setupConstraints()
    }
    
    private func setupViews() {
        // 标题标签
        titleLabel.text = "UIKit 主题演示"
        view.addSubview(titleLabel)
        
        // 描述标签
        descriptionLabel.text = "这是一个使用自定义主题系统的 UIKit 示例。"
        descriptionLabel.numberOfLines = 0
        view.addSubview(descriptionLabel)
        
        // 分隔线
        view.addSubview(divider)
        
        // 卡片视图
        cardView.layer.cornerRadius = ThemeManager.shared.currentTheme.cornerRadius
        cardView.clipsToBounds = true
        view.addSubview(cardView)
        
        // 卡片标题
        cardTitleLabel.text = "主题卡片"
        cardView.addSubview(cardTitleLabel)
        
        // 卡片描述
        cardDescriptionLabel.text = "这个卡片使用了当前主题的样式进行渲染，会随着主题切换而变化。"
        cardDescriptionLabel.numberOfLines = 0
        cardView.addSubview(cardDescriptionLabel)
        
        // 主按钮
        primaryButton.setTitle("主按钮", for: .normal)
        primaryButton.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside)
        view.addSubview(primaryButton)
        
        // 次按钮
        secondaryButton.setTitle("次按钮", for: .normal)
        secondaryButton.addTarget(self, action: #selector(buttonTapped), for: .touchUpInside)
        view.addSubview(secondaryButton)
        
        // 主题开关标签
        themeSwitchLabel.text = "品牌主题"
        view.addSubview(themeSwitchLabel)
        
        // 主题开关
        themeSwitch.isOn = ThemeManager.shared.currentThemeType == .brand
        themeSwitch.addTarget(self, action: #selector(themeSwitchChanged), for: .valueChanged)
        view.addSubview(themeSwitch)
        
        // 应用主题
        applyTheme()
    }
    
    private func setupConstraints() {
        // 启用自动布局
        titleLabel.translatesAutoresizingMaskIntoConstraints = false
        descriptionLabel.translatesAutoresizingMaskIntoConstraints = false
        divider.translatesAutoresizingMaskIntoConstraints = false
        cardView.translatesAutoresizingMaskIntoConstraints = false
        cardTitleLabel.translatesAutoresizingMaskIntoConstraints = false
        cardDescriptionLabel.translatesAutoresizingMaskIntoConstraints = false
        primaryButton.translatesAutoresizingMaskIntoConstraints = false
        secondaryButton.translatesAutoresizingMaskIntoConstraints = false
        themeSwitchLabel.translatesAutoresizingMaskIntoConstraints = false
        themeSwitch.translatesAutoresizingMaskIntoConstraints = false
        
        // 设置约束
        NSLayoutConstraint.activate([
            // 标题标签
            titleLabel.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 20),
            titleLabel.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            titleLabel.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            
            // 描述标签
            descriptionLabel.topAnchor.constraint(equalTo: titleLabel.bottomAnchor, constant: 8),
            descriptionLabel.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            descriptionLabel.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            
            // 分隔线
            divider.topAnchor.constraint(equalTo: descriptionLabel.bottomAnchor, constant: 20),
            divider.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            divider.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            divider.heightAnchor.constraint(equalToConstant: 1),
            
            // 卡片视图
            cardView.topAnchor.constraint(equalTo: divider.bottomAnchor, constant: 20),
            cardView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            cardView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            
            // 卡片标题
            cardTitleLabel.topAnchor.constraint(equalTo: cardView.topAnchor, constant: 16),
            cardTitleLabel.leadingAnchor.constraint(equalTo: cardView.leadingAnchor, constant: 16),
            cardTitleLabel.trailingAnchor.constraint(equalTo: cardView.trailingAnchor, constant: -16),
            
            // 卡片描述
            cardDescriptionLabel.topAnchor.constraint(equalTo: cardTitleLabel.bottomAnchor, constant: 8),
            cardDescriptionLabel.leadingAnchor.constraint(equalTo: cardView.leadingAnchor, constant: 16),
            cardDescriptionLabel.trailingAnchor.constraint(equalTo: cardView.trailingAnchor, constant: -16),
            cardDescriptionLabel.bottomAnchor.constraint(equalTo: cardView.bottomAnchor, constant: -16),
            
            // 主按钮
            primaryButton.topAnchor.constraint(equalTo: cardView.bottomAnchor, constant: 20),
            primaryButton.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            primaryButton.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            primaryButton.heightAnchor.constraint(equalToConstant: 44),
            
            // 次按钮
            secondaryButton.topAnchor.constraint(equalTo: primaryButton.bottomAnchor, constant: 12),
            secondaryButton.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            secondaryButton.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
            secondaryButton.heightAnchor.constraint(equalToConstant: 44),
            
            // 主题开关标签
            themeSwitchLabel.topAnchor.constraint(equalTo: secondaryButton.bottomAnchor, constant: 24),
            themeSwitchLabel.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            
            // 主题开关
            themeSwitch.centerYAnchor.constraint(equalTo: themeSwitchLabel.centerYAnchor),
            themeSwitch.leadingAnchor.constraint(equalTo: themeSwitchLabel.trailingAnchor, constant: 16),
            themeSwitch.trailingAnchor.constraint(lessThanOrEqualTo: view.trailingAnchor, constant: -20)
        ])
    }
    
    override func applyTheme() {
        super.applyTheme()
        
        let theme = ThemeManager.shared.currentTheme
        
        // 应用标签主题
        titleLabel.applyThemeStyle(isTitle: true)
        descriptionLabel.applyThemeStyle(isSecondary: true)
        themeSwitchLabel.applyThemeStyle()
        
        // 应用卡片主题
        cardView.backgroundColor = theme.surfaceColor
        cardView.layer.cornerRadius = theme.cornerRadius
        cardView.layer.borderWidth = 1
        cardView.layer.borderColor = theme.borderColor.cgColor
        
        // 应用卡片内容主题
        cardTitleLabel.applyThemeStyle(isTitle: true)
        cardDescriptionLabel.applyThemeStyle(isSecondary: true)
        
        // 应用按钮主题
        primaryButton.applyPrimaryStyle()
        secondaryButton.applySecondaryStyle()
        
        // 应用分隔线主题
        divider.backgroundColor = theme.dividerColor
    }
    
    @objc private func buttonTapped() {
        // 演示用，实际应用中会有相应功能
        let alert = UIAlertController(
            title: "按钮点击",
            message: "这是一个演示按钮点击的弹窗。",
            preferredStyle: .alert
        )
        alert.addAction(UIAlertAction(title: "确定", style: .default))
        present(alert, animated: true)
    }
    
    @objc private func themeSwitchChanged() {
        let newThemeType: ThemeManager.ThemeType = themeSwitch.isOn ? .brand : .default
        ThemeManager.shared.switchTheme(to: newThemeType)
    }
}

// MARK: - 应用代理实现
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?
    
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        window = UIWindow(frame: UIScreen.main.bounds)
        window?.rootViewController = UINavigationController(rootViewController: ThemeDemoViewController())
        window?.makeKeyAndVisible()
        return true
    }
}
```

## iOS SwiftUI Theme

### 配置方式

SwiftUI 使用环境属性（Environment）和 Environment Values 来管理主题，结合 `@Environment` 属性包装器可以访问系统提供的主题属性：

```swift
struct ContentView: View {
    // 获取系统颜色方案
    @Environment(\.colorScheme) var colorScheme
    
    var body: some View {
        Text("Hello, World!")
            .foregroundColor(colorScheme == .dark ? .white : .black)
            .background(colorScheme == .dark ? Color.black : Color.white)
    }
}
```

自定义颜色使用 Asset Catalog 中的颜色集，支持深色/浅色模式：

```swift
// 使用颜色资源
Color("primaryColor") // 在资源目录中定义的颜色集

// 创建动态颜色
let textColor = Color(UIColor { traitCollection in
    traitCollection.userInterfaceStyle == .dark ? UIColor.white : UIColor.black
})
```

### 全局/局部应用机制

**全局应用**：在 `App` 结构体中使用环境修饰符设置应用范围的主题：

```swift
@main
struct MyApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
                // 设置全局首选颜色方案
                .preferredColorScheme(.light) // 或 .dark 或 nil（跟随系统）
                // 设置全局强调色
                .accentColor(.blue)
                // 设置全局字体
                .font(.system(.body))
        }
    }
}
```

**局部应用**：通过视图修饰符可以在局部范围覆盖主题设置：

```swift
VStack {
    Text("使用默认主题")

    Text("使用局部主题")
        .foregroundColor(.white)
        .background(Color.blue)
        .font(.headline)
}
.accentColor(.purple) // 为此VStack及其子视图设置强调色
```

### 主题继承机制

SwiftUI 中主题通过环境值（Environment Values）继承，子视图会继承父视图的环境设置，除非显式覆盖：

```swift
struct ContentView: View {
    var body: some View {
        // 父视图设置环境值
        VStack {
            Text("继承父主题")
            
            // 子视图覆盖部分环境值
            Text("部分覆盖主题")
                .environment(\.font, .headline)
            
            // 嵌套子视图也能继承环境值
            SubView()
        }
        .environment(\.font, .body)
        .environment(\.foregroundColor, .blue)
    }
}

struct SubView: View {
    // 自动继承父视图的环境值
    var body: some View {
        Text("嵌套视图")
    }
}
```

### 动态切换支持

SwiftUI 内置支持深色模式切换，可以基于系统设置或手动指定：

```swift
struct ThemeSwitcherView: View {
    // 应用程序主题状态
    @State private var useSystemTheme: Bool = true
    @State private var isDarkMode: Bool = false
    
    var body: some View {
        VStack {
            Toggle("使用系统主题", isOn: $useSystemTheme)
                .padding()
            
            if !useSystemTheme {
                Toggle("深色模式", isOn: $isDarkMode)
                    .padding()
            }
            
            Text("当前主题示例")
                .padding()
                .background(Color(.systemBackground))
                .foregroundColor(Color(.label))
                .cornerRadius(8)
            
            Spacer()
        }
        .padding()
        // 根据选择应用主题
        .preferredColorScheme(useSystemTheme ? nil : (isDarkMode ? .dark : .light))
    }
}
```

可以通过状态管理监听和响应系统主题变化：

```swift
class ThemeManager: ObservableObject {
    @Published var colorScheme: ColorScheme?
    
    init() {
        // 初始时跟随系统
        self.colorScheme = nil
    }
    
    func toggleTheme() {
        // 在亮色/暗色之间切换
        if colorScheme == .dark {
            colorScheme = .light
        } else if colorScheme == .light {
            colorScheme = .dark
        } else {
            // 如果原来是系统主题，则设为暗色
            colorScheme = .dark
        }
    }
    
    func useSystemTheme() {
        colorScheme = nil
    }
}

// 在应用中使用
@main
struct MyApp: App {
    @StateObject private var themeManager = ThemeManager()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(themeManager)
                .preferredColorScheme(themeManager.colorScheme)
        }
    }
}

// 在视图中使用和切换
struct ContentView: View {
    @EnvironmentObject var themeManager: ThemeManager
    
    var body: some View {
        VStack {
            Button("切换主题") {
                themeManager.toggleTheme()
            }
            
            Button("使用系统主题") {
                themeManager.useSystemTheme()
            }
        }
    }
}
```

### 自定义主题扩展性

SwiftUI 通过创建自定义环境值和主题对象来扩展主题系统：

```swift
// 1. 定义自定义主题结构体
struct AppTheme {
    let primaryColor: Color
    let secondaryColor: Color
    let backgroundColor: Color
    let textColor: Color
    let accentColor: Color
    let cornerRadius: CGFloat
    let padding: CGFloat
    
    // 预定义主题
    static let light = AppTheme(
        primaryColor: Color(.systemBlue),
        secondaryColor: Color(.systemGreen),
        backgroundColor: Color.white,
        textColor: Color.black,
        accentColor: Color(.systemOrange),
        cornerRadius: 8,
        padding: 16
    )
    
    static let dark = AppTheme(
        primaryColor: Color(.systemBlue),
        secondaryColor: Color(.systemGreen),
        backgroundColor: Color.black,
        textColor: Color.white,
        accentColor: Color(.systemOrange),
        cornerRadius: 8,
        padding: 16
    )
    
    // 品牌主题
    static let brand = AppTheme(
        primaryColor: Color(red: 0.6, green: 0.2, blue: 0.8),
        secondaryColor: Color(red: 1.0, green: 0.5, blue: 0.0),
        backgroundColor: Color(red: 0.98, green: 0.97, blue: 1.0),
        textColor: Color(red: 0.1, green: 0.1, blue: 0.1),
        accentColor: Color(red: 0.8, green: 0.3, blue: 0.7),
        cornerRadius: 12,
        padding: 20
    )
    
    // 品牌深色主题
    static let brandDark = AppTheme(
        primaryColor: Color(red: 0.7, green: 0.3, blue: 0.9),
        secondaryColor: Color(red: 1.0, green: 0.6, blue: 0.1),
        backgroundColor: Color(red: 0.1, green: 0.05, blue: 0.15),
        textColor: Color.white,
        accentColor: Color(red: 0.9, green: 0.4, blue: 0.8),
        cornerRadius: 12,
        padding: 20
    )
}

// 2. 创建自定义环境键
struct ThemeKey: EnvironmentKey {
    static let defaultValue = AppTheme.light
}

// 3. 扩展EnvironmentValues以包含自定义主题
extension EnvironmentValues {
    var appTheme: AppTheme {
        get { self[ThemeKey.self] }
        set { self[ThemeKey.self] = newValue }
    }
}

// 4. 为View添加便捷修饰符
extension View {
    func appTheme(_ theme: AppTheme) -> some View {
        environment(\.appTheme, theme)
    }
}

// 5. 创建主题管理对象
class ThemeManager: ObservableObject {
    enum ThemeType {
        case system
        case light
        case dark
        case brand
    }
    
    @Published var themeType: ThemeType = .system
    
    // 基于当前主题类型和系统设置获取实际主题
    func resolvedTheme(for colorScheme: ColorScheme) -> AppTheme {
        switch themeType {
        case .light:
            return .light
        case .dark:
            return .dark
        case .brand:
            return colorScheme == .dark ? .brandDark : .brand
        case .system:
            return colorScheme == .dark ? .dark : .light
        }
    }
    
    // 切换到下一个主题类型
    func cycleTheme() {
        switch themeType {
        case .system:
            themeType = .light
        case .light:
            themeType = .dark
        case .dark:
            themeType = .brand
        case .brand:
            themeType = .system
        }
    }
}
```

### 平台特性与兼容性

**平台特性**：
- 内置支持深色模式和动态类型
- 支持 Dynamic Type 可访问性文字大小
- 自动支持 RTL 布局
- 支持系统颜色和图像资产适配
- 支持 SF Symbols 和可缩放图标

**兼容性**：
- 需要 iOS 13+ / macOS 10.15+
- 新的主题特性在较新系统版本上逐步增加
- 可以通过 UIKit 和 AppKit 桥接增强兼容性

### 完整示例

下面是一个完整的 SwiftUI 主题管理示例：

```swift
import SwiftUI

// MARK: - 自定义主题定义
struct AppTheme {
    // 颜色
    let primaryColor: Color
    let secondaryColor: Color
    let backgroundColor: Color
    let cardColor: Color
    let textPrimaryColor: Color
    let textSecondaryColor: Color
    
    // 尺寸
    let cornerRadius: CGFloat
    let padding: CGFloat
    
    // 字体
    let titleFont: Font
    let bodyFont: Font
    let buttonFont: Font
    
    // 预定义主题 - 浅色
    static let light = AppTheme(
        primaryColor: .blue,
        secondaryColor: .green,
        backgroundColor: Color(.systemBackground),
        cardColor: Color(.secondarySystemBackground),
        textPrimaryColor: .primary,
        textSecondaryColor: .secondary,
        cornerRadius: 10,
        padding: 16,
        titleFont: .title.bold(),
        bodyFont: .body,
        buttonFont: .headline
    )
    
    // 预定义主题 - 深色
    static let dark = AppTheme(
        primaryColor: .blue,
        secondaryColor: .green,
        backgroundColor: Color(.systemBackground),
        cardColor: Color(.secondarySystemBackground),
        textPrimaryColor: .primary,
        textSecondaryColor: .secondary,
        cornerRadius: 10,
        padding: 16,
        titleFont: .title.bold(),
        bodyFont: .body,
        buttonFont: .headline
    )
    
    // 自定义品牌主题 - 浅色
    static let brandLight = AppTheme(
        primaryColor: Color(red: 0.6, green: 0.2, blue: 0.8),
        secondaryColor: Color(red: 1.0, green: 0.5, blue: 0.0),
        backgroundColor: Color(red: 0.98, green: 0.97, blue: 1.0),
        cardColor: .white,
        textPrimaryColor: Color(red: 0.2, green: 0.2, blue: 0.3),
        textSecondaryColor: Color(red: 0.4, green: 0.4, blue: 0.5),
        cornerRadius: 16,
        padding: 20,
        titleFont: .system(size: 28, weight: .black, design: .default),
        bodyFont: .system(size: 16, weight: .medium, design: .default),
        buttonFont: .system(size: 18, weight: .bold, design: .default)
    )
    
    // 自定义品牌主题 - 深色
    static let brandDark = AppTheme(
        primaryColor: Color(red: 0.7, green: 0.3, blue: 0.9),
        secondaryColor: Color(red: 1.0, green: 0.6, blue: 0.0),
        backgroundColor: Color(red: 0.1, green: 0.05, blue: 0.15),
        cardColor: Color(red: 0.2, green: 0.1, blue: 0.25),
        textPrimaryColor: .white,
        textSecondaryColor: Color(red: 0.8, green: 0.8, blue: 0.85),
        cornerRadius: 16,
        padding: 20,
        titleFont: .system(size: 28, weight: .black, design: .default),
        bodyFont: .system(size: 16, weight: .medium, design: .default),
        buttonFont: .system(size: 18, weight: .bold, design: .default)
    )
}

// MARK: - 环境集成
struct AppThemeKey: EnvironmentKey {
    static let defaultValue = AppTheme.light
}

extension EnvironmentValues {
    var appTheme: AppTheme {
        get { self[AppThemeKey.self] }
        set { self[AppThemeKey.self] = newValue }
    }
}

extension View {
    func appTheme(_ theme: AppTheme) -> some View {
        environment(\.appTheme, theme)
    }
}

// MARK: - 主题管理器
class ThemeManager: ObservableObject {
    enum ThemeType: String, CaseIterable, Identifiable {
        case system = "系统"
        case light = "浅色"
        case dark = "深色"
        case brand = "品牌"
        
        var id: String { self.rawValue }
    }
    
    @Published var themeType: ThemeType = .system
    
    // 计算当前颜色方案
    var colorScheme: ColorScheme? {
        switch themeType {
        case .system:
            return nil
        case .light:
            return .light
        case .dark:
            return .dark
        case .brand:
            return nil // 品牌主题跟随系统
        }
    }
    
    // 根据系统颜色方案解析当前主题
    func resolveTheme(in environment: ColorScheme) -> AppTheme {
        switch themeType {
        case .system:
            return environment == .dark ? .dark : .light
        case .light:
            return .light
        case .dark:
            return .dark
        case .brand:
            return environment == .dark ? .brandDark : .brandLight
        }
    }
}

// MARK: - 自定义组件
struct ThemedCard<Content: View>: View {
    @Environment(\.appTheme) private var theme
    let content: Content
    
    init(@ViewBuilder content: () -> Content) {
        self.content = content()
    }
    
    var body: some View {
        content
            .padding(theme.padding)
            .background(theme.cardColor)
            .cornerRadius(theme.cornerRadius)
    }
}

struct ThemedButton: View {
    @Environment(\.appTheme) private var theme
    let title: String
    let action: () -> Void
    let isPrimary: Bool
    
    init(_ title: String, isPrimary: Bool = true, action: @escaping () -> Void) {
        self.title = title
        self.isPrimary = isPrimary
        self.action = action
    }
    
    var body: some View {
        Button(action: action) {
            Text(title)
                .font(theme.buttonFont)
                .padding(.vertical, 12)
                .padding(.horizontal, 24)
                .frame(maxWidth: .infinity)
                .foregroundColor(isPrimary ? .white : theme.primaryColor)
                .background(isPrimary ? theme.primaryColor : Color.clear)
                .cornerRadius(theme.cornerRadius)
                .overlay(
                    RoundedRectangle(cornerRadius: theme.cornerRadius)
                        .stroke(isPrimary ? Color.clear : theme.primaryColor, lineWidth: 2)
                )
        }
    }
}

// MARK: - 主应用
@main
struct ThemeDemoApp: App {
    @StateObject private var themeManager = ThemeManager()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(themeManager)
                .preferredColorScheme(themeManager.colorScheme)
        }
    }
}

// MARK: - 主视图
struct ContentView: View {
    @EnvironmentObject private var themeManager: ThemeManager
    @Environment(\.colorScheme) private var colorScheme
    
    var body: some View {
        ZStack {
            // 应用当前主题
            themeManager.resolveTheme(in: colorScheme).backgroundColor
                .ignoresSafeArea()
            
            ScrollView {
                VStack(spacing: 20) {
                    ThemeSelector()
                    
                    ThemeInfoCard()
                    
                    ThemeDemo()
                }
                .padding()
            }
        }
        // 将解析后的主题应用到环境中
        .appTheme(themeManager.resolveTheme(in: colorScheme))
    }
}

// MARK: - 主题选择器
struct ThemeSelector: View {
    @EnvironmentObject private var themeManager: ThemeManager
    @Environment(\.appTheme) private var theme
    
    var body: some View {
        VStack(alignment: .leading) {
            Text("选择主题")
                .font(theme.titleFont)
                .foregroundColor(theme.textPrimaryColor)
            
            Picker("主题", selection: $themeManager.themeType) {
                ForEach(ThemeManager.ThemeType.allCases) { type in
                    Text(type.rawValue).tag(type)
                }
            }
            .pickerStyle(SegmentedPickerStyle())
        }
        .padding()
        .background(theme.cardColor)
        .cornerRadius(theme.cornerRadius)
    }
}

// MARK: - 主题信息卡片
struct ThemeInfoCard: View {
    @EnvironmentObject private var themeManager: ThemeManager
    @Environment(\.appTheme) private var theme
    @Environment(\.colorScheme) private var colorScheme
    
    var body: some View {
        ThemedCard {
            VStack(alignment: .leading, spacing: 12) {
                Text("当前主题信息")
                    .font(theme.titleFont)
                    .foregroundColor(theme.textPrimaryColor)
                
                Divider()
                
                InfoRow(label: "主题类型:", value: themeManager.themeType.rawValue)
                InfoRow(label: "系统外观:", value: colorScheme == .dark ? "深色" : "浅色")
                InfoRow(label: "主色:", value: colorDescription(theme.primaryColor))
                InfoRow(label: "圆角:", value: "\(Int(theme.cornerRadius))pt")
                InfoRow(label: "内边距:", value: "\(Int(theme.padding))pt")
            }
        }
    }
    
    private func colorDescription(_ color: Color) -> String {
        if themeManager.themeType == .brand {
            return "品牌紫色"
        } else if color == .blue {
            return "蓝色"
        } else {
            return "自定义"
        }
    }
}

// MARK: - 信息行
struct InfoRow: View {
    @Environment(\.appTheme) private var theme
    let label: String
    let value: String
    
    var body: some View {
        HStack {
            Text(label)
                .font(theme.bodyFont)
                .foregroundColor(theme.textSecondaryColor)
            Spacer()
            Text(value)
                .font(theme.bodyFont)
                .foregroundColor(theme.textPrimaryColor)
        }
    }
}

// MARK: - 主题演示
struct ThemeDemo: View {
    @State private var showModal = false
    @Environment(\.appTheme) private var theme
    
    var body: some View {
        VStack(spacing: 20) {
            ThemedCard {
                VStack(alignment: .leading, spacing: 12) {
                    Text("主题演示")
                        .font(theme.titleFont)
                        .foregroundColor(theme.textPrimaryColor)
                    
                    Text("这是一个使用当前主题的卡片组件，展示了如何利用SwiftUI的环境值系统实现自定义主题。")
                        .font(theme.bodyFont)
                        .foregroundColor(theme.textSecondaryColor)
                    
                    HStack(spacing: 10) {
                        ColorSample(color: theme.primaryColor)
                        ColorSample(color: theme.secondaryColor)
                        ColorSample(color: theme.textPrimaryColor)
                        ColorSample(color: theme.textSecondaryColor)
                    }
                    .padding(.top, 8)
                }
            }
            
            ThemedButton("主按钮示例") {
                showModal = true
            }
            
            ThemedButton("次按钮示例", isPrimary: false) {
                showModal = true
            }
        }
        .sheet(isPresented: $showModal) {
            ModalView()
        }
    }
}

// MARK: - 颜色样本
struct ColorSample: View {
    let color: Color
    
    var body: some View {
        color
            .frame(width: 40, height: 40)
            .cornerRadius(8)
    }
}

// MARK: - 模态视图
struct ModalView: View {
    @Environment(\.appTheme) private var theme
    @Environment(\.presentationMode) private var presentationMode
    
    var body: some View {
        ZStack {
            theme.backgroundColor.ignoresSafeArea()
            
            VStack(spacing: 24) {
                Image(systemName: "paintpalette")
                    .font(.system(size: 60))
                    .foregroundColor(theme.primaryColor)
                
                Text("模态视图")
                    .font(theme.titleFont)
                    .foregroundColor(theme.textPrimaryColor)
                
                Text("这个模态视图同样应用了当前主题设置。SwiftUI的环境值机制确保了整个应用的主题一致性。")
                    .font(theme.bodyFont)
                    .foregroundColor(theme.textSecondaryColor)
                    .multilineTextAlignment(.center)
                    .padding()
                
                Spacer()
                
                ThemedButton("关闭") {
                    presentationMode.wrappedValue.dismiss()
                }
                .padding()
            }
            .padding(.top, 40)
        }
    }
}
```

## 鸿蒙 ArkUI Theme

### 配置方式

鸿蒙 ArkUI 使用内置的主题定制能力，主要通过 ThemeConstants 和样式资源进行配置：

```typescript
// 全局主题配置（app.ets）
export default {
  onCreate() {
    // 设置全局资源别名，用于应用主题
    AppStorage.SetOrCreate('themeMode', 'light'); // 'light'或'dark'
  }
}

// 主题定义（resources/styles/theme.ets）
export const themeStyles = {
  // 亮色主题
  light: {
    colorPrimary: '#007DFF',
    colorSecondary: '#4D9D74',
    colorBackground: '#F5F5F5',
    colorOnBackground: '#252525',
    colorSurface: '#FFFFFF',
    colorOnSurface: '#1A1A1A',
    colorBorder: '#E1E1E1',
    radius: 8,
    fontWeightRegular: FontWeight.Regular,
    fontWeightMedium: FontWeight.Medium,
    fontWeightBold: FontWeight.Bold
  },
  // 深色主题
  dark: {
    colorPrimary: '#409EFF',
    colorSecondary: '#67C23A',
    colorBackground: '#121212',
    colorOnBackground: '#E1E1E1',
    colorSurface: '#242424',
    colorOnSurface: '#E5E5E5',
    colorBorder: '#333333',
    radius: 8,
    fontWeightRegular: FontWeight.Regular,
    fontWeightMedium: FontWeight.Medium,
    fontWeightBold: FontWeight.Bold
  }
}
```

### 全局/局部应用机制

**全局应用**：ArkUI 可以通过 `AppStorage` 和全局资源管理全局主题：

```typescript
// 在应用入口设置全局主题
@Entry
@Component
struct App {
  @StorageLink('themeMode') themeMode: string = 'light'
  
  build() {
    Column() {
      Navigation() {
        // 全局应用路由
        HomePageIndex()
      }
    }
    .backgroundColor(this.themeMode === 'light' 
      ? themeStyles.light.colorBackground 
      : themeStyles.dark.colorBackground)
  }
}
```

**局部应用**：可以在组件或页面级别覆盖主题设置：

```typescript
@Component
struct CustomCard {
  @StorageLink('themeMode') themeMode: string = 'light'
  private styles = themeMode === 'light' ? themeStyles.light : themeStyles.dark
  
  // 自定义覆盖样式
  private customStyles = {
    colorPrimary: '#FF6B00',
    borderRadius: 12
  }
  
  build() {
    Column() {
      Text('自定义卡片')
        .fontSize(16)
        .fontColor(this.styles.colorOnSurface)
    }
    .width('100%')
    .padding(16)
    .backgroundColor(this.styles.colorSurface)
    .borderRadius(this.customStyles.borderRadius) // 应用自定义圆角
    .border({
      color: this.customStyles.colorPrimary, // 应用自定义主色
      width: 1
    })
  }
}
```

### 主题继承机制

鸿蒙 ArkUI 可以通过扩展和合并对象来实现主题继承：

```typescript
// 基础主题
const baseTheme = {
  colorPrimary: '#007DFF',
  colorSecondary: '#4D9D74',
  fontWeightRegular: FontWeight.Regular,
  fontWeightBold: FontWeight.Bold
}

// 继承基础主题，创建扩展主题
const brandTheme = {
  ...baseTheme, // 继承基础主题
  colorPrimary: '#FF5500', // 覆盖主色
  colorTertiary: '#9C27B0', // 新增第三色
  cornerRadius: {
    small: 4,
    medium: 8,
    large: 16
  }
}

// 根据场景需要来动态合并主题
function getTheme(type: string, isDark: boolean) {
  const baseStyles = isDark ? darkTheme : lightTheme;
  
  switch(type) {
    case 'brand':
      return { ...baseStyles, ...brandTheme };
    case 'custom':
      return { ...baseStyles, ...customTheme };
    default:
      return baseStyles;
  }
}
```

### 动态切换支持

鸿蒙 ArkUI 通过 `AppStorage` 和 `@StorageLink` 装饰器实现动态主题切换：

```typescript
// 主题切换页面
@Entry
@Component
struct ThemeSettingsPage {
  @StorageLink('themeMode') themeMode: string = 'light'
  private currentTheme = () => this.themeMode === 'light' ? themeStyles.light : themeStyles.dark
  
  build() {
    Column() {
      // 标题
      Text('主题设置')
        .fontSize(20)
        .fontColor(this.currentTheme().colorOnBackground)
        .fontWeight(this.currentTheme().fontWeightBold)
        .padding({ top: 20, bottom: 16 })
      
      // 主题切换开关
      Row() {
        Text('深色模式')
          .fontSize(16)
          .fontColor(this.currentTheme().colorOnBackground)
        Blank()
        Toggle({ type: ToggleType.Switch, isOn: this.themeMode === 'dark' })
          .onChange((isOn) => {
            this.themeMode = isOn ? 'dark' : 'light'
            // 主题变化后所有使用 @StorageLink('themeMode') 的组件会自动更新
          })
      }
      .width('100%')
      .padding(16)
      .backgroundColor(this.currentTheme().colorSurface)
      .borderRadius(this.currentTheme().radius)
      .margin({ top: 16 })
    }
    .width('100%')
    .height('100%')
    .backgroundColor(this.currentTheme().colorBackground)
    .padding(16)
  }
}
```

鸿蒙系统还原生支持主题跟随系统，可以通过系统API获取：

```typescript
import display from '@ohos.display';

// 获取系统当前深色模式状态
function getSystemDarkMode() {
  try {
    const displayManager = display.getDefaultDisplaySync();
    return displayManager.getDarkMode();
  } catch (error) {
    console.error(`获取系统深色模式失败: ${error}`);
    return false;
  }
}

// 监听系统深色模式变化
display.on('darkModeChange', (mode) => {
  AppStorage.Set('themeMode', mode ? 'dark' : 'light');
});
```

### 自定义主题扩展性

鸿蒙 ArkUI 允许通过自定义样式类和资源目录来扩展主题：

```typescript
// 1. 创建自定义主题类
export class CustomTheme {
  // 标准属性
  colorPrimary: string;
  colorBackground: string;
  radius: number;
  
  // 扩展属性
  gradientColors: Array<string>;
  customShadow: {
    color: string,
    radius: number,
    offsetX: number,
    offsetY: number
  };
  
  constructor(options) {
    this.colorPrimary = options.colorPrimary || '#007DFF';
    this.colorBackground = options.colorBackground || '#F5F5F5';
    this.radius = options.radius || 8;
    this.gradientColors = options.gradientColors || ['#007DFF', '#00C6FF'];
    this.customShadow = options.customShadow || {
      color: 'rgba(0, 0, 0, 0.1)',
      radius: 10,
      offsetX: 0,
      offsetY: 4
    };
  }
  
  // 自定义样式生成方法
  getGradientBg(direction = GradientDirection.Right) {
    return {
      linearGradient: {
        angle: direction,
        colors: this.gradientColors
      }
    };
  }
}

// 2. 创建主题实例
export const lightCustomTheme = new CustomTheme({
  colorPrimary: '#007DFF',
  colorBackground: '#F5F5F5',
  radius: 8,
  gradientColors: ['#007DFF', '#00C6FF'],
  customShadow: {
    color: 'rgba(0, 0, 0, 0.1)',
    radius: 10,
    offsetX: 0,
    offsetY: 4
  }
});

export const darkCustomTheme = new CustomTheme({
  colorPrimary: '#409EFF',
  colorBackground: '#121212',
  radius: 8,
  gradientColors: ['#409EFF', '#00E5FF'],
  customShadow: {
    color: 'rgba(0, 0, 0, 0.3)',
    radius: 10,
    offsetX: 0,
    offsetY: 4
  }
});

// 3. 应用扩展主题
@Component
struct GradientButton {
  @StorageLink('themeMode') themeMode: string = 'light'
  private text: string = '按钮'
  private onClick: () => void = () => {}
  
  get theme() {
    return this.themeMode === 'light' ? lightCustomTheme : darkCustomTheme;
  }
  
  build() {
    Button(this.text)
      .onClick(this.onClick)
      .width('100%')
      .height(48)
      .borderRadius(this.theme.radius)
      .backgroundColor(this.theme.getGradientBg())
      .shadow(
        this.theme.customShadow.color,
        this.theme.customShadow.offsetX,
        this.theme.customShadow.offsetY,
        this.theme.customShadow.radius
      )
  }
}
```

### 平台特性与兼容性

**平台特性**：
- 基于 ArkUI 的声明式开发范式
- 自适应不同设备和屏幕尺寸
- 支持系统深色模式跟随
- 支持多主题资源的适配
- 基于 AppStorage 的状态响应式管理

**兼容性**：
- 需要 HarmonyOS 3.0+
- OpenHarmony 3.2 及以上支持完整特性
- 不同分辨率、屏幕密度自适应支持
- 与 AOSP 生态需要分开适配

### 完整示例

下面是一个完整的鸿蒙 ArkUI 主题管理示例：

```typescript
// theme.ets - 主题定义文件
import display from '@ohos.display';

export interface ThemeStyle {
  colorPrimary: string;
  colorSecondary: string;
  colorBackground: string;
  colorOnBackground: string;
  colorSurface: string;
  colorOnSurface: string;
  colorBorder: string;
  colorError: string;
  radius: {
    small: number;
    medium: number;
    large: number;
  };
  fontSizes: {
    small: number;
    normal: number;
    large: number;
    xlarge: number;
  };
  spacing: {
    mini: number;
    small: number;
    medium: number;
    large: number;
  };
  shadow: {
    color: ResourceColor;
    radius: number;
    offsetX: number;
    offsetY: number;
  };
}

// 浅色主题
export const lightTheme: ThemeStyle = {
  colorPrimary: '#007DFF',
  colorSecondary: '#4D9D74',
  colorBackground: '#F5F5F5',
  colorOnBackground: '#252525',
  colorSurface: '#FFFFFF',
  colorOnSurface: '#1A1A1A',
  colorBorder: '#E1E1E1',
  colorError: '#F56C6C',
  radius: {
    small: 4,
    medium: 8,
    large: 16
  },
  fontSizes: {
    small: 14,
    normal: 16,
    large: 18,
    xlarge: 24
  },
  spacing: {
    mini: 4,
    small: 8,
    medium: 16,
    large: 24
  },
  shadow: {
    color: 'rgba(0, 0, 0, 0.1)',
    radius: 12,
    offsetX: 0,
    offsetY: 4
  }
};

// 深色主题
export const darkTheme: ThemeStyle = {
  colorPrimary: '#409EFF',
  colorSecondary: '#67C23A',
  colorBackground: '#121212',
  colorOnBackground: '#E1E1E1',
  colorSurface: '#242424',
  colorOnSurface: '#E5E5E5',
  colorBorder: '#333333',
  colorError: '#F56C6C',
  radius: {
    small: 4,
    medium: 8,
    large: 16
  },
  fontSizes: {
    small: 14,
    normal: 16,
    large: 18,
    xlarge: 24
  },
  spacing: {
    mini: 4,
    small: 8,
    medium: 16,
    large: 24
  },
  shadow: {
    color: 'rgba(0, 0, 0, 0.3)',
    radius: 12,
    offsetX: 0,
    offsetY: 4
  }
};

// 品牌主题（浅色）
export const brandLightTheme: ThemeStyle = {
  ...lightTheme,
  colorPrimary: '#FF5500',
  colorSecondary: '#FF9500',
  radius: {
    small: 8,
    medium: 16,
    large: 24
  }
};

// 品牌主题（深色）
export const brandDarkTheme: ThemeStyle = {
  ...darkTheme,
  colorPrimary: '#FF7433',
  colorSecondary: '#FFB366',
  radius: {
    small: 8,
    medium: 16,
    large: 24
  }
};

// 主题管理类
export class ThemeManager {
  private static instance: ThemeManager;
  
  // 主题类型枚举
  public static ThemeMode = {
    LIGHT: 'light',
    DARK: 'dark',
    SYSTEM: 'system',
    BRAND: 'brand'
  };
  
  private constructor() {
    // 初始化时获取系统主题
    const isDarkMode = getSystemDarkMode();
    AppStorage.SetOrCreate('systemDarkMode', isDarkMode);
    AppStorage.SetOrCreate('themeMode', ThemeManager.ThemeMode.SYSTEM);
    
    // 监听系统深色模式变化
    display.on('darkModeChange', (mode) => {
      AppStorage.Set('systemDarkMode', mode);
    });
  }
  
  public static getInstance(): ThemeManager {
    if (!ThemeManager.instance) {
      ThemeManager.instance = new ThemeManager();
    }
    return ThemeManager.instance;
  }
  
  // 设置主题模式
  public setThemeMode(mode: string): void {
    AppStorage.Set('themeMode', mode);
  }
  
  // 获取当前主题
  public getTheme(): ThemeStyle {
    const themeMode = AppStorage.Get<string>('themeMode');
    const systemDarkMode = AppStorage.Get<boolean>('systemDarkMode');
    
    switch (themeMode) {
      case ThemeManager.ThemeMode.LIGHT:
        return lightTheme;
      case ThemeManager.ThemeMode.DARK:
        return darkTheme;
      case ThemeManager.ThemeMode.BRAND:
        return systemDarkMode ? brandDarkTheme : brandLightTheme;
      case ThemeManager.ThemeMode.SYSTEM:
      default:
        return systemDarkMode ? darkTheme : lightTheme;
    }
  }
}

// 获取系统当前深色模式状态
function getSystemDarkMode(): boolean {
  try {
    const displayManager = display.getDefaultDisplaySync();
    return displayManager.getDarkMode();
  } catch (error) {
    console.error(`获取系统深色模式失败: ${error}`);
    return false;
  }
}

// 应用入口
@Entry
@Component
struct ThemeApp {
  @StorageLink('themeMode') themeMode: string = 'system';
  @StorageLink('systemDarkMode') systemDarkMode: boolean = false;
  
  aboutToAppear() {
    // 初始化主题管理器
    ThemeManager.getInstance();
  }
  
  get currentTheme(): ThemeStyle {
    return ThemeManager.getInstance().getTheme();
  }
  
  build() {
    Navigation() {
      ThemeDemo()
    }
    .backgroundColor(this.currentTheme.colorBackground)
    .titleMode(NavigationTitleMode.Mini)
  }
}

// 主题演示页面
@Component
struct ThemeDemo {
  @StorageLink('themeMode') themeMode: string = 'system';
  @StorageLink('systemDarkMode') systemDarkMode: boolean = false;
  
  get currentTheme(): ThemeStyle {
    return ThemeManager.getInstance().getTheme();
  }
  
  build() {
    Column() {
      // 应用当前主题
      Theme.applyTheme(this.currentTheme)
      
      // 主题切换选项
      Row() {
        Button({
          type: ButtonType.Normal,
          text: '跟随系统主题',
          onClick: () => {
            ThemeManager.getInstance().setThemeMode(ThemeManager.ThemeMode.SYSTEM)
          }
        })
        .width('100%')
        .height(48)
        .backgroundColor(this.currentTheme.colorBackground)
        .borderRadius(8)
        .border({
          color: this.currentTheme.colorBorder,
          width: 1
        })
        .font({
          size: 16,
          weight: FontWeight.Medium
        })
        .textColor(this.currentTheme.colorOnBackground)
        
        Switch({
          type: SwitchType.Normal,
          checked: this.systemDarkMode,
          onChange: (value) => {
            this.systemDarkMode = value
            ThemeManager.getInstance().setThemeMode(this.systemDarkMode ? ThemeManager.ThemeMode.DARK : ThemeManager.ThemeMode.LIGHT)
          }
        })
        .width(120)
        .height(48)
        .padding({ left: 16, right: 16 })
        .justifyContent(FlexAlign.SpaceBetween)
        .backgroundColor(this.currentTheme.colorBackground)
        .borderRadius(8)
        .border({
          color: this.currentTheme.colorBorder,
          width: 1
        })
        .font({
          size: 16,
          weight: FontWeight.Medium
        })
        .textColor(this.currentTheme.colorOnBackground)
      }
      
      // 标准Material组件
      Button({
        type: ButtonType.Normal,
        text: '标准按钮',
        onClick: () => {
          // 这里可以添加标准按钮的点击事件处理逻辑
        }
      })
      .width('100%')
      .height(48)
      .backgroundColor(this.currentTheme.colorPrimary)
      .borderRadius(8)
      .border({
        color: this.currentTheme.colorBorder,
        width: 1
      })
      .font({
        size: 16,
        weight: FontWeight.Medium
      })
      .textColor(this.currentTheme.colorOnPrimary)
      
      // 使用自定义主题的组件
      CustomThemedCard {
        Text('这是使用自定义主题的卡片')
          .font(this.currentTheme.bodyFont)
          .textColor(this.currentTheme.colorOnSurface)
      }
      
      // 局部主题覆盖
      MaterialTheme(
        colorScheme: this.currentTheme,
        shapes: Shapes({
          medium: {
            borderRadius: {
              medium: 8
            }
          }
        })
      ) {
        Button({
          type: ButtonType.Normal,
          text: '局部主题按钮',
          onClick: () => {
            // 这里可以添加局部主题按钮的点击事件处理逻辑
          }
        })
        .width('100%')
        .height(48)
        .backgroundColor(this.currentTheme.colorPrimary)
        .borderRadius(8)
        .border({
          color: this.currentTheme.colorBorder,
          width: 1
        })
        .font({
          size: 16,
          weight: FontWeight.Medium
        })
        .textColor(this.currentTheme.colorOnPrimary)
      }
    }
    .padding(16)
    .backgroundColor(this.currentTheme.colorBackground)
    .borderRadius(8)
    .border({
      color: this.currentTheme.colorBorder,
      width: 1
    })
  }
}

// 自定义主题组件
@Component
struct CustomThemedCard {
  @Prop theme: ThemeStyle
  
  build() {
    Column() {
      // 这里可以添加自定义主题组件的内容
    }
    .padding(this.theme.spacing.medium)
    .backgroundColor(this.theme.colorBackground)
    .borderRadius(this.theme.radius.medium)
    .border({
      color: this.theme.colorBorder,
      width: 1
    })
  }
}
```

## Vue Theme

### 配置方式

Vue 3 没有内置的主题管理系统，但可以通过多种方式实现主题配置：

1. **CSS 变量方式**：使用 CSS 自定义属性（变量）定义主题颜色和样式

```css
/* 在:root选择器中定义全局CSS变量 */
:root {
  --primary-color: #42b883;
  --secondary-color: #35495e;
  --text-color: #2c3e50;
  --background-color: #ffffff;
  --font-size-base: 16px;
  --border-radius: 4px;
}

/* 暗色主题变量 */
.dark-theme {
  --primary-color: #42d392;
  --secondary-color: #4a687c;
  --text-color: #ffffff;
  --background-color: #1a1a1a;
}
```

2. **组合式API方式**：使用`provide/inject`机制提供主题配置

```js
// theme.js
import { reactive } from 'vue'

export const defaultTheme = reactive({
  primary: '#42b883',
  secondary: '#35495e',
  textColor: '#2c3e50',
  fontSize: {
    small: '14px',
    normal: '16px',
    large: '18px'
  }
})
```

### 全局/局部应用机制

**全局应用**：

1. **CSS变量方式**：在根组件或全局CSS中定义

```js
// main.js
import { createApp } from 'vue'
import App from './App.vue'
import './assets/theme.css'

createApp(App).mount('#app')
```

2. **使用Provide/Inject**：在根组件中提供主题

```js
// App.vue
import { provide } from 'vue'
import { defaultTheme } from './theme'

export default {
  setup() {
    provide('theme', defaultTheme)
  }
}
```

**局部应用**：

可以在组件级别覆盖主题设置：

```vue
<template>
  <div class="local-theme">
    <slot></slot>
  </div>
</template>

<style scoped>
.local-theme {
  --primary-color: #ff7043;
  --secondary-color: #607d8b;
}
</style>
```

也可以使用组合式API提供局部主题：

```js
// 局部组件中
import { provide, reactive } from 'vue'
import { defaultTheme } from './theme'

export default {
  setup() {
    const localTheme = reactive({
      ...defaultTheme,
      primary: '#ff7043'
    })
    
    provide('theme', localTheme)
  }
}
```

### 主题继承机制

Vue 的主题继承主要通过 CSS 的层叠机制和 provide/inject 模式实现：

1. **CSS变量继承**：子元素自动继承父元素的CSS变量

```css
.parent-theme {
  --accent-color: blue;
}

/* 子元素会继承--accent-color，除非重新定义 */
.child-override {
  --accent-color: purple;
}
```

2. **Provide/Inject继承**：可以获取并扩展父级提供的主题

```js
// 子组件中
import { inject, reactive } from 'vue'

export default {
  setup() {
    const parentTheme = inject('theme')
    
    // 创建继承自父主题的本地主题
    const localTheme = reactive({
      ...parentTheme,
      // 仅覆盖需要变更的属性
      secondary: '#9c27b0'
    })
    
    // 可选择是否重新提供修改后的主题
    provide('theme', localTheme)
    
    return { localTheme }
  }
}
```

### 动态切换支持

Vue 支持通过多种方式实现动态主题切换：

1. **CSS类切换方式**：

```vue
<template>
  <div :class="{ 'dark-theme': isDarkTheme }">
    <button @click="toggleTheme">切换主题</button>
    <slot></slot>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  setup() {
    const isDarkTheme = ref(false)
    
    const toggleTheme = () => {
      isDarkTheme.value = !isDarkTheme.value
      // 可选：保存到localStorage
      localStorage.setItem('theme', isDarkTheme.value ? 'dark' : 'light')
    }
    
    // 初始化主题（从localStorage恢复）
    isDarkTheme.value = localStorage.getItem('theme') === 'dark'
    
    return { isDarkTheme, toggleTheme }
  }
}
</script>
```

2. **响应式主题对象**：

```vue
<template>
  <button @click="switchTheme">切换主题</button>
</template>

<script>
import { provide, reactive, watchEffect } from 'vue'
import { lightTheme, darkTheme } from './themes'

export default {
  setup() {
    const currentTheme = reactive({...lightTheme})
    const isDark = ref(false)
    
    const switchTheme = () => {
      isDark.value = !isDark.value
    }
    
    // 监听isDark变化，更新主题
    watchEffect(() => {
      const newTheme = isDark.value ? darkTheme : lightTheme
      Object.keys(newTheme).forEach(key => {
        currentTheme[key] = newTheme[key]
      })
      
      // 可选：同步更新CSS变量
      document.documentElement.style.setProperty('--primary-color', currentTheme.primary)
      document.documentElement.style.setProperty('--text-color', currentTheme.textColor)
      // ...更新其他变量
    })
    
    provide('theme', currentTheme)
    
    return { switchTheme }
  }
}
</script>
```

### 自定义主题扩展性

Vue 的自定义主题扩展性非常灵活：

1. **主题插件系统**：

```js
// 创建主题插件
export function createThemePlugin(options = {}) {
  const defaultThemes = {
    light: {
      primary: '#42b883',
      // ...其他默认值
    },
    dark: {
      primary: '#42d392',
      // ...其他默认值
    },
    // 可以增加更多预设主题
  }
  
  const themes = reactive({
    ...defaultThemes,
    ...options.themes
  })
  
  const currentThemeName = ref(options.defaultTheme || 'light')
  const currentTheme = computed(() => themes[currentThemeName.value])
  
  function setTheme(themeName) {
    if (themes[themeName]) {
      currentThemeName.value = themeName
    }
  }
  
  function defineTheme(name, themeObject) {
    themes[name] = {
      ...(themes[name] || {}),
      ...themeObject
    }
  }
  
  return {
    install(app) {
      app.provide('themeManager', {
        currentTheme,
        currentThemeName,
        setTheme,
        defineTheme,
        themes
      })
      
      // 全局mixin，使所有组件可以通过this.$theme访问
      app.config.globalProperties.$theme = currentTheme
    }
  }
}
```

2. **组件库集成**：可以与UI组件库（如Element Plus、Vuetify）的主题系统结合使用

```js
// 与Element Plus集成
import ElementPlus from 'element-plus'
import 'element-plus/theme-chalk/index.css'
import { defineTheme } from './themePlugin'

// 自定义Element Plus变量
defineTheme('customDark', {
  'el-color-primary': '#409eff',
  'el-color-success': '#67c23a',
  // ...更多变量
})
```

### 平台特性与兼容性

**平台特性**：
- Vue是前端框架，主题系统主要依赖于CSS和JavaScript能力
- 支持响应式设计，适用于各种屏幕尺寸
- 可结合媒体查询实现系统主题跟随
- 支持各种UI组件库的主题集成（Element Plus、Vuetify、Quasar等）

**兼容性**：
- CSS变量支持IE11以上的浏览器
- 可通过PostCSS插件实现对旧浏览器的兼容
- 现代浏览器都有良好的主题切换性能表现
- SSR（服务端渲染）需要特殊处理主题初始化，避免闪烁

### 完整示例

下面是一个完整的Vue 3主题管理系统实现：

```js
// theme.js - 主题管理模块
import { ref, reactive, computed, watchEffect } from 'vue'

// 主题定义
const themes = {
  light: {
    primary: '#42b883',
    secondary: '#35495e',
    success: '#67c23a',
    warning: '#e6a23c',
    danger: '#f56c6c',
    info: '#909399',
    textPrimary: '#2c3e50',
    textSecondary: '#606266',
    border: '#dcdfe6',
    background: '#ffffff',
    cardBackground: '#f5f7fa',
  },
  dark: {
    primary: '#42d392',
    secondary: '#4a687c',
    success: '#85ce61',
    warning: '#f0b942',
    danger: '#ff7875',
    info: '#a6a9ad',
    textPrimary: '#ffffff',
    textSecondary: '#a6a9ad',
    border: '#4c4d4f',
    background: '#1a1a1a',
    cardBackground: '#2a2a2a',
  },
  // 自定义主题
  ocean: {
    primary: '#1976d2',
    secondary: '#0288d1',
    success: '#00796b',
    warning: '#ff8f00',
    danger: '#d32f2f',
    info: '#546e7a',
    textPrimary: '#263238',
    textSecondary: '#607d8b',
    border: '#b0bec5',
    background: '#eceff1',
    cardBackground: '#ffffff',
  }
}

// 创建主题管理器
export function createThemeManager() {
  // 当前主题名称
  const currentThemeName = ref(
    // 尝试从localStorage恢复
    localStorage.getItem('theme') || 
    // 或者使用系统首选颜色方案
    (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light')
  )
  
  // 计算当前主题对象
  const currentTheme = computed(() => themes[currentThemeName.value])
  
  // 设置主题的方法
  function setTheme(themeName) {
    if (themes[themeName]) {
      currentThemeName.value = themeName
      localStorage.setItem('theme', themeName)
    }
  }
  
  // 添加自定义主题
  function addTheme(name, themeConfig) {
    themes[name] = {
      ...(themes[name] || themes.light), // 继承默认主题
      ...themeConfig // 覆盖自定义属性
    }
  }
  
  // 监听系统主题变化
  const prefersDark = window.matchMedia('(prefers-color-scheme: dark)')
  prefersDark.addEventListener('change', (e) => {
    // 仅当设置为跟随系统时才自动切换
    if (localStorage.getItem('themeMode') === 'system') {
      setTheme(e.matches ? 'dark' : 'light')
    }
  })
  
  // 将主题应用到CSS变量
  watchEffect(() => {
    const theme = currentTheme.value
    Object.entries(theme).forEach(([key, value]) => {
      document.documentElement.style.setProperty(`--${key}`, value)
    })
    
    // 添加当前主题类名到body
    document.body.className = currentThemeName.value
  })
  
  return {
    currentTheme,
    currentThemeName,
    setTheme,
    addTheme,
    availableThemes: computed(() => Object.keys(themes))
  }
}
```

使用主题管理器：

```vue
<!-- App.vue -->
<template>
  <div class="app">
    <header>
      <h1>Vue主题演示</h1>
      <div class="theme-selector">
        <span>选择主题:</span>
        <select v-model="themeName">
          <option v-for="name in themeManager.availableThemes" :key="name" :value="name">
            {{ name.charAt(0).toUpperCase() + name.slice(1) }}
          </option>
        </select>
      </div>
    </header>
    
    <main>
      <div class="card">
        <h2>主题示例</h2>
        <p>当前主题: {{ themeName }}</p>
        <div class="color-samples">
          <div v-for="color in colorKeys" :key="color" class="color-sample">
            <div class="color-box" :style="{ backgroundColor: theme[color] }"></div>
            <span>{{ color }}: {{ theme[color] }}</span>
          </div>
        </div>
        <div class="buttons">
          <button class="btn primary">主要按钮</button>
          <button class="btn secondary">次要按钮</button>
          <button class="btn success">成功按钮</button>
          <button class="btn danger">危险按钮</button>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
import { provide, computed, toRefs } from 'vue'
import { createThemeManager } from './theme'

export default {
  setup() {
    const themeManager = createThemeManager()
    const { currentTheme, currentThemeName } = toRefs(themeManager)
    
    // 提供主题给所有子组件
    provide('theme', currentTheme)
    
    const colorKeys = computed(() => 
      Object.keys(currentTheme.value).filter(key => key.indexOf('text') === -1)
    )
    
    return { 
      themeManager,
      theme: currentTheme,
      themeName: currentThemeName,
      colorKeys,
      setTheme: themeManager.setTheme
    }
  }
}
</script>

<style>
/* 使用CSS变量应用主题 */
.app {
  color: var(--textPrimary);
  background-color: var(--background);
  min-height: 100vh;
  padding: 2rem;
}

.card {
  background-color: var(--cardBackground);
  border: 1px solid var(--border);
  border-radius: 8px;
  padding: 1.5rem;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.1);
}

.btn {
  padding: 0.5rem 1rem;
  border-radius: 4px;
  border: none;
  margin-right: 0.5rem;
  cursor: pointer;
}

.btn.primary {
  background-color: var(--primary);
  color: white;
}

.btn.secondary {
  background-color: var(--secondary);
  color: white;
}

.btn.success {
  background-color: var(--success);
  color: white;
}

.btn.danger {
  background-color: var(--danger);
  color: white;
}

.color-samples {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 1rem;
  margin: 1.5rem 0;
}

.color-sample {
  display: flex;
  align-items: center;
}

.color-box {
  width: 24px;
  height: 24px;
  border-radius: 4px;
  margin-right: 0.5rem;
  border: 1px solid var(--border);
}
</style>
```

## 跨平台对比总结

### 配置方式对比

| 框架 | 配置方式 | 便捷性 | 灵活性 |
|------|---------|-------|--------|
| Flutter | ThemeData类 | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| Jetpack Compose | MaterialTheme/主题对象 | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| UIKit | UIAppearance代理/Asset Catalog | ⭐⭐ | ⭐⭐⭐ |
| SwiftUI | 环境变量/Asset Catalog | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| ArkUI | 资源文件/AppStorage/构建器 | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| Vue | CSS变量/响应式对象 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

Flutter和Jetpack Compose提供了更加结构化的主题配置系统，而Vue的CSS变量方式提供了最大的灵活性。UIKit的配置方式相对繁琐，需要通过多个API调用进行设置。

### 全局/局部应用机制对比

| 框架 | 全局机制 | 局部覆盖 | 作用域隔离 |
|------|---------|--------|-----------|
| Flutter | MaterialApp/Theme | Theme组件 | ⭐⭐⭐⭐ |
| Jetpack Compose | MaterialTheme/主题对象 | CompositionLocal | ⭐⭐⭐⭐⭐ |
| UIKit | UIAppearance/全局设置 | 局部重写 | ⭐⭐ |
| SwiftUI | 环境值 | 环境修饰符 | ⭐⭐⭐⭐ |
| ArkUI | 全局资源/AppStorage | 局部样式/自定义组件 | ⭐⭐⭐ |
| Vue | CSS变量/Provide | 局部CSS/局部Provide | ⭐⭐⭐⭐⭐ |

Jetpack Compose的CompositionLocal和Vue的Provide/Inject机制提供了最好的作用域隔离，允许组件树中的任何节点重新提供主题而不影响其他部分。UIKit的局部覆盖需要更多的代码，且缺乏清晰的作用域边界。

### 主题继承机制对比

| 框架 | 继承方式 | 合并能力 | 覆盖灵活性 |
|------|---------|---------|-----------|
| Flutter | copyWith() | ⭐⭐⭐ | ⭐⭐⭐ |
| Jetpack Compose | 扩展函数/复制 | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| UIKit | 自定义扩展/重写 | ⭐⭐ | ⭐⭐ |
| SwiftUI | 继承环境值 | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| ArkUI | 对象展开/继承 | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| Vue | 对象展开/CSS继承 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

Vue通过JavaScript对象和CSS继承提供了最灵活的主题继承机制，而Jetpack Compose和SwiftUI的继承机制更加类型安全和结构化。UIKit则需要更多的自定义代码来实现继承。

### 动态切换支持对比

| 框架 | 实时切换 | 动画过渡 | 系统主题跟随 |
|------|---------|---------|-------------|
| Flutter | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Jetpack Compose | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| UIKit | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ |
| SwiftUI | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| ArkUI | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| Vue | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |

Jetpack Compose提供了最佳的动态切换体验，包括流畅的动画过渡和完整的系统主题跟随。Flutter和SwiftUI表现也很出色。Vue的动态切换需要额外的代码来处理动画过渡，而UIKit的主题切换相对较慢且需要更多的手动管理。

### 自定义主题扩展性对比

| 框架 | 扩展机制 | 复用性 | 类型安全 |
|------|---------|-------|---------|
| Flutter | ThemeExtension | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Jetpack Compose | 自定义CompositionLocal | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| UIKit | 自定义扩展/管理类 | ⭐⭐ | ⭐⭐⭐ |
| SwiftUI | 自定义EnvironmentValues | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| ArkUI | 自定义样式/类 | ⭐⭐⭐ | ⭐⭐⭐ |
| Vue | 插件系统/自定义对象 | ⭐⭐⭐⭐⭐ | ⭐⭐ |

Jetpack Compose和SwiftUI提供了最类型安全的扩展机制，而Vue的扩展性最灵活但类型安全性较低。Flutter的ThemeExtension提供了良好的平衡，UIKit则需要更多的手动代码来实现扩展。

### 生态系统与工具支持

| 框架 | 设计工具集成 | 社区组件兼容 | 开发者体验 |
|------|------------|-------------|-----------|
| Flutter | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Jetpack Compose | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| UIKit | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐ |
| SwiftUI | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| ArkUI | ⭐⭐ | ⭐⭐ | ⭐⭐⭐ |
| Vue | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

Vue在设计工具集成和社区组件兼容方面表现最佳，拥有丰富的UI库和设计系统支持。Jetpack Compose提供了优秀的开发者体验。ArkUI作为新兴框架，其生态系统相对有限，但正在快速发展。

### 性能考虑

| 框架 | 渲染效率 | 主题切换性能 | 内存占用 |
|------|---------|------------|---------|
| Flutter | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| Jetpack Compose | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| UIKit | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐ |
| SwiftUI | ⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ |
| ArkUI | ⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| Vue | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |

UIKit在原生渲染性能方面表现最佳，但主题切换时可能需要重新加载视图。Jetpack Compose在主题切换性能方面领先，能够实现流畅的动画过渡。Vue在轻量级应用中性能良好，但在大型应用中需要更多优化。

### 总体评价

每个框架的主题管理系统都有其独特的优势和限制：

- **Flutter**：提供了全面而强大的主题系统，适合跨平台应用，对Material Design支持最佳
- **Jetpack Compose**：拥有最现代化和类型安全的主题机制，结合Kotlin的语言特性提供优秀的开发体验
- **UIKit**：成熟稳定但配置繁琐，适合需要精细控制的iOS原生应用
- **SwiftUI**：环境变量机制优雅简洁，但生态系统相对较新
- **ArkUI**：为鸿蒙系统优化，提供独特的分布式能力，但生态系统尚在发展中
- **Vue**：最灵活且易于定制，适合Web应用，与各种CSS工具和UI库集成良好

对于跨平台应用，Flutter提供了最一致的主题体验。对于特定平台优化，原生框架（Jetpack Compose、SwiftUI、UIKit、ArkUI）通常提供更好的性能和平台集成。Vue则在Web开发中提供了最灵活和最易于定制的主题系统。

选择合适的框架应基于项目需求、团队专长以及目标平台考虑。对于需要高度定制主题的项目，Vue和Jetpack Compose是最佳选择；对于需要跨平台一致性的项目，Flutter是理想选择；对于需要最佳原生体验的项目，原生框架是更好的选择。