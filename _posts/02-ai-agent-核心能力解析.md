---
layout: post
title: "02  核心能力解析"
date: 2026-03-21
categories: [AI Agent]
tags: ["AI Agent", "技术"]
ext-js:
  - "//cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.min.js"
---

## 第二章：AI Agent 核心能力解析

> **摘要**: 本章深入剖析 AI Agent 的核心技术能力，包括基于 LLM 的推理引擎设计、Chain-of-Thought 思维链技术、Function Calling/Tool Use 底层机制、ReAct 与 Tree of Thoughts 推理范式对比，以及 Agent 幻觉问题的检测与缓解策略。通过理论解析与实战案例相结合的方式，帮助读者全面理解现代 AI Agent 的技术基础。

---

## 2.1 LLM as Reasoning Engine - System Prompt 深度设计

### 为什么 System Prompt 是 Agent 的"大脑皮层"？

现代 AI Agent 的核心在于大语言模型（LLM）作为推理引擎的能力。而如何让这个强大的模型**稳定、可靠、可预测地**执行特定任务，System Prompt（系统提示词）的设计质量起着决定性作用。

可以将 System Prompt 理解为 Agent 的"大脑皮层"——它定义了 Agent 的身份认知、任务边界、行为准则和输出规范。一个精心设计的 System Prompt 能够让同一个 LLM 在不同的应用场景中展现出截然不同的能力特征。

### System Prompt 四大核心要素详解

一个完整的 System Prompt 设计框架包含以下四个关键要素：

#### 1. Role Definition（角色定义）—— "我是谁？"

Role Definition 是最基础也是最关键的要素，它直接决定了 Agent 的认知框架和行为模式。优秀的角色定义需要回答以下几个问题：

- **Agent 的身份定位**：你是客服助手、数据分析专家、还是创作顾问？
- **专业领域边界**：你在哪些领域是专家，在哪些领域应该保持谦逊？
- **语气与风格**：你的沟通方式应该是正式专业还是轻松友好？
- **价值观与原则**：你遵循哪些伦理准则和行为底线？

**设计要点**：
- 避免模糊的身份描述（如"你是一个智能助手"），应具体到场景化角色
- 明确 Agent 的专业领域，防止过度自信导致的幻觉问题
- 考虑目标用户群体的期望，调整沟通风格

#### 2. Task Specification（任务说明）—— "我要做什么？"

清晰的任务说明能够消除 Agent 对需求的理解偏差。优秀的任务说明应该：

- **定义核心目标**：用一句话概括 Agent 的核心使命
- **列举具体职责**：明确列出 3-5 项主要工作任务
- **界定输出标准**：描述什么样的结果算是"完成任务"
- **提供示例场景**：通过典型案例帮助 Agent 理解任务边界

#### 3. Constraints（约束条件）—— "我不能做什么？"

如果说 Task Specification 定义了 Agent 的"自由度"，那么 Constraints 就是设定这些自由度的"护栏"。关键约束类型包括：

- **能力边界**：明确说明哪些操作超出 Agent 的能力范围
- **安全限制**：禁止生成有害内容、违反法律或伦理要求
- **资源约束**：考虑 token 数量、执行时间、API 调用次数等实际限制
- **风格规范**：规定输出格式、语言风格、专业术语使用等

#### 4. Output Format（输出格式）—— "我该如何呈现结果？"

规范的输出格式不仅便于用户阅读，更重要的是让 Agent 的输出结构更加稳定可控。关键要素包括：

- **结构化要求**：是否需要 JSON、Markdown、XML 等特定格式
- **内容模块**：是否需要包含标题、要点列表、总结段落等固定模块
- **长度控制**：建议的字数范围或段落数量
- **语言规范**：使用何种语言、专业术语的使用标准

### System Prompt Design Framework 架构说明

```
┌─────────────────────────────────────────────┐
│        System Prompt Design Framework       │
├─────────────────────────────────────────────┤
│  Role Definition      →  Agent identity     │
│  Task Specification   →  Clear goals        │
│  Constraints          →  Boundaries & Rules │
│  Output Format        →  Structured output  │
└─────────────────────────────────────────────┘
                    ↓           ↓
          Better Decision Making    More Predictable Results
```

