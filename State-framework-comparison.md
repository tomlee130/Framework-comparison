# 六大框架状态管理API对比（2024）

本文档对比了Flutter、Android (Jetpack Compose)、UIKit、SwiftUI (iOS)、鸿蒙 (ArkUI) 和Vue框架的状态管理功能，基于2024年最新稳定版本。

## 目录
1. [核心状态管理](#1-核心状态管理)
2. [路由管理](#2-路由管理)
3. [参数传递](#3-参数传递)
4. [平台特性与兼容性](#4-平台特性与兼容性)
5. [完整实例](#5-完整实例)

## 1. 核心状态管理

### Flutter
Flutter提供了多种状态管理方式：

#### StatefulWidget
最基础的状态管理方式，适合简单场景：
```dart
class CounterWidget extends StatefulWidget {
  @override
  _CounterWidgetState createState() => _CounterWidgetState();
}

class _CounterWidgetState extends State<CounterWidget> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('$_counter'),
        ElevatedButton(
          onPressed: _incrementCounter,
          child: Text('增加'),
        ),
      ],
    );
  }
}
```

#### Provider
官方推荐的状态管理方案，基于InheritedWidget：
```dart
// 创建数据模型
class CounterModel extends ChangeNotifier {
  int _count = 0;
  int get count => _count;

  void increment() {
    _count++;
    notifyListeners();
  }
}

// 提供者
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (context) => CounterModel(),
      child: MyApp(),
    ),
  );
}

// 消费者
class CounterWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Consumer<CounterModel>(
      builder: (context, counter, child) {
        return Column(
          children: [
            Text('${counter.count}'),
            ElevatedButton(
              onPressed: () => counter.increment(),
              child: Text('增加'),
            ),
          ],
        );
      },
    );
  }
}
```

#### Riverpod (Provider的进阶版)
```dart
// 定义状态提供者
final counterProvider = StateNotifierProvider<CounterNotifier, int>((ref) {
  return CounterNotifier();
});

class CounterNotifier extends StateNotifier<int> {
  CounterNotifier() : super(0);
  
  void increment() => state = state + 1;
}

// 使用状态
class CounterWidget extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final count = ref.watch(counterProvider);
    
    return Column(
      children: [
        Text('$count'),
        ElevatedButton(
          onPressed: () => ref.read(counterProvider.notifier).increment(),
          child: Text('增加'),
        ),
      ],
    );
  }
}
```

#### Bloc
复杂应用中流行的状态管理方案：
```dart
// 事件
abstract class CounterEvent {}
class IncrementEvent extends CounterEvent {}

// 状态管理
class CounterBloc extends Bloc<CounterEvent, int> {
  CounterBloc() : super(0) {
    on<IncrementEvent>((event, emit) => emit(state + 1));
  }
}

// 使用
class CounterWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return BlocProvider(
      create: (context) => CounterBloc(),
      child: BlocBuilder<CounterBloc, int>(
        builder: (context, count) {
          return Column(
            children: [
              Text('$count'),
              ElevatedButton(
                onPressed: () => context.read<CounterBloc>().add(IncrementEvent()),
                child: Text('增加'),
              ),
            ],
          );
        },
      ),
    );
  }
}
```

### Android (Jetpack Compose)
Jetpack Compose采用声明式UI模式，提供多种状态管理机制：

#### 组件内状态 (remember)
适用于仅在单个组件内使用的状态：
```kotlin
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }
    
    Column {
        Text(text = "$count")
        Button(onClick = { count++ }) {
            Text("增加")
        }
    }
}
```

#### ViewModel + State
官方推荐的MVVM模式，结合StateFlow/LiveData：
```kotlin
// ViewModel
class CounterViewModel : ViewModel() {
    private val _count = MutableStateFlow(0)
    val count: StateFlow<Int> = _count.asStateFlow()
    
    fun increment() {
        _count.value++
    }
}

// 视图
@Composable
fun CounterScreen(viewModel: CounterViewModel = viewModel()) {
    val count by viewModel.count.collectAsState()
    
    Column {
        Text(text = "$count")
        Button(onClick = { viewModel.increment() }) {
            Text("增加")
        }
    }
}
```

#### Hilt依赖注入
结合ViewModel使用Hilt提供依赖注入：
```kotlin
@HiltViewModel
class CounterViewModel @Inject constructor(
    private val repository: CounterRepository
) : ViewModel() {
    private val _count = MutableStateFlow(0)
    val count: StateFlow<Int> = _count.asStateFlow()
    
    fun increment() {
        _count.value++
        repository.saveCount(_count.value)
    }
}

// 在Activity或Fragment中使用
@AndroidEntryPoint
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            val viewModel: CounterViewModel = hiltViewModel()
            CounterScreen(viewModel)
        }
    }
}
```

#### 可组合函数参数
通过参数提升状态，实现状态共享：
```kotlin
@Composable
fun CounterApp() {
    var count by remember { mutableStateOf(0) }
    
    CounterDisplay(count)
    CounterButton(onIncrement = { count++ })
}

@Composable
fun CounterDisplay(count: Int) {
    Text(text = "$count")
}

@Composable
fun CounterButton(onIncrement: () -> Unit) {
    Button(onClick = onIncrement) {
        Text("增加")
    }
}
```

### UIKit (iOS)
UIKit作为传统的命令式UI框架，主要通过以下方式管理状态：

#### 属性和委托模式
最基本的状态管理方式：
```swift
class CounterViewController: UIViewController {
    private var count = 0 {
        didSet {
            countLabel.text = "\(count)"
        }
    }
    
    private let countLabel = UILabel()
    private let incrementButton = UIButton()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
    }
    
    private func setupUI() {
        // 配置UI元素
        countLabel.text = "\(count)"
        incrementButton.setTitle("增加", for: .normal)
        incrementButton.addTarget(self, action: #selector(incrementCount), for: .touchUpInside)
        
        // 添加到视图层次
        view.addSubview(countLabel)
        view.addSubview(incrementButton)
        
        // 设置约束...
    }
    
    @objc private func incrementCount() {
        count += 1
    }
}
```

#### 通知中心
用于跨组件通信：
```swift
// 发送通知
NotificationCenter.default.post(name: NSNotification.Name("CountDidChange"), object: nil, userInfo: ["count": count])

// 接收通知
NotificationCenter.default.addObserver(self, selector: #selector(handleCountChange(_:)), name: NSNotification.Name("CountDidChange"), object: nil)

@objc func handleCountChange(_ notification: Notification) {
    if let count = notification.userInfo?["count"] as? Int {
        countLabel.text = "\(count)"
    }
}
```

#### KVO (Key-Value Observing)
观察对象属性变化：
```swift
// 添加观察
observation = counter.observe(\.value, options: [.new]) { [weak self] object, change in
    guard let newValue = change.newValue else { return }
    self?.countLabel.text = "\(newValue)"
}

// 定义可观察对象
class Counter: NSObject {
    @objc dynamic var value = 0
}
```

#### MVC/MVVM模式
结合其他技术，如绑定库或RxSwift：
```swift
// 使用RxSwift实现MVVM
class CounterViewModel {
    let count = BehaviorRelay<Int>(value: 0)
    
    func increment() {
        count.accept(count.value + 1)
    }
}

class CounterViewController: UIViewController {
    private let viewModel = CounterViewModel()
    private let countLabel = UILabel()
    private let disposeBag = DisposeBag()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // 绑定数据
        viewModel.count
            .map { "\($0)" }
            .bind(to: countLabel.rx.text)
            .disposed(by: disposeBag)
    }
}
```

### SwiftUI (iOS)
SwiftUI是声明式UI框架，提供了多种状态管理工具：

#### @State
用于组件内部状态：
```swift
struct CounterView: View {
    @State private var count = 0
    
    var body: some View {
        VStack {
            Text("\(count)")
            Button("增加") {
                count += 1
            }
        }
    }
}
```

#### @Binding
用于从父视图传递可变状态：
```swift
struct CounterView: View {
    @Binding var count: Int
    
    var body: some View {
        Button("增加") {
            count += 1
        }
    }
}

struct ParentView: View {
    @State private var count = 0
    
    var body: some View {
        VStack {
            Text("\(count)")
            CounterView(count: $count)
        }
    }
}
```

#### @ObservableObject
用于跨视图层次结构共享状态：
```swift
class CounterViewModel: ObservableObject {
    @Published var count = 0
    
    func increment() {
        count += 1
    }
}

struct CounterView: View {
    @ObservedObject var viewModel: CounterViewModel
    
    var body: some View {
        VStack {
            Text("\(viewModel.count)")
            Button("增加") {
                viewModel.increment()
            }
        }
    }
}

struct ContentView: View {
    @StateObject var viewModel = CounterViewModel()
    
    var body: some View {
        CounterView(viewModel: viewModel)
    }
}
```

#### @EnvironmentObject
通过环境注入依赖：
```swift
// 应用入口点
@main
struct MyApp: App {
    @StateObject var counterVM = CounterViewModel()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(counterVM)
        }
    }
}

// 使用环境对象
struct CounterView: View {
    @EnvironmentObject var viewModel: CounterViewModel
    
    var body: some View {
        VStack {
            Text("\(viewModel.count)")
            Button("增加") {
                viewModel.increment()
            }
        }
    }
}
```

#### SwiftData (iOS 17+)
最新的数据持久化框架：
```swift
// 模型定义
@Model
class Counter {
    var count: Int
    
    init(count: Int = 0) {
        self.count = count
    }
}

// 在视图中使用
struct CounterView: View {
    @Query var counters: [Counter]
    @Environment(\.modelContext) private var modelContext
    
    var body: some View {
        let counter = counters.first ?? Counter()
        
        VStack {
            Text("\(counter.count)")
            Button("增加") {
                counter.count += 1
            }
        }
        .onAppear {
            if counters.isEmpty {
                modelContext.insert(Counter())
            }
        }
    }
}
```

### 鸿蒙 (ArkUI)
鸿蒙ArkUI框架提供了以下状态管理机制：

#### @State
组件内状态管理装饰器：
```typescript
@Entry
@Component
struct CounterComponent {
  @State count: number = 0;

  build() {
    Column() {
      Text(`${this.count}`)
        .fontSize(20)
      
      Button('增加')
        .onClick(() => {
          this.count++;
        })
    }
    .width('100%')
    .padding(20)
  }
}
```

#### @Prop
用于从父组件接收状态：
```typescript
@Component
struct ChildComponent {
  @Prop count: number;  // 接收父组件传递的状态

  build() {
    Button('子组件增加')
      .onClick(() => {
        this.count++;  // 修改会同步回父组件
      })
  }
}

@Entry
@Component
struct ParentComponent {
  @State parentCount: number = 0;

  build() {
    Column() {
      Text(`父组件计数: ${this.parentCount}`)
        .fontSize(20)
      
      ChildComponent({ count: this.parentCount })
    }
    .width('100%')
    .padding(20)
  }
}
```

#### @Link
双向数据绑定装饰器：
```typescript
@Component
struct ChildComponent {
  @Link @Watch('onCountChange') count: number;

  onCountChange() {
    console.info(`Count changed to: ${this.count}`);
  }

  build() {
    Button('子组件增加')
      .onClick(() => {
        this.count++;
      })
  }
}

@Entry
@Component
struct ParentComponent {
  @State parentCount: number = 0;

  build() {
    Column() {
      Text(`父组件计数: ${this.parentCount}`)
        .fontSize(20)
      
      ChildComponent({ count: $parentCount })
    }
    .width('100%')
    .padding(20)
  }
}
```

#### @Provide/@Consume
跨组件层次结构共享状态：
```typescript
@Entry
@Component
struct GrandParentComponent {
  @Provide('countKey') count: number = 0;

  build() {
    Column() {
      Text(`祖父组件计数: ${this.count}`)
        .fontSize(20)
      
      Button('祖父组件增加')
        .onClick(() => {
          this.count++;
        })
      
      ParentComponent()
    }
    .width('100%')
    .padding(20)
  }
}

@Component
struct ParentComponent {
  build() {
    Column() {
      Text('父组件')
        .fontSize(16)
      
      ChildComponent()
    }
    .padding(10)
  }
}

@Component
struct ChildComponent {
  @Consume('countKey') count: number;

  build() {
    Column() {
      Text(`子组件计数: ${this.count}`)
        .fontSize(16)
      
      Button('子组件增加')
        .onClick(() => {
          this.count++;  // 修改会影响所有共享此状态的组件
        })
    }
    .padding(10)
  }
}
```

#### AppStorage
应用级全局状态管理：
```typescript
// 定义全局状态
AppStorage.SetOrCreate('globalCount', 0);

// 组件中使用
@Entry
@Component
struct CounterComponent {
  @StorageLink('globalCount') count: number = 0;

  build() {
    Column() {
      Text(`全局计数: ${this.count}`)
        .fontSize(20)
      
      Button('增加')
        .onClick(() => {
          this.count++;
        })
    }
    .width('100%')
    .padding(20)
  }
}
```

#### PersistentStorage
持久化状态管理：
```typescript
// 初始化持久化存储
PersistentStorage.PersistProp('persistentCount', 0);

@Entry
@Component
struct PersistentCounterComponent {
  @StorageProp('persistentCount') count: number = 0;

  build() {
    Column() {
      Text(`持久化计数: ${this.count}`)
        .fontSize(20)
      
      Button('增加')
        .onClick(() => {
          this.count++;
        })
    }
    .width('100%')
    .padding(20)
  }
}
```

### Vue
Vue提供了多层次的状态管理选项：

#### Vue 3 组合式API (Composition API)
使用`ref`、`reactive`和`computed`管理组件状态：

```vue
<template>
  <div>
    <p>{{ count }}</p>
    <button @click="increment">增加</button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>
```

#### Reactive对象
使用`reactive`创建响应式对象：

```vue
<template>
  <div>
    <p>{{ state.count }}</p>
    <button @click="increment">增加</button>
  </div>
</template>

<script setup>
import { reactive } from 'vue'

const state = reactive({
  count: 0
})

function increment() {
  state.count++
}
</script>
```

#### 计算属性
使用`computed`派生状态：

```vue
<template>
  <div>
    <p>计数: {{ count }}</p>
    <p>双倍计数: {{ doubleCount }}</p>
    <button @click="increment">增加</button>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const count = ref(0)
const doubleCount = computed(() => count.value * 2)

function increment() {
  count.value++
}
</script>
```

#### 组件间通信 (Props/Emits)
父子组件通信：

```vue
<!-- 子组件 (ChildCounter.vue) -->
<template>
  <div>
    <p>子组件计数: {{ modelValue }}</p>
    <button @click="$emit('update:modelValue', modelValue + 1)">增加</button>
  </div>
</template>

<script setup>
defineProps({
  modelValue: {
    type: Number,
    required: true
  }
})

defineEmits(['update:modelValue'])
</script>

<!-- 父组件 -->
<template>
  <div>
    <p>父组件计数: {{ count }}</p>
    <ChildCounter v-model="count" />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import ChildCounter from './ChildCounter.vue'

const count = ref(0)
</script>
```

#### Provide/Inject
跨多级组件传递状态：

```vue
<!-- 祖先组件 -->
<template>
  <div>
    <p>祖先组件计数: {{ count }}</p>
    <button @click="increment">增加</button>
    <ChildComponent />
  </div>
</template>

<script setup>
import { ref, provide } from 'vue'
import ChildComponent from './ChildComponent.vue'

const count = ref(0)
provide('count', count) // 提供响应式状态

function increment() {
  count.value++
}
</script>

<!-- 后代组件 -->
<template>
  <div>
    <p>后代组件获取到的计数: {{ count }}</p>
    <button @click="increment">在后代组件中增加</button>
  </div>
</template>

<script setup>
import { inject } from 'vue'

const count = inject('count')

function increment() {
  count.value++ // 直接修改注入的响应式引用
}
</script>
```

#### Pinia (官方推荐的状态管理库)
全局状态管理：

```javascript
// stores/counter.js
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0
  }),
  getters: {
    doubleCount: (state) => state.count * 2
  },
  actions: {
    increment() {
      this.count++
    },
    async incrementAsync() {
      await new Promise(resolve => setTimeout(resolve, 1000))
      this.increment()
    }
  }
})
```

```vue
<!-- 在组件中使用 -->
<template>
  <div>
    <p>计数: {{ counter.count }}</p>
    <p>双倍计数: {{ counter.doubleCount }}</p>
    <button @click="counter.increment">增加</button>
    <button @click="counter.incrementAsync">异步增加</button>
  </div>
</template>

<script setup>
import { useCounterStore } from '@/stores/counter'

const counter = useCounterStore()
</script>
```

#### 组合式Pinia
使用组合式API风格定义Store：

```javascript
// stores/counter.js
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const doubleCount = computed(() => count.value * 2)

  function increment() {
    count.value++
  }

  async function incrementAsync() {
    await new Promise(resolve => setTimeout(resolve, 1000))
    increment()
  }

  return { count, doubleCount, increment, incrementAsync }
})
```

## 2. 路由管理

### Flutter
Flutter提供了强大的路由和导航系统：

#### 基本导航
使用Navigator 1.0 API：
```dart
// 导航到新页面
Navigator.push(
  context,
  MaterialPageRoute(builder: (context) => SecondPage()),
);

// 返回上一页
Navigator.pop(context);

// 替换当前页面
Navigator.pushReplacement(
  context,
  MaterialPageRoute(builder: (context) => ReplacementPage()),
);
```

#### 命名路由
定义和使用命名路由：
```dart
// 在MaterialApp中定义路由
MaterialApp(
  initialRoute: '/',
  routes: {
    '/': (context) => HomePage(),
    '/details': (context) => DetailsPage(),
    '/settings': (context) => SettingsPage(),
  },
);

// 导航到命名路由
Navigator.pushNamed(context, '/details');

// 带参数导航
Navigator.pushNamed(
  context,
  '/details',
  arguments: {'id': 123, 'title': '产品详情'},
);

// 在目标页面获取参数
final args = ModalRoute.of(context)!.settings.arguments as Map<String, dynamic>;
```

#### Navigator 2.0
声明式导航API，适用于更复杂的导航逻辑：
```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      routerDelegate: MyRouterDelegate(),
      routeInformationParser: MyRouteInformationParser(),
    );
  }
}

// 路由委托
class MyRouterDelegate extends RouterDelegate<RouteConfig> {
  final List<Page> _pages = [];
  
  @override
  Widget build(BuildContext context) {
    return Navigator(
      pages: _pages,
      onPopPage: (route, result) {
        if (!route.didPop(result)) return false;
        _pages.removeLast();
        notifyListeners();
        return true;
      },
    );
  }
  
  // 更多方法实现...
}
```

#### GoRouter (推荐的第三方库)
简化路由管理的第三方库：
```dart
final router = GoRouter(
  routes: [
    GoRoute(
      path: '/',
      builder: (context, state) => HomePage(),
      routes: [
        GoRoute(
          path: 'details/:id',
          builder: (context, state) {
            final id = state.pathParameters['id'];
            return DetailsPage(id: id);
          },
        ),
      ],
    ),
  ],
);

// 在应用中使用
MaterialApp.router(
  routerConfig: router,
);

// 导航
context.go('/details/123');

// 带参数导航
context.goNamed(
  'details',
  pathParameters: {'id': '123'},
  queryParameters: {'tab': 'overview'},
);
```

### Android (Jetpack Compose)
Jetpack Compose Navigation提供了专为声明式UI设计的导航系统：

#### 基本导航
使用NavHost和NavController：
```kotlin
@Composable
fun AppNavigation() {
    val navController = rememberNavController()
    
    NavHost(navController = navController, startDestination = "home") {
        composable("home") {
            HomeScreen(
                onNavigateToDetails = { itemId ->
                    navController.navigate("details/$itemId")
                }
            )
        }
        
        composable(
            route = "details/{itemId}",
            arguments = listOf(navArgument("itemId") { type = NavType.StringType })
        ) { backStackEntry ->
            val itemId = backStackEntry.arguments?.getString("itemId")
            DetailsScreen(itemId = itemId)
        }
    }
}

// 在屏幕中使用
@Composable
fun HomeScreen(onNavigateToDetail: (String) -> Unit) {
    Button(onClick = { onNavigateToDetail("123") }) {
        Text("查看详情")
    }
}
```

#### 深层链接
支持从外部启动特定页面：
```kotlin
// 在NavHost中定义
NavHost(navController, startDestination = "home") {
    composable(
        "details/{itemId}",
        deepLinks = listOf(navDeepLink { 
            uriPattern = "myapp://details/{itemId}" 
        })
    ) { backStackEntry ->
        val itemId = backStackEntry.arguments?.getString("itemId")
        DetailsScreen(itemId = itemId)
    }
}
```

#### 导航选项
配置导航行为：
```kotlin
navController.navigate("profile") {
    // 避免在返回栈上重复创建相同目的地
    launchSingleTop = true
    // 从返回栈中弹出到特定目的地
    popUpTo("home") {
        // 是否包含弹出目的地
        inclusive = false
        // 是否保存状态
        saveState = true
    }
    // 恢复此目的地的状态
    restoreState = true
}
```

### UIKit (iOS)
UIKit使用UINavigationController和UITabBarController管理导航：

#### 导航控制器
基本推入和弹出页面：
```swift
// 推入新视图控制器
let detailVC = DetailViewController()
detailVC.itemId = "123"
navigationController?.pushViewController(detailVC, animated: true)

// 返回上一页
navigationController?.popViewController(animated: true)

// 返回到根视图
navigationController?.popToRootViewController(animated: true)
```

#### 故事板导航
使用Interface Builder配置导航：
```swift
// 代码中执行导航
performSegue(withIdentifier: "showDetail", sender: self)

// 准备导航
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    if segue.identifier == "showDetail" {
        if let detailVC = segue.destination as? DetailViewController,
           let cell = sender as? UITableViewCell,
           let indexPath = tableView.indexPath(for: cell) {
            let itemId = items[indexPath.row].id
            detailVC.itemId = itemId
        }
    }
}
```

#### 模态呈现
模态弹出页面：
```swift
// 呈现视图控制器
let settingsVC = SettingsViewController()
present(settingsVC, animated: true)

// 关闭模态视图
dismiss(animated: true)
```

#### 协调器模式
使用协调器封装导航逻辑：
```swift
protocol Coordinator {
    func start()
}

class MainCoordinator: Coordinator {
    var navigationController: UINavigationController
    
    init(navigationController: UINavigationController) {
        self.navigationController = navigationController
    }
    
    func start() {
        let vc = HomeViewController()
        vc.coordinator = self
        navigationController.pushViewController(vc, animated: false)
    }
    
    func showDetail(id: String) {
        let vc = DetailViewController()
        vc.itemId = id
        vc.coordinator = self
        navigationController.pushViewController(vc, animated: true)
    }
}
```

### SwiftUI (iOS)
SwiftUI提供了声明式导航系统：

#### NavigationStack/NavigationLink
使用NavigationStack（iOS 16+）：
```swift
struct ContentView: View {
    var body: some View {
        NavigationStack {
            List {
                NavigationLink("产品1", value: "1")
                NavigationLink("产品2", value: "2")
            }
            .navigationDestination(for: String.self) { id in
                DetailView(id: id)
            }
            .navigationTitle("产品列表")
        }
    }
}

struct DetailView: View {
    let id: String
    
    var body: some View {
        Text("产品详情 \(id)")
            .navigationTitle("详情")
    }
}
```

#### 导航路径
使用可观察导航路径（iOS 16+）：
```swift
struct ContentView: View {
    @State private var path = NavigationPath()
    
    var body: some View {
        NavigationStack(path: $path) {
            VStack {
                Button("跳转到详情") {
                    path.append("123")
                }
                Button("跳转到两个页面之后") {
                    path.append("123")
                    path.append(SettingsSection.privacy)
                }
                Button("返回首页") {
                    path = NavigationPath()
                }
            }
            .navigationDestination(for: String.self) { id in
                DetailView(id: id)
            }
            .navigationDestination(for: SettingsSection.self) { section in
                SettingsView(section: section)
            }
        }
    }
}

enum SettingsSection {
    case privacy, notifications
}
```

#### 深度链接
处理URL打开应用：
```swift
struct MyApp: App {
    @StateObject private var appState = AppState()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(appState)
                // 处理深度链接
                .onOpenURL { url in
                    appState.handleDeepLink(url)
                }
        }
    }
}

class AppState: ObservableObject {
    @Published var navigationPath = NavigationPath()
    
    func handleDeepLink(_ url: URL) {
        guard url.scheme == "myapp" else { return }
        if url.host == "product", let id = url.pathComponents.last {
            navigationPath.append(id)
        }
    }
}
```

### 鸿蒙 (ArkUI)
鸿蒙ArkUI提供页面路由和导航功能：

#### 页面路由Router
使用页面路由：
```typescript
@Entry
@Component
struct RouterExample {
  @State active: boolean = false;

  build() {
    Column() {
      // 条件渲染不同页面
      if (this.active) {
        DetailPage()
      } else {
        HomePage({ onNavigate: () => this.active = true })
      }
    }
  }
}

@Component
struct HomePage {
  onNavigate: () => void;

  build() {
    Column() {
      Text('首页')
      Button('前往详情页')
        .onClick(() => {
          this.onNavigate();
        })
    }
  }
}

@Component
struct DetailPage {
  build() {
    Text('详情页')
  }
}
```

#### 页面Router组件
使用Router声明式导航：
```typescript
@Entry
@Component
struct RouterExample {
  @State currentIndex: number = 0;

  build() {
    Column() {
      // 页面路由
      Router() {
        HomePage({ onNavigate: () => this.currentIndex = 1 })
      }.onRouter(this.currentIndex, () => '')

      Button('导航') {
        Text('前往详情页')
      }.onClick(() => {
        this.currentIndex = 1;
      })
    }
  }
}

@Component
struct HomePage {
  onNavigate: () => void;

  build() {
    Column() {
      Text('首页')
    }
  }
}
```

#### 路由Router.push
页面导航：
```typescript
@Entry
@Component
struct Index {
  @State message: string = 'Hello World'

  build() {
    Row() {
      Column() {
        Text(this.message)
          .fontSize(50)
          .fontWeight(FontWeight.Bold)

        Button('跳转到详情页')
          .onClick(() => {
            router.pushUrl({
              url: 'pages/Detail',
              params: {
                id: '123',
                title: '详情页'
              }
            });
          })
      }
      .width('100%')
    }
    .height('100%')
  }
}

// Detail.ets
@Entry
@Component
struct Detail {
  @State id: string = '';
  @State title: string = '';

  aboutToAppear() {
    // 获取路由参数
    const params = router.getParams();
    if (params) {
      this.id = params['id'] as string;
      this.title = params['title'] as string;
    }
  }

  build() {
    Row() {
      Column() {
        Text(`详情页: ${this.id}`)
          .fontSize(30)
        
        Button('返回')
          .onClick(() => {
            router.back();
          })
      }
      .width('100%')
    }
    .height('100%')
  }
}
```

### Vue
Vue Router提供了功能完善的客户端路由系统：

#### 基本路由配置
定义路由配置：
```javascript
// router/index.js
import { createRouter, createWebHistory } from 'vue-router'
import Home from '../views/Home.vue'

const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    // 路由懒加载
    component: () => import('../views/About.vue')
  },
  {
    path: '/users/:id',
    name: 'UserDetails',
    component: () => import('../views/UserDetails.vue'),
    props: true
  }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

export default router
```

#### 导航
在组件中使用路由导航：
```vue
<template>
  <div>
    <!-- 路由链接 -->
    <router-link to="/">首页</router-link>
    <router-link :to="{ name: 'About' }">关于</router-link>
    <router-link :to="{ name: 'UserDetails', params: { id: 123 } }">用户详情</router-link>
    
    <!-- 路由视图 -->
    <router-view />
    
    <!-- 编程式导航 -->
    <button @click="goToUser">查看用户</button>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()

function goToUser() {
  router.push({ name: 'UserDetails', params: { id: 123 } })
}
</script>
```

#### 嵌套路由
定义嵌套路由：
```javascript
const routes = [
  {
    path: '/user',
    component: User,
    children: [
      { path: '', component: UserHome },
      { path: 'profile', component: UserProfile },
      { path: 'posts', component: UserPosts }
    ]
  }
]
```

```vue
<!-- User.vue -->
<template>
  <div>
    <h2>用户中心</h2>
    <router-link to="/user">用户首页</router-link>
    <router-link to="/user/profile">用户资料</router-link>
    <router-link to="/user/posts">用户文章</router-link>
    
    <!-- 渲染嵌套路由 -->
    <router-view />
  </div>
</template>
```

#### 路由守卫
使用全局和组件路由守卫：
```javascript
// 全局前置守卫
router.beforeEach((to, from) => {
  // 检查用户是否登录，未登录则跳转到登录页
  const isAuthenticated = localStorage.getItem('token')
  if (to.meta.requiresAuth && !isAuthenticated) {
    return { name: 'Login', query: { redirect: to.fullPath } }
  }
})

// 路由元信息
const routes = [
  {
    path: '/admin',
    component: Admin,
    meta: { requiresAuth: true }
  }
]
```

```vue
<!-- 组件内守卫 -->
<script setup>
import { onBeforeRouteLeave, onBeforeRouteUpdate } from 'vue-router'

// 离开路由前确认
onBeforeRouteLeave((to, from) => {
  const answer = window.confirm('确定要离开吗？未保存的更改将丢失')
  if (!answer) return false
})

// 更新路由参数时重新获取数据
onBeforeRouteUpdate(async (to, from) => {
  if (to.params.id !== from.params.id) {
    await fetchData(to.params.id)
  }
})
</script>
```

#### 状态管理(Pinia)
使用Pinia在组件间共享状态：
```javascript
// stores/items.js
import { defineStore } from 'pinia'

export const useItemsStore = defineStore('items', {
  state: () => ({
    selectedId: '',
    selectedTitle: ''
  }),
  actions: {
    selectItem(id, title) {
      this.selectedId = id
      this.selectedTitle = title
    }
  }
})
```

```vue
<!-- 第一个组件 -->
<template>
  <div>
    <button @click="selectAndNavigate">选择并导航</button>
  </div>
</template>

<script setup>
import { useItemsStore } from '@/stores/items'
import { useRouter } from 'vue-router'

const itemsStore = useItemsStore()
const router = useRouter()

function selectAndNavigate() {
  itemsStore.selectItem('123', '产品详情')
  router.push('/details')
}
</script>

<!-- 第二个组件 -->
<template>
  <div>
    <h2>详情页</h2>
    <p>选中的ID: {{ itemsStore.selectedId }}</p>
    <p>选中的标题: {{ itemsStore.selectedTitle }}</p>
  </div>
</template>

<script setup>
import { useItemsStore } from '@/stores/items'

const itemsStore = useItemsStore()
</script>
```

#### Provide/Inject
使用依赖注入在组件层次中传递数据：
```vue
<!-- 祖先组件 -->
<template>
  <div>
    <button @click="selectItem('123', '产品详情')">选择项目</button>
    <router-view />
  </div>
</template>

<script setup>
import { ref, provide } from 'vue'

const selectedId = ref('')
const selectedTitle = ref('')

function selectItem(id, title) {
  selectedId.value = id
  selectedTitle.value = title
}

// 提供数据和方法
provide('selectedId', selectedId)
provide('selectedTitle', selectedTitle)
provide('selectItem', selectItem)
</script>

<!-- 后代组件（可以在组件树的任何位置） -->
<template>
  <div>
    <p>选中的ID: {{ selectedId }}</p>
    <p>选中的标题: {{ selectedTitle }}</p>
    <button @click="selectItem('456', '新项目')">修改选中项</button>
  </div>
</template>

<script setup>
import { inject } from 'vue'

const selectedId = inject('selectedId')
const selectedTitle = inject('selectedTitle')
const selectItem = inject('selectItem')
</script>
```

## 3. 参数传递

### Flutter
Flutter提供了多种参数传递机制：

#### 构造函数传参
最直接的参数传递方式：
```dart
// 定义接收参数的页面
class DetailPage extends StatelessWidget {
  final String id;
  final String title;
  
  const DetailPage({required this.id, required this.title, Key? key}) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(title)),
      body: Center(child: Text('ID: $id')),
    );
  }
}

// 传递参数
Navigator.push(
  context,
  MaterialPageRoute(builder: (context) => DetailPage(id: '123', title: '详情页')),
);
```

#### 命名路由参数
使用命名路由传递参数：
```dart
// 导航并传参
Navigator.pushNamed(
  context,
  '/details',
  arguments: {'id': '123', 'title': '详情页'},
);

// 接收参数
class DetailPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final args = ModalRoute.of(context)!.settings.arguments as Map<String, dynamic>;
    final id = args['id'] as String;
    final title = args['title'] as String;
    
    return Scaffold(
      appBar: AppBar(title: Text(title)),
      body: Center(child: Text('ID: $id')),
    );
  }
}
```

#### GoRouter参数
使用GoRouter传递参数：
```dart
// 路由定义
GoRoute(
  path: 'details/:id',
  name: 'details',
  builder: (context, state) {
    final id = state.pathParameters['id']!;
    final title = state.queryParameters['title'] ?? '详情页';
    return DetailPage(id: id, title: title);
  },
),

// 传递参数
context.go('/details/123?title=产品详情');
// 或
context.goNamed(
  'details',
  pathParameters: {'id': '123'},
  queryParameters: {'title': '产品详情'},
);
```

#### Provider跨页面状态共享
使用Provider在页面间共享数据：
```dart
// 定义模型
class AppState extends ChangeNotifier {
  String selectedId = '';
  
  void selectItem(String id) {
    selectedId = id;
    notifyListeners();
  }
}

// 在应用顶层提供状态
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (context) => AppState(),
      child: MyApp(),
    ),
  );
}

// 在页面中访问
class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Consumer<AppState>(
      builder: (context, appState, child) {
        return ElevatedButton(
          onPressed: () {
            appState.selectItem('123');
            Navigator.pushNamed(context, '/details');
          },
          child: Text('跳转到详情页'),
        );
      },
    );
  }
}

class DetailPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Consumer<AppState>(
      builder: (context, appState, child) {
        return Center(
          child: Text('选中项ID: ${appState.selectedId}'),
        );
      },
    );
  }
}
```

### Android (Jetpack Compose)
Jetpack Compose提供了多种传递参数的方式：

#### 导航参数
使用NavHost传递和接收参数：
```kotlin
// 定义路由
NavHost(navController, startDestination = "home") {
    composable("home") {
        HomeScreen(onNavigateToDetail = { id, title ->
            navController.navigate("details/$id?title=$title")
        })
    }
    
    composable(
        route = "details/{id}?title={title}",
        arguments = listOf(
            navArgument("id") { type = NavType.StringType },
            navArgument("title") { 
                type = NavType.StringType 
                defaultValue = "详情页"
            }
        )
    ) { backStackEntry ->
        val id = backStackEntry.arguments?.getString("id") ?: ""
        val title = backStackEntry.arguments?.getString("title") ?: "详情页"
        DetailScreen(id = id, title = title)
    }
}

// 在屏幕组件中传递参数
@Composable
fun HomeScreen(onNavigateToDetail: (String, String) -> Unit) {
    Button(onClick = { onNavigateToDetail("123", "产品详情") }) {
        Text("查看详情")
    }
}

// 接收参数
@Composable
fun DetailScreen(id: String, title: String) {
    Column {
        Text("ID: $id")
        Text("标题: $title")
    }
}
```

#### SavedStateHandle
用于在流程变化后(如配置变更)保留参数：
```kotlin
class DetailViewModel(
    savedStateHandle: SavedStateHandle
) : ViewModel() {
    // 读取savedStateHandle中的状态
    val count: MutableState<Int> = savedStateHandle.getStateFlow("count", 0)
        .collectAsState(0)
    
    fun increment() {
        val newValue = count.value + 1
        count.value = newValue
        // 自动保存到savedStateHandle
        savedStateHandle["count"] = newValue
    }
}

@Composable
fun DetailScreen(
    viewModel: DetailViewModel = hiltViewModel()
) {
    // 使用ViewModel中的数据
}
```

#### 共享ViewModel
屏幕间共享状态：
```kotlin
// 共享ViewModel
class SharedViewModel : ViewModel() {
    private val _selectedId = MutableStateFlow("")
    val selectedId: StateFlow<String> = _selectedId.asStateFlow()
    
    fun selectItem(id: String) {
        _selectedId.value = id
    }
}

// 在导航宿主中提供共享ViewModel
@Composable
fun AppNavigation() {
    val navController = rememberNavController()
    val sharedViewModel: SharedViewModel = viewModel()
    
    NavHost(navController, startDestination = "home") {
        composable("home") {
            HomeScreen(
                sharedViewModel = sharedViewModel,
                onNavigateToDetail = { navController.navigate("details") }
            )
        }
        
        composable("details") {
            DetailScreen(sharedViewModel = sharedViewModel)
        }
    }
}

// 在第一个屏幕中设置值
@Composable
fun HomeScreen(
    sharedViewModel: SharedViewModel,
    onNavigateToDetail: () -> Unit
) {
    Button(onClick = { 
        sharedViewModel.selectItem("123")
        onNavigateToDetail()
    }) {
        Text("查看详情")
    }
}

// 在第二个屏幕中使用值
@Composable
fun DetailScreen(sharedViewModel: SharedViewModel) {
    val selectedId by sharedViewModel.selectedId.collectAsState()
    
    Text("选中项ID: $selectedId")
}
```

### UIKit (iOS)
UIKit提供了多种参数传递方式：

#### 直接属性传递
最简单的参数传递方式：
```swift
// 目标视图控制器定义属性
class DetailViewController: UIViewController {
    var itemId: String?
    var title: String?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // 使用传递的参数
        if let itemId = itemId {
            print("Item ID: \(itemId)")
        }
    }
}

// 传递参数
let detailVC = DetailViewController()
detailVC.itemId = "123"
detailVC.title = "产品详情"
navigationController?.pushViewController(detailVC, animated: true)
```

#### Segue传参
使用故事板Segue传递参数：
```swift
// 准备导航
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    if segue.identifier == "showDetail" {
        if let detailVC = segue.destination as? DetailViewController,
           let cell = sender as? UITableViewCell,
           let indexPath = tableView.indexPath(for: cell) {
            let itemId = items[indexPath.row].id
            detailVC.itemId = itemId
        }
    }
}
```

#### 委托模式
使用委托在视图控制器间通信：
```swift
// 定义委托协议
protocol ItemSelectionDelegate: AnyObject {
    func didSelectItem(id: String)
}

// 接收方实现协议
class MainViewController: UIViewController, ItemSelectionDelegate {
    func didSelectItem(id: String) {
        // 处理所选项
        print("选中项ID: \(id)")
    }
    
    func showPicker() {
        let pickerVC = ItemPickerViewController()
        pickerVC.delegate = self
        present(pickerVC, animated: true)
    }
}

// 发送方使用委托
class ItemPickerViewController: UIViewController {
    weak var delegate: ItemSelectionDelegate?
    
    func selectItem(id: String) {
        delegate?.didSelectItem(id: id)
        dismiss(animated: true)
    }
}
```

#### 通知中心
使用通知进行松散耦合的通信：
```swift
// 发布通知
NotificationCenter.default.post(
    name: NSNotification.Name("ItemSelected"),
    object: nil,
    userInfo: ["itemId": "123"]
)

// 观察通知
NotificationCenter.default.addObserver(
    self,
    selector: #selector(handleItemSelection(_:)),
    name: NSNotification.Name("ItemSelected"),
    object: nil
)

@objc func handleItemSelection(_ notification: Notification) {
    if let itemId = notification.userInfo?["itemId"] as? String {
        print("通过通知收到选中项ID: \(itemId)")
    }
}
```

### SwiftUI (iOS)
SwiftUI提供了现代化的参数传递方式：

#### 视图初始化
通过视图初始化传递参数：
```swift
struct DetailView: View {
    let id: String
    let title: String
    
    var body: some View {
        VStack {
            Text("ID: \(id)")
            Text("标题: \(title)")
        }
    }
}

// 传递参数
NavigationLink {
    DetailView(id: "123", title: "产品详情")
} label: {
    Text("查看详情")
}
```

#### 环境对象
使用环境对象在视图层次间共享数据：
```swift
// 定义共享数据
class AppData: ObservableObject {
    @Published var selectedId: String = ""
    
    func selectItem(id: String) {
        selectedId = id
    }
}

// 在顶层提供环境对象
@main
struct MyApp: App {
    @StateObject private var appData = AppData()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environmentObject(appData)
        }
    }
}

// 在第一个视图中设置值
struct HomeView: View {
    @EnvironmentObject var appData: AppData
    
    var body: some View {
        Button("选择项目") {
            appData.selectItem(id: "123")
        }
    }
}

// 在另一个视图中访问值
struct DetailView: View {
    @EnvironmentObject var appData: AppData
    
    var body: some View {
        Text("选中项ID: \(appData.selectedId)")
    }
}
```

#### NavigationPath与参数结合
使用NavigationStack管理视图堆栈和参数：
```swift
// 定义路由目标和参数
enum Destination: Hashable {
    case detail(id: String, title: String)
    case settings
}

struct ContentView: View {
    @State private var path = NavigationPath()
    
    var body: some View {
        NavigationStack(path: $path) {
            List {
                Button("查看产品详情") {
                    path.append(Destination.detail(id: "123", title: "产品详情"))
                }
                
                Button("设置") {
                    path.append(Destination.settings)
                }
            }
            .navigationDestination(for: Destination.self) { destination in
                switch destination {
                case .detail(let id, let title):
                    DetailView(id: id, title: title)
                case .settings:
                    SettingsView()
                }
            }
        }
    }
}
```

### 鸿蒙 (ArkUI)
鸿蒙ArkUI提供了多种参数传递方式：

#### 页面参数传递
使用router传递页面参数：
```typescript
// 传递参数
router.pushUrl({
  url: 'pages/Detail',
  params: {
    id: '123',
    title: '详情页'
  }
});

// 接收参数
@Entry
@Component
struct DetailPage {
  @State id: string = '';
  @State title: string = '';

  aboutToAppear() {
    const params = router.getParams();
    if (params) {
      this.id = params['id'] as string;
      this.title = params['title'] as string;
    }
  }

  build() {
    Column() {
      Text(`ID: ${this.id}`)
      Text(`标题: ${this.title}`)
    }
  }
}
```

#### 父子组件通信
使用@State和@Prop/@Link：
```typescript
// 子组件定义
@Component
struct ChildComponent {
  @Prop itemId: string;  // 只读属性
  @Link title: string;   // 双向绑定属性
  
  build() {
    Column() {
      Text(`ID: ${this.itemId}`)
      Text(`标题: ${this.title}`)
      
      Button('修改标题')
        .onClick(() => {
          this.title = '新标题'; // 修改会影响父组件
        })
    }
  }
}

// 父组件
@Entry
@Component
struct ParentComponent {
  @State itemId: string = '123';
  @State title: string = '详情页';
  
  build() {
    Column() {
      Text('父组件')
      
      // 传递参数
      ChildComponent({ 
        itemId: this.itemId,  // 传递只读属性
        title: $title         // 传递双向绑定属性
      })
      
      Text(`父组件中的标题: ${this.title}`)
    }
  }
}
```

#### 全局应用状态
使用AppStorage共享数据：
```typescript
// 定义全局状态
AppStorage.SetOrCreate('selectedId', '');

// 第一个页面设置值
@Entry
@Component
struct HomePage {
  @StorageLink('selectedId') selectedId: string = '';
  
  build() {
    Column() {
      Button('选择项目')
        .onClick(() => {
          this.selectedId = '123';
          router.pushUrl({ url: 'pages/Detail' });
        })
    }
  }
}

// 第二个页面使用值
@Entry
@Component
struct DetailPage {
  @StorageLink('selectedId') selectedId: string = '';
  
  build() {
    Column() {
      Text(`选中项ID: ${this.selectedId}`)
    }
  }
}
```

#### 事件总线
使用EventHub实现组件间通信：
```typescript
// 创建事件总线
const eventHub = new EventHub();

// 发送事件
@Entry
@Component
struct SenderComponent {
  build() {
    Button('发送数据')
      .onClick(() => {
        eventHub.emit('itemSelected', { id: '123', title: '产品详情' });
      })
  }
}

// 接收事件
@Entry
@Component
struct ReceiverComponent {
  @State itemId: string = '';
  @State title: string = '';
  
  aboutToAppear() {
    // 订阅事件
    eventHub.on('itemSelected', (data) => {
      this.itemId = data.id;
      this.title = data.title;
    });
  }
  
  aboutToDisappear() {
    // 移除订阅
    eventHub.off('itemSelected');
  }
  
  build() {
    Column() {
      Text(`接收到的ID: ${this.itemId}`)
      Text(`接收到的标题: ${this.title}`)
    }
  }
}
```

### Vue
Vue提供了多种传递参数的方式：

#### 路由参数
使用Vue Router传递和接收参数：
```javascript
// 定义路由
const routes = [
  {
    path: '/details/:id',
    name: 'Details',
    component: () => import('../views/Details.vue'),
    props: true
  }
]
```

```vue
<!-- 传递参数 -->
<template>
  <div>
    <router-link :to="{ name: 'Details', params: { id: '123' }, query: { title: '产品详情' } }">
      查看详情
    </router-link>
    
    <button @click="navigateToDetails">使用编程方式导航</button>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()

function navigateToDetails() {
  router.push({
    name: 'Details',
    params: { id: '123' },
    query: { title: '产品详情' }
  })
}
</script>
```

```vue
<!-- 接收参数 -->
<template>
  <div>
    <h2>详情页</h2>
    <p>ID: {{ id }}</p>
    <p>标题: {{ $route.query.title }}</p>
  </div>
</template>

<script setup>
// 使用props接收路由参数
defineProps({
  id: {
    type: String,
    required: true
  }
})

// 或者使用useRoute访问参数
// import { useRoute } from 'vue-router'
// const route = useRoute()
// const id = route.params.id
// const title = route.query.title
</script>
```

#### 组件Props
父子组件通信：
```vue
<!-- 子组件 (ChildComponent.vue) -->
<template>
  <div>
    <p>ID: {{ id }}</p>
    <p>标题: {{ title }}</p>
    <button @click="$emit('update:title', '新标题')">修改标题</button>
  </div>
</template>

<script setup>
defineProps({
  id: {
    type: String,
    required: true
  },
  title: {
    type: String,
    default: '默认标题'
  }
})

defineEmits(['update:title'])
</script>

<!-- 父组件 -->
<template>
  <div>
    <h2>父组件</h2>
    <p>标题: {{ title }}</p>
    
    <ChildComponent :id="id" v-model:title="title" />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import ChildComponent from './ChildComponent.vue'

const id = ref('123')
const title = ref('产品详情')
</script>
```

#### 状态管理(Pinia)
使用Pinia在组件间共享状态：
```javascript
// stores/items.js
import { defineStore } from 'pinia'

export const useItemsStore = defineStore('items', {
  state: () => ({
    selectedId: '',
    selectedTitle: ''
  }),
  actions: {
    selectItem(id, title) {
      this.selectedId = id
      this.selectedTitle = title
    }
  }
})
```

```vue
<!-- 第一个组件 -->
<template>
  <div>
    <button @click="selectAndNavigate">选择并导航</button>
  </div>
</template>

<script setup>
import { useItemsStore } from '@/stores/items'
import { useRouter } from 'vue-router'

const itemsStore = useItemsStore()
const router = useRouter()

function selectAndNavigate() {
  itemsStore.selectItem('123', '产品详情')
  router.push('/details')
}
</script>

<!-- 第二个组件 -->
<template>
  <div>
    <h2>详情页</h2>
    <p>选中的ID: {{ itemsStore.selectedId }}</p>
    <p>选中的标题: {{ itemsStore.selectedTitle }}</p>
  </div>
</template>

<script setup>
import { useItemsStore } from '@/stores/items'

const itemsStore = useItemsStore()
</script>
```

#### Provide/Inject
使用依赖注入在组件层次中传递数据：
```vue
<!-- 祖先组件 -->
<template>
  <div>
    <button @click="selectItem('123', '产品详情')">选择项目</button>
    <router-view />
  </div>
</template>

<script setup>
import { ref, provide } from 'vue'

const selectedId = ref('')
const selectedTitle = ref('')

function selectItem(id, title) {
  selectedId.value = id
  selectedTitle.value = title
}

// 提供数据和方法
provide('selectedId', selectedId)
provide('selectedTitle', selectedTitle)
provide('selectItem', selectItem)
</script>

<!-- 后代组件（可以在组件树的任何位置） -->
<template>
  <div>
    <p>选中的ID: {{ selectedId }}</p>
    <p>选中的标题: {{ selectedTitle }}</p>
    <button @click="selectItem('456', '新项目')">修改选中项</button>
  </div>
</template>

<script setup>
import { inject } from 'vue'

const selectedId = inject('selectedId')
const selectedTitle = inject('selectedTitle')
const selectItem = inject('selectItem')
</script>
```

## 4. 平台特性与兼容性

### Flutter
Flutter以跨平台一致性著称，同时提供了访问平台特性的方式：

#### 平台自适应组件
根据平台显示相应风格的UI：
```dart
// 自适应组件
import 'package:flutter/material.dart';
import 'package:flutter/cupertino.dart';

class AdaptiveWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    final platform = Theme.of(context).platform;
    
    // 根据平台选择不同组件
    return platform == TargetPlatform.iOS
        ? CupertinoButton(
            child: Text('iOS风格按钮'),
            onPressed: () {},
          )
        : ElevatedButton(
            child: Text('Material风格按钮'),
            onPressed: () {},
          );
  }
}
```

#### 平台通道
通过方法通道与原生代码交互：
```dart
// 定义方法通道
static const platform = MethodChannel('com.example.app/battery');

// 调用原生方法
Future<void> getBatteryLevel() async {
  try {
    final int result = await platform.invokeMethod('getBatteryLevel');
    batteryLevel = result;
  } on PlatformException catch (e) {
    batteryLevel = -1;
  }
}
```

#### 条件导入
根据平台导入不同实现：
```dart
// 接口定义
abstract class PlatformService {
  Future<void> shareContent(String text);
}

// 导入不同平台实现
import 'platform_service.dart'
    if (dart.library.io) 'mobile_platform_service.dart'
    if (dart.library.html) 'web_platform_service.dart';

// 使用统一接口
final service = PlatformService();
service.shareContent('分享内容');
```

#### 状态管理兼容性
Flutter的状态管理方案在各平台表现一致：
- StatefulWidget: 所有平台支持
- Provider/Riverpod: 所有平台支持
- Bloc: 所有平台支持

### Android (Jetpack Compose)
Jetpack Compose专为Android平台设计：

#### 平台集成
与Android系统紧密集成：
```kotlin
// 系统UI集成
@Composable
fun SystemBarsEffect() {
    val systemUiController = rememberSystemUiController()
    val useDarkIcons = MaterialTheme.colors.isLight
    
    DisposableEffect(systemUiController, useDarkIcons) {
        systemUiController.setSystemBarsColor(
            color = Color.Transparent,
            darkIcons = useDarkIcons
        )
        onDispose {}
    }
}
```

#### 与View系统互操作
集成传统View和Compose：
```kotlin
// 在Compose中嵌入View
@Composable
fun MapViewComposable() {
    AndroidView(
        factory = { context ->
            MapView(context).apply {
                // 配置MapView
            }
        },
        update = { mapView ->
            // 更新MapView
        }
    )
}

// 在View中嵌入Compose
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        setContentView(R.layout.activity_main)
        
        // 在View布局中找到ComposeView
        val composeView = findViewById<ComposeView>(R.id.compose_view)
        composeView.setContent {
            MaterialTheme {
                MyComposableUI()
            }
        }
    }
}
```

#### 状态保存
处理配置变更和进程死亡：
```kotlin
// 在ViewModel中使用SavedStateHandle
class MyViewModel(
    private val savedStateHandle: SavedStateHandle
) : ViewModel() {
    // 读取savedStateHandle中的状态
    val count: MutableState<Int> = savedStateHandle.getStateFlow("count", 0)
        .collectAsState(0)
    
    fun increment() {
        val newValue = count.value + 1
        count.value = newValue
        // 自动保存到savedStateHandle
        savedStateHandle["count"] = newValue
    }
}
```

#### 状态管理兼容性
Jetpack Compose状态管理仅适用于Android平台：
- `remember`: 仅Android
- ViewModel + Compose: 仅Android，但ViewModel概念可跨平台
- Hilt依赖注入: 仅Android

### UIKit (iOS)
UIKit是iOS原生UI框架：

#### 平台特性使用
访问iOS平台独有功能：
```swift
// 使用iOS特有功能
import UIKit

class BiometricAuthController: UIViewController {
    func authenticateUser() {
        let context = LAContext()
        var error: NSError?
        
        if context.canEvaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, error: &error) {
            context.evaluatePolicy(.deviceOwnerAuthenticationWithBiometrics, localizedReason: "验证身份") { success, error in
                DispatchQueue.main.async {
                    if success {
                        // 认证成功
                    } else {
                        // 认证失败
                    }
                }
            }
        }
    }
}
```

#### 适配不同iOS版本
兼容不同iOS版本：
```swift
// 版本适配
if #available(iOS 15.0, *) {
    // 使用iOS 15特有功能
    navigationItem.scrollEdgeAppearance = UINavigationBarAppearance()
} else {
    // 兼容旧版本
    navigationController?.navigationBar.isTranslucent = true
}
```

#### UIKit/SwiftUI互操作
UIKit和SwiftUI混合使用：
```swift
// 在UIKit中嵌入SwiftUI
class ViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // 创建SwiftUI视图
        let swiftUIView = UIHostingController(rootView: ContentView())
        
        // 添加为子视图控制器
        addChild(swiftUIView)
        view.addSubview(swiftUIView.view)
        swiftUIView.didMove(toParent: self)
        
        // 设置约束
        swiftUIView.view.translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([
            swiftUIView.view.topAnchor.constraint(equalTo: view.topAnchor),
            swiftUIView.view.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            swiftUIView.view.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            swiftUIView.view.bottomAnchor.constraint(equalTo: view.bottomAnchor)
        ])
    }
}
```

#### 状态管理兼容性
UIKit状态管理方案仅适用于iOS平台：
- 属性和KVO: 仅iOS
- 通知中心: 仅iOS
- MVVM模式: 概念可跨平台，但实现仅限iOS

### SwiftUI (iOS)
SwiftUI是iOS现代UI框架：

#### 平台特性集成
使用iOS平台独有功能：
```swift
// 使用动态岛 (Dynamic Island)
struct ContentView: View {
    @Environment(\.scenePhase) var scenePhase
    
    var body: some View {
        Text("Hello World")
            .onChange(of: scenePhase) { phase in
                switch phase {
                case .active:
                    // 应用处于活动状态
                    break
                case .background:
                    // 创建Live Activity
                    if ActivityAuthorizationInfo().areActivitiesEnabled {
                        startLiveActivity()
                    }
                    break
                default:
                    break
                }
            }
    }
    
    func startLiveActivity() {
        // 创建动态岛活动
    }
}
```

#### 多平台适配
SwiftUI支持iOS、macOS、tvOS和watchOS：
```swift
struct PlatformAdaptiveView: View {
    var body: some View {
        #if os(iOS)
        // iOS特有UI
        TabView {
            HomeView()
                .tabItem {
                    Label("首页", systemImage: "house")
                }
            SettingsView()
                .tabItem {
                    Label("设置", systemImage: "gear")
                }
        }
        #elseif os(macOS)
        // macOS特有UI
        NavigationView {
            List {
                NavigationLink("首页", destination: HomeView())
                NavigationLink("设置", destination: SettingsView())
            }
            .listStyle(SidebarListStyle())
            
            Text("选择一个选项")
        }
        #endif
    }
}
```

#### 向后兼容性
在旧版iOS上降级体验：
```swift
struct UserProfileView: View {
    var body: some View {
        VStack {
            // 基本内容
            Text("用户资料")
            
            // 条件性使用新功能
            if #available(iOS 16.0, *) {
                // iOS 16引入的功能
                ShareLink("分享资料", item: URL(string: "https://example.com/profile")!)
            } else {
                // 旧版iOS的替代方案
                Button("分享资料") {
                    // 手动实现分享
                    let activityVC = UIActivityViewController(
                        activityItems: [URL(string: "https://example.com/profile")!],
                        applicationActivities: nil
                    )
                    
                    // 呈现分享控制器
                    UIApplication.shared.windows.first?.rootViewController?
                        .present(activityVC, animated: true)
                }
            }
        }
    }
}
```

#### 状态管理兼容性
SwiftUI状态管理方案主要支持Apple平台：
- @State, @Binding: 仅限Apple平台
- @ObservableObject: 仅限Apple平台
- SwiftData: 仅限iOS 17+、macOS 14+及更新的Apple平台

### 鸿蒙 (ArkUI)
鸿蒙ArkUI为华为设备生态系统设计：

#### 一次开发，多端部署
支持在不同设备类型上运行：
```typescript
@Entry
@Component
struct ResponsiveComponent {
  @StorageLink('windowWidth') windowWidth: number = 0;

  aboutToAppear() {
    // 获取窗口宽度
    this.windowWidth = window.getWindowWidth();
  }

  build() {
    Column() {
      if (this.windowWidth >= 600) {
        // 大屏设备布局（平板、折叠屏）
        Row() {
          // 导航栏
          NavPanel()
            .width('30%')
          
          // 内容区
          ContentPanel()
            .width('70%')
        }
      } else {
        // 小屏设备布局（手机）
        Column() {
          // 内容区
          ContentPanel()
          
          // 底部导航栏
          BottomTabBar()
        }
      }
    }
  }
}
```

#### 分布式能力
跨设备状态同步：
```typescript
// 注册分布式数据对象
@Entry
@Component
struct DistributedApp {
  @StorageLink('deviceId') deviceId: string = '';
  @State deviceList: Array<DeviceInfo> = [];
  
  aboutToAppear() {
    // 初始化跨设备通信
    DistributedObjectModel.registerObject('countSync');
    
    // 获取连接设备列表
    deviceManager.getDeviceList().then((devices) => {
      this.deviceList = devices;
    });
  }
  
  build() {
    Column() {
      Text(`当前设备: ${this.deviceId}`)
      
      ForEach(this.deviceList, (device) => {
        Button(`发送数据到设备 ${device.name}`)
          .onClick(() => {
            // 发送数据到其他设备
            this.sendDataToDevice(device.id, { count: 10 });
          })
      })
    }
  }
  
  sendDataToDevice(deviceId: string, data: object) {
    // 实现分布式数据同步
  }
}
```

#### 系统服务集成
调用鸿蒙系统服务：
```typescript
@Entry
@Component
struct SystemServiceExample {
  build() {
    Column() {
      Button('获取位置')
        .onClick(() => {
          // 调用定位服务
          geolocation.getLocation({
            success: (location) => {
              console.info(`位置: ${location.latitude}, ${location.longitude}`);
            },
            fail: (error) => {
              console.error(`获取位置失败: ${error.code}, ${error.message}`);
            }
          });
        })
      
      Button('调用相机')
        .onClick(() => {
          // 调用相机服务
          camera.createCamera().then((camera) => {
            camera.takePicture({
              success: (photoUri) => {
                console.info(`拍照成功: ${photoUri}`);
              },
              fail: (error) => {
                console.error(`拍照失败: ${error.code}, ${error.message}`);
              }
            });
          });
        })
    }
  }
}
```

#### 状态管理兼容性
ArkUI状态管理方案仅支持鸿蒙设备：
- @State, @Prop, @Link: 仅限鸿蒙系统
- @Provide/@Consume: 仅限鸿蒙系统
- AppStorage: 仅限鸿蒙系统
- PersistentStorage: 仅限鸿蒙系统

### Vue
Vue作为前端框架主要运行在浏览器环境：

#### 跨平台特性
通过不同构建工具适应不同环境：
```javascript
// vite.config.js 适配不同平台
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import electron from 'vite-plugin-electron'
import { fileURLToPath, URL } from 'node:url'

export default defineConfig({
  plugins: [
    vue(),
    // 条件性引入Electron配置
    process.env.ELECTRON === 'true'
      ? electron({
          entry: 'electron/main.js',
        })
      : null,
  ].filter(Boolean),
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url)),
      // 根据平台使用不同实现
      '@platform': fileURLToPath(
        new URL(
          process.env.ELECTRON === 'true'
            ? './src/platforms/electron'
            : './src/platforms/web',
          import.meta.url
        )
      ),
    },
  },
})
```

#### 服务端渲染
Vue支持SSR (服务端渲染)：
```javascript
// 客户端和服务端渲染配置
import { createSSRApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'
import { createRouter } from './router'

// 工厂函数避免状态污染
export function createApp() {
  const app = createSSRApp(App)
  const pinia = createPinia()
  const router = createRouter()
  
  app.use(pinia)
  app.use(router)
  
  return { app, router, pinia }
}
```

#### 平台适配
根据运行环境调整功能：
```typescript
// 平台服务接口
interface PlatformService {
  getStorage(key: string): Promise<string | null>;
  setStorage(key: string, value: string): Promise<void>;
  share(data: ShareData): Promise<void>;
}

// Web实现
class WebPlatformService implements PlatformService {
  async getStorage(key: string): Promise<string | null> {
    return localStorage.getItem(key);
  }
  
  async setStorage(key: string, value: string): Promise<void> {
    localStorage.setItem(key, value);
  }
  
  async share(data: ShareData): Promise<void> {
    if (navigator.share) {
      await navigator.share(data);
    } else {
      alert('分享功能在当前浏览器不可用');
    }
  }
}

// 移动端WebView桥接实现
class NativeBridgePlatformService implements PlatformService {
  async getStorage(key: string): Promise<string | null> {
    return new Promise((resolve) => {
      window.nativeBridge.getStorage(key, (value: string) => {
        resolve(value);
      });
    });
  }
  
  // 其他方法实现...
}

// 导出适当的实现
export const platformService: PlatformService = 
  window.nativeBridge ? new NativeBridgePlatformService() : new WebPlatformService();
```

#### 状态管理兼容性
Vue状态管理方案可在多种环境使用：
- 组合式API: Web、移动端WebView、Electron等
- Pinia: 支持客户端和服务端渲染
- Provide/Inject: 在所有Vue支持的平台上可用

## 5. 完整实例

### Flutter
待完善

### Android (Jetpack Compose)
待完善

### UIKit (iOS)
一个使用UIKit和MVVM模式的计数器应用：

```swift
// CounterViewModel.swift
import Foundation
import RxSwift
import RxCocoa

class CounterViewModel {
    // 可观察属性
    let count = BehaviorRelay<Int>(value: 0)
    
    // 计算属性
    var countSquared: Observable<Int> {
        return count.map { $0 * $0 }
    }
    
    var countDoubled: Observable<Int> {
        return count.map { $0 * 2 }
    }
    
    // 操作方法
    func increment() {
        count.accept(count.value + 1)
    }
    
    func decrement() {
        if count.value > 0 {
            count.accept(count.value - 1)
        }
    }
    
    func reset() {
        count.accept(0)
    }
}

// 协调器
protocol CounterCoordinatorProtocol: AnyObject {
    func showDetails()
    func showSettings()
}

class CounterCoordinator: CounterCoordinatorProtocol {
    private let navigationController: UINavigationController
    private let viewModel: CounterViewModel
    
    init(navigationController: UINavigationController) {
        self.navigationController = navigationController
        self.viewModel = CounterViewModel()
        
        let homeVC = HomeViewController(viewModel: viewModel)
        homeVC.coordinator = self
        navigationController.pushViewController(homeVC, animated: false)
    }
    
    func showDetails() {
        let detailsVC = DetailsViewController(viewModel: viewModel)
        navigationController.pushViewController(detailsVC, animated: true)
    }
    
    func showSettings() {
        let settingsVC = SettingsViewController(viewModel: viewModel)
        navigationController.pushViewController(settingsVC, animated: true)
    }
}

// HomeViewController.swift
import UIKit
import RxSwift
import RxCocoa

class HomeViewController: UIViewController {
    private let viewModel: CounterViewModel
    private let disposeBag = DisposeBag()
    weak var coordinator: CounterCoordinatorProtocol?
    
    // UI元素
    private let titleLabel = UILabel()
    private let countLabel = UILabel()
    private let decrementButton = UIButton(type: .system)
    private let resetButton = UIButton(type: .system)
    private let incrementButton = UIButton(type: .system)
    private let detailsButton = UIButton(type: .system)
    
    init(viewModel: CounterViewModel) {
        self.viewModel = viewModel
        super.init(nibName: nil, bundle: nil)
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
        bindViewModel()
    }
    
    private func setupUI() {
        title = "UIKit计数器"
        view.backgroundColor = .white
        
        // 导航栏设置按钮
        let settingsButton = UIBarButtonItem(
            image: UIImage(systemName: "gear"),
            style: .plain,
            target: self,
            action: #selector(settingsTapped)
        )
        navigationItem.rightBarButtonItem = settingsButton
        
        // 配置UI元素
        titleLabel.text = "当前计数:"
        titleLabel.font = .systemFont(ofSize: 24)
        titleLabel.textAlignment = .center
        
        countLabel.font = .boldSystemFont(ofSize: 48)
        countLabel.textAlignment = .center
        
        decrementButton.setTitle("减少", for: .normal)
        decrementButton.addTarget(self, action: #selector(decrementTapped), for: .touchUpInside)
        
        resetButton.setTitle("重置", for: .normal)
        resetButton.addTarget(self, action: #selector(resetTapped), for: .touchUpInside)
        
        incrementButton.setTitle("增加", for: .normal)
        incrementButton.addTarget(self, action: #selector(incrementTapped), for: .touchUpInside)
        
        detailsButton.setTitle("查看详情", for: .normal)
        detailsButton.addTarget(self, action: #selector(detailsTapped), for: .touchUpInside)
        
        // 添加到视图层次
        let buttonsStackView = UIStackView(arrangedSubviews: [decrementButton, resetButton, incrementButton])
        buttonsStackView.axis = .horizontal
        buttonsStackView.spacing = 20
        buttonsStackView.distribution = .fillEqually
        
        let mainStackView = UIStackView(arrangedSubviews: [titleLabel, countLabel, buttonsStackView, detailsButton])
        mainStackView.axis = .vertical
        mainStackView.spacing = 20
        mainStackView.alignment = .center
        
        view.addSubview(mainStackView)
        
        // 设置约束
        mainStackView.translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([
            mainStackView.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            mainStackView.centerYAnchor.constraint(equalTo: view.centerYAnchor),
            mainStackView.leadingAnchor.constraint(greaterThanOrEqualTo: view.leadingAnchor, constant: 20),
            mainStackView.trailingAnchor.constraint(lessThanOrEqualTo: view.trailingAnchor, constant: -20)
        ])
    }
    
    private func bindViewModel() {
        // 绑定计数到标签
        viewModel.count
            .map { "\($0)" }
            .bind(to: countLabel.rx.text)
            .disposed(by: disposeBag)
    }
    
    @objc private func decrementTapped() {
        viewModel.decrement()
    }
    
    @objc private func resetTapped() {
        viewModel.reset()
    }
    
    @objc private func incrementTapped() {
        viewModel.increment()
    }
    
    @objc private func detailsTapped() {
        coordinator?.showDetails()
    }
    
    @objc private func settingsTapped() {
        coordinator?.showSettings()
    }
}

// DetailsViewController.swift
import UIKit
import RxSwift
import RxCocoa

class DetailsViewController: UIViewController {
    private let viewModel: CounterViewModel
    private let disposeBag = DisposeBag()
    
    // UI元素
    private let titleLabel = UILabel()
    private let countLabel = UILabel()
    private let squaredLabel = UILabel()
    private let doubledLabel = UILabel()
    
    init(viewModel: CounterViewModel) {
        self.viewModel = viewModel
        super.init(nibName: nil, bundle: nil)
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()
        setupUI()
        bindViewModel()
    }
    
    private func setupUI() {
        title = "计数详情"
        view.backgroundColor = .white
        
        // 配置UI元素
        titleLabel.text = "当前计数详情:"
        titleLabel.font = .systemFont(ofSize: 18)
        titleLabel.textAlignment = .center
        
        countLabel.font = .boldSystemFont(ofSize: 48)
        countLabel.textAlignment = .center
        
        squaredLabel.font = .systemFont(ofSize: 18)
        squaredLabel.textAlignment = .center
        
        doubledLabel.font = .systemFont(ofSize: 18)
        doubledLabel.textAlignment = .center
        
        // 添加到视图层次
        let mainStackView = UIStackView(arrangedSubviews: [titleLabel, countLabel, squaredLabel, doubledLabel])
        mainStackView.axis = .vertical
        mainStackView.spacing = 20
        mainStackView.alignment = .center
        
        view.addSubview(mainStackView)
        
        // 设置约束
        mainStackView.translatesAutoresizingMaskIntoConstraints = false
        NSLayoutConstraint.activate([
            mainStackView.centerXAnchor.constraint(equalTo: view.centerXAnchor),
            mainStackView.centerYAnchor.constraint(equalTo: view.centerYAnchor)
        ])
    }
    
    private func bindViewModel() {
        // 绑定计数到标签
        viewModel.count
            .map { "\($0)" }
            .bind(to: countLabel.rx.text)
            .disposed(by: disposeBag)
        
        // 绑定计数的平方
        viewModel.countSquared
            .map { "计数的平方: \($0)" }
            .bind(to: squaredLabel.rx.text)
            .disposed(by: disposeBag)
        
        // 绑定计数的二倍
        viewModel.countDoubled
            .map { "计数的二倍: \($0)" }
            .bind(to: doubledLabel.rx.text)
            .disposed(by: disposeBag)
    }
}

// AppDelegate.swift
import UIKit

@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    var window: UIWindow?
    var coordinator: CounterCoordinator?

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
        window = UIWindow(frame: UIScreen.main.bounds)
        
        let navigationController = UINavigationController()
        coordinator = CounterCoordinator(navigationController: navigationController)
        
        window?.rootViewController = navigationController
        window?.makeKeyAndVisible()
        
        return true
    }
}
```

### SwiftUI (iOS)
一个使用SwiftUI和ObservableObject的计数器应用：

```swift
// CounterApp.swift
import SwiftUI

@main
struct CounterApp: App {
    @StateObject private var counterViewModel = CounterViewModel()
    
    var body: some Scene {
        WindowGroup {
            // 使用NavigationStack包装视图，并提供环境对象
            NavigationStack {
                HomeView()
            }
            .environmentObject(counterViewModel)
        }
    }
}

// CounterViewModel.swift
import Foundation
import Combine

class CounterViewModel: ObservableObject {
    @Published var count = 0
    
    // 计算属性
    var countSquared: Int {
        count * count
    }
    
    var countDoubled: Int {
        count * 2
    }
    
    // 操作方法
    func increment() {
        count += 1
    }
    
    func decrement() {
        if count > 0 {
            count -= 1
        }
    }
    
    func reset() {
        count = 0
    }
}

// HomeView.swift
import SwiftUI

struct HomeView: View {
    @EnvironmentObject var viewModel: CounterViewModel
    @State private var showingAlert = false
    
    var body: some View {
        VStack(spacing: 20) {
            Text("当前计数:")
                .font(.title2)
            
            Text("\(viewModel.count)")
                .font(.system(size: 48, weight: .bold))
            
            HStack(spacing: 20) {
                Button("减少") {
                    viewModel.decrement()
                }
                .buttonStyle(.bordered)
                
                Button("重置") {
                    viewModel.reset()
                }
                .buttonStyle(.bordered)
                
                Button("增加") {
                    viewModel.increment()
                }
                .buttonStyle(.bordered)
            }
            
            Spacer().frame(height: 20)
            
            NavigationLink(destination: DetailsView()) {
                Text("查看详情")
                    .frame(minWidth: 100)
            }
            .buttonStyle(.borderedProminent)
        }
        .padding()
        .navigationTitle("SwiftUI计数器")
        .toolbar {
            ToolbarItem(placement: .navigationBarTrailing) {
                NavigationLink(destination: SettingsView()) {
                    Image(systemName: "gear")
                }
            }
        }
        .alert("提示", isPresented: $showingAlert) {
            Button("确定", role: .cancel) { }
        } message: {
            Text("计数已重置")
        }
    }
}

// DetailsView.swift
import SwiftUI

struct DetailsView: View {
    @EnvironmentObject var viewModel: CounterViewModel
    
    var body: some View {
        VStack(spacing: 20) {
            Text("当前计数详情:")
                .font(.title3)
            
            Text("\(viewModel.count)")
                .font(.system(size: 48, weight: .bold))
            
            Text("计数的平方: \(viewModel.countSquared)")
                .font(.title3)
            
            Text("计数的二倍: \(viewModel.countDoubled)")
                .font(.title3)
        }
        .padding()
        .navigationTitle("计数详情")
    }
}

// SettingsView.swift
import SwiftUI

struct SettingsView: View {
    @EnvironmentObject var viewModel: CounterViewModel
    @State private var showingAlert = false
    
    var body: some View {
        VStack(spacing: 20) {
            Text("计数器设置")
                .font(.title2)
            
            Button("重置计数") {
                viewModel.reset()
                showingAlert = true
            }
            .buttonStyle(.borderedProminent)
        }
        .padding()
        .navigationTitle("设置")
        .alert("成功", isPresented: $showingAlert) {
            Button("确定", role: .cancel) { }
        } message: {
            Text("计数已重置")
        }
    }
}
```

### 鸿蒙 (ArkUI)
一个使用ArkUI和状态管理的计数器应用：

```typescript
// pages/Index.ets (首页)
import router from '@ohos.router';
import common from '@ohos.app.ability.common';

@Entry
@Component
struct Index {
  @State count: number = 0;
  @StorageLink('globalCount') globalCount: number = 0;

  build() {
    Column() {
      Navigation() {
        Column() {
          // 标题
          Text('当前计数:')
            .fontSize(24)
            .margin({ top: 40 })
          
          // 计数显示
          Text(`${this.count}`)
            .fontSize(48)
            .fontWeight(FontWeight.Bold)
            .margin({ top: 20 })
          
          // 操作按钮
          Row() {
            Button('减少')
              .onClick(() => {
                if (this.count > 0) {
                  this.count--;
                  this.globalCount = this.count;
                }
              })
              .margin({ right: 10 })
            
            Button('重置')
              .onClick(() => {
                this.count = 0;
                this.globalCount = 0;
              })
              .margin({ horizontal: 10 })
            
            Button('增加')
              .onClick(() => {
                this.count++;
                this.globalCount = this.count;
              })
              .margin({ left: 10 })
          }
          .margin({ top: 20 })
          
          // 详情页按钮
          Button('查看详情')
            .margin({ top: 40 })
            .onClick(() => {
              router.pushUrl({
                url: 'pages/Detail'
              });
            })
        }
        .width('100%')
        .height('100%')
        .alignItems(HorizontalAlign.Center)
        .justifyContent(FlexAlign.Start)
      }
      .title('鸿蒙计数器')
      .titleMode(NavigationTitleMode.Full)
      .menus([
        {
          value: '',
          icon: $r('app.media.ic_settings'),
          action: () => {
            router.pushUrl({
              url: 'pages/Settings'
            });
          }
        }
      ])
    }
    .width('100%')
    .height('100%')
  }

  aboutToAppear() {
    // 初始化全局状态
    AppStorage.SetOrCreate('globalCount', this.count);
  }
}

// pages/Detail.ets (详情页)
import router from '@ohos.router';

@Entry
@Component
struct Detail {
  @StorageLink('globalCount') count: number = 0;

  build() {
    Column() {
      Navigation() {
        Column() {
          // 标题
          Text('当前计数详情:')
            .fontSize(18)
            .margin({ top: 40 })
          
          // 计数显示
          Text(`${this.count}`)
            .fontSize(48)
            .fontWeight(FontWeight.Bold)
            .margin({ top: 20 })
          
          // 计数的平方
          Text(`计数的平方: ${this.count * this.count}`)
            .fontSize(18)
            .margin({ top: 20 })
          
          // 计数的二倍
          Text(`计数的二倍: ${this.count * 2}`)
            .fontSize(18)
            .margin({ top: 10 })
        }
        .width('100%')
        .height('100%')
        .alignItems(HorizontalAlign.Center)
        .justifyContent(FlexAlign.Start)
      }
      .title('计数详情')
      .titleMode(NavigationTitleMode.Full)
      .hideBackButton(false)
      .onBackClick(() => {
        router.back();
      })
    }
    .width('100%')
    .height('100%')
  }
}

// pages/Settings.ets (设置页)
import router from '@ohos.router';
import promptAction from '@ohos.promptAction';

@Entry
@Component
struct Settings {
  @StorageLink('globalCount') count: number = 0;

  build() {
    Column() {
      Navigation() {
        Column() {
          // 标题
          Text('计数器设置')
            .fontSize(24)
            .margin({ top: 40 })
          
          // 重置按钮
          Button('重置计数')
            .margin({ top: 20 })
            .onClick(() => {
              this.count = 0;
              
              // 显示提示
              promptAction.showToast({
                message: '计数已重置',
                duration: 2000
              });
            })
        }
        .width('100%')
        .height('100%')
        .alignItems(HorizontalAlign.Center)
        .justifyContent(FlexAlign.Start)
      }
      .title('设置')
      .titleMode(NavigationTitleMode.Full)
      .hideBackButton(false)
      .onBackClick(() => {
        router.back();
      })
    }
    .width('100%')
    .height('100%')
  }
}

// app.ets (全局状态初始化)
import common from '@ohos.app.ability.common';
import hilog from '@ohos.hilog';

export default {
  onCreate() {
    // 创建初始全局状态
    AppStorage.SetOrCreate('globalCount', 0);
    
    hilog.info(0x0000, 'CounterApp', 'Application onCreate');
  },
  onDestroy() {
    hilog.info(0x0000, 'CounterApp', 'Application onDestroy');
  },
};
```

### Vue
一个使用Vue 3和Pinia的计数器应用：

```javascript
// src/stores/counter.js (Pinia状态管理)
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', {
  state: () => ({
    count: 0
  }),
  getters: {
    countSquared: (state) => state.count * state.count,
    countDoubled: (state) => state.count * 2
  },
  actions: {
    increment() {
      this.count++
    },
    decrement() {
      if (this.count > 0) {
        this.count--
      }
    },
    reset() {
      this.count = 0
    }
  }
})
```

```javascript
// src/router/index.js (Vue Router配置)
import { createRouter, createWebHistory } from 'vue-router'
import HomeView from '../views/HomeView.vue'
import DetailsView from '../views/DetailsView.vue'
import SettingsView from '../views/SettingsView.vue'

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes: [
    {
      path: '/',
      name: 'home',
      component: HomeView
    },
    {
      path: '/details',
      name: 'details',
      component: DetailsView
    },
    {
      path: '/settings',
      name: 'settings',
      component: SettingsView
    }
  ]
})

export default router
```

```javascript
// src/main.js (主入口文件)
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'
import router from './router'

import './assets/main.css'

const app = createApp(App)

app.use(createPinia())
app.use(router)

app.mount('#app')
```

```vue
<!-- src/App.vue (应用组件) -->
<template>
  <header>
    <div class="wrapper">
      <nav>
        <RouterLink to="/">首页</RouterLink>
        <RouterLink to="/details">详情</RouterLink>
        <RouterLink to="/settings">设置</RouterLink>
      </nav>
    </div>
  </header>

  <RouterView />
</template>

<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>
```

```vue
<!-- src/views/HomeView.vue (首页) -->
<template>
  <main>
    <div class="counter-container">
      <h2>当前计数:</h2>
      <div class="counter-value">{{ counter.count }}</div>
      
      <div class="counter-buttons">
        <button @click="counter.decrement">减少</button>
        <button @click="counter.reset">重置</button>
        <button @click="counter.increment">增加</button>
      </div>
      
      <RouterLink to="/details" class="details-link">查看详情</RouterLink>
    </div>
  </main>
</template>

<script setup>
import { useCounterStore } from '../stores/counter'

const counter = useCounterStore()
</script>

<style scoped>
.counter-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2rem;
}

.counter-value {
  font-size: 3rem;
  font-weight: bold;
  margin: 1rem 0;
}

.counter-buttons {
  display: flex;
  gap: 1rem;
  margin: 1rem 0;
}

.details-link {
  margin-top: 2rem;
  padding: 0.5rem 1rem;
  background-color: #4caf50;
  color: white;
  text-decoration: none;
  border-radius: 4px;
}
</style>
```

```vue
<!-- src/views/DetailsView.vue (详情页) -->
<template>
  <main>
    <div class="details-container">
      <h2>当前计数详情:</h2>
      <div class="counter-value">{{ counter.count }}</div>
      
      <div class="counter-details">
        <p>计数的平方: {{ counter.countSquared }}</p>
        <p>计数的二倍: {{ counter.countDoubled }}</p>
      </div>
    </div>
  </main>
</template>

<script setup>
import { useCounterStore } from '../stores/counter'

const counter = useCounterStore()
</script>

<style scoped>
.details-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2rem;
}

.counter-value {
  font-size: 3rem;
  font-weight: bold;
  margin: 1rem 0;
}

.counter-details {
  font-size: 1.2rem;
  margin-top: 1rem;
}
</style>
```

```vue
<!-- src/views/SettingsView.vue (设置页) -->
<template>
  <main>
    <div class="settings-container">
      <h2>计数器设置</h2>
      
      <button @click="resetCounter" class="reset-button">重置计数</button>
      
      <div v-if="showMessage" class="message">
        计数已重置
      </div>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
import { useCounterStore } from '../stores/counter'

const counter = useCounterStore()
const showMessage = ref(false)

function resetCounter() {
  counter.reset()
  showMessage.value = true
  
  // 3秒后隐藏消息
  setTimeout(() => {
    showMessage.value = false
  }, 3000)
}
</script>

<style scoped>
.settings-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 2rem;
}

.reset-button {
  margin-top: 1rem;
  padding: 0.5rem 1rem;
  background-color: #f44336;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.message {
  margin-top: 1rem;
  padding: 0.5rem 1rem;
  background-color: #4caf50;
  color: white;
  border-radius: 4px;
}
</style>
```