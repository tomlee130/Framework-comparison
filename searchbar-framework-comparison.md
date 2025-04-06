# 六大框架 Search Bar 组件比较

本文档对比Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue六大框架中Search Bar组件的实现和特性，从以下维度进行分析：

- 搜索触发机制
- 搜索建议支持
- 历史记录管理
- 搜索结果展示
- 搜索范围配置

每个框架部分包含平台特性、兼容性分析和完整实例代码。

## 目录

1. [Flutter SearchBar](#flutter-searchbar)
2. [Android Jetpack Compose SearchBar](#android-jetpack-compose-searchbar)
3. [iOS UIKit SearchBar](#ios-uikit-searchbar)
4. [iOS SwiftUI SearchBar](#ios-swiftui-searchbar)
5. [鸿蒙 ArkUI SearchBar](#鸿蒙-arkui-searchbar)
6. [Vue SearchBar](#vue-searchbar)
7. [总结对比](#总结对比)

## Flutter SearchBar

### 搜索触发机制

Flutter提供了多种搜索组件和触发方式：

- `SearchBar`：Flutter 3.10+新增的Material 3设计搜索栏
- `SearchAnchor`：Flutter 3.10+新增的可自定义搜索菜单组件
- `TextField`+`AppBar`：传统搜索实现方式
- `SearchDelegate`：专为搜索场景设计的委托类

搜索触发方式包括：

- 输入文本变化（`onChanged`回调）
- 提交搜索（`onSubmitted`回调）
- 点击搜索图标（`onTap`回调）
- 语音输入（需自行集成平台语音API）
- 焦点变化触发（通过`FocusNode`控制）
- 支持防抖和节流处理

### 搜索建议支持

- 可通过`SearchAnchor.suggestions`提供实时搜索建议
- 支持自定义建议项布局和样式
- 建议可以基于当前输入内容动态生成
- 支持异步加载建议（如网络请求）
- 可实现带高亮的匹配文本显示
- 建议数量可控制，支持分类展示
- 支持键盘导航建议项
- 可添加图标、尾随操作等富内容

### 历史记录管理

Flutter没有内置的搜索历史记录管理，但可以通过以下方式实现：

- 使用`SharedPreferences`或`Hive`等本地存储
- 结合`Provider`或`Bloc`进行状态管理
- 支持历史记录的增删改查操作
- 可实现历史记录排序（基于时间、频率等）
- 支持历史记录去重
- 可设置最大历史记录数量
- 支持一键清除历史
- 可选择性禁用历史记录功能

### 搜索结果展示

- 高度自定义的结果展示方式
- 可使用`ListView`、`GridView`等展示结果
- 支持分页加载（无限滚动）
- 可实现结果分组和分类
- 结果项可包含任意复杂布局
- 支持空结果状态处理
- 可添加加载状态指示器
- 支持结果过滤和排序
- 可实现结果高亮显示匹配文本

### 搜索范围配置

- 可通过`PopupMenuButton`或`DropdownButton`添加搜索范围选择
- 支持多维度搜索条件组合
- 可实现搜索筛选器（Filter）
- 支持类别选择器整合
- 范围配置可保存并在会话中持久化
- 可设置默认搜索范围
- 支持搜索范围的动态变化
- 范围切换可触发不同搜索策略

### 平台特性与兼容性

- 完全跨平台，在所有Flutter支持的平台上表现一致
- Material 3设计的`SearchBar`需要Flutter 3.10+
- 可适配不同平台的输入法和键盘类型
- 支持深色模式和动态主题
- 响应式设计，适应不同屏幕尺寸
- 支持国际化和本地化
- 可访问性支持（语义标签、屏幕阅读等）
- 性能优化，适合大数据集搜索

### 完整示例代码

```dart
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';
import 'dart:async';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter SearchBar Demo',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.blue),
        useMaterial3: true,
      ),
      home: const SearchBarDemo(),
    );
  }
}

class SearchBarDemo extends StatefulWidget {
  const SearchBarDemo({Key? key}) : super(key: key);

  @override
  State<SearchBarDemo> createState() => _SearchBarDemoState();
}

class _SearchBarDemoState extends State<SearchBarDemo> {
  // 控制搜索状态
  final TextEditingController _searchController = TextEditingController();
  final FocusNode _focusNode = FocusNode();
  bool _isSearching = false;
  Timer? _debounce;
  
  // 搜索范围
  String _selectedSearchScope = "全部";
  final List<String> _searchScopes = ["全部", "标题", "内容", "标签", "作者"];
  
  // 搜索历史
  List<String> _searchHistory = [];
  
  // 搜索建议
  final List<String> _allItems = [
    "苹果手机", "苹果电脑", "苹果平板", "苹果手表",
    "安卓手机", "安卓平板", "安卓手表",
    "华为手机", "华为平板", "华为电脑", "华为手表",
    "小米手机", "小米平板", "小米电视", "小米手表",
    "三星手机", "三星平板", "三星电视", "三星手表",
  ];
  List<String> _searchResults = [];
  List<String> _searchSuggestions = [];
  
  @override
  void initState() {
    super.initState();
    _loadSearchHistory();
    
    // 监听文本变化
    _searchController.addListener(_onSearchChanged);
  }
  
  @override
  void dispose() {
    _searchController.dispose();
    _focusNode.dispose();
    _debounce?.cancel();
    super.dispose();
  }
  
  // 加载搜索历史
  Future<void> _loadSearchHistory() async {
    final prefs = await SharedPreferences.getInstance();
    setState(() {
      _searchHistory = prefs.getStringList('search_history') ?? [];
    });
  }
  
  // 保存搜索历史
  Future<void> _saveSearchHistory(String query) async {
    if (query.isEmpty) return;
    
    // 移除重复并放到最前面
    _searchHistory.remove(query);
    _searchHistory.insert(0, query);
    
    // 限制历史记录数量
    if (_searchHistory.length > 10) {
      _searchHistory = _searchHistory.sublist(0, 10);
    }
    
    final prefs = await SharedPreferences.getInstance();
    await prefs.setStringList('search_history', _searchHistory);
    
    setState(() {});
  }
  
  // 清除搜索历史
  Future<void> _clearSearchHistory() async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.remove('search_history');
    
    setState(() {
      _searchHistory = [];
    });
  }
  
  // 处理搜索文本变化（带防抖）
  void _onSearchChanged() {
    if (_debounce?.isActive ?? false) _debounce!.cancel();
    
    _debounce = Timer(const Duration(milliseconds: 500), () {
      if (_searchController.text.isNotEmpty) {
        _updateSuggestions();
      } else {
        setState(() {
          _searchSuggestions = [];
        });
      }
    });
  }
  
  // 更新搜索建议
  void _updateSuggestions() {
    final query = _searchController.text.toLowerCase();
    
    setState(() {
      _searchSuggestions = _allItems
          .where((item) => item.toLowerCase().contains(query))
          .take(5)
          .toList();
    });
  }
  
  // 执行搜索
  void _performSearch(String query) {
    // 保存到历史记录
    _saveSearchHistory(query);
    
    // 根据搜索范围和查询词过滤结果
    setState(() {
      if (query.isEmpty) {
        _searchResults = [];
      } else {
        final lowerQuery = query.toLowerCase();
        _searchResults = _allItems
            .where((item) => item.toLowerCase().contains(lowerQuery))
            .toList();
      }
      
      // 清空建议
      _searchSuggestions = [];
    });
  }
  
  // 构建搜索建议列表
  Widget _buildSuggestions() {
    if (_searchSuggestions.isEmpty && _searchHistory.isEmpty) {
      return const SizedBox.shrink();
    }
    
    return Card(
      margin: const EdgeInsets.only(top: 8.0),
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: [
          // 搜索建议
          if (_searchSuggestions.isNotEmpty) ...[
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: Align(
                alignment: Alignment.centerLeft,
                child: Text('搜索建议', style: Theme.of(context).textTheme.titleSmall),
              ),
            ),
            ...List.generate(
              _searchSuggestions.length,
              (index) => ListTile(
                leading: const Icon(Icons.search),
                title: Text(_searchSuggestions[index]),
                onTap: () {
                  _searchController.text = _searchSuggestions[index];
                  _performSearch(_searchSuggestions[index]);
                  FocusScope.of(context).unfocus();
                },
              ),
            ),
            const Divider(),
          ],
          
          // 搜索历史
          if (_searchHistory.isNotEmpty) ...[
            Padding(
              padding: const EdgeInsets.all(8.0),
              child: Row(
                mainAxisAlignment: MainAxisAlignment.spaceBetween,
                children: [
                  Text('搜索历史', style: Theme.of(context).textTheme.titleSmall),
                  TextButton(
                    onPressed: _clearSearchHistory,
                    child: const Text('清除'),
                  ),
                ],
              ),
            ),
            ...List.generate(
              _searchHistory.length,
              (index) => ListTile(
                leading: const Icon(Icons.history),
                title: Text(_searchHistory[index]),
                trailing: IconButton(
                  icon: const Icon(Icons.north_west),
                  onPressed: () {
                    _searchController.text = _searchHistory[index];
                    _performSearch(_searchHistory[index]);
                    FocusScope.of(context).unfocus();
                  },
                ),
                onTap: () {
                  _searchController.text = _searchHistory[index];
                  _performSearch(_searchHistory[index]);
                  FocusScope.of(context).unfocus();
                },
              ),
            ),
          ],
        ],
      ),
    );
  }
  
  // 构建搜索范围选择器
  Widget _buildScopeSelector() {
    return DropdownButton<String>(
      value: _selectedSearchScope,
      icon: const Icon(Icons.arrow_drop_down),
      elevation: 16,
      underline: Container(height: 0),
      onChanged: (String? newValue) {
        setState(() {
          _selectedSearchScope = newValue!;
          if (_searchController.text.isNotEmpty) {
            _performSearch(_searchController.text);
          }
        });
      },
      items: _searchScopes.map<DropdownMenuItem<String>>((String value) {
        return DropdownMenuItem<String>(
          value: value,
          child: Text(value),
        );
      }).toList(),
    );
  }
  
  // 构建搜索结果
  Widget _buildSearchResults() {
    if (_searchResults.isEmpty) {
      if (_searchController.text.isEmpty) {
        return const Center(
          child: Text('请输入搜索内容'),
        );
      } else {
        return const Center(
          child: Text('没有找到相关结果'),
        );
      }
    }
    
    return ListView.builder(
      itemCount: _searchResults.length,
      itemBuilder: (context, index) {
        final result = _searchResults[index];
        return ListTile(
          title: Text(result),
          subtitle: Text('范围: $_selectedSearchScope · 结果 #${index + 1}'),
          leading: const Icon(Icons.article),
          onTap: () {
            ScaffoldMessenger.of(context).showSnackBar(
              SnackBar(content: Text('选择了: $result')),
            );
          },
        );
      },
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Flutter SearchBar Demo'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          children: [
            // Material 3 SearchBar (Flutter 3.10+)
            SearchBar(
              controller: _searchController,
              focusNode: _focusNode,
              hintText: '搜索...',
              leading: const Icon(Icons.search),
              trailing: [
                _buildScopeSelector(),
                IconButton(
                  icon: const Icon(Icons.clear),
                  onPressed: () {
                    _searchController.clear();
                    setState(() {
                      _searchSuggestions = [];
                      _searchResults = [];
                    });
                  },
                ),
              ],
              onChanged: (value) {
                setState(() {
                  _isSearching = value.isNotEmpty;
                });
              },
              onSubmitted: (value) {
                _performSearch(value);
                FocusScope.of(context).unfocus();
              },
            ),
            
            // 搜索建议和历史记录
            if (_focusNode.hasFocus)
              _buildSuggestions(),
              
            // 搜索结果
            Expanded(
              child: _buildSearchResults(),
            ),
          ],
        ),
      ),
    );
  }
}

## Android Jetpack Compose SearchBar

### 搜索触发机制

Jetpack Compose提供了专门的搜索组件和多种触发机制：

- `SearchBar`：Material 3规范的现代搜索组件
- `DockedSearchBar`：固定在界面顶部的搜索栏
- `TextField`：可自定义的基础文本输入组件
- `OutlinedTextField`：带边框的文本输入组件

搜索触发方式包括：

- 输入文本变化（`onValueChange`回调）
- 提交查询（`onSearch`回调）
- 点击搜索图标（`onClick`回调）
- 语音搜索集成（通过`ExperimentalMaterial3Api`）
- 焦点状态变化（`onActiveChange`回调）
- 支持自定义防抖逻辑

### 搜索建议支持

- 可在`SearchBar`中通过`content`作用域配置建议
- 支持异步加载搜索建议
- 可根据输入内容实时过滤建议
- 支持带高亮的匹配文本展示
- 建议项可包含图标、文本、动作等
- 支持分组和分类的建议列表
- 完全可自定义的建议项布局
- 支持键盘导航建议项

### 历史记录管理

- 可使用`DataStore`或`Room`数据库存储历史记录
- 支持通过ViewModel管理历史记录生命周期
- 历史记录支持增删改查操作
- 可基于时间、频率排序历史记录
- 支持历史记录智能排序和去重
- 可设置历史记录最大条数
- 支持历史记录加密存储
- 可实现历史记录跨设备同步（结合账户）

### 搜索结果展示

- 高度灵活的结果展示模式
- 支持`LazyColumn`/`LazyVerticalGrid`结果列表
- 内置分页加载和无限滚动支持
- 支持结果的分组和分类显示
- 可实现动画过渡效果
- 内置空结果状态处理
- 支持加载状态、错误状态展示
- 可实现结果项的展开/折叠
- 支持结果拖拽和滑动操作

### 搜索范围配置

- 可通过`FilterChip`或`DropdownMenu`设置搜索范围
- 支持自定义筛选条件组合
- 可实现高级搜索条件配置
- 支持搜索参数记忆功能
- 范围配置可持久化保存
- 可实现范围的动态切换
- 支持范围间的逻辑关系配置
- 结合Material 3设计的筛选组件

### 平台特性与兼容性

- Material 3设计语言的完整实现
- 需要Jetpack Compose 1.2.0+支持
- 适配AndroidX和最新Android API
- 兼容Android 5.0（API 21）及以上
- 支持深色模式和动态主题
- Material You动态颜色支持
- 可访问性支持（内容描述、屏幕阅读器）
- 具有统一行为和动效的沉浸式体验
- 支持平板大屏适配

### 完整示例代码

```kotlin
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.platform.LocalContext
import androidx.compose.ui.unit.dp

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MaterialTheme {
                SearchDemo()
            }
        }
    }
}

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun SearchDemo() {
    var searchQuery by remember { mutableStateOf("") }
    var active by remember { mutableStateOf(false) }
    var searchResults by remember { mutableStateOf(emptyList<String>()) }
    var searchSuggestions by remember { mutableStateOf(emptyList<String>()) }
    var selectedScope by remember { mutableStateOf("全部") }
    val searchScopes = listOf("全部", "标题", "内容", "标签", "作者")
    var showScopeSelector by remember { mutableStateOf(false) }
    val context = LocalContext.current
    
    // 示例数据
    val allItems = listOf(
        "苹果手机", "苹果电脑", "苹果平板", "苹果手表",
        "安卓手机", "安卓平板", "安卓手表",
        "华为手机", "华为平板", "华为电脑", "华为手表",
        "小米手机", "小米平板", "小米电视", "小米手表",
        "三星手机", "三星平板", "三星电视", "三星手表"
    )
    
    // 模拟历史记录
    val searchHistory = remember { mutableStateListOf("Android 12", "Jetpack Compose", "Material 3") }
    
    // 更新搜索建议函数
    val updateSuggestions = { query: String ->
        if (query.isNotEmpty()) {
            val lowerQuery = query.lowercase()
            searchSuggestions = allItems
                .filter { it.lowercase().contains(lowerQuery) }
                .take(5)
        } else {
            searchSuggestions = emptyList()
        }
    }
    
    // 执行搜索函数
    val performSearch = { query: String ->
        if (query.isEmpty()) {
            searchResults = emptyList()
        } else {
            val lowerQuery = query.lowercase()
            searchResults = allItems
                .filter { it.lowercase().contains(lowerQuery) }
            
            // 添加到历史记录（仅作示范，实际应使用持久化存储）
            if (query !in searchHistory) {
                searchHistory.add(0, query)
                if (searchHistory.size > 5) {
                    searchHistory.removeLast()
                }
            }
        }
    }
    
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("Jetpack Compose SearchBar") },
                colors = TopAppBarDefaults.topAppBarColors(
                    containerColor = MaterialTheme.colorScheme.primaryContainer
                )
            )
        }
    ) { paddingValues ->
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues)
                .padding(16.dp)
        ) {
            // Material 3 SearchBar
            SearchBar(
                query = searchQuery,
                onQueryChange = { 
                    searchQuery = it
                    updateSuggestions(it)
                },
                onSearch = { 
                    performSearch(it)
                    active = false
                },
                active = active,
                onActiveChange = { active = it },
                placeholder = { Text("搜索...") },
                leadingIcon = { Icon(Icons.Default.Search, contentDescription = "搜索") },
                trailingIcon = {
                    Row {
                        // 搜索范围选择器
                        IconButton(onClick = { showScopeSelector = true }) {
                            Icon(Icons.Default.FilterList, contentDescription = "筛选")
                        }
                        
                        // 清除按钮
                        if (searchQuery.isNotEmpty()) {
                            IconButton(onClick = { 
                                searchQuery = ""
                                searchSuggestions = emptyList()
                            }) {
                                Icon(Icons.Default.Clear, contentDescription = "清除")
                            }
                        }
                    }
                    
                    // 搜索范围下拉菜单
                    DropdownMenu(
                        expanded = showScopeSelector,
                        onDismissRequest = { showScopeSelector = false }
                    ) {
                        searchScopes.forEach { scope ->
                            DropdownMenuItem(
                                text = { Text(scope) },
                                onClick = {
                                    selectedScope = scope
                                    showScopeSelector = false
                                    // 重新执行搜索以应用新范围
                                    if (searchQuery.isNotEmpty()) {
                                        performSearch(searchQuery)
                                    }
                                },
                                leadingIcon = {
                                    if (scope == selectedScope) {
                                        Icon(Icons.Default.Check, contentDescription = null)
                                    }
                                }
                            )
                        }
                    }
                }
            ) {
                // 搜索历史
                if (searchQuery.isEmpty() && searchHistory.isNotEmpty()) {
                    Row(
                        modifier = Modifier
                            .fillMaxWidth()
                            .padding(horizontal = 16.dp, vertical = 8.dp),
                        horizontalArrangement = Arrangement.SpaceBetween,
                        verticalAlignment = Alignment.CenterVertically
                    ) {
                        Text("搜索历史", style = MaterialTheme.typography.titleMedium)
                        TextButton(onClick = { searchHistory.clear() }) {
                            Text("清除")
                        }
                    }
                    
                    searchHistory.forEach { history ->
                        ListItem(
                            headlineContent = { Text(history) },
                            leadingContent = { Icon(Icons.Default.History, contentDescription = null) },
                            modifier = Modifier.clickable {
                                searchQuery = history
                                performSearch(history)
                                active = false
                            }
                        )
                    }
                    
                    Divider(modifier = Modifier.padding(vertical = 8.dp))
                }
                
                // 搜索建议
                if (searchSuggestions.isNotEmpty()) {
                    Text(
                        "搜索建议",
                        style = MaterialTheme.typography.titleMedium,
                        modifier = Modifier.padding(horizontal = 16.dp, vertical = 8.dp)
                    )
                    
                    searchSuggestions.forEach { suggestion ->
                        ListItem(
                            headlineContent = { Text(suggestion) },
                            leadingContent = { Icon(Icons.Default.Search, contentDescription = null) },
                            modifier = Modifier.clickable {
                                searchQuery = suggestion
                                performSearch(suggestion)
                                active = false
                            }
                        )
                    }
                }
            }
            
            // 搜索范围指示器
            if (!active && selectedScope != "全部") {
                Chip(
                    onClick = { showScopeSelector = true },
                    modifier = Modifier.padding(vertical = 8.dp),
                    label = { Text("范围: $selectedScope") },
                    leadingIcon = { Icon(Icons.Default.FilterList, contentDescription = null) }
                )
            }
            
            // 搜索结果
            if (!active) {
                if (searchResults.isEmpty()) {
                    if (searchQuery.isEmpty()) {
                        Box(
                            modifier = Modifier.fillMaxSize(),
                            contentAlignment = Alignment.Center
                        ) {
                            Text("请输入搜索内容")
                        }
                    } else {
                        Box(
                            modifier = Modifier.fillMaxSize(),
                            contentAlignment = Alignment.Center
                        ) {
                            Text("没有找到相关结果")
                        }
                    }
                } else {
                    Text(
                        "搜索结果 (${searchResults.size})",
                        style = MaterialTheme.typography.titleMedium,
                        modifier = Modifier.padding(vertical = 8.dp)
                    )
                    
                    LazyColumn {
                        items(searchResults) { result ->
                            ListItem(
                                headlineContent = { Text(result) },
                                supportingContent = { Text("范围: $selectedScope") },
                                leadingContent = { Icon(Icons.Default.Article, contentDescription = null) }
                            )
                            Divider()
                        }
                    }
                }
            }
        }
    }
}

