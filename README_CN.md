# Study Project Skills

一个 Claude Code Skill，用于深度分析开源大模型（LLM）项目的提示词、工具定义和架构，自动生成中文翻译文档、技术分析报告和产品说明书。

**[English](README.md)**

## 功能特性

- **提示词提取与翻译** — 通过 8 种搜索方法系统化扫描项目中散落的提示词（系统提示、用户模板、Few-shot 示例等），翻译为中文并保留英文原文
- **技术分析报告** — 自动生成包含架构图、数据流、模型参数汇总等内容的技术分析报告
- **产品说明书** — 面向非技术用户的通俗产品使用指南
- **大型项目支持** — 支持从小型（30 个提示词以内）到超大型（300+）项目的分级并行处理策略
- **断点续传** — 通过持久化文件实现进度恢复，长时间分析中断后可继续执行

## 安装

将本项目克隆到 Claude Code 的 Skill 目录中：

```bash
# 全局安装
git clone https://github.com/autumnseasonism/study-project-skills.git ~/.claude/skills/study-project-skills

# 或项目级安装
git clone https://github.com/autumnseasonism/study-project-skills.git .claude/skills/study-project-skills
```

## 使用方法

在任意包含 LLM 调用的开源项目目录下，通过 Claude Code 触发：

```
> 帮我分析一下这个项目的提示词
> 学习一下这个 AI 项目
> 看看这个项目怎么调 GPT 的
> 这个 agent 框架的 prompt 在哪
```

Skill 会自动执行五个阶段：

| 阶段 | 说明 |
|---|---|
| 1. 项目探索 | 识别项目结构、LLM 依赖和规模 |
| 2. 提示词识别 | 8 种方法系统搜索所有提示词和工具定义 |
| 3. 文档化 | 翻译每个提示词并生成索引 |
| 4. 分析报告 | 生成技术分析报告 |
| 5. 产品说明书 | 生成面向用户的产品说明书 |

## 输出结构

```
ai_analysis/
├── translated_prompts/
│   ├── MANIFEST.md              ← 提示词主清单
│   ├── INDEX.md                 ← 翻译文档索引
│   └── [各翻译文档]
├── AI_MODEL_USAGE_ANALYSIS.md   ← 技术分析报告
├── PRODUCT_GUIDE.md             ← 产品说明书
└── ERRORS.md                    ← 异常日志（如有）
```

## 项目结构

```
study-project-skills/
├── SKILL.md                 ← Skill 主定义
├── reference/
│   └── fault_handling.md    ← 故障处理规范
└── templates/
    ├── doc_template.md      ← 翻译文档模板
    ├── guide_template.md    ← 产品说明书模板
    ├── report_template.md   ← 分析报告模板
    └── search_patterns.md   ← 搜索模式参考
```

## 适用场景

- 快速了解一个陌生的 LLM/AI 开源项目的架构和提示词设计
- 为团队整理项目中所有 AI 调用的中文文档
- 学习优秀开源项目的 Prompt Engineering 实践
- 对项目进行 LLM 使用审计

## 许可证

[MIT](LICENSE)
