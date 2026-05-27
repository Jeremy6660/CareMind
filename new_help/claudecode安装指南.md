## 整体思路说明

这个方案的核心逻辑是：**VS Code** → **Cline（临时智能体插件）** → **Claude Code**。

Cline 本质上是 VS Code 里的一个"AI 助手"，它可以听懂人类的指令，直接在终端里执行命令、操作文件、下载安装软件。我们利用 Cline 的这个能力，让它代劳**所有环境检查和依赖安装**工作，包括检查 Node.js 版本、安装 Git、配置 PATH 等。电脑小白只需要操作到"装好 Cline 并完成 API 配置"这一步，之后的环境准备和 Claude Code 安装全部由 Cline 自动完成。

整个流程分为五个阶段：

| 阶段 | 操作内容 | 说明 |
|------|---------|------|
| 第一阶段 | 安装 VS Code | 官方下载，注意 PATH 选项 |
| 第二阶段 | 安装 Cline 插件 | 在 VS Code 扩展商店搜索安装 |
| 第三阶段 | 配置 Cline | 填入 API Key，选择模型 |
| 第四阶段 | 用 Cline 安装依赖和 Claude Code | 输入指令，Cline 自动检查/安装 Node.js、Git 并安装 Claude Code |
| 第五阶段 | 验证与收尾 | 确认安装成功，测试使用 |


## 第一阶段：安装 VS Code

### 下载和安装步骤

1. 打开浏览器，访问 **code.visualstudio.com**（这是唯一官方下载地址，不要从其他地方下载）
2. 页面会自动识别你的系统，点击大大的 **Download** 按钮
3. 下载完成后，双击安装文件（文件名类似 `VSCodeUserSetup-x64-xxx.exe`）

### ⚠️ 安装过程中的关键一步

安装向导走到"选择附加任务"这个页面时，有一个选项**非常关键**：

> ✅ **一定要勾选"添加到 PATH"这个选项！**

**为什么要勾选？** 勾选后可以在终端的任意位置输入 `code` 命令启动 VS Code，后续 Cline 也需要用到这个能力。不勾选的话，终端里输入 `code` 会提示找不到命令。

其他选项保持默认即可，一路点"下一步"直到安装完成。

### 可选但强烈推荐：安装中文语言包

VS Code 默认是英文界面。如果觉得英文不习惯：

1. 打开 VS Code
2. 点击左侧竖条上的 **扩展图标**（四个小方块组成的图标），或者按快捷键 `Ctrl + Shift + X`
3. 在搜索框里输入 **Chinese**
4. 找到微软官方发布的 **"Chinese (Simplified) (简体中文) Language Pack"**，点击 **Install（安装）**
5. 安装完成后，VS Code 右下角会弹出提示，点击 **"Change Language and Restart"** 即可切换为中文


## 第二阶段：安装 Cline 插件

### 安装步骤

1. 打开 VS Code
2. 点击左侧活动栏的 **扩展图标**（四个小方块），或按快捷键 `Ctrl + Shift + X`
3. 在搜索框中输入 **Cline**
4. 找到由 **"saoudrizwan"** 发布的那个 Cline 扩展，确认下载量超过百万的那个（这是唯一正版）
5. 点击右侧蓝色的 **Install（安装）** 按钮
6. 等待安装完成，左侧活动栏会出现一个 Cline 的图标

> ⚠️ **注意区分**：扩展商店里可能有类似名字的插件，一定要认准 **saoudrizwan** 发布的原版 Cline，下载量最大的那个。


## 第三阶段：配置 Cline（让 AI 能工作）

Cline 是一个 AI 智能体，它需要连接到一个大语言模型才能运行。也就是说，**Cline 本身只是一个"框架"，背后必须接入 AI 模型才能工作**。

### 最关键的准备工作：获取 DeepSeek API Key

Cline 需要 API Key 才能调用 AI 模型。这里推荐使用 **DeepSeek** 的 API，原因有三：