## iOS UIKit SearchBar

### 搜索触发机制

UIKit提供了专门的`UISearchBar`和`UISearchController`组件，支持多种触发方式：

- `UISearchBar`：基础的搜索栏组件，通常嵌入在导航栏或视图中
- `UISearchController`：专用的搜索控制器，提供完整的搜索体验
- `UISearchTextField`：iOS 13+的新组件，提供更多自定义选项

搜索触发方式包括：

- 文本变化（`textDidChange`代理方法）
- 搜索按钮点击（`searchBarSearchButtonClicked`代理方法）
- 取消按钮点击（`searchBarCancelButtonClicked`代理方法）
- 书签按钮点击（`searchBarBookmarkButtonClicked`代理方法）
- 搜索栏获得/失去焦点（`searchBarTextDidBeginEditing/searchBarTextDidEndEditing`方法）
- 支持键盘搜索键触发

### 搜索建议支持

- 可通过`UISearchController`的`searchResultsUpdater`协议显示实时建议
- 可配置搜索建议表格视图（`UITableView`）或集合视图（`UICollectionView`）
- 支持自定义搜索建议布局和样式
- 可实现异步加载搜索建议
- 支持分组和分类的建议列表
- 可实现带高亮的匹配文本
- 支持快速动作按钮

### 历史记录管理

