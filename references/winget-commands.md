# winget 命令与核对流程

`winget` 是 Windows 的官方软件包工具；它依赖 Microsoft 的“应用安装程序”（App Installer）。只对用户已经同意从 `winget` 获取的软件使用本流程，且一次只给一条命令，等用户回报结果再继续。

## 0. 确认 winget 可用

让用户打开“开始 → 终端”或“PowerShell”（通常无需管理员），运行：

```powershell
winget --version
```

正常时会显示类似 `v1.x.x` 的版本号。若终端提示找不到 `winget`，先在 Microsoft Store 搜索并更新 **App Installer（发布者：Microsoft Corporation）**，再重新打开终端测试。常见提示因终端不同而异：PowerShell 会显示“无法将‘winget’项识别为 cmdlet、函数、脚本文件或可运行程序的名称”，命令提示符（cmd）会显示“`winget` 不是内部或外部命令”。公司或学校电脑无法打开 Store、安装受策略限制时，停止并联系 IT；不要从第三方网站下载所谓 winget 安装器。

## 1. 搜索并确定包 ID

把“软件名称”替换为用户想安装的产品名：

```powershell
winget search --name "软件名称" --source winget
```

让用户回报结果中目标行的 `Id`、`Name`、`Version` 和 `Source`。名称相同不代表同一产品；不能只凭名称猜包 ID。

## 2. 安装前核对详情

将 `<Package.Id>` 换成上一步结果中的完整 `Id`，例如 `VideoLAN.VLC`：

```powershell
winget show --id <Package.Id> --exact --source winget
```

核对输出中的名称、发布者、版本、许可信息和安装程序信息是否与目标软件及厂商相符；若有下载地址，也应与该厂商的官方域名相符。任何一项不明确时，不执行安装命令。

## 3. 安装

核对无误后运行：

```powershell
winget install --id <Package.Id> --exact --source winget
```

命令可能要求同意源协议或软件许可。先让用户阅读画面上的协议与产品名，再由用户决定是否确认。不要默认加上 `--accept-package-agreements`，以免跳过用户应看到的许可确认。

安装完成后，让用户从开始菜单启动软件，并在“帮助 → 关于”或设置中确认版本。若安装器打开额外窗口，回到本 skill 的正常安装节奏：说明当前页面、推荐选择、预期结果和停止条件。

## Microsoft Store 应用

若用户明确要安装 Microsoft Store 应用，或 `--source winget` 找不到该应用，先用 Microsoft Store 的产品页确认产品和发布者；也可将下列命令中的 `软件名称` 换成目标名称：

```powershell
winget search --name "软件名称" --source msstore
```

之后按本文件的“核对详情”和“安装”顺序操作，但将每条命令中的 `--source winget` 替换成 `--source msstore`。Store 产品可能要求登录 Microsoft 账号或购买；这是用户的选择，不能代为确认。

## 哈希校验失败与来源边界

若 `winget install` 报安装程序哈希不匹配，停止安装。可先更新现有官方来源的元数据，再重新执行 `show` 核对详情后重试：

```powershell
winget source update
```

仍失败时，等待官方源同步、改用厂商官网安装包，或咨询厂商/IT。不要添加 `--force` 绕过哈希校验，也不要执行 `winget source add` 或添加不明第三方源。

## 可选：更新或卸载

仅在用户明确要求，且已再次确认准确包 ID 后使用：

```powershell
winget upgrade --id <Package.Id> --exact --source winget
```

```powershell
winget uninstall --id <Package.Id> --exact
```

升级前核对项目、插件和许可证兼容性。卸载前确认用户数据、配置和许可证文件的位置；不要把 `winget uninstall` 当作清理残留文件的手段。
