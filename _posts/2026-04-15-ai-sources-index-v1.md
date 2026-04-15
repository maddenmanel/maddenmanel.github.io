---
title: "AI 应用层（RAG / Agent / LLM App）优质信源索引 v1"
toc: true
excerpt_separator: "<!--more-->"
categories:
  - Blog
tags:
  - AI
  - RAG
  - Agent
  - LLM
  - 信源
  - 知识管理
---

> **用途**：系统性学习 + 行业洞察 + 个人创作语料库
> **覆盖**：国际 & 国内，博客 / 论文 / 社区 / 播客 / 视频 / 投资机构
> **维护节奏**：每日自动摘要（配套 scheduled task）
> **版本**：2026-04-14

这份索引花了相当长时间整理，把 AI 应用层（RAG / Agent / LLM App）值得持续跟踪的信源按信号价值分成七类，从一手工程博客到年度权威报告，从国际生态到中文圈，以及每个信源的核心立场和反常识观点。直接当目录用。

<!--more-->

## 一、分类框架

按 **"信号价值 × 立场光谱"** 分成七类，便于按权重取用：

| 类别 | 作用 | 典型更新频率 | 信号噪声比 |
|---|---|---|---|
| A. 一手工程博客（个人大V） | 看工程真经验、反套路 | 周更 | 极高 |
| B. 厂商官方 & 研究实验室 | 看 SOTA、新能力发布 | 周更 | 高（但有营销滤镜） |
| C. 论文 & 综述平台 | 看学术前沿 | 日更 | 中（需筛选） |
| D. 社区 / 论坛 | 看草根实践、真实踩坑 | 实时 | 低但偶有宝藏 |
| E. 投资机构 & 行业研究 | 看商业化趋势 | 月更 | 高（叙事为主） |
| F. 中文生态 | 看本土落地、政策、ToB 实践 | 日更 | 中 |
| G. 年度报告 & 榜单 & 权威数据源 | 写作引用的硬通货 | 年/季更 | 极高 |

---

## 二、A 类：一手工程博客（核心必订）

这类是语料库的**压舱石**。这些人写的内容几乎全部是"做过才能写出来"的，引用率极高。

### A1. Hamel Husain — https://hamel.dev/
- **身份**：前 Airbnb / GitHub ML，现独立顾问，教 AI 评估（Evals）课程（与 Shreya Shankar 合开）。
- **核心观点**：
  - "**Your AI Product Needs Evals**"——LLM 应用失败 90% 是因为没写 eval，而不是模型不够强。
  - 反对盲目追 RAG 复杂度，主张 **"先看数据、再选方案"**（data-first debugging）。
  - Fine-tune vs RAG 不是对立，而是 **eval → 发现 gap → 选手段**。
- **必读**：Your AI product needs evals、Fuck you show me the prompt、A Field Guide to Rapidly Improving AI Products。

### A2. Simon Willison — https://simonwillison.net/
- **身份**：Django 联合创始人，LLM CLI 作者，每天写。
- **核心观点**：
  - **Prompt Injection 永远无法完全解决**——这是 LLM 架构级问题，而非 patch 能修的 bug。
  - 推崇"**LLM as CLI tool**"——工程师应该把 LLM 当 Unix 管道用。
  - 对 Agent 的安全性态度非常谨慎，提出 "**the lethal trifecta**"（私有数据访问 + 外部内容 + 外发能力）。
- **必读**：标签页 llms、prompt-injection、agents。RSS: /atom.xml。

### A3. Lilian Weng — https://lilianweng.github.io/
- **身份**：前 OpenAI Safety 负责人，现 Thinking Machines。
- **核心观点**：综述式写作，几乎定义了业界对 Agent 的标准认知（planning + memory + tool use）。
- **必读**：LLM Powered Autonomous Agents、Prompt Engineering、What are Diffusion Models、Extrinsic Hallucinations in LLMs。
- **特点**：更新慢（季度级）但每篇都是教科书级。

### A4. Chip Huyen — https://huyenchip.com/
- **身份**：《Designing Machine Learning Systems》《AI Engineering》作者。
- **核心观点**：
  - AI 工程 ≠ ML 工程，重点在 **系统、数据闭环、评测**。
  - 警惕"Agent 炒作"，主张先做 **workflow**（确定性流程）再谈 Agent。
- **必读**：Building LLM applications for production、RLHF、Agents。

