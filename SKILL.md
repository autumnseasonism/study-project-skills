---
name: study_project
description: 对开源大模型项目进行系统化分析：提取并翻译所有提示词（prompt），梳理工具定义和 LLM 调用架构，生成技术分析报告和面向非技术人员的产品说明书。当用户想要学习/分析/研究/调研一个 LLM/AI 项目、提取 prompt、查看项目用了什么提示词、理解大模型调用逻辑、做竞品技术调研、或为 AI 项目生成报告和产品说明书时，都应该使用这个 skill。即使用户没有明确说"分析提示词"，只要他们想对一个包含大模型调用的项目做整体理解或生成文档，就应该触发。典型触发表述（中英文均适用）："看看这个项目怎么调 GPT 的"、"这个 agent 框架的 prompt 在哪"、"帮我整理下这个 AI 项目"、"帮我学习一下这个项目"、"研究一下这个项目怎么用的大模型"、"给这个 AI 项目出个产品说明书"、"analyze this LLM project"、"extract all prompts from this repo"、"how does this project use Claude/OpenAI"。注意：本 skill 用于对项目进行系统化全面分析，不适用于翻译单个文件、修改配置参数、编写新代码等单点操作。
allowed-tools: Read Glob Grep Bash Edit Write Agent
---

<!--
  This skill is inspired by and built upon howPrompt by @comeonzhj
  Original project: https://github.com/comeonzhj/howPrompt
-->

# Study Project

分析当前工作目录下的开源大模型项目：找出所有提示词和工具定义，翻译成中文，梳理 LLM 使用架构，生成分析报告和产品说明书。

开源 LLM 项目中，提示词散落在代码各处——变量定义、配置文件、API 调用参数、独立模板文件——很难快速掌握全貌。这个 skill 通过系统化搜索把它们全部找出来，产出三样东西：翻译文档集、技术分析报告、面向普通用户的产品说明书。

## 五阶段流程

每完成一个阶段，简要汇报进度（用精确数字，不用"大部分"等模糊词）。

### 第一阶段：项目探索

确定项目根目录（向上查找 `.git/`、`package.json`、`pyproject.toml`、`go.mod`、`Cargo.toml`），找不到则用当前目录并提示用户确认。

快速建立项目认知：
1. 读 README.md 了解定位
2. 读依赖文件，识别 LLM 库（`langchain`、`openai`、`anthropic`、`crewai`、`autogen`、`llamaindex`、`dspy`、`instructor` 等）
3. 扫描目录结构，定位关键目录
4. 如果没有发现任何 LLM 相关依赖或提示词文件——告知用户并终止，不要在非 LLM 项目上硬凑内容
5. 评估项目规模：超过 1000 个文件的项目先扫核心目录（`src/`、`app/`、`lib/`），小项目全量扫描

搜索时排除：`node_modules/`、`vendor/`、`site-packages/`、`.venv/`、`venv/`、`dist/`、`build/`、`__pycache__/`。

### 第二阶段：提示词与工具识别

读取 [templates/search_patterns.md](templates/search_patterns.md) 获取 8 种搜索方法（文件名匹配、代码变量、API 调用、配置文件、RAG、模型参数、Few-shot、工具 Schema）。每种方法覆盖不同维度的提示词，全部执行才能确保不遗漏——比如文件名搜索能找到 `.md` 模板，但内联在代码中的 `system_prompt = """..."""` 只有代码变量搜索才能捕获。

**大型项目效率策略**：超过 500 个源文件时，用 Agent 子代理并行执行不同类别的搜索（如一个搜文件名模式，一个搜代码变量，一个搜 API 调用），然后合并结果。小项目直接顺序执行即可。

区分生产代码和测试代码（`test/`、`tests/`、`__tests__/`、`*_test.*`、`*.spec.*`）——测试中的提示词是 mock 数据，不代表项目的实际 LLM 使用方式，但仍然值得记录。

**追踪引用关系**：一个提示词可能在 A 文件定义、B 文件组装、C 文件调用。发现可疑文件后，读取提示词所在函数或类的完整上下文，追踪导入和引用链。

搜索完成后去重（同一提示词在定义处和调用处都出现时，以定义处为准），然后生成**提示词主清单**（MANIFEST）并写入文件。

#### 为什么 MANIFEST 必须持久化到文件

大型项目执行过程中对话上下文会被压缩。如果主清单只存在于对话中，压缩后就丢失了，后续阶段将无法验证完整性。所以：
- ≤100 个提示词：写入单文件 `ai_analysis/translated_prompts/MANIFEST.md`
- \>100 个提示词：按功能模块拆分为 `MANIFEST_[模块名].md`

清单必须逐项列出每一个提示词（文件路径、变量名、简述、翻译状态），不得用"等"、"其他 X 个"省略。生成后快速回顾各搜索来源确认没有遗漏。容易遗漏的来源：配对的 system/user 提示词、中英文双语变体、代码中内联的硬编码提示词、Agent 模板 JSON 中嵌套的字段、`_r` 后缀的推理模型变体。

MANIFEST 格式和验证流程参见 [reference/verification.md](reference/verification.md)。

### 第二阶段出口 → 第三阶段入口

第二阶段完成后，根据 MANIFEST 中的提示词数量，读取 [reference/scale_strategies.md](reference/scale_strategies.md) 选择第三阶段的执行策略（小型 ≤30 / 中型 31-100 / 大型 101-300 / 超大型 300+）。

### 第三阶段：提示词文档化

读取 [templates/doc_template.md](templates/doc_template.md) 获取翻译文档和索引模板。