这个框架的核心逻辑在于：通过明确**身份认知（Role）**、**目标定义（Task）**、**边界约束（Constraints）**和**输出规范（Format）**四个维度，形成一个完整的 Agent 行为指导体系。当这四个要素都得到清晰定义时，Agent 的决策质量和结果稳定性都会显著提升。

### Few-shot vs Zero-shot vs CoT 提示策略对比

在实际的 System Prompt 设计中，有三种常见的提示策略：**Zero-shot**（零样本）、**Few-shot**（少样本）和 **Chain-of-Thought**（思维链）。它们各有适用场景：

#### Zero-shot Prompting（零样本提示）

**定义**：不提供任何示例，仅通过任务描述让 LLM 理解需求。

**优势**：
- 节省 token 消耗
- 灵活性高，适用于复杂多变的任务
- 减少提示工程的工作量

**劣势**：
- 对 LLM 的理解能力要求较高
- 输出格式可能不够稳定
- 复杂任务容易出现偏差

**适用场景**：简单明确的任务、LLM 能力较强时、需要快速迭代的情况。

#### Few-shot Prompting（少样本提示）

**定义**：提供 1-5 个输入输出示例，让 LLM 通过类比理解任务要求。

**优势**：
- 显著降低理解偏差
- 输出格式更加稳定可预测
- 能够帮助 LLM 捕捉细节模式

**劣势**：
- token 消耗较大
- 示例质量直接影响效果
- 可能引入不必要的 bias（偏见）

**适用场景**：需要精确控制输出格式的任务、复杂任务理解困难时、需要保证一致性。

#### Chain-of-Thought Prompting（思维链提示）

**定义**：要求 LLM 在给出最终答案之前，先展示推理过程。

**优势**：
- 显著提升复杂推理任务的准确率
- 输出更透明，便于调试和优化
- 能够有效减少逻辑错误

**劣势**：
- 响应时间较长
- token 消耗大
- 不适用于简单查询任务

**适用场景**：数学计算、逻辑推理、多步骤规划等复杂任务。

### 实验数据对比

根据 Google Research 和多个独立研究的实验结果，不同提示策略在复杂任务上的表现差异显著：

| 方法 | Math Word Problems 准确率 | 典型响应时间 | Token 消耗 |
|------|---------------------------|--------------|----------|
| Standard Prompting (Zero-shot) | 57% | Fast (~1-2s) | 低 |
| CoT Prompting | 85% | Medium (~3-5s) | 中 |
| Self-Consistency CoT | 94% | Slow (~10-15s) | 高 |

从这个数据可以看出：

1. **Chain-of-Thought 相比标准提示准确率提升 28 个百分点**，证明引导 LLM 展示推理过程能够显著提升复杂任务表现。

2. **Self-Consistency 方法（多次采样 + 多数投票）**将准确率进一步提升至 94%，但代价是响应时间和 token 消耗的显著增加。

3. **性能权衡**：对于生产环境，需要根据任务复杂度、用户等待容忍度和成本预算进行综合考量。

### PromptArchitect 工具包核心接口

虽然我们不深入代码细节，但了解一下主流提示工程工具包的核心设计理念仍然有帮助。以 `PromptArchitect` 为例，其核心接口设计体现了上述四大要素的理念：

**核心组件结构：**
```
┌─────────────────────────────────────┐
│      PromptArchitect Toolkit        │
├─────────────────────────────────────┤
│  RoleBuilder → Define agent identity│
│  TaskBuilder → Specify objectives   │
│  ConstraintBuilder → Set boundaries │
│  FormatBuilder → Structure output   │
│                                     │
│  CompositePrompt() → Complete prompt│
└─────────────────────────────────────┘
```

