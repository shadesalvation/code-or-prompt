# 当前 Phase1 标准与输出样例

本文档用于汇总**目前正在使用的 Phase1 编码标准**，并给出若干已经按较新口径重做过的输出样例，便于后续保持一致。

说明：

- 本文档总结的是**当前口径**，不是最早一批 Phase1 文件的历史格式。
- 早期文件中若仍存在旧结构，应以本文档和近期重做文件为准。
- 当前较能代表新标准的样例包括：
  - `02-audio-transcriber-phase1-coding.yaml`
  - `03-database-design-phase1-coding.yaml`
  - `05-supply-chain-attack-simulation-phase1-coding.yaml`
  - `19-arboreto-phase1-coding.yaml`
  - `20-notebooklm-phase1-coding.yaml`

## 1. 当前输出结构

当前推荐输出结构如下：

```yaml
skill_id: "xx_skill-name"
skill_source: "zzzexamples/xx_skill-name"

task_code: "该 skill 主要完成的任务"
domain_hint: "领域 / 子领域"

implementation:
  components:
    - part: "某一组成部分"
      nature: "这部分内容是什么"
      approx_weight: "~xx%"
      role: "它在 skill 中承担什么作用"
    - part: "另一组成部分"
      nature: "..."
      approx_weight: "~yy%"
      role: "..."
  desc_code_relationship: |
    描述 description、code、reference 如何配合，
    哪一层负责导航，哪一层负责执行，哪一层负责知识支撑。
  implementation_mode: "对整体实现方式的总结"

review_flags:
  - "需要人工复核的边界点 1"
  - "需要人工复核的边界点 2"
```

## 2. 字段含义

### `skill_id`

- 直接对应样例 skill 编号与目录名。
- 建议保留编号，方便后续横向比较。

### `skill_source`

- 指向 `zzzexamples/` 下的原始目录。
- 便于回溯原始文件结构。

### `task_code`

- 用一句话概括该 skill 的主要任务。
- 尽量写成“Agent 实际在做什么”，而不是泛泛领域名。

### `domain_hint`

- 用于标注大致领域。
- 可以是单领域，也可以是 `主领域 / 子领域` 的形式。

### `implementation.components`

这是当前 Phase1 的核心部分。  
每个 component 需要写清楚三件事：

- `part`：这部分到底由哪些文件/内容组成
- `nature`：它本质是什么内容
- `role`：它在整个 skill 里的作用

### `desc_code_relationship`

这个字段不是简单重复比例，而是解释：

- Description 是否明确调用 Code
- Code 是否只是辅助，还是实际执行核心
- Reference 是否只是背景资料，还是主要知识来源
- 去掉其中某部分后，skill 会丢失什么能力

### `implementation_mode`

这是一个**高层总结**，用于快速判断该 skill 的实现重心。常见写法例如：

- `纯 Description 驱动`
- `Description 主导 + Code 执行`
- `Code 主导 + Description 操作编排`
- `Reference 主导 + Code 辅助校验`
- `Code 主导 + Description 轻量说明`

### `review_flags`

用于记录边界判断和不确定点，典型内容包括：

- 某个 markdown 同时混合了 command 和 reference
- 某个脚本能力与 SKILL.md 表述不完全一致
- 某目录下文件名像 reference，但实际承担的是 description
- 某 skill 依赖仓库外部环境/外部模块，不能独立运行

## 3. 当前三分法定义

## `description`

优先归入 `description` 的内容：

- 纯自然语言说明
- 规则、流程、导航、决策顺序
- 何时触发 skill、何时停止、何时追问
- 行为边界、安全约束、默认策略

一句话理解：

`description` 主要回答“Agent 应该怎么想、怎么走流程、怎么决定下一步”。

## `code`

优先归入 `code` 的内容：

- 独立可执行脚本/程序
- 会被 Agent 原样执行的命令块
- markdown 中明确作为执行入口的 bash/python/node/cli 命令

当前采用的关键规则：

1. **独立脚本文件**计入 `code`
2. **嵌在 `.md` 里、但会被 Agent 原样执行的命令**计入 `code`

例如：

- `python scripts/run.py ask_question.py ...`
- `bash scripts/install-requirements.sh`
- `pip-audit --format json`
- `dask-scheduler`

## `reference`

优先归入 `reference` 的内容：

- 结构化、信息型、可查阅的主题材料
- API 速查、对比表、工作流图、字段说明
- 用来“理解”和“查询”的资料
- 示教性质代码

当前采用的关键规则：

1. **示教型代码**归入 `reference`，不归入 `code`
2. 结构化方法论若更像“专题知识卡片/速查页”，优先归入 `reference`

例如：

- `generate_meeting_minutes()` 这种用于说明思路的代码模板
- API response JSON 结构示例
- 选型对比表、MITRE 映射表、决策树
- 分主题的 `database-selection.md` / `orm-selection.md`

## 4. 边界判断规则

## 4.1 `SKILL.md` 不能整体一刀切

当前口径下，`SKILL.md` 需要**内部拆分**。

同一个 `SKILL.md` 里可能同时有：

- 纯自然语言说明 -> `description`
- 会直接执行的命令块 -> `code`
- 示教型 Python/JS/JSON 模板 -> `reference`

因此，不能再简单写成“SKILL.md 全算 description”。

## 4.2 结构化方法论算不算 `description`

答案是：**理论上可以，操作上通常要看它更像哪一类。**

推荐判定法：

