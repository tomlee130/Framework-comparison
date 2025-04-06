# 六大框架 MediaQuery/Device 组件比较

本文档对比Flutter、Android（Jetpack Compose）、UIKit、SwiftUI、鸿蒙（ArkUI）和Vue六大框架中MediaQuery/Device组件的实现和特性，从以下维度进行分析：

- 设备信息获取
- 屏幕尺寸响应
- 方向变化处理
- 安全区域适配
- 设备特性检测

每个框架部分包含平台特性、兼容性分析和完整实例代码。

## 目录

1. [Flutter MediaQuery](#flutter-mediaquery)
2. [Android Jetpack Compose](#android-jetpack-compose)
3. [iOS UIKit](#ios-uikit)
4. [iOS SwiftUI](#ios-swiftui)
5. [鸿蒙 ArkUI](#鸿蒙-arkui)
6. [Vue](#vue)
7. [总结对比](#总结对比)

## 鸿蒙 ArkUI

### 设备信息获取

ArkUI提供了多种获取设备信息的方式：

- `deviceInfo`模块：获取设备名称、制造商、品牌、型号等
- `display`模块：获取屏幕尺寸、分辨率、刷新率等
- `window`模块：获取窗口尺寸和属性
- `systemParameter`：获取系统参数
- `mediaQuery`组件：响应式获取媒体信息
- `Environment`：获取应用环境变量
- `ResourceManager`：获取设备资源信息
- `appStorage`/`localStorage`：存储和获取应用数据

主要可获取的设备信息：

```typescript
// 使用deviceInfo获取设备信息
import deviceInfo from '@ohos.deviceInfo';

@Entry
@Component
struct DeviceInfoDisplay {
  build() {
    Column() {
      Text('设备型号: ' + deviceInfo.deviceType) // 如：phone、tablet、tv、wearable等
      Text('制造商: ' + deviceInfo.manufacturer)
      Text('品牌: ' + deviceInfo.brand)
      Text('产品名称: ' + deviceInfo.productModel)
      Text('设备序列号: ' + deviceInfo.serial)
      Text('系统名称: ' + deviceInfo.displayVersion)
      Text('API版本: ' + deviceInfo.sdkApiVersion)
      Text('系统版本: ' + deviceInfo.osFullName)
    }
    .width('100%')
    .padding(20)
  }
}

// 使用display获取屏幕信息
import display from '@ohos.display';

async function getScreenInfo() {
  try {
    const displayInfo = await display.getDefaultDisplay();
    console.info('屏幕宽度: ' + displayInfo.width + ' px');
    console.info('屏幕高度: ' + displayInfo.height + ' px');
    console.info('屏幕刷新率: ' + displayInfo.refreshRate + ' Hz');
    console.info('屏幕密度: ' + displayInfo.densityDPI + ' DPI');
    console.info('屏幕物理密度: ' + displayInfo.densityPixels);
    console.info('屏幕比例: ' + displayInfo.scaledDensity);
  } catch (error) {
    console.error('获取屏幕信息失败: ' + error);
  }
}
```

### 屏幕尺寸响应

ArkUI的响应式设计主要通过以下方式实现：

- `mediaQuery`组件：监听和响应媒体查询条件
- 尺寸单位：`vp`、`fp`、`%`等相对单位
- 弹性布局：`Flex`、`Row`、`Column`等布局组件
- `GridRow`/`GridCol`：网格响应式布局
- 栅格系统：基于行列的响应式布局系统
- `@State`/`@Prop`：状态驱动的响应式UI
- `BreakpointSystem`：断点系统适配不同尺寸
- 相对定位：`RelativeContainer`相对定位布局
- 尺寸限制：`constraintSize`等约束条件
- 自适应组件：`AdaptiveComponents`

响应式布局示例：

```typescript
// 使用mediaQuery实现响应式布局
@Entry
@Component
struct ResponsiveLayout {
  @State screenWidth: number = 0;
  @State screenHeight: number = 0;
  @State isTablet: boolean = false;
  
  // 断点系统 - 定义屏幕尺寸范围
  private breakpoints = {
    sm: 320,  // 小屏手机
    md: 600,  // 大屏手机/小屏平板
    lg: 840   // 平板
  };
  
  aboutToAppear() {
    try {
      display.getDefaultDisplay().then(displayInfo => {
        this.screenWidth = px2vp(displayInfo.width);
        this.screenHeight = px2vp(displayInfo.height);
        this.isTablet = this.screenWidth >= this.breakpoints.md;
      });
    } catch (error) {
      console.error('获取屏幕信息失败: ' + error);
    }
  }
  
  build() {
    Column() {
      // 根据屏幕宽度选择不同布局
      if (this.screenWidth < this.breakpoints.md) {
        MobileLayout()
      } else if (this.screenWidth < this.breakpoints.lg) {
        TabletCompactLayout()
      } else {
        TabletExpandedLayout()
      }
      
      // 使用百分比宽度的容器
      Container() {
        Text('宽度为屏幕的80%')
      }
      .width('80%')
      .height(50)
      .backgroundColor('#0D9FFB')
      .margin({ top: 20 })
      
      // 使用栅格系统
      GridRow() {
        GridCol({ span: { sm: 4, md: 3, lg: 2 } }) {
          Container().height(50).backgroundColor('#FF6B6B')
        }
        GridCol({ span: { sm: 4, md: 3, lg: 2 } }) {
          Container().height(50).backgroundColor('#FFD93D')
        }
        GridCol({ span: { sm: 4, md: 3, lg: 2 } }) {
          Container().height(50).backgroundColor('#6BCB77')
        }
      }
      .margin({ top: 20 })
    }
    .width('100%')
    .height('100%')
    .padding(20)
  }
}

// 栅格系统示例
@Component
struct GridExample {
  build() {
    GridContainer({ columns: { sm: 4, md: 8, lg: 12 }, gutter: 10 }) {
      GridRow() {
        GridCol({ span: { sm: 4, md: 4, lg: 4 } }) {
          Container().height(100).backgroundColor('#F56C6C')
        }
        GridCol({ span: { sm: 4, md: 4, lg: 4 } }) {
          Container().height(100).backgroundColor('#E6A23C')
        }
        GridCol({ span: { sm: 4, md: 4, lg: 4 } }) {
          Container().height(100).backgroundColor('#67C23A')
        }
      }
    }
    .width('100%')
  }
}

// 使用mediaQuery适配不同屏幕尺寸
@Component
struct MediaQueryExample {
  @Builder
  smallScreenLayout() {
    Column() {
      Text('小屏布局').fontSize(20).fontWeight(FontWeight.Bold)
      // 小屏特定内容
    }
  }
  
  @Builder
  largeScreenLayout() {
    Row() {
      Column() {
        Text('大屏布局 - 侧边栏').fontSize(20).fontWeight(FontWeight.Bold)
      }.width('30%')
      
      Divider().vertical(true).height('100%')
      
      Column() {
        Text('大屏布局 - 主内容').fontSize(20).fontWeight(FontWeight.Bold)
      }.width('70%')
    }
  }
  
  build() {
    Column() {
      MediaQuery({ minWidth: 600 }, this.largeScreenLayout)
      MediaQuery({ maxWidth: 599 }, this.smallScreenLayout)
    }
    .width('100%')
    .height('100%')
  }
}
```

### 方向变化处理

ArkUI处理设备方向变化的方式：

- `onOrientationChange`：监听方向变化事件
- `display.on('orientationChange')`：方向变化订阅
- `display.getDefaultDisplay()`：获取当前方向信息
- 方向锁定：`window.setPreferredOrientation()`
- 自适应布局：布局组件自动处理方向变化
- `deviceRotation`：获取设备旋转角度
- 动态调整：基于方向重新排列UI元素
- 断点系统：结合方向和尺寸定义响应策略

方向变化处理示例：

```typescript
// 方向监听和适配示例
import window from '@ohos.window';
import display from '@ohos.display';

@Entry
@Component
struct OrientationExample {
  @State orientation: display.Orientation = display.Orientation.PORTRAIT;
  @State windowWidth: number = 0;
  @State windowHeight: number = 0;
  
  aboutToAppear() {
    // 获取初始方向和尺寸
    try {
      display.getDefaultDisplay().then(displayInfo => {
        this.orientation = displayInfo.orientation;
        this.updateDimensions();
      });
    } catch (error) {
      console.error('获取屏幕信息失败: ' + error);
    }
    
    // 监听方向变化
    display.on('orientationChange', (screenOrientation) => {
      this.orientation = screenOrientation;
      this.updateDimensions();
    });
  }
  
  aboutToDisappear() {
    // 移除监听器
    display.off('orientationChange');
  }
  
  updateDimensions() {
    try {
      window.getLastWindow().then((windowObj) => {
        let rect = windowObj.getWindowProperties().windowRect;
        this.windowWidth = px2vp(rect.width);
        this.windowHeight = px2vp(rect.height);
      });
    } catch (error) {
      console.error('获取窗口尺寸失败: ' + error);
    }
  }
  
  build() {
    Column() {
      Text(`当前方向: ${this.orientation === display.Orientation.PORTRAIT ? '竖屏' : '横屏'}`)
        .fontSize(24)
        .fontWeight(FontWeight.Bold)
      
      Text(`窗口尺寸: ${this.windowWidth} x ${this.windowHeight} vp`)
        .fontSize(16)
        .margin({ top: 10 })
      
      // 根据方向显示不同布局
      if (this.orientation === display.Orientation.PORTRAIT) {
        this.portraitLayout()
      } else {
        this.landscapeLayout()
      }
    }
    .width('100%')
    .height('100%')
    .padding(20)
  }
  
  @Builder
  portraitLayout() {
    Column() {
      Text('竖屏布局').fontSize(20).margin({ top: 20 })
      Flex({ direction: FlexDirection.Column, justifyContent: FlexAlign.SpaceAround }) {
        Container().width('100%').height(100).backgroundColor('#FF6B6B')
        Container().width('100%').height(100).backgroundColor('#FFD93D')
        Container().width('100%').height(100).backgroundColor('#6BCB77')
      }
      .width('100%')
      .height(350)
      .margin({ top: 20 })
    }
  }
  
  @Builder
  landscapeLayout() {
    Column() {
      Text('横屏布局').fontSize(20).margin({ top: 20 })
      Flex({ direction: FlexDirection.Row, justifyContent: FlexAlign.SpaceAround }) {
        Container().width('30%').height(200).backgroundColor('#FF6B6B')
        Container().width('30%').height(200).backgroundColor('#FFD93D')
        Container().width('30%').height(200).backgroundColor('#6BCB77')
      }
      .width('100%')
      .margin({ top: 20 })
    }
  }
}

// 锁定屏幕方向示例
function lockOrientation() {
  window.getLastWindow().then((windowObj) => {
    // 锁定为竖屏
    windowObj.setPreferredOrientation(window.Orientation.PORTRAIT);
  }).catch((error) => {
    console.error('锁定屏幕方向失败: ' + error);
  });
}
```

### 安全区域适配

ArkUI提供了多种方式处理设备的安全区域：

- `windowStage.getWindowAvoidArea()`：获取需要避开的安全区域
- `window.AvoidArea`：定义窗口避开区域类型
- `Rect`：定义安全区域边界矩形
- 屏幕适配：自动适配不同设备特性（如刘海屏）
- 边距约束：使用内边距适配安全区域
- 状态栏/导航栏管理：通过window API控制系统UI

安全区域适配示例：

```typescript
// 安全区域获取和适配示例
import window from '@ohos.window';

@Entry
@Component
struct SafeAreaExample {
  @State topSafeArea: number = 0;
  @State bottomSafeArea: number = 0;
  @State leftSafeArea: number = 0;
  @State rightSafeArea: number = 0;
  
  aboutToAppear() {
    this.getSafeAreaInsets();
  }
  
  getSafeAreaInsets() {
    try {
      window.getLastWindow().then((windowObj) => {
        // 获取状态栏安全区域
        windowObj.getWindowAvoidArea(window.AvoidAreaType.TYPE_SYSTEM).then((rect) => {
          this.topSafeArea = px2vp(rect.top);
          this.bottomSafeArea = px2vp(rect.bottom);
          this.leftSafeArea = px2vp(rect.left);
          this.rightSafeArea = px2vp(rect.right);
        });
      });
    } catch (error) {
      console.error('获取安全区域失败: ' + error);
    }
  }
  
  build() {
    Column() {
      // 显示安全区域数值
      Text('安全区域尺寸').fontSize(24).fontWeight(FontWeight.Bold)
      Text(`顶部: ${this.topSafeArea}vp`).margin({ top: 10 })
      Text(`底部: ${this.bottomSafeArea}vp`).margin({ top: 5 })
      Text(`左侧: ${this.leftSafeArea}vp`).margin({ top: 5 })
      Text(`右侧: ${this.rightSafeArea}vp`).margin({ top: 5 })
      
      // 手动适配安全区域的示例
      Column() {
        Text('安全区域适配').fontSize(20).fontWeight(FontWeight.Bold)
        Text('内容区域').fontSize(16)
      }
      .width('100%')
      .height('60%')
      .backgroundColor('#E6F1F5')
      .padding({
        top: this.topSafeArea + 10,
        bottom: this.bottomSafeArea + 10,
        left: this.leftSafeArea + 10,
        right: this.rightSafeArea + 10
      })
      .margin({ top: 20 })
    }
    .width('100%')
    .height('100%')
    .padding(20)
  }
}

// 处理全屏应用的安全区域
function handleFullScreenSafeArea() {
  window.getLastWindow().then((windowObj) => {
    // 设置全屏模式
    windowObj.setWindowLayoutFullScreen(true);
    
    // 获取所有需要避开的区域
    windowObj.getWindowAvoidArea(window.AvoidAreaType.TYPE_SYSTEM).then((systemRect) => {
      // systemRect包含状态栏等系统UI的区域
      console.info('系统UI区域: ' + JSON.stringify(systemRect));
    });
    
    // 获取刘海屏区域
    windowObj.getWindowAvoidArea(window.AvoidAreaType.TYPE_CUTOUT).then((cutoutRect) => {
      console.info('刘海屏区域: ' + JSON.stringify(cutoutRect));
    });
  }).catch((error) => {
    console.error('处理安全区域失败: ' + error);
  });
}
```

### 设备特性检测

ArkUI中检测设备特性的方式：

- `deviceInfo`模块：检测设备类型和系统版本
- `systemCapability`：检测系统能力
- `systemParameter`：获取系统参数
- `featureAbility`：查询设备能力
- `display.getDefaultDisplay()`：获取显示特性
- `sensor`模块：检测传感器可用性
- `bluetooth`/`wifi`：检测无线连接能力
- `mediaquery`：响应特定设备和显示特性
- `accessibilityExtension`：检测无障碍特性
- `device.getInfo()`：获取设备硬件信息

设备特性检测示例：

```typescript
// 设备特性检测示例
import deviceInfo from '@ohos.deviceInfo';
import systemParameter from '@ohos.systemParameter';
import featureAbility from '@ohos.ability.featureAbility';
import sensor from '@ohos.sensor';
import brightness from '@ohos.brightness';
import vibrator from '@ohos.vibrator';
import geolocation from '@ohos.geolocation';

@Entry
@Component
struct DeviceCapabilitiesExample {
  // 设备类型检测
  private deviceType: string = deviceInfo.deviceType;
  private isPhone: boolean = this.deviceType === 'phone';
  private isTablet: boolean = this.deviceType === 'tablet';
  private isTV: boolean = this.deviceType === 'tv';
  private isWearable: boolean = this.deviceType === 'wearable';
  
  // API版本检测
  private apiVersion: number = Number(deviceInfo.sdkApiVersion);
  private supportsHarmonyOS3: boolean = this.apiVersion >= 9; // HarmonyOS 3.0 = API 9
  
  // 传感器能力检测
  @State accelerometerAvailable: boolean = false;
  @State gyroscopeAvailable: boolean = false;
  @State ambientLightAvailable: boolean = false;
  
  // 系统特性
  @State darkModeEnabled: boolean = false;
  
  aboutToAppear() {
    // 检测传感器可用性
    this.checkSensors();
    
    // 检测暗黑模式
    this.checkDarkMode();
  }
  
  async checkSensors() {
    try {
      // 检测加速度计
      const accelerometerSensors = await sensor.getSensorList(sensor.SensorType.SENSOR_TYPE_ACCELEROMETER);
      this.accelerometerAvailable = accelerometerSensors && accelerometerSensors.length > 0;
      
      // 检测陀螺仪
      const gyroscopeSensors = await sensor.getSensorList(sensor.SensorType.SENSOR_TYPE_GYROSCOPE);
      this.gyroscopeAvailable = gyroscopeSensors && gyroscopeSensors.length > 0;
      
      // 检测环境光传感器
      const lightSensors = await sensor.getSensorList(sensor.SensorType.SENSOR_TYPE_AMBIENT_LIGHT);
      this.ambientLightAvailable = lightSensors && lightSensors.length > 0;
    } catch (error) {
      console.error('检测传感器失败: ' + error);
    }
  }
  
  async checkDarkMode() {
    try {
      // 获取系统暗黑模式设置
      const isDarkMode = await systemParameter.getSync('settings.display.color_mode', '0');
      this.darkModeEnabled = isDarkMode === '1';
    } catch (error) {
      console.error('获取暗黑模式设置失败: ' + error);
    }
  }
  
  // 检查位置权限并获取位置
  async checkLocationPermission() {
    try {
      const result = await featureAbility.requestPermission('ohos.permission.LOCATION');
      if (result.authResult === 0) {
        // 权限已授予，可以获取位置
        const location = await geolocation.getLocation();
        console.info('当前位置: ' + JSON.stringify(location));
        return true;
      } else {
        console.info('位置权限被拒绝');
        return false;
      }
    } catch (error) {
      console.error('检查位置权限失败: ' + error);
      return false;
    }
  }
  
  build() {
    Column() {
      Text('设备特性检测').fontSize(24).fontWeight(FontWeight.Bold)
      
      Scroll() {
        Column() {
          // 设备类型信息
          this.sectionTitle('设备基本信息')
          this.infoRow('设备型号', deviceInfo.productModel)
          this.infoRow('制造商', deviceInfo.manufacturer)
          this.infoRow('品牌', deviceInfo.brand)
          this.infoRow('设备类型', this.deviceType)
          this.infoRow('API版本', this.apiVersion.toString())
          this.infoRow('系统版本', deviceInfo.displayVersion)
          
          // 设备类型检测
          this.sectionTitle('设备类型')
          this.featureRow('手机', this.isPhone)
          this.featureRow('平板', this.isTablet)
          this.featureRow('电视', this.isTV)
          this.featureRow('可穿戴设备', this.isWearable)
          
          // 传感器可用性
          this.sectionTitle('传感器')
          this.featureRow('加速度计', this.accelerometerAvailable)
          this.featureRow('陀螺仪', this.gyroscopeAvailable)
          this.featureRow('环境光传感器', this.ambientLightAvailable)
          
          // 系统特性
          this.sectionTitle('系统特性')
          this.featureRow('暗黑模式', this.darkModeEnabled)
          this.featureRow('HarmonyOS 3+', this.supportsHarmonyOS3)
        }
        .width('100%')
      }
      .width('100%')
      .height('90%')
    }
    .width('100%')
    .height('100%')
    .padding(20)
  }
  
  @Builder
  sectionTitle(title: string) {
    Text(title)
      .fontSize(20)
      .fontWeight(FontWeight.Bold)
      .width('100%')
      .margin({ top: 20, bottom: 10 })
  }
  
  @Builder
  infoRow(label: string, value: string) {
    Row() {
      Text(label).fontSize(16).width('40%')
      Text(value).fontSize(16).fontWeight(FontWeight.Normal)
    }
    .width('100%')
    .margin({ top: 5, bottom: 5 })
  }
  
  @Builder
  featureRow(feature: string, available: boolean) {
    Row() {
      Text(feature).fontSize(16).width('60%')
      Text(available ? '可用' : '不可用')
        .fontSize(16)
        .fontColor(available ? '#67C23A' : '#F56C6C')
    }
    .width('100%')
    .margin({ top: 5, bottom: 5 })
  }
}
```

### 平台特性与兼容性

ArkUI的设备信息和适配特性：

- **多设备适配**：专为鸿蒙生态设计，适配手机、平板、智能穿戴、车机、智慧屏
- **分布式特性**：支持跨设备协同，一套代码多端运行
- **声明式UI**：类似SwiftUI和Compose的现代声明式UI范式
- **栅格系统**：内置栅格系统简化响应式布局设计
- **屏幕密度适配**：使用vp、fp等相对单位自动适配不同屏幕
- **多设备形态**：支持折叠屏、大屏等新型设备形态
- **动态ArkUI组件**：支持基于设备参数动态调整组件样式和布局
- **系统API集成**：与鸿蒙系统API深度集成，获取丰富设备信息
- **兼容性设计**：设计上兼容安卓，易于迁移和运行安卓应用
- **轻量级运行**：针对低功耗设备优化，支持资源受限设备

## Android Jetpack Compose

### 设备信息获取

Jetpack Compose提供了多种获取设备信息的方式：

- `LocalConfiguration.current`：获取设备配置信息
- `LocalDensity.current`：获取屏幕密度信息
- `LocalContext.current`：获取Android Context，进而获取更多系统信息
- `WindowInsets`：提供窗口插图、安全区域等信息
- `isSystemInDarkTheme()`：检测系统深色模式
- `rememberWindowInfo()`：自定义工具函数获取窗口信息
- `Build.VERSION`：获取Android操作系统版本

主要可获取的设备信息：

```kotlin
// 使用Composable函数中的环境变量获取信息
@Composable
fun DeviceInfoDisplay() {
    val configuration = LocalConfiguration.current
    val density = LocalDensity.current
    val context = LocalContext.current
    
    val screenWidthDp = configuration.screenWidthDp
    val screenHeightDp = configuration.screenHeightDp
    val densityDpi = configuration.densityDpi
    val orientation = configuration.orientation
    val uiMode = configuration.uiMode
    val smallestScreenWidthDp = configuration.smallestScreenWidthDp
    val fontScale = configuration.fontScale
    
    // 使用系统服务获取更多信息
    val displayMetrics = context.resources.displayMetrics
    val windowManager = context.getSystemService(Context.WINDOW_SERVICE) as WindowManager
    val screenSize = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
        val metrics = windowManager.currentWindowMetrics
        "宽 ${metrics.bounds.width()}px x 高 ${metrics.bounds.height()}px"
    } else {
        "宽 ${displayMetrics.widthPixels}px x 高 ${displayMetrics.heightPixels}px"
    }
    
    // Display info...
}
```

### 屏幕尺寸响应

Jetpack Compose的响应式设计主要通过以下方式实现：

- `BoxWithConstraints`：根据父约束确定布局策略
- `LocalConfiguration.current`：获取屏幕尺寸信息
- `WindowSizeClass`：Material 3提供的窗口尺寸分类
- `ConstraintLayout`：约束布局实现复杂响应式UI
- 自适应布局：使用弹性布局、百分比尺寸适应不同屏幕
- `Modifier.fillMaxSize(fraction)`：基于父容器比例的尺寸定义
- 自定义窗口信息辅助工具：定制响应式布局决策
- Compose布局模型：声明式UI的固有响应式特性

响应式布局示例：

```kotlin
// 使用BoxWithConstraints实现响应式布局
@Composable
fun ResponsiveLayout() {
    BoxWithConstraints {
        // 基于约束条件判断布局类型
        val layoutType = when {
            maxWidth < 600.dp -> LayoutType.COMPACT
            maxWidth < 840.dp -> LayoutType.MEDIUM
            else -> LayoutType.EXPANDED
        }
        
        // 根据布局类型应用不同UI
        when (layoutType) {
            LayoutType.COMPACT -> CompactLayout()
            LayoutType.MEDIUM -> MediumLayout()
            LayoutType.EXPANDED -> ExpandedLayout()
        }
    }
}

// 使用WindowSizeClass (Material 3)实现响应式布局
@Composable
fun MaterialResponsiveLayout() {
    val windowSizeClass = calculateWindowSizeClass()
    
    when (windowSizeClass.widthSizeClass) {
        WindowWidthSizeClass.Compact -> CompactLayout()
        WindowWidthSizeClass.Medium -> MediumLayout()
        WindowWidthSizeClass.Expanded -> ExpandedLayout()
    }
}

// 基于百分比的响应式组件
@Composable
fun ResponsiveContainer() {
    val configuration = LocalConfiguration.current
    val screenWidth = configuration.screenWidthDp.dp
    
    Box(
        modifier = Modifier
            .width(screenWidth * 0.8f) // 屏幕宽度的80%
            .height(screenWidth * 0.5f) // 宽高比1.6:1
            .background(Color.LightGray)
    ) {
        Text("响应式容器")
    }
}
```

### 方向变化处理

Jetpack Compose处理设备方向变化的方式：

- `LocalConfiguration.current.orientation`：获取当前方向
- `activity.requestedOrientation`：设置活动的方向
- 内置响应方向变化重组：Compose自动响应配置变化
- `AndroidView`与`Activity.configChanges`：细粒度控制方向变化行为
- `rememberSaveable`：保存状态在配置变化时恢复
- `WindowInsets`方向感知：窗口内嵌入随方向自动适配

方向变化处理示例：

```kotlin
// 检测方向并应用相应布局
@Composable
fun OrientationAwareLayout() {
    val configuration = LocalConfiguration.current
    val isPortrait = configuration.orientation == Configuration.ORIENTATION_PORTRAIT
    
    if (isPortrait) {
        PortraitLayout()
    } else {
        LandscapeLayout()
    }
}

// 固定屏幕方向
@Composable
fun LockScreenOrientation(orientation: Int) {
    val context = LocalContext.current
    DisposableEffect(orientation) {
        val activity = context.findActivity() ?: return@DisposableEffect onDispose {}
        val originalOrientation = activity.requestedOrientation
        activity.requestedOrientation = orientation
        onDispose {
            // 组件销毁时恢复原方向
            activity.requestedOrientation = originalOrientation
        }
    }
}

// 使用例
@Composable
fun PortraitOnlyScreen() {
    LockScreenOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT)
    // 屏幕内容...
}

// 方向变化时保存状态
@Composable
fun StatePreservingScreen() {
    var count by rememberSaveable { mutableStateOf(0) }
    
    Column(modifier = Modifier.fillMaxSize()) {
        Text("旋转屏幕后计数会保留: $count")
        Button(onClick = { count++ }) {
            Text("增加计数")
        }
    }
}
```

### 安全区域适配

Jetpack Compose提供了处理设备安全区域的方法：

- `WindowInsets`：获取系统UI占用的窗口插入区域
- `Modifier.windowInsetsPadding()`：添加窗口插入边距
- `Modifier.imePadding()`：自动适配输入法区域
- `Modifier.systemBarsPadding()`：适配系统栏（状态栏、导航栏）
- `Scaffold`：Material组件自动处理安全区域
- 可组合的窗口插入区域：自定义特定区域的适配逻辑
- 动态插图处理：响应系统UI可见性变化

安全区域适配示例：

```kotlin
// 使用WindowInsets处理安全区域
@Composable
fun SafeAreaAwareContent() {
    Box(
        modifier = Modifier
            .fillMaxSize()
            .windowInsetsPadding(WindowInsets.safeDrawing) // 适配安全绘制区域
    ) {
        // 内容不会被系统UI遮挡
        Content()
    }
}

// 使用Scaffold自动处理安全区域
@Composable
fun ScaffoldSafeAreaExample() {
    Scaffold(
        topBar = { TopAppBar(title = { Text("安全区域示例") }) },
        bottomBar = { BottomAppBar { Text("底部导航栏") } },
        // Scaffold自动处理内容不被状态栏、导航栏遮挡
    ) { innerPadding ->
        Box(modifier = Modifier.padding(innerPadding)) {
            // 内容区域
            Text("内容不会被系统UI遮挡")
        }
    }
}

// 处理键盘弹出
@Composable
fun KeyboardAwareInput() {
    Column(
        modifier = Modifier
            .fillMaxSize()
            .imePadding() // 自动处理键盘弹出的空间
    ) {
        Spacer(modifier = Modifier.weight(1f))
        TextField(
            value = "输入内容",
            onValueChange = {},
            modifier = Modifier.fillMaxWidth()
        )
    }
}

// 获取各种系统UI的尺寸
@Composable
fun SystemInsetsInfo() {
    val statusBars = WindowInsets.statusBars
    val navigationBars = WindowInsets.navigationBars
    val ime = WindowInsets.ime
    val displayCutout = WindowInsets.displayCutout
    
    val density = LocalDensity.current
    
    val statusBarHeight = with(density) { statusBars.getTop(density).toDp() }
    val navBarHeight = with(density) { navigationBars.getBottom(density).toDp() }
    
    // Display insets info...
}
```

### 设备特性检测

Jetpack Compose中检测设备特性的方式：

- `Build.VERSION.SDK_INT`：检测Android API级别
- `LocalContext.current`：获取context访问系统服务
- `PackageManager`：检查设备功能和应用权限
- `isSystemInDarkTheme()`：检测系统深色模式
- `LocalConfiguration.current`：检测屏幕尺寸、密度等配置
- `LocalHapticFeedback.current`：触觉反馈能力
- `LocalFontFamilyResolver.current`：字体支持
- `Build.MODEL` & `Build.MANUFACTURER`：设备型号和制造商信息
- `Activity.resources.configuration`：获取详细配置信息
- `LocalView.current.display`：访问显示器信息

设备特性检测示例：

```kotlin
// 平台版本检测
@Composable
fun ApiLevelAwareComponent() {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
        // Android 12 (API 31)及以上特性
        Android12Feature()
    } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
        // Android 11 (API 30)特性
        Android11Feature()
    } else {
        // 低版本后备实现
        LegacyFeature()
    }
}

// 检测设备特性和硬件
@Composable
fun DeviceCapabilitiesInfo() {
    val context = LocalContext.current
    val packageManager = context.packageManager
    
    val hasCamera = packageManager.hasSystemFeature(PackageManager.FEATURE_CAMERA)
    val hasFingerprint = packageManager.hasSystemFeature(PackageManager.FEATURE_FINGERPRINT)
    val hasBluetooth = packageManager.hasSystemFeature(PackageManager.FEATURE_BLUETOOTH)
    val hasGps = packageManager.hasSystemFeature(PackageManager.FEATURE_LOCATION_GPS)
    
    val isTv = packageManager.hasSystemFeature(PackageManager.FEATURE_LEANBACK)
    val isWatch = packageManager.hasSystemFeature(PackageManager.FEATURE_WATCH)
    val isAutomotive = packageManager.hasSystemFeature(PackageManager.FEATURE_AUTOMOTIVE)
    
    // 获取屏幕刷新率
    val display = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
        context.display
    } else {
        @Suppress("DEPRECATION")
        (context.getSystemService(Context.WINDOW_SERVICE) as WindowManager).defaultDisplay
    }
    
    val refreshRate = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R) {
        display?.refreshRate ?: 60f
    } else {
        @Suppress("DEPRECATION")
        display?.refreshRate ?: 60f
    }
    
    // 检测暗黑模式
    val isDarkTheme = isSystemInDarkTheme()
    
    // 获取更多设备详情
    val deviceModel = Build.MODEL
    val manufacturer = Build.MANUFACTURER
    val apiLevel = Build.VERSION.SDK_INT
    
    // Display capabilities...
}
```

### 平台特性与兼容性

Jetpack Compose的设备信息和适配特性：

- **Android原生集成**：紧密集成Android系统，可获取全面的平台特性
- **基于配置的重组**：自动响应配置变化（如方向变化、深色模式切换）
- **Material 3窗口尺寸类**：提供标准化的响应式布局决策框架
- **动态适配**：处理系统UI可见性变化、软键盘弹出等动态场景
- **高级窗口插入区域API**：简化安全区域处理，支持刘海屏、打孔屏等
- **声明式UI模型**：简化状态与UI同步，使响应式设计更直观
- **API兼容性**：向后兼容，同时提供新API访问最新Android特性
- **可组合性**：组件可自由组合，易于创建自定义响应逻辑
- **Jetpack集成**：与其他Jetpack库无缝协作（如Navigation、ViewModel）
- **性能优化**：智能重组系统减少不必要的UI更新

## iOS UIKit

### 设备信息获取

UIKit提供了多种获取设备信息的方式：

- `UIScreen.main`：获取主屏幕尺寸、缩放比例等信息
- `UIDevice.current`：获取设备型号、系统版本、电池状态等
- `UITraitCollection`：获取屏幕尺寸类型、显示比例、用户界面样式等
- `NSProcessInfo.processInfo`：获取系统内存、处理器数量等
- `UIApplication.shared`：应用状态、界面方向等信息
- `Bundle.main`：获取应用信息（版本、构建号等）

主要可获取的设备信息：

```swift
// 获取设备基本信息
let screen = UIScreen.main
let screenBounds = screen.bounds
let screenWidth = screenBounds.width
let screenHeight = screenBounds.height
let screenScale = screen.scale
let nativeScale = screen.nativeScale

// 获取设备型号和系统信息
let device = UIDevice.current
let deviceModel = device.model
let deviceName = device.name
let systemName = device.systemName
let systemVersion = device.systemVersion
let deviceOrientation = device.orientation
let userInterfaceIdiom = device.userInterfaceIdiom
let isIPad = (userInterfaceIdiom == .pad)

// 获取电池信息
device.isBatteryMonitoringEnabled = true
let batteryLevel = device.batteryLevel
let batteryState = device.batteryState

// 获取系统信息
let processInfo = ProcessInfo.processInfo
let physicalMemory = processInfo.physicalMemory
let processorCount = processInfo.processorCount
let operatingSystemVersion = processInfo.operatingSystemVersion
let lowPowerModeEnabled = processInfo.isLowPowerModeEnabled
```

### 屏幕尺寸响应

UIKit的响应式设计主要通过以下方式实现：

- `Auto Layout`：约束布局系统，动态适应不同屏幕尺寸
- `UITraitCollection`：根据水平和垂直尺寸类别调整布局
- `UITraitEnvironment`：响应特征环境变化的协议
- `Size Classes`：紧凑型(.compact)和常规型(.regular)尺寸类别
- `UIContentContainer`：响应容器尺寸变化的协议
- `UIViewController`：视图控制器的尺寸调整方法
- `Safe Area Layout Guide`：安全区域布局指南
- `Margin Layout Guide`：边距布局指南
- `NSLayoutConstraint.activate`：激活约束组

响应式布局示例：

```swift
// 使用Auto Layout创建响应式布局
func setupLayout() {
    let contentView = UIView()
    contentView.translatesAutoresizingMaskIntoConstraints = false
    view.addSubview(contentView)
    
    // 居中并设置宽度为屏幕的80%
    NSLayoutConstraint.activate([
        contentView.centerXAnchor.constraint(equalTo: view.centerXAnchor),
        contentView.centerYAnchor.constraint(equalTo: view.centerYAnchor),
        contentView.widthAnchor.constraint(equalTo: view.widthAnchor, multiplier: 0.8),
        contentView.heightAnchor.constraint(equalTo: contentView.widthAnchor, multiplier: 0.6)
    ])
}

// 使用UITraitCollection实现响应式设计
override func traitCollectionDidChange(_ previousTraitCollection: UITraitCollection?) {
    super.traitCollectionDidChange(previousTraitCollection)
    
    if traitCollection.horizontalSizeClass == .compact {
        // 紧凑型宽度 (如iPhone竖屏)
        stackView.axis = .vertical
    } else {
        // 常规型宽度 (如iPad或iPhone横屏)
        stackView.axis = .horizontal
    }
}

// 适配不同设备类型
func setupResponsiveUI() {
    if UIDevice.current.userInterfaceIdiom == .pad {
        // iPad专用布局
        setupIPadLayout()
    } else {
        // iPhone专用布局
        setupIPhoneLayout()
    }
}
```

### 方向变化处理

UIKit处理设备方向变化的方式：

- `UIDevice.orientation`：获取设备物理方向
- `UIApplication.shared.statusBarOrientation`：获取应用UI方向
- `UIViewController`方向方法：处理控制器方向变化
- `Info.plist`：配置应用支持的方向
- `UIDeviceOrientationDidChangeNotification`：设备方向变化通知
- `UIViewController.supportedInterfaceOrientations`：控制器支持的方向
- `UIViewController.preferredInterfaceOrientationForPresentation`：首选的展示方向
- `AppDelegate.application(_:supportedInterfaceOrientationsFor:)`：全局控制支持的方向

方向变化处理示例：

```swift
// 在视图控制器中响应方向变化
override func viewWillTransition(to size: CGSize, with coordinator: UIViewControllerTransitionCoordinator) {
    super.viewWillTransition(to: size, with: coordinator)
    
    // 判断当前方向
    let isPortrait = size.height > size.width
    
    coordinator.animate(alongsideTransition: { _ in
        if isPortrait {
            // 竖屏布局调整
            self.setupPortraitLayout()
        } else {
            // 横屏布局调整
            self.setupLandscapeLayout()
        }
    })
}

// 限制视图控制器支持的方向
override var supportedInterfaceOrientations: UIInterfaceOrientationMask {
    return .portrait // 仅支持竖屏
}

// 监听设备方向变化通知
override func viewDidLoad() {
    super.viewDidLoad()
    
    NotificationCenter.default.addObserver(
        self,
        selector: #selector(deviceOrientationDidChange),
        name: UIDevice.orientationDidChangeNotification,
        object: nil
    )
    
    // 启用设备方向通知
    UIDevice.current.beginGeneratingDeviceOrientationNotifications()
}

@objc func deviceOrientationDidChange() {
    let orientation = UIDevice.current.orientation
    
    switch orientation {
    case .portrait:
        updateForPortrait()
    case .landscapeLeft, .landscapeRight:
        updateForLandscape()
    default:
        break // 忽略其他方向如faceUp, faceDown等
    }
}

deinit {
    // 移除观察者并停止生成通知
    NotificationCenter.default.removeObserver(self)
    UIDevice.current.endGeneratingDeviceOrientationNotifications()
}
```

### 安全区域适配

UIKit提供了多种方式处理设备的安全区域：

- `view.safeAreaLayoutGuide`：安全区域布局指南
- `view.safeAreaInsets`：安全区域插入距离
- `UIViewController.additionalSafeAreaInsets`：额外的安全区域插入
- `UIEdgeInsets`：定义四边插入数值
- `UIScrollView.contentInsetAdjustmentBehavior`：滚动视图内容插入调整行为
- `UIScrollView.adjustedContentInset`：调整后的内容插入

安全区域适配示例：

```swift
// 使用安全区域布局指南
func setupWithSafeArea() {
    let contentView = UIView()
    contentView.translatesAutoresizingMaskIntoConstraints = false
    view.addSubview(contentView)
    
    NSLayoutConstraint.activate([
        contentView.topAnchor.constraint(equalTo: view.safeAreaLayoutGuide.topAnchor),
        contentView.leadingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.leadingAnchor),
        contentView.trailingAnchor.constraint(equalTo: view.safeAreaLayoutGuide.trailingAnchor),
        contentView.bottomAnchor.constraint(equalTo: view.safeAreaLayoutGuide.bottomAnchor)
    ])
}

// 手动处理安全区域
func setupWithSafeAreaInsets() {
    let safeAreaInsets = view.safeAreaInsets
    
    contentView.frame = CGRect(
        x: safeAreaInsets.left,
        y: safeAreaInsets.top,
        width: view.bounds.width - safeAreaInsets.left - safeAreaInsets.right,
        height: view.bounds.height - safeAreaInsets.top - safeAreaInsets.bottom
    )
}

// 自定义安全区域
override func viewDidLoad() {
    super.viewDidLoad()
    
    // 为顶部添加额外的安全区域
    additionalSafeAreaInsets = UIEdgeInsets(top: 20, left: 0, bottom: 0, right: 0)
}

// 配置滚动视图的安全区域行为
func setupScrollView() {
    scrollView.contentInsetAdjustmentBehavior = .always
    
    // iOS 11之前的处理方式
    if #available(iOS 11.0, *) {
        // 使用新API
    } else {
        automaticallyAdjustsScrollViewInsets = true
    }
}

// 监听安全区域变化
override func viewSafeAreaInsetsDidChange() {
    super.viewSafeAreaInsetsDidChange()
    
    // 安全区域变化时更新布局
    updateLayoutForSafeArea()
}
```

### 设备特性检测

UIKit中检测设备特性的方式：

- `UIDevice.current.systemVersion`：检查iOS版本
- `UIDevice.current.userInterfaceIdiom`：设备类型（手机/平板等）
- `UIScreen.main.scale`：屏幕缩放比例
- `traitCollection.forceTouchCapability`：3D Touch/Force Touch支持
- `traitCollection.displayGamut`：显示色域
- `ProcessInfo.processInfo.thermalState`：设备热状态
- `ProcessInfo.processInfo.isLowPowerModeEnabled`：低电量模式
- `UIAccessibility`：无障碍功能状态
- `AVCaptureDevice.authorizationStatus`：相机权限状态
- `CLLocationManager.authorizationStatus`：位置权限状态

设备特性检测示例：

```swift
// 系统版本检测
func checkSystemVersion() {
    if #available(iOS 15, *) {
        // iOS 15及以上专用代码
        setupForIOS15()
    } else if #available(iOS 14, *) {
        // iOS 14专用代码
        setupForIOS14()
    } else {
        // 低版本兼容代码
        setupForLegacy()
    }
}

// 设备类型检测
func setupBasedOnDevice() {
    switch UIDevice.current.userInterfaceIdiom {
    case .phone:
        // iPhone专用设置
        setupForPhone()
    case .pad:
        // iPad专用设置
        setupForPad()
    case .tv:
        // Apple TV专用设置
        setupForTV()
    case .mac:
        // Mac Catalyst专用设置
        setupForMac()
    default:
        // 其他设备
        setupDefault()
    }
}

// 特定硬件特性检测
func checkDeviceCapabilities() {
    // 检查Force Touch是否支持
    let forceTouchSupported = (traitCollection.forceTouchCapability == .available)
    
    // 检查显示色域
    let isWideColorDisplay = (traitCollection.displayGamut == .P3)
    
    // 检查是否为视网膜显示屏
    let isRetina = (UIScreen.main.scale >= 2.0)
    
    // 检查Face ID支持
    let isFaceIDAvailable = false // 需要使用LocalAuthentication框架
    
    // 检查深色模式
    let isDarkMode = (traitCollection.userInterfaceStyle == .dark)
    
    // 检查设备型号 (间接方法)
    let modelName = UIDevice.modelName // 需要自定义扩展
    
    // 检查电池状态
    UIDevice.current.isBatteryMonitoringEnabled = true
    let batteryLevel = UIDevice.current.batteryLevel * 100 // 转为百分比
    let batteryState = UIDevice.current.batteryState
    
    // 检查内存警告
    NotificationCenter.default.addObserver(
        self,
        selector: #selector(didReceiveMemoryWarning),
        name: UIApplication.didReceiveMemoryWarningNotification,
        object: nil
    )
}

// 设备模型名称扩展
extension UIDevice {
    static var modelName: String {
        var systemInfo = utsname()
        uname(&systemInfo)
        let machineMirror = Mirror(reflecting: systemInfo.machine)
        let identifier = machineMirror.children.reduce("") { identifier, element in
            guard let value = element.value as? Int8, value != 0 else { return identifier }
            return identifier + String(UnicodeScalar(UInt8(value)))
        }
        
        // 映射标识符到常见名称
        switch identifier {
        case "iPhone13,1": return "iPhone 12 mini"
        case "iPhone13,2": return "iPhone 12"
        case "iPhone13,3": return "iPhone 12 Pro"
        case "iPhone13,4": return "iPhone 12 Pro Max"
        // 添加更多型号...
        default: return identifier
        }
    }
}
```

### 平台特性与兼容性

UIKit的设备信息和适配特性：

- **iOS原生支持**：直接访问iOS设备硬件和系统信息
- **Auto Layout系统**：强大的约束布局系统，适配任何屏幕尺寸
- **尺寸类别API**：通过水平和垂直尺寸类别实现灵活响应式布局
- **安全区域适配**：完善的API处理刘海屏、Home Indicator等
- **设备族适配**：支持iPhone、iPad、Mac、Apple TV等多种设备
- **方向处理**：丰富的API控制和响应方向变化
- **适配层级**：从应用级到控制器级的精细适配控制
- **渐进式增强**：通过API可用性检查支持新旧系统版本
- **硬件差异处理**：适应不同设备硬件特性（如Force Touch）
- **系统特性集成**：与iOS系统特性（如Dark Mode、Dynamic Type）紧密集成

## iOS SwiftUI

### 设备信息获取

SwiftUI提供了多种获取设备信息的方式：

- `@Environment`：通过环境变量获取设备和视图环境信息
- `UIScreen.main`：通过UIKit桥接获取屏幕信息
- `UIDevice.current`：通过UIKit桥接获取设备信息
- `GeometryReader`：获取视图几何信息
- `AppStorage`/`SceneStorage`：访问设备和场景持久化存储
- `ProcessInfo.processInfo`：获取系统和进程信息
- `Bundle.main`：获取应用信息

主要可获取的设备信息：

```swift
// 通过@Environment获取环境信息
struct DeviceInfoView: View {
    // 获取环境值
    @Environment(\.colorScheme) private var colorScheme
    @Environment(\.horizontalSizeClass) private var horizontalSizeClass
    @Environment(\.verticalSizeClass) private var verticalSizeClass
    @Environment(\.displayScale) private var displayScale
    @Environment(\.dynamicTypeSize) private var dynamicTypeSize
    @Environment(\.locale) private var locale
    @Environment(\.calendar) private var calendar
    @Environment(\.layoutDirection) private var layoutDirection
    @Environment(\.pixelLength) private var pixelLength // iOS 16+
    
    // 通过UIKit桥接获取设备信息
    private var screenWidth = UIScreen.main.bounds.width
    private var screenHeight = UIScreen.main.bounds.height
    private var screenScale = UIScreen.main.scale
    
    private var deviceModel = UIDevice.current.model
    private var systemName = UIDevice.current.systemName
    private var systemVersion = UIDevice.current.systemVersion
    private var deviceName = UIDevice.current.name
    
    var body: some View {
        List {
            Section("环境信息") {
                InfoRow(title: "颜色方案", value: colorScheme == .dark ? "暗色" : "亮色")
                InfoRow(title: "水平尺寸类别", value: horizontalSizeClass == .compact ? "紧凑" : "常规")
                InfoRow(title: "垂直尺寸类别", value: verticalSizeClass == .compact ? "紧凑" : "常规")
                InfoRow(title: "显示缩放", value: String(format: "%.1f", displayScale))
                InfoRow(title: "动态类型大小", value: "\(dynamicTypeSize)")
                InfoRow(title: "区域设置", value: "\(locale.identifier)")
                InfoRow(title: "布局方向", value: layoutDirection == .leftToRight ? "从左到右" : "从右到左")
            }
            
            Section("设备信息") {
                InfoRow(title: "屏幕尺寸", value: "\(screenWidth) x \(screenHeight) 点")
                InfoRow(title: "屏幕缩放", value: "\(screenScale)x")
                InfoRow(title: "设备型号", value: deviceModel)
                InfoRow(title: "系统名称", value: systemName)
                InfoRow(title: "系统版本", value: systemVersion)
                InfoRow(title: "设备名称", value: deviceName)
            }
        }
    }
}

struct InfoRow: View {
    let title: String
    let value: String
    
    var body: some View {
        HStack {
            Text(title)
            Spacer()
            Text(value)
                .foregroundColor(.secondary)
        }
    }
}
```

### 屏幕尺寸响应

SwiftUI的响应式设计主要通过以下方式实现：

- `GeometryReader`：提供容器几何信息的视图容器
- `@Environment(\.horizontalSizeClass)`：水平尺寸类别（紧凑型/常规型）
- `@Environment(\.verticalSizeClass)`：垂直尺寸类别（紧凑型/常规型）
- `@Environment(\.dynamicTypeSize)`：动态类型大小
- 弹性布局：`HStack`、`VStack`、`ZStack`等灵活排列组件
- `.frame()`修饰符：设置视图尺寸、最小/最大尺寸
- `.layoutPriority()`：设置布局优先级
- `.aspectRatio()`：保持视图宽高比
- 内置的条件修饰符：`.iPad`、`.iPhone`等设备特定修饰符
- 自定义视图构建器：根据设备条件返回不同布局

响应式布局示例：

```swift
// 使用GeometryReader创建响应式布局
struct ResponsiveLayoutView: View {
    var body: some View {
        GeometryReader { geometry in
            // 根据容器宽度选择不同布局
            if geometry.size.width > 700 {
                HStack(spacing: 20) {
                    SidebarView()
                        .frame(width: 250)
                    ContentView()
                }
            } else {
                VStack {
                    ContentView()
                    TabBarView()
                }
            }
        }
    }
}

// 使用尺寸类别实现响应式设计
struct SizeClassResponsiveView: View {
    @Environment(\.horizontalSizeClass) private var horizontalSizeClass
    
    var body: some View {
        Group {
            if horizontalSizeClass == .compact {
                // 紧凑型布局 (如iPhone竖屏)
                CompactLayoutView()
            } else {
                // 常规型布局 (如iPad或iPhone横屏)
                RegularLayoutView()
            }
        }
    }
}

// 使用ViewThatFits根据可用空间选择合适的视图
struct AdaptiveContentView: View {
    var body: some View {
        ViewThatFits { // iOS 16+
            // 优先使用宽布局，若不适合则使用紧凑布局
            WideLayoutView()
            CompactLayoutView()
        }
    }
}

// 使用比例尺寸和灵活布局
struct FlexibleLayoutView: View {
    var body: some View {
        VStack {
            // 占据父视图80%宽度，按16:9比例
            Rectangle()
                .fill(Color.blue)
                .frame(width: UIScreen.main.bounds.width * 0.8)
                .aspectRatio(16/9, contentMode: .fit)
            
            // 弹性布局示例
            HStack {
                Text("固定宽度")
                    .frame(width: 100)
                
                Text("弹性宽度")
                    .frame(maxWidth: .infinity)
                
                Text("权重为2的弹性宽度")
                    .frame(maxWidth: .infinity)
                    .layoutPriority(2)
            }
            .padding()
        }
    }
}
```

### 方向变化处理

SwiftUI处理设备方向变化的方式：

- 内置自适应布局：布局自动适应方向变化
- `@Environment(\.horizontalSizeClass)`：检测紧凑/常规变化（间接检测方向）
- `UIDevice.current.orientation`：通过UIKit获取设备方向
- `GeometryReader`：检测尺寸变化以推断方向
- `onRotate` 修饰符：自定义的方向变化检测修饰符
- `UIDeviceOrientationDidChangeNotification`：通过NotificationCenter监听方向变化
- `UISupportedInterfaceOrientations`：在Info.plist中配置支持的方向
- `UIApplicationSceneManifest`：配置场景支持的方向

方向变化处理示例：

```swift
// 创建方向检测修饰符
struct DeviceRotationViewModifier: ViewModifier {
    let action: (UIDeviceOrientation) -> Void
    
    func body(content: Content) -> some View {
        content
            .onAppear()
            .onReceive(NotificationCenter.default.publisher(for: UIDevice.orientationDidChangeNotification)) { _ in
                action(UIDevice.current.orientation)
            }
    }
}

// 扩展View添加方便使用的修饰符
extension View {
    func onRotate(perform action: @escaping (UIDeviceOrientation) -> Void) -> some View {
        self.modifier(DeviceRotationViewModifier(action: action))
    }
}

// 使用方向监听创建响应式布局
struct OrientationAwareView: View {
    @State private var orientation = UIDeviceOrientation.unknown
    
    var body: some View {
        Group {
            if orientation.isPortrait || orientation == .unknown {
                PortraitLayoutView()
            } else if orientation.isLandscape {
                LandscapeLayoutView()
            } else {
                // 处理其他方向如faceUp, faceDown
                PortraitLayoutView()
            }
        }
        .onRotate { newOrientation in
            orientation = newOrientation
        }
        .onAppear {
            // 确保获取初始方向
            orientation = UIDevice.current.orientation
        }
    }
}

// 使用GeometryReader监测方向变化
struct GeometryOrientationView: View {
    var body: some View {
        GeometryReader { geometry in
            // 通过判断宽高比确定方向
            let isLandscape = geometry.size.width > geometry.size.height
            
            if isLandscape {
                HStack {
                    Image(systemName: "sidebar.left")
                        .frame(width: 200)
                    Text("横屏内容")
                        .frame(maxWidth: .infinity)
                }
            } else {
                VStack {
                    Text("竖屏内容")
                        .frame(maxWidth: .infinity)
                    
                    Spacer()
                    
                    HStack {
                        ForEach(1..<5) { i in
                            Image(systemName: "circle.\(i).fill")
                                .frame(maxWidth: .infinity)
                        }
                    }
                    .padding()
                }
            }
        }
    }
}
```

### 安全区域适配

SwiftUI提供了多种方式处理设备的安全区域：

- `.edgesIgnoringSafeArea()`：指定边缘忽略安全区域
- `.ignoresSafeArea()`：更灵活的忽略安全区域修饰符（iOS 14+）
- `safeAreaInsets`：通过GeometryReader获取安全区域插入距离
- `@Environment(\.safeAreaInsets)`：环境变量获取安全区域插入（iOS 15+）
- `UIApplication.shared.windows`：通过UIKit获取安全区域
- `.padding(.safeAreaInsets)`：使用安全区域插入值增加内边距

安全区域适配示例：

```swift
// 基本的安全区域使用
struct SafeAreaBasicView: View {
    var body: some View {
        VStack {
            // 内容会自动尊重安全区域
            Text("默认尊重安全区域的内容")
            
            // 背景延伸到顶部安全区域外
            Text("标题")
                .frame(maxWidth: .infinity)
                .padding()
                .background(Color.blue)
                .foregroundColor(.white)
                .ignoresSafeArea(edges: .top)
            
            Spacer()
            
            // 背景延伸到底部安全区域外，但内容保持在安全区域内
            VStack {
                Text("底部工具栏")
            }
            .frame(maxWidth: .infinity)
            .padding()
            .background(Color.secondary.opacity(0.3).ignoresSafeArea(edges: .bottom))
        }
    }
}

// 获取安全区域尺寸
struct SafeAreaInspectorView: View {
    var body: some View {
        GeometryReader { geometry in
            let safeArea = geometry.safeAreaInsets
            
            VStack {
                Text("安全区域尺寸")
                    .font(.headline)
                
                VStack(alignment: .leading) {
                    Text("顶部: \(Int(safeArea.top)) 点")
                    Text("底部: \(Int(safeArea.bottom)) 点")
                    Text("左侧: \(Int(safeArea.leading)) 点")
                    Text("右侧: \(Int(safeArea.trailing)) 点")
                }
                .padding()
                .background(Color.secondary.opacity(0.2))
                .cornerRadius(8)
            }
            .frame(maxWidth: .infinity, maxHeight: .infinity)
            .background(
                // 使用不同颜色标记安全区域边界
                ZStack {
                    // 整个屏幕
                    Color.green.opacity(0.2)
                    
                    // 安全区域
                    VStack {
                        Spacer().frame(height: safeArea.top)
                        HStack {
                            Spacer().frame(width: safeArea.leading)
                            Color.blue.opacity(0.2)
                            Spacer().frame(width: safeArea.trailing)
                        }
                        Spacer().frame(height: safeArea.bottom)
                    }
                }
                .ignoresSafeArea()
            )
        }
    }
}

// 使用UIKit获取安全区域 (替代方案)
struct UIKitSafeAreaView: View {
    var safeAreaInsets: UIEdgeInsets {
        guard let scene = UIApplication.shared.connectedScenes.first as? UIWindowScene,
              let window = scene.windows.first else {
            return .zero
        }
        return window.safeAreaInsets
    }
    
    var body: some View {
        Text("顶部安全区域: \(safeAreaInsets.top)")
            .padding()
    }
}

// iOS 15+ 使用环境变量直接获取安全区域
struct EnvironmentSafeAreaView: View {
    @Environment(\.safeAreaInsets) private var safeAreaInsets // iOS 15+
    
    var body: some View {
        if #available(iOS 15.0, *) {
            Text("底部安全区域: \(safeAreaInsets.bottom)")
                .padding()
        } else {
            // 兼容性回退
            UIKitSafeAreaView()
        }
    }
}
```

### 设备特性检测

SwiftUI中检测设备特性的方式：

- `@Environment`：获取环境变量以检测系统配置
- `UIDevice`和`UIScreen`：通过UIKit桥接检测设备信息
- `#available`：编译时检查API可用性
- `ProcessInfo.processInfo`：检测系统特性如低电量模式
- 条件编译：`#if os(...)`和`#if targetEnvironment(...)`
- `@Environment(\.horizontalSizeClass)`：检测设备布局特性
- `UITraitCollection`：通过UIKit获取更多特性
- `@Environment(\.accessibilityEnabled)`：检测无障碍功能
- `@Environment(\.colorSchemeContrast)`：检测高对比度模式
- `UIAccessibility.isVoiceOverRunning`：检测VoiceOver状态

设备特性检测示例：

```swift
// 使用环境变量检测设备特性
struct DeviceCapabilitiesView: View {
    @Environment(\.colorScheme) private var colorScheme
    @Environment(\.horizontalSizeClass) private var horizontalSizeClass
    @Environment(\.dynamicTypeSize) private var dynamicTypeSize
    @Environment(\.accessibilityEnabled) private var accessibilityEnabled
    @Environment(\.colorSchemeContrast) private var colorContrast
    @Environment(\.legibilityWeight) private var legibilityWeight
    
    // 检测低电量模式
    @State private var isLowPowerMode = ProcessInfo.processInfo.isLowPowerModeEnabled
    
    // 检测设备类型
    private var deviceIdiom: String {
        switch UIDevice.current.userInterfaceIdiom {
        case .phone: return "iPhone"
        case .pad: return "iPad"
        case .mac: return "Mac"
        case .tv: return "Apple TV"
        case .vision: return "Apple Vision Pro" // iOS 17+
        default: return "其他"
        }
    }
    
    // 检测系统版本
    private var systemVersion: String {
        let version = UIDevice.current.systemVersion
        let majorVersion = version.split(separator: ".").first ?? ""
        return "iOS \(majorVersion)"
    }
    
    var body: some View {
        List {
            Section("界面特性") {
                FeatureRow(name: "颜色方案", value: colorScheme == .dark ? "暗色" : "亮色")
                FeatureRow(name: "布局类型", value: horizontalSizeClass == .compact ? "紧凑型布局" : "常规型布局")
                FeatureRow(name: "文字大小", value: "\(dynamicTypeSize)")
                FeatureRow(name: "无障碍模式", value: accessibilityEnabled ? "启用" : "禁用")
                FeatureRow(name: "颜色对比度", value: colorContrast == .increased ? "增强" : "标准")
                FeatureRow(name: "文字权重", value: legibilityWeight == .bold ? "粗体" : "标准")
            }
            
            Section("设备特性") {
                FeatureRow(name: "设备类型", value: deviceIdiom)
                FeatureRow(name: "系统版本", value: systemVersion)
                FeatureRow(name: "低电量模式", value: isLowPowerMode ? "启用" : "禁用")
                FeatureRow(name: "多任务支持", value: UIDevice.current.isMultitaskingSupported ? "支持" : "不支持")
                FeatureRow(name: "有刘海屏", value: hasNotch ? "是" : "否")
                FeatureRow(name: "ProMotion显示", value: hasProMotionDisplay ? "支持" : "不支持")
            }
        }
        .onReceive(NotificationCenter.default.publisher(for: Notification.Name.NSProcessInfoPowerStateDidChange)) { _ in
            isLowPowerMode = ProcessInfo.processInfo.isLowPowerModeEnabled
        }
    }
    
    // 检测刘海屏
    private var hasNotch: Bool {
        guard let window = UIApplication.shared.windows.first else {
            return false
        }
        return window.safeAreaInsets.top > 20
    }
    
    // 检测ProMotion刷新率
    private var hasProMotionDisplay: Bool {
        return UIScreen.main.maximumFramesPerSecond > 60
    }
}

struct FeatureRow: View {
    let name: String
    let value: String
    
    var body: some View {
        HStack {
            Text(name)
            Spacer()
            Text(value)
                .foregroundColor(.secondary)
        }
    }
}

// 条件视图修饰符
struct ConditionalModifiersView: View {
    var body: some View {
        Text("设备特定的修饰符示例")
            .padding()
            .background(Color.blue.opacity(0.2))
            .cornerRadius(8)
            // iOS 16+ 特定修饰符
            .modifier {
                if #available(iOS 16.0, *) {
                    $0.bold()
                       .shadow(radius: 3)
                } else {
                    $0
                }
            }
            // 设备特定修饰符
            .modifier {
                if UIDevice.current.userInterfaceIdiom == .pad {
                    $0.font(.title)
                } else {
                    $0.font(.body)
                }
            }
    }
}
```

### 平台特性与兼容性

SwiftUI的设备信息和适配特性：

- **声明式UI设计**：自动适应布局变化，减少手动适配代码
- **环境变量系统**：通过@Environment传递设备和系统信息
- **内置响应式布局**：容器组件自动适应屏幕尺寸和方向
- **易用的安全区域API**：简化刘海屏、Home Indicator等适配
- **跨Apple平台支持**：一套代码支持iOS、iPadOS、macOS、tvOS和watchOS
- **深色模式内置支持**：自动响应系统外观变化
- **Dynamic Type适配**：响应用户字体大小设置
- **可组合性**：视图和修饰符可自由组合，创建复杂响应式布局
- **UIKit互操作性**：通过UIViewRepresentable和UIViewControllerRepresentable桥接
- **SwiftUI 2.0+增强**：iOS 14后提供更强大的布局工具和环境变量

## Vue

## 总结对比 