### A5. Eugene Yan — https://eugeneyan.com/
- **身份**：Amazon 应用科学家。
- **核心观点**：
  - **"Patterns for Building LLM-based Systems"**——把 LLM 应用模式化（Evals, RAG, Fine-tuning, Caching, Guardrails, Defensive UX, Collect feedback）。
  - 强调 UX + ML 协同，不是纯技术视角。
- **必读**：Patterns for Building LLM-based Systems & Products、Task-Specific LLM Evals that Do & Don't Work。

### A6. Jason Liu (jxnl) — https://jxnl.co/
- **身份**：instructor 库作者（结构化输出事实标准）、RAG 顾问。
- **核心观点**：
  - **"RAG is not a chatbot"**——大多数 RAG 失败是因为没做查询理解和指标分层。
  - 提出 **RAG 改进六步法**（识别主题簇 → 分主题做 eval → 优化检索 → ...）。
  - 强调 **Pydantic + instructor** 做结构化输出，取代裸 prompt。
- **必读**：Systematically improving your RAG、How to build a terrible RAG。

### A7. Shreya Shankar — https://www.sh-reya.com/
- **身份**：UC Berkeley PhD，和 Hamel 合开 Evals 课。
- **核心观点**：Eval 工程化、人机协同数据标注（SPADE, EvalGen）。
- **必读**：Who Validates the Validators、Data Flywheels for LLM Applications。

### A8. Philipp Schmid — https://www.philschmid.de/
- **身份**：前 HuggingFace 现 Google DeepMind。
- **核心观点**：开源模型部署 & fine-tune 实操，代码密度高。
- **必读**：Gemma/Llama fine-tune tutorials、vLLM 部署文章。

### A9. Sebastian Raschka — https://magazine.sebastianraschka.com/（Ahead of AI）
- **身份**：《Build a Large Language Model (From Scratch)》作者。
- **核心观点**：每月综述新论文、解释训练细节。适合打底。

### A10. Nathan Lambert — https://www.interconnects.ai/
- **身份**：Allen AI，RLHF 领域核心贡献者。
- **核心观点**：
  - 开源 vs 闭源不是二元，**"open weights ≠ open source"**。
  - 对"Scaling is all you need"持怀疑，更看重 post-training（SFT + RLHF + DPO）的工艺。
- **必读**：每月 open models roundup、RLHF 分析。

### A11. Jim Fan — X @DrJimFan（博客少，主在 X）
- **身份**：NVIDIA GEAR Lab。
- **核心观点**：**物理具身智能 = Agent 的终极形态**；Foundation Agent for robotics。

### A12. Andrej Karpathy — https://karpathy.ai/ + YouTube
- **身份**：OpenAI 创始成员、Tesla 前 AI 主管。
- **核心观点**：
  - **"LLM OS"**——LLM 是新的 CPU，tool use + memory + multimodal 是外设。
  - 教育主义路线，亲自从 0 手写 GPT/Tokenizer/LLM101n。

### A13. Swyx (Shawn Wang) — https://www.latent.space/ + https://www.swyx.io/
- **身份**：Latent Space 播客主理人。
- **核心观点**：提出 **"AI Engineer"** 这个职业类别；主张工程师 + 产品 + 社区三栖。

### A14. Harrison Chase (LangChain) — https://blog.langchain.dev/
- **身份**：LangChain / LangGraph CEO。
- **核心观点**：Agent 要从"prompt 链"转向 **"graph + state + human-in-the-loop"**（LangGraph 理念）。

### A15. Jerry Liu (LlamaIndex) — https://www.llamaindex.ai/blog
- **核心观点**：RAG 是**检索+推理分层架构**，推 "agentic RAG"（query planning + tool use over indexes）。

### A16. Greg Kamradt (Data Independent) — YouTube + https://www.dataindependent.com/
- **核心观点**："Needle in a Haystack" 长上下文评测作者，反对把 long context 当 RAG 替代品。

### A17. 其他值得订阅（简）
- **Omar Khattab** — ColBERT/DSPy 作者，主张 **"programming, not prompting"**。https://omarkhattab.com/
- **Matt Shumer** — Prompt 工程实战派。
- **Anton Osika (Lovable)** — AI coding agent 产品视角。
- **Erik Bernhardsson** — https://erikbern.com/ ML infra 老兵。
- **Vicki Boykis** — https://vickiboykis.com/ embedding、数据文化。

---

## 三、B 类：厂商官方 & 研究实验室

