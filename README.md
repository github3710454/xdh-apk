# XDH-ERP Android APK 打包项目

将 http://xdh.brerp.cn/ 网站打包成 Android APK。

## 项目说明

- **应用名称**: XDH-ERP
- **包名**: `cn.brerp.xdh`
- **目标网址**: http://xdh.brerp.cn/
- **技术方案**: Capacitor + Android WebView

## 快速构建 APK（推荐：GitHub Actions 自动构建）

1. 在 GitHub 上创建一个新仓库
2. 将本项目代码 push 到仓库
3. GitHub Actions 会自动构建 APK
4. 在 Actions 页面下载 `app-debug.apk` 或 `app-release-unsigned.apk`

### 手动触发构建

进入仓库的 **Actions** → **Build APK** → **Run workflow** 即可手动触发。

## 本地构建（需要 Android Studio）

### 环境要求

- Android Studio（建议最新稳定版）
- JDK 17+
- Node.js 18+

### 构建步骤

1. 打开 Android Studio
2. 选择 **Open**，导入 `android/` 文件夹
3. 等待 Gradle 同步完成
4. 点击菜单 **Build → Build Bundle(s) / APK(s) → Build APK(s)**
5. 生成的 APK 位于：
   - Debug: `android/app/build/outputs/apk/debug/app-debug.apk`
   - Release: `android/app/build/outputs/apk/release/app-release-unsigned.apk`

### 命令行构建

```bash
# 进入 Android 项目目录
cd android

# 构建 Debug APK
./gradlew assembleDebug

# 构建 Release APK（未签名）
./gradlew assembleRelease
```

## 项目结构

```
xdh-apk/
├── android/                  # Android 原生项目（由 Capacitor 生成）
│   ├── app/
│   │   ├── src/main/
│   │   │   ├── AndroidManifest.xml   # 已配置 HTTP 明文支持
│   │   │   └── ...
│   │   └── build.gradle
│   └── gradlew
├── www/
│   └── index.html            # 启动页（加载后自动跳转到目标网址）
├── capacitor.config.json     # Capacitor 配置（已设置外部 URL）
├── package.json
└── README.md
```

## 关键配置说明

### HTTP 明文流量支持

由于目标网站使用 `http://` 而非 `https://`，已在 `AndroidManifest.xml` 中配置：

```xml
<application
    android:usesCleartextTraffic="true"
    ... >
```

### 外部网址加载

`capacitor.config.json` 中已配置：

```json
{
  "server": {
    "url": "http://xdh.brerp.cn/",
    "cleartext": true
  }
}
```

## 常见问题

**Q: 安装后打开是白屏？**
A: 请确保设备可以访问 `http://xdh.brerp.cn/`（内网需要连接公司 WiFi/VPN）。

**Q: 如何修改应用图标？**
A: 替换 `android/app/src/main/res/mipmap-*` 目录下的图标文件，然后重新构建。

**Q: Release APK 安装时提示未签名？**
A: Release 版本需要签名才能安装。请使用 Debug 版本测试，或对 Release 版本进行签名：
```bash
cd android/app/build/outputs/apk/release
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-key.keystore app-release-unsigned.apk alias_name
```

## 技术栈

- [Capacitor](https://capacitorjs.com/) - 跨平台原生应用容器
- Android WebView - 加载网页内容