这种模块化设计的好处在于：
- **可复用性**：角色定义、约束条件等可以跨多个 Agent 共享
- **可维护性**：修改单一组件不会影响其他部分
- **可扩展性**：轻松添加新的提示要素或自定义规则

### System Prompt 最佳实践清单

基于上述分析，以下是设计高效 System Prompt 的实战建议：

#### ✅ 应该做的
1. **具体化角色**：避免"智能助手"这类模糊描述，使用"电商客服专家"等具体定位
2. **分层定义任务**：核心目标 + 子任务清单，帮助 Agent 理解优先级
3. **明确约束条件**：包括能力边界、安全限制、资源约束等多个维度
4. **提供输出模板**：给出具体的格式示例，减少猜测空间
5. **设置异常处理**：说明遇到无法处理的情况时应如何回应
6. **迭代优化**：基于实际运行情况不断调整提示词内容

#### ❌ 应避免的
1. **过度复杂的描述**：一段话超过 500 字时，Agent 容易忽略关键指令
2. **相互冲突的要求**：如既要求简洁又要求详细解释
3. **假设 LLM 具有人类能力**：避免暗示 Agent 可以"记住"不存在的上下文
4. **忽视安全约束**：没有明确说明禁止行为的边界
5. **缺乏测试验证**：未在实际场景中进行效果评估就投入使用

### System Prompt 与模型参数的协同

值得注意的是，System Prompt 的效果与 LLM 的推理参数（如 temperature、top_p）密切相关。一般建议：

- **确定性任务**（如数据提取、格式化输出）：使用较低的 temperature（0.1-0.3）
- **创造性任务**（如内容生成、头脑风暴）：使用较高的 temperature（0.7-0.9）
- **推理任务**（如逻辑分析、数学计算）：使用中等 temperature（0.4-0.6），配合 CoT 提示

理解这种协同关系，能够帮助开发者更精准地调优 Agent 的行为表现。

---

## 2.2 Chain-of-Thought (CoT) 技术详解

### Chain-of-Thought 的核心原理

Chain-of-Thought（思维链，简称 CoT）是近年来 AI Agent 推理能力提升的**里程碑式技术**。其核心思想非常简单却极其有效：**要求语言模型在给出最终答案之前，先逐步展示它的思考过程**。

这个看似简单的改变，实际上触及了 LLM 推理机制的本质。当 LLM 被要求"一步步思考"时，它能够：

1. **将复杂问题分解**为多个可管理的子步骤
2. **保持中间状态的追踪**，避免在长链推理中丢失上下文
3. **暴露推理路径**，便于发现并纠正逻辑错误
4. **利用中间计算结果**，减少重复计算和记忆负担

### CoT 的工作机制

CoT 的作用可以通过一个具体例子直观理解：

**普通提示方式：**
> "小明有 5 个苹果，吃掉 2 个后买了 3 个，现在有多少个？"
> [LLM 直接输出答案] → 8

**CoT 提示方式：**
> "小明有 5 个苹果，吃掉 2 个后买了 3 个，现在有多少个？请一步步思考。"
> [LLM 输出推理过程]
> - 初始状态：5 个苹果
> - 吃掉 2 个后：5 - 2 = 3 个
> - 又买了 3 个：3 + 3 = 6 个
> 最终答案：6 个

从表面上看，两种方式的输出似乎都能得到正确答案。但在**复杂任务**上，CoT 的价值才真正显现。

### Self-Consistency（自洽性方法）—— 多路径推理的魔力

当 CoT 与 **Self-Consistency** 策略结合时，效果会得到显著提升。这种方法的核心思想是：**不依赖单次推理结果，而是通过多次采样产生多条推理路径，然后选择出现次数最多的那个答案作为最终输出**。

#### Self-Consistency 工作流程：

