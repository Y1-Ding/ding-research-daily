# Daily Research - 2026-04-25 (21:30)

## 0) 今日3条结论
- Agent 可靠性正在从“模型能力”转向“整条 harness + 传输 + 上下文管理”的系统工程：OpenAI 用 WebSocket 降低 agent loop 开销，Anthropic 的 Claude Code 复盘则说明一次 effort 默认值、缓存清理或系统提示改动都可能造成真实质量回退。
- RAG/数据 Agent 的关键不只是检索，而是可维护的 context layer：业务定义、实体映射、权限、历史决策与人类修正需要被持续写回上下文。
- 连续学习今天只保留两个微动作：LC 用间隔复习优先于刷题数量；IELTS 需要切换到机考/One Skill Retake 的备考假设。

## 1) Agent工程化（high-signal）
- [Speeding up agentic workflows with WebSockets in the Responses API](https://openai.com/index/speeding-up-agentic-workflows-with-websockets/)
  - 摘要: OpenAI 解释 Codex/Responses API 的 agent loop 为什么在推理变快后被 API 往返、重复验证、重复 token rendering 拖慢；WebSocket 模式通过持久连接、`previous_response_id`、连接级内存缓存复用 prior input/output、工具定义、采样 artifacts，使 alpha 用户的 agentic workflow 最高提速约 40%。
  - 为什么重要(对我): 做 agent 工程时，性能瓶颈不只在模型 TPS；多轮工具调用会放大状态重建、网络往返、安全校验和计费路径的开销。我的 RAG/规划 agent 若每轮全量重传上下文，也会遇到同类问题。
  - 明日动作(<=20min): 画一张自己当前 agent loop 的“每轮重复工作”清单，标出哪些状态可以缓存或增量传递。

- [An update on recent Claude Code quality reports](https://www.anthropic.com/engineering/april-23-postmortem)
  - 摘要: Anthropic 将 Claude Code 近期质量问题归因于 3 个产品层改动：默认 reasoning effort 从 high 降到 medium、空闲会话清理 thinking 的 bug 导致后续每轮持续丢失旧 reasoning、系统提示中过强的简短输出限制损害 coding 质量；API/inference layer 未受影响，相关问题已在 4 月 20 日前修复。
  - 为什么重要(对我): 这是典型 agent harness 事故案例：用户感知的“模型变笨”可能来自 effort、上下文裁剪、prompt policy、缓存行为，而不是底层模型。评测必须覆盖长会话、空闲恢复、多轮工具调用和系统提示 ablation。
  - 明日动作(<=20min): 给自己的 agent eval checklist 增加 3 项：空闲后恢复、reasoning/plan 保留、系统提示单行 ablation。

- [LangChain core 1.3.2 release: content-block-centric streaming v2](https://github.com/langchain-ai/langchain/releases/tag/langchain-core%3D%3D1.3.2)
  - 摘要: `langchain-core==1.3.2` 发布，核心变更是加入 content-block-centric streaming v2。
  - 为什么重要(对我): Agent 前端/日志/评测越来越依赖结构化流式输出，而不是单纯 token stream；content block 级流式有利于区分文本、工具调用、推理块和多模态内容，降低观测与回放复杂度。
  - 明日动作(<=20min): 阅读该 PR/release 链接，记录 content block streaming 与当前 token streaming 的接口差异。

- [LangGraph 1.1.9 release](https://github.com/langchain-ai/langgraph/releases/tag/1.1.9)
  - 摘要: LangGraph 1.1.9 修复了 plain resume 时不应向 subgraph 传播 `ReplayState` 的问题，并回退了 stream handler 中不必要的改动。
  - 为什么重要(对我): 多图/子图 agent 的 resume 语义是长任务可靠性的底层问题；错误传播 replay state 可能造成重复执行、状态污染或难复现的恢复 bug。
  - 明日动作(<=20min): 用一个 parent graph + subgraph 的最小例子，写下“resume 时哪些状态应该传播/不传播”的预期。

- [LlamaIndex v0.14.21 release](https://github.com/run-llama/llama_index/releases/tag/v0.14.21)
  - 摘要: 该版本包含多个 RAG/索引稳定性修复：`DocumentSummaryIndex.delete_nodes` 对无效 node ID 防 `KeyError`、structured output 失败时处理 `ValueError`/`TypeError`、持久化层显式 UTF-8 encoding、message block buffer resolution breaking change 修复等。
  - 为什么重要(对我): RAG 系统的失败常出在边界条件：坏 node id、结构化输出失败、编码、消息块解析。生产化 eval 不能只测 happy path。
  - 明日动作(<=20min): 为自己的 RAG demo 增加 2 个负例：无效文档节点删除、structured output 返回非法 JSON/类型。

- [vLLM v0.19.1 release](https://github.com/vllm-project/vllm/releases/tag/v0.19.1)
  - 摘要: vLLM 0.19.1 是 0.19.0 的 patch release，升级 Transformers v5.5.3，并集中修复 Gemma4 streaming tool calls 的 JSON/HTML/布尔与数字拆分 corruption、裸 `null` 解析、动态 BOS 注入、Gemma4 LoRA 和量化 MoE 等问题。
  - 为什么重要(对我): 工具调用 agent 的“看似模型输出错”也可能来自 serving/parser 层；流式 tool call JSON 被拆坏会直接破坏规划执行链。
  - 明日动作(<=20min): 记录一条 serving 侧验收用例：流式输出中跨 chunk 的 bool/number/null/tool JSON 必须可恢复。

## 2) 原理深化（Attention/Transformer/Agent）
- [Introducing GPT-5.5](https://openai.com/index/introducing-gpt-5-5/)
  - 摘要: OpenAI 发布 GPT-5.5，强调 agentic coding、computer use、知识工作和科学研究；官方给出 Terminal-Bench 2.0、SWE-Bench Pro、OSWorld-Verified、Toolathlon、Graphwalks 等多维 eval，并强调更强模型还可用更少 token 完成 Codex 任务。
  - 为什么重要(对我): 这提供了一个观察 agent 原理的框架：长上下文图遍历、工具使用、终端任务、计算机操作、token efficiency 应被拆成不同能力轴，而不是只看单一 benchmark。
  - 明日动作(<=20min): 做一次 3 轮追问笔记：1) Terminal-Bench 测的是什么规划能力；2) Graphwalks 测的是什么长上下文能力；3) 两者如何对应到我的 agent eval。

- [GPT-5.5 System Card](https://openai.com/index/gpt-5-5-system-card/)
  - 摘要: System Card 说明 GPT-5.5 面向复杂真实工作，强调更早理解任务、减少用户指导、更有效工具使用、检查并持续推进；同时列出预部署安全评估、Preparedness Framework、网络安全/生物能力 red-teaming 和 API 部署额外 safeguards。
  - 为什么重要(对我): Agent 原理不能只看能力，也要看约束：一旦模型能长期行动，安全分类器、权限、误用检测、可信访问会成为系统设计的一部分。
  - 明日动作(<=20min): 写 5 条“高权限 agent 必须先问/必须拒绝/必须记录”的本地 policy 草案。

- [Introducing Claude Opus 4.7](https://www.anthropic.com/news/claude-opus-4-7)
  - 摘要: Anthropic 发布 Opus 4.7，强调长任务、instruction following、验证自身输出、高分辨率视觉、文件系统 memory、`xhigh` effort、task budgets 与 tokenizer 变化；官方建议迁移时重新测真实流量 token 使用与 prompt/harness。
  - 为什么重要(对我): 这是 test-time compute 与 agent 控制面的实践样本：effort level、task budget、memory、prompt 字面遵循都会改变 agent 行为。
  - 明日动作(<=20min): 用 3 轮对话复盘一个问题：当模型更严格遵循指令时，旧 prompt 中哪些“随手写的限制”会变成质量风险。

## 3) 热门项目/可试个人项目
- [Extract PDF text in your browser with LiteParse for the web](https://simonwillison.net/2026/Apr/23/liteparse-for-the-web/)
  - 摘要: Simon Willison 基于 LlamaIndex 的开源项目 [LiteParse](https://github.com/run-llama/liteparse) 做了浏览器内 PDF 文本抽取实验，复用 Node 版依赖思路，让 PDF 解析尽量在本地浏览器完成。
  - 为什么重要(对我): 这是低成本 RAG side project 入口：本地 PDF 抽取 + chunking + 小型检索 UI，可作为“隐私友好个人知识库”的最小原型。
  - 明日动作(<=20min): clone LiteParse，找 1 份英文 PDF 跑通抽取，并记录是否保留段落/页码。
  - 估计复现成本: 低

## 4) 职业与商业探索池（AI个人公司/出海/海外工作）
- [Your Data Agents Need Context](https://a16z.com/your-data-agents-need-context)
  - 机会信号: a16z 将 data agents 的核心瓶颈定义为“context layer”：业务定义、canonical entities、identity resolution、治理规则、tribal knowledge、自更新反馈流。对个人公司而言，可切入垂直行业的轻量 context layer / RAG 质量治理工具，而不是泛泛做 chat-with-data。
  - 风险信号: 文章明确说“proper data agent is no small feat”，难点同时涉及数据基础设施和人类组织知识；没有真实客户数据/工作流，很容易做成 demo 而非产品。
  - 我可执行的一步(<=30min): 选一个熟悉领域，写出 10 条“数据 agent 必须知道但数据库里没有”的业务规则，评估它们是否能形成微型 context product。

## 5) 连续性维护（LC / IELTS）
- [OUTSIDE_WHITELIST] LC: [Spaced Repetition for LeetCode](https://leetsrs.com/blog/spaced-repetition-for-leetcode/)
  - 今日只执行一个动作: 明天先复习旧题再做新题；用“空白编辑器 10-15 分钟重写”判断是否真会，不看旧代码。
- [OUTSIDE_WHITELIST] IELTS: [Updates to IELTS test delivery](https://ielts.org/news-and-insights/updates-to-ielts-test-delivery)
  - 今日只执行一个动作: 备考假设切到 computer-delivered IELTS；写作仍练键盘输入，同时关注本地是否有 Writing on Paper 选项。

## 6) 噪音过滤记录
- `microsoft/autogen` 最新 GitHub release 为 2025-09-30：可信但 freshness 低，今天不进主区。
- LlamaIndex v0.14.21 中大量包仅为 dependency bump：缺少直接行动价值，只保留 core/RAG 稳定性相关修复。
- OpenAI ChatGPT Images 2.0：新鲜但与四条长期主线映射较弱，今天不纳入。
- Anthropic NEC/compute/election safeguards 新闻：有链接但更偏公司公告或政策更新，工程可执行性不足。
- 广泛搜索与非白名单社媒：白名单来源已超过 8 个高信号条目，按 whitelist-first policy 不扩展噪音搜索。

## 7) 本周累计计数
- Agent: 6
- Theory: 3
- Career/Business: 1
- LC: 1
- IELTS: 1
