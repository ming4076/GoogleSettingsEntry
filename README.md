# Google 设置入口（libxposed API 102）

独立 LSPosed 模块，目标是恢复小米系统设置首页中的原生 Google 设置入口。

## 已针对用户提供的 APK 核对的目标方法

目标 APK：`设置-com.android.settings-17.apk`

- 类：`com.android.settings.MiuiSettings`
- `updateHeaderList(java.util.List): void`
- `AddGoogleSettingsHeaders(java.util.List): void`
- 资源 ID：`com.android.settings.R$id.header_google_settings`

模块在 `updateHeaderList(List)` 原方法执行完成后，检查 Header 列表是否已经包含 Google Header；若没有，则调用小米原生 `AddGoogleSettingsHeaders(List)`。

这意味着模块不自己创建 Google Activity，也不伪造跳转 Intent。Google Play 服务必须已经安装，并且系统中存在符合小米筛选条件的 Google 设置 Activity。

## API

- `io.github.libxposed:api:102.0.0`
- `minApiVersion=102`
- `targetApiVersion=102`
- 新式入口：`META-INF/xposed/java_init.list`
- 新式作用域：`META-INF/xposed/scope.list`
- 入口类继承 `io.github.libxposed.api.XposedModule`

## 构建（手机浏览器也可操作）

1. 将整个目录上传到自己的 GitHub 仓库。
2. 打开仓库的 **Actions**。
3. 选择 **Build Google Settings Entry**。
4. 点击 **Run workflow**。
5. 等待任务成功，进入该次运行页面底部的 **Artifacts**。
6. 下载 `GoogleSettingsEntry-API102`，解压得到 APK。

工作流构建的是带 Android debug 签名的可安装 APK。

## 安装与启用

1. 安装 APK。
2. 在 LSPosed 管理器中启用模块。
3. 作用域只选择 `com.android.settings`。
4. 重启设置应用（或重启手机）。
5. 检查系统设置首页。

## 日志

在 LSPosed 日志中搜索 `GoogleEntry102`：

- `Hook installed...`：Hook 已安装。
- `Native Google header method invoked.`：已调用小米原生入口构建方法。
- `Hook installation failed...`：当前 Settings 构建与目标不匹配，查看异常堆栈。

## 风险与限制

- 这是针对用户提供的 Android 17 Settings APK 编写的首版，尚未在真实设备运行验证。
- 若小米后续更新 Settings 并改变方法名/签名，需要重新定位。
- `header_google_settings` 重复检查依赖 Header 的 `id` 字段；若目标 ROM 的 Header 实现不同，需要根据运行日志调整。
- API 102 要求 LSPosed 框架本身支持 Modern API 102。
- 本模块不包含 Google Play 服务，也不能在缺少 Google 设置 Activity 时凭空创建入口。
