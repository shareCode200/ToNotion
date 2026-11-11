# ToNotion

**把当前 Session 沉淀为易读、完整、可继续学习的 Notion 项目文档。**

ToNotion 是一个可复用的 Codex Skill。调用 `$tonotion` 后，Codex 会结合当前会话中的项目上下文，使用已连接的 Notion 工具，把可读取的问答整理到项目主页下。

它保留完整回答中的解释、示例、表格、图片和代码，同时统一标题、问题目录与文字排版。项目名称和页面地址都是运行时输入，可以用于不同项目。

> 界面显示名称：**ToNotion**；技能标识和调用名称：**tonotion**。本项目是指令型 Skill，通过 Codex 和 Notion 连接执行工作，不是独立运行的同步程序。

## 能做什么

| 能力 | 行为 |
| --- | --- |
| 项目归档 | 查找项目主页；不存在时创建，再将归档作为实际子页面放入 |
| 完整问答 | 按对话顺序组织，完整回答默认展开；完整原问题收进折叠块 |
| 简洁标题 | 提炼主题，省略父页面中已经明确的项目名称 |
| 图表与代码 | 保留原生表格、图片与图注、代码块、链接和版本证据 |
| 阅读排版 | 添加简短导读和问题目录，优化列宽、单元格换行、段落与重点提示 |
| 增量更新 | 优先更新明确属于同一 Session 的归档，避免重复创建或追加历史 |
| 范围说明 | 遇到无法读取的历史轮次或缺失媒体时，明确说明缺口 |

## 使用前准备

- 使用支持本地 Skills 的 Codex 环境。
- 在该环境中连接 Notion，并具有目标页面的读取和写入权限。
- 准备项目名称或项目主页链接；当前会话已经明确项目时可以直接调用。
- 通过 Git 安装需要 Git；也可以下载 ZIP 手动安装。

本仓库不需要 `npm install`、Python 依赖或单独配置 API Key。Notion 的连接与授权由宿主环境管理；仅安装技能文件不会自动连接 Notion。

## 安装

### 方式一：Git 安装到用户目录

将仓库克隆到用户级技能目录中的 `tonotion` 文件夹，可以在多个项目中调用。

**Windows PowerShell：**

```powershell
$skillRoot = Join-Path $HOME '.agents/skills'
New-Item -ItemType Directory -Path $skillRoot -Force | Out-Null
git clone https://github.com/shareCode200/ToNotion.git (Join-Path $skillRoot 'tonotion')
```

**macOS / Linux：**

```bash
mkdir -p "$HOME/.agents/skills"
git clone https://github.com/shareCode200/ToNotion.git "$HOME/.agents/skills/tonotion"
```

如果目录已经存在，先检查并备份自己的修改，不要直接覆盖。私有仓库需要当前 Git 客户端已经获得仓库访问权限。

### 方式二：下载 ZIP

1. 在 GitHub 仓库页面选择 **Code → Download ZIP**，解压。
2. 将包含 `SKILL.md` 和 `agents` 的整个文件夹重命名为 `tonotion`。
3. 放入用户目录的 `.agents/skills/`，使最终路径为 `.agents/skills/tonotion/SKILL.md`。
4. 在 Codex 的技能列表中查找 **ToNotion**，或输入 `$tonotion`。若没有出现，重启 Codex 后再检查。

也可安装到某个项目的 `.agents/skills/tonotion/`，供该项目使用。用户级与项目级安装选择一种即可，避免出现同名副本。

