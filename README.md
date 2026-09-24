# teach-skill-bundle

一套基于 **WorkBuddy** 的 AI 教学 / 自学技能组合：`teach` + `visualize`。

核心目标：用"真正理解而非死记"的方式教（或学）任何东西——把知识建成依赖图，从已接受的基础推导出来，而非堆砌孤立事实；并支持**跨会话续学**（用 `progress.md` 记录进度与路线路）。

## 包含什么

```
teach-skill-bundle/
├── README.md          # 本说明
├── teach/             # 教学主技能
│   └── SKILL.md
└── visualize/         # 极简正确配图技能（被 teach 在阶段二/三调用）
    └── SKILL.md
```

- **teach**：教学主流程。定来源 → 探查 → 规划 → 教学（动机→确立→连接→答疑门→测验→块末检查点），含跨会话续学机制。
- **visualize**：给讲解配一张正确、极简的图（依赖图 / 几何图），在对话内联渲染。

## 依赖

**仅依赖 WorkBuddy 内置工具，无需安装任何第三方软件、运行时或包。**

| 用途                  | WorkBuddy 内置工具                    |
| ------------------- | --------------------------------- |
| 提问 / 摸底 / 答疑门 / 检查点 | AskUserQuestion                   |
| 联网检索与核实（researcher） | Agent（派子代理）                       |
| 测验正确项随机位置           | Bash（`date +%s` 取余）               |
| 关系图 / 几何图           | Visualizer（read_me + show_widget） |
| 进度持久化               | 文件读写（写 `progress.md`）             |

- 选"自动联网 / 混合"模式时需要**网络**；选"指定目录 / .md 资料清单"模式则完全离线可用。
- 无 tmux、无 Docker、无 `requirements.txt`。

## 安装

把 `teach/` 与 `visualize/` 两个文件夹复制到 WorkBuddy 的技能目录即可。

**方式一：手动复制**

- 用户全局（推荐，所有工作区可用）：
  - Windows：`C:\Users\<你的用户名>\.workbuddy\skills\`
  - macOS / Linux：`~/.workbuddy/skills/`
- 或仅某项目可用：`<项目根目录>/.workbuddy/skills/`

复制后结构应为：`.../.workbuddy/skills/teach/SKILL.md` 与 `.../.workbuddy/skills/visualize/SKILL.md`。

**方式二：git（便于分享 / 版本管理）**

```bash
git clone <本仓库地址>
cp -r teach-skill-bundle/teach   ~/.workbuddy/skills/teach
cp -r teach-skill-bundle/visualize ~/.workbuddy/skills/visualize
```

## 使用

任意工作区对话中调用：

- `@skill: teach` 或 Skill 工具选择 `teach`，然后给它一个你想讲 / 想学的主题。
- 启动后它会先问"内容从哪来"（自动联网 / 混合 / 指定目录 / .md 资料清单），然后走探查 → 规划 → 教学 → 块末检查点。
- 一块学完后，检查点可选"保存进度，下次继续"，生成/更新 `progress.md`；下次新会话说"继续"即可从断点接着讲。
- 多主题建议每个主题建一个独立目录（如 `bridge/`），各放一份 `progress.md`，进度互不污染。

## 自定义

直接编辑对应 `SKILL.md` 即可（frontmatter 中 `agent_created: true` 表示可自由修改）。常见可调整项：阶段零的默认来源、测验随机化的取时方式、续学 `progress.md` 的字段结构。

## 许可

按你分享时的需要自行声明。技能本身为纯文本 Markdown，无额外许可约束。

## 致谢

本项目是对 [amosblomqvist/learn](https://github.com/amosblomqvist/learn) 的**移植与再实现**。原仓库是一套基于 [earendil-works/pi](https://github.com/earendil-works/pi) 框架、并以 [pi-interactive-subagents](https://github.com/amosblomqvist/pi-interactive-subagents) 作为子代理后端的教学系统。

原项目的教学理念（"真正理解而非死记"的两条原则）、可视化与"测验/提问"的交互形态，是本项目 `teach` / `visualize` 技能的设计蓝本。在此特别感谢原作者 **amosblomqvist** 的构思与开源。

### 相对原项目的改进

- **平台移植，去除重依赖**：原系统强绑定 pi 框架（Node/TypeScript 运行时）与 tmux 终端多路复用；本版改写为 WorkBuddy 技能，仅依赖其内置工具，无需安装任何第三方运行时。
- **pi 专有机制映射为内置能力**：`researcher` 子代理 → Agent 工具；`quiz` / `ask_user_question` → AskUserQuestion；`mermaid` / `svg maker` → Visualizer（read_me + show_widget）；Obsidian `md-log` → 对话内联展示。
- **新增"阶段零：定资料来源"**：开课时主动询问内容从哪来（自动联网 / 混合 / 指定目录 / .md 资料清单），并硬性规定"用户资料优先于联网"。
- **新增跨会话续学（progress.md）**：用进度文件记录已掌握节点与路线路，开课读取并自动定位下一节点；块末检查点可"保存进度，下次继续"。
- **新增"理解确认与答疑门"**：每个知识点讲完、出测验前主动确认是否理解/有疑问，并支持随时插话优先解答。
- **测验正确项强制随机化**：以 Bash `date +%s` 取余驱动正确项位置，消除模型主观排布偏见（原实现无此保障）。
- **多主题隔离实践**：建议每主题一个独立目录存放各自的 `progress.md`，进度互不污染。

2026.09.26