- 可使用`NSUserDefaults`或`Core Data`存储搜索历史
- 支持历史记录的CRUD操作
- 可基于时间或频率排序历史记录
- 支持历史记录的最大存储条数限制
- 可使用iCloud同步搜索历史
- 提供历史记录清除功能
- 支持加密存储敏感的搜索历史
- 可实现智能历史记录推荐

### 搜索结果展示

- 可通过表格视图(`UITableView`)或集合视图(`UICollectionView`)展示
- 支持分组和分类的结果展示
- 可实现结果项的展开/折叠
- 支持结果列表的分页加载
- 可定制结果项的外观和交互
- 提供空结果状态处理
- 支持结果排序和筛选
- 可实现结果项的侧滑操作
- 支持3D Touch预览

### 搜索范围配置

- 内置`scopeButtonTitles`属性支持多范围搜索
- `selectedScopeButtonIndex`属性跟踪选中的范围
- 提供`searchBar(_:selectedScopeButtonIndexDidChange:)`代理方法监听范围变化
- 支持范围按钮自定义样式
- 可实现分段控制器(UISegmentedControl)作为自定义范围选择器
- 支持层级化的搜索范围
- 可将搜索范围保存到用户设置中
- 支持动态调整搜索范围

### 平台特性与兼容性

- iOS原生组件，提供与系统一致的外观和行为
- 完整支持iOS深色模式
- 支持Dynamic Type和字体缩放
- 提供VoiceOver无障碍支持
- 兼容iOS 7及以上版本
- UISearchTextField仅支持iOS 13+
- 支持语言从右到左(RTL)布局
- 适配iPad分屏和多任务模式
- 支持自定义键盘工具栏
- 提供键盘快捷键支持(iPad)

### 完整示例代码

```swift
import UIKit

class SearchViewController: UIViewController {
    
    // MARK: - 属性
    
    let searchController = UISearchController(searchResultsController: nil)
    var tableView = UITableView()
    
    // 数据源
    var allItems = [
        "苹果手机", "苹果电脑", "苹果平板", "苹果手表",
        "安卓手机", "安卓平板", "安卓手表",
        "华为手机", "华为平板", "华为电脑", "华为手表",
        "小米手机", "小米平板", "小米电视", "小米手表", 
        "三星手机", "三星平板", "三星电视", "三星手表"
    ]
    var filteredItems: [String] = []
    
    // 搜索范围
    let searchScopes = ["全部", "手机", "平板", "电脑", "手表"]
    var selectedScope = 0
    
    // 历史记录
    var searchHistory: [String] = []
    let historyKey = "searchHistory"
    let maxHistoryCount = 10
    
    // 当前状态
    var isShowingHistory = false
    var currentSearchText = ""
    
    // MARK: - 生命周期方法
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        setupNavigation()
        setupSearchController()
        setupTableView()
        loadSearchHistory()
    }
    
    // MARK: - 设置
    
    func setupNavigation() {
        title = "UIKit SearchBar"
        navigationController?.navigationBar.prefersLargeTitles = true
    }
    
    func setupSearchController() {
        // 配置搜索控制器
        searchController.searchResultsUpdater = self
        searchController.obscuresBackgroundDuringPresentation = false
        searchController.searchBar.placeholder = "搜索..."
        
        // 配置搜索栏
        searchController.searchBar.delegate = self
        
        // 配置搜索范围
        searchController.searchBar.scopeButtonTitles = searchScopes
        
        // 添加搜索控制器到导航栏
        navigationItem.searchController = searchController
        navigationItem.hidesSearchBarWhenScrolling = false
        
        // 始终显示取消按钮
        searchController.searchBar.showsCancelButton = true
        
        // 添加清除历史按钮到键盘工具栏
        let toolbar = UIToolbar()
        toolbar.sizeToFit()
        let flexSpace = UIBarButtonItem(barButtonSystemItem: .flexibleSpace, target: nil, action: nil)
        let clearButton = UIBarButtonItem(title: "清除历史", style: .plain, target: self, action: #selector(clearHistory))
        toolbar.items = [flexSpace, clearButton]
        searchController.searchBar.searchTextField.inputAccessoryView = toolbar
        
        definesPresentationContext = true
    }
    
    func setupTableView() {
        view.addSubview(tableView)
        tableView.translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([
            tableView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
            tableView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            tableView.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            tableView.bottomAnchor.constraint(equalTo: view.bottomAnchor)
        ])
        
        tableView.register(UITableViewCell.self, forCellReuseIdentifier: "Cell")
        tableView.dataSource = self
        tableView.delegate = self
    }
    
    // MARK: - 搜索历史管理
    
    func loadSearchHistory() {
        if let history = UserDefaults.standard.array(forKey: historyKey) as? [String] {
            searchHistory = history
        }
    }
    
    func saveSearchHistory() {
        UserDefaults.standard.set(searchHistory, forKey: historyKey)
    }
    
    func addToHistory(_ query: String) {
        if query.isEmpty { return }
        
        // 如果已存在，先移除
        if let index = searchHistory.firstIndex(of: query) {
            searchHistory.remove(at: index)
        }
        
        // 添加到最前
        searchHistory.insert(query, at: 0)
        
        // 限制数量
        if searchHistory.count > maxHistoryCount {
            searchHistory.removeLast()
        }
        
        saveSearchHistory()
    }
    
    @objc func clearHistory() {
        searchHistory.removeAll()
        saveSearchHistory()
        
        if isShowingHistory {
            tableView.reloadData()
        }
    }
    
    // MARK: - 搜索逻辑
    
    func filterContentForSearchText(_ searchText: String, scope: Int) {
        currentSearchText = searchText
        
        // 判断是否显示历史记录
        if searchText.isEmpty && searchController.isActive {
            isShowingHistory = true
            tableView.reloadData()
            return
        } else {
            isShowingHistory = false
        }
        
        // 根据范围筛选
        if scope == 0 { // 全部
            filteredItems = allItems.filter { $0.localizedCaseInsensitiveContains(searchText) }
        } else {
            let scopeText = searchScopes[scope].lowercased()
            filteredItems = allItems.filter { 
                $0.localizedCaseInsensitiveContains(searchText) && 
                $0.lowercased().contains(scopeText)
            }
        }
        
        tableView.reloadData()
    }
    
    func performSearch() {
        guard !currentSearchText.isEmpty else { return }
        
        // 添加到历史记录
        addToHistory(currentSearchText)
        
        // 隐藏键盘
        searchController.searchBar.resignFirstResponder()
        
        // 通知用户搜索完成
        let alert = UIAlertController(
            title: "搜索完成", 
            message: "找到 \(filteredItems.count) 个结果", 
            preferredStyle: .alert
        )
        alert.addAction(UIAlertAction(title: "确定", style: .default))
        present(alert, animated: true)
    }
}

// MARK: - UISearchResultsUpdating

extension SearchViewController: UISearchResultsUpdating {
    func updateSearchResults(for searchController: UISearchController) {
        let searchBar = searchController.searchBar
        let scope = searchBar.selectedScopeButtonIndex
        filterContentForSearchText(searchBar.text ?? "", scope: scope)
    }
}

// MARK: - UISearchBarDelegate

extension SearchViewController: UISearchBarDelegate {
    func searchBarSearchButtonClicked(_ searchBar: UISearchBar) {
        performSearch()
    }
    
    func searchBar(_ searchBar: UISearchBar, selectedScopeButtonIndexDidChange selectedScope: Int) {
        self.selectedScope = selectedScope
        filterContentForSearchText(searchBar.text ?? "", scope: selectedScope)
    }
    
    func searchBarTextDidBeginEditing(_ searchBar: UISearchBar) {
        // 光标进入时如果没有文字，显示历史记录
        if searchBar.text?.isEmpty ?? true {
            isShowingHistory = true
            tableView.reloadData()
        }
    }
}

// MARK: - UITableViewDataSource, UITableViewDelegate

extension SearchViewController: UITableViewDataSource, UITableViewDelegate {
    func numberOfSections(in tableView: UITableView) -> Int {
        return 1
    }
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        if isShowingHistory {
            return searchHistory.isEmpty ? 1 : searchHistory.count
        } else {
            return filteredItems.isEmpty ? 1 : filteredItems.count
        }
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)
        
        if isShowingHistory {
            if searchHistory.isEmpty {
                cell.textLabel?.text = "无搜索历史"
                cell.textLabel?.textColor = .gray
                cell.selectionStyle = .none
                cell.accessoryType = .none
            } else {
                let historyItem = searchHistory[indexPath.row]
                cell.textLabel?.text = historyItem
                cell.textLabel?.textColor = .systemBlue
                cell.selectionStyle = .default
                cell.accessoryType = .disclosureIndicator
            }
        } else {
            if filteredItems.isEmpty {
                cell.textLabel?.text = "未找到相关结果"
                cell.textLabel?.textColor = .gray
                cell.selectionStyle = .none
                cell.accessoryType = .none
            } else {
                let item = filteredItems[indexPath.row]
                cell.textLabel?.text = item
                cell.textLabel?.textColor = .label
                cell.selectionStyle = .default
                cell.accessoryType = .detailDisclosureButton
            }
        }
        
        return cell
    }
    
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        tableView.deselectRow(at: indexPath, animated: true)
        
        if isShowingHistory && !searchHistory.isEmpty {
            // 点击历史记录项
            let historyItem = searchHistory[indexPath.row]
            searchController.searchBar.text = historyItem
            filterContentForSearchText(historyItem, scope: selectedScope)
            performSearch()
        } else if !filteredItems.isEmpty {
            // 点击搜索结果项
            let selectedItem = filteredItems[indexPath.row]
            let alert = UIAlertController(
                title: "选择了", 
                message: selectedItem, 
                preferredStyle: .alert
            )
            alert.addAction(UIAlertAction(title: "确定", style: .default))
            present(alert, animated: true)
        }
    }
    
    func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        if isShowingHistory {
            return "搜索历史"
        } else if !filteredItems.isEmpty {
            return "搜索结果 (范围: \(searchScopes[selectedScope]))"
        }
        return nil
    }
    
    func tableView(_ tableView: UITableView, accessoryButtonTappedForRowWith indexPath: IndexPath) {
        if !filteredItems.isEmpty {
            let selectedItem = filteredItems[indexPath.row]
            let alert = UIAlertController(
                title: "详情", 
                message: "这是关于 \(selectedItem) 的详细信息", 
                preferredStyle: .alert
            )
            alert.addAction(UIAlertAction(title: "确定", style: .default))
            present(alert, animated: true)
        }
    }
}

## iOS SwiftUI SearchBar

### 搜索触发机制

SwiftUI提供了多种搜索组件和触发机制：

- `searchable`修饰符：iOS 15+引入，为导航栏添加搜索功能
- `.searchCompletion`修饰符：iOS 16+引入，提供搜索建议
- `.searchScopes`修饰符：iOS 16+引入，提供搜索范围选择
- `TextField`与自定义组合：适用于任何iOS版本的通用解决方案

搜索触发方式包括：

- 文本变化（通过绑定的State变量自动触发）
- 提交搜索（通过`.onSubmit`修饰符）
- 取消按钮点击（通过`.onCancel`修饰符，iOS 16+）
- 搜索栏获得/失去焦点（通过`.searchSuggestions`或`.focused`修饰符）
- 使用系统搜索快捷键（针对iPad和Mac）
- 支持全键盘操作

### 搜索建议支持

- iOS 16+使用`.searchSuggestions`修饰符提供建议视图
- 可使用`.searchCompletion`添加简单文本建议
- 支持任意SwiftUI视图作为建议项
- 可实现异步加载搜索建议
- 支持分组和分类的建议列表
- 可实现带高亮的匹配文本
- 支持动态建议布局
- 兼容键盘导航

### 历史记录管理

- 可使用`@AppStorage`属性包装器存储简单历史记录
- 可使用SwiftData(iOS 17+)提供高级持久化存储
- 支持历史记录的CRUD操作
- 可基于时间或频率排序历史记录
- 支持历史记录的最大存储条数限制
- 可使用iCloud同步历史记录
- 提供历史记录清除功能
- 支持加密存储敏感历史记录
- 可实现智能历史记录推荐

### 搜索结果展示

- 使用`List`、`LazyVStack`、`LazyVGrid`等视图展示结果
- 支持异步加载结果（通过`async/await`和`Task`）
- 提供动画和过渡效果
- 支持分组结果展示（通过`Section`）
- 可实现无限滚动加载
- 提供空结果状态处理
- 支持结果项的展开/折叠
- 支持多种布局动态切换
- 适配不同设备尺寸

### 搜索范围配置

- iOS 16+通过`.searchScopes`修饰符支持内置范围选择
- 可使用`Picker`或分段控件(`Picker`配合`.pickerStyle(.segmented)`)实现自定义范围
- 支持通过`@State`绑定当前选中范围
- 可根据范围自动更新搜索结果
- 支持层级化的搜索范围
- 可将搜索范围保存到用户设置中
- 提供范围切换动画
- 支持动态添加/移除范围

### 平台特性与兼容性

- 基于声明式的SwiftUI框架
- 内置搜索功能（`searchable`）需要iOS 15+
- 高级搜索功能（建议、范围等）需要iOS 16+
- 支持iOS/iPadOS/macOS/tvOS/visionOS等平台
- 完全适配深色模式
- 支持Dynamic Type和字体缩放
- 提供VoiceOver和全套无障碍功能
- 支持语言从右到左(RTL)布局
- 适配iPad多任务模式
- 支持键盘快捷键操作
- 与系统搜索体验一致

### 完整示例代码

```swift
import SwiftUI

