# 主流框架深浅色模式对比分析（2024）

## 目录
- [概述](#概述)
- [Flutter](#flutter)
- [Android Jetpack Compose](#android-jetpack-compose)
- [iOS UIKit](#ios-uikit)
- [iOS SwiftUI](#ios-swiftui)
- [鸿蒙 ArkUI](#鸿蒙-arkui)
- [Vue](#vue)
- [跨平台对比总结](#跨平台对比总结)

## 概述

本文档对比了六大主流框架的深浅色模式实现，基于 2024 年最新稳定版本：
- Flutter 3.19
- Jetpack Compose 1.6.0
- UIKit (iOS 17)
- SwiftUI (iOS 17)
- ArkUI (HarmonyOS 4.0)
- Vue 3.4

每个框架的分析将从以下五个维度进行：
1. 切换机制
2. 系统集成方式
3. 自动/手动切换支持
4. 组件适配方式
5. 资源适配机制

此外，还将提供相关平台特性、兼容性考虑以及完整的示例代码。

## Flutter

### 切换机制

Flutter 使用 `ThemeMode` 枚举来控制应用程序的亮暗模式，可在 `MaterialApp` 中设置：

```dart
MaterialApp(
  theme: ThemeData.light(), // 亮色主题
  darkTheme: ThemeData.dark(), // 暗色主题
  themeMode: ThemeMode.system, // 系统、亮色或暗色模式
  // ...
)
```

`ThemeMode` 有三个选项：
- `ThemeMode.light`：始终使用亮色主题
- `ThemeMode.dark`：始终使用暗色主题
- `ThemeMode.system`：跟随系统设置（默认值）

切换主题通常是通过修改状态变量实现的：

```dart
class _MyAppState extends State<MyApp> {
  ThemeMode _themeMode = ThemeMode.system;

  void toggleTheme(ThemeMode themeMode) {
    setState(() {
      _themeMode = themeMode;
    });
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData.light(),
      darkTheme: ThemeData.dark(),
      themeMode: _themeMode,
      // ...
    );
  }
}
```

### 系统集成方式

Flutter 通过平台通道（Platform Channels）获取系统的深浅色模式设置：

```dart
// 系统深浅色模式变化时自动同步
MediaQuery.platformBrightnessOf(context)
```

在 Flutter 2.5 之后，使用 `ThemeMode.system` 时会自动跟踪系统主题变化，无需额外代码。当系统切换深浅色模式时，Flutter应用会自动响应并切换相应的主题。

### 自动/手动切换支持

**自动切换**：
设置 `themeMode: ThemeMode.system` 即可跟随系统设置自动切换：

```dart
MaterialApp(
  theme: ThemeData.light(),
  darkTheme: ThemeData.dark(),
  themeMode: ThemeMode.system,
  // ...
)
```

**手动切换**：
可以使用自定义 UI 让用户手动选择主题模式：

```dart
// 主题选择界面
class ThemeSelectionScreen extends StatelessWidget {
  final ThemeMode currentThemeMode;
  final ValueChanged<ThemeMode> onThemeModeChanged;

  const ThemeSelectionScreen({
    Key? key,
    required this.currentThemeMode,
    required this.onThemeModeChanged,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('主题设置')),
      body: Column(
        children: [
          RadioListTile<ThemeMode>(
            title: Text('跟随系统'),
            value: ThemeMode.system,
            groupValue: currentThemeMode,
            onChanged: (value) => onThemeModeChanged(value!),
          ),
          RadioListTile<ThemeMode>(
            title: Text('亮色模式'),
            value: ThemeMode.light,
            groupValue: currentThemeMode,
            onChanged: (value) => onThemeModeChanged(value!),
          ),
          RadioListTile<ThemeMode>(
            title: Text('暗色模式'),
            value: ThemeMode.dark,
            groupValue: currentThemeMode,
            onChanged: (value) => onThemeModeChanged(value!),
          ),
        ],
      ),
    );
  }
}
```

### 组件适配方式

Flutter 中的 Material 组件会自动适应亮暗主题，无需手动调整。组件使用 `Theme.of(context)` 获取当前活动的主题：

```dart
Widget build(BuildContext context) {
  // 自动获取当前主题
  final theme = Theme.of(context);
  
  return Card(
    // Card 会自动适配当前主题
    child: Padding(
      padding: EdgeInsets.all(16.0),
      child: Text(
        '这是一段文本',
        style: theme.textTheme.bodyLarge,
      ),
    ),
  );
}
```

对于自定义组件，可以使用主题中的颜色或准备专用的亮暗色方案：

```dart
Widget build(BuildContext context) {
  final isDark = Theme.of(context).brightness == Brightness.dark;
  
  return Container(
    color: isDark ? Colors.grey[850] : Colors.white,
    child: Text(
      '自定义组件',
      style: TextStyle(
        color: isDark ? Colors.white : Colors.black,
      ),
    ),
  );
}
```

### 资源适配机制

对于颜色和样式，Flutter 通过亮暗主题对象进行适配：

```dart
// 定义亮暗主题使用的不同颜色
final lightTheme = ThemeData(
  primaryColor: Colors.blue,
  cardColor: Colors.white,
  canvasColor: Colors.grey[50],
  textTheme: TextTheme(
    bodyLarge: TextStyle(color: Colors.black87),
  ),
);

final darkTheme = ThemeData(
  primaryColor: Colors.blueAccent,
  cardColor: Colors.grey[850],
  canvasColor: Colors.grey[900],
  textTheme: TextTheme(
    bodyLarge: TextStyle(color: Colors.white),
  ),
);
```

对于图片资源，Flutter 可以通过代码判断来使用不同资源：

```dart
final bool isDark = Theme.of(context).brightness == Brightness.dark;
final String imagePath = isDark 
    ? 'assets/images/dark_logo.png' 
    : 'assets/images/light_logo.png';

Image.asset(imagePath)
```

从 Flutter 2.0 开始，也可以使用 `AssetImage` 的 `package` 参数结合资源变体来实现自动适配：

```dart
// pubspec.yaml 中定义资源
// assets:
//   - assets/images/logo.png
//   - assets/images/dark/logo.png

// 使用时无需区分亮暗模式，框架会自动选择对应资源
Image.asset('assets/images/logo.png')
```

### 平台特性与兼容性

**平台特性**：
- 支持 Material Design 和 Cupertino（iOS风格）的深浅色主题
- 深浅色切换无需重启应用，立即生效
- 支持屏幕亮度传感器跟随
- 支持以编程方式预览不同主题（DevTools）

**兼容性**：
- 所有 Flutter 支持的平台（Android、iOS、Web、桌面等）均可使用深浅色模式
- Android 10+ 和 iOS 13+ 原生支持系统级深浅色模式
- 在较旧的系统版本中，仍可手动切换深浅色模式，但无法跟随系统

### 完整示例

下面是一个完整的 Flutter 深浅色模式实现示例，包含系统跟随和手动切换功能：

```dart
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  ThemeMode _themeMode = ThemeMode.system;
  
  @override
  void initState() {
    super.initState();
    _loadThemeMode();
  }
  
  // 从持久化存储加载主题模式
  Future<void> _loadThemeMode() async {
    final prefs = await SharedPreferences.getInstance();
    final savedThemeMode = prefs.getString('themeMode');
    
    if (savedThemeMode != null) {
      setState(() {
        _themeMode = ThemeMode.values.firstWhere(
          (e) => e.toString() == savedThemeMode,
          orElse: () => ThemeMode.system,
        );
      });
    }
  }
  
  // 保存并更新主题模式
  Future<void> _setThemeMode(ThemeMode mode) async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.setString('themeMode', mode.toString());
    
    setState(() {
      _themeMode = mode;
    });
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter深浅色模式示例',
      theme: ThemeData(
        brightness: Brightness.light,
        primaryColor: Colors.blue,
        colorScheme: ColorScheme.light(
          primary: Colors.blue,
          secondary: Colors.blueAccent,
        ),
        appBarTheme: AppBarTheme(
          backgroundColor: Colors.blue,
          foregroundColor: Colors.white,
        ),
        cardTheme: CardTheme(
          color: Colors.white,
          elevation: 2,
        ),
        textTheme: TextTheme(
          titleLarge: TextStyle(fontSize: 20, fontWeight: FontWeight.bold, color: Colors.black87),
          bodyMedium: TextStyle(fontSize: 16, color: Colors.black87),
        ),
      ),
      darkTheme: ThemeData(
        brightness: Brightness.dark,
        primaryColor: Colors.blueAccent,
        colorScheme: ColorScheme.dark(
          primary: Colors.blueAccent,
          secondary: Colors.lightBlueAccent,
        ),
        appBarTheme: AppBarTheme(
          backgroundColor: Colors.grey[900],
          foregroundColor: Colors.white,
        ),
        cardTheme: CardTheme(
          color: Colors.grey[850],
          elevation: 2,
        ),
        textTheme: TextTheme(
          titleLarge: TextStyle(fontSize: 20, fontWeight: FontWeight.bold, color: Colors.white),
          bodyMedium: TextStyle(fontSize: 16, color: Colors.white70),
        ),
      ),
      themeMode: _themeMode,
      home: HomePage(
        currentThemeMode: _themeMode,
        onThemeModeChanged: _setThemeMode,
      ),
    );
  }
}

class HomePage extends StatelessWidget {
  final ThemeMode currentThemeMode;
  final ValueChanged<ThemeMode> onThemeModeChanged;

  const HomePage({
    Key? key,
    required this.currentThemeMode,
    required this.onThemeModeChanged,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    final theme = Theme.of(context);
    final isDarkMode = theme.brightness == Brightness.dark;
    
    return Scaffold(
      appBar: AppBar(
        title: Text('深浅色模式示例'),
        actions: [
          IconButton(
            icon: Icon(Icons.settings),
            onPressed: () {
              showModalBottomSheet(
                context: context,
                builder: (_) => ThemeSettingsSheet(
                  currentThemeMode: currentThemeMode,
                  onThemeModeChanged: onThemeModeChanged,
                ),
              );
            },
          ),
        ],
      ),
      body: Padding(
        padding: EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text(
              '当前主题模式',
              style: theme.textTheme.titleLarge,
            ),
            SizedBox(height: 8),
            Text(
              '${isDarkMode ? "暗色" : "亮色"}模式',
              style: theme.textTheme.bodyMedium,
            ),
            SizedBox(height: 24),
            
            Card(
              child: Padding(
                padding: EdgeInsets.all(16.0),
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    Text(
                      '卡片示例',
                      style: theme.textTheme.titleLarge,
                    ),
                    SizedBox(height: 8),
                    Text(
                      '这个卡片会根据当前主题自动调整颜色和样式。',
                      style: theme.textTheme.bodyMedium,
                    ),
                    SizedBox(height: 16),
                    // 使用不同的资源图片
                    Image.asset(
                      isDarkMode
                          ? 'assets/images/dark_image.png'
                          : 'assets/images/light_image.png',
                      height: 120,
                      width: double.infinity,
                      fit: BoxFit.contain,
                    ),
                    SizedBox(height: 16),
                    ElevatedButton(
                      onPressed: () {},
                      child: Text('操作按钮'),
                    ),
                  ],
                ),
              ),
            ),
            
            SizedBox(height: 24),
            // 自定义组件示例
            Container(
              padding: EdgeInsets.all(16),
              decoration: BoxDecoration(
                color: isDarkMode ? Colors.blueGrey[800] : Colors.blue[50],
                borderRadius: BorderRadius.circular(8),
                border: Border.all(
                  color: isDarkMode ? Colors.blueGrey[700]! : Colors.blue[200]!,
                ),
              ),
              child: Row(
                children: [
                  Icon(
                    Icons.info,
                    color: theme.colorScheme.primary,
                  ),
                  SizedBox(width: 16),
                  Expanded(
                    child: Text(
                      '这是一个自定义组件，根据深浅色模式调整样式',
                      style: theme.textTheme.bodyMedium,
                    ),
                  ),
                ],
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class ThemeSettingsSheet extends StatelessWidget {
  final ThemeMode currentThemeMode;
  final ValueChanged<ThemeMode> onThemeModeChanged;

  const ThemeSettingsSheet({
    Key? key,
    required this.currentThemeMode,
    required this.onThemeModeChanged,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.all(16),
      child: Column(
        mainAxisSize: MainAxisSize.min,
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          Text(
            '主题设置',
            style: Theme.of(context).textTheme.titleLarge,
          ),
          SizedBox(height: 16),
          RadioListTile<ThemeMode>(
            title: Text('跟随系统'),
            subtitle: Text('根据系统设置自动切换深浅色模式'),
            value: ThemeMode.system,
            groupValue: currentThemeMode,
            onChanged: (mode) => onThemeModeChanged(mode!),
          ),
          RadioListTile<ThemeMode>(
            title: Text('亮色模式'),
            subtitle: Text('始终使用亮色主题'),
            value: ThemeMode.light,
            groupValue: currentThemeMode,
            onChanged: (mode) => onThemeModeChanged(mode!),
          ),
          RadioListTile<ThemeMode>(
            title: Text('暗色模式'),
            subtitle: Text('始终使用暗色主题'),
            value: ThemeMode.dark,
            groupValue: currentThemeMode,
            onChanged: (mode) => onThemeModeChanged(mode!),
          ),
        ],
      ),
    );
  }
}

## Android Jetpack Compose

### 切换机制

Jetpack Compose 使用 `MaterialTheme` 组合函数来提供主题，通过 `darkTheme` 参数控制深浅色模式：

```kotlin
@Composable
fun MyApp() {
    val darkTheme = isSystemInDarkTheme() // 检测系统是否处于暗色模式
    
    MaterialTheme(
        colorScheme = if (darkTheme) {
            darkColorScheme(
                primary = Purple80,
                secondary = PurpleGrey80,
                tertiary = Pink80
            )
        } else {
            lightColorScheme(
                primary = Purple40,
                secondary = PurpleGrey40,
                tertiary = Pink40
            )
        },
        typography = Typography(),
        shapes = Shapes(),
        content = { /* 应用内容 */ }
    )
}
```

如果需要手动控制主题模式，可以使用状态管理：

```kotlin
@Composable
fun MyApp() {
    // 使用状态管理主题模式
    val themeMode = remember { mutableStateOf(ThemeMode.System) }
    
    // 根据主题模式决定是否使用暗色主题
    val darkTheme = when (themeMode.value) {
        ThemeMode.Dark -> true
        ThemeMode.Light -> false
        ThemeMode.System -> isSystemInDarkTheme()
    }
    
    MaterialTheme(
        colorScheme = if (darkTheme) {
            darkColorScheme(/* ... */)
        } else {
            lightColorScheme(/* ... */)
        },
        // ...
        content = {
            // 将主题模式状态传递给内容
            AppContent(
                themeMode = themeMode.value,
                onThemeModeChanged = { themeMode.value = it }
            )
        }
    )
}

// 定义主题模式枚举
enum class ThemeMode { Light, Dark, System }
```

### 系统集成方式

Jetpack Compose 通过 `isSystemInDarkTheme()` 组合函数获取系统的深浅色模式设置：

```kotlin
@Composable
fun MyApp() {
    val darkTheme = isSystemInDarkTheme() // 自动检测系统深浅色模式
    
    // 主题配置
    MaterialTheme(
        colorScheme = if (darkTheme) darkColorScheme() else lightColorScheme(),
        // ...
    )
}
```

此函数在 Android 10（API 29）及以上版本会跟踪系统的深浅色模式设置，当系统切换主题时，组合函数会自动重组以反映变化。在较低版本上，它会根据系统的当前设置返回一个固定值。

对于 API 29 以下的设备，可以使用 `AppCompatDelegate` 来跟踪系统设置变化：

```kotlin
import androidx.appcompat.app.AppCompatDelegate

// 在 Activity 或 Application 中：
AppCompatDelegate.setDefaultNightMode(AppCompatDelegate.MODE_NIGHT_FOLLOW_SYSTEM)

// 在 Compose 中检测当前模式
@Composable
fun isDarkTheme(): Boolean {
    return when (AppCompatDelegate.getDefaultNightMode()) {
        AppCompatDelegate.MODE_NIGHT_YES -> true
        AppCompatDelegate.MODE_NIGHT_NO -> false
        else -> isSystemInDarkTheme()
    }
}
```

### 自动/手动切换支持

**自动切换**：
使用 `isSystemInDarkTheme()` 可以实现自动跟随系统设置：

```kotlin
@Composable
fun MyApp() {
    val darkTheme = isSystemInDarkTheme()
    // 使用系统设置的深浅色模式
    MaterialTheme(
        colorScheme = if (darkTheme) darkColorScheme() else lightColorScheme(),
        // ...
    )
}
```

**手动切换**：
可以创建自定义 UI，让用户手动选择主题模式：

```kotlin
@Composable
fun ThemeSettings(
    currentThemeMode: ThemeMode,
    onThemeModeChanged: (ThemeMode) -> Unit
) {
    Column {
        Text(
            text = "主题设置",
            style = MaterialTheme.typography.headlineSmall,
            modifier = Modifier.padding(16.dp)
        )
        
        // 主题模式选项
        RadioButtonWithText(
            text = "跟随系统",
            selected = currentThemeMode == ThemeMode.System,
            onClick = { onThemeModeChanged(ThemeMode.System) }
        )
        
        RadioButtonWithText(
            text = "亮色模式",
            selected = currentThemeMode == ThemeMode.Light,
            onClick = { onThemeModeChanged(ThemeMode.Light) }
        )
        
        RadioButtonWithText(
            text = "暗色模式",
            selected = currentThemeMode == ThemeMode.Dark,
            onClick = { onThemeModeChanged(ThemeMode.Dark) }
        )
    }
}

@Composable
fun RadioButtonWithText(
    text: String,
    selected: Boolean,
    onClick: () -> Unit
) {
    Row(
        verticalAlignment = Alignment.CenterVertically,
        modifier = Modifier
            .fillMaxWidth()
            .clickable(onClick = onClick)
            .padding(horizontal = 16.dp, vertical = 8.dp)
    ) {
        RadioButton(
            selected = selected,
            onClick = onClick
        )
        Spacer(modifier = Modifier.width(8.dp))
        Text(text = text)
    }
}
```

切换后的设置可以使用 DataStore 或 SharedPreferences 持久化存储：

```kotlin
// 使用 DataStore 存储主题偏好
val Context.dataStore by preferencesDataStore(name = "settings")

// 定义 keys
val THEME_MODE_KEY = stringPreferencesKey("theme_mode")

// 存储主题设置
suspend fun Context.saveThemeMode(themeMode: ThemeMode) {
    dataStore.edit { preferences ->
        preferences[THEME_MODE_KEY] = themeMode.name
    }
}

// 读取主题设置
val Context.themeMode: Flow<ThemeMode> = dataStore.data
    .map { preferences ->
        val themeModeString = preferences[THEME_MODE_KEY] ?: ThemeMode.System.name
        try {
            ThemeMode.valueOf(themeModeString)
        } catch (e: Exception) {
            ThemeMode.System
        }
    }
```

### 组件适配方式

Compose 组件会自动响应 `MaterialTheme` 中的颜色方案变化，无需手动调整：

```kotlin
@Composable
fun MyComposeScreen() {
    val colors = MaterialTheme.colorScheme  // 自动获取当前颜色方案
    
    Card(
        colors = CardDefaults.cardColors(
            containerColor = colors.surface,  // 会根据深浅色模式自动变化
        ),
        modifier = Modifier.padding(16.dp)
    ) {
        Column(modifier = Modifier.padding(16.dp)) {
            Text(
                text = "示例卡片",
                style = MaterialTheme.typography.titleLarge,
                color = colors.onSurface  // 会根据深浅色模式自动变化
            )
            Spacer(modifier = Modifier.height(8.dp))
            Text(
                text = "这个文本将根据深浅色模式自动调整颜色",
                style = MaterialTheme.typography.bodyMedium,
                color = colors.onSurfaceVariant  // 会根据深浅色模式自动变化
            )
        }
    }
}
```

对于自定义组件，可以使用当前主题的颜色方案来适配：

```kotlin
@Composable
fun CustomInfoBox(
    icon: @Composable () -> Unit,
    title: String,
    description: String
) {
    val colors = MaterialTheme.colorScheme
    
    Surface(
        color = colors.primaryContainer,
        contentColor = colors.onPrimaryContainer,
        shape = RoundedCornerShape(8.dp),
        modifier = Modifier.padding(8.dp)
    ) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            Box(
                modifier = Modifier
                    .size(40.dp)
                    .background(
                        color = colors.primary,
                        shape = CircleShape
                    ),
                contentAlignment = Alignment.Center
            ) {
                CompositionLocalProvider(
                    LocalContentColor provides colors.onPrimary
                ) {
                    icon()
                }
            }
            
            Spacer(modifier = Modifier.width(16.dp))
            
            Column {
                Text(
                    text = title,
                    style = MaterialTheme.typography.titleMedium
                )
                Spacer(modifier = Modifier.height(4.dp))
                Text(
                    text = description,
                    style = MaterialTheme.typography.bodyMedium
                )
            }
        }
    }
}
```

### 资源适配机制

**颜色和样式**：
颜色方案通过 `darkColorScheme()` 和 `lightColorScheme()` 函数定义：

```kotlin
// 定义亮色和暗色颜色方案
val LightColors = lightColorScheme(
    primary = Color(0xFF6200EE),
    secondary = Color(0xFF03DAC6),
    background = Color(0xFFF1F1F1),
    surface = Color(0xFFFFFFFF),
    onPrimary = Color(0xFFFFFFFF),
    onSecondary = Color(0xFF000000),
    onBackground = Color(0xFF000000),
    onSurface = Color(0xFF000000),
)

val DarkColors = darkColorScheme(
    primary = Color(0xFFBB86FC),
    secondary = Color(0xFF03DAC6),
    background = Color(0xFF121212),
    surface = Color(0xFF121212),
    onPrimary = Color(0xFF000000),
    onSecondary = Color(0xFF000000),
    onBackground = Color(0xFFFFFFFF),
    onSurface = Color(0xFFFFFFFF),
)

// 使用颜色方案
@Composable
fun MyApp() {
    val darkTheme = isSystemInDarkTheme()
    val colors = if (darkTheme) DarkColors else LightColors
    
    MaterialTheme(
        colorScheme = colors,
        // ...
    )
}
```

**图片资源**：
Android 支持不同配置的资源目录，可以为深浅色模式准备不同的资源：

```
res/
  drawable/
    icon.xml       # 默认图标
  drawable-night/
    icon.xml       # 暗色模式图标
```

Compose 中通过 `painterResource` 使用资源时，会自动根据当前主题选择合适的资源：

```kotlin
@Composable
fun Logo() {
    // 会自动选择 drawable 或 drawable-night 目录中的资源
    Image(
        painter = painterResource(id = R.drawable.logo),
        contentDescription = "Logo",
        modifier = Modifier.size(100.dp)
    )
}
```

另外，也可以使用代码动态选择资源：

```kotlin
@Composable
fun DynamicImage() {
    val darkTheme = isSystemInDarkTheme()
    val imageRes = if (darkTheme) {
        R.drawable.dark_image
    } else {
        R.drawable.light_image
    }
    
    Image(
        painter = painterResource(id = imageRes),
        contentDescription = null
    )
}
```

### 平台特性与兼容性

**平台特性**：
- 完全支持 Material 3 动态调色板
- 支持动态颜色（在 Android 12+ 上基于壁纸生成主题）
- 深浅色切换无需重启 Activity，即时生效
- 组件在切换时支持过渡动画
- 支持暗色模式下的特殊互动效果

**兼容性**：
- 自动深浅色模式切换需要 Android 10（API 29）或更高版本
- 动态颜色需要 Android 12（API 31）或更高版本
- 在较低版本的 Android 上，可以使用 AppCompat 兼容库支持手动切换
- 资源变体（-night 限定符）在所有 Android 版本上均可工作

### 完整示例

以下是一个完整的 Jetpack Compose 深浅色模式实现示例，包含系统跟随和手动切换功能：

```kotlin
import android.content.Context
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.shape.CircleShape
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.DarkMode
import androidx.compose.material.icons.filled.LightMode
import androidx.compose.material.icons.filled.Settings
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.draw.clip
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.res.painterResource
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.dp
import androidx.datastore.core.DataStore
import androidx.datastore.preferences.core.Preferences
import androidx.datastore.preferences.core.edit
import androidx.datastore.preferences.core.stringPreferencesKey
import androidx.datastore.preferences.preferencesDataStore
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.map
import kotlinx.coroutines.launch

// DataStore 设置
val Context.dataStore: DataStore<Preferences> by preferencesDataStore(name = "settings")
val THEME_MODE_KEY = stringPreferencesKey("theme_mode")

// 主题模式枚举
enum class ThemeMode { Light, Dark, System }

// 主题数据仓库
class ThemeRepository(private val context: Context) {
    // 保存主题模式
    suspend fun saveThemeMode(themeMode: ThemeMode) {
        context.dataStore.edit { preferences ->
            preferences[THEME_MODE_KEY] = themeMode.name
        }
    }
    
    // 读取主题模式
    val themeMode: Flow<ThemeMode> = context.dataStore.data
        .map { preferences ->
            val themeModeString = preferences[THEME_MODE_KEY] ?: ThemeMode.System.name
            try {
                ThemeMode.valueOf(themeModeString)
            } catch (e: Exception) {
                ThemeMode.System
            }
        }
}

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            DarkThemeDemoApp()
        }
    }
}

@Composable
fun DarkThemeDemoApp() {
    val context = LocalContext.current
    val themeRepository = remember { ThemeRepository(context) }
    val themeMode = themeRepository.themeMode.collectAsState(initial = ThemeMode.System)
    val coroutineScope = rememberCoroutineScope()
    
    // 根据主题模式决定是否使用暗色主题
    val darkTheme = when (themeMode.value) {
        ThemeMode.Dark -> true
        ThemeMode.Light -> false
        ThemeMode.System -> isSystemInDarkTheme()
    }
    
    // 定义颜色方案
    val lightColorScheme = lightColorScheme(
        primary = Color(0xFF6200EE),
        secondary = Color(0xFF03DAC6),
        tertiary = Color(0xFF3700B3),
        background = Color(0xFFF1F1F1),
        surface = Color(0xFFFFFFFF),
        onPrimary = Color(0xFFFFFFFF),
        onSecondary = Color(0xFF000000),
        onTertiary = Color(0xFFFFFFFF),
        onBackground = Color(0xFF1C1B1F),
        onSurface = Color(0xFF1C1B1F),
    )
    
    val darkColorScheme = darkColorScheme(
        primary = Color(0xFFBB86FC),
        secondary = Color(0xFF03DAC6),
        tertiary = Color(0xFF3700B3),
        background = Color(0xFF121212),
        surface = Color(0xFF1E1E1E),
        onPrimary = Color(0xFF000000),
        onSecondary = Color(0xFF000000),
        onTertiary = Color(0xFFFFFFFF),
        onBackground = Color(0xFFE6E1E5),
        onSurface = Color(0xFFE6E1E5),
    )
    
    MaterialTheme(
        colorScheme = if (darkTheme) darkColorScheme else lightColorScheme,
        typography = Typography(),
        shapes = Shapes()
    ) {
        AppContent(
            themeMode = themeMode.value,
            onThemeModeChanged = { newMode ->
                coroutineScope.launch {
                    themeRepository.saveThemeMode(newMode)
                }
            }
        )
    }
}

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun AppContent(
    themeMode: ThemeMode,
    onThemeModeChanged: (ThemeMode) -> Unit
) {
    val showSettings = remember { mutableStateOf(false) }
    
    Scaffold(
        topBar = {
            SmallTopAppBar(
                title = { Text("深浅色模式示例") },
                actions = {
                    IconButton(onClick = { showSettings.value = true }) {
                        Icon(
                            imageVector = Icons.Default.Settings,
                            contentDescription = "设置"
                        )
                    }
                }
            )
        }
    ) { innerPadding ->
        Box(
            modifier = Modifier
                .fillMaxSize()
                .padding(innerPadding)
        ) {
            // 主屏幕内容
            MainScreenContent()
            
            // 设置弹窗
            if (showSettings.value) {
                AlertDialog(
                    onDismissRequest = { showSettings.value = false },
                    title = { Text("主题设置") },
                    text = {
                        ThemeSettings(
                            currentThemeMode = themeMode,
                            onThemeModeChanged = onThemeModeChanged
                        )
                    },
                    confirmButton = {
                        TextButton(onClick = { showSettings.value = false }) {
                            Text("完成")
                        }
                    }
                )
            }
        }
    }
}

@Composable
fun MainScreenContent() {
    val colorScheme = MaterialTheme.colorScheme
    val isDark = isSystemInDarkTheme()
    
    Column(
        modifier = Modifier
            .fillMaxSize()
            .padding(16.dp)
    ) {
        // 当前主题状态
        Card(
            colors = CardDefaults.cardColors(),
            modifier = Modifier.fillMaxWidth()
        ) {
            Column(
                modifier = Modifier.padding(16.dp),
                horizontalAlignment = Alignment.CenterHorizontally
            ) {
                Icon(
                    imageVector = if (isDark) Icons.Default.DarkMode else Icons.Default.LightMode,
                    contentDescription = null,
                    tint = colorScheme.primary,
                    modifier = Modifier.size(48.dp)
                )
                Spacer(modifier = Modifier.height(8.dp))
                Text(
                    text = "当前使用${if (isDark) "暗色" else "亮色"}主题",
                    style = MaterialTheme.typography.titleLarge,
                    textAlign = TextAlign.Center
                )
            }
        }
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // 示例卡片
        Card(
            modifier = Modifier.fillMaxWidth()
        ) {
            Column(
                modifier = Modifier.padding(16.dp)
            ) {
                Text(
                    text = "卡片示例",
                    style = MaterialTheme.typography.titleMedium
                )
                Spacer(modifier = Modifier.height(8.dp))
                Text(
                    text = "这个卡片会根据当前主题自动调整外观。",
                    style = MaterialTheme.typography.bodyMedium
                )
                Spacer(modifier = Modifier.height(16.dp))
                
                // 图片资源会自动适配深浅色模式
                Image(
                    painter = painterResource(id = R.drawable.sample_image),
                    contentDescription = null,
                    modifier = Modifier
                        .height(150.dp)
                        .fillMaxWidth()
                        .clip(RoundedCornerShape(8.dp))
                )
                
                Spacer(modifier = Modifier.height(16.dp))
                
                Row(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalArrangement = Arrangement.End
                ) {
                    TextButton(onClick = { /* 操作 */ }) {
                        Text("取消")
                    }
                    Spacer(modifier = Modifier.width(8.dp))
                    Button(onClick = { /* 操作 */ }) {
                        Text("确认")
                    }
                }
            }
        }
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // 自定义组件示例
        CustomInfoBox(
            icon = {
                Icon(
                    imageVector = Icons.Default.DarkMode,
                    contentDescription = null
                )
            },
            title = "自定义组件",
            description = "这个自定义组件会根据当前主题自动调整颜色和样式。"
        )
    }
}

@Composable
fun ThemeSettings(
    currentThemeMode: ThemeMode,
    onThemeModeChanged: (ThemeMode) -> Unit
) {
    Column {
        // 主题模式选项
        RadioButtonWithText(
            text = "跟随系统",
            selected = currentThemeMode == ThemeMode.System,
            onClick = { onThemeModeChanged(ThemeMode.System) }
        )
        
        RadioButtonWithText(
            text = "亮色模式",
            selected = currentThemeMode == ThemeMode.Light,
            onClick = { onThemeModeChanged(ThemeMode.Light) }
        )
        
        RadioButtonWithText(
            text = "暗色模式",
            selected = currentThemeMode == ThemeMode.Dark,
            onClick = { onThemeModeChanged(ThemeMode.Dark) }
        )
    }
}

@Composable
fun RadioButtonWithText(
    text: String,
    selected: Boolean,
    onClick: () -> Unit
) {
    Row(
        verticalAlignment = Alignment.CenterVertically,
        modifier = Modifier
            .fillMaxWidth()
            .clickable(onClick = onClick)
            .padding(vertical = 8.dp)
    ) {
        RadioButton(
            selected = selected,
            onClick = onClick
        )
        Spacer(modifier = Modifier.width(8.dp))
        Text(text = text)
    }
}

@Composable
fun CustomInfoBox(
    icon: @Composable () -> Unit,
    title: String,
    description: String
) {
    val colors = MaterialTheme.colorScheme
    
    Surface(
        color = colors.primaryContainer,
        contentColor = colors.onPrimaryContainer,
        shape = RoundedCornerShape(8.dp),
        modifier = Modifier.fillMaxWidth()
    ) {
        Row(
            modifier = Modifier
                .fillMaxWidth()
                .padding(16.dp),
            verticalAlignment = Alignment.CenterVertically
        ) {
            Box(
                modifier = Modifier
                    .size(40.dp)
                    .background(
                        color = colors.primary,
                        shape = CircleShape
                    ),
                contentAlignment = Alignment.Center
            ) {
                CompositionLocalProvider(
                    LocalContentColor provides colors.onPrimary
                ) {
                    icon()
                }
            }
            
            Spacer(modifier = Modifier.width(16.dp))
            
            Column {
                Text(
                    text = title,
                    style = MaterialTheme.typography.titleMedium
                )
                Spacer(modifier = Modifier.height(4.dp))
                Text(
                    text = description,
                    style = MaterialTheme.typography.bodyMedium
                )
            }
        }
    }
} 