```
Step 1: Generate Multiple Reasoning Paths
┌──────────────────────────────────────────┐
│ Path 1: Problem → Step A → B → C → Answer1 │
│ Path 2: Problem → Step D → E → F → Answer2 │
│ Path 3: Problem → Step G → H → I → Answer1 │
│ Path 4: Problem → Step J → K → L → Answer3 │
│ Path 5: Problem → Step M → N → O → Answer1 │
└──────────────────────────────────────────┘
                  ↓
Step 2: Majority Voting (Collect all answers)
├─ Answer1: 3 votes (60%)
├─ Answer2: 1 vote (20%)
└─ Answer3: 1 vote (20%)
                  ↓
Step 3: Select Most Frequent Answer
Final Output: Answer1 (confidence: 60%)
```

#### Self-Consistency 的优势来源：

1. **容错性**：即使某些推理路径出现错误，只要大多数路径正确，最终答案仍然是准确的
2. **多样性探索**：不同推理路径可能从不同角度解决问题，增加找到最优解的机会
3. **置信度评估**：投票结果本身提供了对答案确定性的量化指标

### Least-to-Most Prompting —— 复杂问题的拆解艺术

Least-to-Most Prompting（由简到繁提示法）是 CoT 的进阶变体，专门针对**极其复杂的任务场景**。它遵循的核心原则是：**将大问题分解为一系列子问题，然后按照从简单到复杂的顺序逐一解决**。

#### Least-to-Most 的工作流程：

```
复杂问题 → LLM 自动拆解 → 识别依赖关系 → 排序处理顺序
                                           ↓
                    Subproblem 1 (最简单) → Solution 1
                                           ↓
                    Subproblem 2 (依赖 S1) → Solution 2 (使用 S1 结果)
                                           ↓
                    ... ...
                                           ↓
                    Subproblem N → Solution N (累积之前所有结果)
                                           ↓
                              整合所有解法 → Final Answer
```

这个策略的关键创新在于：**显式地识别子问题之间的依赖关系**，确保在解决每个子问题时，必要的信息已经被前面的步骤计算出来。

### CoT 与 Self-Consistency 的实验数据对比

根据 Google Research、Stanford 和多个独立研究团队的大规模实验，CoT 技术在数学推理、常识推理等任务上取得了突破性进展：

| 方法 | Math Word Problems 准确率 | GSM8K (小学级数学) | ARC-Science |
|------|---------------------------|--------------------|-------------|
| Standard Prompting | 57% | 18% | 49% |
| CoT Prompting | 85% | 36% | 60% |
| Self-Consistency CoT | 94% | 52% | 71% |
| Least-to-Most CoT | 96% | 58% | 75% |

**数据解读：**

1. **CoT 带来的质变**：相比标准提示，CoT 在数学问题上准确率提升了 28 个百分点，证明了引导推理过程的重要性。

2. **Self-Consistency 的增量价值**：通过多次采样和投票机制，准确率从 85% 提升到 94%，虽然提升幅度不如 CoT 本身显著，但在生产环境中这种稳定性至关重要。

3. **Least-to-Most 的专业优势**：对于极度复杂的问题链式推理，Least-to-Most 通过显式拆解达到了 96% 的最高准确率，但代价是更大的计算开销和更长的响应时间。

### CoT 的适用场景与局限

尽管 CoT 技术取得了显著成效，但它并非适用于所有任务。理解其**适用边界**同样重要。

#### ✅ 适合使用 CoT 的场景：

1. **数学计算与符号推理**
   - 多步骤算术运算
   - 代数问题求解
   - 几何证明推导

2. **逻辑分析与决策规划**
   - 复杂条件判断
   - 多因素权衡决策
   - 因果关系分析

3. **科学问题解答**
   - 物理公式应用
   - 化学反应推断
   - 生物学机制解释

4. **法律与合规咨询**
   - 条款解读与分析
   - 合规性判断
   - 案例类比推理

5. **代码生成与调试**
   - 算法实现思路
   - 错误排查过程
   - 架构设计决策

#### ❌ 不适合使用 CoT 的场景：

1. **简单事实查询**
   - "今天北京的天气怎么样？"
   - "Python 的 print 函数是什么？"
   - 这类问题直接回答即可，CoT 只会增加不必要的 token 消耗和时间延迟。