struct SearchableExample: View {
    // 状态
    @State private var searchText = ""
    @State private var selectedScope = "全部"
    @State private var searchHistory: [String] = []
    @State private var showingHistory = false
    @State private var isSearching = false
    
    // 数据
    let searchScopes = ["全部", "手机", "平板", "电脑", "手表"]
    let allItems = [
        "苹果手机", "苹果电脑", "苹果平板", "苹果手表",
        "安卓手机", "安卓平板", "安卓手表",
        "华为手机", "华为平板", "华为电脑", "华为手表",
        "小米手机", "小米平板", "小米电视", "小米手表",
        "三星手机", "三星平板", "三星电视", "三星手表"
    ]
    
    // 计算属性
    var filteredItems: [String] {
        guard !searchText.isEmpty else { return [] }
        
        if selectedScope == "全部" {
            return allItems.filter { $0.localizedCaseInsensitiveContains(searchText) }
        } else {
            return allItems.filter {
                $0.localizedCaseInsensitiveContains(searchText) &&
                $0.localizedCaseInsensitiveContains(selectedScope)
            }
        }
    }
    
    // 建议列表
    var suggestions: [String] {
        guard !searchText.isEmpty else { return [] }
        
        return allItems
            .filter { $0.localizedCaseInsensitiveContains(searchText) }
            .prefix(5)
            .map { $0 }
    }
    
    var body: some View {
        NavigationStack {
            VStack {
                if isSearching && searchText.isEmpty && !searchHistory.isEmpty {
                    // 历史记录视图
                    historyView
                } else if !searchText.isEmpty {
                    // 搜索结果视图
                    searchResultView
                } else {
                    // 默认内容
                    contentView
                }
            }
            .navigationTitle("SwiftUI SearchBar")
            .searchable(
                text: $searchText,
                placement: .navigationBarDrawer(displayMode: .always),
                prompt: "搜索..."
            )
            .searchScopes($selectedScope) {
                ForEach(searchScopes, id: \.self) { scope in
                    Text(scope).tag(scope)
                }
            }
            .searchSuggestions {
                if searchText.isEmpty {
                    // 历史记录建议
                    ForEach(searchHistory.prefix(5), id: \.self) { history in
                        Button {
                            searchText = history
                            performSearch()
                        } label: {
                            Label(history, systemImage: "clock")
                        }
                    }
                } else {
                    // 搜索建议
                    ForEach(suggestions, id: \.self) { suggestion in
                        Button(suggestion) {
                            searchText = suggestion
                            performSearch()
                        }
                    }
                }
            }
            .onSubmit(of: .search) {
                performSearch()
            }
            .onChange(of: selectedScope) { _ in
                if !searchText.isEmpty {
                    // 范围变化时重新搜索
                    performSearch()
                }
            }
            .onChange(of: isSearching) { newValue in
                if newValue && searchText.isEmpty {
                    // 显示历史记录
                    showingHistory = true
                } else {
                    showingHistory = false
                }
            }
            .toolbar {
                ToolbarItem(placement: .primaryAction) {
                    Menu {
                        Button(role: .destructive) {
                            searchHistory.removeAll()
                        } label: {
                            Label("清除历史", systemImage: "trash")
                        }
                        
                        Divider()
                        
                        ForEach(searchScopes, id: \.self) { scope in
                            Button {
                                selectedScope = scope
                            } label: {
                                if selectedScope == scope {
                                    Label(scope, systemImage: "checkmark")
                                } else {
                                    Text(scope)
                                }
                            }
                        }
                    } label: {
                        Image(systemName: "ellipsis.circle")
                    }
                }
            }
        }
        .onAppear {
            // 加载历史记录
            searchHistory = UserDefaults.standard.stringArray(forKey: "searchHistory") ?? []
        }
    }
    
    // 执行搜索操作
    private func performSearch() {
        guard !searchText.isEmpty else { return }
        
        // 添加到历史记录
        if !searchHistory.contains(searchText) {
            searchHistory.insert(searchText, at: 0)
            if searchHistory.count > 10 {
                searchHistory.removeLast()
            }
            
            // 保存历史记录
            UserDefaults.standard.set(searchHistory, forKey: "searchHistory")
        }
    }
    
    // 历史记录视图
    private var historyView: some View {
        VStack(alignment: .leading, spacing: 12) {
            HStack {
                Text("搜索历史")
                    .font(.headline)
                
                Spacer()
                
                Button("清除") {
                    searchHistory.removeAll()
                    UserDefaults.standard.removeObject(forKey: "searchHistory")
                }
                .foregroundColor(.accentColor)
            }
            .padding(.horizontal)
            
            List {
                ForEach(searchHistory, id: \.self) { history in
                    Button {
                        searchText = history
                        performSearch()
                    } label: {
                        HStack {
                            Image(systemName: "clock")
                                .foregroundColor(.gray)
                            Text(history)
                                .foregroundColor(.primary)
                            Spacer()
                            Image(systemName: "arrow.up.left")
                                .font(.caption)
                                .foregroundColor(.gray)
                        }
                    }
                }
            }
            .listStyle(.plain)
        }
    }
    
    // 搜索结果视图
    private var searchResultView: some View {
        VStack {
            if filteredItems.isEmpty {
                // 无结果视图
                VStack(spacing: 16) {
                    Image(systemName: "magnifyingglass")
                        .font(.largeTitle)
                        .foregroundColor(.gray)
                    
                    Text("未找到"\(searchText)"的相关结果")
                        .font(.headline)
                    
                    Text("请尝试其他关键词或切换搜索范围")
                        .font(.subheadline)
                        .foregroundColor(.gray)
                }
                .frame(maxWidth: .infinity, maxHeight: .infinity)
                .background(Color(.systemGroupedBackground))
            } else {
                // 结果列表
                List {
                    Section(header: Text("搜索结果 (\(filteredItems.count))")) {
                        ForEach(filteredItems, id: \.self) { item in
                            NavigationLink {
                                // 详情页面
                                VStack {
                                    Image(systemName: imageNameForItem(item))
                                        .font(.system(size: 60))
                                        .foregroundColor(.accentColor)
                                        .padding()
                                    
                                    Text(item)
                                        .font(.title)
                                    
                                    Text("这是关于\(item)的详细信息")
                                        .foregroundColor(.secondary)
                                        .padding()
                                    
                                    Spacer()
                                }
                                .padding()
                                .navigationTitle(item)
                            } label: {
                                HStack {
                                    Image(systemName: imageNameForItem(item))
                                        .foregroundColor(.accentColor)
                                    
                                    VStack(alignment: .leading) {
                                        Text(item)
                                        
                                        Text("类别: \(categoryForItem(item))")
                                            .font(.caption)
                                            .foregroundColor(.secondary)
                                    }
                                }
                            }
                        }
                    }
                }
                .listStyle(.insetGrouped)
            }
        }
    }
    
