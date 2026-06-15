---
name: cc-skill-prompt-engineer
description: Prompt engineering assistant. Activate this skill whenever the user types /prompt-engineer (optionally followed by their rough idea), or explicitly asks to "improve my prompt", "rewrite this prompt", "engineer a prompt for me", or similar phrasing. The skill converts a rough idea into a structured, optimized 5-component prompt. Do NOT activate for regular questions, code tasks, or general conversation — only on /prompt-engineer or an explicit prompt-engineering request.
---

# Prompt Engineer / Prompt 工程师

将用户的粗糙指令转化为结构化、高质量的 prompt。

---

## 触发与提取

用户输入 `/prompt-engineer` 时，将其后跟随的文字视为待工程化的原始 prompt；若 `/prompt-engineer` 单独出现（无跟随文字），则把同一条消息的其余内容作为原始 prompt。补充说明作为上下文参考。

---

## 核心公式（五组件结构）

每个优化后的 prompt 必须包含这五个组件：

1. **Context & Role（上下文与角色）**
   Claude 是谁？处于什么场景？这锚定了回应的基调和专业视角。

2. **Query & Task（核心任务）**
   具体要做什么？一句话说清，避免多任务叠加。

3. **Specifications（规格与约束）**
   有哪些要求、限制、排除项？（字数、格式、受众、禁止事项等）

4. **Quality Criteria（质量标准）**
   什么样的输出算"好"？判断标准越明确，结果越可预测。

5. **Response Format（输出格式）**
   期望的结构：段落 / 列表 / Markdown / JSON / 代码 / 表格？
   选择依据：
   - **段落叙述** → 解释类、说服类
   - **列表 / Markdown** → 步骤、对比、扫描类内容
   - **JSON** → 机器读取、数据提取
   - **代码块** → 技术实现
   - **表格** → 多维度比较

---

## 执行流程

### 第一步：评估信息充分性

拿到原始 prompt 后，判断五个组件的信息是否足够：

- **信息充分** → 直接进入第二步
- **信息不足** → 先提问。**每次只问一个最关键的缺失信息**；能给候选时**优先用选择题**（列 2-3 个选项）降低回答门槛，等收到回答后再继续，直到信息足够
- **多任务叠加** → 若原始 prompt 实为多个独立任务，先指出并建议**拆成多个 prompt**，而非塞进一个（单个 prompt 聚焦一件事，质量更可控）

判断标准：任务意图清晰、目标受众可推断、核心约束可识别，即为"充分"。不需要完美，宁可合理推断也不要无限追问。

### 第二步：生成优化 prompt

按五组件结构输出，格式如下：

---
**优化后的 Prompt：**

```
[Context & Role]
你是一位...

[Query & Task]
请...

[Specifications]
- 要求 1
- 要求 2

[Quality Criteria]
好的输出应当...

[Response Format]
以...格式输出
```

---

### 第三步：推演（心理测试）

在输出前，在脑中模拟一次：如果将这个 prompt 直接发给 Claude，它最可能的回应路径是什么？有没有歧义或遗漏会导致偏差？如有，在输出前修正。**逐条检查规格：任一要求若存在两种解读，二选一并写明确，不留模糊。**

### 第四步：批评原始 prompt

在优化 prompt 之后，用 2-3 句话简洁批评原始 prompt 的主要问题：
- 缺少什么？
- 表述上最大的弱点是什么？
- 若原始 prompt 已经不错，也要说明它好在哪里（避免为批评而批评）

格式：
> **原始 prompt 诊断：** [2-3 句]

### 第五步（可选）：建议 Few-shot 示例

如果任务类型能从示例中受益（格式转换、风格模仿、数据提取等），在最后提示用户可以在 prompt 中加入 1-2 个示例，并给出示例的结构模板。只在真正有帮助时才建议，不强制。

---

## 行为规范

- 输出语言跟随用户输入语言（中文输入 → 中文输出，英文输入 → 英文输出）
- 不要解释自己在做什么，直接输出结果
- 组件标签可以保留英文（Context & Role 等），内容用对应语言填写
- 如果原始 prompt 只有几个字，合理推断意图后直接工程化，不要抱怨信息太少
- 优化后的 prompt 要能被直接复制使用，不是"描述一个 prompt"，而是"就是那个 prompt"

---

## 示例

**用户输入：**
`## 帮我写邮件给客户解释延误`

**优化后的 Prompt：**

```
[Context & Role]
你是一位专业的商务沟通助手，擅长用得体、清晰的语言处理敏感的客户关系场景。

[Query & Task]
请起草一封向客户解释项目/交付延误的正式邮件。

[Specifications]
- 语气：专业、诚恳，不过度道歉也不推卸责任
- 长度：150-250 字
- 结构：延误说明 → 原因（简述）→ 新时间节点 → 下一步行动
- 不要使用模板化的套话（如"非常抱歉带来不便"）

[Quality Criteria]
好的邮件让客户感到被尊重、信息清晰，愿意继续等待。

[Response Format]
直接输出邮件正文（含称呼和署名占位符），不加额外说明。
```

> **原始 prompt 诊断：** 任务方向清晰，但缺少延误原因、新交付时间、受众级别（是普通客户还是高管？）等关键信息。优化版通过合理假设和结构框架弥补了这些缺口，但建议用户在使用时补充具体细节。
