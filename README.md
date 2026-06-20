# 🐉 SagaSmith

**Autonomous D&D 5e AI Dungeon Master** — 战役管理 · 模组生成 · 自主带团

> *"规则书为经文，模组为地图，骰子为审判官。"*  
> — 明萨拉·班瑞，SagaSmith 默认 DM

SagaSmith 是一个跨平台 AI 跑团主持人 skill 包。它把完整的 D&D 5e DM 能力——战役生命周期管理、SRD 规则裁判、模组自动生成、角色创建与升级——打包为可安装的 AI agent skill，支持 NanoBot / OpenClaw / Hermes / Claude Code 等任意兼容 SKILL.md 标准的平台。

---

## 为什么是 SagaSmith

大多数 D&D AI 工具只做一件事：掷骰、查规则、或者写一段描述。SagaSmith 是**完整的 DM**：

- 🏛️ **战役管理** — SQLite/PostgreSQL 数据库驱动，完整的 campaign CRUD、Snapshot 存档/读档/校验/撤销、事件日志、模组进度追踪
- 🎲 **规则裁判** — BGE-M3 Dense Vector 检索 SRD 5.2.1（20 个规则文件，CC-BY-4.0），精确+全文+语义混合搜索
- ✍️ **模组生成** — 5 种类型（one-shot / short / medium / long / sandbox）× 25 种叙事范式，多步渐进生成，自动入库
- ⚔️ **战斗引擎** — d20 真实掷骰、先攻/回合/命中/伤害/豁免计算、XP 与升级
- 🎭 **明萨拉人格** — 守序邪恶 DM，严格但不失冷幽默，从不放水、从不泄露隐藏信息

---

## 快速安装

### Claude Code / Codex / Cursor / Copilot（推荐）

```bash
npx skills add dajiaohuang/SagaSmith-skill
```

### ClawHub

```bash
npx clawhub install sagasmith
```

### 手动安装（NanoBot）

```bash
git clone https://github.com/dajiaohuang/SagaSmith-skill.git
cp -r SagaSmith-skill/skills/*    ~/.nanobot/skills/
cp -r SagaSmith-skill/templates/* ~/.nanobot/templates/
cp -r SagaSmith-skill/tools/*.py  ~/.nanobot/agent/tools/
cp -r SagaSmith-skill/domain/*    ~/.nanobot/dnd/
cp -r SagaSmith-skill/data/srd    ~/.nanobot/dnd/data/srd/
python -m <domain-cli> rules ingest-srd
```

---

## 技能拆解

| 技能 | SKILL.md | 职责 |
|------|----------|------|
| 🎲 **dnd-dm** | [SKILL.md](skills/dnd-dm/SKILL.md) | 核心 DM 人格（always-on），规则裁判，战斗引擎，SRD 检索 |
| 📋 **dnd-campaign-manager** | [SKILL.md](skills/dnd-campaign-manager/SKILL.md) | 战役生命周期，Snapshot 存档/读档，模组导入，USER.md 同步 |
| ✍️ **dnd-module-gen** | [SKILL.md](skills/dnd-module-gen/SKILL.md) | 模组生成：one-shot → short → medium → long → sandbox，25 种范式 |

### 模组生成范式一览

| 类型 | 推荐范式 | 产出规模 |
|------|----------|----------|
| One-shot | Five-Room Dungeon, Heist, Mystery | 1 章，3-6h |
| Short | Three-Act, Kishōtenketsu, Race Against Time | 3 章，3-8 次 |
| Medium | Hero's Journey, Plot Point, Faction Turn | 5 章，2-4 月 |
| Long | Double Triangle, Conspyramid, Megadungeon | 8 章，6+ 月 |
| Sandbox | Hexcrawl, Node-Based, Blorb | 4-6 区域，开放 |

---

## DM 人格：明萨拉·班瑞

以《博德之门 3》经典角色明萨拉·班瑞为原型的守序邪恶 DM：

- **规则绝对主义** — 严格按 2024 版规则书裁决，骰子结果不可商量
- **冷刺幽默** — 指出战术失误后，补一句带刺的可行建议
- **信息边界** — 绝不泄露 DC、怪物隐藏数值、未发现房间、后续剧情
- **玩家自主** — 不替玩家做任何决定，不因戏剧效果改骰

默认适配《博德之门：坠入阿弗纳斯》模组，可通过模组导入适配任意冒险。

---

## 目录结构

```
SagaSmith-skill/
├── skills/                     # 3 个 Skill（纯 Markdown，跨平台）
│   ├── dnd-dm/                 #   核心 DM + 20 个 SRD 文件
│   ├── dnd-campaign-manager/   #   战役管理 + 数据库约定
│   └── dnd-module-gen/         #   模组生成
├── templates/                  # DM 人格模板
│   ├── SOUL.md                 #   明萨拉·班瑞人格
│   ├── IDENTITY.md             #   身份约束与规则源
│   ├── AGENTS.md               #   会话启动协议
│   ├── agent/identity.md       #   运行时 identity 注入
│   └── memory/MEMORY.md        #   长期记忆模板
├── tools/                      # Agent 工具（Python）
│   ├── dnd_campaign.py         #   战役 CRUD + 一键开团
│   ├── dnd_save.py             #   存档管理
│   ├── dnd_module.py           #   模组导入/检索/场景进度
│   └── dnd_rules.py            #   规则混合检索（精确 + FTS + Dense）
├── domain/                     # 业务逻辑（纯 Python，零框架依赖）
│   ├── db/                     #   SQLAlchemy ORM + Service + Alembic 迁移
│   ├── modules/                #   模组分块、PDF 解析、场景索引
│   ├── rules/                  #   BGE-M3 嵌入、Markdown 解析、规则摄入
│   └── engine/                 #   骰子、检定、战斗结算、XP、模板工厂
├── data/srd/                   # SRD 5.2.1 英文源文件（20 × CC-BY-4.0）
└── data/srd-zh/                # SRD 中文翻译（可选子模块）
```

---

## 外部依赖

| 依赖 | 用途 |
|------|------|
| Python 3.11+ | domain 运行时 |
| SQLAlchemy | 数据库 ORM |
| FlagEmbedding | BGE-M3 Dense Vector 检索 |
| markitdown | PDF / DOCX 模组导入 |

---

## 生态

[![ClawHub](https://img.shields.io/badge/ClawHub-sagasmith-blue)](https://clawhub.ai)
[![skills.sh](https://img.shields.io/badge/skills.sh-dajiaohuang%2FSagaSmith--skill-green)](https://skills.sh)
[![License](https://img.shields.io/badge/license-MIT-orange)](LICENSE)
[![SRD](https://img.shields.io/badge/SRD_5.2.1-CC--BY--4.0-lightgrey)](https://creativecommons.org/licenses/by/4.0/)

已发布至 **ClawHub**、**skills.sh**（72 agent 兼容）。  
LobeHub 可通过 [agentskill.sh/submit](https://agentskill.sh/submit) 提交。

---

## 致谢

- [ackiles/dnd-dm-skill](https://github.com/ackiles/dnd-dm-skill) — D&D DM skill 先驱，SagaSmith 的灵感与设计参考
- [NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent) — SKILL.md 生态标准推动者
- D&D 5e SRD 5.2.1 © Wizards of the Coast，以 [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/) 授权使用

---

## 许可证

- 代码：MIT
- SRD 5.2.1 数据文件：[CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/)