1. **国内直接访问**，不需要科学上网
2. **价格极低**，安装 Claude Code 这个临时任务消耗的 Token 通常只需几分钱
3. **注册简单**，支持支付宝/微信充值

**获取步骤：**
1. 访问 **platform.deepseek.com**，注册/登录你的账号
2. 在左侧菜单找到 **API Keys** 页面，点击"创建 API key"
3. 复制生成的密钥（格式为 `sk-xxx...`）

> ⚠️ **特别注意**：API Key **只会在创建时完整显示一次**，请务必立即复制并保存在安全的地方！

> ⚠️ **充值提醒**：DeepSeek 的 API 按量付费，网页版对话免费不代表 API 免费。建议先充值 **10 元**（支付宝/微信均可），安装 Claude Code 这个任务通常只需几分钱，剩余余额以后还能继续用。

> 这个环节是**整个流程最大的坑之一**。Cline 必须要有 API Key 才能工作，没有 API Key = Cline 完全不能用 = 没办法让它帮忙装 Claude Code。安装前务必确认 API Key 已准备好且账户有余额。


### 在 Cline 中配置 API

1. 打开 VS Code，点击左侧活动栏的 **Cline 图标**
2. 如果是首次打开，会看到几个选项，选择 **"Bring my own API key"**（使用自己的 API Key）
3. 在弹出的配置页面中：
   - **API Provider**（API 提供商）：选择 **OpenAI Compatible**
   - **Base URL**：填入 `https://api.deepseek.com/anthropic`
   - **API Key**：粘贴你获取到的 DeepSeek API Key
   - **Model**（模型）：手动输入 `deepseek-v4-pro`
4. 配置完成后点击 **Done** 或 **Continue**


## 第四阶段：用 Cline 安装依赖和 Claude Code

这是最"魔法"的一步——让 Cline 这个 AI 助手帮你完成**所有环境准备和安装工作**。你不需要手动检查 Node.js 版本、不需要手动安装 Git，只需要把下面这段指令发给 Cline，它会自动完成一切。

### 操作步骤

1. 打开 VS Code，点击左侧的 **Cline 图标**
2. 在 Cline 的对话框中，输入以下指令：

```
请帮我在当前 Windows 电脑上完成 Claude Code 的安装。你需要按顺序完成以下所有步骤，不要跳过任何一步：

## 第一步：检查并安装 Node.js（要求版本 ≥ 18）
- 在终端运行 node --version 检查是否已安装以及版本号
- 如果未安装或版本低于 18：
  - 用国内镜像下载 Node.js LTS 安装包：Invoke-WebRequest -Uri "https://npmmirror.com/mirrors/node/v22.12.0/node-v22.12.0-x64.msi" -OutFile "$env:TEMP\node-install.msi"
  - 运行安装：Start-Process msiexec.exe -Wait -ArgumentList "/i `"$env:TEMP\node-install.msi`" /quiet"
  - 安装完成后，重新打开一个终端，再次运行 node --version 确认版本 ≥ 18
- 如果版本已经 ≥ 18，直接进入下一步

## 第二步：检查并安装 Git
- 在终端运行 git --version 检查是否已安装
- 如果未安装：
  - 用国内镜像下载 Git for Windows：Invoke-WebRequest -Uri "https://registry.npmmirror.com/-/binary/git-for-windows/v2.47.1.windows.2/Git-2.47.1.2-64-bit.exe" -OutFile "$env:TEMP\git-install.exe"
  - 运行安装：Start-Process "$env:TEMP\git-install.exe" -Wait -ArgumentList "/VERYSILENT /NORESTART"
  - 安装完成后，重新打开一个终端，运行 git --version 确认安装成功
- 如果已经安装，直接进入下一步

## 第三步：解除 PowerShell 执行策略限制
- 运行：Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
- 这一步是防止后续安装脚本被系统拦截

## 第四步：安装 Claude Code
- 使用 PowerShell 原生安装方式，运行：
  irm https://claude.ai/install.ps1 | iex
