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
- LLM較善於利用完整對話語境來糾正先前輪次可能出現的錯誤
- LLM-DST的整體性能隨對話輪數增加而下降得更慢，對錯誤傳播（error propagation）的抵抗力比傳統模型更佳
- 用對話狀態（如槽位-值集合）作為對話歷史的高度摘要
- 論文：
  - [(2022)"Do you follow me?": A Survey of Recent Approaches in Dialogue State Tracking](https://arxiv.org/abs/2207.14627)
  - [(2023)Towards LLM-driven Dialogue State Tracking](https://aclanthology.org/2023.emnlp-main.48.pdf)
    - 提出LDST框架: 基於開源LLaMA模型驅動DST，透過"領域-槽位指令微調(domain-slot instruction tuning)"實現
  - [(2024)Chain of Thought Explanation for Dialogue State Tracking](https://arxiv.org/html/2403.04656v1)
    - 將CoT引入至DST當中，模型在決定槽位值後生成逐步推理的解釋
    - 能引導模型從相關對話輪中蒐集資訊並推理正確的槽值，從而提高預測的準確可靠性
  - [(2024)Enhancing Dialogue State Tracking Models through LLM-backed User-Agents Simulation](https://aclanthology.org/2024.acl-long.473.pdf)
    - 因人工標註成本高，利用LLM來模擬對話生成標註數據
    - 論文使用GPT-4充當用戶和代理人，生成帶有DST標籤的大量模擬對話，然後用這些合成數據對LLaMA 2模型進行兩階段微調，在MultiWOZ等資料集上取得優於僅用真實數據訓練的效果
  - [(2024)Large Language Models as Zero-shot Dialogue State Tracker through Function Calling](https://arxiv.org/abs/2402.10466)
  - [(2025) Interpretable and Robust Dialogue State Tracking via Natural Language Summarization with LLMs](https://arxiv.org/abs/2503.08857)
### RL與對話決策
- 類別
  - RLHF
    - [(2019)Reinforcement Learning for Personalized Dialogue Management](https://arxiv.org/abs/1908.00286)
      - 可能太舊 
    - [(2024)Knowledge acquisition for dialogue agents using reinforcement learning on graph representationsKnowledge acquisition for dialogue agents using reinforcement learning on graph representations](https://arxiv.org/html/2406.19500v1)
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
- 資料集
  - LoCoMo長程對話資料集
    - 每段對話300輪次，9K個詞以上，有數十次獨立對話
    - 用來評估LLM長程記憶能力
- 對應策略
  - 增加模型上下文窗口：但可能丟失對話中間內容
  - 動態檢索相關歷史：
    - 參考RAG概念，將超出上下文窗口的歷史對話內容存入向量化資料庫
    - 產生回應時，透過檢索相關的過往語句，將相關內容附加給LLM)
  - 摘要與壓縮歷史：
    - 隨著對話進行，不斷壓縮舊對話內容生成摘要
    - 提取出對話狀態，以壓縮表示方式融入上下文
  - 顯式對話記錄與狀態跟蹤
    - 構建一個外部記憶來存儲對話中的關鍵資訊或狀態，舉例
     - 之前已確認的事實
     - 用戶偏好
    - 每次需要產生回應時，將使用者輸入及相關資訊提供給模型
- 方法:
  - [(2024)StreamingDialogue: Prolonged Dialogue Learning via Long Context Compression with Minimal Losses](https://openreview.net/pdf?id=eNvVjpx97O)
    - 利用對話中特殊的「發話結束」標記（End-of-Utterance）作為會話注意匯聚點
    - 將長長的對話歷史壓縮到這些標記上，儘可能保留關鍵資訊
    - 其他策略，減少壓縮帶來的信息損失​
      - 短期記憶重構
      - 長期記憶再激活
    - 大幅降低隨著對話輪數增加的計算成本
    - 模型能處理超過數百輪對話而保持良好效果

### 以推理引擎增強對話管理
#### reference
- [DeepMind最新：发布说话者-推理者架构实现Agents快慢思考 | 融合系统1+系统2](https://blog.csdn.net/m0_59164520/article/details/143027392)

### 待分類
- [(2023)MIRACLE: Towards Personalized Dialogue Generation with Latent-Space Multiple Personal Attribute Control](https://arxiv.org/abs/2310.18342)
- [(2024)Evolving to be Your Soulmate: Personalized Dialogue Agents with Dynamically Adapted Personas](https://arxiv.org/html/2406.13960v1)
- [(2024)Recent Trends in Personalized Dialogue Generation: A Review of Datasets, Methodologies, and Evaluations](https://arxiv.org/html/2405.17974v1)
- [(2025)In Prospect and Retrospect: Reflective Memory Management for Long-term Personalized Dialogue Agents](https://arxiv.org/abs/2503.08026)
