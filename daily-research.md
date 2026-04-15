# Daily Research - 2026-04-15 (21:30)

## 0) 今日3条结论
- Agent 工程化正在从“功能堆叠”转向“运行时可控性+吞吐优化”：LangGraph、LangChain、vLLM 同日附近更新都在强化可观测、调度和安全边界。
- 原理深化最值得持续投入的是“测试时计算（test-time compute）+可验证评测”：Lil'Log 给了方法论全景，DeepMind 给了可执行评测框架。
- 职业杠杆依旧清晰：企业真实付费主要集中在 coding / support / search，可验证产出能力比“泛 AI 叙事”更有竞争力。

## 1) Agent工程化（high-signal）
- [LangGraph 1.1.7a2 发布](https://github.com/langchain-ai/langgraph/releases/tag/1.1.7a2)
  - 摘要: 新增“仅用于 tracing 的 metadata 透传”并修复 `assistant_id` 来源，减少配置/元数据错配；同时含依赖升级与小版本发布。
  - 为什么重要(对我): 你做多 Agent 编排时，trace 元数据与运行配置解耦能显著降低线上排障成本，尤其适合评估/回放场景。
  - 明日动作(<=20min): 用你现有 agent flow 加一组 trace-only metadata 字段，验证日志链路是否完整可检索。

- [LangChain Core 1.2.28 发布（模板清洗增强）](https://github.com/langchain-ai/langchain/releases/tag/langchain-core%3D%3D1.2.28)
  - 摘要: 该版本核心变更之一是模板 sanitization 增强（`fix(core): add more sanitization to templates`）。
  - 为什么重要(对我): 这直接关系到 prompt/template 注入面，属于你做 Agent 工程落地时的基础安全面。
  - 明日动作(<=20min): 抽 2 个历史 prompt 模板做“恶意占位符/异常变量”回归测试，记录是否被正确拦截。

- [LlamaIndex v0.14.20 发布](https://github.com/run-llama/llama_index/releases/tag/v0.14.20)
  - 摘要: 核心包更新包含 `QueryFusionRetriever._aretrieve` 异步查询生成、多集成修复、以及 `nltk` 漏洞修复等。
  - 为什么重要(对我): RAG/检索链路里异步化与依赖安全是“稳定吞吐 + 低故障率”的关键，适配你 Agent/RAG 深度主线。
  - 明日动作(<=20min): 在你的 RAG 原型里把一次检索改为 async 路径，测一次 P95 延迟差异。

- [vLLM v0.19.0 发布](https://github.com/vllm-project/vllm/releases/tag/v0.19.0)
  - 摘要: 大版本重点包括 Gemma 4 支持、zero-bubble async scheduling + speculative decoding、通用 CPU KV cache offloading、Transformers v5 兼容等。
  - 为什么重要(对我): 对 Agent 后端来说，这些能力直接影响多请求并发成本与响应时间，是“可规模化”必需项。
  - 明日动作(<=20min): 列一个你当前推理栈对照表（调度/Spec decode/KV offload），标出 1 个最容易先落地的优化点。

- [OpenAI Evals 最近提交（固定不可变版本，降低供应链漂移）](https://github.com/openai/evals/commits/main)
  - 摘要: 近期提交包括把 pre-commit 与 GitHub Actions 引用固定到不可变 commit，减少上游漂移导致的评测不稳定。
  - 为什么重要(对我): 你做训练/评测闭环时，评测环境“可复现”比单次分数更重要，这条是工程纪律信号。
  - 明日动作(<=20min): 给你自己的 eval 仓库加一条检查：CI 中禁止浮动 action 版本与浮动 pre-commit rev。

- [Simon Willison: Muse Spark 工具编排实测](https://simonwillison.net/2026/Apr/8/muse-spark/)
  - 摘要: 文中实测展示了 chat harness 下多工具协作（搜索、Python 执行、文件编辑、subagent 等）的实际行为与边界。
  - 为什么重要(对我): 这是 Agent 系统“工具协议设计/安全沙箱/子代理委派”很好的反向样本，能帮你提炼工具层设计原则。
  - 明日动作(<=20min): 按文中工具分类画一页“我自己的工具层最小协议草图”（调用、权限、审计、失败回退）。

## 2) 原理深化（Attention/Transformer/Agent）
- [Lil'Log: Why We Think](https://lilianweng.github.io/posts/2025-05-01-thinking/)
  - 摘要: 系统梳理 test-time compute、CoT、并行采样/序列修订、RL 改进推理、外部工具使用与 CoT faithful 监控。
  - 为什么重要(对我): 你在做 Agent/eval 时，能直接把“推理质量提升手段”拆成可实验变量，而非凭感觉调 prompt。
  - 明日动作(<=20min): 做 3 轮原理对话笔记（第1轮: 并行采样 vs 序列修订；第2轮: 何时引入 verifier；第3轮: 如何防 reward hacking）。

- [DeepMind: Measuring progress toward AGI（认知框架）](https://blog.google/innovation-and-ai/models-and-research/google-deepmind/measuring-agi-cognitive-framework/)
  - 摘要: 提出 10 类认知能力 taxonomy 和 3 阶段评测协议（任务评测-人类基线-相对映射），并开放 Kaggle 评测黑客松。
  - 为什么重要(对我): 你能把“Agent 是否更强”从主观体验转成结构化维度（注意、记忆、执行功能等）做长期跟踪。
  - 明日动作(<=20min): 选你当前 agent 的 1 个场景，用 10 能力里 3 个维度先写一个最小评测 rubric（每维 0-2 分）。

## 3) 热门项目/可试个人项目
- [openai/evals](https://github.com/openai/evals)
  - 摘要: 仓库近期仍在维护评测基础设施（如固定依赖引用），适合做“个人最小评测回归”骨架而非只看 leaderboard。
  - 为什么重要(对我): 你长期目标是 agent engineering depth，先有稳定 eval harness 才能形成训练/提示/工具改动的可比较闭环。
  - 估计复现成本: 低
  - 明日动作(<=20min): 建一个 5 条样本的小评测集（成功/失败各半），跑一次并保存基线结果。

## 4) 职业与商业探索池（AI个人公司/出海/海外工作）
- [a16z: Where Enterprises are Actually Adopting AI](https://a16z.com/where-enterprises-are-actually-adopting-ai/)
  - 机会信号: 文中给出“29% Fortune 500、约19% Global 2000 已为领先 AI 创业公司真实付费上线”的数据，并指出 coding/support/search 是强 ROI 区域。
  - 风险信号: 许多行业仍受监管、流程协同和可验证输出约束；“模型能力上升”不等于“企业立刻产生可计费价值”。
  - 我可执行的一步(<=30min): 把你的对外叙事改成 1 页“可验证 ROI 模板”（输入成本、自动化比例、人工兜底、可审计指标）。

## 5) 连续性维护（LC / IELTS）
- LC: [OUTSIDE_WHITELIST][LeetCode 热题100](https://leetcode.cn/studyplan/top-100-liked/)：今天只做 1 题二刷（优先你本周错题类型），并记录“错因标签”1 条。
- IELTS: [OUTSIDE_WHITELIST][IELTS One Skill Retake 官方说明](https://ielts.org/take-a-test/booking-your-test/one-skill-retake)：核对目标院校/机构是否接受 OSR，避免无效重考。

## 6) 噪音过滤记录
- 丢弃: OpenAI《The next phase of enterprise AI》；原因: 可验证但偏公司叙事/市场表达，工程可执行细节不足。
- 丢弃: 多条 Anthropic 相关新闻二手报道；原因: 非官方一手发布页，且信息在不同媒体重复、可验证性不稳定。
- 丢弃: YouTube 频道聚合页“最新视频”泛链接；原因: 页面动态渲染导致无法稳定提取具体视频证据链。
- 丢弃: openai/evals release 查询结果为空；原因: 无有效 release 实体，改用 commits 页面作为可验证信号源。

## 7) 本周累计计数
- Agent: 6
- Theory: 2
- Career/Business: 1
- LC: 1
- IELTS: 1