- 等待安装完成

## 第五步：配置 PATH 并验证
- 确认 %USERPROFILE%\.local\bin 已在系统环境变量 PATH 中，如果不在则添加
- 重新打开一个终端，运行 claude --version 验证安装是否成功
- 如果 claude --version 正常输出版本号，说明整个安装流程完成

## 注意事项
- 每完成一步都要明确告诉我结果（成功/失败/版本号）
- 如果某一步失败，先尝试修复，不要直接跳过
- 安装过程中如需管理员权限，请告诉我
```

3. 按回车发送

4. 接下来 Cline 会自动依次执行：
   - 检查 Node.js 版本，不够则自动下载安装
   - 检查 Git 是否安装，没有则自动下载安装
   - 解除 PowerShell 脚本限制
   - 运行安装命令安装 Claude Code
   - 配置 PATH 环境变量
   - 验证安装结果

5. 过程中如果 Cline 弹出权限确认提示（比如"Allow"或"Run"），点击 **Allow（允许）** 即可

> 📌 **小提示**：Cline 执行整个过程可能需要 5-10 分钟，具体取决于你的网络速度和是否需要下载安装 Node.js/Git。终端窗口会自动弹出并执行命令，代码滚动是正常的，不需要人工干预，等 Cline 自己跑完就行。如果超过 **10 分钟**还没完成，可以在 Cline 对话框中输入"请继续"来推动它。

### ⚠️ 这一步可能遇到的问题

**问题 1：PATH 配置后找不到 claude 命令**

安装完成后，如果提示 `'claude' 不是内部或外部命令`，说明 PATH 没有生效。解决方案：

1. **关闭当前终端，重新打开一个新的终端**再试
2. 或者重启电脑（这是最彻底的方法）

如果仍然不行，在 Cline 中输入：
```
请把 %USERPROFILE%\.local\bin 添加到系统环境变量 PATH 中，然后重启终端验证
```

**问题 2：npm 全局安装（备选方案）**

如果 PowerShell 原生安装方式出现问题，可以让 Cline 改用 npm 安装（前提是 Node.js 已安装成功）：
```
请改用 npm 全局安装 Claude Code：
npm install -g @anthropic-ai/claude-code
安装完成后验证 claude --version
```


## 第五阶段：验证与收尾

安装完成后，打开一个新的终端窗口（cmd 或 PowerShell），输入：

```
claude --version
```

如果显示了版本号（如 `v1.x.x`），说明安装成功！

如果显示 `'claude' 不是内部或外部命令`，说明 PATH 还没生效，可以：
1. **关闭当前终端，重新打开一个新的终端**再试
2. 或者重启电脑（这是最彻底的方法）


## ⚠️ 整个流程中最大的几个"坑"总结

| 序号 | 坑 | 原因 | 解决方案 |
|------|-----|------|---------|
| 1 | **VS Code 没加 PATH** | 终端找不到 `code` 命令 | 安装时勾选"添加到 PATH" |
| 2 | **DeepSeek API Key 没有或无效** | Cline 需要 API Key 才能工作 | 提前注册 DeepSeek 并创建 API Key |
| 3 | **API Key 只显示一次** | 生成后不保存就永远丢失 | 生成后立即复制粘贴到安全的地方 |
| 4 | **DeepSeek 账户余额不足** | API 调用按量付费，余额为零则不可用 | 先充值 10 元（支付宝/微信均可） |
| 5 | **Node.js/Git 下载失败** | 国内镜像偶尔不稳定 | 让 Cline 换用官方源或其他镜像重试下载 |
| 6 | **PowerShell 执行策略** | Windows 默认禁止运行远程脚本 | 提示词中已包含解除命令，或手动运行 `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` |
| 7 | **安装后找不到 claude 命令** | PATH 没配或没生效 | 添加 `%USERPROFILE%\.local\bin` 到 PATH，然后重启终端 |
| 8 | **Cline 自动执行时被安全软件拦截** | 杀毒软件误报 | 暂时关闭杀毒软件，或在提示时选择"允许" |
| 9 | **Cline 中途卡住不动** | 网络波动或等待用户确认 | 在对话框输入"请继续"推动它 |

> 📌 **补充提醒**：Cline 在执行安装任务时会自动操作终端和文件系统，整个过程对小白来说可能看起来有些"吓人"（终端窗口突然弹出、代码疯狂滚动）。这是正常的，不需要人工干预，等 Cline 自己跑完就行。整个流程（包括下载安装 Node.js/Git）可能需要 5-10 分钟，如果超过 **10 分钟**还没完成，可以在 Cline 对话框中输入"请继续"来推动它。


## 附录：整个流程的简化 Checklist

打印出来给小白用，做完一项打一个勾：

```
□ 1. 下载 VS Code：访问 code.visualstudio.com，下载并安装
    □ 安装时勾选"添加到 PATH"
    □ 可选：装完装 Chinese 中文语言包
