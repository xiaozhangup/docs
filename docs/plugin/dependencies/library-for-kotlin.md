---
sidebar_position: 3
---

# 第三方库 (Kotlin)

Kotlin 或 Kotlinx 相关库在 TabooLib 项目中有特殊的运行逻辑，前一章内容对其不可用。

本章以 `Kotlinx Serialization` 和 `Kotlinx Coroutines` 为例。

:::tip

本章不对如何引用/使用 Kotlin 相关库作详细解释。

:::

:::danger

无论如何，在使用 Kotlin 相关库时，都必须进行重定向。

:::

## Serialization

:::caution

截至目前，TabooLib 不支持 `1.4.0` 以上版本的 `Serialization`。

:::

### 重定向

首先对项目中使用到 `Serialization` 的部分进行重定向，这是 **先决条件**。

```kotlin title="build.gradle.kts"
taboolib {
    relocate("kotlinx.serialization", "kotlinx_1_3_3.serialization")
}
```

### 添加依赖

#### 打包模式（简单，体积较大）

直接将 `Serialization` 打包到插件内。

```kotlin
dependencies {
    taboo("org.jetbrains.kotlinx:kotlinx-serialization-core-jvm:1.3.3") { isTransitive = false }
    taboo("org.jetbrains.kotlinx:kotlinx-serialization-json-jvm:1.3.3") { isTransitive = false }
}
```

#### 引用模式（复杂，优化体积）

随服务端启动从 **阿里云中央仓库** 下载。

```kotlin
dependencies {
    compileOnly("org.jetbrains.kotlinx:kotlinx-serialization-json:1.3.3")
}
```

因动态加载，因此我们还需要在代码中添加能够被 TabooLib 识别的依赖声明。

```kotlin
@RuntimeDependencies(
    RuntimeDependency(
        "!org.jetbrains.kotlinx:kotlinx-serialization-core-jvm:1.3.3",
        test = "!kotlinx.serialization.Serializer",
        relocate = ["!kotlin.", "!kotlin@kotlin_version_escape@.", "!kotlinx.", "!kotlinx_1_3_3."],
        transitive = false
    ),
    RuntimeDependency(
        "!org.jetbrains.kotlinx:kotlinx-serialization-json-jvm:1.3.3",
        test = "!kotlinx.serialization.json.Json",
        relocate = ["!kotlin.", "!kotlin@kotlin_version_escape@.", "!kotlinx.", "!kotlinx_1_3_3."],
        transitive = false
    )
)
class RuntimeEnv
```

## Coroutines

### 重定向

首先对项目中使用到 `Coroutines` 的部分进行重定向，这是 **先决条件**。

```kotlin title="build.gradle.kts"
taboolib {
    relocate("kotlinx.coroutines", "kotlinx_1_3_3.coroutines")
}
```

### 添加依赖

#### 打包模式（简单，体积较大）

直接将 `Coroutines` 打包到插件内。

```kotlin
dependencies {
    taboo("org.jetbrains.kotlinx:kotlinx-coroutines-core-jvm:1.6.4") { isTransitive = false }
}
```

#### 引用模式（复杂，优化体积）

随服务端启动从 **阿里云中央仓库** 下载。

```kotlin
dependencies {
    compileOnly("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.6.4")
}
```

因动态加载，因此我们还需要在代码中添加能够被 TabooLib 识别的依赖声明。

```kotlin
@RuntimeDependencies(
    RuntimeDependency(
        "!org.jetbrains.kotlinx:kotlinx-coroutines-core-jvm:1.6.4",
        test = "!kotlinx.coroutines.GlobalScope",
        relocate = ["!kotlin.", "!kotlin@kotlin_version_escape@.", "!kotlinx.", "!kotlinx_1_6_4."],
        transitive = false
    )
)
class RuntimeEnv
```