# Overview
## 記憶的種類
- 短期記憶：Work Memory
- 長期記憶：Factual Memory, Experiencital Memory
## 記憶生命週期
- 記憶如何形成?
  - 語意摘要
    - 遞增式語意摘要: 將新觀測到的資訊與既有摘要融合
      - [AgentFold: Long-Horizon Web Agents with Proactive Context Management](https://arxiv.org/pdf/2510.24699)
      - [Write Summary Step-by-Step A Pilot Study of Stepwise Summarization](https://arxiv.org/pdf/2406.05361)
      - [MEM1: Learning to Synergize Memory and Reasoning for Efficient Long-Horizon Agents](https://arxiv.org/pdf/2506.15841)
      - [MemAgent: Reshaping Long-Context LLM with Multi-Conv RL-based Memory Agent](https://arxiv.org/pdf/2507.02259)
    - 分割式語意摘要: 將資訊劃分為不同的語意區塊，並為每個區塊生成獨立摘要
- 記憶如何演進?
    - 
- 記憶如何儲存?
- 記憶如何檢索?

# 挑戰
挑戰不在於「能不能記住」，關鍵在於如何「記得對、存得好、調得巧、用得對」
- 記憶形成
  - 哪些才是重要的?
    - Semantic Summarization、Knowledge Distillation
    - [In Prospect and Retrospect: Reflective Memory Management for Long-term Personalized Dialogue Agents](https://aclanthology.org/2025.acl-long.413.pdf)
    - [From Storage to Experience: A Survey on the Evolution of LLM Agent Memory Mechanisms](https://www.preprints.org/manuscript/202601.0618)
  - 摘要與壓縮會不會「失真」？
    - [CloneMem: Benchmarking Long-Term Memory for AI Clones](https://arxiv.org/html/2601.07023v1) 
  - 不同型態記憶該用同一種形成方式嗎？
- 記憶儲存
  - 可讀性與可用性的取捨
  - 如何修正錯誤、過時、衝突記憶
  - 規模與成本的取捨
- 記憶演化
  - 整合 vs 保留例外
  - 更新的穩定性問題 (Stability–Plasticity Dilemma)
    - 何時該更新? 何時該保留? 要更新多少呢? 
  - 遺忘的風險
    - 不遺忘 → 記憶膨脹、干擾推理
    - 遺忘太多 → 關鍵長尾知識消失 
- 記憶檢索
  - 檢索噪音（Retrieval Noise）
    - Top-K 檢索容易帶入：不相關、過時、語意相近但情境不合的記憶
    - 如何避免「看起來很相關，其實沒用」的記憶干擾推理
  - 檢索時機判斷（When to Retrieve）
    - 太常檢索 → 成本高、干擾思考
    - 不檢索 → 幻覺、錯誤推論 
  - 檢索後如何使用
    - 多段記憶可能：重複、矛盾、粒度不一致
    - 如何整理、融合、壓縮檢索結果，讓推理更清楚而不是更亂 

 
## Reference
- [Memory in Agent Systems](https://www.newsletter.swirlai.com/p/memory-in-agent-systems)

# 衡量指標
## 核心指標
- 效能 Effectiveness: 任務成功率（Task Success Rate），代理啟動的任務中，成功從頭到尾正確完成的百分比
- 效率 Efficiency: 平均任務耗時（Avg Task Duration），任務完成所需的時間，通常與"自動化"或"人工流程"相比
- 自主性 Autonomy: 決策操作次數（Decision Turn Count），代理自主採取的動作次數
- 準確性 Accuracy: 工具／操作選擇準確率（Tool/Action Selection Accuracy），代理在每個步驟中是否選擇了正確的 API 或工具
- 穩健性 Robustness: 恢復率（Recovery Rate），發生錯誤後，透過重試、回退或釐清成功恢復的百分比

## 進階指標
- 每個任務的 LLM 成本（LLM Cost per Task）： 由「消耗的 token 數 × 模型單位成本」計算，衡量每個任務使用語言模型的花費。
- 幻覺率（Hallucination Rate）：適用於摘要或內容生成任務，用來評估模型生成虛構、不準確資訊的比例
- 每次代理迴圈的延遲時間 Latency Per Agent Loop：衡量代理每次決策迴圈所需的時間，用來調整系統的即時反應能力
- 上下文使用分數（Context Utilization Score）: 評估代理在任務中有效使用記憶或過去對話上下文的程度

# Reference
## 基礎介紹
- [什麼是 AI Agent ？一窺 2025 最重要企業 AI 應用趨勢](https://aiworks.tw/blog-ai-agent-2025-trends)
## 評估指標
- [Evaluating Agentic AI in the Enterprise: Metrics, KPIs, and Benchmarks](https://www.auxiliobits.com/blog/evaluating-agentic-ai-in-the-enterprise-metrics-kpis-and-benchmarks)
  - 提出核心衡量指標&進階指標
- [How to Effectively Measure Agentic AI Performance: Essential KPIs and Success Metrics](https://www.getmonetizely.com/articles/how-to-effectively-measure-agentic-ai-performance-essential-kpis-and-success-metrics)
- [AI agent evaluation: Metrics, strategies, and best practices](https://medium.com/online-inference/ai-agent-evaluation-metrics-strategies-and-best-practices-8a00a5b17377)
- [AI Agent Evaluation: Metrics That Actually Matter](https://qawerk.com/blog/ai-agent-evaluation-metrics)
- [How effective is your AI agent? 9 benchmarks to consider](https://www.techtarget.com/searchsoftwarequality/tip/How-effective-is-your-AI-agent-benchmarks-to-consider)
  - [AgentBench](https://github.com/THUDM/AgentBench?tab=readme-ov-file#leaderboard): evaluate LLMs acting as autonomous agents. It emphasizes decision-making, reasoning and adaptability.
  - ALFWorld: evaluate an AI agent's ability to interact with a simulated household, understanding and planning actions that involve manipulating objects.
  - ColBench: aims to simulate a real-world software development workflow. It has the AI agent collaborate with a simulated human partner to create resources such as code or web pages. It tests an agent's ability to reason, clarify human interactions and evaluate complex conversations.
  - Cybench: evaluates the effectiveness of AI agents by assessing how they accomplish cybersecurity challenges.
- [AI Agent Benchmark Compendium](https://github.com/philschmid/ai-agent-benchmark-compendium)
  - BFCL (Berkeley Function Calling Leaderboard): From Tool Use to Agentic Evaluation of Large Language Models
  - ToolBench: A massive-scale benchmark designed for evaluating and facilitating large language models in mastering over 16,000 real-world RESTful APIs
  - ComplexFuncBench: designed for the evaluation of complex function calling in LLMs
  - τ-Bench: designed to test AI agents in dynamic, open-ended real-world scenarios
  - Composio Function Calling Benchmark: Tests the ability of LLMs to correctly call functions based on given prompts. It comprises 50 function calling problems
  - API-Bank: Evaluates an agent's ability to plan step-by-step API calls, retrieve relevant APIs, and correctly execute API calls to meet human needs based on understanding real-world API documentation
  - HammerBench: designed to evaluate the function-calling capabilities of LLMs in realistic, multi-turn human-agent interactions, particularly simulating mobile assistant use cases
  - DPAB-α: a comprehensive benchmark designed to evaluate the function calling capabilities of LLMs
  - NFCL: evaluate the proficiency of LLMs in single-turn function calling tasks
  - [LiveMCPBench](https://docs.google.com/spreadsheets/d/1EXpgXq1VKw5A7l7-N2E9xt3w0eLJ2YPVPT-VrRxKZBw/edit?gid=0#gid=0): designed to evaluate the ability of LLM agents to navigate and effectively utilize a large-scale Model Context Protocol (MCP) toolset in real-world scenarios, overcoming limitations of single-server environments.
  - [MCP-Universe](https://mcp-universe.github.io/): for developing, testing, and evaluating AI agents and LLMs through direct interaction with real-world Model Context Protocol (MCP) servers, rather than relying on simulations, covering domains like financial analysis and browser automation
  - [GAIA (General AI Assistants)](https://huggingface.co/spaces/gaia-benchmark/leaderboard): designed to evaluate General AI Assistants, posing real-world questions that are conceptually simple for humans but significantly challenging for most advanced AI systems
  - [AssistantBench]()
