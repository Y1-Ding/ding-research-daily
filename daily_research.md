# Daily Research - 2026-04-14 (21:30)

## 0) 今日3条结论
- Agent 工程进入“可靠性+成本可控”双主线：LangGraph/LangChain/vLLM 的近期更新都在修稳定性边界，而 OpenAI Codex 定价改动把团队试点门槛显著拉低。
- 理论上最值得深挖的是“测试时算力(TTC)+认知评估框架”：DeepMind 的 AGI 认知框架 + Lil'Log 的 TTC 文章，适合直接转成你自己的 eval rubric。
- 职业/商业上，企业 AI 已进入真实付费阶段（a16z 给出 Fortune 500 渗透数据）；但“AI 单人公司”案例仍有样本偏差，适合借鉴执行系统，不宜直接照搬增长叙事。

## 1) Agent工程化（high-signal）
- [LangGraph commit: fix populate assistant_id from config configurable instead of metadata](https://github.com/langchain-ai/langgraph/commit/2c98c59fca6c99b696988b97dfaeb885f652920c)
  - 摘要: 修复 assistant_id 来源，避免从 metadata 取值导致运行态不一致。
  - 为什么重要(对我): 这类“配置优先级”问题会直接影响多 Agent 编排的可复现性，是生产事故高发点。
  - 明日动作(<=20min): 检查你现有 Agent 配置链路，明确 `config > metadata` 优先级并补 1 条回归用例。
  - 评分: 88/100（relevance 38 + actionability 22 + credibility 18 + freshness 10）

- [LangChain release: langchain-core==1.3.0a2](https://github.com/langchain-ai/langchain/releases/tag/langchain-core%3D%3D1.3.0a2)
  - 摘要: core 预发布持续迭代，并配套缓存命中/未命中测试强化（同日 commit）。
  - 为什么重要(对我): 你的 Agent/RAG 框架如果依赖 core 抽象层，版本漂移会影响调用语义与评测稳定性。
  - 明日动作(<=20min): 建一个最小 smoke matrix（当前版本 vs 1.3.0a2）只跑关键链路 3 条。
  - 评分: 83/100（36 + 21 + 16 + 10）

- [vLLM commit: Fix mismatch between global and local attention heads in tensor-parallel mode for param2moe model](https://github.com/vllm-project/vllm/commit/f7e62e3d6618f64430262bf776079d0d89f20501)
  - 摘要: 修复张量并行下注意力头对齐错误，属于推理正确性级别 bug。
  - 为什么重要(对我): 这直接映射“Attention 原理 -> 推理系统实现”的落差，关系到线上结果可信度。
  - 明日动作(<=20min): 在你的推理评测集里新增“TP + MoE + 长上下文”一致性对照 1 组。
  - 评分: 90/100（39 + 22 + 19 + 10）

- [OpenAI: Codex now offers pay-as-you-go pricing for teams](https://openai.com/index/codex-flexible-pricing-for-teams/)
  - 摘要: Codex-only 席位改为按 token 计费、无固定 seat fee；Business 年费下调。
  - 为什么重要(对我): 你做 Agent 工程试点时，可先按工作流计费验证 ROI，再决定是否全面席位化。
  - 明日动作(<=20min): 选 1 条高频开发流（如测试生成），估算 token 成本与人工时间节省比。
  - 评分: 84/100（35 + 23 + 17 + 9）

- [OpenAI: Our response to the Axios developer tool compromise](https://openai.com/index/axios-developer-tool-compromise/)
  - 摘要: 官方披露供应链事件处置：证书轮换、旧版本下线窗口、并明确 root cause 为 Actions 流程配置不当（浮动 tag、缺少最小发布时间约束）。
  - 为什么重要(对我): 这是“Agent 工程安全基线”实战样本：依赖固定 commit、包年龄门槛、签名/发布链路隔离。
  - 明日动作(<=20min): 对你一个 CI 工作流做快速审计：把 1 个浮动 action 改为 commit pin。
  - 归属: 技术探索池（3%）
  - 评分: 92/100（37 + 24 + 20 + 11）

- [Anthropic: Project Glasswing](https://www.anthropic.com/glasswing)
  - 摘要: 联合多家基础设施厂商，用前沿模型做防御侧漏洞发现，包含公开评测与披露机制。
  - 为什么重要(对我): 提示你后续 Agent 方向可从“任务自动化”扩展到“安全自动化 + 证据化评测”。
  - 明日动作(<=20min): 写一个 1 页草案：把“代码代理”拆成发现/验证/修复建议三阶段指标。
  - 评分: 86/100（36 + 21 + 19 + 10）

- [LlamaIndex commit: Fix Breaking Change in Message Block Buffer Resolution](https://github.com/run-llama/llama_index/commit/91fe33e75ce31d3ca447c017a5ea153ed8b38700)
  - 摘要: 修复消息块缓冲解析的破坏性变更问题。
  - 为什么重要(对我): RAG/Agent 编排里消息结构变化是高频故障源，需持续盯“格式兼容性”。
  - 明日动作(<=20min): 对你现有消息 schema 增加 1 条向后兼容单测（旧字段缺失场景）。
  - 评分: 80/100（34 + 21 + 16 + 9）

## 2) 原理深化（Attention/Transformer/Agent）
- [Measuring Progress Towards AGI: A Cognitive Framework (Google DeepMind)](https://blog.google/innovation-and-ai/models-and-research/google-deepmind/measuring-agi-cognitive-framework/)
  - 摘要: 给出 AGI 进展的认知能力分解框架（而非单一总分），强调分维度评估。
  - 为什么重要(对我): 可直接用于你做 Agent eval rubric，支持至少 3 轮讨论：能力定义 -> 任务映射 -> 指标可测性。
  - 明日动作(<=20min): 从框架里挑 3 个能力维度，映射到你当前 3 个真实任务。
  - 评分: 87/100（37 + 22 + 18 + 10）

- [Gemma 4: Byte for byte, the most capable open models](https://blog.google/innovation-and-ai/technology/developers-tools/gemma-4/)
  - 摘要: 提出 E2B/E4B“有效参数”设计、长上下文与 agentic workflow 支持（含函数调用/结构化输出）。
  - 为什么重要(对我): 连接了 Transformer 架构细节与工程落地（边缘侧推理、参数效率、上下文预算）。
  - 明日动作(<=20min): 写一页“参数效率 vs 任务质量”对照模板，后续对本地模型统一评估。
  - 评分: 85/100（35 + 22 + 18 + 10）

- [Why We Think (Lil'Log)](https://lilianweng.github.io/posts/2025-05-01-thinking/)
  - 摘要: 系统回顾 test-time compute（思考时间）如何提升模型表现及相关研究问题。
  - 为什么重要(对我): 对“规划深度/推理预算/延迟成本”三者权衡有直接指导，可展开 3 轮讨论并转成实验设计。
  - 明日动作(<=20min): 为你一个任务添加“低/中/高思考预算”三档对比记录表。
  - 评分: 89/100（38 + 22 + 19 + 10）

## 3) 热门项目/可试个人项目
- [scan-for-secrets 0.3 (Simon Willison)](https://github.com/simonw/scan-for-secrets/releases/tag/0.3)
  - 摘要: 新增 `--redact`，可在扫描后进行批量脱敏替换，覆盖常见转义形态。
  - 为什么重要(对我): 对 Agent 开发日志/提示词记录场景非常实用，能减少密钥泄漏风险。
  - 明日动作(<=20min): 在本地准备一份假密钥样本目录，跑一次扫描+脱敏演练。
  - 估计复现成本: 低
  - 评分: 81/100（33 + 22 + 17 + 9）

## 4) 职业与商业探索池（AI个人公司/出海/海外工作）
- [Where Enterprises are Actually Adopting AI (a16z)](https://a16z.com/where-enterprises-are-actually-adopting-ai/)
  - 机会信号: 给出企业真实付费渗透数据（含 Fortune 500/Global 2000 维度）与高 ROI 场景（coding/support/search）。
  - 风险信号: 文章基于机构样本与私有数据，外推到你个人产品时需防止“赛道热度=个人可得性”误判。
  - 我可执行的一步(<=30min): 把你现有想法对照“可验证产出+人类兜底+采购路径”做 1 页 go/no-go 表。
  - 评分: 79/100（31 + 22 + 17 + 9）

- [Growing a fully-autonomus business to a $500k/mo in 3 months (Indie Hackers)](https://www.indiehackers.com/post/tech/growing-a-fully-autonomus-business-to-a-500k-mo-in-3-months-diZ8gkqMHm0CvEsc7Pfo)
  - 机会信号: 提供“单人+Agent 执行层”案例，强调 dogfooding 与公开构建带来的传播杠杆。
  - 风险信号: 单案例叙事、数据不可独立审计，且行业/渠道复制性不确定。
  - 我可执行的一步(<=30min): 抽取其中可复用的“监控报警先行”原则，为你项目补 3 条自动告警规则。
  - 评分: 68/100（28 + 21 + 10 + 9，career/business 放宽阈值后保留）

## 5) 连续性维护（LC / IELTS）
- LC: [OUTSIDE_WHITELIST][LeetCode Articles](https://leetcode.com/articles/) 作为每日学习入口；今天仅做 1 题 medium + 10 分钟错因复盘（关注“边界条件漏判”）。
- IELTS: [OUTSIDE_WHITELIST][Updates to IELTS test delivery in 2026 (IDP)](https://ielts.idp.com/about/news-and-articles/article-updates-to-ielts-test-delivery)；今天用 20 分钟按机考流程做 1 次听力计时模拟。

## 6) 噪音过滤记录
- 丢弃: 多个 YouTube 白名单频道页面仅返回导航骨架，未拿到当日可验证视频条目。
- 丢弃: X 白名单账号内容访问受限（403），无法完成正文级核验。
- 丢弃: [OUTSIDE_WHITELIST] Bilibili 检索结果多为二手搬运/标题党，缺少原始证据链与可执行动作。
- 丢弃: 同一事件跨平台重复（如 Axios 供应链事件在二手解读中重复出现），仅保留官方一手披露。
- 丢弃: Anthropic/OpenAI 中偏品牌/公告且缺乏你可执行动作的条目。

## 7) 本周累计计数
- Agent: 7
- Theory: 3
- Career/Business: 2
- LC: 1
- IELTS: 1
