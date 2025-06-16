# Dialogue System
## 依據對話目的分類
### 開放域對話（open-domain dialogue）
- 目標：
  - 需要展現出知識淵博、風趣且語言自然的特性
  - 追求對話的豐富性和連貫性，比如閒聊、問答、娛樂
  - 避免不當言論
  - 強調個性化和情感溝通等品質
- LLM：
  - 優點：經過大規模預訓練的模型本身掌握了廣泛的常識和知識，能夠就各種話題侃侃而談，並產生類似人類風格的回應
  - 缺點：容易天馬行空：例如對於未知領域的提問，模型可能不願承認自己的不知道，而編造看似合理但實則錯誤的答案（即幻覺問題）
  - 
### 任務導向對話（task-oriented dialogue, TOD）
- 目標：
  - 聚焦在完成特定目標上
  - 如: 預訂餐廳、查詢列車時刻
  - 需要與後端資料庫或API交互，遵循嚴格的業務邏輯
  - 要求對話精確且高效
- LLM：
  - 優點：讓LLM直接根據對話歷史生成對話行為和槽位填充結果，再據此產生回應，減少模組誤差傳遞
  - 缺點：容易天馬行空：例如對於未知領域的提問，模型可能不願承認自己的不知道，而編造看似合理但實則錯誤的答案（即幻覺問題）
- 作法
  - LLM在對話中自動決定何時調用API並填入參數，執行如資料庫查詢、計算等操作後再繼續對話
  - 混合式系統成為熱門方案：讓LLM處理自然語言理解和生成部分，但關鍵決策仍由顯式策略模組或規則把關
- 挑戰：
  - 當任務流程複雜或需要遵循業務政策時，僅靠在Prompt中添加大量指示往往不足以約束LLM的行為
  - LLM在任務型對話中的幻覺和不服從問題仍需關注
  - LLM在嚴格遵守對話流程上仍缺乏機制，開發者難以完全控制其每一步行為
### 融合風格對話
- 在主要任務流程中穿插寒暄和安撫，用戶既能得到任務結果也有良好體驗​
- 挑戰：
  - 如何動態調節兩種對話模式的切換、確保不互相干擾
