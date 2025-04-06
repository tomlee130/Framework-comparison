# 六大框架本地化与国际化比较

本文档对比Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue六大框架中本地化与国际化的实现和特性，从以下维度进行分析：

- 本地化与国际化
- 字符串资源管理
- 多语言切换机制
- 日期/时间/数字格式化
- RTL布局支持
- 区域特定内容适配

每个框架部分包含平台特性、兼容性分析和完整实例代码。

## 目录

1. [Flutter 本地化与国际化](#flutter-本地化与国际化)
2. [Android Jetpack Compose 本地化与国际化](#android-jetpack-compose-本地化与国际化)
3. [iOS UIKit 本地化与国际化](#ios-uikit-本地化与国际化)
4. [iOS SwiftUI 本地化与国际化](#ios-swiftui-本地化与国际化)
5. [鸿蒙 ArkUI 本地化与国际化](#鸿蒙-arkui-本地化与国际化)
6. [Vue 本地化与国际化](#vue-本地化与国际化)
7. [总结对比](#总结对比)

## Flutter 本地化与国际化

### 本地化与国际化

Flutter提供了完善的国际化支持，主要通过`flutter_localizations`包和`intl`包实现：

- 使用`MaterialApp`的`localizationsDelegates`和`supportedLocales`配置支持的语言
- 可通过`Localizations.of(context, AppLocalizations)`访问翻译资源
- 支持查找最接近的匹配语言（语言回退机制）
- 内置多种常用语言的本地化支持
- 可以根据系统语言自动切换应用语言
- 支持运行时动态切换语言

### 字符串资源管理

- 通过ARB（Application Resource Bundle）文件管理不同语言的字符串资源
- 支持复数形式和性别形式的字符串处理
- 使用`intl`包中的`Intl.message()`定义可翻译字符串
- 支持参数化字符串，可插入变量
- 支持通过命令行工具从ARB文件生成Dart代码
- 可以使用注释提供上下文信息，帮助翻译人员
- 支持字符串分组和命名空间管理

### 多语言切换机制

- 默认跟随系统语言设置
- 可通过修改`MaterialApp`的`locale`属性实现运行时切换
- 使用`Localizations`小部件监听并响应区域设置变化
- 支持将用户选择的语言保存到持久化存储
- 可以实现细粒度的语言控制，如仅修改特定小部件的语言
- 支持重新启动应用程序以应用语言更改
- 支持使用`InheritedWidget`在整个应用中传递语言设置

### 日期/时间/数字格式化

- 通过`intl`包提供丰富的格式化功能
- `DateFormat`类支持多种日期和时间格式化样式
- `NumberFormat`支持数字、货币、百分比等的格式化
- 格式会根据当前区域设置自动调整
- 支持自定义格式模式
- 自动处理不同区域的数字分隔符、货币符号等
- 支持相对时间格式化（如"3分钟前"）

### RTL布局支持

- 内置对从右到左(RTL)语言的支持，如阿拉伯语和希伯来语
- 通过`Directionality`小部件控制文本和布局方向
- 使用`TextDirection`枚举指定文本方向
- 布局会根据RTL语言自动镜像
- 支持`textAlign`属性适应不同文本方向
- 图标和图像可以通过`TextDirection.rtl`自动镜像
- 提供`TextDirection.of(context)`获取当前文本方向

### 区域特定内容适配

- 支持基于区域设置条件性显示不同内容
- 可以根据区域设置加载不同的图像和资源
- 支持为特定区域定制布局调整
- 可以处理不同语言的文本长度变化
- 提供工具处理货币、度量单位等区域差异
- 支持本地化数据验证规则（如电话号码格式）
- 可以根据地区特定假日或事件显示特定内容

### 平台特性与兼容性

- 跨平台一致的国际化API
- 完全支持所有Flutter运行的平台（iOS、Android、Web、Windows、macOS、Linux）
- 支持最新Unicode标准和CLDR数据
- 兼容Material Design和Cupertino的本地化规范
- 与平台特定的本地化框架无缝集成
- 支持渐进式增强，可以从简单实现开始，逐步添加复杂功能
- 对旧版本设备和操作系统有良好的兼容性

### 完整示例代码

```dart
import 'package:flutter/material.dart';
import 'package:flutter_localizations/flutter_localizations.dart';
import 'package:intl/intl.dart';
import 'l10n/app_localizations.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatefulWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  State<MyApp> createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  Locale _locale = const Locale('zh', 'CN');

  void _setLocale(Locale locale) {
    setState(() {
      _locale = locale;
    });
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter本地化示例',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      locale: _locale,
      // 1. 配置支持的语言
      supportedLocales: const [
        Locale('en', 'US'),
        Locale('zh', 'CN'),
        Locale('ja', 'JP'),
        Locale('ar', 'SA'), // 阿拉伯语(RTL语言)
      ],
      // 2. 配置本地化代理
      localizationsDelegates: const [
        AppLocalizations.delegate, // 应用特定的本地化委托
        GlobalMaterialLocalizations.delegate, // Material组件本地化
        GlobalWidgetsLocalizations.delegate, // 基础组件本地化
        GlobalCupertinoLocalizations.delegate, // Cupertino组件本地化
      ],
      home: LocalizationDemo(
        setLocale: _setLocale,
        currentLocale: _locale,
      ),
    );
  }
}

class LocalizationDemo extends StatefulWidget {
  final Function(Locale) setLocale;
  final Locale currentLocale;

  const LocalizationDemo({
    Key? key,
    required this.setLocale,
    required this.currentLocale,
  }) : super(key: key);

  @override
  State<LocalizationDemo> createState() => _LocalizationDemoState();
}

class _LocalizationDemoState extends State<LocalizationDemo> {
  int _counter = 0;
  final DateTime _now = DateTime.now();

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    // 3. 获取本地化实例
    final localizations = AppLocalizations.of(context)!;
    
    // 4. 日期格式化示例
    final dateFormatter = DateFormat.yMMMMd(widget.currentLocale.toString());
    final timeFormatter = DateFormat.Hms(widget.currentLocale.toString());
    final formattedDate = dateFormatter.format(_now);
    final formattedTime = timeFormatter.format(_now);
    
    // 5. 数字格式化示例
    final numberFormatter = NumberFormat.decimalPattern(widget.currentLocale.toString());
    final currencyFormatter = NumberFormat.currency(
      locale: widget.currentLocale.toString(),
      symbol: widget.currentLocale.countryCode == 'US' ? '\$' : 
              widget.currentLocale.countryCode == 'CN' ? '¥' :
              widget.currentLocale.countryCode == 'JP' ? '¥' : '€',
    );
    
    // 判断是否为RTL语言
    final isRtl = Localizations.localeOf(context).languageCode == 'ar';

    return Scaffold(
      appBar: AppBar(
        title: Text(localizations.appTitle),
      ),
      body: Directionality(
        // 6. RTL支持
        textDirection: isRtl ? TextDirection.rtl : TextDirection.ltr,
        child: Center(
          child: Padding(
            padding: const EdgeInsets.all(20.0),
            child: Column(
              mainAxisAlignment: MainAxisAlignment.center,
              crossAxisAlignment: CrossAxisAlignment.start,
              children: <Widget>[
                // 7. 使用本地化字符串
                Text(
                  localizations.helloWorld,
                  style: Theme.of(context).textTheme.headlineMedium,
                ),
                const SizedBox(height: 20),
                
                // 8. 带参数的本地化字符串
                Text(
                  localizations.counterText(_counter),
                  style: Theme.of(context).textTheme.titleLarge,
                ),
                const SizedBox(height: 20),
                
                // 9. 日期和时间格式化
                Text(localizations.dateLabel),
                Text(formattedDate, style: const TextStyle(fontWeight: FontWeight.bold)),
                const SizedBox(height: 10),
                
                Text(localizations.timeLabel),
                Text(formattedTime, style: const TextStyle(fontWeight: FontWeight.bold)),
                const SizedBox(height: 20),
                
                // 10. 数字和货币格式化
                Text(localizations.numberLabel),
                Text(numberFormatter.format(1234567.89), 
                     style: const TextStyle(fontWeight: FontWeight.bold)),
                const SizedBox(height: 10),
                
                Text(localizations.currencyLabel),
                Text(currencyFormatter.format(1234.56), 
                     style: const TextStyle(fontWeight: FontWeight.bold)),
                const SizedBox(height: 20),
                
                // 11. 区域特定内容适配
                Container(
                  padding: const EdgeInsets.all(10),
                  decoration: BoxDecoration(
                    color: Colors.grey.shade200,
                    borderRadius: BorderRadius.circular(8),
                  ),
                  child: Column(
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      Text(
                        localizations.regionSpecificContent,
                        style: const TextStyle(fontWeight: FontWeight.bold),
                      ),
                      const SizedBox(height: 5),
                      // 根据区域显示不同内容
                      Text(localizations.regionSpecificExample),
                    ],
                  ),
                ),
                
                const Spacer(),
                
                // 12. 语言选择器
                Center(
                  child: Wrap(
                    spacing: 10,
                    children: [
                      LanguageButton(
                        onPressed: () => widget.setLocale(const Locale('en', 'US')),
                        isSelected: widget.currentLocale.languageCode == 'en',
                        languageName: 'English',
                      ),
                      LanguageButton(
                        onPressed: () => widget.setLocale(const Locale('zh', 'CN')),
                        isSelected: widget.currentLocale.languageCode == 'zh',
                        languageName: '中文',
                      ),
                      LanguageButton(
                        onPressed: () => widget.setLocale(const Locale('ja', 'JP')),
                        isSelected: widget.currentLocale.languageCode == 'ja',
                        languageName: '日本語',
                      ),
                      LanguageButton(
                        onPressed: () => widget.setLocale(const Locale('ar', 'SA')),
                        isSelected: widget.currentLocale.languageCode == 'ar',
                        languageName: 'العربية',
                      ),
                    ],
                  ),
                ),
              ],
            ),
          ),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: localizations.incrementTooltip,
        child: const Icon(Icons.add),
      ),
    );
  }
}

class LanguageButton extends StatelessWidget {
  final VoidCallback onPressed;
  final bool isSelected;
  final String languageName;
  
  const LanguageButton({
    Key? key,
    required this.onPressed,
    required this.isSelected,
    required this.languageName,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return ElevatedButton(
      onPressed: onPressed,
      style: ElevatedButton.styleFrom(
        backgroundColor: isSelected ? Colors.blue : Colors.grey,
      ),
      child: Text(languageName),
    );
  }
}

## Android Jetpack Compose 本地化与国际化

### 本地化与国际化

Jetpack Compose利用Android平台原生的本地化系统实现国际化：

- 通过Android资源系统管理本地化资源
- 使用`stringResource()`函数加载本地化字符串
- 自动根据系统语言设置加载对应的资源
- 支持区域限定符（如`values-zh-rCN`）细化本地化资源
- 兼容Android平台的资源回退机制
- 可以通过Composable函数的参数传递本地化信息
- 支持应用内语言切换

### 字符串资源管理

- 通过XML文件（`strings.xml`）管理不同语言的字符串
- 资源文件放置在`res/values-<语言代码>`目录下
- 支持字符串中的参数占位符和格式化
- 可以使用Android资源工具进行资源管理
- 支持字符串数组和复数形式
- 可以从资源中加载各种格式化文本
- 支持HTML标记和特殊字符处理

### 多语言切换机制

- 默认跟随系统语言设置
- 使用`AppCompatDelegate.setApplicationLocales()`实现运行时切换
- 可以通过`LocaleListCompat`设置优先语言顺序
- 支持保存用户语言偏好到`SharedPreferences`
- 通过`CompositionLocalProvider`在Compose中传递语言设置
- 语言切换可以立即应用，无需重启应用
- 支持在特定屏幕使用不同于应用其他部分的语言

### 日期/时间/数字格式化

- 使用`java.text.DateFormat`和`java.text.SimpleDateFormat`格式化日期时间
- `java.text.NumberFormat`支持数字、货币、百分比格式化
- 格式会根据当前区域设置自动调整
- Android 8.0+提供的`android.icu`包支持更高级的格式化
- Kotlin扩展函数简化格式化操作
- 提供相对时间格式化（如"2小时前"）
- 自动处理区域特定的数字和日期分隔符

### RTL布局支持

- 完全支持从右到左(RTL)语言
- 通过`layoutDirection`参数控制布局方向
- 自动镜像适当的UI元素（如返回图标）
- 支持`CompositionLocalProvider(LocalLayoutDirection)`修改布局方向
- 文本对齐自动适应当前布局方向
- 支持混合布局方向（部分LTR，部分RTL）
- 配合`BiDi`算法处理混合方向文本

### 区域特定内容适配

- 支持基于当前语言和区域条件性显示内容
- 可以根据地区加载不同布局和资源
- 支持为特定区域自定义主题和样式
- 自动适应不同语言的文本长度
- 可以定制区域特定的验证规则和输入格式
- 支持本地化的排序和搜索功能
- 处理不同区域的度量单位和日期格式差异

### 平台特性与兼容性

- 专为Android平台优化
- 支持Android 5.0（API 21）及以上版本
- 与Android系统本地化设置无缝集成
- 兼容Material Design的国际化指南
- 支持Android 7.0+的语言偏好设置
- 适配不同屏幕尺寸和方向的本地化布局
- 与旧版UI框架（如XML布局）的本地化资源兼容

### 完整示例代码

```kotlin
import android.content.Context
import android.content.res.Configuration
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.appcompat.app.AppCompatDelegate
import androidx.compose.foundation.background
import androidx.compose.foundation.border
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.shape.RoundedCornerShape
import androidx.compose.foundation.verticalScroll
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.platform.LocalConfiguration
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.platform.LocalLayoutDirection
import androidx.compose.ui.res.pluralStringResource
import androidx.compose.ui.res.stringResource
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.LayoutDirection
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import androidx.core.os.LocaleListCompat
import java.text.DateFormat
import java.text.NumberFormat
import java.util.*

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        setContent {
            MaterialTheme {
                LocalizationDemoScreen()
            }
        }
    }
}

@Composable
fun LocalizationDemoScreen() {
    // 1. 获取当前上下文和配置
    val context = LocalContext.current
    val configuration = LocalConfiguration.current
    val currentLocale = configuration.locales[0]
    
    // 2. 本地化数据
    val now = remember { Date() }
    var counter by remember { mutableStateOf(0) }
    
    // 3. 提供支持的语言选项
    val supportedLocales = remember {
        listOf(
            Locale.US,             // 英语(美国)
            Locale.SIMPLIFIED_CHINESE, // 中文(简体)
            Locale.JAPAN,          // 日语
            Locale("ar", "SA")     // 阿拉伯语
        )
    }
    
    // 4. 检测当前是否为RTL布局
    val isRtl = configuration.layoutDirection == Configuration.SCREENLAYOUT_LAYOUTDIR_RTL
    
    // 包装内容在适当的布局方向中
    CompositionLocalProvider(
        LocalLayoutDirection provides if (isRtl) LayoutDirection.Rtl else LayoutDirection.Ltr
    ) {
        Surface(
            modifier = Modifier.fillMaxSize()
        ) {
            Column(
                modifier = Modifier
                    .padding(16.dp)
                    .verticalScroll(rememberScrollState())
            ) {
                // 5. 应用标题 (从字符串资源加载)
                Text(
                    text = stringResource(id = R.string.app_title),
                    style = MaterialTheme.typography.headlineMedium,
                    modifier = Modifier.padding(bottom = 24.dp)
                )
                
                // 6. 基本本地化字符串示例
                Text(
                    text = stringResource(id = R.string.hello_world),
                    style = MaterialTheme.typography.titleLarge,
                    modifier = Modifier.padding(bottom = 16.dp)
                )
                
                // 7. 使用复数形式
                Button(
                    onClick = { counter++ },
                    modifier = Modifier.align(Alignment.CenterHorizontally)
                ) {
                    Text(stringResource(id = R.string.increment_button))
                }
                
                Text(
                    text = pluralStringResource(
                        id = R.plurals.counter_text,
                        count = counter,
                        counter
                    ),
                    modifier = Modifier.padding(vertical = 16.dp)
                )
                
                // 8. 日期和时间格式化
                FormattedSection(
                    title = stringResource(id = R.string.date_label),
                    formattedValue = remember(currentLocale) {
                        DateFormat.getDateInstance(DateFormat.LONG, currentLocale).format(now)
                    }
                )
                
                FormattedSection(
                    title = stringResource(id = R.string.time_label),
                    formattedValue = remember(currentLocale) {
                        DateFormat.getTimeInstance(DateFormat.MEDIUM, currentLocale).format(now)
                    }
                )
                
                // 9. 数字和货币格式化
                FormattedSection(
                    title = stringResource(id = R.string.number_label),
                    formattedValue = remember(currentLocale) {
                        NumberFormat.getNumberInstance(currentLocale).format(1234567.89)
                    }
                )
                
                FormattedSection(
                    title = stringResource(id = R.string.currency_label),
                    formattedValue = remember(currentLocale) {
                        NumberFormat.getCurrencyInstance(currentLocale).format(1234.56)
                    }
                )
                
                // 10. 区域特定内容展示
                Card(
                    modifier = Modifier
                        .fillMaxWidth()
                        .padding(vertical = 16.dp),
                    shape = RoundedCornerShape(8.dp)
                ) {
                    Column(modifier = Modifier.padding(16.dp)) {
                        Text(
                            text = stringResource(id = R.string.region_specific_title),
                            fontWeight = FontWeight.Bold,
                            modifier = Modifier.padding(bottom = 8.dp)
                        )
                        
                        Text(
                            text = stringResource(id = R.string.region_specific_content)
                        )
                    }
                }
                
                // 11. 当前语言信息
                Text(
                    text = stringResource(id = R.string.current_language, currentLocale.displayName),
                    modifier = Modifier.padding(vertical = 16.dp)
                )
                
                // 12. 语言选择器
                Text(
                    text = stringResource(id = R.string.select_language),
                    style = MaterialTheme.typography.titleMedium,
                    modifier = Modifier.padding(bottom = 8.dp)
                )
                
                Row(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalArrangement = Arrangement.SpaceEvenly
                ) {
                    supportedLocales.forEach { locale ->
                        LanguageButton(
                            languageName = locale.displayName,
                            isSelected = currentLocale.language == locale.language,
                            onClick = {
                                val localeList = LocaleListCompat.create(locale)
                                AppCompatDelegate.setApplicationLocales(localeList)
                            }
                        )
                    }
                }
                
                Spacer(modifier = Modifier.height(40.dp))
            }
        }
    }
}

@Composable
fun FormattedSection(title: String, formattedValue: String) {
    Column(modifier = Modifier.padding(vertical = 8.dp)) {
        Text(
            text = title,
            fontSize = 16.sp
        )
        Text(
            text = formattedValue,
            fontWeight = FontWeight.Bold,
            fontSize = 18.sp,
            modifier = Modifier.padding(top = 4.dp)
        )
    }
}

@Composable
fun LanguageButton(
    languageName: String,
    isSelected: Boolean,
    onClick: () -> Unit
) {
    Button(
        onClick = onClick,
        colors = ButtonDefaults.buttonColors(
            containerColor = if (isSelected) MaterialTheme.colorScheme.primary 
                            else MaterialTheme.colorScheme.surfaceVariant
        ),
        modifier = Modifier.padding(4.dp)
    ) {
        Text(
            text = when (languageName) {
                "English (United States)" -> "English"
                "Chinese (Simplified)" -> "中文"
                "Japanese" -> "日本語"
                "Arabic (Saudi Arabia)" -> "العربية"
                else -> languageName
            },
            color = if (isSelected) MaterialTheme.colorScheme.onPrimary 
                   else MaterialTheme.colorScheme.onSurfaceVariant
        )
    }
}
```

资源文件结构示例：

`res/values/strings.xml` (默认英文)
```xml
<resources>
    <string name="app_title">Jetpack Compose Localization Demo</string>
    <string name="hello_world">Hello World!</string>
    <string name="increment_button">Increment Counter</string>
    <plurals name="counter_text">
        <item quantity="one">You have clicked the button %d time</item>
        <item quantity="other">You have clicked the button %d times</item>
    </plurals>
    <string name="date_label">Today\'s date:</string>
    <string name="time_label">Current time:</string>
    <string name="number_label">Formatted number:</string>
    <string name="currency_label">Formatted currency:</string>
    <string name="region_specific_title">Region Specific Content:</string>
    <string name="region_specific_content">This content is customized for English-speaking regions. It might show different information based on your location and language.</string>
    <string name="current_language">Current language: %s</string>
    <string name="select_language">Select a language:</string>
</resources>
```

`res/values-zh-rCN/strings.xml` (简体中文)
```xml
<resources>
    <string name="app_title">Jetpack Compose 本地化示例</string>
    <string name="hello_world">你好，世界！</string>
    <string name="increment_button">增加计数</string>
    <plurals name="counter_text">
        <item quantity="other">你已点击按钮 %d 次</item>
    </plurals>
    <string name="date_label">今天日期：</string>
    <string name="time_label">当前时间：</string>
    <string name="number_label">格式化数字：</string>
    <string name="currency_label">格式化货币：</string>
    <string name="region_specific_title">区域特定内容：</string>
    <string name="region_specific_content">此内容专为中文使用区域定制。根据您的位置和语言，可能会显示不同的信息。</string>
    <string name="current_language">当前语言：%s</string>
    <string name="select_language">选择语言：</string>
</resources>
```

`res/values-ja/strings.xml` (日语)
```xml
<resources>
    <string name="app_title">Jetpack Compose ローカリゼーションデモ</string>
    <string name="hello_world">こんにちは世界！</string>
    <string name="increment_button">カウンターを増やす</string>
    <plurals name="counter_text">
        <item quantity="other">ボタンを %d 回クリックしました</item>
    </plurals>
    <string name="date_label">今日の日付：</string>
    <string name="time_label">現在の時刻：</string>
    <string name="number_label">フォーマットされた数字：</string>
    <string name="currency_label">フォーマットされた通貨：</string>
    <string name="region_specific_title">地域固有のコンテンツ：</string>
    <string name="region_specific_content">このコンテンツは日本語圏向けにカスタマイズされています。あなたの場所と言語に基づいて異なる情報が表示される場合があります。</string>
    <string name="current_language">現在の言語：%s</string>
    <string name="select_language">言語を選択：</string>
</resources>
```

`res/values-ar/strings.xml` (阿拉伯语 - RTL支持)
```xml
<resources>
    <string name="app_title">عرض توطين Jetpack Compose</string>
    <string name="hello_world">مرحبًا بالعالم!</string>
    <string name="increment_button">زيادة العداد</string>
    <plurals name="counter_text">
        <item quantity="zero">لم تنقر على الزر بعد</item>
        <item quantity="one">لقد نقرت على الزر مرة واحدة</item>
        <item quantity="two">لقد نقرت على الزر مرتين</item>
        <item quantity="few">لقد نقرت على الزر %d مرات</item>
        <item quantity="many">لقد نقرت على الزر %d مرة</item>
        <item quantity="other">لقد نقرت على الزر %d مرة</item>
    </plurals>
    <string name="date_label">تاريخ اليوم:</string>
    <string name="time_label">الوقت الحالي:</string>
    <string name="number_label">الرقم المنسق:</string>
    <string name="currency_label">العملة المنسقة:</string>
    <string name="region_specific_title">المحتوى الخاص بالمنطقة:</string>
    <string name="region_specific_content">تم تخصيص هذا المحتوى للمناطق الناطقة بالعربية. قد يعرض معلومات مختلفة بناءً على موقعك ولغتك.</string>
    <string name="current_language">اللغة الحالية: %s</string>
    <string name="select_language">اختر لغة:</string>
</resources>
``` 