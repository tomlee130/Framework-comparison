# 主流框架 Image 组件对比分析（2024）

## 目录
- [概述](#概述)
- [基本用法对比](#基本用法对比)
- [核心功能对比](#核心功能对比)
- [路由与参数传递](#路由与参数传递)
- [平台特性](#平台特性)
- [兼容性分析](#兼容性分析)
- [完整示例](#完整示例)

## 概述

本文档对比了六大主流框架的 Image 组件实现，包括：
- Flutter (v3.19)
- Android Jetpack Compose (v1.6)
- iOS UIKit (iOS 17)
- iOS SwiftUI (iOS 17)
- 鸿蒙 ArkUI (v4.0)
- Vue.js (v3.4)

## 基本用法对比

### Flutter
```dart
Image(
  image: NetworkImage('https://example.com/image.jpg'),
  width: 200,
  height: 200,
  fit: BoxFit.cover,
)

// 或使用简写方式
Image.network(
  'https://example.com/image.jpg',
  width: 200,
  height: 200,
  fit: BoxFit.cover,
)
```

### Jetpack Compose
```kotlin
Image(
    painter = rememberImagePainter("https://example.com/image.jpg"),
    contentDescription = "Image description",
    modifier = Modifier
        .size(200.dp)
        .clip(RoundedCornerShape(8.dp)),
    contentScale = ContentScale.Crop
)
```

### UIKit
```swift
let imageView = UIImageView(frame: CGRect(x: 0, y: 0, width: 200, height: 200))
imageView.contentMode = .scaleAspectFill
imageView.clipsToBounds = true

// 本地图片
imageView.image = UIImage(named: "imageName")

// 网络图片
if let url = URL(string: "https://example.com/image.jpg") {
    URLSession.shared.dataTask(with: url) { data, response, error in
        if let data = data, let image = UIImage(data: data) {
            DispatchQueue.main.async {
                imageView.image = image
            }
        }
    }.resume()
}
```

### SwiftUI
```swift
AsyncImage(url: URL(string: "https://example.com/image.jpg")) { phase in
    switch phase {
        case .empty:
            ProgressView()
        case .success(let image):
            image
                .resizable()
                .aspectRatio(contentMode: .fill)
                .frame(width: 200, height: 200)
        case .failure:
            Image(systemName: "photo")
        @unknown default:
            EmptyView()
    }
}
```

### ArkUI (鸿蒙)
```typescript
Image('https://example.com/image.jpg')
  .width(200)
  .height(200)
  .objectFit(ImageFit.Cover)
  .borderRadius(8)
```

### Vue.js
```vue
<template>
  <img
    :src="imageUrl"
    :alt="description"
    class="custom-image"
    @load="onImageLoad"
    @error="onImageError"
  />
</template>

<script setup>
const imageUrl = 'https://example.com/image.jpg'
const description = '图片描述'

const onImageLoad = () => {
  console.log('图片加载成功')
}

const onImageError = () => {
  console.log('图片加载失败')
}
</script>

<style scoped>
.custom-image {
  width: 200px;
  height: 200px;
  object-fit: cover;
  border-radius: 8px;
}
</style>
```

## 核心功能对比

### 图片加载来源
| 框架 | 本地图片 | 网络图片 | Base64 | 资源文件 | SVG支持 |
|-----|---------|----------|---------|----------|---------|
| Flutter | ✓ | ✓ | ✓ | ✓ | 需插件 |
| Compose | ✓ | ✓ | ✓ | ✓ | ✓ |
| UIKit | ✓ | ✓ | ✓ | ✓ | ✓ |
| SwiftUI | ✓ | ✓ | ✓ | ✓ | ✓ |
| ArkUI | ✓ | ✓ | ✓ | ✓ | ✓ |
| Vue | ✓ | ✓ | ✓ | ✓ | ✓ |

### 图片处理能力
| 框架 | 缩放模式 | 裁剪 | 圆角 | 边框 | 滤镜 | 占位图 | 加载动画 |
|-----|----------|------|------|------|------|---------|----------|
| Flutter | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Compose | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| UIKit | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | 需自实现 |
| SwiftUI | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| ArkUI | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Vue | ✓ | CSS | CSS | CSS | CSS | ✓ | CSS |

## 路由与参数传递

### Flutter
```dart
// 图片预览路由
Navigator.push(
  context,
  MaterialPageRoute(
    builder: (context) => ImagePreview(
      imageUrl: 'https://example.com/image.jpg',
    ),
  ),
);
```

### Jetpack Compose
```kotlin
// 使用 Navigation Compose
NavHost(navController, startDestination = "home") {
    composable("imagePreview/{imageUrl}") { backStackEntry ->
        val imageUrl = backStackEntry.arguments?.getString("imageUrl")
        ImagePreview(imageUrl = imageUrl)
    }
}
```

### UIKit
```swift
// 使用 Storyboard 或代码跳转
let previewVC = ImagePreviewViewController()
previewVC.imageUrl = "https://example.com/image.jpg"
navigationController?.pushViewController(previewVC, animated: true)
```

### SwiftUI
```swift
NavigationLink(
    destination: ImagePreview(imageUrl: imageUrl)
) {
    AsyncImage(url: URL(string: imageUrl))
}
```

### ArkUI
```typescript
// 页面路由
router.push({
  url: 'pages/imagePreview',
  params: {
    imageUrl: 'https://example.com/image.jpg'
  }
})
```

### Vue
```vue
<!-- Vue Router -->
<router-link 
  :to="{ 
    name: 'ImagePreview',
    params: { imageUrl: encodeURIComponent(imageUrl) }
  }"
>
  <img :src="imageUrl" />
</router-link>
```

## 平台特性

### Flutter
- 跨平台一致性渲染
- 内置图片缓存
- 支持自定义 ImageProvider
- 内存优化处理

### Jetpack Compose
- Material Design 3 集成
- Coil/Glide 图片加载库集成
- 协程支持
- 手势处理

### UIKit
- 原生性能
- Core Image 框架集成
- Metal 支持
- 内存优化

### SwiftUI
- 声明式语法
- 系统级图片缓存
- SF Symbols 集成
- Combine 框架支持

### ArkUI
- 原子化能力
- 统一渲染引擎
- 动效系统集成
- 多设备自适应

### Vue
- 虚拟 DOM
- 响应式数据
- 生态系统支持
- 自定义指令

## 兼容性分析

### Flutter
- Android 4.1+ (API 16+)
- iOS 11.0+
- Web (所有现代浏览器)
- Desktop (Windows, macOS, Linux)

### Jetpack Compose
- Android 5.0+ (API 21+)
- 需要 Kotlin 1.8.0+
- AndroidX 依赖

### UIKit
- iOS 11.0+
- iPadOS 11.0+
- tvOS 11.0+

### SwiftUI
- iOS 13.0+
- iPadOS 13.0+
- macOS 10.15+
- tvOS 13.0+
- watchOS 6.0+

### ArkUI
- 鸿蒙 OS 3.0+
- 支持多设备形态

### Vue
- 所有现代浏览器
- IE 11+ (需要 polyfills)
- SSR 支持

## 完整示例

### Flutter 图片预览示例
```dart
class ImagePreview extends StatelessWidget {
  final String imageUrl;

  const ImagePreview({Key? key, required this.imageUrl}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: InteractiveViewer(
          boundaryMargin: EdgeInsets.all(20.0),
          minScale: 0.5,
          maxScale: 4.0,
          child: Image.network(
            imageUrl,
            loadingBuilder: (context, child, progress) {
              if (progress == null) return child;
              return CircularProgressIndicator();
            },
            errorBuilder: (context, error, stackTrace) {
              return Icon(Icons.error);
            },
          ),
        ),
      ),
    );
  }
}
```

### Jetpack Compose 图片预览示例
```kotlin
@Composable
fun ImagePreview(imageUrl: String) {
    var scale by remember { mutableStateOf(1f) }
    var offset by remember { mutableStateOf(Offset.Zero) }

    Box(
        modifier = Modifier.fillMaxSize()
    ) {
        Image(
            painter = rememberImagePainter(
                data = imageUrl,
                builder = {
                    crossfade(true)
                    placeholder(R.drawable.placeholder)
                    error(R.drawable.error)
                }
            ),
            contentDescription = null,
            modifier = Modifier
                .fillMaxSize()
                .graphicsLayer(
                    scaleX = scale,
                    scaleY = scale,
                    translationX = offset.x,
                    translationY = offset.y
                )
                .pointerInput(Unit) {
                    detectTransformGestures { _, pan, zoom, _ ->
                        scale = (scale * zoom).coerceIn(0.5f, 3f)
                        offset += pan
                    }
                }
        )
    }
}
```

### SwiftUI 图片预览示例
```swift
struct ImagePreview: View {
    let imageUrl: String
    @State private var scale: CGFloat = 1.0
    
    var body: some View {
        AsyncImage(url: URL(string: imageUrl)) { phase in
            switch phase {
            case .empty:
                ProgressView()
            case .success(let image):
                image
                    .resizable()
                    .aspectRatio(contentMode: .fit)
                    .scaleEffect(scale)
                    .gesture(
                        MagnificationGesture()
                            .onChanged { value in
                                scale = value.magnitude
                            }
                    )
            case .failure:
                Image(systemName: "exclamationmark.triangle")
                    .foregroundColor(.red)
            @unknown default:
                EmptyView()
            }
        }
    }
}
```

### ArkUI 图片预览示例
```typescript
@Entry
@Component
struct ImagePreview {
  @State scale: number = 1
  private imageUrl: string

  build() {
    Column() {
      Image(this.imageUrl)
        .objectFit(ImageFit.Contain)
        .gesture(
          PinchGesture()
            .onActionStart((event: GestureEvent) => {
              this.scale = event.scale
            })
        )
    }
    .width('100%')
    .height('100%')
  }
}
```

### Vue 图片预览示例
```vue
<template>
  <div class="image-preview">
    <div
      class="image-container"
      :style="{
        transform: `scale(${scale}) translate(${position.x}px, ${position.y}px)`
      }"
      @wheel="handleZoom"
      @mousedown="startDrag"
      @mousemove="onDrag"
      @mouseup="stopDrag"
      @mouseleave="stopDrag"
    >
      <img
        :src="imageUrl"
        :alt="description"
        @load="onImageLoad"
        @error="onImageError"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'

const props = defineProps({
  imageUrl: {
    type: String,
    required: true
  },
  description: {
    type: String,
    default: ''
  }
})

const scale = ref(1)
const position = reactive({ x: 0, y: 0 })
const isDragging = ref(false)
const startPosition = reactive({ x: 0, y: 0 })

const handleZoom = (e) => {
  e.preventDefault()
  const delta = e.deltaY > 0 ? -0.1 : 0.1
  scale.value = Math.max(0.5, Math.min(3, scale.value + delta))
}

const startDrag = (e) => {
  isDragging.value = true
  startPosition.x = e.clientX - position.x
  startPosition.y = e.clientY - position.y
}

const onDrag = (e) => {
  if (isDragging.value) {
    position.x = e.clientX - startPosition.x
    position.y = e.clientY - startPosition.y
  }
}

const stopDrag = () => {
  isDragging.value = false
}

const onImageLoad = () => {
  console.log('图片加载成功')
}

const onImageError = () => {
  console.log('图片加载失败')
}
</script>

<style scoped>
.image-preview {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background: #000;
  display: flex;
  align-items: center;
  justify-content: center;
}

.image-container {
  transition: transform 0.1s ease-out;
  cursor: grab;
}

.image-container:active {
  cursor: grabbing;
}

img {
  max-width: 100vw;
  max-height: 100vh;
  object-fit: contain;
  user-select: none;
}
</style> 