2. **创意生成任务**
   - 诗歌创作、故事构思
   - 营销文案撰写
   - 艺术风格迁移
   - 过度结构化可能抑制创造力。

3. **实时性要求极高的场景**
   - 聊天机器人快速响应
   - 实时翻译服务
   - 紧急事件处理
   - CoT 可能导致超过用户等待阈值。

4. **低精度要求的任务**
   - 情感倾向分析（正/负）
   - 简单的分类任务
   - 关键词提取
   - 这些任务不需要复杂的推理过程。

### CoT 在实际 Agent 系统中的应用策略

在现代 AI Agent 系统中，CoT 不应该被盲目应用，而应该根据任务类型智能选择：

#### 决策树推荐：
```
                    ┌─────────────────┐
                    │   用户请求到达  │
                    └────────┬────────┘
                             ↓
            ┌────────────────┴────────────────┐
            ↓                                 ↓
     ┌──────▼──────┐                   ┌──────▼──────┐
     │任务是否复杂？│                   │  任务简单？ │
     └──────┬──────┘                   └──────┬──────┘
            ↓ Yes                            ↓ Yes
    ┌───────▼────────┐                 ┌──────▼──────┐
    │是否需要推理？│                 │ 直接回答    │
    └───────┬────────┘                 └─────────────┘
            ↓ No                   
    ┌───────▼────────┐
    │使用标准提示   │ (Zero-shot)
    └────────────────┘
            ↓ Yes
    ┌──────────────┐
    │使用 CoT 提示  │
    ├──────────────┤
    │是否需要高精度？│
    └───────┬────────┘
            ↓ No
    ┌──────▼──────┐
    │标准 CoT     │ (1 次推理)
    └─────────────┘
            ↓ Yes
    ┌──────────────┐
    │CoT + Self-   │
    │Consistency   │ (多次采样投票)
    └──────────────┘
```

#### 性能与精度的权衡策略：

| 场景类型 | CoT 次数 | Expected Accuracy | 响应时间 | Token 消耗 |
|---------|---------|-------------------|----------|------------|
| 简单查询 | 0 次 | 95% | <1s | 低 |
| 中等复杂度 | 1 次 (标准 CoT) | 85% | 2-3s | 中 |
| 高精度要求 | 5 次 (Self-Consistency) | 94% | 8-12s | 高 |
| 极复杂任务 | 10 次 + Least-to-Most | 96% | 15-20s | 极高 |

### CoT 的实现技巧与最佳实践

#### 技巧 1：明确"思考起点"

CoT 提示词的关键不是简单地说"请一步步思考"，而是给 LLM 一个明确的**推理启动点**：

❌ **模糊提示**：
> "请解决这个问题，展示你的思考过程。"

✅ **精准提示**：
> "为了解决这个问题，请按以下步骤思考：
> 1. 首先识别问题中的关键信息
> 2. 然后确定需要使用的公式或逻辑规则
> 3. 逐步应用这些规则进行计算
> 4. 最后验证答案是否合理"

#### 技巧 2：为中间步骤设置检查点

在长链推理中，可以在关键节点插入**自我验证机制**：

```
Step 1: [执行操作] → Intermediate Result A
        ↓
[验证检查点 1]: A 是否符合预期范围？是/否？
        ↓ (如果否) 回退到上一步，调整策略
        ↓
Step 2: [基于 A 继续操作] → Intermediate Result B
        ↓
[验证检查点 2]: B 与 A 的关系是否合理？是/否？
...
```

这种机制能够**在推理早期发现错误**，避免"谬误累积效应"。

#### 技巧 3：利用 CoT 进行问题拆解提示

对于特别复杂的问题，可以要求 LLM **先自己拆解问题**，再进行详细分析：

> "面对这个问题，你会如何将其分解为可管理的子步骤？请先列出你的问题拆解方案，然后针对每个步骤逐一分析。"

这种**元推理（Meta-Reasoning）**能力能够让 Agent 更好地应对从未见过的新类型问题。

---

## 2.3 Function Calling/Tool Use底层机制