- 
## 相關研究議題
### 對話狀態追蹤(Dialogue State Tracking, DST)
- 用對話狀態（如槽位-值集合）作為對話歷史的高度摘要
- [詳細說明](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Dialogue_State_Tracking.md)
#### 論文
- [(2022)"Do you follow me?": A Survey of Recent Approaches in Dialogue State Tracking](https://arxiv.org/abs/2207.14627)
- [(2023)Towards LLM-driven Dialogue State Tracking](https://aclanthology.org/2023.emnlp-main.48.pdf)
  - 要解決的問題: 期望不依賴大型閉源模型(如 ChatGPT)，實現具強泛化能力、且可實際部署的 DST系統
  - 貢獻:
    - 以GPT-3.5做實驗(擷取關鍵資訊)發現Prompt中若提供範例，對話範例會影響GPT取得關鍵資訊的正確率，同時問多個Slot也會影響準確率
    - 提出LDST框架: 基於開源LLaMA模型驅動DST，包含"組合式領域-槽位指令微調(assembled domainslot instruction tuning method)"和"參數高效微調技術(parameter efficient tuning technique)"實現
- [(2024)Enhancing Dialogue State Tracking Models through LLM-backed User-Agents Simulation](https://arxiv.org/abs/2405.13037)
  - 要解決的問題: 如何在缺乏大量真實標註對話資料的情況下，有效訓練出適應多領域、可泛化的對話狀態追蹤（DST）模型
  - 貢獻
    - 提出LLM 支援的使用者–代理人模擬LUAS : 使用GPT-4模擬user和agent互動，生成帶有DST標籤的大量模擬對話，降低對話資料的收集與標註成本
    - 提出兩階段微調，避免生成資料與真實資料混和一起訓練模型導致資料分布不一致，避免訓練偏移
- [(2024)Large Language Models as Zero-shot Dialogue State Tracker through Function Calling](https://arxiv.org/abs/2402.10466)
  - 要解決的問題: 
  - 貢獻
    - 提出 FNCTOD：以函數呼叫實現零樣本對話狀態追蹤（Zero-shot DST）
    - 提出兩階段函數呼叫生成流程，避免每輪對話都需重複載入完整函數規格，提升效能與成本效率
- [(2025) Interpretable and Robust Dialogue State Tracking via Natural Language Summarization with LLMs](https://arxiv.org/abs/2503.08857)
  - 要解決的問題: 結構化輸出(slot-value)缺乏對複雜語境與使用者意圖的表達力
  - 貢獻: 提出了 自然語言 DST（NL-DST） 框架，訓練 LLM 直接產出可讀性高的"人類語言狀態描述"取代Slot-Value

### RL與對話決策
- 類別
  - RLHF
    - [(2025)Modeling Future Conversation Turns to Teach LLMs to Ask Clarifying Questions](https://openreview.net/pdf?id=cwuSAR7EKd)
    - [(2024)Knowledge acquisition for dialogue agents using reinforcement learning on graph representationsKnowledge acquisition for dialogue agents using reinforcement learning on graph representations](https://arxiv.org/html/2406.19500v1)
    - [(2024)On Overcoming Miscalibrated Conversational Priors in LLM-based Chatbots](https://www.microsoft.com/en-us/research/wp-content/uploads/2024/06/Overcoming_RLHF.pdf)
    - [(2019)Reinforcement Learning for Personalized Dialogue Management](https://arxiv.org/abs/1908.00286)
      - 可能太舊
  - RLAIF 
- 論文：
  - [(2024)Plug-and-Play Policy Planner for Large Language Model Powered Dialogue Agents](https://openreview.net/pdf?id=MCNqgUFTHI)
    - 讓一個可調參的小型策略模型作為插件指導LLM的對話決策
    - 以有標註數據進行監督微調，隨後採用自我對弈(self-play)方式讓LLM與自身模擬對話，透過AI代理提供的目標導向反饋進行強化學習​
    - 經過RL微調的策略插件可以顯著提升LLM在主動式對話任務，使LLM在沒有人工介入的情況下，不斷改進對話策略，並能快速遷移到新場景
  
### 外部知識增強對話管理
- 透過RAG改善LLM幻覺議題
- 這在任務型問答、推薦對話系統當中會很有幫助
- 系統
  - stanford的KITA
  - ChatCRS：工具增強的知識檢索代理、目標規劃代理
- 論文
  - [(2024)Knowledge-enhanced Response Generation in Dialogue Systems: Current Advancements and Emerging Horizons](https://aclanthology.org/2024.lrec-tutorials.13.pdf) 
### 上下文管理與長程對話
- LLM受限於有限的上下文視窗，在特別長的對話仍面臨挑戰
- 多數模型在輸入超過數千Token後，性能會下降，難以掌握對話中跨越很多倫次的因果關係、時間脈絡
- [詳細說明](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Context_and_Long-term_Dialogue_Management.md)
#### 論文:
- [(2023)MemoryBank: Enhancing Large Language Models with Long-Term Memory](https://arxiv.org/pdf/2305.10250)
  - 要解決的問題: 大型語言模型（LLMs）缺乏「長期記憶機制」
  - 貢獻
    - 提出 MemoryBank：針對 LLM 的新型長期記憶機制
    - 實作 SiliconFriend：具備長期記憶與同理心的 AI 聊天夥伴
    - 建立評估框架並進行質性與量化實驗

- [(2024)Hello Again! LLM-powered Personalized Agent for Long-term Dialogue](https://arxiv.org/pdf/2406.05925)
  - 要解決的問題
    - 如何讓聊天機器人在長期、多場次對話中，能夠記住過去事件、維持人設一致，並生成連貫且個人化的回應。
  - 貢獻
    - 提出框架 LD-Agent: 通用且模組化的長期對話代理 

- [(2024)THEANINE: Revisiting Memory Management in Long-term Conversations with Timeline-augmented Response Generation](https://arxiv.org/pdf/2406.10996v1)
  - 要解決的問題: 在實務中LLM經常忽略或錯誤回憶過往對話內容，導致回應品質不佳，特別是在多輪／長期對話中無法正確參照先前的事件或人物資訊。
  - 貢獻
    - 提出框架 THEANINE: 以「記憶時間線」為核心、具備因果推理能力的回應生成框架，用於長期對話(Memory Graph + Timeline結構)
    - 設計 TeaFarm：一種基於反事實推理的自動化評估流程，用來測試模型是否真正記得過去對話
    - 建構 TeaBag：一套專為 TeaFarm 設計的測試資料集，涵蓋 MSC 和 CC 兩個多輪對話基準集

  - [(2024)Commonsense-augmented Memory Construction and Management in Long-term Conversations via Context-aware Persona Refinement](https://arxiv.org/abs/2401.14215)
    - 要解決的問題: 如何在不損失資訊的前提下，有效處理與整合多輪對話中可能互相矛盾的人設句子，並提升長期對話中的回應品質與人設一致性 
    - 貢獻
      - 首次在多輪對話中探討基於常識的人設擴展（persona expansion）
      - 提出 CAFFEINE：情境感知的人設精煉框架
      - 設計圖結構的「逐步精煉（iterative refinement）」流程
      - 結合 LLM（ChatGPT）執行常識推理與精煉判斷
      - 兼具時間與成本效益

  - [(2024)StreamingDialogue: Prolonged Dialogue Learning via Long Context Compression with Minimal Losses](https://openreview.net/pdf?id=eNvVjpx97O)
    - 要解決的問題: 在不犧牲生成品質與上下文一致性的前提下，讓LLM能高效地處理長對話上下文，並具備長期記憶能力
    - 貢獻
      - 發現對話中特殊的「發話結束」標記（End-of-Utterance）EoU tokens 的資訊聚合潛力，並提出 conv-attn sinks 概念
      - 提出 StreamingDialogue 架構，支援長對話生成

  - [(2025)In Prospect and Retrospect: Reflective Memory Management for Long-term Personalized Dialogue Agents](https://arxiv.org/abs/2503.08026)
    - 要解決的問題: 如何讓 LLM 在長期對話中能有效、動態地記住過去資訊，並根據當前對話任務，準確地擷取與整合相關記憶，生成具一致性與個人化的回應。
    - 貢獻
      - 提出框架Reflective Memory Management(RMM)
      - 
### 以推理引擎增強對話管理
- 論文
  - [(2024)Chain of Thought Explanation for Dialogue State Tracking](https://arxiv.org/html/2403.04656v1)
    - 貢獻
      - 提出模型
        - Chain-of-Thought-Explanation, CoTE
        - CoTE-refined: CoTE + 自動同義改寫（paraphrasing）機制
      - 資料集
        - MultiWOZ 2.2
        - WoZ 2.0 (2017): 來自 Wizard-of-Oz 實驗的資料集，使用者透過打字輸入查詢（非語音輸入），因此對話上下文更為複雜。
        - M2M
          - 包含來自兩個領域（餐廳與電影）的 3,000 筆模擬對話，後續經人工校正。
          - 將餐廳與電影子資料集分別稱為 M2M-R 和 M2M-M，合併版則記為 M2M-R+M。 
    - 概念
      - 將CoT引入至DST當中，模型在決定槽位值後生成逐步推理的解釋
        - ![image](https://github.com/user-attachments/assets/03259e63-8067-473d-af85-db8f73b0c0b4)

        -  粗略解釋（Coarse Explanation)
        -  精緻解釋（Refined Explanation）
      - 能引導模型從相關對話輪中蒐集資訊並推理正確的槽值，從而提高預測的準確可靠性
    - 模型: Chain-of-Thought-Explanation（CoTE）
      - 基底模型：T5-Base (220M) 
      - 專為 DST 任務設計
      - 強調獲得槽位值所需的推理步驟，能在確定槽位值後逐步產出詳細的推理解釋，有助於生成更準確且可靠的槽位值
#### reference
- [DeepMind最新：发布说话者-推理者架构实现Agents快慢思考 | 融合系统1+系统2](https://blog.csdn.net/m0_59164520/article/details/143027392)

### 待分類
- [(2023)MIRACLE: Towards Personalized Dialogue Generation with Latent-Space Multiple Personal Attribute Control](https://arxiv.org/abs/2310.18342)
  - 如何實現高精度、多屬性可控的個性化對話生成，克服現有方法在人格建模稀疏、控制能力不足與生成品質折衷上的限制。
  - 屬於"個性化回覆生成" 
- [(2024)Evolving to be Your Soulmate: Personalized Dialogue Agents with Dynamically Adapted Personas](https://arxiv.org/html/2406.13960v1)
  - 自我演化的個人化對話代理人（Self-evolving Personalized Dialogue Agents, SPDA）
    - 逐步對齊使用者資訊 → 代理人根據對話中逐步揭露的使用者偏好與反應來更新自身人格
    - 需避免自我矛盾（例如職業不能前後矛盾） → 這實際上涉及對「人格記憶（persona memory）」的一致性管理，屬於一種結構化長期記憶的形式  
  - 人格對齊
  -  
- [(2024)Recent Trends in Personalized Dialogue Generation: A Review of Datasets, Methodologies, and Evaluations](https://arxiv.org/html/2405.17974v1)
  - 整理了22個資料集，並重點介紹了基準資料集以及具備額外特徵的新型資料集
  - 分析了2021至2023年間於頂尖會議中發表的17篇代表性研究，並歸納出五種不同的研究問題類型。
- [(2024)KAG: Boosting LLMs in Professional Domains via Knowledge Augmented Generation](https://arxiv.org/pdf/2409.13731)
- [(2024)LLM-Friendly Knowledge Representation for Customer Support](https://aclanthology.org/2025.coling-industry.42.pdf)
    - 要解決的問題: 如何讓大型語言模型（LLM）能夠有效理解並執行企業內部複雜且非結構化的客服工作流程知識？
    - 貢獻
      - 提出 ICA（Intent, Context, Action）格式：結構化業務知識以利 LLM 理解
      - 設計一套高效的合成資料生成流程（Synthetic Data Synthesis）
    - 微調模型
- [(2024)Chatbot Meets Pipeline: Augment Large Language Model with Definite Finite Automaton](https://arxiv.org/pdf/2402.04411v1)
- [(2024)On Overcoming Miscalibrated Conversational Priors in LLM-based Chatbots](https://arxiv.org/pdf/2406.01633)
- [(2024)ELOQ: Enhancing LLM Detection of Out-of-Scope Questions](https://arxiv.org/pdf/2410.14567)
- [(2025)Modeling Future Conversation Turns to Teach LLMs to Ask Clarifying Questions](https://arxiv.org/pdf/2410.13788)
- 