目录发现与调用方式参见 [OpenAI 官方 Skills 文档](https://learn.chatgpt.com/docs/build-skills)。

## 使用

### 直接同步当前会话

当前会话已明确目标项目时：

```text
$tonotion
```

技能会开始归档当前 Session。只有项目不明确或存在无法区分的同名主页时，才询问目标位置。

### 指定项目名称

```text
$tonotion 项目：我的 Agent 项目
```

找到对应项目主页后复用；没有对应主页时先创建。没有指定新项目的容器位置时，技能会创建私有项目主页并回报实际位置。

### 指定项目主页

```text
$tonotion
项目名称：我的 Agent 项目
项目主页：在这里粘贴 Notion 项目主页链接
```

### 更新已有归档

```text
$tonotion
项目主页：在这里粘贴项目主页链接
已有归档页：在这里粘贴需要更新的归档链接
把本次 Session 的新增问答同步到这个页面，保留原有内容。
```

### 先预览，或限定范围

```text
$tonotion 先给我归档预览，不写入 Notion。
```

```text
$tonotion 项目：我的 Agent 项目，只归档本次会话中关于工具调用的问答。
```

这些补充要求会覆盖默认范围。仅讨论或修改技能时，不会因出现 ToNotion 这个名称就执行同步。

## 归档页面是什么样

```text
项目主页
└── 简短的主题标题
    ├── 一两句导读
    ├── 项目与来源信息
    ├── 问题目录
    ├── 01｜第一个问题的主题
    │   ├── 查看原问题（折叠）
    │   └── 完整回答、表格、图片和代码（展开）
    ├── 02｜第二个问题的主题
    │   └── ……
    └── 必要的版本、证据与归档说明
```

标题通常优先使用 6—12 个汉字左右，但会以主题准确为先。表格突出表头和首列，按内容分配列宽；正文按语义分段，适量使用加粗、提示框与留白。

完成后返回项目主页和归档页链接，并说明本次覆盖范围及实际未完成项。

## 能力边界

- **可读取范围**：技能只能归档宿主环境提供的当前会话内容。上下文被压缩时，会尝试通过可用的会话读取工具补读；无法补读则注明缺失，不把摘要冒充完整原文。
- **图片可用性**：图片需要有可访问的来源或可用的上传工具。图片引用保留不等于已验证所有客户端都能正常显示。
- **重复同步**：优先使用明确的归档链接或可取得的 Session 稳定标识。相似页面标题不足以证明是同一会话。
- **连接依赖**：没有可用 Notion 连接或写入权限时，只能提供草稿，并明确未落盘。
- **触发方式**：这是一次性按需执行的技能，不是后台定时同步服务，也不会自动导出其他会话。

首次用于新项目时，建议提供准确的项目主页链接，核对一次归档位置和内容。

## 常见问题

**为什么安装后找不到 ToNotion？**

检查是否多套了一层目录：正确路径是 `tonotion/SKILL.md`，不是 `tonotion/ToNotion-main/SKILL.md`。确认当前环境支持该技能目录；必要时重启 Codex。

**为什么输入 `$tonotion` 后还询问项目？**

当前会话没有明确项目，或同名页面无法区分。提供项目名称或主页链接即可，技能不会将临时目录名或任意可见页面当作项目。

**为什么没有保存到 Notion？**

先检查 Notion 连接和目标页权限。技能文件本身不包含凭据，也不会绕过服务的授权要求。

**想继续改版式，改哪里？**

修改 [SKILL.md](SKILL.md) 中的“标题与页面结构”和“表格与正文排版”。若修改调用名称，同时调整 `agents/openai.yaml` 中的默认调用提示。

## 文件结构

```text
ToNotion/
├── SKILL.md             # 归档流程、内容保留与版式规则
├── agents/
│   └── openai.yaml      # ToNotion 显示名称及默认调用提示
├── README.md            # 安装、使用与维护说明
└── .gitignore
```

## 更新与反馈

通过 Git 安装且没有未提交修改时，可以更新本地版本：

```powershell
git -C (Join-Path $HOME '.agents/skills/tonotion') pull --ff-only
```

```bash
git -C "$HOME/.agents/skills/tonotion" pull --ff-only
```

反馈问题时，提供调用方式、预期结果和实际结果即可；不要把凭据或不适合分享的会话内容提交到 Issue。