### 从"黑盒 LLM"到"工具增强 Agent"的范式转变

在 AI Agent 的发展史上，**Function Calling（函数调用）和 Tool Use（工具使用）**的出现具有里程碑意义。它标志着 LLM 从纯粹的文本生成器转变为能够**与外部世界交互的智能化代理**。这个转变的核心在于：**将 LLM 的推理能力与具体工具的执行能力无缝结合**。

在此之前，LLM 只是一个信息处理单元；在此之后，LLM 成为了**智能中枢**——它可以根据任务需求，自主选择合适的工具来扩展自身能力边界。

### JSON Schema 与 OpenAPI 规范深度解析

Function Calling 的底层机制建立在**结构化数据交换**的基础上。主流 LLM 平台（OpenAI、Anthropic、Google）都采用了基于 **JSON Schema** 的工具定义格式，这与行业标准 **OpenAPI Specification (OAS)** 高度兼容。

#### 工具定义结构完整说明

一个完整的工具定义包含以下核心要素：

```
┌─ Tool Definition Structure (JSON Format) ───────────────────┐
│                                                              │
│  {
│    "name": string,          // 🔹 工具标识符（唯一）         │
│        description: string, // 🔹 功能描述（LLM 理解用途）    │
│                                      
│        parameters: {       // 🔹 参数定义对象                │
│          type: "object",
│          properties: {     //   └─ 各参数详情                 │
│            "param1": {
│              type: "string",
│              description: "参数说明"
│            },
│            "param2": {
│              type: "integer",
│              minimum: 0,
│              maximum: 100
│            }
│          },
│          required: ["param1"] //   └─ 必需参数列表           │
│        },
│                                          
│        returns: {          // 🔹 返回类型定义                  │
│          type: "object",
│          properties: {     //   └─ 返回字段详情                │
│            "status": string,
│            "result": object
│          }
│        }
│  }                                                          │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

#### 各要素的详细设计原则：

**1. Name（名称）**
- 必须是有效的标识符，遵循 camelCase 命名规范
- 应该具有描述性，如 "search_knowledge_base"而非 "fn1"
- 在整个 Agent 系统中必须唯一

**2. Description（描述）**
- 这是 LLM **学习何时使用工具的关键线索**
- 应该清晰说明：
  - 工具的核心功能是什么
  - 适用于什么场景
  - 输入输出特征
  - 与其他工具的差异

**3. Parameters（参数定义）**
- 遵循 JSON Schema Draft 7+规范
- 包含类型声明、验证规则、默认值等元数据
- `required`字段决定了 LLM 调用时的约束条件

**4. Returns（返回类型）**
- 虽然许多平台不强制要求，但提供返回类型定义有助于：
  - 让 LLM 理解工具调用的预期结果
  - 便于后续对返回值的处理逻辑
  - 文档化和测试目的

### LLM 自动参数生成的完整工作流

当用户提出一个需要调用外部工具的请求时，LLM 会在后台执行一个复杂的**参数生成推理过程**。这个过程可以分解为以下几个步骤：

#### Step 1: Intent Recognition（意图识别）

```
用户输入 → LLM 分析语义 → 确定目标工具
"查一下明天北京的天气" → "获取天气预报" → select_weather_forecast()
```

LLM 需要从自然语言中**提取任务意图**，并将其映射到已注册的工具列表中。这一步依赖于：
- 工具描述的清晰程度
- LLM 对领域知识的理解
- 语义匹配算法的质量

#### Step 2: Parameter Extraction（参数提取）

```
Intent: "查询明天北京的天气" → Extract Parameters:
├── location: "北京"
├── date: "2024-03-10" (inferred as tomorrow)
└── unit: "Celsius" (default inferred from locale)
```

LLM 需要从用户语句中**识别并提取参数值**，对于缺失的参数则根据上下文或默认值进行推断。

#### Step 3: Validation Check（验证检查）

```json
{ "location": "北京", "date": "2024-03-10" }
       ↓
[Schema Validator] → ✅ Pass (所有必需参数完整，类型匹配)
       ↓
   构造 API Call