    // 默认内容视图
    private var contentView: some View {
        VStack(spacing: 20) {
            Image(systemName: "magnifyingglass")
                .font(.system(size: 60))
                .foregroundColor(.accentColor)
            
            Text("搜索产品")
                .font(.title)
            
            Text("请在上方搜索栏中输入关键词")
                .foregroundColor(.secondary)
            
            Spacer().frame(height: 40)
            
            // 热门搜索建议
            VStack(alignment: .leading) {
                Text("热门搜索")
                    .font(.headline)
                    .padding(.horizontal)
                
                ScrollView(.horizontal, showsIndicators: false) {
                    HStack(spacing: 10) {
                        ForEach(["手机", "平板", "电脑", "华为", "苹果", "小米"], id: \.self) { keyword in
                            Button {
                                searchText = keyword
                                performSearch()
                            } label: {
                                Text(keyword)
                                    .padding(.horizontal, 15)
                                    .padding(.vertical, 8)
                                    .background(Color.gray.opacity(0.1))
                                    .clipShape(Capsule())
                            }
                        }
                    }
                    .padding(.horizontal)
                }
            }
        }
        .padding()
        .frame(maxWidth: .infinity, maxHeight: .infinity)
        .background(Color(.systemGroupedBackground))
    }
    
    // 工具函数：根据项目获取图标
    private func imageNameForItem(_ item: String) -> String {
        if item.contains("手机") {
            return "iphone"
        } else if item.contains("平板") {
            return "ipad"
        } else if item.contains("电脑") {
            return "laptopcomputer"
        } else if item.contains("手表") {
            return "applewatch"
        } else if item.contains("电视") {
            return "tv"
        }
        return "questionmark.circle"
    }
    
    // 工具函数：根据项目获取类别
    private func categoryForItem(_ item: String) -> String {
        if item.contains("苹果") {
            return "Apple"
        } else if item.contains("安卓") {
            return "Android"
        } else if item.contains("华为") {
            return "Huawei"
        } else if item.contains("小米") {
            return "Xiaomi"
        } else if item.contains("三星") {
            return "Samsung"
        }
        return "其他"
    }
}

#Preview {
    SearchableExample()
}

## 鸿蒙 ArkUI SearchBar

### 搜索触发机制

ArkUI提供了专门的搜索组件和多种触发方式：

- `Search`组件：鸿蒙原生搜索框组件
- `TextInput`组件：可定制的基础文本输入组件
- `SearchEffect`效果：提供搜索视觉效果的装饰器

搜索触发方式包括：

- 输入文本变化（`onChange`回调）
- 提交搜索（`onSubmit`回调）
- 搜索按钮点击（`onSearch`回调）
- 取消按钮点击（`onCancel`回调）
- 控件焦点变化（`onFocus`和`onBlur`回调）
- 声控搜索（结合语音助手）

### 搜索建议支持

- 通过`Search`的`searchSuggest`插槽提供建议视图
- 支持实时更新的搜索建议
- 可自定义建议项外观和交互方式
- 支持异步加载搜索建议
- 支持分组和分类的建议列表
- 可实现带高亮的匹配文本展示
- 支持语音输入生成建议
- 提供建议项的滚动、分页展示

### 历史记录管理

- 可使用`AppStorage`或`LocalStorage`进行本地存储
- 支持历史记录的增删改查操作
- 提供历史记录排序功能（时间、频率）
- 支持历史记录最大数量限制
- 可实现历史记录加密存储
- 提供历史记录一键清除功能
- 可实现历史记录跨设备同步
- 支持历史记录智能推荐

### 搜索结果展示

- 使用`List`或`Grid`组件展示结果
- 支持瀑布流布局展示搜索结果
- 提供懒加载和虚拟列表优化
- 支持下拉刷新和上拉加载更多
- 可实现分组和分类展示
- 提供空结果状态的优雅处理
- 支持自定义结果项动效
- 适配多设备（手机、平板、大屏）

### 搜索范围配置

- 通过`Tabs`或`Segmented`组件实现范围切换
- 支持动态配置搜索范围
- 可将搜索范围与过滤器结合
- 支持层级化的搜索范围设置
- 提供范围切换状态保存功能
- 可实现范围配置的个性化定制
- 支持范围的动态增减
- 与分布式搜索系统集成

### 平台特性与兼容性

- 基于ArkTS声明式UI编程语言开发
- 完整支持鸿蒙原生设计规范
- 支持HarmonyOS 3.0及以上版本
- 适配手机、平板、可折叠设备、大屏设备
- 提供深色模式支持
- 完整的无障碍支持（含屏幕阅读）
- 支持从右到左(RTL)布局
- 与华为设备生态深度集成
- 优化的功耗和性能特性
- 支持超级终端协同搜索

### 完整示例代码