□ 2. 准备 DeepSeek API Key（访问 platform.deepseek.com）
    □ 注册并生成 API Key，立即复制保存
    □ 充值 10 元（支付宝/微信）
□ 3. 在 VS Code 中安装 Cline 插件
    □ 扩展商店搜索 "Cline"，认准 saoudrizwan
□ 4. 配置 Cline：API Provider 选 OpenAI Compatible，Base URL 填 https://api.deepseek.com/anthropic，填入 API Key，Model 填 deepseek-v4-pro
□ 5. 在 Cline 对话框中输入安装指令（复制第四阶段的完整提示词）
    □ Cline 会自动检查/安装 Node.js 和 Git
    □ Cline 会自动安装 Claude Code 并配置 PATH
    □ 如有权限弹窗，点"Allow"
□ 6. 打开新终端，输入 claude --version 验证
□ 7. 看到版本号 → 大功告成！
```

这样整个流程就走完了。如果中间任何一步卡住了，把终端里显示的错误信息复制下来，发给懂的人帮你排查。


---

## 进阶：使用 CCSwitch 自由切换模型（可选）

### 🎯 这个方案用来实现什么？

通过 CCSwitch，可以方便地在 Claude Code 中切换使用不同的模型，相当于一个模型配置的管家。当你使用 DeepSeek 模型时，Claude Code 的核心执行能力（如代码分析、终端指令执行等）是保留的，但底层的思考模型从 Claude 换成了 DeepSeek，实现性价比更好的选择。

> ⚠️ **重要提示**：此方案仅适用于通过前面的指南安装的**原生 Claude Code 命令行版本**，不适用于 VS Code 中的 Cline 或 Claude Code 插件版，因为它们的模型配置机制不同。


### 第一步：获取 DeepSeek API Key

在使用 CCSwitch 之前，需要先拥有一个 DeepSeek 的 API Key。

1. **注册并访问**：前往 [DeepSeek 开放平台](https://platform.deepseek.com)，注册并登录你的账号。
2. **创建与复制 Key**：在平台左侧菜单找到"API Keys"页面，点击"创建 API key"按钮。
   > 🔑 **特别注意**：生成的 API Key **只会在创建时完整显示一次**，请务必立即复制并保存在安全的地方。
3. **充值余额**：DeepSeek 的 API 按量付费，网页版对话免费不代表 API 免费。建议先进行小额充值（例如 10 元）。


### 第二步：安装与配置 CCSwitch

#### 🛠️ 安装 CCSwitch

CCSwitch 是一个独立的桌面程序，通常在可视化界面中操作，比命令行直观很多。你可以下载它：

- 打开浏览器，访问 CCSwitch 的 [GitHub 发布页面](https://github.com/farion1231/cc-switch)，在页面底部的"Assets"区域，找到并下载适用于 Windows 的 `.msi` 或 `.exe` 安装包。
- 下载完成后，双击运行安装文件，全程选择默认选项，一路点击"下一步"即可完成安装。

#### ⚙️ 在 CCSwitch 中配置 DeepSeek

1. **添加新 Provider**：启动 CCSwitch 程序。在程序界面右上角找到并点击 "**+**"（加号）按钮，意为"新建模型配置"。
2. **选择供应商**：在弹出的预设供应商列表中，选择 "**DeepSeek**"。
3. **填入 API Key**：在新弹出的窗口中，将你在第一步复制好的 **DeepSeek API Key** 粘贴到对应输入框。
4. **配置关键参数**：
   - **请求地址**：确认 API 请求地址已设置为 `https://api.deepseek.com/anthropic`。
     > 🌐 **参数解析**：这个地址是 DeepSeek 专门为兼容 Anthropic API 格式而设的，确保 Claude Code 的请求能被 DeepSeek 正确理解。
   - **模型选择与映射**：通常下拉框或列表里已有推荐模型，建议将所有模型映射都统一选择为 `deepseek-v4-pro`。
     > 🧠 **模型选择建议**：
     > - **追求质量**：选择 `deepseek-v4-pro`，适合重构、疑难排障等复杂任务。
     > - **追求效率**：选择 `deepseek-v4-flash`，适合日常编码、补全等高频任务，成本更低。