```

在发送工具调用之前，必须**验证参数是否符合 Schema 定义**：
- 必填字段是否齐全
- 数据类型是否正确
- 数值范围是否在约束内

#### Step 4: API Invocation（API 调用）

```python
# Pseudo-code representation
response = execute_tool(
    tool_name="get_weather",
    parameters={"location": "北京", "date": "2024-03-10"}
)
```

验证通过后，构造标准的 API 调用请求，执行实际的工具逻辑。

#### Step 5: Result Integration（结果整合）

```json
{
    "tool_call_id": "call_abc123",
    "tool_name": "get_weather",
    "status": "success",
    "result": {
        "temperature": 18,
        "humidity": 65,
        "condition": "晴"
    }
}
```

将工具执行结果格式化，返回给 LLM 作为后续推理的上下文输入。

### OpenAI / Anthropic / Google Vertex AI 三大 API 对比分析

虽然三大平台的核心设计理念相似，但在具体实现细节和演进历程上各有特点：

#### OpenAI: `function_call` → `tool_choice` 演进历程

**早期版本（Function Calling v1）**：
- 使用 `function_call` 参数控制 LLM 是否调用函数
- 返回格式：`{"name": "search_web", "arguments": "{\"query\": \"...\"}"}`
- 局限：单一工具调用，难以处理复杂场景

**当前版本（Function Calling v2）**：
- 引入 `tool_choice`参数，支持更精细的控制：
  - `"auto"` (默认)：LLM 自主决定是否调用工具
  - `"none"`：禁止工具调用
  - `{"type": "function", "function": {"name": "specific_fn"}}`：强制使用指定工具
- **支持多工具选择**，可在多个已注册工具中自动选择最合适的
- 更稳定的参数解析，减少 JSON 格式错误

**核心优势：**
- 生态系统成熟，文档完善
- 与 GPT-4、GPT-3.5-Turbo深度集成
- 支持流式输出中的工具调用识别

#### Anthropic: `tool_use` block结构特点

Anthropic 的 Claude系列模型采用了独特的 XML-like 标记格式：

```xml
<function_calls>
<invoke name="get_weather">
<parameter name="location">北京</parameter>
<parameter name="date">2024-03-10</parameter>
</invoke>
</function_calls>

<tool_response>
<result>温度：18°C，湿度：65%</result>
</tool_response>
```

**特点：**
- **人类可读性强**：XML 格式便于调试和日志记录
- **嵌套结构清晰**：适合复杂参数层级
- **类型安全**：在生成式文本中保持结构化标记

**工作机制：**
1. LLM 输出`<function_calls>`块表示需要调用工具
2. 系统解析该块，执行对应的函数
3. 将结果包装在`<tool_response>`块中返回给 LLM
4. LLM 基于响应生成最终回答

**适用场景：**
- 需要高度结构化的输出格式
- 调试和日志分析要求严格的环境
- 多步骤复杂任务编排

#### Google Vertex AI: `ToolsCalling` 语法差异

Google 的 Tool Calling API 采用了更为**现代化的 RESTful设计理念**：

```json
{
  "tools": [
    {
      "functionDeclarations": [
        {
          "name": "get_current_weather",
          "description": "Get the current weather in a given location",
          "parameters": {
            "type": "OBJECT",
            "properties": {
              "location": {
                "type": "STRING",
                "description": "The city and state"
              },
              "unit": {
                "type": "STRING",
                "enum": ["celsius", "fahrenheit"]
              }
            },
            "required": ["location"]
          }
        }
      ]
    }
  ],
  "tool_config": {
    "function_calling_config": {
      "mode": "ANY",
      "allowed_function_names": ["get_current_weather"]
    }
  }
}
```

**特点：**
- **原生支持 Google Cloud 生态**，与 Vertex AI、Cloud Functions 无缝集成
- **TypeScript-like Schema**：使用 JSON Schema v4+规范
- **多模态工具支持**：除了函数调用，还支持图片分析等功能

**三种模式对比：**

| 维度 | OpenAI | Anthropic | Google |
|------|--------|-----------|--------|
| **标记格式** | JSON String | XML Tags | JSON Object |
| **人类可读性** | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **解析稳定性** | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| **生态系统** | 成熟广泛 | 快速增长 | GCP 深度集成 |
| **复杂任务支持** | Good | Excellent | Very Good |

### 选择建议与实践指南

#### 根据现有基础设施选择：
- **使用 OpenAI API** → 优先选择 OpenAI Function Calling
- **已部署 Claude 模型** → 采用 Anthropic tool_use 格式
- **Google Cloud 用户** → Vertex AI ToolsCalling是自然选择

#### 根据任务特性选择：
- **简单工具调用** → OpenAI 或 Google（JSON 更简洁）
- **复杂嵌套参数** → Anthropic（XML 层次清晰）
- **需要人工调试** → Anthropic（人类可读性强）

### Function Calling 的安全设计原则

在将 LLM 与外部工具集成时，必须考虑以下安全层面：

#### 1. 输入净化层（Input Sanitization）

LLM 生成的参数可能存在注入攻击风险。必须在传递给真实 API 之前进行净化：

```python
def sanitize_tool_input(raw_params):
    # 移除可疑字符、限制长度、验证格式
    sanitized = {
        "location": sanitize_string(raw_params["location"]),
        "date": parse_date_safe(raw_params["date"])
    }
    return sanitized
