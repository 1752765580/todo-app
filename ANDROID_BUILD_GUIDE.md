# 📱 Android APK 打包指南

## 已完成的工作 ✅

1. ✅ 配置 Next.js 静态导出
2. ✅ 安装 Capacitor 依赖
3. ✅ 初始化 Capacitor 并添加 Android 平台
4. ✅ 生成完整的 Android 项目结构
5. ✅ 同步 Web 资源到 Android 项目

## 🔧 在本地完成打包

### 前置要求

在你的电脑上需要安装：

1. **JDK 17+** (Java Development Kit)
   ```bash
   # macOS
   brew install openjdk@17
   
   # Windows (使用 Scoop)
   scoop install openjdk17
   
   # Linux
   sudo apt install openjdk-17-jdk
   ```

2. **Android SDK**
   - 安装 [Android Studio](https://developer.android.com/studio)
   - 或者仅安装 [Android Command Line Tools](https://developer.android.com/studio#command-tools)

3. **设置环境变量**
   ```bash
   # macOS/Linux (添加到 ~/.bashrc 或 ~/.zshrc)
   export ANDROID_HOME=$HOME/Android/Sdk
   export PATH=$PATH:$ANDROID_HOME/emulator
   export PATH=$PATH:$ANDROID_HOME/platform-tools
   
   # Windows (系统环境变量)
   ANDROID_HOME=C:\Users\你的用户名\AppData\Local\Android\Sdk
   ```

### 方法一：使用 Android Studio（推荐）

1. **下载项目代码**
   ```bash
   git clone <你的仓库地址>
   cd 项目目录
   ```

2. **打开 Android Studio**
   - 选择 `Open an existing project`
   - 打开项目中的 `android` 目录

3. **等待 Gradle 同步**
   - 首次打开会自动下载依赖，可能需要几分钟

4. **构建 APK**
   - 菜单：`Build` → `Build Bundle(s) / APK(s)` → `Build APK(s)`
   - 或者在终端运行：
     ```bash
     cd android
     ./gradlew assembleDebug
     ```

5. **获取 APK**
   - APK 文件位置：`android/app/build/outputs/apk/debug/app-debug.apk`

### 方法二：使用命令行

```bash
# 进入项目目录
cd 项目目录

# 安装依赖（如果还没安装）
pnpm install

# 构建静态文件
pnpm run build

# 同步到 Android 项目
npx cap sync android

# 构建 APK
cd android
./gradlew assembleDebug

# APK 输出位置
# android/app/build/outputs/apk/debug/app-debug.apk
```

## 📦 项目结构

```
项目目录/
├── android/                # Android 原生项目
│   ├── app/               # 主应用模块
│   │   ├── src/main/
│   │   │   ├── assets/public/  # Web 资源（你的应用）
│   │   │   └── java/           # 原生代码
│   │   └── build.gradle        # 应用配置
│   ├── gradlew            # Gradle 构建脚本
│   └── build.gradle       # 项目配置
├── out/                   # 静态网站文件
├── capacitor.config.ts    # Capacitor 配置
└── src/                   # Next.js 源码
```

## 🎨 自定义应用信息

编辑 `android/app/src/main/AndroidManifest.xml`：

```xml
<application
    android:label="待办事项"  <!-- 应用名称 -->
    android:icon="@mipmap/ic_launcher">  <!-- 应用图标 -->
```

### 更换应用图标

将图标文件放入：
- `android/app/src/main/res/mipmap-hdpi/ic_launcher.png` (72×72)
- `android/app/src/main/res/mipmap-mdpi/ic_launcher.png` (48×48)
- `android/app/src/main/res/mipmap-xhdpi/ic_launcher.png` (96×96)
- `android/app/src/main/res/mipmap-xxhdpi/ic_launcher.png` (144×144)
- `android/app/src/main/res/mipmap-xxxhdpi/ic_launcher.png` (192×192)

## 🔐 签名发布版本

### 生成签名密钥

```bash
keytool -genkey -v -keystore todo-app.keystore -alias todo -keyalg RSA -keysize 2048 -validity 10000
```

### 构建发布版 APK

```bash
cd android
./gradlew assembleRelease
```

### 配置签名（可选）

编辑 `android/app/build.gradle`：

```gradle
android {
    ...
    signingConfigs {
        release {
            storeFile file('../../todo-app.keystore')
            storePassword '你的密码'
            keyAlias 'todo'
            keyPassword '你的密码'
        }
    }
    buildTypes {
        release {
            signingConfig signingConfigs.release
        }
    }
}
```

## 🚀 分发应用

- **直接安装**: 将 APK 发送给用户安装
- **应用商店**: 上架 Google Play、华为应用市场等
- **内部分发**: 使用蒲公英、fir.im 等平台

## 📱 应用功能

- ✅ 添加待办事项
- ✅ 标记完成/未完成
- ✅ 删除待办事项
- ✅ 筛选任务（全部/进行中/已完成）
- ✅ 本地数据存储（使用 Capacitor Preferences）
- ✅ 统计面板显示任务数量

## ❓ 常见问题

### Q: Gradle 构建失败？
尝试清理项目：
```bash
cd android
./gradlew clean
./gradlew assembleDebug
```

### Q: 找不到 ANDROID_HOME？
确保已安装 Android SDK 并正确设置环境变量。

### Q: 应用图标显示异常？
确保图标文件格式为 PNG，且尺寸正确。

### Q: 如何更新应用？
修改 `src/` 目录下的代码后，运行：
```bash
pnpm run build
npx cap sync android
cd android && ./gradlew assembleDebug
```