### B1. Anthropic
- **Blog**: https://www.anthropic.com/news
- **Research**: https://www.anthropic.com/research
- **Cookbook**: https://github.com/anthropics/anthropic-cookbook
- **立场特点**：**安全优先 + Constitutional AI**；近期主推 **Claude Code、Agent Skills、MCP、Tool Use**；对 Agent 的 "computer use" 能力走在最前。
- **必读**：Building effective agents（把 Agent 拆成 workflow vs agent 非常精辟）、How we built Claude Code、所有 MCP 相关文档。

### B2. OpenAI
- **Blog**: https://openai.com/blog
- **Cookbook**: https://cookbook.openai.com/
- **立场**：产品先行；Assistants API → Responses API → Agents SDK 的演化。
- **必读**：A practical guide to building agents、Swarm/Agents SDK repo。

### B3. Google DeepMind / Google Research
- https://deepmind.google/discover/blog/
- https://research.google/blog/
- **立场**：长上下文（Gemini 2M+）、科学 AI（AlphaFold/AlphaProteo）、Agent（Project Astra/Mariner）。

### B4. Meta AI
- https://ai.meta.com/blog/
- **立场**：开源权重 (Llama)；推"开源是 AI 民主化"叙事。

### B5. Mistral / Cohere / DeepSeek / Qwen / Kimi（Moonshot）
- Mistral: https://mistral.ai/news
- Cohere: https://cohere.com/blog（企业 RAG 与 reranker 的主要玩家）
- DeepSeek: https://api-docs.deepseek.com/news（开源推理模型代表）
- Qwen: https://qwenlm.github.io/blog/
- Moonshot Kimi: https://moonshotai.github.io/

### B6. HuggingFace — https://huggingface.co/blog
- **立场**：开源集散地；必看 smolagents、text-generation-inference、datasets 系列。

### B7. NVIDIA Developer — https://developer.nvidia.com/blog
- **立场**：推理优化 (TensorRT-LLM、Triton)、agent 基础设施。

---

## 四、C 类：论文 & 综述平台

