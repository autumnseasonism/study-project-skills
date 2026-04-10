# Study Project Skills

一个 Claude Code Skill，用于深度分析开源大模型（LLM）项目的提示词、工具定义和架构，自动生成中文翻译文档、技术分析报告和产品说明书。

A Claude Code Skill for deep analysis of open-source LLM projects — extracting prompts, tool definitions, and architecture, then generating translated documentation, technical reports, and product guides.

## 功能特性 / Features

- **提示词提取与翻译** — 通过 8 种搜索方法系统化扫描项目中散落的提示词（系统提示、用户模板、Few-shot 示例等），翻译为中文并保留英文原文
- **技术分析报告** — 自动生成包含架构图、数据流、模型参数汇总等内容的技术分析报告
- **产品说明书** — 面向非技术用户的通俗产品使用指南
- **大型项目支持** — 支持从小型（30 个提示词以内）到超大型（300+）项目的分级并行处理策略
- **断点续传** — 通过持久化文件实现进度恢复，长时间分析中断后可继续执行

---

- **Prompt Extraction & Translation** — Systematically scans prompts scattered across projects (system prompts, user templates, few-shot examples, etc.) using 8 search methods, translates to Chinese while preserving English originals
- **Technical Analysis Report** — Auto-generates reports covering architecture diagrams, data flow, model parameter summaries, and more
- **Product Guide** — Plain-language product guide for non-technical users
- **Large Project Support** — Tiered parallel processing strategies from small (≤30 prompts) to extra-large (300+) projects
- **Checkpoint Resume** — Progress recovery via persisted files, allowing continuation after interruption

## 安装 / Installation

将本项目克隆到 Claude Code 的 Skill 目录中：

Clone this repo into your Claude Code skill directory:

```bash
# 全局安装 / Global installation
git clone https://github.com/<your-username>/study-project-skills.git ~/.claude/skills/study-project-skills

# 或项目级安装 / Or project-level installation
git clone https://github.com/<your-username>/study-project-skills.git .claude/skills/study-project-skills
```

## 使用方法 / Usage

在任意包含 LLM 调用的开源项目目录下，通过 Claude Code 触发：

In any directory containing an open-source LLM project, trigger via Claude Code:

```
> 帮我分析一下这个项目的提示词
> 学习一下这个 AI 项目
> 看看这个项目怎么调 GPT 的
> 这个 agent 框架的 prompt 在哪
```

Skill 会自动执行五个阶段：

The skill automatically runs through five phases:

| 阶段 / Phase | 说明 / Description |
|---|---|
| 1. 项目探索 / Project Exploration | 识别项目结构、LLM 依赖和规模 |
| 2. 提示词识别 / Prompt Identification | 8 种方法系统搜索所有提示词和工具定义 |
| 3. 文档化 / Documentation | 翻译每个提示词并生成索引 |
| 4. 分析报告 / Analysis Report | 生成技术分析报告 |
| 5. 产品说明书 / Product Guide | 生成面向用户的产品说明书 |

## 输出结构 / Output Structure

```
ai_analysis/
├── translated_prompts/
│   ├── MANIFEST.md              ← 提示词主清单 / Prompt manifest
│   ├── INDEX.md                 ← 翻译文档索引 / Translation index
│   └── [translated docs]       ← 各提示词翻译文档 / Individual translations
├── AI_MODEL_USAGE_ANALYSIS.md   ← 技术分析报告 / Technical report
├── PRODUCT_GUIDE.md             ← 产品说明书 / Product guide
└── ERRORS.md                    ← 异常日志（如有）/ Error log (if any)
```

## 项目结构 / Project Structure

```
study-project-skills/
├── SKILL.md                 ← Skill 主定义 / Main skill definition
├── reference/
│   └── fault_handling.md    ← 故障处理规范 / Fault handling spec
└── templates/
    ├── doc_template.md      ← 翻译文档模板 / Translation template
    ├── guide_template.md    ← 产品说明书模板 / Product guide template
    ├── report_template.md   ← 分析报告模板 / Report template
    └── search_patterns.md   ← 搜索模式参考 / Search patterns reference
```

## 适用场景 / Use Cases

- 快速了解一个陌生的 LLM/AI 开源项目的架构和提示词设计
- 为团队整理项目中所有 AI 调用的中文文档
- 学习优秀开源项目的 Prompt Engineering 实践
- 对项目进行 LLM 使用审计

---

- Quickly understand the architecture and prompt design of an unfamiliar LLM/AI project
- Generate Chinese documentation of all AI calls in a project for your team
- Learn prompt engineering practices from top open-source projects
- Audit LLM usage across a project

## 许可证 / License

[MIT](LICENSE)