```typescript
// SearchPage.ets
import router from '@ohos.router';
import promptAction from '@ohos.promptAction';
import preferences from '@ohos.data.preferences';

@Entry
@Component
struct SearchPage {
  // 存储搜索历史的首选项
  private static readonly PREFERENCES_NAME = 'SearchHistory';
  private static readonly HISTORY_KEY = 'searchHistoryList';
  private static readonly MAX_HISTORY_COUNT = 10;
  
  // 状态数据
  @State searchText: string = '';
  @State isSearching: boolean = false;
  @State searchHistory: string[] = [];
  @State searchResults: string[] = [];
  @State searchSuggestions: string[] = [];
  @State selectedScope: number = 0;
  
  // 数据源
  private searchScopes: string[] = ['全部', '手机', '平板', '电脑', '手表'];
  private allItems: string[] = [
    '华为手机', '华为平板', '华为电脑', '华为手表',
    '荣耀手机', '荣耀平板', '荣耀电脑', '荣耀手表',
    '小米手机', '小米平板', '小米电视', '小米手表',
    '苹果手机', '苹果电脑', '苹果平板', '苹果手表',
    '三星手机', '三星平板', '三星电视', '三星手表'
  ];
  
  // 生命周期
  aboutToAppear() {
    this.loadSearchHistory();
  }
  
  // 搜索历史处理
  async loadSearchHistory() {
    try {
      const preferences = await preferences.getPreferences(getContext(this), SearchPage.PREFERENCES_NAME);
      const value = await preferences.get(SearchPage.HISTORY_KEY, '[]');
      this.searchHistory = JSON.parse(value.toString());
    } catch (error) {
      console.error('Failed to load search history:', error);
      this.searchHistory = [];
    }
  }
  
  async saveSearchHistory() {
    try {
      const preferences = await preferences.getPreferences(getContext(this), SearchPage.PREFERENCES_NAME);
      await preferences.put(SearchPage.HISTORY_KEY, JSON.stringify(this.searchHistory));
      await preferences.flush();
    } catch (error) {
      console.error('Failed to save search history:', error);
    }
  }
  
  addToSearchHistory(query: string) {
    if (!query.trim()) return;
    
    // 移除已存在的相同项
    const index = this.searchHistory.indexOf(query);
    if (index !== -1) {
      this.searchHistory.splice(index, 1);
    }
    
    // 添加到最前面
    this.searchHistory.unshift(query);
    
    // 限制历史记录数量
    if (this.searchHistory.length > SearchPage.MAX_HISTORY_COUNT) {
      this.searchHistory = this.searchHistory.slice(0, SearchPage.MAX_HISTORY_COUNT);
    }
    
    this.saveSearchHistory();
  }
  
  clearSearchHistory() {
    this.searchHistory = [];
    this.saveSearchHistory();
  }
  
  // 搜索处理
  updateSearchSuggestions(text: string) {
    if (!text.trim()) {
      this.searchSuggestions = [];
      return;
    }
    
    // 根据输入文本筛选建议
    this.searchSuggestions = this.allItems
      .filter(item => item.toLowerCase().includes(text.toLowerCase()))
      .slice(0, 5);
  }
  
  performSearch() {
    if (!this.searchText.trim()) {
      this.searchResults = [];
      return;
    }
    
    // 添加到历史记录
    this.addToSearchHistory(this.searchText);
    
    // 根据选择的范围筛选结果
    const scope = this.searchScopes[this.selectedScope];
    const lowerSearchText = this.searchText.toLowerCase();
    
    if (scope === '全部') {
      this.searchResults = this.allItems.filter(
        item => item.toLowerCase().includes(lowerSearchText)
      );
    } else {
      this.searchResults = this.allItems.filter(
        item => item.toLowerCase().includes(lowerSearchText) && 
                item.toLowerCase().includes(scope.toLowerCase())
      );
    }
    
    // 隐藏搜索建议
    this.searchSuggestions = [];
    
    // 退出搜索状态
    this.isSearching = false;
  }
  
  build() {
    Column() {
      // 顶部导航栏
      Row() {
        Text('ArkUI SearchBar')
          .fontSize(20)
          .fontWeight(FontWeight.Bold)
      }
      .width('100%')
      .height(56)
      .padding({ left: 16, right: 16 })
      .backgroundColor('#F1F3F5')
      
      // 搜索栏区域
      Search({ value: this.searchText, placeholder: '搜索...' })
        .searchButton('搜索')
        .width('100%')
        .height(40)
        .margin({ top: 8, bottom: 8 })
        .textFont({ size: 16 })
        .placeholderColor('#99000000')
        .placeholderFont({ size: 16 })
        .backgroundColor('#FFFFFF')
        .onSubmit(() => this.performSearch())
        .onChange((value: string) => {
          this.searchText = value;
          this.updateSearchSuggestions(value);
        })
        .onSearch(() => this.performSearch())
        .onFocus(() => {
          this.isSearching = true;
        })
        .onCancel(() => {
          this.isSearching = false;
          this.searchText = '';
          this.searchSuggestions = [];
        })
        .searchSuggest(
          // 搜索建议显示区域
          Column() {
            if (this.searchText === '' && this.searchHistory.length > 0) {
              // 显示历史记录
              Row() {
                Text('搜索历史')
                  .fontSize(16)
                  .fontWeight(FontWeight.Medium)
                
                Blank()
                
                Button({ type: ButtonType.Capsule }) {
                  Text('清除')
                    .fontSize(14)
                    .fontColor('#0A59F7')
                }
                .backgroundColor('transparent')
                .onClick(() => this.clearSearchHistory())
              }
              .width('100%')
              .padding({ left: 16, right: 16, top: 8, bottom: 8 })
              
              List() {
                ForEach(this.searchHistory, (history: string) => {
                  ListItem() {
                    Row() {
                      Image($r('app.media.ic_history'))
                        .width(20)
                        .height(20)
                        .margin({ right: 12 })
                      
                      Text(history)
                        .fontSize(16)
                    }
                    .width('100%')
                    .padding(16)
                    .onClick(() => {
                      this.searchText = history;
                      this.performSearch();
                    })
                  }
                })
              }
              .width('100%')
              .divider({ strokeWidth: 1, color: '#0D000000' })
            } else if (this.searchSuggestions.length > 0) {
              // 显示搜索建议
              Text('搜索建议')
                .fontSize(16)
                .fontWeight(FontWeight.Medium)
                .width('100%')
                .padding({ left: 16, right: 16, top: 8, bottom: 8 })
              
              List() {
                ForEach(this.searchSuggestions, (suggestion: string) => {
                  ListItem() {
                    Row() {
                      Image($r('app.media.ic_search_small'))
                        .width(20)
                        .height(20)
                        .margin({ right: 12 })
                      
                      Text(suggestion)
                        .fontSize(16)
                    }
                    .width('100%')
                    .padding(16)
                    .onClick(() => {
                      this.searchText = suggestion;
                      this.performSearch();
                    })
                  }
                })
              }
              .width('100%')
              .divider({ strokeWidth: 1, color: '#0D000000' })
            }
          }
          .width('100%')
          .backgroundColor('#FFFFFF')
        )
      
      // 搜索范围选择器
      if (!this.isSearching) {
        Tabs({ barPosition: BarPosition.Start, index: this.selectedScope }) {
          ForEach(this.searchScopes, (scope: string, index: number) => {
            TabContent() {
              if (this.searchResults.length === 0 && this.searchText) {
                // 空结果状态
                Column() {
                  Image($r('app.media.ic_search_empty'))
                    .width(120)
                    .height(120)
                    .margin({ bottom: 16 })
                  
                  Text(`未找到"${this.searchText}"的相关结果`)
                    .fontSize(16)
                    .fontColor('#000000')
                    .margin({ bottom: 8 })
                  
                  Text('请尝试其他关键词或切换搜索范围')
                    .fontSize(14)
                    .fontColor('#99000000')
                }
                .width('100%')
                .height('70%')
                .justifyContent(FlexAlign.Center)
                .alignItems(HorizontalAlign.Center)
              } else if (this.searchResults.length > 0) {
                // 搜索结果
                List() {
                  ForEach(this.searchResults, (item: string) => {
                    ListItem() {
                      Row() {
                        Image(this.getImageForItem(item))
                          .width(40)
                          .height(40)
                          .margin({ right: 16 })
                        
                        Column() {
                          Text(item)
                            .fontSize(16)
                            .fontWeight(FontWeight.Medium)
                            .margin({ bottom: 4 })
                          
                          Text(this.getCategoryForItem(item))
                            .fontSize(14)
                            .fontColor('#99000000')
                        }
                        .alignItems(HorizontalAlign.Start)
                        
                        Blank()
                        
                        Image($r('app.media.ic_arrow_right'))
                          .width(20)
                          .height(20)
                      }
                      .width('100%')
                      .padding(16)
                      .onClick(() => {
                        promptAction.showToast({
                          message: `您选择了: ${item}`,
                          duration: 2000
                        });
                      })
                    }
                  })
                }
                .width('100%')
                .divider({ strokeWidth: 1, color: '#0D000000' })
              } else if (this.searchText === '') {
                // 默认状态 - 搜索前
                Column() {
                  Image($r('app.media.ic_search_start'))
                    .width(120)
                    .height(120)
                    .margin({ bottom: 16 })
                  
                  Text('搜索产品')
                    .fontSize(20)
                    .fontWeight(FontWeight.Medium)
                    .margin({ bottom: 8 })
                  
                  Text('请在搜索栏中输入关键词')
                    .fontSize(16)
                    .fontColor('#99000000')
                    .margin({ bottom: 32 })
                  
                  // 热门搜索
                  Column() {
                    Text('热门搜索')
                      .fontSize(16)
                      .fontWeight(FontWeight.Medium)
                      .width('100%')
                      .margin({ bottom: 16 })
                    
                    Flex({ wrap: FlexWrap.Wrap, justifyContent: FlexAlign.Start }) {
                      ForEach(['手机', '平板', '电脑', '华为', '荣耀', '小米'], (keyword: string) => {
                        Text(keyword)
                          .fontSize(14)
                          .fontColor('#0A59F7')
                          .padding({ left: 16, right: 16, top: 8, bottom: 8 })
                          .backgroundColor('#E6EEF9')
                          .borderRadius(16)
                          .margin({ right: 8, bottom: 8 })
                          .onClick(() => {
                            this.searchText = keyword;
                            this.performSearch();
                          })
                      })
                    }
                  }
                  .width('100%')
                  .padding(16)
                  .borderRadius(8)
                  .backgroundColor('#F1F3F5')
                }
                .width('100%')
                .height('70%')
                .justifyContent(FlexAlign.Center)
                .alignItems(HorizontalAlign.Center)
                .padding(16)
              }
            }
            .tabBar(scope)
          })
        }
        .width('100%')
        .barHeight(48)
        .barMode(BarMode.Fixed)
        .onChange(index => {
          this.selectedScope = index;
          if (this.searchText) {
            this.performSearch();
          }
        })
      }
    }
    .width('100%')
    .height('100%')
  }
  
  // 辅助方法：获取项目对应的图片
  getImageForItem(item: string): ResourceStr {
    if (item.includes('手机')) {
      return $r('app.media.ic_smartphone');
    } else if (item.includes('平板')) {
      return $r('app.media.ic_tablet');
    } else if (item.includes('电脑')) {
      return $r('app.media.ic_laptop');
    } else if (item.includes('手表')) {
      return $r('app.media.ic_watch');
    } else if (item.includes('电视')) {
      return $r('app.media.ic_tv');
    }
    return $r('app.media.ic_device');
  }
  
  // 辅助方法：获取项目对应的分类
  getCategoryForItem(item: string): string {
    if (item.includes('华为')) {
      return '华为系列';
    } else if (item.includes('荣耀')) {
      return '荣耀系列';
    } else if (item.includes('小米')) {
      return '小米系列';
    } else if (item.includes('苹果')) {
      return '苹果系列';
    } else if (item.includes('三星')) {
      return '三星系列';
    }
    return '其他系列';
  }
}

## Vue SearchBar

### 搜索触发机制

Vue中可以使用多种UI库的搜索组件或自定义实现：

- 第三方UI库组件：Element UI、Vuetify、Ant Design Vue等
- 自定义组件：使用`v-model`绑定输入，灵活定制外观和行为
- 原生HTML组件：使用`<input type="search">`配合Vue事件处理

搜索触发方式包括：

- 输入文本变化（`v-model`和`input`事件）
- 点击搜索按钮（`@click`或`@search`事件）
- 按下回车键（`@keyup.enter`事件）
- 提交表单（`@submit.prevent`事件）
- 清除按钮点击（`@clear`事件）
- 支持节流和防抖优化

### 搜索建议支持

- 可实现下拉建议列表（`v-show`条件显示）
- 支持异步加载建议（Vue 3的`watchEffect`或Vue 2的`watch`）
- 可通过Composition API封装搜索建议逻辑
- 支持键盘导航建议列表（上下键选择）
- 可实现多种建议布局和样式
- 支持富文本建议项（图标、高亮匹配文本等）
- 可根据用户输入实时更新建议
- 支持建议项分组和分类

### 历史记录管理

- 可使用`localStorage`或`Vuex`/`Pinia`存储历史记录
- 支持历史记录的增删改查操作
- 提供基于时间或频率的排序功能
- 支持历史记录最大数量限制
- 可使用IndexedDB存储大量历史记录
- 支持历史记录云同步（结合后端API）
- 提供历史记录清除功能
- 支持智能历史记录推荐

### 搜索结果展示

- 使用Vue的条件渲染（`v-if`/`v-show`）切换结果视图
- 支持多种列表布局（表格、卡片、网格等）
- 提供无限滚动加载（`v-infinite-scroll`）
- 支持分页显示结果（`v-pagination`）
- 可实现结果的排序和筛选（计算属性或方法）
- 提供空结果状态优雅处理
- 支持动画过渡效果（`<transition>`和`<transition-group>`）
- 响应式适配各种屏幕尺寸

### 搜索范围配置

- 可使用单选按钮组、下拉菜单或标签页配置范围
- 支持范围与过滤器的组合使用
- 提供范围状态的管理（Vuex/Pinia或组件状态）
- 可实现层级化的搜索范围
- 支持保存用户的范围偏好
- 可动态调整可用的搜索范围
- 提供范围间的逻辑关系配置
- 支持范围切换的视觉反馈

### 平台特性与兼容性

- 跨框架兼容性：支持Vue 2和Vue 3
- 跨平台支持：适用于Web、移动端WebView、桌面应用（Electron）
- 响应式设计：完全适配各种尺寸设备
- 主题支持：明暗主题、自定义主题
- 国际化：多语言搜索支持
- 无障碍性：键盘导航、屏幕阅读器支持
- SEO友好：可实现服务端渲染(SSR)
- 单页应用集成：支持Vue Router的URL同步
- 性能优化：虚拟滚动、懒加载、缓存
- 易于扩展：支持自定义指令和插件

### 完整示例代码

```vue
<!-- SearchBar.vue -->
<template>
  <div class="search-container">
    <!-- 搜索区域 -->
    <div class="search-bar">
      <div class="search-input-wrapper" :class="{ active: isSearchFocused }">
        <i class="search-icon el-icon-search"></i>
        <input
          type="search"
          class="search-input"
          v-model="searchText"
          :placeholder="placeholder"
          @input="handleInput"
          @focus="handleFocus"
          @blur="handleBlur"
          @keyup.enter="performSearch"
          ref="searchInput"
        />
        <button v-if="searchText" class="clear-button" @click="clearSearch">
          <i class="el-icon-close"></i>
        </button>
        <button class="search-button" @click="performSearch">搜索</button>
      </div>
      
      <!-- 搜索范围选择器 -->
      <div class="search-scope">
        <el-radio-group v-model="selectedScope" size="small" @change="handleScopeChange">
          <el-radio-button v-for="scope in searchScopes" :key="scope" :label="scope">
            {{ scope }}
          </el-radio-button>
        </el-radio-group>
      </div>
      
      <!-- 搜索建议下拉框 -->
      <div 
        v-if="isSearchFocused && (showSuggestions || showHistory)" 
        class="suggestions-dropdown"
      >
        <!-- 搜索历史 -->
        <div v-if="showHistory && searchHistory.length > 0" class="search-history">
          <div class="suggestions-header">
            <span>搜索历史</span>
            <button class="text-button" @click="clearHistory">清除</button>
          </div>
          <ul class="suggestions-list">
            <li 
              v-for="(item, index) in searchHistory" 
              :key="'history-' + index" 
              @click="selectHistoryItem(item)"
              :class="{ 'selected': selectedSuggestionIndex === index && showHistory }"
            >
              <i class="el-icon-time"></i>
              <span>{{ item }}</span>
            </li>
          </ul>
          <div class="divider" v-if="searchSuggestions.length > 0"></div>
        </div>
        
        <!-- 搜索建议 -->
        <div v-if="showSuggestions && searchSuggestions.length > 0" class="search-suggestions">
          <div class="suggestions-header">
            <span>搜索建议</span>
          </div>
          <ul class="suggestions-list">
            <li 
              v-for="(suggestion, index) in searchSuggestions" 
              :key="'suggestion-' + index" 
              @click="selectSuggestion(suggestion)"
              :class="{ 'selected': selectedSuggestionIndex === (showHistory ? searchHistory.length + index : index) }"
            >
              <i class="el-icon-search"></i>
              <span v-html="highlightMatch(suggestion, searchText)"></span>
            </li>
          </ul>
        </div>
      </div>
    </div>
    
    <!-- 搜索结果区域 -->
    <div v-if="!isSearchFocused" class="search-results">
      <!-- 空搜索状态 -->
      <div v-if="!searchResults.length && searchPerformed" class="empty-results">
        <img src="./assets/empty-search.svg" alt="没有结果" />
        <h3>未找到"{{ searchText }}"的相关结果</h3>
        <p>请尝试其他关键词或切换搜索范围</p>
      </div>
      
      <!-- 初始状态 -->
      <div v-else-if="!searchResults.length && !searchPerformed" class="search-initial">
        <img src="./assets/search-illustration.svg" alt="搜索" />
        <h2>搜索产品</h2>
        <p>请在上方输入关键词进行搜索</p>
        
        <!-- 热门搜索标签 -->
        <div class="hot-search">
          <h4>热门搜索</h4>
          <div class="tag-cloud">
            <el-tag 
              v-for="tag in hotTags" 
              :key="tag"
              @click="quickSearch(tag)"
              size="medium"
              effect="plain"
            >
              {{ tag }}
            </el-tag>
          </div>
        </div>
      </div>
      
      <!-- 搜索结果列表 -->
      <div v-else class="results-list">
        <h3 class="results-header">
          搜索结果 ({{ searchResults.length }})
          <small v-if="selectedScope !== '全部'">· 范围: {{ selectedScope }}</small>
        </h3>
        
        <el-row :gutter="20">
          <el-col 
            v-for="(item, index) in searchResults" 
            :key="index" 
            :xs="24" :sm="12" :md="8" :lg="6"
          >
            <el-card class="result-card" shadow="hover" @click.native="selectResult(item)">
              <div class="result-icon">
                <i :class="getIconForItem(item)"></i>
              </div>
              <div class="result-content">
                <h4 class="result-title">{{ item }}</h4>
                <div class="result-category">{{ getCategoryForItem(item) }}</div>
              </div>
            </el-card>
          </el-col>
        </el-row>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed, watch, onMounted, nextTick } from 'vue';
