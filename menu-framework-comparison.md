# 六大框架 Menu 组件比较

本文档对比Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue六大框架中Menu组件的实现和特性，从以下维度进行分析：

- 菜单项配置
- 子菜单支持
- 上下文菜单
- 快捷键绑定
- 可访问性支持

每个框架部分包含平台特性、兼容性分析和完整实例代码。

## 目录

1. [Flutter Menu](#flutter-menu)
2. [Android Jetpack Compose Menu](#android-jetpack-compose-menu)
3. [iOS UIKit Menu](#ios-uikit-menu)
4. [iOS SwiftUI Menu](#ios-swiftui-menu)
5. [鸿蒙 ArkUI Menu](#鸿蒙-arkui-menu)
6. [Vue Menu](#vue-menu)
7. [总结对比](#总结对比)

## Flutter Menu

### 菜单项配置

Flutter提供了多种菜单相关组件，主要包括：

- `PopupMenuButton`：弹出菜单按钮，常用于AppBar的操作菜单
- `DropdownButton`：下拉菜单按钮，适用于单选场景
- `MenuBar` 和 `MenuItemButton`：Flutter 3.7+引入的桌面风格菜单栏组件
- `ContextMenuArea`：用于处理右键上下文菜单（Flutter 3.3+）

菜单项配置选项：

- 可以通过`PopupMenuItem`自定义每个菜单项
- 支持图标、文本、分割线等元素组合
- 可以设置菜单项禁用状态（`enabled: false`）
- 支持自定义菜单项高度、内边距等样式
- 可以通过`PopupMenuDivider`添加分隔线
- 支持通过传入自定义Widget实现复杂菜单项
- 菜单项可以返回任意类型的值

### 子菜单支持

- `PopupMenuButton`原生不支持嵌套子菜单
- Flutter 3.7+引入的`MenuBar`、`SubmenuButton`和`MenuItemButton`提供了原生子菜单支持
- 可以通过手动管理菜单状态来实现自定义子菜单
- 子菜单可以设置级联动画和展开方向
- 子菜单层级没有明确限制，但建议不超过3层
- 子菜单可以继承父菜单的样式或使用独立样式

### 上下文菜单

- Flutter 3.3+引入`ContextMenuArea`用于实现右键上下文菜单
- 可通过`GestureDetector`配合`showMenu`实现长按弹出菜单
- 自定义`PopupMenuButton`的`offset`实现菜单位置控制
- 支持检测菜单触发位置，可以根据位置动态调整菜单内容
- 上下文菜单可以响应特定Widget区域的事件
- 可以用`showMenu`直接在任意位置显示菜单

### 快捷键绑定

- Flutter 3.7+的`MenuItemButton`支持通过`shortcuts`属性设置快捷键
- 可以使用`MenuAcceleratorLabel`显示菜单项快捷键提示
- 支持跨平台的快捷键定义（如Ctrl+C、Alt+F4等）
- 可通过`Actions`和`Shortcuts`系统实现全局快捷键
- `FocusableActionDetector`可用于响应焦点区域的快捷键
- 快捷键显示会根据平台自动适配（Windows/Linux/macOS）

### 可访问性支持

- 菜单项支持`Semantics`标签配置
- 通过`semanticLabel`提供屏幕阅读器描述
- 支持键盘导航操作菜单
- 符合WCAG可访问性指南的焦点管理
- 支持高对比度模式
- 菜单可以响应系统文字大小设置
- 支持屏幕阅读器朗读菜单内容
- 提供足够的触摸目标大小

### 平台特性与兼容性

- 跨平台一致的API，但会自动适应各平台的视觉风格
- 在Android和iOS上遵循Material Design和Cupertino风格
- 桌面平台上提供更贴近原生的菜单体验
- Flutter 3.7+版本对菜单组件进行了重大增强，特别是桌面平台
- 支持所有Flutter运行的平台（iOS、Android、Web、Windows、macOS、Linux）
- 新的菜单组件需要Flutter 3.7+版本
- 子菜单和上下文菜单功能在较旧版本中需要自行实现

### 完整示例代码

```dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Menu Demo',
      theme: ThemeData(
        primarySwatch: Colors.blue,
        visualDensity: VisualDensity.adaptivePlatformDensity,
      ),
      home: const MenuDemo(),
    );
  }
}

class MenuDemo extends StatefulWidget {
  const MenuDemo({Key? key}) : super(key: key);

  @override
  State<MenuDemo> createState() => _MenuDemoState();
}

class _MenuDemoState extends State<MenuDemo> {
  String _selectedMenuItem = "无";
  String _selectedDropdown = "选项1";
  String _contextMenuItem = "无";
  bool _checkboxSelected = false;
  bool _radioSelected = false;
  
  final List<String> _dropdownItems = ["选项1", "选项2", "选项3", "选项4"];
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Flutter Menu 组件示例'),
        actions: [
          // 1. PopupMenuButton示例
          PopupMenuButton<String>(
            tooltip: '菜单',
            icon: const Icon(Icons.more_vert),
            onSelected: (String result) {
              setState(() {
                _selectedMenuItem = result;
              });
            },
            itemBuilder: (BuildContext context) => <PopupMenuEntry<String>>[
              const PopupMenuItem<String>(
                value: '设置',
                child: ListTile(
                  leading: Icon(Icons.settings),
                  title: Text('设置'),
                ),
              ),
              const PopupMenuItem<String>(
                value: '个人资料',
                child: ListTile(
                  leading: Icon(Icons.person),
                  title: Text('个人资料'),
                ),
              ),
              const PopupMenuDivider(),
              PopupMenuItem<String>(
                value: '复选框选项',
                child: StatefulBuilder(
                  builder: (context, setState) => CheckboxListTile(
                    title: const Text('复选框选项'),
                    value: _checkboxSelected,
                    onChanged: (bool? value) {
                      setState(() {
                        _checkboxSelected = value!;
                      });
                      this.setState(() {});
                    },
                    controlAffinity: ListTileControlAffinity.leading,
                    contentPadding: EdgeInsets.zero,
                  ),
                ),
              ),
              PopupMenuItem<String>(
                value: '单选框选项',
                child: ListTile(
                  leading: Radio<bool>(
                    value: true,
                    groupValue: _radioSelected,
                    onChanged: (bool? value) {
                      setState(() {
                        _radioSelected = value!;
                      });
                    },
                  ),
                  title: const Text('单选框选项'),
                  onTap: () {
                    setState(() {
                      _radioSelected = !_radioSelected;
                    });
                    Navigator.pop(context);
                  },
                ),
              ),
              const PopupMenuItem<String>(
                value: '禁用项',
                enabled: false,
                child: ListTile(
                  leading: Icon(Icons.block),
                  title: Text('禁用项'),
                ),
              ),
            ],
          ),
        ],
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text('选择的菜单项: $_selectedMenuItem', style: const TextStyle(fontSize: 16)),
            const SizedBox(height: 20),
            
            // 2. DropdownButton示例
            const Text('DropdownButton(下拉菜单):', style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold)),
            DropdownButton<String>(
              value: _selectedDropdown,
              icon: const Icon(Icons.arrow_drop_down),
              isExpanded: true,
              underline: Container(
                height: 2,
                color: Colors.blue,
              ),
              onChanged: (String? newValue) {
                setState(() {
                  _selectedDropdown = newValue!;
                });
              },
              items: _dropdownItems.map<DropdownMenuItem<String>>((String value) {
                return DropdownMenuItem<String>(
                  value: value,
                  child: Text(value),
                );
              }).toList(),
            ),
            const SizedBox(height: 20),
            
            // 3. 上下文菜单示例
            const Text('上下文菜单(长按或右键):', style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold)),
            ContextMenuArea(
              builder: (BuildContext context, Offset offset) {
                return [
                  PopupMenuItem<String>(
                    value: '复制',
                    onTap: () => setState(() => _contextMenuItem = '复制'),
                    child: const Text('复制'),
                  ),
                  PopupMenuItem<String>(
                    value: '粘贴',
                    onTap: () => setState(() => _contextMenuItem = '粘贴'),
                    child: const Text('粘贴'),
                  ),
                  PopupMenuItem<String>(
                    value: '剪切',
                    onTap: () => setState(() => _contextMenuItem = '剪切'),
                    child: const Text('剪切'),
                  ),
                ];
              },
              child: Container(
                padding: const EdgeInsets.all(16),
                height: 100,
                width: double.infinity,
                decoration: BoxDecoration(
                  border: Border.all(color: Colors.grey),
                  borderRadius: BorderRadius.circular(8),
                ),
                alignment: Alignment.center,
                child: Text('上下文菜单区域 - 长按或右键点击\n上次操作: $_contextMenuItem', 
                  textAlign: TextAlign.center,
                ),
              ),
            ),
            const SizedBox(height: 30),
            
            // 4. MenuBar示例 (Flutter 3.7+)
            const Text('MenuBar 示例 (Flutter 3.7+):', style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold)),
            const SizedBox(height: 10),
            MenuBar(
              children: [
                SubmenuButton(
                  menuChildren: [
                    MenuItemButton(
                      onPressed: () => setState(() => _selectedMenuItem = '新建文件'),
                      shortcut: const SingleActivator(LogicalKeyboardKey.keyN, control: true),
                      child: const Text('新建文件'),
                    ),
                    MenuItemButton(
                      onPressed: () => setState(() => _selectedMenuItem = '打开文件'),
                      shortcut: const SingleActivator(LogicalKeyboardKey.keyO, control: true),
                      child: const Text('打开文件'),
                    ),
                    const MenuDivider(),
                    SubmenuButton(
                      menuChildren: [
                        MenuItemButton(
                          onPressed: () => setState(() => _selectedMenuItem = '导出为PDF'),
                          child: const Text('PDF'),
                        ),
                        MenuItemButton(
                          onPressed: () => setState(() => _selectedMenuItem = '导出为图片'),
                          child: const Text('图片'),
                        ),
                      ],
                      child: const Text('导出为'),
                    ),
                    const MenuDivider(),
                    MenuItemButton(
                      onPressed: () => setState(() => _selectedMenuItem = '退出'),
                      shortcut: const SingleActivator(LogicalKeyboardKey.keyQ, control: true),
                      child: const Text('退出'),
                    ),
                  ],
                  child: const Text('文件'),
                ),
                SubmenuButton(
                  menuChildren: [
                    MenuItemButton(
                      onPressed: () => setState(() => _selectedMenuItem = '撤销'),
                      shortcut: const SingleActivator(LogicalKeyboardKey.keyZ, control: true),
                      child: const Text('撤销'),
                    ),
                    MenuItemButton(
                      onPressed: () => setState(() => _selectedMenuItem = '重做'),
                      shortcut: const SingleActivator(LogicalKeyboardKey.keyY, control: true),
                      child: const Text('重做'),
                    ),
                    const MenuDivider(),
                    MenuItemButton(
                      onPressed: () => setState(() => _selectedMenuItem = '剪切'),
                      shortcut: const SingleActivator(LogicalKeyboardKey.keyX, control: true),
                      child: const Text('剪切'),
                    ),
                    MenuItemButton(
                      onPressed: () => setState(() => _selectedMenuItem = '复制'),
                      shortcut: const SingleActivator(LogicalKeyboardKey.keyC, control: true),
                      child: const Text('复制'),
                    ),
                    MenuItemButton(
                      onPressed: () => setState(() => _selectedMenuItem = '粘贴'),
                      shortcut: const SingleActivator(LogicalKeyboardKey.keyV, control: true),
                      child: const Text('粘贴'),
                    ),
                  ],
                  child: const Text('编辑'),
                ),
                SubmenuButton(
                  menuChildren: [
                    MenuItemButton(
                      onPressed: () => setState(() => _selectedMenuItem = '关于'),
                      child: const Text('关于'),
                    ),
                    MenuItemButton(
                      onPressed: () => setState(() => _selectedMenuItem = '帮助'),
                      shortcut: const SingleActivator(LogicalKeyboardKey.f1),
                      child: const Text('帮助'),
                    ),
                  ],
                  child: const Text('帮助'),
                ),
              ],
            ),
            const SizedBox(height: 20),
            
            Center(
              child: Text('最后选择的操作: $_selectedMenuItem', 
                style: const TextStyle(fontSize: 18, fontWeight: FontWeight.bold)
              ),
            ),
          ],
        ),
      ),
    );
  }
}

## Android Jetpack Compose Menu

### 菜单项配置

Jetpack Compose提供了多种菜单组件，主要包括：

- `DropdownMenu`：基础下拉菜单组件，用于显示选项列表
- `ExposedDropdownMenuBox`：Material 3设计的可搜索下拉菜单
- `TopAppBar`中的菜单按钮，通常配合`DropdownMenu`使用
- `CascadeMenu`：Compose Material 3中的级联菜单组件（较新版本）

菜单项配置选项：

- 通过`DropdownMenuItem`定义菜单项
- 支持任意Compose组件作为菜单项内容
- 可以通过`enabled`属性设置禁用状态
- 支持内联函数定义点击行为
- 可以添加图标、文本、复选框等元素
- 支持自定义菜单项高度、内边距、颜色等样式
- 菜单布局完全可定制，可以使用行、列或其他布局

### 子菜单支持

- 原生`DropdownMenu`不直接支持嵌套子菜单
- Material 3引入的`CascadeMenu`支持子菜单功能
- 可以通过状态管理自行实现子菜单逻辑
- 子菜单可以使用动画过渡效果
- 可以设置子菜单的显示位置和偏移量
- 子菜单跟随父级菜单的显示和隐藏状态
- 通过嵌套的状态管理可以实现多级菜单

### 上下文菜单

- 使用`DropdownMenu`结合`detectTapGestures`实现长按上下文菜单
- 通过`rememberDismissState`管理菜单的显示和隐藏
- 支持捕获长按的精确位置，在该位置显示菜单
- 可以利用`PopupProperties`控制菜单显示行为
- 支持自定义菜单出现和消失的动画效果
- 可以实现弹性行为（如滑动或点击外部关闭）

### 快捷键绑定

- 结合Compose的`Shortcut`API实现菜单快捷键
- 支持使用`KeyEvent`直接响应键盘事件
- 可以使用`ShortcutManager`管理全局快捷键
- 菜单项可以显示对应的快捷键提示
- 支持特定于平台的键盘修饰键（Ctrl/Cmd/Alt/Shift）
- 可以通过组合键实现复杂的快捷操作
- 支持自定义快捷键文本显示格式

### 可访问性支持

- 提供`semantics`修饰符为菜单项添加语义信息
- 支持`contentDescription`属性为屏幕阅读器提供描述
- 完全支持TalkBack无障碍服务
- 支持通过键盘导航和操作菜单项
- 可以设置合适的点击目标大小以符合可访问性要求
- 支持高对比度、大字体等辅助功能
- 符合Material Design的可访问性指南

### 平台特性与兼容性

- 专为Android平台优化，默认遵循Material Design 3规范
- 支持Android 5.0（API 21）及以上版本，最佳体验在Android 12+
- 可以根据Android版本自动调整菜单样式
- 与Android系统交互（如系统返回键）无缝集成
- 支持不同屏幕密度和尺寸适配
- 支持深色模式和动态颜色（Material You）
- `CascadeMenu`等新特性需要较新版本的Compose库
- 可以在Compose Multiplatform中用于跨平台开发

### 完整示例代码

```kotlin
import androidx.compose.foundation.ExperimentalFoundationApi
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.combinedClickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.geometry.Offset
import androidx.compose.ui.input.key.*
import androidx.compose.ui.input.pointer.pointerInput
import androidx.compose.ui.platform.LocalDensity
import androidx.compose.ui.platform.LocalFocusManager
import androidx.compose.ui.semantics.contentDescription
import androidx.compose.ui.semantics.semantics
import androidx.compose.ui.text.style.TextAlign
import androidx.compose.ui.unit.DpOffset
import androidx.compose.ui.unit.dp
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.ui.platform.LocalContext
import android.widget.Toast

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MaterialTheme {
                MenuDemoScreen()
            }
        }
    }
}

@OptIn(ExperimentalMaterial3Api::class, ExperimentalFoundationApi::class)
@Composable
fun MenuDemoScreen() {
    val selectedAction = remember { mutableStateOf("无") }
    val contextMenuAction = remember { mutableStateOf("无") }
    val dropdownExpanded = remember { mutableStateOf(false) }
    var dropdownSelectedItem by remember { mutableStateOf("选项1") }
    val dropdownItems = listOf("选项1", "选项2", "选项3", "选项4")
    val context = LocalContext.current
    
    var contextMenuOffset by remember { mutableStateOf(Offset.Zero) }
    var showContextMenu by remember { mutableStateOf(false) }
    
    // 复选框状态
    var checkboxSelected by remember { mutableStateOf(false) }
    
    // 级联菜单状态
    var showCascadeMenu by remember { mutableStateOf(false) }
    var cascadeMenuPosition by remember { mutableStateOf(DpOffset.Zero) }
    
    // 标志按钮菜单状态
    var showActionMenu by remember { mutableStateOf(false) }
    
    Scaffold(
        topBar = {
            CenterAlignedTopAppBar(
                title = { Text("Jetpack Compose Menu 示例") },
                actions = {
                    // 菜单按钮
                    IconButton(onClick = { showActionMenu = true }) {
                        Icon(Icons.Default.MoreVert, contentDescription = "更多选项")
                    }
                    
                    // 下拉菜单
                    DropdownMenu(
                        expanded = showActionMenu,
                        onDismissRequest = { showActionMenu = false },
                        modifier = Modifier.width(200.dp)
                    ) {
                        DropdownMenuItem(
                            text = { Text("设置") },
                            leadingIcon = { Icon(Icons.Default.Settings, null) },
                            onClick = {
                                selectedAction.value = "设置"
                                showActionMenu = false
                            }
                        )
                        
                        DropdownMenuItem(
                            text = { Text("个人资料") },
                            leadingIcon = { Icon(Icons.Default.Person, null) },
                            onClick = {
                                selectedAction.value = "个人资料"
                                showActionMenu = false
                            }
                        )
                        
                        Divider()
                        
                        DropdownMenuItem(
                            text = { Text("复选框选项") },
                            leadingIcon = {
                                Checkbox(
                                    checked = checkboxSelected,
                                    onCheckedChange = null
                                )
                            },
                            onClick = {
                                checkboxSelected = !checkboxSelected
                                selectedAction.value = "复选框: \(action.state == .on ? "选中" : "未选中")"
                            }
                        )
                        
                        DropdownMenuItem(
                            text = { Text("禁用项") },
                            leadingIcon = { Icon(Icons.Default.Block, null) },
                            enabled = false,
                            onClick = {}
                        )
                    }
                }
            )
        }
    ) { paddingValues ->
        LazyColumn(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues)
                .padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(16.dp)
        ) {
            // 1. 基本信息
            item {
                Text("选择的菜单项: ${selectedAction.value}", style = MaterialTheme.typography.bodyLarge)
                Spacer(modifier = Modifier.height(20.dp))
            }
            
            // 2. ExposedDropdownMenuBox示例
            item {
                Text(
                    "ExposedDropdownMenuBox (下拉菜单):",
                    style = MaterialTheme.typography.titleMedium
                )
                Spacer(modifier = Modifier.height(8.dp))
                
                ExposedDropdownMenuBox(
                    expanded = dropdownExpanded.value,
                    onExpandedChange = { dropdownExpanded.value = !dropdownExpanded.value }
                ) {
                    OutlinedTextField(
                        value = dropdownSelectedItem,
                        onValueChange = {},
                        readOnly = true,
                        trailingIcon = {
                            ExposedDropdownMenuDefaults.TrailingIcon(expanded = dropdownExpanded.value)
                        },
                        modifier = Modifier
                            .menuAnchor()
                            .fillMaxWidth()
                    )
                    
                    ExposedDropdownMenu(
                        expanded = dropdownExpanded.value,
                        onDismissRequest = { dropdownExpanded.value = false }
                    ) {
                        dropdownItems.forEach { item ->
                            DropdownMenuItem(
                                text = { Text(item) },
                                onClick = {
                                    dropdownSelectedItem = item
                                    dropdownExpanded.value = false
                                    selectedAction.value = "下拉菜单: $item"
                                }
                            )
                        }
                    }
                }
                
                Spacer(modifier = Modifier.height(20.dp))
            }
            
            // 3. 上下文菜单示例
            item {
                Text(
                    "上下文菜单(长按区域):",
                    style = MaterialTheme.typography.titleMedium
                )
                Spacer(modifier = Modifier.height(8.dp))
                
                Box(
                    modifier = Modifier
                        .fillMaxWidth()
                        .height(100.dp)
                        .border(1.dp, MaterialTheme.colorScheme.outline, shape = MaterialTheme.shapes.small)
                        .combinedClickable(
                            onClick = {},
                            onLongClick = {
                                showContextMenu = true
                            }
                        )
                        .padding(16.dp),
                    contentAlignment = Alignment.Center
                ) {
                    Text(
                        "上下文菜单区域 - 长按此处\n上次操作: ${contextMenuAction.value}",
                        textAlign = TextAlign.Center
                    )
                    
                    DropdownMenu(
                        expanded = showContextMenu,
                        onDismissRequest = { showContextMenu = false },
                    ) {
                        DropdownMenuItem(
                            text = { Text("复制") },
                            leadingIcon = { Icon(Icons.Default.ContentCopy, null) },
                            onClick = {
                                contextMenuAction.value = "复制"
                                showContextMenu = false
                            }
                        )
                        DropdownMenuItem(
                            text = { Text("粘贴") },
                            leadingIcon = { Icon(Icons.Default.ContentPaste, null) },
                            onClick = {
                                contextMenuAction.value = "粘贴"
                                showContextMenu = false
                            }
                        )
                        DropdownMenuItem(
                            text = { Text("剪切") },
                            leadingIcon = { Icon(Icons.Default.ContentCut, null) },
                            onClick = {
                                contextMenuAction.value = "剪切"
                                showContextMenu = false
                            }
                        )
                    }
                }
                
                Spacer(modifier = Modifier.height(30.dp))
            }
            
            // 4. 级联菜单示例
            item {
                Text(
                    "级联菜单示例:",
                    style = MaterialTheme.typography.titleMedium
                )
                Spacer(modifier = Modifier.height(8.dp))
                
                Row(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalArrangement = Arrangement.spacedBy(8.dp)
                ) {
                    listOf("文件", "编辑", "视图", "帮助").forEach { menuTitle ->
                        Button(
                            onClick = {
                                showCascadeMenu = true
                                selectedAction.value = "打开$menuTitle菜单"
                            },
                            modifier = Modifier.semantics {
                                contentDescription = "$menuTitle菜单按钮"
                            }
                        ) {
                            Text(menuTitle)
                        }
                    }
                }
                
                // 级联菜单演示示例 (使用DropdownMenu模拟)
                val focusManager = LocalFocusManager.current
                
                if (showCascadeMenu) {
                    AlertDialog(
                        onDismissRequest = { showCascadeMenu = false },
                        title = { Text("菜单功能演示") },
                        text = {
                            Column {
                                Text("在实际应用中，这里会显示级联菜单。Material 3的CascadeMenu组件可提供此功能。")
                                Text("支持的快捷键:")
                                Text("• 新建 - Ctrl+N")
                                Text("• 打开 - Ctrl+O")
                                Text("• 保存 - Ctrl+S")
                                Text("• 撤销 - Ctrl+Z")
                                Text("• 重做 - Ctrl+Y")
                                
                                Spacer(modifier = Modifier.height(8.dp))
                                
                                // 快捷键处理模拟
                                Text(
                                    "按下Ctrl+S试试看",
                                    modifier = Modifier
                                        .background(MaterialTheme.colorScheme.secondaryContainer)
                                        .padding(8.dp)
                                        .fillMaxWidth()
                                        .focusable()
                                        .onKeyEvent { keyEvent ->
                                            if (keyEvent.type == KeyEventType.KeyDown && 
                                                keyEvent.key == Key.S &&
                                                keyEvent.isCtrlPressed) {
                                                selectedAction.value = "保存(Ctrl+S)"
                                                showCascadeMenu = false
                                                Toast.makeText(context, "文件已保存", Toast.LENGTH_SHORT).show()
                                                true
                                            } else {
                                                false
                                            }
                                        }
                                )
                            }
                        },
                        confirmButton = {
                            Button(onClick = { showCascadeMenu = false }) {
                                Text("关闭")
                            }
                        }
                    )
                }
                
                Spacer(modifier = Modifier.height(20.dp))
            }
            
            // 5. 最后选择的操作
            item {
                Text(
                    "最后选择的操作: ${selectedAction.value}",
                    style = MaterialTheme.typography.headlineSmall,
                    modifier = Modifier
                        .fillMaxWidth()
                        .padding(vertical = 16.dp),
                    textAlign = TextAlign.Center
                )
            }
        }
    }
}

## iOS UIKit Menu

### 菜单项配置

UIKit提供了多种菜单相关组件，主要包括：

- `UIMenu`：iOS 13及以上版本的现代菜单系统，高度可定制
- `UIMenuController`：老式的菜单控制器，支持简单的文本操作菜单
- `UIContextMenuInteraction`：上下文菜单交互控制器
- `UIButton`的菜单配置：iOS 14+支持按钮直接显示菜单

菜单项配置选项：

- 通过`UIAction`创建菜单项，可定义标题、图标和处理器
- 支持设置`UIAction.State`管理选中状态
- 通过`UIAction.Attributes`设置菜单项属性（如禁用、隐藏、毁灭性操作等）
- 支持为菜单项添加SF Symbols图标
- 可以使用分隔符分组菜单项
- 支持设置菜单显示选项（如单选/多选）
- 菜单项可以包含详细子标题

### 子菜单支持

- `UIMenu`天然支持嵌套子菜单
- 可以通过`UIMenu.children`属性添加子菜单项
- 支持多级嵌套，无明确层级限制
- 子菜单可以显示不同的图标和样式
- 支持为子菜单设置不同的展示选项
- 子菜单可以有自己的标题和描述
- iOS 15+支持根据状态动态更新子菜单内容

### 上下文菜单

- `UIContextMenuInteraction`专门用于实现长按上下文菜单
- 通过`UIContextMenuConfiguration`配置菜单内容和预览
- 支持为不同视图元素配置不同的上下文菜单
- 可以在上下文菜单中显示丰富的预览视图
- 支持上下文菜单的动画过渡
- 菜单可以响应用户交互动态更新内容
- 支持嵌套上下文菜单交互

### 快捷键绑定

- iOS 13+支持通过`UIKeyCommand`配置快捷键
- 可以为`UIAction`设置`keyEquivalent`和`keyModifiers`
- 支持标准键盘快捷键，如Cmd+C、Cmd+V等
- iPad上支持物理键盘的所有快捷键
- 快捷键会自动显示在菜单项旁边
- 可以通过`UIResponder`链传递键盘命令
- 支持自定义键盘修饰符组合

### 可访问性支持

- 完全支持VoiceOver屏幕阅读器
- 通过`accessibilityLabel`和`accessibilityHint`提供额外信息
- 支持动态类型适配系统字体大小
- 菜单项有足够大的点击区域
- 支持可访问性操作的替代方式
- 色彩对比度符合可访问性标准
- 支持通过Siri访问菜单功能
- 兼容辅助触控和开关控制等辅助功能

### 平台特性与兼容性

- 专为iOS/iPadOS设计，完美遵循Apple设计规范
- 现代菜单系统需要iOS 13+（UIMenu）
- 完整功能支持需要iOS 14+或iOS 15+
- 在iPad上有特殊的交互模式（如悬停预览）
- 支持深色模式和动态外观切换
- 与系统操作（如长按、3D Touch）无缝集成
- 在Mac Catalyst应用中自动适应macOS菜单风格
- 可以响应iOS系统设置（如减弱动画）

### 完整示例代码

```swift
import UIKit

class MenuDemoViewController: UIViewController {
    
    // MARK: - Properties
    
    private let menuButton = UIButton(type: .system)
    private let dropdownMenu = UIButton(type: .system)
    private let contextMenuArea = UIView()
    private let cascadeMenuContainer = UIStackView()
    private let resultLabel = UILabel()
    
    private var selectedAction: String = "无" {
        didSet {
            resultLabel.text = "最后选择的操作: \(selectedAction)"
        }
    }
    
    private var contextMenuAction: String = "无" {
        didSet {
            updateContextMenuLabel()
        }
    }
    
    private let contextMenuLabel = UILabel()
    private let dropdownOptions = ["选项1", "选项2", "选项3", "选项4"]
    private var currentDropdownSelection = "选项1"
    
    // MARK: - Lifecycle Methods
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        view.backgroundColor = .systemBackground
        title = "UIKit Menu 示例"
        
        setupMenuButton()
        setupUI()
        configureDropdownMenu()
        configureContextMenuArea()
        configureCascadeMenu()
    }
    
    // MARK: - Setup Methods
    
    private func setupMenuButton() {
        // 导航栏右侧的菜单按钮
        let moreButton = UIBarButtonItem(image: UIImage(systemName: "ellipsis.circle"), 
                                         style: .plain, 
                                         target: nil, 
                                         action: nil)
        
        // 创建一个菜单
        if #available(iOS 14.0, *) {
            
            // 创建子菜单
            let exportSubmenu = UIMenu(title: "导出为", 
                                       image: UIImage(systemName: "square.and.arrow.up"),
                                       children: [
                UIAction(title: "PDF", image: UIImage(systemName: "doc.fill")) { [weak self] _ in
                    self?.selectedAction = "导出为PDF"
                },
                UIAction(title: "图片", image: UIImage(systemName: "photo")) { [weak self] _ in
                    self?.selectedAction = "导出为图片"
                }
            ])
            
            // 创建带有子菜单的主菜单
            let menu = UIMenu(title: "", children: [
                UIAction(title: "设置", image: UIImage(systemName: "gear")) { [weak self] _ in
                    self?.selectedAction = "设置"
                },
                UIAction(title: "个人资料", image: UIImage(systemName: "person.fill")) { [weak self] _ in
                    self?.selectedAction = "个人资料"
                },
                UIMenu(title: "", options: .displayInline, children: [
                    UIAction(title: "复选框选项", image: UIImage(systemName: "checkmark.square"), 
                             state: .off) { [weak self] action in
                        // 切换选中状态
                        action.state = action.state == .on ? .off : .on
                        self?.selectedAction = "复选框: \(action.state == .on ? "选中" : "未选中")"
                    }
                ]),
                UIMenu(title: "", options: .displayInline, children: [
                    exportSubmenu
                ]),
                UIAction(title: "禁用项", image: UIImage(systemName: "xmark.circle"), 
                         attributes: .disabled) { _ in }
            ])
            
            moreButton.menu = menu
        }
        
        navigationItem.rightBarButtonItem = moreButton
    }
    
    private func setupUI() {
        // 创建垂直堆栈视图用于内容布局
        let stackView = UIStackView()
        stackView.axis = .vertical
        stackView.spacing = 20
        stackView.alignment = .fill
        stackView.distribution = .fill
        stackView.translatesAutoresizingMaskIntoConstraints = false
        
        view.addSubview(stackView)
        
        // 设置约束
        NSLayoutConstraint.activate([
            stackView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor, constant: 20),
            stackView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
            stackView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
        ])
        
        // 添加标签显示当前选择
        let selectedLabel = UILabel()
        selectedLabel.text = "选择的菜单项: \(selectedAction)"
        selectedLabel.font = .systemFont(ofSize: 16)
        stackView.addArrangedSubview(selectedLabel)
        
        // 添加下拉菜单标题
        let dropdownTitle = UILabel()
        dropdownTitle.text = "UIMenu下拉菜单:"
        dropdownTitle.font = .boldSystemFont(ofSize: 16)
        stackView.addArrangedSubview(dropdownTitle)
        
        // 添加下拉菜单按钮
        dropdownMenu.setTitle(currentDropdownSelection, for: .normal)
        dropdownMenu.titleLabel?.font = .systemFont(ofSize: 16)
        dropdownMenu.contentHorizontalAlignment = .left
        dropdownMenu.backgroundColor = .systemGray6
        dropdownMenu.layer.cornerRadius = 5
        dropdownMenu.contentEdgeInsets = UIEdgeInsets(top: 10, left: 15, bottom: 10, right: 15)
        dropdownMenu.translatesAutoresizingMaskIntoConstraints = false
        stackView.addArrangedSubview(dropdownMenu)
        
        NSLayoutConstraint.activate([
            dropdownMenu.heightAnchor.constraint(equalToConstant: 44)
        ])
        
        // 添加上下文菜单区域标题
        let contextMenuTitle = UILabel()
        contextMenuTitle.text = "上下文菜单(长按区域):"
        contextMenuTitle.font = .boldSystemFont(ofSize: 16)
        stackView.addArrangedSubview(contextMenuTitle)
        
        // 添加上下文菜单区域
        contextMenuArea.backgroundColor = .systemGray6
        contextMenuArea.layer.cornerRadius = 8
        contextMenuArea.layer.borderWidth = 1
        contextMenuArea.layer.borderColor = UIColor.systemGray4.cgColor
        contextMenuArea.translatesAutoresizingMaskIntoConstraints = false
        stackView.addArrangedSubview(contextMenuArea)
        
        NSLayoutConstraint.activate([
            contextMenuArea.heightAnchor.constraint(equalToConstant: 100)
        ])
        
        // 添加上下文菜单标签
        contextMenuLabel.text = "上下文菜单区域 - 长按此处\n上次操作: \(contextMenuAction)"
        contextMenuLabel.textAlignment = .center
        contextMenuLabel.numberOfLines = 0
        contextMenuLabel.translatesAutoresizingMaskIntoConstraints = false
        contextMenuArea.addSubview(contextMenuLabel)
        
        NSLayoutConstraint.activate([
            contextMenuLabel.centerXAnchor.constraint(equalTo: contextMenuArea.centerXAnchor),
            contextMenuLabel.centerYAnchor.constraint(equalTo: contextMenuArea.centerYAnchor)
        ])
        
        // 添加级联菜单标题
        let cascadeMenuTitle = UILabel()
        cascadeMenuTitle.text = "级联菜单示例:"
        cascadeMenuTitle.font = .boldSystemFont(ofSize: 16)
        stackView.addArrangedSubview(cascadeMenuTitle)
        
        // 创建级联菜单容器
        cascadeMenuContainer.axis = .horizontal
        cascadeMenuContainer.spacing = 10
        cascadeMenuContainer.distribution = .fillEqually
        cascadeMenuContainer.translatesAutoresizingMaskIntoConstraints = false
        stackView.addArrangedSubview(cascadeMenuContainer)
        
        // 添加结果标签
        resultLabel.text = "最后选择的操作: \(selectedAction)"
        resultLabel.textAlignment = .center
        resultLabel.font = .boldSystemFont(ofSize: 18)
        resultLabel.translatesAutoresizingMaskIntoConstraints = false
        stackView.addArrangedSubview(resultLabel)
        
        // 在顶层堆栈和结果标签之间添加填充视图
        let fillerView = UIView()
        fillerView.translatesAutoresizingMaskIntoConstraints = false
        stackView.addArrangedSubview(fillerView)
        
        // 确保结果标签足够靠下
        NSLayoutConstraint.activate([
            fillerView.heightAnchor.constraint(greaterThanOrEqualToConstant: 100)
        ])
    }
    
    private func configureDropdownMenu() {
        // iOS 14+ UIMenu 下拉菜单
        if #available(iOS 14.0, *) {
            var menuActions = [UIMenuElement]()
            
            for option in dropdownOptions {
                let action = UIAction(title: option, state: option == currentDropdownSelection ? .on : .off) { [weak self] action in
                    guard let self = self else { return }
                    self.currentDropdownSelection = option
                    self.dropdownMenu.setTitle(option, for: .normal)
                    self.selectedAction = "下拉菜单: \(option)"
                    
                    // 重新配置菜单以更新勾选状态
                    self.configureDropdownMenu()
                }
                menuActions.append(action)
            }
            
            dropdownMenu.menu = UIMenu(title: "", children: menuActions)
            dropdownMenu.showsMenuAsPrimaryAction = true
        } else {
            // iOS 13之前的替代方法
            dropdownMenu.addTarget(self, action: #selector(handleLegacyDropdown), for: .touchUpInside)
        }
    }
    
    @objc private func handleLegacyDropdown() {
        // iOS 13之前的替代方法 - 通过UIAlertController模拟下拉菜单
        let alertController = UIAlertController(title: "选择选项", message: nil, preferredStyle: .actionSheet)
        
        for option in dropdownOptions {
            let action = UIAlertAction(title: option, style: .default) { [weak self] _ in
                self?.currentDropdownSelection = option
                self?.dropdownMenu.setTitle(option, for: .normal)
                self?.selectedAction = "下拉菜单: \(option)"
            }
            // 添加勾选标记
            if option == currentDropdownSelection {
                action.setValue(true, forKey: "checked")
            }
            alertController.addAction(action)
        }
        
        alertController.addAction(UIAlertAction(title: "取消", style: .cancel, handler: nil))
        
        present(alertController, animated: true)
    }
    
    private func configureContextMenuArea() {
        // 配置上下文菜单交互
        if #available(iOS 13.0, *) {
            let interaction = UIContextMenuInteraction(delegate: self)
            contextMenuArea.addInteraction(interaction)
            contextMenuArea.isUserInteractionEnabled = true
        } else {
            // iOS 13以前的长按手势
            let longPressGesture = UILongPressGestureRecognizer(target: self, action: #selector(handleLegacyLongPress))
            contextMenuArea.addGestureRecognizer(longPressGesture)
        }
    }
    
    @objc private func handleLegacyLongPress(_ gesture: UILongPressGestureRecognizer) {
        if gesture.state == .began {
            // 使用老式的UIMenuController
            let menuController = UIMenuController.shared
            
            // 创建菜单项
            let copyItem = UIMenuItem(title: "复制", action: #selector(handleCopy))
            let pasteItem = UIMenuItem(title: "粘贴", action: #selector(handlePaste))
            let cutItem = UIMenuItem(title: "剪切", action: #selector(handleCut))
            
            menuController.menuItems = [copyItem, pasteItem, cutItem]
            
            // 设置菜单位置
            if #available(iOS 13.0, *) {
                menuController.showMenu(from: contextMenuArea, rect: contextMenuArea.bounds)
            } else {
                menuController.setTargetRect(contextMenuArea.bounds, in: contextMenuArea)
                menuController.setMenuVisible(true, animated: true)
            }
        }
    }
    
    @objc private func handleCopy() {
        contextMenuAction = "复制"
    }
    
    @objc private func handlePaste() {
        contextMenuAction = "粘贴"
    }
    
    @objc private func handleCut() {
        contextMenuAction = "剪切"
    }
    
    private func updateContextMenuLabel() {
        contextMenuLabel.text = "上下文菜单区域 - 长按此处\n上次操作: \(contextMenuAction)"
    }
    
    private func configureCascadeMenu() {
        if #available(iOS 14.0, *) {
            // 创建几个示例菜单按钮
            let menuTitles = ["文件", "编辑", "视图", "帮助"]
            
            for title in menuTitles {
                let button = UIButton(type: .system)
                button.setTitle(title, for: .normal)
                button.titleLabel?.font = .systemFont(ofSize: 16)
                button.backgroundColor = .systemBlue
                button.setTitleColor(.white, for: .normal)
                button.layer.cornerRadius = 5
                button.translatesAutoresizingMaskIntoConstraints = false
                
                // 配置菜单内容
                switch title {
                case "文件":
                    // 创建文件菜单
                    let newAction = UIAction(title: "新建", image: UIImage(systemName: "doc.badge.plus")) { [weak self] _ in
                        self?.selectedAction = "新建文件"
                    }
                    
                    let openAction = UIAction(title: "打开...", image: UIImage(systemName: "folder")) { [weak self] _ in
                        self?.selectedAction = "打开文件"
                    }
                    
                    let saveAction = UIAction(title: "保存", image: UIImage(systemName: "square.and.arrow.down")) { [weak self] _ in
                        self?.selectedAction = "保存文件"
                    }
                    
                    // 添加键盘快捷键
                    if #available(iOS 15.0, *) {
                        newAction.keyEquivalent = "n"
                        newAction.keyEquivalentModifierFlags = .command
                        
                        openAction.keyEquivalent = "o"
                        openAction.keyEquivalentModifierFlags = .command
                        
                        saveAction.keyEquivalent = "s"
                        saveAction.keyEquivalentModifierFlags = .command
                    }
                    
                    // 创建导出子菜单
                    let exportSubmenu = UIMenu(title: "导出为", image: UIImage(systemName: "arrow.up.doc"), children: [
                        UIAction(title: "PDF", image: UIImage(systemName: "doc.fill")) { [weak self] _ in
                            self?.selectedAction = "导出为PDF"
                        },
                        UIAction(title: "图片", image: UIImage(systemName: "photo")) { [weak self] _ in
                            self?.selectedAction = "导出为图片"
                        }
                    ])
                    
                    // 创建完整菜单
                    button.menu = UIMenu(title: "", children: [
                        newAction,
                        openAction,
                        saveAction,
                        UIMenu(title: "", options: .displayInline, children: [exportSubmenu]),
                        UIAction(title: "退出", image: UIImage(systemName: "escape"), attributes: .destructive) { [weak self] _ in
                            self?.selectedAction = "退出"
                        }
                    ])
                    button.showsMenuAsPrimaryAction = true
                    
                case "编辑":
                    // 创建编辑菜单
                    let undoAction = UIAction(title: "撤销", image: UIImage(systemName: "arrow.uturn.backward")) { [weak self] _ in
                        self?.selectedAction = "撤销"
                    }
                    
                    let redoAction = UIAction(title: "重做", image: UIImage(systemName: "arrow.uturn.forward")) { [weak self] _ in
                        self?.selectedAction = "重做"
                    }
                    
                    // 添加快捷键
                    if #available(iOS 15.0, *) {
                        undoAction.keyEquivalent = "z"
                        undoAction.keyEquivalentModifierFlags = .command
                        
                        redoAction.keyEquivalent = "z"
                        redoAction.keyEquivalentModifierFlags = [.command, .shift]
                    }
                    
                    // 创建编辑菜单
                    button.menu = UIMenu(title: "", children: [
                        undoAction,
                        redoAction,
                        UIMenu(title: "", options: .displayInline, children: [
                            UIAction(title: "剪切", image: UIImage(systemName: "scissors")) { [weak self] _ in
                                self?.selectedAction = "剪切"
                            },
                            UIAction(title: "复制", image: UIImage(systemName: "doc.on.doc")) { [weak self] _ in
                                self?.selectedAction = "复制"
                            },
                            UIAction(title: "粘贴", image: UIImage(systemName: "doc.on.clipboard")) { [weak self] _ in
                                self?.selectedAction = "粘贴"
                            }
                        ])
                    ])
                    button.showsMenuAsPrimaryAction = true
                    
                case "视图":
                    button.menu = UIMenu(title: "", children: [
                        UIAction(title: "放大", image: UIImage(systemName: "plus.magnifyingglass")) { [weak self] _ in
                            self?.selectedAction = "放大"
                        },
                        UIAction(title: "缩小", image: UIImage(systemName: "minus.magnifyingglass")) { [weak self] _ in
                            self?.selectedAction = "缩小"
                        },
                        UIAction(title: "全屏", image: UIImage(systemName: "arrow.up.left.and.arrow.down.right")) { [weak self] _ in
                            self?.selectedAction = "全屏"
                        }
                    ])
                    button.showsMenuAsPrimaryAction = true
                    
                case "帮助":
                    button.menu = UIMenu(title: "", children: [
                        UIAction(title: "帮助文档", image: UIImage(systemName: "book")) { [weak self] _ in
                            self?.selectedAction = "帮助文档"
                        },
                        UIAction(title: "关于", image: UIImage(systemName: "info.circle")) { [weak self] _ in
                            self?.selectedAction = "关于"
                        }
                    ])
                    button.showsMenuAsPrimaryAction = true
                    
                default:
                    break
                }
                
                cascadeMenuContainer.addArrangedSubview(button)
                
                // 设置按钮高度
                NSLayoutConstraint.activate([
                    button.heightAnchor.constraint(equalToConstant: 40)
                ])
            }
        } else {
            // iOS 13以前的版本显示提示
            let label = UILabel()
            label.text = "级联菜单需要iOS 14及以上版本"
            label.textAlignment = .center
            label.textColor = .systemGray
            cascadeMenuContainer.addArrangedSubview(label)
        }
    }
}

// MARK: - UIContextMenuInteractionDelegate
extension MenuDemoViewController: UIContextMenuInteractionDelegate {
    @available(iOS 13.0, *)
    func contextMenuInteraction(_ interaction: UIContextMenuInteraction, configurationForMenuAtLocation location: CGPoint) -> UIContextMenuConfiguration? {
        return UIContextMenuConfiguration(identifier: nil, previewProvider: nil) { [weak self] suggestedActions in
            guard let self = self else { return nil }
            
            // 创建操作
            let copy = UIAction(title: "复制", image: UIImage(systemName: "doc.on.doc")) { _ in
                self.contextMenuAction = "复制"
            }
            
            let paste = UIAction(title: "粘贴", image: UIImage(systemName: "doc.on.clipboard")) { _ in
                self.contextMenuAction = "粘贴"
            }
            
            let cut = UIAction(title: "剪切", image: UIImage(systemName: "scissors")) { _ in
                self.contextMenuAction = "剪切"
            }
            
            // 创建并返回菜单
            return UIMenu(title: "", children: [copy, paste, cut])
        }
    }
}

## iOS SwiftUI Menu

### 菜单项配置

SwiftUI提供了简洁而强大的菜单组件体系：

- `Menu`：主要菜单组件，可以附加到任何视图
- `ContextMenu`：上下文菜单修饰符，提供长按菜单
- `Picker`：带有菜单样式的选择器
- `CommandMenu`和`CommandGroup`：用于macOS应用的菜单栏

菜单项配置选项：

- 完全声明式API，简洁直观
- 支持任何SwiftUI视图作为菜单项
- 通过`Button`、`Toggle`等组件创建交互式菜单项
- 支持图标、文本和自定义布局
- 可以使用`disabled(_:)`动态控制菜单项状态
- 菜单项样式自动适配当前平台
- 通过`Label`组件实现图标+文本的标准菜单项

### 子菜单支持

- 原生支持无限层级嵌套子菜单
- 只需在`Menu`内部放置另一个`Menu`即可创建子菜单
- 子菜单自动继承父级菜单的样式和行为
- 子菜单可以使用不同的图标和标题
- 支持动态生成的子菜单内容
- 可以结合`ForEach`创建动态子菜单
- 子菜单的展开/折叠状态自动管理

### 上下文菜单

- 通过`.contextMenu { }`修饰符轻松实现长按菜单
- 可以为任何视图添加上下文菜单
- 上下文菜单可以包含任何SwiftUI视图
- 支持预览内容（iOS 14+）
- 完全遵循平台设计规范，显示效果与原生一致
- 可以根据程序状态动态更新菜单内容
- 提供优雅的触觉反馈和动画效果

### 快捷键绑定

- 通过`.keyboardShortcut(_:modifiers:)`为菜单项添加快捷键
- 支持所有标准键盘修饰符（cmd、shift、opt、ctrl等）
- 与系统级快捷键处理集成
- 快捷键会显示在菜单项旁边
- 可以设置自定义修饰符组合
- 支持跨平台的快捷键定义
- 支持复杂的条件键盘快捷键

### 可访问性支持

- 完全支持VoiceOver和所有系统辅助功能
- 自动适配动态类型和用户首选字体大小
- 支持通过`.accessibilityLabel(_:)`提供额外描述
- 可通过`.accessibilityHint(_:)`提供使用提示
- 完全支持键盘导航
- 颜色对比度符合可访问性标准
- 支持语音控制和开关控制等辅助功能

### 平台特性与兼容性

- 完美适配iOS、iPadOS、macOS、watchOS和tvOS
- 在macOS上自动转换为原生菜单栏样式
- 基本支持需要iOS 13+/macOS 10.15+
- 高级功能需要iOS 14+/macOS 11+
- 与系统外观和动态深色模式无缝集成
- 在Mac Catalyst应用中保持高度一致性
- 适配所有尺寸类和设备方向
- 支持所有Apple生态系统的独特交互模式

### 完整示例代码

```swift
import SwiftUI

struct MenuDemoView: View {
    @State private var selectedAction: String = "无"
    @State private var contextMenuAction: String = "无"
    @State private var selectedDropdownOption: String = "选项1"
    @State private var isCheckboxSelected: Bool = false
    
    private let dropdownOptions = ["选项1", "选项2", "选项3", "选项4"]
    
    var body: some View {
        NavigationView {
            ScrollView {
                VStack(alignment: .leading, spacing: 20) {
                    // 显示选择的结果
                    Text("选择的菜单项: \(selectedAction)")
                        .font(.body)
                        .padding(.top)
                    
                    // 1. 基本下拉菜单
                    MenuSectionHeader("1. 下拉菜单")
                    
                    Menu {
                        ForEach(dropdownOptions, id: \.self) { option in
                            Button {
                                selectedDropdownOption = option
                                selectedAction = "下拉菜单: \(option)"
                            } label: {
                                if selectedDropdownOption == option {
                                    Label(option, systemImage: "checkmark")
                                } else {
                                    Text(option)
                                }
                            }
                        }
                        
                        Divider()
                        
                        Toggle(isOn: $isCheckboxSelected) {
                            Label("复选框选项", systemImage: isCheckboxSelected ? "checkmark.square.fill" : "square")
                                .onChange(of: isCheckboxSelected) { newValue in
                                    selectedAction = "复选框: \(newValue ? "选中" : "未选中")"
                                }
                        }
                        
                        Button {
                            // 禁用按钮，什么都不做
                        } label: {
                            Label("禁用选项", systemImage: "xmark.circle")
                        }
                        .disabled(true)
                    } label: {
                        HStack {
                            Text(selectedDropdownOption)
                            Spacer()
                            Image(systemName: "chevron.down")
                                .font(.caption)
                        }
                        .padding()
                        .frame(maxWidth: .infinity, alignment: .leading)
                        .background(Color(.systemGray6))
                        .cornerRadius(8)
                    }
                    
                    // 2. 上下文菜单
                    MenuSectionHeader("2. 上下文菜单(长按区域)")
                    
                    Text("上下文菜单区域 - 长按此处\n上次操作: \(contextMenuAction)")
                        .frame(maxWidth: .infinity, minHeight: 100)
                        .multilineTextAlignment(.center)
                        .padding()
                        .background(Color(.systemGray6))
                        .cornerRadius(8)
                        .contextMenu {
                            Button {
                                contextMenuAction = "复制"
                            } label: {
                                Label("复制", systemImage: "doc.on.doc")
                            }
                            
                            Button {
                                contextMenuAction = "粘贴"
                            } label: {
                                Label("粘贴", systemImage: "doc.on.clipboard")
                            }
                            
                            Button {
                                contextMenuAction = "剪切"
                            } label: {
                                Label("剪切", systemImage: "scissors")
                            }
                        }
                    
                    // 3. 级联菜单
                    MenuSectionHeader("3. 级联菜单示例")
                    
                    HStack(spacing: 12) {
                        // 文件菜单
                        FileMenu(selectedAction: $selectedAction)
                            .frame(maxWidth: .infinity)
                        
                        // 编辑菜单
                        EditMenu(selectedAction: $selectedAction)
                            .frame(maxWidth: .infinity)
                    }
                    
                    HStack(spacing: 12) {
                        // 视图菜单
                        ViewMenu(selectedAction: $selectedAction)
                            .frame(maxWidth: .infinity)
                        
                        // 帮助菜单
                        HelpMenu(selectedAction: $selectedAction)
                            .frame(maxWidth: .infinity)
                    }
                    
                    // 4. Command菜单（macOS专用）
                    MenuSectionHeader("4. Command菜单（macOS专用）")
                    
                    VStack(alignment: .leading) {
                        Text("在macOS上，SwiftUI提供了以下命令菜单组件：")
                            .font(.caption)
                        
                        Text("• CommandMenu - 添加自定义菜单到菜单栏")
                            .font(.caption)
                        
                        Text("• CommandGroup - 将命令分组到默认菜单中")
                            .font(.caption)
                        
                        Text("• Commands - 自定义应用的命令结构")
                            .font(.caption)
                    }
                    .padding()
                    .frame(maxWidth: .infinity, alignment: .leading)
                    .background(Color(.systemGray6))
                    .cornerRadius(8)
                    
                    Spacer(minLength: 40)
                    
                    // 底部结果显示
                    Text("最后选择的操作: \(selectedAction)")
                        .font(.headline)
                        .frame(maxWidth: .infinity, alignment: .center)
                        .padding(.bottom)
                }
                .padding()
            }
            .navigationTitle("SwiftUI Menu 示例")
        }
    }
}

// MARK: - 辅助组件

struct MenuSectionHeader: View {
    let title: String
    
    init(_ title: String) {
        self.title = title
    }
    
    var body: some View {
        Text(title)
            .font(.headline)
            .padding(.top, 8)
    }
}

// MARK: - 菜单组件

struct FileMenu: View {
    @Binding var selectedAction: String
    
    var body: some View {
        Menu {
            Button {
                selectedAction = "新建文件"
            } label: {
                Label("新建", systemImage: "doc.badge.plus")
            }
            .keyboardShortcut("n", modifiers: .command)
            
            Button {
                selectedAction = "打开文件"
            } label: {
                Label("打开...", systemImage: "folder")
            }
            .keyboardShortcut("o", modifiers: .command)
            
            Button {
                selectedAction = "保存文件"
            } label: {
                Label("保存", systemImage: "square.and.arrow.down")
            }
            .keyboardShortcut("s", modifiers: .command)
            
            Divider()
            
            Menu {
                Button {
                    selectedAction = "导出为PDF"
                } label: {
                    Label("PDF", systemImage: "doc.fill")
                }
                
                Button {
                    selectedAction = "导出为图片"
                } label: {
                    Label("图片", systemImage: "photo")
                }
            } label: {
                Label("导出为", systemImage: "arrow.up.doc")
            }
            
            Divider()
            
            Button(role: .destructive) {
                selectedAction = "退出"
            } label: {
                Label("退出", systemImage: "escape")
            }
            .keyboardShortcut("q", modifiers: .command)
            
        } label: {
            MenuButton(title: "文件")
        }
    }
}

struct EditMenu: View {
    @Binding var selectedAction: String
    
    var body: some View {
        Menu {
            Button {
                selectedAction = "撤销"
            } label: {
                Label("撤销", systemImage: "arrow.uturn.backward")
            }
            .keyboardShortcut("z", modifiers: .command)
            
            Button {
                selectedAction = "重做"
            } label: {
                Label("重做", systemImage: "arrow.uturn.forward")
            }
            .keyboardShortcut("z", modifiers: [.command, .shift])
            
            Divider()
            
            Button {
                selectedAction = "剪切"
            } label: {
                Label("剪切", systemImage: "scissors")
            }
            .keyboardShortcut("x", modifiers: .command)
            
            Button {
                selectedAction = "复制"
            } label: {
                Label("复制", systemImage: "doc.on.doc")
            }
            .keyboardShortcut("c", modifiers: .command)
            
            Button {
                selectedAction = "粘贴"
            } label: {
                Label("粘贴", systemImage: "doc.on.clipboard")
            }
            .keyboardShortcut("v", modifiers: .command)
            
        } label: {
            MenuButton(title: "编辑")
        }
    }
}

struct ViewMenu: View {
    @Binding var selectedAction: String
    
    var body: some View {
        Menu {
            Button {
                selectedAction = "放大"
            } label: {
                Label("放大", systemImage: "plus.magnifyingglass")
            }
            .keyboardShortcut("+", modifiers: .command)
            
            Button {
                selectedAction = "缩小"
            } label: {
                Label("缩小", systemImage: "minus.magnifyingglass")
            }
            .keyboardShortcut("-", modifiers: .command)
            
            Button {
                selectedAction = "全屏"
            } label: {
                Label("全屏", systemImage: "arrow.up.left.and.arrow.down.right")
            }
            .keyboardShortcut("f", modifiers: [.command, .control])
            
        } label: {
            MenuButton(title: "视图")
        }
    }
}

struct HelpMenu: View {
    @Binding var selectedAction: String
    
    var body: some View {
        Menu {
            Button {
                selectedAction = "帮助文档"
            } label: {
                Label("帮助文档", systemImage: "book")
            }
            .keyboardShortcut("?", modifiers: .command)
            
            Button {
                selectedAction = "关于"
            } label: {
                Label("关于", systemImage: "info.circle")
            }
            
        } label: {
            MenuButton(title: "帮助")
        }
    }
}

struct MenuButton: View {
    let title: String
    
    var body: some View {
        Text(title)
            .frame(minWidth: 0, maxWidth: .infinity)
            .padding(10)
            .background(Color.blue)
            .foregroundColor(.white)
            .cornerRadius(8)
    }
}