### C1. arXiv
- 分类: cs.CL、cs.AI、cs.LG、cs.IR（RAG 相关）
- 每日精选工具：**alphaXiv** (https://www.alphaxiv.org/)、**arxiv-sanity** (https://arxiv-sanity-lite.com/)

### C2. Papers With Code — https://paperswithcode.com/
- 看榜单变化，不看文章。

### C3. 重要综述"常读"列表（应用层必备）
- Gao et al. *Retrieval-Augmented Generation for Large Language Models: A Survey* (2024)
- Wang et al. *A Survey on Large Language Model based Autonomous Agents*
- Xi et al. *The Rise and Potential of LLM Based Agents: A Survey*
- Anthropic *Building Effective Agents*
- OpenAI *A Practical Guide to Building Agents*

### C4. 论文追踪工具
- **Emergent Mind**: https://www.emergentmind.com/ — 按 HN/Twitter 热度排论文
- **AK (@_akhaliq)** on X / HuggingFace Daily Papers: https://huggingface.co/papers

---

## 五、D 类：社区 / 论坛

### D1. Reddit（公开，部分内容可抓）
- **r/LocalLLaMA** — 开源模型、本地部署真实战场
- **r/MachineLearning** — 学术讨论
- **r/LangChain** — Agent 框架吐槽 & 实践
- **r/OpenAI**、**r/Anthropic** — 厂商动态
- **r/ChatGPTPromptGenius** — prompt 实战
- **r/singularity** — 偏噪声，仅看高赞

### D2. Hacker News — https://news.ycombinator.com/
- 搜索 `show:hn ai`、`llm`、`rag`、`agent`。评论区信息密度往往高于文章本身。

### D3. Discord（封闭，需手动）
- LlamaIndex、LangChain、HuggingFace、OpenAI Devs、Latent Space、Anthropic Builder、EleutherAI。
- **抓取说明**：Discord 明确禁止机器人爬取非本机器人消息；只能靠加入后手动 export 所在频道历史（Discord 官方 Data Request）。

### D4. GitHub Trending — https://github.com/trending
- 按 Python、TypeScript、月维度看，是 Agent/RAG 新工具的第一现场。

---

## 六、E 类：投资机构 & 行业研究

### E1. Sequoia Capital — https://www.sequoiacap.com/ai/
- 代表作者：**Sonya Huang、Pat Grady、Konstantine Buhler**
- 必读：Generative AI's Act One/Two/Three、AI's $600B Question、AI Agent Landscape。
- **立场**：市场结构化叙事，善于造概念（Act 系列、$600B、application layer is where value accrues）。

### E2. a16z — https://a16z.com/ai/
- 代表作者：**Martin Casado、Marc Andreessen、Guido Appenzeller**
- 必读：Emerging Architectures for LLM Applications、The Economic Case for Generative AI。

### E3. Menlo Ventures — https://menlovc.com/perspective/
- 每年 State of Generative AI in the Enterprise 是企业落地数据金矿。

### E4. Bessemer / Madrona / Greylock — 行业报告质量也高，偶尔看。

### E5. 独立分析师 / Newsletter
- **Latent Space** (swyx & alessio): https://www.latent.space/
- **Import AI** (Jack Clark, Anthropic 联创): https://importai.substack.com/
- **The Batch** (Andrew Ng / DeepLearning.AI): https://www.deeplearning.ai/the-batch/
- **Ben's Bites**: https://bensbites.com/
- **TLDR AI**: https://tldr.tech/ai
- **AlphaSignal**: https://alphasignal.ai/
- **Last Week in AI**: https://lastweekin.ai/
- **Stratechery** (Ben Thompson, 付费): 对 AI 商业层最深的 meta-分析
- **Gergely Orosz — Pragmatic Engineer**: AI 工程团队管理视角

---

## 七、F 类：中文生态

### F1. 研究院 / 官方机构
- **腾讯研究院** — https://www.tisi.org/ 偏政策、社会影响、合规
- **阿里研究院** — http://www.aliresearch.com/ 偏产业趋势
- **中国信通院 (CAICT)** — http://www.caict.ac.cn/ 权威白皮书
- **智源研究院 BAAI** — https://www.baai.ac.cn/ 学术+开源
- **上海人工智能实验室** — https://www.shlab.org.cn/

### F2. 科技媒体（公众号 + 网站）

| 名称 | 定位 | 入口 |
|---|---|---|
| 机器之心 | 学术+产业快报 | jiqizhixin.com |
| 量子位 | 快讯+解读 | qbitai.com |
| 新智元 | 快讯 | — |
| 智能涌现 | 36氪旗下，偏商业 | 36氪/公众号 |
| AI前线 | InfoQ，工程向 | 公众号 |
| Founder Park | 极客公园，偏创业访谈 | foundpark |
| 海外独角兽 | 拾象，海外深度 | 公众号 |
| 腾讯科技 | 综合 | — |
| 甲子光年 | 行业报告 | — |

### F3. 知乎 / 公众号大V（AI 应用层）
- **张俊林** — 大模型全景综述（《拆解通向AGI之路》系列）
- **符尧 (Yao Fu)** — 香港大学，scaling/post-training 深度分析 https://franxyao.github.io/
- **李沐 (Mu Li)** — B站《跟李沐学AI》、《论文精读》，Boson AI
- **刘聪NLP** — 大模型开源实践
- **李rumor** — NLP 女性工程师视角
- **Naiyan Wang (王乃岩)** — CV/多模态
- **JackCui / ShowMeAI** — 教程向

### F4. 即刻 / X 中文圈 / 独立博客
- **宝玉 (@dotey)** — 翻译搬运 + 工程实践，产出密度极高
- **歸藏 (@op7418)** — AI 工具地图，产品向
- **向阳乔木** — AI 工具实测
- **indigo / Jason Ng** — 产品 + AI 思考
- **Idoubi** — 独立开发者 AI 应用
- **Hypernova** — 海外信源搬运
- **賽博禪心（公众号）** — 深度技术解读

### F5. 中文播客
- **OnBoard!** — M小姐 & Monica，硅谷 AI 访谈（质量最高之一）
- **十字路口 Crossing** — Koji & Ronghui，AI 创业
- **硅谷101** — 泓君
- **乱翻书** — 潘乱，互联网+AI
- **张小珺 Jùn|商业访谈录** — 长访谈
- **屠龙之术** — AI 工程

### F6. B 站 / 视频
- **跟李沐学AI** — 论文精读
- **AI科技聊斋 / AI 大神** 频道
- **YouTube 中文**: AI Jason (陈泽)、林亦LYi

---

## 七·五、G 类：年度报告 / 基准榜单 / 权威数据源

这类是写文章时的"硬通货"——可引用、可对比、可溯源。

### G1. 年度行业报告
- **Stanford HAI — AI Index Report** — https://hai.stanford.edu/ai-index/2026-ai-index-report
  - 斯坦福以人为本 AI 研究院每年 3-4 月发布，是学界/政策/产业**引用率最高**的年度总报告。覆盖：研究产出、训练成本、能力基准、负责任 AI、经济影响、公众观感、政策。立场：中立学术，数据驱动；不推任何叙事。
- **State of AI Report** (Nathan Benaich / Air Street Capital) — https://www.stateof.ai/
  - 每年 10 月发布，投资人视角，话题比 HAI 更激进（含预测 prediction scorecard）。
- **Menlo Ventures — State of Generative AI in the Enterprise** — https://menlovc.com/2025-the-state-of-generative-ai-in-the-enterprise/
  - 企业采购侧最权威的数据（谁用 Claude / GPT / Gemini、预算、使用场景）。
- **a16z — 16 Changes to the Way Enterprises Build and Buy Generative AI** — 每年更新，企业落地趋势。
- **McKinsey — The State of AI** — https://www.mckinsey.com/capabilities/quantumblack/our-insights 年度 + 季度。
- **BCG — AI at Work** — 员工侧使用数据。
- **中国信通院《人工智能发展白皮书》** — http://www.caict.ac.cn/ 国内官方口径。
- **IDC / Gartner AI 魔力象限与预测** — 付费但节选公开。

### G2. 基准 / 榜单（Leaderboards）
- **LMSYS Chatbot Arena** — https://lmarena.ai/ 人类盲测 Elo 排名，**业界最被认可的综合能力榜**。
- **Artificial Analysis** — https://artificialanalysis.ai/ 第三方独立评测（速度、价格、质量三维），API 对比首选。
- **HuggingFace Open LLM Leaderboard** — https://huggingface.co/open-llm-leaderboard 开源模型标准榜。
- **SWE-bench** — https://www.swebench.com/ 代码 agent 事实标准。
- **GAIA** — 通用 Agent 基准（Meta 提出）。
- **AgentBench / WebArena / OSWorld** — Agent 专项。
- **MTEB** — https://huggingface.co/spaces/mteb/leaderboard Embedding 模型榜。
- **Scale SEAL Leaderboards** — https://scale.com/leaderboard 闭源公正评测。

### G3. 数据与趋势追踪站
- **Epoch AI** — https://epochai.org/ Compute、训练成本、scaling 的**唯一权威定量来源**（被 HAI、State of AI 反复引用）。
- **Our World in Data — AI** — https://ourworldindata.org/artificial-intelligence 长时间序列可视化。
- **AI Incident Database** — https://incidentdatabase.ai/ 事故案例库（写风险/安全文必备）。
- **Stanford CRFM** — https://crfm.stanford.edu/ 基础模型中心，HELM 评测源头。
- **MLCommons** — https://mlcommons.org/ MLPerf 基准。
- **AI Alignment Forum / LessWrong** — 安全 & alignment 讨论（观点浓度高）。

### G4. 政策 & 智库
- **CSET (Georgetown)** — https://cset.georgetown.edu/ AI 政策首选
- **AI Now Institute** — https://ainowinstitute.org/
- **Brookings AI** — https://www.brookings.edu/topic/artificial-intelligence/
- **OECD.AI** — https://oecd.ai/ 全球政策追踪
- **欧盟 AI Act 官方** — https://artificialintelligenceact.eu/
- **中国《生成式人工智能服务管理暂行办法》及配套** — 国家网信办、工信部

### G5. 科技媒体（深度报道）
- **MIT Technology Review — AI** — https://www.technologyreview.com/topic/artificial-intelligence/
- **The Information**（付费）— 独家行业内幕最强
- **财新 / 晚点 LatePost** — 中文深度报道

---

## 八、YouTube（英文）必订

| 频道 | 价值 |
|---|---|
| Andrej Karpathy | 第一手大师课 |
| Yannic Kilcher | 论文精读（偏学术） |
| AI Jason (陈泽，英文) | Agent/RAG 实战 |
| James Briggs | RAG/向量检索实战，Pinecone |
| Sam Witteveen | LangChain/LangGraph 教程 |
| Matthew Berman | 开源模型测评 |
| David Shapiro | Agent 思辨（争议性，看观点） |
| 1littlecoder | 每日 AI 新闻 |
| All About AI | 工具测评 |
| Matt Wolfe | 行业新闻综述 |
| bycloud | 论文趣味解读 |
| Fireship | 轻快综述 |
| Latent Space（podcast 视频版） | 访谈 |

---

## 九、立场光谱与交叉验证

为了避免信息茧房，按**核心议题**把上面的人分成立场簇，供写作时对照引用：

### Q1: Agent 是 workflow 加壳，还是范式革命？
- **workflow 派（冷静）**：Anthropic《Building effective agents》、Chip Huyen、Hamel Husain、Simon Willison
- **agent 激进派**：Harrison Chase (LangGraph)、Yohei (BabyAGI 遗产)、David Shapiro
- **折中**：Jerry Liu（agentic RAG）、Jason Liu

### Q2: RAG 的未来是长上下文替代还是持续进化？
- **长上下文威胁论**：早期 Gemini 发布时社区观点
- **RAG 不会死**：Jason Liu、Jerry Liu、Greg Kamradt（Needle 测试）、Eugene Yan
- **混合派**：大多数工程博主，分层检索 + 长上下文

### Q3: Fine-tune vs Prompt vs RAG 怎么选？
- **eval-first**：Hamel、Shreya、Eugene
- **prompt 为主**：Simon Willison
- **structured prompt + programming**：Omar Khattab (DSPy)、Jason Liu (instructor)
- **fine-tune 回归派**：Nathan Lambert、Philipp Schmid

### Q4: 开源 vs 闭源终局？
- **开源权重派**：Meta、Mistral、DeepSeek、Nathan Lambert
- **闭源前沿派**：OpenAI、Anthropic、Google
- **分层派**：前沿闭源 + 长尾开源（多数分析师）

### Q5: 通用 Agent 平台 vs 垂直 Agent？
- **通用派**：LangChain、OpenAI Agents SDK、Anthropic Claude
- **垂直派**：Sequoia "AI Agent Landscape"、应用层创业者

---

## 十、抓取可行性速查表

| 平台 | 公开抓取 | RSS/API | 替代方案 |
|---|---|---|---|
| 个人博客 (hamel.dev 等) | ✅ | ✅ RSS | 直接爬 |
| arXiv | ✅ | ✅ API | arxiv Python 包 |
| HackerNews | ✅ | ✅ Firebase API | 官方 API |
| Reddit | ⚠️ 限流 | ✅ JSON endpoint | old.reddit.com + UA |
| YouTube 字幕 | ✅ | ✅ | yt-dlp --write-auto-subs |
| Medium | ⚠️ 部分付费墙 | ✅ RSS /feed/@user | RSS |
| X / Twitter | ❌ 严格反爬 | ❌ (收费) | Nitter 镜像（不稳）、手动 bookmark 导出 |
| 微信公众号 | ❌ | ❌ | 手动复制 / 订阅号助手导出 / 搜狗搜索 |
| 知乎 | ❌ 登录墙 | ⚠️ 部分 RSS | 手动 / RSSHub 自建 |
| 即刻 | ❌ | ⚠️ RSSHub | RSSHub |
| Discord | ❌ ToS 禁止 | ❌ | 官方 Data Request 导出 |
| Substack / Ghost | ✅ | ✅ RSS | RSS |
| YouTube 频道 | ✅ | ✅ RSS | /feeds/videos.xml?channel_id= |

**建议路径**：先不碰封闭平台，**用 RSS + arXiv API + 官方 API** 覆盖 70% 的高价值信号，剩下中文封闭平台靠手动收藏 + AI 摘要。

---

## 十一、下一步（执行顺序）

**你现在就可以做的**：把这份文档当目录，按兴趣点开链接订阅 RSS（推荐 Feedly / Inoreader / Readwise Reader）。

**配套自动化可以做的**：
- 把 A 类博客 + B 类官方博客的 RSS 全部列出，生成一个 OPML 订阅包，导入 Feedly 一键订阅。
- 配置**每日 scheduled task**：每天早上自动抓取 A/B/E 类 RSS 最新文章，产出一份"昨日要点 + 选题建议" Markdown。
- arXiv 每日过滤：用关键词（RAG / agent / eval / tool use / LLM application）筛选，输出 Top 5。

**需要决策的三件事**：
1. 每日摘要以什么形式交付？（a. Markdown 文件到 outputs 文件夹；b. 发邮件到 Gmail 草稿箱；c. 写入 Notion 数据库）
2. 每日运行时间？（建议北京时间早 8:00，对应硅谷前一晚更新完毕）
3. 覆盖面：先覆盖 A 类 17 个博客 + B 类 7 个官方 + arXiv，还是全量？

---

*本文档为 v1，后续每周随信源变化增删。建议在每个人/平台后面加一列"我的评分 1-5"和"是否已订阅"，半年后回顾保留真正在读的。*
