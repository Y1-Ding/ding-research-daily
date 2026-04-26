# Daily Research - 2026-04-26 (21:30)

## 0) 今日3条结论
- Agent 工程主线今天最强信号集中在“可观测 trace -> eval dataset -> regression gate”：OpenAI、LangChain、LlamaIndex 都在把 agent 质量工程从 demo 推向持续评估与生产闭环。
- 多轮与长任务正在成为新评估单位：单次 response 分数不够，thread-level / trajectory / compaction / early stopping 才能覆盖真实 agent 失败模式。
- 职业与商业方向上，“agentic coworkers / enterprise orchestration / solo founder with AI coding partner”仍是高杠杆叙事，但机会更偏垂直上下文、交付可靠性和企业集成，而不是泛聊天机器人。

## 1) Agent工程化（high-signal）
- [Inside OpenAI's in-house data agent](https://openai.com/index/inside-our-in-house-data-agent/)（评分: 94）
  - 摘要: OpenAI 公开内部数据 Agent 的构建方式：离线聚合表使用、人工标注和 Codex 生成的上下文，再嵌入检索；运行时只拉取相关 schema/context，结合 SQL 执行与 Evals API 做持续回归。
  - 为什么重要(对我): 这是“企业数据 Agent”的完整工程样板：RAG 不是简单文档检索，而是把元数据、权限、golden SQL、grader 和开发期 canary eval 串成闭环。
  - 明日动作(<=20min): 画一张“我的数据/RAG Agent eval loop”草图：输入、检索上下文、golden case、SQL/工具结果比较、失败样本回流。

- [How we build evals for Deep Agents](https://blog.langchain.com/how-we-build-evals-for-deep-agents/)（评分: 93）
  - 摘要: LangChain 说明 Deep Agents 的 eval 方法：先定义生产中关心的行为，再为每个 eval 写 docstring、打 capability 标签，并从 trace 中复盘失败模式；指标包含 correctness、step ratio、tool call ratio、latency ratio、solve rate。
  - 为什么重要(对我): 这比“跑 benchmark 看总分”更贴近 Agent 工程能力建设，特别适合你长期跟踪 training/eval/planning/RAG。
  - 明日动作(<=20min): 为自己常用 agent 写 5 个 capability 标签：retrieval、tool_use、file_operations、memory、conversation，并各列 1 个失败样本。

- [Agent Evaluation Readiness Checklist](https://blog.langchain.com/agent-evaluation-readiness-checklist/)（评分: 91）
  - 摘要: 清单强调先人工看真实 traces，再选择 run/trace/thread 三个评估层级；从 trace-level 起步，再补 run-level 调试和 thread-level 多轮评价，并把高通过率 eval 纳入 CI/CD gate。
  - 为什么重要(对我): 这是把 Agent eval 从“想法”变成“上线前检查表”的模板，尤其适合构建你自己的 agent 产品质量标准。
  - 明日动作(<=20min): 把 checklist 改成 8 项中文短清单，贴到你的 agent 项目 README 或任务模板里。

- [Get Started with LangSmith Multi-turn Evaluations](https://www.youtube.com/watch?v=sC0KhJHJTP0)（评分: 88）
  - 摘要: LangChain 视频演示 multi-turn eval：以 thread 为单位评估 semantic intent、semantic outcome、agent trajectory，并可过滤至少 2 轮以上对话来关注真正有信息量的用户交互。
  - 为什么重要(对我): 你明确要求 theory “talk >= 3 turns”，工程上也应把 3+ 轮对话作为 agent 能力测试的最低颗粒度之一。
  - 明日动作(<=20min): 设计 3 个三轮测试脚本：澄清需求、调用工具、纠错/复盘，作为未来 agent regression set。

- [LlamaIndex releases: evaluation and agent workflow updates](https://github.com/run-llama/llama_index/releases)（评分: 84）
  - 摘要: LlamaIndex 近期 release 包含 evaluation-tonic-validate、agent-agentmesh、agentops/honeyhive callbacks、evaluator-benchmarker，以及 agent workflow 依赖更新等。
  - 为什么重要(对我): LlamaIndex 正把 RAG/Document Agent、AgentOps、Evaluator Pack 拼到同一生态里，适合作为 RAG Agent 实验基线。
  - 明日动作(<=20min): 记录一个最小 LlamaIndex eval 实验：1 个 query engine、3 条 labelled cases、faithfulness/relevancy 两个指标。

- [LlamaIndex PR: early_stopping_method for agent workflows](https://github.com/run-llama/llama_index/pull/20389)（评分: 82）
  - 摘要: PR 为 AgentWorkflow/BaseWorkflowAgent 增加 `early_stopping_method`，到达 max iterations 时可选择强制报错或再调用一次 LLM 汇总已获得信息。
  - 为什么重要(对我): 这触及长任务 agent 的“失败退化策略”：不是无限 loop，也不是粗暴崩溃，而是把部分进展转成可交付中间结果。
  - 明日动作(<=20min): 给自己的 agent runbook 加一句规则：超过 N 步时输出“已完成/缺失/下一步”，而不是继续盲跑。

- [microsoft/autogen: AutoGen Maintenance Mode](https://github.com/microsoft/autogen)（评分: 80）
  - 摘要: AutoGen README 显示项目进入 maintenance mode，不再接收新功能，建议新用户转向 Microsoft Agent Framework，强调 MAF 1.0 的企业级多 agent 编排、A2A/MCP 和长期支持。
  - 为什么重要(对我): Agent 框架选择要看维护状态；AutoGen 仍有研究价值，但新项目应谨慎依赖其未来路线。
  - 明日动作(<=20min): 在你的 agent 框架对比表里加一列“维护/迁移风险”，把 AutoGen 标为 legacy/research。

## 2) 原理深化（Attention/Transformer/Agent）
- [Why We Think | Lil'Log](https://lilianweng.github.io/posts/2025-05-01-thinking)（评分: 92）
  - 摘要: Lilian Weng 系统梳理 test-time compute、adaptive computation、recurrent Transformer、pause/thought tokens 等“为什么模型思考更久会变强”的机制。
  - 为什么重要(对我): 它把 Attention/Transformer 原理和 Agent 的长任务推理连接起来：planning 不只是 prompt 技巧，也和推理预算、循环结构、训练信号有关。
  - 明日动作(<=20min): 只读“adaptive test-time compute”相关小节，写 5 行笔记：哪些方法增加深度，哪些增加采样/搜索宽度。

- [LLM Powered Autonomous Agents | Lil'Log](https://lilianweng.github.io/posts/2023-06-23-agent/)（评分: 88）
  - 摘要: 经典 Agent 原理综述：planning、memory、tool use、reflection，并明确指出长上下文、任务分解和计划调整仍是主要瓶颈。
  - 为什么重要(对我): 这是你 Agent principles 的长期基准文；今天的 LangGraph/LangSmith/Agents SDK 变化都可以映射回这几个组件。
  - 明日动作(<=20min): 用 3 轮对话案例标注：第 1 轮 planning，第 2 轮 tool use，第 3 轮 reflection/repair。

- [Beyond A*: Better Planning with Transformers via Search Dynamics Bootstrapping (Searchformer)](https://www.youtube.com/watch?v=PW4JiJ-WaY4)（评分: 77）
  - 摘要: Yannic Kilcher 解读 Searchformer：训练 Transformer 预测 A* 搜索动态，再通过 expert iteration 减少搜索步数，在 Sokoban 等规划任务上提升泛化与效率。
  - 为什么重要(对我): 它给“LLM planning”一个更形式化的视角：不要只生成最终计划，也可以学习搜索过程本身。
  - 明日动作(<=20min): 写一个类比：ReAct trace 是否可以被当作“搜索动态序列”来训练或评估？

## 3) 热门项目/可试个人项目
- [LangGraph releases: typed streaming / invoke and subgraph resume fixes](https://github.com/langchain-ai/langgraph/releases)（评分: 84）
  - 摘要: LangGraph 1.1 系列引入 v2 typed stream/invoke；近期 1.1.9 修复 plain resume 时不应向 subgraph 传播 ReplayState 等问题。
  - 为什么重要(对我): typed stream、interrupt、subgraph resume 都是构建可靠 agent UI/后台任务的基础设施，不是表层 demo。
  - 明日动作(<=20min): 读 release 中 v2 stream 的字段：`type`、`ns`、`data`、`interrupts`，想一个前端事件日志展示方式。
  - 估计复现成本: 低

- [OUTSIDE_WHITELIST] [Wirasm/PRPs-agentic-eng](https://github.com/Wirasm/PRPs-agentic-eng)（评分: 78）
  - 摘要: PRP 方法把 PRD、代码库上下文、实现 runbook、验证命令组合成 AI coding agent 可执行的“交付包”，并包含 `/prp-plan`、`/prp-implement`、`/prp-debug` 等命令流。
  - 为什么重要(对我): 这是低成本技术探索：把 agent 工程中的 context engineering 与 validation loop 固化为模板，能直接提升 solo 开发效率。
  - 明日动作(<=20min): 复制其 PRP 核心字段，给一个小功能写 1 页 PRP：Goal / Context / Blueprint / Validation。
  - 估计复现成本: 低

## 4) 职业与商业探索池（AI个人公司/出海/海外工作）
- [The Rise of Computer Use and Agentic Coworkers | a16z](https://a16z.com/the-rise-of-computer-use-and-agentic-coworkers)（评分: 86）
  - 机会信号: a16z 认为 computer-use agents 可自动化无 API、长尾和遗留软件流程；创业机会在垂直化、企业上下文化、部署与调优。
  - 风险信号: 当前 agent 仍慢、贵，且复杂企业软件 out-of-box 不可靠；需要大量 domain/company-specific context。
  - 我可执行的一步(<=30min): 选一个垂直流程（如招聘筛选、客服质检、财务对账），列出“无 API 但人每天点 UI”的 5 个步骤。

- [I built an enterprise AI chatbot platform solo -- 6 microservices, 7 channels, and Claude Code as my co-developer](https://www.indiehackers.com/post/i-built-an-enterprise-ai-chatbot-platform-solo-6-microservices-7-channels-and-claude-code-as-my-co-developer-5bafd24c20)（评分: 74）
  - 机会信号: Solo founder 借助 Claude Code 搭出 6 microservices、7 channels、RAG、tracing、eval service，说明 AI coding partner 正在放大个人交付复杂系统的能力。
  - 风险信号: 作者也承认 hardest part 是业务、获客、内容和支持的上下文切换；技术复杂度不是唯一瓶颈。
  - 我可执行的一步(<=30min): 写一个“我能 solo 交付的企业 AI 微产品”清单，要求每个 idea 都包含 tracing/eval/成本可视化。

## 5) 连续性维护（LC / IELTS）
- [OUTSIDE_WHITELIST] LC: [Spaced Repetition for LeetCode - LeetSRS](https://leetsrs.com/blog/spaced-repetition-for-leetcode/)（评分: 76）
  - 今日只做一件事: 按一个 pattern 选 2 题，空白编辑器重做；每题标记 Again/Hard/Good/Easy，复习优先于新题。

- [OUTSIDE_WHITELIST] IELTS: [IELTS official update: paper-based test phased out from mid-2026](https://ielts.org/news-and-insights/updates-to-ielts-test-delivery)（评分: 82）
  - 今日只做一件事: 明天练 10 分钟 computer-delivered Writing：打字写 Task 2 开头段，并记录字数与修改次数。

## 6) 噪音过滤记录
- X 白名单搜索结果：未获得足够直接、可核验的单条原始推文证据；为避免二手摘要和不可访问页面，未纳入主文。
- Bilibili LangGraph/MCP/RAG 搜索结果：标题多为“最全/少走99%弯路/涨薪”等强营销表达，缺少明确作者可信度与可验证技术细节；未纳入主文。
- OpenAI AgentKit / Agents SDK 多篇发布：与 OpenAI data agent、LangChain eval 信号语义重叠，今天只保留更贴近 eval/RAG 闭环的条目。
- GitHub trending 中多个 agent skill/插件库：除 PRP 方法外，多数更像资源集合或营销页，暂不作为高信号工程结论。
- 非白名单使用计数: 3/15 = 20%，均已标注 `[OUTSIDE_WHITELIST]`。

## 7) 本周累计计数
- Agent: 7
- Theory: 3
- Career/Business: 2
- LC: 1
- IELTS: 1
