# Daily Research - 2026-04-13 (21:30)

## 0) 今日3条结论
- Agent 工程化最强信号仍在“可验证闭环”：LangGraph 生命周期回调、Anthropic agent eval 框架、OpenAI Model Spec + Evals 都在把“能跑”升级为“可控、可测、可回归”。
- 理论侧出现可执行框架化：DeepMind 把 AGI 进展拆成 10 个认知能力 + 三阶段评测协议，可直接映射到你的训练/评测路线图。
- 职业与商业侧短期高胜率方向依旧是“窄场景+可量化 ROI”：a16z 数据显示企业付费落地集中在 coding/support/search，个人可先做“单一流程的 agent 产品”而非泛化平台。

## 1) Agent工程化（high-signal）
- [langgraph==1.1.7a1 发布：新增 graph lifecycle callback handlers](https://github.com/langchain-ai/langgraph/releases/tag/1.1.7a1)
  - 摘要: 本次发布新增图生命周期回调，外加流式测试稳定性修复与依赖安全更新。
  - 为什么重要(对我): 你做多 Agent/长流程时，生命周期钩子是做观测、重试、审计与成本归因的关键插点。
  - 明日动作(<=20min): 列出你当前 Agent 流程的 3 个关键节点（start/tool_call/end），设计一版统一 callback schema。

- [langchain-core==1.3.0a1：强化流式元数据与安全相关修复](https://github.com/langchain-ai/langchain/releases/tag/langchain-core%3D%3D1.3.0a1)
  - 摘要: 新版本包含 streaming metadata/perf 优化、模板/反序列化安全加强、token 计数与 tool schema 相关改进。
  - 为什么重要(对我): 对训练/评测前的“输入输出可解释性”和线上稳定性有直接增益，减少隐性失败与安全债务。
  - 明日动作(<=20min): 选一个现有链路，补一条“输入模板+工具参数”sanitize 检查清单。

- [Anthropic：Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
  - 摘要: 系统阐述 agent eval 的任务-试验-评分器-轨迹-结果框架，强调 deterministic grader + LLM grader + 人审校准组合。
  - 为什么重要(对我): 这正对应你的 Agent 深度主线（training/eval/planning/RAG），可直接作为内部 eval 规范骨架。
  - 明日动作(<=20min): 为你一个 agent 任务写最小评测 YAML（至少含 1 个 deterministic grader + 1 个 rubric grader）。

- [OpenAI：Inside our approach to the Model Spec](https://openai.com/index/our-approach-to-the-model-spec/)
  - 摘要: 公开“行为规范-指令层级-评测套件”的对齐方法，强调可审计、可修订、可测量的模型行为治理。
  - 为什么重要(对我): 你在做 agent 产品时，prompt policy 往往隐式；该方法可把规则显式化并持续回归测试。
  - 明日动作(<=20min): 给你的系统提示词增加“优先级链（平台>开发者>用户）”注释，并写 2 条冲突用例。

- [vLLM v0.19.0：Gemma 4、异步调度+Spec Decode、KV offloading](https://github.com/vllm-project/vllm/releases/tag/v0.19.0)
  - 摘要: 大版本强化推理吞吐与多模型兼容，含零气泡异步调度+ speculative decoding、CPU KV offloading 等。
  - 为什么重要(对我): 若你在做低成本 agent 服务，这类底层推理优化直接决定单位任务成本与并发上限。
  - 明日动作(<=20min): 记录你当前服务的 TTFT/吞吐/成本基线，作为后续引擎切换对照组。

- [run-llama/llama_index v0.14.20：多组件更新与安全修复](https://github.com/run-llama/llama_index/releases/tag/v0.14.20)
  - 摘要: 本次版本涉及大量插件与核心组件更新，包含漏洞修复与检索/工作流相关改进。
  - 为什么重要(对我): 你若走 RAG+Agent 路线，依赖面广，版本与供应链安全会成为稳定迭代的隐性瓶颈。
  - 明日动作(<=20min): 把 RAG 依赖树按“核心/可选插件”分层，先冻结核心层版本并写升级窗口规则。

## 2) 原理深化（Attention/Transformer/Agent）
- [DeepMind: Measuring progress toward AGI: A cognitive framework](https://blog.google/innovation-and-ai/models-and-research/google-deepmind/measuring-agi-cognitive-framework/)
  - 摘要: 提出 10 项认知能力（含 attention、memory、metacognition、executive functions）与三阶段人机对齐评测协议。
  - 为什么重要(对我): 你可把“理论学习”直接映射成可测能力维度，避免只看论文不落地。
  - 明日动作(<=20min): 做 3 轮讨论（>=3 turns）：第1轮定义你最弱的2项能力；第2轮设可测指标；第3轮定一周实验。

- [Gemma 4: Byte for byte, the most capable open models](https://blog.google/innovation-and-ai/technology/developers-tools/gemma-4/)
  - 摘要: 强调 agentic workflows、长上下文、多模态与函数调用能力，展示“参数效率与能力密度”的工程权衡。
  - 为什么重要(对我): 这是理解 Transformer 设计如何服务 agent 能力的现实样本（注意力/上下文/工具调用耦合）。
  - 明日动作(<=20min): 做 3 轮讨论（>=3 turns）：架构特性→对你任务的收益→是否值得本地部署试验。

- [Lil'Log Transformer tag（Transformer Family 2.0 索引）](https://lilianweng.github.io/tags/transformer/)
  - 摘要: 汇总 Transformer 架构演进与推理成本问题，适合做系统性理论回顾入口。
  - 为什么重要(对我): 你的“原理深化”可从零散阅读升级为结构化知识图谱（attention 变体→工程影响）。
  - 明日动作(<=20min): 做 3 轮讨论（>=3 turns）：选 1 个 attention 变体，写“原理-优点-风险-适用场景”四格笔记。

## 3) 热门项目/可试个人项目
- [doocs/leetcode](https://github.com/doocs/leetcode) [OUTSIDE_WHITELIST]
  - 摘要: 高星题解仓库，适合构建“LC 解题讲解 Agent”的低成本数据底座（题目-解法-多语言映射）。
  - 为什么重要(对我): 可与“学习连续性（LC）”主线联动，顺手练 agent 的检索与讲解能力。
  - 明日动作(<=20min): 抽 1 道 medium 题，做“题意重述→思路→复杂度→代码点评”四段式提示模板。
  - 估计复现成本: 低

- [vllm-project/vllm](https://github.com/vllm-project/vllm)（基于新版本做本地推理服务） 
  - 摘要: 依托 v0.19.0 可快速搭建本地/云端推理实验环境，验证 agent 任务成本曲线。
  - 为什么重要(对我): 属于“技术探索池（3%）”的高价值小实验，能为后续架构决策提供真实数据。
  - 明日动作(<=20min): 先只定义实验表头：模型、并发、TTFT、tokens/s、成本/任务。
  - 估计复现成本: 中

## 4) 职业与商业探索池（AI个人公司/出海/海外工作）
- [a16z: Where Enterprises are Actually Adopting AI](https://a16z.com/where-enterprises-are-actually-adopting-ai/)
  - 机会信号: Fortune 500 里已有显著比例进入真实付费部署，价值最集中的横向场景是 coding/support/search。
  - 风险信号: “泛平台叙事”过热，若无可量化 ROI 与行业流程深度，容易被大厂能力下沉或同质化挤压。
  - 我可执行的一步(<=30min): 写一页“单一工作流 Agent 商业假设卡”（目标角色、痛点、可量化指标、首个付费触发条件）。

- [Indie Hackers: 30天独立上线生产力SaaS复盘](https://www.indiehackers.com/post/i-shipped-a-productivity-saas-in-30-days-as-a-solo-dev-heres-what-ai-actually-changed-and-what-it-didn-t-15c8876106) [OUTSIDE_WHITELIST]
  - 机会信号: AI 显著降低“开发+设计”启动门槛，个人开发者能更快进入验证循环。
  - 风险信号: 分发与获客仍是主瓶颈；“会做不会卖”会导致项目停在技术演示阶段。
  - 我可执行的一步(<=30min): 产出 1 条英文冷启动帖（问题-方案-证据-CTA），投放到 1 个海外社区做真实反馈。

## 5) 连续性维护（LC / IELTS）
- LC: 用 20 分钟做 1 题 medium，并复盘“错因标签”（思路盲点/边界条件/实现粗心）各 1 条。
- IELTS: [官方样题页面](https://www.ielts.org/for-test-takers/sample-test-questions) [OUTSIDE_WHITELIST]，今天只做 1 篇阅读计时（15 分钟）+ 5 分钟错题归因。

## 6) 噪音过滤记录
- X 白名单账号抓取失败（403），无法获取可验证正文证据，已丢弃。
- YouTube 白名单频道主页抓取仅返回壳页面，缺少具体视频元数据与时间戳证据，已丢弃。
- 多条 B 站“2026 最强教程”标题党内容，缺少稳定的作者资质与跨源验证，已降权/丢弃。
- Anthropic “Claude code leak”相关二手解读多来自二级媒体，原始证据链不完整，已丢弃。
- OpenAI 企业新闻与 a16z 企业采用数据存在话题重叠，已按“产品侧/市场侧”做语义去重后各保留 1 条主线。

## 7) 本周累计计数
- Agent: 6
- Theory: 3
- Career/Business: 2
- LC: 1
- IELTS: 1