import debounce from 'lodash/debounce';

export default {
  name: 'SearchBar',
  props: {
    placeholder: {
      type: String,
      default: '搜索...'
    }
  },
  setup() {
    // 数据源
    const allItems = [
      '苹果手机', '苹果电脑', '苹果平板', '苹果手表',
      '安卓手机', '安卓平板', '安卓手表',
      '华为手机', '华为平板', '华为电脑', '华为手表',
      '小米手机', '小米平板', '小米电视', '小米手表',
      '三星手机', '三星平板', '三星电视', '三星手表'
    ];
    
    // 热门标签
    const hotTags = ['手机', '平板', '电脑', '华为', '苹果', '小米'];
    
    // 状态
    const searchText = ref('');
    const searchResults = ref([]);
    const searchSuggestions = ref([]);
    const searchHistory = ref([]);
    const isSearchFocused = ref(false);
    const searchPerformed = ref(false);
    const selectedSuggestionIndex = ref(-1);
    const searchInput = ref(null);
    
    // 搜索范围
    const searchScopes = ['全部', '手机', '平板', '电脑', '手表'];
    const selectedScope = ref('全部');
    
    // 计算属性
    const showSuggestions = computed(() => 
      searchText.value.length > 0 && searchSuggestions.value.length > 0
    );
    
    const showHistory = computed(() => 
      searchText.value.length === 0 && searchHistory.value.length > 0
    );
    
    // 加载历史记录
    const loadSearchHistory = () => {
      try {
        const history = localStorage.getItem('searchHistory');
        if (history) {
          searchHistory.value = JSON.parse(history);
        }
      } catch (error) {
        console.error('Failed to load search history:', error);
        searchHistory.value = [];
      }
    };
    
    // 保存历史记录
    const saveSearchHistory = () => {
      try {
        localStorage.setItem('searchHistory', JSON.stringify(searchHistory.value));
      } catch (error) {
        console.error('Failed to save search history:', error);
      }
    };
    
    // 添加历史记录
    const addToHistory = (query) => {
      if (!query.trim()) return;
      
      // 移除已存在的相同记录
      const index = searchHistory.value.indexOf(query);
      if (index !== -1) {
        searchHistory.value.splice(index, 1);
      }
      
      // 添加到开头
      searchHistory.value.unshift(query);
      
      // 限制最大数量
      if (searchHistory.value.length > 10) {
        searchHistory.value = searchHistory.value.slice(0, 10);
      }
      
      saveSearchHistory();
    };
    
    // 清除历史记录
    const clearHistory = () => {
      searchHistory.value = [];
      saveSearchHistory();
    };
    
    // 生成搜索建议
    const generateSuggestions = debounce((text) => {
      if (!text.trim()) {
        searchSuggestions.value = [];
        return;
      }
      
      const lowerText = text.toLowerCase();
      searchSuggestions.value = allItems
        .filter(item => item.toLowerCase().includes(lowerText))
        .slice(0, 5);
    }, 300);
    
    // 执行搜索
    const performSearch = () => {
      if (!searchText.value.trim()) {
        searchResults.value = [];
        searchPerformed.value = false;
        return;
      }
      
      // 添加到历史记录
      addToHistory(searchText.value);
      
      // 根据搜索范围过滤结果
      const scope = selectedScope.value;
      const lowerText = searchText.value.toLowerCase();
      
      if (scope === '全部') {
        searchResults.value = allItems.filter(
          item => item.toLowerCase().includes(lowerText)
        );
      } else {
        searchResults.value = allItems.filter(
          item => item.toLowerCase().includes(lowerText) && 
                  item.toLowerCase().includes(scope.toLowerCase())
        );
      }
      
      // 标记搜索已执行
      searchPerformed.value = true;
      
      // 清除建议并失去焦点
      searchSuggestions.value = [];
      isSearchFocused.value = false;
      searchInput.value.blur();
    };
    
    // 清除搜索
    const clearSearch = () => {
      searchText.value = '';
      searchSuggestions.value = [];
      selectedSuggestionIndex.value = -1;
      nextTick(() => {
        searchInput.value.focus();
      });
    };
    
    // 选择历史记录项
    const selectHistoryItem = (item) => {
      searchText.value = item;
      performSearch();
    };
    
    // 选择建议项
    const selectSuggestion = (suggestion) => {
      searchText.value = suggestion;
      performSearch();
    };
    
    // 选择搜索结果
    const selectResult = (item) => {
      console.log('选择了结果:', item);
      // 这里可以导航到详情页或执行其他操作
    };
    
    // 快速搜索（热门标签）
    const quickSearch = (tag) => {
      searchText.value = tag;
      performSearch();
    };
    
    // 高亮匹配文本
    const highlightMatch = (text, query) => {
      if (!query) return text;
      
      const lowerText = text.toLowerCase();
      const lowerQuery = query.toLowerCase();
      const index = lowerText.indexOf(lowerQuery);
      
      if (index >= 0) {
        const before = text.substring(0, index);
        const match = text.substring(index, index + query.length);
        const after = text.substring(index + query.length);
        return `${before}<span class="highlight">${match}</span>${after}`;
      }
      
      return text;
    };
    
    // 搜索范围变化处理
    const handleScopeChange = () => {
      if (searchPerformed.value) {
        performSearch();
      }
    };
    
    // 搜索输入处理
    const handleInput = () => {
      generateSuggestions(searchText.value);
    };
    
    // 焦点处理
    const handleFocus = () => {
      isSearchFocused.value = true;
    };
    
    const handleBlur = () => {
      // 延迟失去焦点，以允许点击建议
      setTimeout(() => {
        isSearchFocused.value = false;
      }, 200);
    };
    
    // 获取项目对应的图标
    const getIconForItem = (item) => {
      if (item.includes('手机')) {
        return 'el-icon-mobile-phone';
      } else if (item.includes('平板')) {
        return 'el-icon-notebook-1';
      } else if (item.includes('电脑')) {
        return 'el-icon-monitor';
      } else if (item.includes('手表')) {
        return 'el-icon-timer';
      } else if (item.includes('电视')) {
        return 'el-icon-video-camera';
      }
      return 'el-icon-goods';
    };
    
    // 获取项目对应的分类
    const getCategoryForItem = (item) => {
      if (item.includes('苹果')) {
        return 'Apple';
      } else if (item.includes('安卓')) {
        return 'Android';
      } else if (item.includes('华为')) {
        return 'Huawei';
      } else if (item.includes('小米')) {
        return 'Xiaomi';
      } else if (item.includes('三星')) {
        return 'Samsung';
      }
      return '其他';
    };
    
    // 键盘导航建议列表
    const handleKeyDown = (e) => {
      const suggestions = showSuggestions.value ? searchSuggestions.value : [];
      const history = showHistory.value ? searchHistory.value : [];
      const totalItems = suggestions.length + history.length;
      
      if (totalItems === 0) return;
      
      if (e.key === 'ArrowDown') {
        e.preventDefault();
        selectedSuggestionIndex.value = (selectedSuggestionIndex.value + 1) % totalItems;
      } else if (e.key === 'ArrowUp') {
        e.preventDefault();
        selectedSuggestionIndex.value = (selectedSuggestionIndex.value - 1 + totalItems) % totalItems;
      } else if (e.key === 'Enter' && selectedSuggestionIndex.value !== -1) {
        e.preventDefault();
        if (selectedSuggestionIndex.value < history.length) {
          selectHistoryItem(history[selectedSuggestionIndex.value]);
        } else {
          selectSuggestion(suggestions[selectedSuggestionIndex.value - history.length]);
        }
      }
    };
    
    // 监听搜索文本变化重置选中的建议索引
    watch(searchText, () => {
      selectedSuggestionIndex.value = -1;
    });
    
    // 组件挂载
    onMounted(() => {
      loadSearchHistory();
      
      // 添加键盘事件监听
      window.addEventListener('keydown', handleKeyDown);
    });
    
    return {
      searchText,
      searchResults,
      searchSuggestions,
      searchHistory,
      searchPerformed,
      isSearchFocused,
      showSuggestions,
      showHistory,
      selectedSuggestionIndex,
      searchScopes,
      selectedScope,
      searchInput,
      hotTags,
      handleInput,
      handleFocus,
      handleBlur,
      performSearch,
      clearSearch,
      selectHistoryItem,
      selectSuggestion,
      selectResult,
      clearHistory,
      highlightMatch,
      handleScopeChange,
      quickSearch,
      getIconForItem,
      getCategoryForItem
    };
  }
};
</script>