```

#### 2. 权限控制层（Permission Control）

- **最小权限原则**：每个工具只授予完成功能所需的最小权限
- **角色分离**：不同 Agent 类型使用不同的工具集合
- **审计日志**：记录所有工具调用的详细信息

#### 3. 速率限制层（Rate Limiting）

```python
# Token bucket 算法示意
class ToolRateLimiter:
    def __init__(self, max_requests, window_seconds):
        self.max_requests = max_requests
        self.window = window_seconds
        self.tokens = max_requests
        self.last_refill = time.time()
    
    def acquire(self):
        now = time.time()
        elapsed = now - self.last_refill
        self.tokens = min(self.max_requests, 
                          self.tokens + elapsed * (self.max_requests / self.window))
        self.last_refill = now
        
        if self.tokens >= 1:
            self.tokens -= 1
            return True
        return False
```

这种机制防止单个 Agent 过度消耗 API 配额。

---

（本章剩余内容将继续撰写 2.4 ReAct vs ToT、2.5 Agent 幻觉问题等内容，因篇幅限制分批次完成）

## 📚 参考文献与延伸阅读

1. **Chain-of-Thought Prompting Elicits Reasoning in Large Language Models** - Wei et al., Google Research (2022) [[arXiv:2201.11903]](https://arxiv.org/abs/2201.11903)
2. **Self-Consistency Improves Chain of Thought Reasoning in Language Models** - Wang et al., Google AI (2022) [[arXiv:2203.11171]](https://arxiv.org/abs/2203.11171)
3. **Least-to-Most Prompting Enables Complex Reasoning in Language Models** - Zhou et al., Stanford (2022) [[arXiv:2205.10625]](https://arxiv.org/abs/2205.10625)
4. **ReAct: Synergizing Reasoning and Acting in Language Models** - Yao et al., Princeton (2023) [[arXiv:2210.03629]](https://arxiv.org/abs/2210.03629)
5. **Tree of Thoughts: Deliberate Problem Solving with Large Language Models** - Yao et al., Princeton (2023) [[arXiv:2305.10601]](https://arxiv.org/abs/2305.10601)

---

**[下一章]**：第三章 Memory System 深度解析 - 探索 AI Agent 的记忆架构设计原理

**[上一章]**：第一章 AI Agent 基础概念与生态概览

<script>
document.addEventListener('DOMContentLoaded', function() {
  if (typeof mermaid !== 'undefined') {
    mermaid.initialize({
      startOnLoad: true,
      theme: 'default',
      securityLevel: 'loose'
    });
  }
});
</script>