5. **勾选重要选项**：在窗口右下角找到并勾选 "**写入通用配置**" 选项。这是确保配置对所有终端窗口生效的关键一步。
6. **保存配置**：最后，点击右下角的 "**添加**" 按钮，完成所有配置。


### 第三步：验证切换是否成功

配置完成后，我们需要验证模型是否已切换成功。

1. **打开新终端**：**关闭所有已打开的 PowerShell 或命令提示符窗口**，然后重新打开一个。
2. **启动并提问**：在新的终端窗口中，输入 `claude` 命令启动 Claude Code。
3. **验证模型**：
   - **直接查看**：如果一切顺利，Claude Code 的启动界面或状态栏会显示 "`deepseek-v4-pro · API Usage Billing`" 字样，这代表配置成功。
   - **提问验证**：你也可以直接向它提问 "**你现在是什么模型？**" 来最终确认。

#### ✅ 验证通过后

现在，你已经成功将 Claude Code 背后的"大脑"切换为 DeepSeek 模型。所有交互都将由 DeepSeek 的算力支持，费用将从你的 DeepSeek 账户中扣除，并遵循 DeepSeek 的计费规则。


### ⚠️ 关键步骤与常见问题 (Q&A)

**Q1：安装时提示"API 格式"选项，我该如何选择？**
**A**：在 CCSwitch 的高级选项中，如果看到"API 格式"的设置，请务必选择 `Anthropic Message (原生)`。Claude Code 需要这种格式的 API 才能正常工作。

**Q2：CCSwitch 安装后无法启动，提示缺少 Python？**
**A**：通常通过 MSI 或 EXE 安装包安装的 CCSwitch 不需要额外安装 Python。如果遇到启动失败，请确认你下载的是 Windows 安装包，而非 Python 的 `ccswitcher` 命令行工具。

**Q3：安装或配置后，Claude Code 启动时显示"provider"相关错误？**
**A**：这通常是某个环境变量或文件未生效，最稳妥的解决方法是**重启电脑**。重启后，所有系统环境变量和配置文件都会重新加载，确保 CCSwitch 的配置能百分百生效。

**Q4：如何切回 Claude 官方的 API？**
**A**：打开 CCSwitch 的界面，在模型列表中，将当前启用的"DeepSeek"切换回"Claude Official"或你原先的官方配置即可。

**Q5：Windows 的安全软件可能会拦截 CCSwitch 的安装或运行，怎么办？**
**A**：CCSwitch 是开源工具，如果系统弹出安全警告，选择"**仍要运行**"即可。如果安装过程被中断，可以尝试暂时禁用安全软件，安装完成后再重新启用。