- 如果主要是在说“你现在该怎么做、按什么顺序做、有什么行为约束”，更偏 `description`
- 如果主要是在说“某个主题有哪些原则、选项、决策树、对比表，供你按需翻查”，更偏 `reference`

因此：

- `03-database-design` 里的 6 份专题文档，更适合作为 `reference`
- `20-notebooklm` 里要求“先认证、再选 notebook、再追问直到信息完整”的流程性规则，更适合作为 `description`

## 4.3 混合文件要拆分，不必整文件单归类

例如 `05` 的 `api-reference.md`：

- `pip-audit`、`curl`、`pip install` 这些命令 -> `code`
- JSON 结构示例、MITRE 表格、指标表 -> `reference`

所以混合文件允许拆分，不要求整文件只能进一种类别。

## 4.4 比例是“近似值”，不是精密统计

`approx_weight` 当前是**近似百分比**，通常基于：

- 文件行数
- 内容块体量
- 命令/说明/示教代码在整体中的占比

写法建议：

- `~5%`
- `~35%`
- `~62%`

不要求加总到数学上绝对精确，但应尽量接近 100%。

## 4.5 哪些内容通常不计入三类比例

通常可不计或只在 `review_flags` 说明：

- `LICENSE`
- `.gitignore`
- 图片资源
- evals / tests 数据文件
- 很薄的元数据文件
- 纯版本日志（如有时只在 `review_flags` 提一下）

## 5. implementation_mode 的常见判定

## `纯 Description 驱动`

适用：

- 几乎没有独立脚本
- skill 主要依赖提示词、规则、协议、工作流文本

## `Description 主导 + Code 执行`

适用：

- SKILL.md 明确规定完整操作流程
- 脚本负责其中的执行动作
- 没有 description，Agent 会不知道何时触发、如何编排

## `Code 主导 + Description 操作编排`

适用：

- 大型脚本是实际核心
- SKILL.md 主要负责调用顺序、wrapper 约束、操作纪律

## `Reference 主导 + Code 辅助校验`

适用：

- skill 的主要知识体量来自专题文档/结构化参考
- 脚本只是做局部校验或辅助处理

## `Code 主导 + Description 轻量说明`

适用：

- SKILL.md 很短
- 主要价值在可运行程序
- 文档只负责简短说明与触发条件

## 6. 当前推荐样例

下面选 5 个样例，分别代表不同实现模式。

### 样例 A：`02-audio-transcriber-phase1-coding.yaml`

代表类型：

- `Description 主导 + Code 执行`
- 同时展示了“SKILL.md 内部拆分”的做法

关键点：

- SKILL.md 中 bash 命令块计入 `code`
- SKILL.md 中教学性 Python 模板计入 `reference`
- 真正的执行核心在 `scripts/transcribe.py`

适合参考的场景：

- 工作流型 skill
- markdown 中既有命令又有示教代码

### 样例 B：`03-database-design-phase1-coding.yaml`

代表类型：

- `Reference 主导 + Code 辅助校验`

关键点：

- `SKILL.md` 只是轻量入口
- 6 份专题文档是知识主体
- `schema_validator.py` 只做局部 validator，不能替代整个 skill

适合参考的场景：

- 轻入口 + 多专题文档 + 单个辅助脚本

### 样例 C：`05-supply-chain-attack-simulation-phase1-coding.yaml`

代表类型：

- `Code 主导 + Description 轻量说明`
- 展示“混合 reference 文件拆分”的做法

关键点：

- `SKILL.md` 很短，只做风险面概述
- `agent.py` 是主要执行器
- `api-reference.md` 内部拆成 `code` 和 `reference`

适合参考的场景：

- 短说明 + 强脚本
- API/reference 文档中同时出现命令和表格/JSON

### 样例 D：`19-arboreto-phase1-coding.yaml`

代表类型：

- `Reference 主导 + Code 执行 + Description 轻量导航`

关键点：

- references/ 是主要体量
- `basic_grn_inference.py` 是标准执行入口
- SKILL.md 同时含导航和示教型 Python，需要拆分

适合参考的场景：

- 大量技术原理都写在 reference
- 脚本只是标准入口而不是知识主体

### 样例 E：`20-notebooklm-phase1-coding.yaml`

代表类型：

- `Code 主导 + Description 操作编排`

关键点：

- 8 个脚本体量巨大，是真正执行核心
- SKILL.md 中存在大量必须原样执行的 `python scripts/run.py ...`
- `AUTHENTICATION.md + references/` 是 why/how 层补充，而不是主入口

适合参考的场景：

- 大型自动化 skill
- wrapper 强约束
- 说明层和代码层强耦合

## 7. 当前实践建议

后续做新的 Phase1 时，建议按下面顺序判断：

1. 先看这个 skill 的**实际执行入口**在哪里  
   是脚本、命令块，还是根本没有代码。

2. 再看 `SKILL.md` 是在做什么  
   是主流程编排，还是仅仅做目录导航。

3. 再看其它 markdown 是什么性质  
   是步骤说明，还是结构化知识库，还是混合型文件。

4. 最后再写 `implementation_mode`  
   不要先贴标签，再倒推比例。

## 8. 一句话总结

当前 Phase1 的关键不是“按文件名分类”，而是：

- **按内容性质分类**
- **允许同一 markdown 内部拆分**
- **把会执行的东西与供查阅的东西分开**
- **用 `desc_code_relationship` 解释三者如何协同**

