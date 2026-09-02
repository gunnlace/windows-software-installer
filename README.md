# windows-software-installer

一个 Agent Skill：帮助 **Windows 10/11 新手**安全地寻找、下载、安装、验证正版软件，并排查安装故障。

面向办公软件与专业/工程软件（CAD、仿真类），不处理 Linux/macOS 安装，也不处理编程依赖管理。

## 这个 skill 会做什么

- **先判断再动手**：确认软件用途与版本、Windows 版本与系统类型、管理员权限、许可证要求、目标磁盘可用空间，而不是假设"有 D 盘""有管理员权限"。
- **控制节奏**：按「准备 → 下载 → 打开安装程序 → 选择选项 → 验证」推进，每轮只给 1–3 个动作，并说清点哪里、推荐怎么选、正常应看到什么、什么画面下要立刻停。
- **把关来源**：来源优先级为厂商官网/客户门户 → 官方 `winget` → 学校或公司官方分发地址；覆盖数字签名核对、SHA-256 校验、SmartScreen 拦截处理。
- **解释安装选项**：区分程序目录、项目数据与厂商配置；对改默认浏览器、捆绑软件、覆盖旧版本等选项要求先确认。
- **安装后验证与记录**：核对版本与许可证状态，记录程序路径、数据路径、来源、安装日期。

它同时定义了**必须暂停**的边界：来源或签名不可信、需要关闭 Defender/UAC、需要改组策略或注册表、安装器要求格式化分区或覆盖项目数据等情形，一律停下说明风险，不给绕过方案。它也不会代替用户输入密码、许可证密钥或支付信息。

## 目录结构

```
SKILL.md                                # 主入口：判断、节奏、来源路由、暂停边界
references/
  source-and-signature.md               # 来源判断、数字签名、SHA-256、SmartScreen
  installer-options.md                  # EXE/MSI 安装器逐页选项
  winget-commands.md                    # winget 命令与核对流程
  windows-install-paths.md              # 安装路径、空间与权限
  software-type-checklists.md           # 办公 / 工程仿真 / 便携版 / ISO 检查表
  troubleshooting.md                    # 错误码、失败处理、升级与回退
agents/
  openai.yaml                           # OpenAI 平台的界面与调用配置
```

`SKILL.md` 保持精简，具体流程按需读取 `references/` 下的文件，避免一次性占用过多上下文。

## 安装

### Claude Code / Claude 桌面端

复制到 skills 目录，目录名需与 `SKILL.md` 中的 `name` 一致，否则不会被识别：

```bash
# 对所有项目生效
git clone https://github.com/gunnlace/windows-software-installer.git \
  ~/.claude/skills/windows-software-installer

# 或只对单个项目生效
git clone https://github.com/gunnlace/windows-software-installer.git \
  <your-project>/.claude/skills/windows-software-installer
```

重启会话后即可使用。可以直接描述需求触发，例如"帮我在 Windows 上装 AutoCAD"，也可以显式说"用 windows-software-installer"。

### OpenAI 平台

`agents/openai.yaml` 提供了显示名称、简介与默认提示词，并开启隐式调用。

## 使用示例

```
我要打开同事发来的 .dwg 文件，但不知道该装什么，电脑是新买的 Windows 11。
```

```
下载的安装包被 SmartScreen 拦了，说"已阻止不受信任的应用"，还能装吗？
```

```
装 SolidWorks 报错 1603，之前装过 2021 版没卸载。
```

## 语言

skill 的指令内容为简体中文，`description` 字段为英文（供模型匹配调用）。

## License

见 [LICENSE](LICENSE)。

## 声明

本 skill 只引导用户从官方渠道获取正版软件，不提供任何破解、绕过授权或规避许可证校验的方法。遇到来源不可信的安装包、要求关闭系统安全防护、或许可证状态不明的情况，它会停下并说明风险，而不是给出绕过方案。

许可证合规与安装后果由使用者自行负责；商业软件的授权条款请以厂商或所属组织 IT 的说明为准。
