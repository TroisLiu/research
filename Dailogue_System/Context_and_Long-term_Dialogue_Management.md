# 快速索引
- [(2025)In Prospect and Retrospect: Reflective Memory Management for Long-term Personalized Dialogue Agents](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Context_and_Long-term_Dialogue_Management.md#2025in-prospect-and-retrospect-reflective-memory-management-for-long-term-personalized-dialogue-agents)
- [(2024)StreamingDialogue: Prolonged Dialogue Learning via Long Context Compression with Minimal Losses](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Context_and_Long-term_Dialogue_Management.md#2024streamingdialogue-prolonged-dialogue-learning-via-long-context-compression-with-minimal-losses)
- [(2024)Commonsense-augmented Memory Construction and Management in Long-term Conversations via Context-aware Persona Refinement](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Context_and_Long-term_Dialogue_Management.md#2024commonsense-augmented-memory-construction-and-management-in-long-term-conversations-via-context-aware-persona-refinement)
- [(2024)Hello Again! LLM-powered Personalized Agent for Long-term Dialogue](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Context_and_Long-term_Dialogue_Management.md#2024hello-again-llm-powered-personalized-agent-for-long-term-dialogue)
- [(2024)THEANINE: Revisiting Memory Management in Long-term Conversations with Timeline-augmented Response Generation](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Context_and_Long-term_Dialogue_Management.md#2024theanine-revisiting-memory-management-in-long-term-conversations-with-timeline-augmented-response-generation)
- [(2023)MemoryBank: Enhancing Large Language Models with Long-Term Memory](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Context_and_Long-term_Dialogue_Management.md#2023memorybank-enhancing-large-language-models-with-long-term-memory)

# 概述
- LLM受限於有限的上下文視窗，在特別長的對話仍面臨挑戰
- 多數模型在輸入超過數千Token後，性能會下降，難以掌握對話中跨越很多倫次的因果關係、時間脈絡

## 資料集
- LoCoMo長程對話資料集
  - 每段對話300輪次，9K個詞以上，有數十次獨立對話
  - 用來評估LLM長程記憶能力
 
## 對應策略
### 模型架構調整 (1篇論文)
- 增加模型上下文窗口：但可能丟失對話中間內容
- 強化注意力機制
- 優化 KV 快取
- 改進位置編碼
### 動態檢索相關歷史
- 參考RAG概念，將超出上下文窗口的歷史對話內容存入向量化資料庫
- 產生回應時，透過檢索相關的過往語句，將相關內容附加給LLM)
### 摘要與壓縮歷史 (1篇論文)
- 隨著對話進行，不斷壓縮舊對話內容生成摘要
- 提取出對話狀態，以壓縮表示方式融入上下文
### LLM 結合記憶模組：顯式對話記錄與狀態跟蹤 (3篇論文)
- 構建一個外部記憶來存儲對話中的關鍵資訊或狀態，舉例
 - 之前已確認的事實
 - 用戶偏好
- 每次需要產生回應時，將使用者輸入及相關資訊提供給模型

## 論文
### [(2023)MemoryBank: Enhancing Large Language Models with Long-Term Memory](https://arxiv.org/pdf/2305.10250)
- 屬於**LLM 結合記憶模組**
#### 要解決的問題
- 大型語言模型（LLMs）缺乏「長期記憶機制」
  - 現有記憶增強方法（如 MANNs、NTM）不夠靈活或難以與 LLM 整合
  - 現有模型缺乏擬人化的記憶管理行為: 該忘記哪些舊記憶、強化哪些重要記憶
  - 缺乏有效結合使用者個性建模與記憶更新的機制
  - 尚未有完整實作系統能結合心理對話知識、記憶回憶機制與長期互動能力 
#### 貢獻
- 提出 MemoryBank：針對 LLM 的新型長期記憶機制
  - 記憶儲存器：儲存多輪對話、事件摘要、使用者個性。
  - 記憶擷取器：使用 Dense Retrieval 技術（如 FAISS + MiniLM）檢索上下文記憶。
  - 記憶更新器：結合 艾賓浩斯遺忘曲線理論，模擬人類對「重要記憶強化、次要記憶淡忘」的能力 
- 實作 SiliconFriend：具備長期記憶與同理心的 AI 聊天夥伴
  - 建立了一個整合 MemoryBank 的 AI chatbot 系統 SiliconFriend，支援中英文對話
  - 搭配心理對話資料（38K 條）進行 LoRA 微調，使其具備情感感知與支持能力
  - 系統可部署在多種 LLM 上（ChatGPT、ChatGLM、BELLE），展示開放/封閉模型皆可整合記憶機制
- 建立評估框架並進行質性與量化實驗
#### 缺點
- 記憶更新機制過於簡化:
  -  忽略了內容語意、情感強度、任務重要性等語用因素，這些在真實對話中會強烈影響記憶是否該被保留。
  -  實作中的遺忘策略其實很簡化，例如「每被提及一次就 +1」，不具備「選擇性遺忘與強化」的細緻程度。
- 使用者個性建模方法較粗略
  -  個性推論僅依賴 LLM 對對話進行摘要，形成 daily 與 global 的 personality representation
 
### [(2024)Hello Again! LLM-powered Personalized Agent for Long-term Dialogue](https://arxiv.org/pdf/2406.05925)
  - 屬於**LLM 結合記憶模組**
#### 要解決的問題
- 如何讓聊天機器人在長期、多場次對話中，能夠記住過去事件、維持人設一致，並生成連貫且個人化的回應。
  - 現有對話系統缺乏長期記憶能力
    - 多數聊天機器人僅支援 2–15 輪的單場短對話，無法記住或合理運用跨場次對話的歷史事件。
    - 缺乏對「歷史對話記憶的檢索與利用」能力，導致回應斷裂、不連貫 
  - 對使用者與機器人的人設建模薄弱
    - 雖有研究探討人設建構，但多為單向建模或特定架構依賴，難以支援真實場景中長期、動態的人設演變
    - 人設不一致導致角色表現不穩定、個人化體驗不足
#### 貢獻
- 提出框架 LD-Agent
  - 通用且模組化的長期對話代理
#### 缺點
- 依賴合成資料集，缺乏真實世界驗證

### [(2024)THEANINE: Revisiting Memory Management in Long-term Conversations with Timeline-augmented Response Generation](https://arxiv.org/pdf/2406.10996v1)
  - 屬於**LLM 結合記憶模組**
  - 圖結構 
#### 要解決的問題
- 在實務中LLM經常忽略或錯誤回憶過往對話內容，導致回應品質不佳，特別是在多輪／長期對話中無法正確參照先前的事件或人物資訊。
  - 記憶遺失與回應偏差問題: 雖然 LLM 有大上下文窗口，但生成回應時會過度集中於最近一句話，而忽略對話早期的重要語境資訊
  - 傳統記憶管理資訊更新策略存在資訊遺失: 現有方法為了控制記憶容量，會「更新」或「移除」舊記憶，這可能導致遺失對回應有幫助的重要內容
  - 缺乏有效的記憶組織與檢索機制: 傳統「top-k 檢索」方法容易錯過與事件發展有關的背景資訊，造成回應與對話脈絡脫節
#### 貢獻
- 提出框架 THEANINE: 以「記憶時間線」為核心、具備因果推理能力的回應生成框架，用於長期對話(Memory Graph + Timeline結構)
  - 模仿人類記憶結構的創新設計
  - 利用**事件時間線（memory timelines）**替代單一記憶片段，有效呈現對話事件的發展脈絡與因果關係
  - Memory Graph with Cause-Effect Reasoning（因果記憶圖）: 採用圖結構連結記憶節點，邊上的標註關係來自修改後的常識因果關係集（如 Cause、Want、React、SameTopic）。
  - Context-Aware Timeline Refinement（情境感知的時間線微調）: 為每次對話動態調整所檢索到的時間線，根據當前語境刪減冗餘或加強關鍵資訊，提升即時適用性與生成品質
- 設計 TeaFarm：一種基於反事實推理的自動化評估流程，用來測試模型是否真正記得過去對話
  - 利用「誤導性問題」來檢驗系統是否真正理解／記得歷史事件
  - 比傳統 GPT 評分更能準確測試模型是否正確引用長期記憶 
- 建構 TeaBag：一套專為 TeaFarm 設計的測試資料集，涵蓋 MSC 和 CC 兩個多輪對話基準集
  - 包含 100 組對話（來自 MSC、CC）、200 組反事實問答及對應的第 6 輪自然過渡對話，專為長期記憶檢驗設計 
#### 缺點
- 缺乏在超長對話（>5 輪）上的驗證
  - 所有實驗僅涵蓋 5 輪對話（Session 1–5），尚未驗證 THEANINE 在真正「長期」場景（如數十輪對話）中的效能與穩定性
- 效率問題
  - 記憶圖建構、時間線檢索與情境微調皆需呼叫多次 LLM，推理成本高於傳統記憶檢索法
- TeaFarm 評估框架雖創新，但準確性仍依賴 LLM 判斷，仍存在模型誤判風險

### [(2024)Commonsense-augmented Memory Construction and Management in Long-term Conversations via Context-aware Persona Refinement](https://arxiv.org/abs/2401.14215)
#### 要解決的問題
- 如何在不損失資訊的前提下，有效處理與整合多輪對話中可能互相矛盾的人設句子，並提升長期對話中的回應品質與人設一致性 
  - 人工人設資訊過於貧乏，限制生成效果: 現有對話資料集中，多數由人類撰寫的人設句子太過通用或簡化，缺乏細節，導致生成的回應缺乏多樣性與吸引力
  - 基於常識的人設擴展容易導致人設矛盾: 即使透過 COMET 等常識模型對人設擴展，仍可能引入語義矛盾（如：「我很懶」與「我每天打掃房間」），尤其在多輪對話累積後更明顯
  - 現有方法處理矛盾人設的方式不夠理想
    - 一些方法會直接移除矛盾人設（如使用 NLI 模型過濾），但這樣會損失有用資訊。
    - 其他方法則會避免生成矛盾人設，但這與人類個性多樣且具情境依賴性的特性不一致
  - 缺乏針對多輪對話的人設精煉機制
    - 過去研究大多聚焦於單輪或短期對話，缺乏跨多輪對話下的人設擴展與整合機制
    - 長期對話中的人設需要根據不同語境進行情境感知的精煉 
#### 貢獻
- 首次在多輪對話中探討基於常識的人設擴展（persona expansion）
  - 過去人設擴展大多集中在單輪對話或短期任務，這篇是首度聚焦於跨多輪（multi-session）對話場景 
  - 強調長期記憶中人設的管理與進化需求，對持續性對話的實用性高
- 提出 CAFFEINE：情境感知的人設精煉框架
  - 所創框架能保留並轉化矛盾人設為具豐富資訊的敘述，而不是簡單移除或忽略
  - 透過三種策略（整合、釐清、保留）與 LLM 結合，讓矛盾的人設更貼近人類語言與思維邏輯
- 設計圖結構的「逐步精煉（iterative refinement）」流程
  - 引入圖結構來追蹤與選取待精煉的矛盾人設對，提升效率與擴展性。
  - 精煉後的結果儲存進長期記憶，並動態更新圖結構
- 結合 LLM（ChatGPT）執行常識推理與精煉判斷
  - 不只是生成，還讓 LLM 根據語境自選合適策略、說明理由，進一步產出經「人類化」調整後的人設句
- 兼具時間與成本效益
#### 缺點
- 過度依賴 COMET 等常識模型，品質有限
  - 所使用的 COMET 常識生成模型本身有錯誤率與偏差，若其產生的擴展內容品質不佳，將直接影響後續人設精煉與回應生成。
  - 作者也承認未來希望改用 LLM 本身進行人設擴展，但目前仍依賴外部模型，形成效能瓶頸。
- NLI 模型有誤判風險，影響精煉圖構建
  - CAFFEINE 所依賴的矛盾判斷邏輯（δ）是基於 NLI 模型（RoBERTa-MNLI），但 NLI 模型無法考慮語境與細節語意
  - 可能出現：
    - 原本合理的人設被誤判為矛盾 → 不必要地精煉甚至錯誤合併
    - 真正需要處理的人設未被檢出 → 被遺漏處理
- 僅處理單一使用者人設，缺乏對雙方人設互動的建模
  - 精煉流程是逐一針對單一說話者的人設處理，而非同時考量「雙方在同一主題上可能展現不同特質」的互動動態
- 未充分處理 LLM 輸入長度限制問題
- 人設精煉與回應生成的區隔不夠明確
  - 雖然兩者功能不同（精煉 vs. 回應），但實驗與架構整合度高，讀者可能會混淆兩階段的貢獻來源。
  - 缺乏單獨 ablation study 分析各個策略（如 Resolution vs. Disambiguation）在不同任務中的效果差異。
 
### [(2024)StreamingDialogue: Prolonged Dialogue Learning via Long Context Compression with Minimal Losses](https://openreview.net/pdf?id=eNvVjpx97O)
#### 要解決的問題
- 在不犧牲生成品質與上下文一致性的前提下，讓LLM能高效地處理長對話上下文，並具備長期記憶能力
  - LLM在處理長對話時效率低落
    -  LLaMA2為例[6]，當輸入超過 4,096 的上下文長度時，其推論能力會明顯下降。
    -  此外，注意力機制 [7] 的計算複雜度隨文字長度呈二次成長，導致 GPU 記憶體使用量增加、 
  - 長對話中歷史資訊逐漸流失
  - 稀疏注意力方法雖然加快處理速度，但記憶保留能力不足
  - 缺乏高效的壓縮對話歷史機制
  - 現有模型難以支持「終身對話學習」或極長序列建模
  - 尚無策略能有效學習「對話注意力匯聚點」的資訊聚合能力
#### 貢獻
- 發現對話中特殊的「發話結束」標記（End-of-Utterance）EoU tokens 的資訊聚合潛力，並提出 conv-attn sinks 概念
  - 利用此作為會話注意匯聚點 
  - 在多輪對話中，用來分隔發言的特殊 token（如 </s> 或換行符 \n）在注意力分布上具有顯著聚合特性
  - 「對話注意力匯聚點（conversational attention sinks, conv-attn sinks）」 
- 提出 StreamingDialogue 架構，支援長對話生成
  - 設計一個能在保留關鍵資訊的情況下，大幅減少計算與記憶體成本的對話框架
    - 4 倍速度提升
    - 18 倍記憶體使用量降低 
  - 設計兩項學習策略，強化模型的資訊聚合與記憶能力，減少壓縮帶來的信息損失​
    - SMR（短期記憶重建）：引導模型學會將發言資訊聚合到 conv-attn sink(讓模型僅能透過目標發言的 conv-attn sink 來完成重建)，促使該 sink 有能力從目標句子中恢復資訊，並以其重建內容
    - LMR（長期記憶重啟）：訓練模型將最終發言視為查詢，從歷史對話中召回特定回應(僅針對對話歷史中的 conv-attn sinks 進行注意力操作)，加強對話上下文的長期引用能力
  - 可在未經訓練的 LLM（如 LLaMA 2/3、Mistral）中應用，仍具資訊保留能力
    - 架構外部化（externalized design）
    - 僅修改輸入的 Attention Mask，不改模型參數
      - 關鍵改動在於如何設定 Attention Mask，引導模型僅對 conv-attn sinks、第一個 token、以及最近兩輪的 token 進行注意力運算
    - 在推論階段只快取關鍵位置（conv-attn sinks）以節省記憶體與計算
      - 和 FlashAttention 類似，StreamingDialogue 能在推論階段套用自身的記憶管理與注意力範圍限制，僅保留 conv-attn sinks 的 KV-cache
      - 即使是如 LLaMA-2/3、Mistral 等原生不支援長記憶的模型，只要支援 custom attention mask / KV cache pruning，就可以直接套用
  - 長度外推穩定性佳，對話長達 25K token 仍能維持品質
    - 能準確回憶長達 44 輪前的發言內容，展現卓越的長期記憶能力 
- StreamingDialogue
  - 僅需快取
    - 首個 token
    - conv-attn sinks
    - 最近兩輪發言的 tokens
#### 資料集
  - PersonaChat [35]
  - Multi-Session Chat（MSC）[36]: 延伸的對話上下文
  - Topical-Chat [37]
  - MultiWOZ [38]  
#### Baseline方法
  - Dense Attention（密集注意力）[7]：用來捕捉所有資訊
  - Local Attention [14]：具有固定視窗限制
  - Big Bird [39]：結合滑動視窗、全域與隨機注意力
  - StreamingLLM [15]：在固定視窗內額外關注注意力匯聚點
  - MemBART [40]：帶記憶模組的 Transformer 編碼解碼模型
  - HRED [41]：具階層式編碼器，可編碼任意長度的對話
  - VHRED [42]：具階層式編碼器，可編碼任意長度的對話
#### 評估指標
  - BLEU（B-avg / B-1 / B-2） [43]：B-avg 為 BLEU-1 到 BLEU-4 的平均
  - ROUGE（R-1 / R-2 / R-L） [44]
  - Distinct（D-1 / D-2 / D-3） [45]
  - USL-H [46] 與 Dial-M [47]：兩項無參考基準的對話品質評估指標
  - Perplexity (PPL)
  - C score [48]（在 PersonaChat 中衡量對話一致性）
      - 人工評估
#### 缺點
- 缺乏對 “高可讀性重建文本” 的質化分析與語言品質驗證
  - 雖然提出 SMR（短期記憶重建）任務以驗證資訊聚合能力，但僅使用 BLEU-1（89.19%）來評估重建效果，未進一步分析生成文本是否具有「語義完整」、「上下文流暢」或「符合語用邏輯」等層面 
  - 缺少人類評估對重建語句可讀性、合理性、與原始語句一致性的分析 
- 對 conv-attn sinks 的理論基礎與學理探討不足
  - 雖然發現分隔符如 </s> 有注意力聚合現象，但未深入語言學或以建模機制解釋為何這能聚合語義資訊
  - 未明確區分 attention 聚合 vs. semantic aggregation 的差異，可能混淆注意力重點與實際語義承載能力
- 缺乏與其他「長期記憶結構」的綜合比較與結合討論
  - 僅與部分稀疏注意力與記憶增強方法（如 MemBART、StreamingLLM）比較，未納入 Retrieval-Augmented Generation (RAG)、External Memory Module、或 Memory Compression Transformer 等代表性架構進行分析與對照
- 效能提升的可轉移性仍有待驗證
- 推論效率雖提升，但上下文推理能力未深入探討
  - 論文重點在壓縮與資訊保留，但未呈現本方法對「跨多輪推理」、「因果關係延續」、「角色一致性維持」等高階語用任務的具體優勢
  - 尚無實驗設計能驗證 conv-attn sinks 是否有助於真正的上下文理解與長程邏輯維繫

### [(2025)In Prospect and Retrospect: Reflective Memory Management for Long-term Personalized Dialogue Agents](https://arxiv.org/abs/2503.08026)
- 屬於"摘要與壓縮歷史"
#### 要解決的問題
- 如何讓 LLM 在長期對話中能有效、動態地記住過去資訊，並根據當前對話任務，準確地擷取與整合相關記憶，生成具一致性與個人化的回應。
  - LLM 無狀態（stateless）特性，無法記住長期對話歷史 : 現有 LLM 雖可進行開放式對話，但缺乏記憶機制，難以維持跨 session 的一致性與個人化
  - 現有記憶管理方法使用固定粒度，導致語意片段化 : 多數系統依據「回合」或「階段」為邊界進行記憶切分，但這常常無法對應對話的語意結構，導致關鍵資訊分散，難以有效擷取。
  - 記憶擷取器靜態、難以適應不同對話情境與使用者偏好 : 現有擷取機制（retrievers）通常為固定策略，無法根據實際回應的需要動態優化擷取結果
#### 貢獻
- 提出框架Reflective Memory Management(RMM)
  - 簡單說：讓 AI 能記得你說過的話，而且記得對的重點，用在對的時候
    - 你跟 AI 聊了一些東西（可能好幾次）
    - 它會整理你說過的主題（前瞻性反思）
    - 下次你提問，它去記憶庫找相關筆記
    - 回答後，它再反思自己是不是有用到正確的記憶（回顧性反思）
    - 記憶庫隨著時間會越整理越聰明
  - (1) 前瞻性反思（Prospective Reflection）:
    - 簡單說：聰明整理筆記
    - 動態地將互動內容依不同粒度（如發話、回合、對話階段）進行摘要，存入個人化記憶庫以利未來擷取
    - 持續維持對話歷史的一致性與整合性
    - 兩步驟
      - 記憶擷取（memory extraction）: 利用 LLM 從當前 session 中擷取對話片段，針對每個明確提及的主題，生成對應的主題摘要
      - 記憶更新（memory update）:
        - 對每一條新擷取的記憶，我們從記憶庫中找出語意上最相似的前 K 筆記憶項目
        - 使用另一個 LLM
          - 判斷該筆新記憶應該直接新增進記憶庫（例如：屬於新主題）?
          - 與現有的相似記憶合併為一條更新後的記憶（例如：針對已討論過的主題新增新資訊）?
  - (2) 回顧性反思（Retrospective Reflection）:
    - 簡單說：自我檢討找關鍵
    - 基於 LLM 所引用的證據，透過線上強化學習方式持續優化記憶擷取策略
      - 若 LLM 回應中引用該記憶 → +1（有用）
      - 若未引用該記憶 → −1（無用) : 如果沒用上，它會把這些記憶「降權重」，下次不再亂用
    - ![image](https://github.com/user-attachments/assets/34065f19-1118-481c-b610-75dc02b23b71) 
#### 組成
##### 記憶庫（Memory Bank）
- 將對話歷史儲存為一系列記憶項目（memory entries），每項記憶為一組二元組 (主題摘要, 原始對話)。
- 「主題摘要（topic summary）」作為檢索的關鍵字，用以對應到原始的對話片段。
##### 擷取器（Retriever）
- 根據使用者當前的提問，識別出最相關的記憶項目
- 密集向量擷取器（dense retrievers）
  - Contriever（facebook/contriever）（Izacard et al., 2022）：一種基於對比學習的語意搜尋專用擷取器。
  - Stella（dunzhang/stella_en_1.5B_v5）（Zhang et al., 2024b）：基於語言模型訓練的大型嵌入式擷取器。
  - GTE（Alibaba-NLP/gte-Qwen2-7B-instruct）（Li et al., 2023）：設計用於指令型查詢，涵蓋多語言、多領域語料進行訓練的擷取器。
- 設定
  - 在未搭配重排序器（reranker）的情況下，Top-𝐾 預設為 5
  - 若搭配 reranker，則 Top-𝐾 為 20、Top-𝑀 為 5
- 實驗觀察
  - 增加記憶數量（M）能穩定提升效能 
  - 使用 GTE 與 Stella 這類強大擷取器時，Top-𝐾/Top-𝑀 的調整帶來的效益最為明顯，顯示擷取品質本身是一個重要因素
##### 重排序器（Reranker）
- 對擷取器的初始輸出進行再排序，優先保留最關鍵的記憶內容
- 讓系統能動態調整擷取策略
  - 向量適應（Embedding Adaptation）
  - 使用 Gumbel 技巧的隨機取樣（Stochastic Sampling with Gumbel Trick）
  - 並對每筆記憶分數加入 Gumbel 雜訊（Gumbel, 1954）以進行隨機取樣
  - 接著使用 softmax 將擾動後的分數正規化為機率分布
  - 基於有無引用記憶，產生回饋訊號
    - 若 LLM 回應中引用該記憶 → +1（有用）
    - 若未引用該記憶 → −1（無用）
  - 以REINFORCE演算法進行增強學習
- 處理由擷取器取得的前 K 筆記憶向量（embeddings），並根據當前使用者提問的語意，挑選出前 M 筆最相關的記憶。
  - 增加記憶數量（M）能穩定提升效能 
##### 大型語言模型（LLM）
- 將當前上下文與擷取到的相關記憶整合起來，生成個人化回應
- 根據實際使用的記憶內容產生「回饋訊號」，這些訊號會用於回顧性反思階段，進一步精緻化重排序器
#### 資料集
- MSC（Xu et al., 2022）：多階段對話基準資料集
- LongMemEval（Wu et al., 2024）：長期記憶對話評估資料集
#### 使用模型
- Gemini-1.5-Flash
#### 算力
- 16 顆 NVIDIA A100 (CUDA 版本：12.2)
- 40G 記憶體 
#### Baseline
- No History
- Long Context
- RAG（檢索式生成）: 預設使用「回合」為擷取粒度，以獲得較佳表現
- MemoryBank（Zhong et al., 2024）：將對話歷史視為靜態資料庫，並根據遺忘曲線原則進行擷取調節
- LD-Agent（Li et al., 2024b）：使用固定資料庫，並額外搭配關鍵字比對等策略進行擷取調整       
#### 缺點
- 依賴強化學習，成本高昂 (可能造成訓練成本高與收斂速度慢的問題)
- 記憶更新流程潛在複雜且未量化成本
  - 動態擷取主題摘要、相似度比對、記憶合併 
- 無針對錯誤擷取記憶對回應的影響做深入探討
- 對 Citation-based Reward 的可信度與泛化性探討不足 (雖有驗證 citation precision/recall，但缺乏更深層的語用層級分析，例如 citation 是否代表因果貢獻、是否對生成決策構成實質影響)