为每个提示词创建独立的翻译文档（原文 + 中文翻译 + 关键参数 + 上下游链路）。之所以保留英文原文，是因为翻译可能丢失细微语义，方便用户校对。上下游链路记录是为了在报告中绘制提示词串联关系。

**并行策略**：超过 10 个提示词时，按功能模块分组，用 Agent 子代理并行生成。每个子代理负责 5-10 个提示词（不超过 15 个）。子代理 prompt 中给出模板文件路径让它自行读取——不要在 prompt 中内联模板，这样可以节省主 agent 构建 prompt 时的上下文消耗。并行不超过 6 个，超出则分波调度（每波全部返回后再派发下一波）。

**子代理可能失败**（权限拒绝、超时、静默失败等）。每个子代理完成后用 Glob 验证文件实际写入——子代理声称完成不等于事实，它可能因权限拒绝而静默失败。失败时重试 2 次，仍然失败则主 agent 兜底补写。详见 [reference/fault_handling.md](reference/fault_handling.md)。

**验证门控**：第三阶段结束前，比对 MANIFEST 与实际生成的翻译文件，确认每个提示词都有对应文档。确认 0 个缺失后才生成 INDEX.md。完整的 6 步验证流程参见 [reference/verification.md](reference/verification.md)。

### 第四阶段：项目分析报告

读取 [templates/report_template.md](templates/report_template.md) 获取 8 章报告模板。生成 `ai_analysis/AI_MODEL_USAGE_ANALYSIS.md`。报告面向技术读者（开发者、架构师），所有结论必须有代码位置支撑，不要凭空推测。如果某个章节不适用（如没有 RAG、没有多 Agent），写明"该项目未涉及"而不是跳过。

### 第五阶段：产品说明书

读取 [templates/guide_template.md](templates/guide_template.md) 获取 12 章说明书模板。生成 `ai_analysis/PRODUCT_GUIDE.md`。目标读者是没有编程经验的人——避免直接使用技术术语，必须用时紧跟括号解释。写作时站在"想用这个项目但不知道从哪开始"的用户视角。

## 输出目录

```
ai_analysis/
├── translated_prompts/
│   ├── MANIFEST*.md        ← 提示词主清单
│   ├── INDEX.md            ← 翻译文档索引（验证通过后生成）
│   └── [各翻译文档].md
├── AI_MODEL_USAGE_ANALYSIS.md
├── PRODUCT_GUIDE.md
└── ERRORS.md               ← 异常日志（有异常时才创建）
```

## 执行原则

### 上下文节约

这是大型项目成功执行的关键——主 agent 容易因为读取太多源文件内容而耗尽上下文窗口，导致后半程质量骤降甚至丢失关键信息。

- 主 agent 搜索阶段只记录文件路径列表，不 Read 文件内容。需要了解内容时委派 Explore 子代理，子代理返回一句话摘要
- 子代理 prompt 中给出模板文件路径让子代理自行 Read，不要在主 agent 中内联模板内容
- 验证阶段只比对文件名列表，不读翻译文档内容
- 汇报时使用简洁的统计数字和表格，不逐个列出所有文件详情

根据项目规模选择更具体的执行策略，参见 [reference/scale_strategies.md](reference/scale_strategies.md)。

### 翻译质量

- 保持技术术语准确（"chain of thought" → "思维链"，不意译为"一步步思考"）
- 占位符（`{variable}`、`{{template}}`、`${expr}`）保持原样
- 保留原始格式（Markdown、JSON、YAML 结构等）
- 有歧义的术语添加译注

### 诚实报告

用户可以接受"还有 15 个没翻译完，原因是 XXX"，但无法接受"全部完成"后发现一半是空的。报告时用精确数字（"已完成 67/72 个"而非"大部分完成"），如有异常引导用户查看 ERRORS.md。故障分类和降级交付规则参见 [reference/fault_handling.md](reference/fault_handling.md)。

## 断点续传

大型项目执行中对话上下文会被压缩，skill 通过文件系统实现断点续传——如果发现 `ai_analysis/` 已有部分产出，不要从头开始：

| 检查文件 | 如果存在说明… | 动作 |
|---------|-------------|------|
| `MANIFEST*.md` | 第二阶段已完成 | 跳过搜索，读取 MANIFEST |
| 翻译文档但无 INDEX.md | 第三阶段部分完成 | 从验证步骤开始（比对 MANIFEST 与已有文件） |
| `INDEX.md` | 第三阶段已完成 | 跳过翻译 |
| `AI_MODEL_USAGE_ANALYSIS.md` | 第四阶段已完成 | 跳过报告 |
| `PRODUCT_GUIDE.md` | 第五阶段已完成 | 跳过说明书 |

## 特殊情况

- **中文提示词**：标注位置，跳过翻译
- **加密/混淆内容**：标注 [无法解析] 并记录位置
- **动态生成的提示词**：追踪函数调用链和条件分支，还原模板，用 `{动态部分}` 标记可变内容
- **超大提示词**：分段翻译，保持结构完整
- **外部托管**：代码中只有 `prompt_id` 引用时，标注 [内容托管于外部平台: 平台名称]
- **不确定的内容**：标注 [待确认] 并说明原因

## 交互与进度

1. 每完成一个阶段，简要汇报发现数量和关键信息
2. 发现不确定的内容时标注 [待确认] 继续推进，不中断等待确认
3. 完成后提供完整的文件清单和核心发现摘要
4. 如果某阶段搜索结果为空，在报告中如实说明
5. 如果中途发现之前阶段有遗漏，回头补齐后再继续
6. 如果出现无法解决的故障，立即告知用户：出了什么问题、已经尝试了什么、建议用户怎么做
