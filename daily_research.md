# Daily Research - 2026-04-09 (21:30)

## 0) 今日3条结论
- 评测正在成为 Agent 工程的第一优先级：Anthropic 的 agent eval 方法论与 OpenAI 的 Model Spec Evals / IH-Challenge 形成“规范-数据集-评测闭环”，可直接迁移到你的训练/评测流水线。
- 推理与执行基础设施同步升级：vLLM v0.19.0（异步调度+spec decode+Gemma4 支持）和 LangGraph CLI `validate` 能明显降低线上不稳定与配置回归风险。
- 职业与商业上，企业 AI 已从试点转向规模化合同（a16z 数据），同时 OpenAI Safety Fellowship 提供了“安全评测/对齐”方向的高杠杆入口。
- 配比执行: Agent 60% / Theory 25% / 项目 5% / LC 2.5% / IELTS 2.5% / 技术探索池 3% / 职业商业池 2%（今日按该权重筛选与编排；非白名单占比控制在 20% 以内）。

## 1) Agent工程化（high-signal）
- [Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
  - 摘要: Anthropic 系统化给出 agent eval 结构（task/trial/grader/transcript/outcome/harness），并明确 code-based + model-based + human 三类 grader 组合、capability vs regression 双套评测、pass@k 与 pass^k 的使用场景。
  - 为什么重要(对我): 这是一套可直接复用的“评测架构蓝图”，能把你当前 agent 开发从“改完再试”升级为“可回归、可比较、可迭代”。
  - 评分: 94/100
  - 明日动作(<=20min): 为你当前一个 agent 场景写 5 个任务样例（含 outcome check + transcript 指标），先跑最小 regression 套件。

- [Improving instruction hierarchy in frontier LLMs (IH-Challenge)](https://openai.com/index/instruction-hierarchy-challenge/)
  - 摘要: OpenAI 发布 IH-Challenge（开源数据集）强化 System > Developer > User > Tool 的指令优先级；在注入鲁棒性和安全可控性评测上有显著提升。
  - 为什么重要(对我): 你做多工具/多轮代理时，提示注入和“低优先级覆写高优先级”是高频风险；该范式可直接指导你的对抗训练与评测设计。
  - 评分: 92/100
  - 明日动作(<=20min): 抽 3 条你现有 prompt 链路，人工构造 tool 注入样本，先做一版“层级冲突单测”。

- [Introducing Model Spec Evals](https://alignment.openai.com/model-spec-evals/)
  - 摘要: OpenAI 公布 596 条场景化规范评测集与开源评测代码，用于衡量模型对行为规范（指令层级、安全边界、风格/诚实性等）的遵循度。
  - 为什么重要(对我): 你可以把“主观好坏”变成可度量的规范遵循率，尤其适合 Agent 上线前的质量门禁。
  - 评分: 90/100
  - 明日动作(<=20min): 先借鉴其 rubric 结构，给你自己的 10 条核心策略写二值化通过标准。

- [vLLM v0.19.0](https://github.com/vllm-project/vllm/releases/tag/v0.19.0)
  - 摘要: 本版包含 448 commits，重点是 async scheduling + speculative decoding、Gemma 4 支持、KV cache offloading、Transformers v5 兼容等。
  - 为什么重要(对我): 对你做本地/私有部署 Agent 推理栈时，吞吐与延迟优化会直接决定多 Agent 并发可行性和成本边界。
  - 评分: 88/100
  - 明日动作(<=20min): 记录你当前 serving 栈是否支持 speculative decoding 与 KV offload，列出 1 个可验证的压测指标。

- [langgraph-cli==0.4.21（新增 validate 命令）](https://github.com/langchain-ai/langgraph/releases/tag/cli%3D%3D0.4.21)
  - 摘要: LangGraph CLI 新增 `validate`，用于图/配置层面的快速校验。
  - 为什么重要(对我): 对多节点 workflow 的回归防护非常实用，能把运行时错误前移到构建阶段。
  - 评分: 84/100
  - 明日动作(<=20min): 在你的 graph 配置里加入一次 validate 预检，作为本地提交前固定步骤。

## 2) 原理深化（Attention/Transformer/Agent）
- [Measuring progress toward AGI: A cognitive framework](https://blog.google/innovation-and-ai/models-and-research/google-deepmind/measuring-agi-cognitive-framework/)
  - 摘要: DeepMind 给出 10 认知能力 taxonomy（含注意力、记忆、执行功能、社会认知等）与“任务评测-人类基线-认知画像”三阶段协议，并配套 Kaggle 评测黑客松。
  - 为什么重要(对我): 这是把“通用智能讨论”映射到可评测维度的框架，可用于你自己的 agent 能力雷达图与短板定位。
  - 评分: 89/100
  - 明日动作(<=20min): 用该 taxonomy 给你当前系统做一个 10 维自评（高/中/低），挑 1 个最低维度补实验。

- [Reasoning models struggle to control their chains of thought, and that’s good](https://openai.com/index/reasoning-models-chain-of-thought-controllability/)
  - 摘要: OpenAI 提出 CoT-Control，观察到前沿模型 CoT 可控性整体偏低；在当前阶段这对监控可解释性是利好信号。
  - 为什么重要(对我): 对 Agent 监督来说，你可以把“推理轨迹可监控”作为防线之一，但要持续跟踪其随模型升级的变化。
  - 评分: 86/100
  - 明日动作(<=20min): 给你现有 agent 日志增加 1 条“异常推理模式”规则（例如越权目标/绕过策略词）。

- [Inside our approach to the Model Spec](https://openai.com/index/our-approach-to-the-model-spec/)
  - 摘要: 明确了行为规范设计的“公开原则 + 指令链 + 解释性样例 + 可迭代评测”方法，强调 Spec 是可验证的行为接口而非仅口号。
  - 为什么重要(对我): 你在做 Agent 规则系统时，可直接采用“高优先级硬规则 + 可覆盖默认策略 + 评测回路”的结构化设计。
  - 评分: 87/100
  - 明日动作(<=20min): 将你当前 1 个模糊策略改写为“层级规则 + 可测试示例”格式。
  - 三轮深聊(>=3 turns): 
    1) 我当前规则里，哪些属于不可覆盖“硬规则”？  
    2) 哪些默认策略应允许用户显式覆盖？  
    3) 如何把每条规则转成可自动回归的测试样例？

## 3) 热门项目/可试个人项目
- [scan-for-secrets 0.3](https://github.com/simonw/scan-for-secrets/releases/tag/0.3)
  - 摘要: 新增 `--redact`，可在扫描后批量替换泄露密钥并处理常见转义形式。
  - 为什么重要(对我): 适合你在发布 agent 日志/对话记录前做自动脱敏，降低 API key 泄露风险。
  - 评分: 79/100
  - 明日动作(<=20min): 用一个测试目录跑一次 secrets 扫描+脱敏演练，形成固定发布前检查。
  - 估计复现成本: 低

- [research-llm-apis 2026-04-04](https://github.com/simonw/research-llm-apis/releases/tag/2026-04-04)
  - 摘要: 汇总多家 LLM 厂商的流式/非流式 JSON 响应样本，用于设计更稳健的抽象层。
  - 为什么重要(对我): 你做多模型/多供应商 Agent 编排时，可减少协议差异导致的工具调用故障。
  - 评分: 76/100
  - 明日动作(<=20min): 对照你当前 SDK 抽象，列出 3 个跨供应商不一致字段并补兼容层。
  - 估计复现成本: 中

## 4) 职业与商业探索池（AI个人公司/出海/海外工作）
- [Where Enterprises are Actually Adopting AI](https://a16z.com/where-enterprises-are-actually-adopting-ai/)
  - 机会信号: 文中给出“Fortune 500 中约 29% 已为头部 AI 创企付费并上线”的量化信号，且 coding/support/search 是最强 ROI 赛道。
  - 风险信号: 行业采用分化显著；可验证输出、低合规阻力、短闭环任务更容易落地，泛化到高监管行业并不线性。
  - 评分: 82/100
  - 我可执行的一步(<=30min): 用“可验证输出+短闭环+明确 ROI”三条件，重排你现有 3 个产品想法优先级。

- [Introducing the OpenAI Safety Fellowship](https://openai.com/index/introducing-openai-safety-fellowship/)
  - 机会信号: 面向外部研究者开放安全/对齐研究项目（含导师、资源、产出要求），是进入 AI safety 赛道的高质量入口。
  - 风险信号: 申请竞争强，且要求可交付研究产出（paper/benchmark/dataset），对执行与研究方法要求高。
  - 评分: 75/100
  - 我可执行的一步(<=30min): 先写 1 页研究提案草稿（问题定义、评测方案、可交付），用于后续投递或自建项目。

## 5) 连续性维护（LC / IELTS）
- LC: [OUTSIDE_WHITELIST] [LeetCode Top Interview 150](https://leetcode.com/studyplan/top-interview-150/)——今天只做 1 题数组/双指针并写 5 行复盘（错因+模板）。
- IELTS: [OUTSIDE_WHITELIST] [IELTS Preparation Resources](https://ielts.org/take-a-test/preparation-resources)——完成 1 个官方写作评分讲解视频并记录 3 个可套用句式。

## 6) 噪音过滤记录
- YouTube 白名单频道抓取结果页面结构缺失（无法稳定提取“今日新视频”与可验证摘要）→ 暂不纳入主清单。
- X 白名单账号（如 @simonw）页面访问受限（403）→ 仅保留可从其博客/仓库反查的可验证内容。
- OpenAI News 中偏公司经营/融资类条目（如并购、融资新闻）对你当前“工程深度/原理深度”动作性不足，评分<70，已过滤。
- 重复语义内容去重：Model Spec 方法论文章与其评测发布内容保留“方法+数据集/评测工具”两条，删除重复解读源。

## 7) 本周累计计数
- Agent: 5
- Theory: 3
- Career/Business: 2
- LC: 1
- IELTS: 1
- 技术探索池: 1
- 配比核对(按权重): 已执行（Agent/Theory 合计覆盖主报告高信号主体；Outside Whitelist 条目仅用于连续性维护）