<style scoped>
.search-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Helvetica Neue', Helvetica, 'PingFang SC', 'Hiragino Sans GB', Arial, sans-serif;
}

.search-bar {
  position: relative;
  margin-bottom: 20px;
}

.search-input-wrapper {
  display: flex;
  align-items: center;
  border: 2px solid #dcdfe6;
  border-radius: 4px;
  padding: 0 15px;
  background: #fff;
  transition: all 0.3s;
}

.search-input-wrapper.active {
  border-color: #409eff;
  box-shadow: 0 0 0 2px rgba(64, 158, 255, 0.2);
}

.search-icon {
  color: #909399;
  margin-right: 8px;
}

.search-input {
  flex: 1;
  height: 40px;
  border: none;
  outline: none;
  font-size: 16px;
  background: transparent;
}

.clear-button {
  background: none;
  border: none;
  cursor: pointer;
  color: #909399;
  font-size: 16px;
  padding: 0 8px;
}

.search-button {
  background: #409eff;
  color: white;
  border: none;
  padding: 0 20px;
  height: 40px;
  border-radius: 0 2px 2px 0;
  cursor: pointer;
  margin-right: -15px;
  transition: background 0.3s;
}

.search-button:hover {
  background: #66b1ff;
}

.search-scope {
  margin-top: 10px;
}

.suggestions-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background: white;
  border-radius: 4px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  z-index: 10;
  margin-top: 5px;
  max-height: 400px;
  overflow-y: auto;
}

.suggestions-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 15px;
  color: #606266;
  font-size: 14px;
  font-weight: bold;
}

.text-button {
  background: none;
  border: none;
  color: #409eff;
  cursor: pointer;
  font-size: 14px;
}

.suggestions-list {
  list-style: none;
  margin: 0;
  padding: 0;
}

.suggestions-list li {
  display: flex;
  align-items: center;
  padding: 10px 15px;
  cursor: pointer;
}

.suggestions-list li:hover,
.suggestions-list li.selected {
  background-color: #f5f7fa;
}

.suggestions-list li i {
  margin-right: 10px;
  color: #909399;
}

.divider {
  height: 1px;
  background: #ebeef5;
  margin: 5px 0;
}

.search-results {
  margin-top: 30px;
}

.empty-results {
  text-align: center;
  padding: 40px 0;
}

.empty-results img {
  width: 120px;
  margin-bottom: 20px;
}

.empty-results h3 {
  color: #303133;
  margin-bottom: 10px;
}

.empty-results p {
  color: #909399;
}

.search-initial {
  text-align: center;
  padding: 40px 0;
}

.search-initial img {
  width: 150px;
  margin-bottom: 20px;
}

.search-initial h2 {
  color: #303133;
  margin-bottom: 10px;
}

.search-initial p {
  color: #909399;
  margin-bottom: 30px;
}

.hot-search {
  max-width: 600px;
  margin: 0 auto;
  text-align: left;
}

.hot-search h4 {
  margin-bottom: 15px;
  color: #606266;
}

.tag-cloud {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  justify-content: center;
}

.tag-cloud .el-tag {
  cursor: pointer;
  font-size: 14px;
}

.results-header {
  margin-bottom: 20px;
  color: #303133;
}

.results-header small {
  color: #909399;
  font-weight: normal;
}

.result-card {
  margin-bottom: 20px;
  cursor: pointer;
  transition: transform 0.3s;
  height: 100%;
}

.result-card:hover {
  transform: translateY(-5px);
}

.result-icon {
  display: flex;
  justify-content: center;
  font-size: 40px;
  color: #409eff;
  margin-bottom: 15px;
}

.result-content {
  text-align: center;
}

.result-title {
  margin: 0 0 10px;
  color: #303133;
}

.result-category {
  color: #909399;
  font-size: 14px;
}

.highlight {
  color: #409eff;
  font-weight: bold;
}
</style>
```

## 总结对比

以下对六大框架的SearchBar组件进行多维度对比分析，帮助开发者根据项目需求选择最适合的方案。

### 搜索触发机制对比

| 框架 | 评分 | 特点 |
| --- | --- | --- |
| Flutter | ★★★★☆ | 提供丰富的触发机制，灵活的事件处理，但需要自行处理状态 |
| Jetpack Compose | ★★★★★ | Material 3设计规范，提供丰富的回调接口，内置防抖支持 |
| UIKit | ★★★★☆ | 完整的代理方法，与系统体验一致，但相对繁琐 |
| SwiftUI | ★★★★★ | 声明式API，简洁且内置多种修饰符，iOS 16+支持更丰富的特性 |
| ArkUI | ★★★★☆ | 丰富的事件API，以事件驱动为主，与鸿蒙体验一致 |
| Vue | ★★★★☆ | 灵活度最高，支持多种UI库集成，自定义性强 |

### 搜索建议支持对比

| 框架 | 评分 | 特点 |
| --- | --- | --- |
| Flutter | ★★★★☆ | 高度可定制但需要自行实现逻辑，无内置解决方案 |
| Jetpack Compose | ★★★★★ | Material 3内置支持，searchSuggestion功能完善，键盘导航支持良好 |
| UIKit | ★★★★☆ | 需要通过UISearchController实现，成熟可靠 |
| SwiftUI | ★★★★★ | iOS 16+的searchSuggestions提供原生支持，实现简洁 |
| ArkUI | ★★★★★ | 原生searchSuggest插槽，提供完整支持 |
| Vue | ★★★★☆ | 完全自定义，实现灵活但需要较多代码 |

### 历史记录管理对比

| 框架 | 评分 | 特点 |
| --- | --- | --- |
| Flutter | ★★★☆☆ | 需要自行实现存储逻辑，多种持久化方案可选 |
| Jetpack Compose | ★★★★☆ | 利用Android平台DataStore/Room存储，方案成熟 |
| UIKit | ★★★★☆ | NSUserDefaults或Core Data存储，生态成熟 |
| SwiftUI | ★★★★★ | AppStorage和SwiftData(iOS 17+)提供简洁API |
| ArkUI | ★★★★☆ | 提供preferences API，简洁实用 |
| Vue | ★★★★☆ | localStorage/IndexedDB支持，与Web API紧密集成 |

### 搜索结果展示对比

| 框架 | 评分 | 特点 |
| --- | --- | --- |
| Flutter | ★★★★☆ | 自由度高，多种布局组件可用，一致性好 |
| Jetpack Compose | ★★★★★ | LazyColumn/Grid原生支持，动画和过渡效果丰富 |
| UIKit | ★★★★☆ | UITableView/UICollectionView成熟稳定 |
| SwiftUI | ★★★★★ | List/LazyStack/Grid等组件简洁易用，动画支持好 |
| ArkUI | ★★★★☆ | List/Grid/虚拟列表支持，针对移动设备优化 |
| Vue | ★★★★★ | 多种方案可选，适配性最强，支持最多设备类型 |

### 搜索范围配置对比

| 框架 | 评分 | 特点 |
| --- | --- | --- |
| Flutter | ★★★☆☆ | 需要自行实现，无内置解决方案 |
| Jetpack Compose | ★★★★☆ | 结合FilterChip等组件，方案丰富 |
| UIKit | ★★★★★ | 内置scopeButtonTitles支持，原生体验 |
| SwiftUI | ★★★★★ | iOS 16+的searchScopes提供原生支持 |
| ArkUI | ★★★★☆ | 结合Tabs组件简洁实现 |
| Vue | ★★★★☆ | 方案灵活多样，但需要自行实现 |

### 平台特性与兼容性对比

| 框架 | 评分 | 特点 |
| --- | --- | --- |
| Flutter | ★★★★★ | 跨平台一致性最佳，但原生适配度稍弱 |
| Jetpack Compose | ★★★★☆ | 安卓平台体验一流，Material You支持，但仅限安卓 |
| UIKit | ★★★★☆ | iOS原生体验，版本兼容性强，但仅限iOS |
| SwiftUI | ★★★★☆ | Apple全平台支持，现代特性需iOS 15+/16+ |
| ArkUI | ★★★★☆ | 鸿蒙设备适配最佳，超级终端特性，但生态局限 |
| Vue | ★★★★★ | 覆盖平台最广，适配性最强，但需注意跨平台一致性 |

### 综合评价

- **Flutter**: 跨平台一致性最佳，适合追求多平台统一体验的项目，但部分高级功能需自行实现。
- **Jetpack Compose**: 安卓平台最佳选择，Material 3设计语言支持完善，现代化程度高。
- **UIKit**: iOS平台传统选择，成熟稳定，适合需要广泛兼容的iOS项目。
- **SwiftUI**: 现代Apple平台首选，声明式API大幅提高开发效率，但新特性要求较高系统版本。
- **ArkUI**: 鸿蒙系统首选，与系统深度集成，超级终端跨设备优势明显。
- **Vue**: Web平台最灵活选择，适配范围最广，可根据需求灵活定制。

### 推荐使用场景

- **需要最大跨平台一致性**: Flutter
- **安卓专属应用**: Jetpack Compose
- **iOS兼容性要求高**: UIKit
- **现代Apple生态**: SwiftUI
- **华为生态应用**: ArkUI
- **Web应用/跨平台Web容器**: Vue

各框架都有其独特优势，开发者应根据项目的具体需求、目标平台、团队技术栈和用户体验要求选择合适的方案。在某些情况下，混合使用不同框架（如原生移动应用+Vue的Web应用）可能是最佳选择。