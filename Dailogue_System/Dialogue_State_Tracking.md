# 快速索引
- [(2025)Interpretable and Robust Dialogue State Tracking via Natural Language Summarization with LLMs](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Dialogue_State_Tracking.md#2025-interpretable-and-robust-dialogue-state-tracking-via-natural-language-summarization-with-llms)
- [(2024)Enhancing Dialogue State Tracking Models through LLM-backed User-Agents Simulation](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Dialogue_State_Tracking.md#2024enhancing-dialogue-state-tracking-models-through-llm-backed-user-agents-simulation)
- [(2024)Large Language Models as Zero-shot Dialogue State Tracker through Function Calling](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Dialogue_State_Tracking.md#2024large-language-models-as-zero-shot-dialogue-state-tracker-through-function-calling)
- [(2023)Towards LLM-driven Dialogue State Tracking](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Dialogue_State_Tracking.md#2023towards-llm-driven-dialogue-state-tracking)
- [(2022)"Do you follow me?": A Survey of Recent Approaches in Dialogue State Tracking](https://github.com/TroisLiu/research/blob/master/Dailogue_System/Dialogue_State_Tracking.md#2022do-you-follow-me-a-survey-of-recent-approaches-in-dialogue-state-tracking)

# 概述
- 用對話狀態（如槽位-值集合）作為對話歷史的高度摘要
  
## 兩種設計方向
### 分類式模型（classification-based
- 傳統作法 
- 從一組候選值中選取欄位的值   
- 需事先定義對話本體（ontology），泛化能力較弱
- 在受限領域中表現良好，但在更複雜、開放式的對話中，這些方法常難以泛化
### 生成式模型（generation-based)
- 直接產生欄位值，能處理未見過的新領域與新值 
- LLM較善於利用完整對話語境來糾正先前輪次可能出現的錯誤
- LLM-DST的整體性能隨對話輪數增加而下降得更慢，對錯誤傳播（error propagation）的抵抗力比傳統模型更佳
- 
## 核心議題
### 數據稀缺性與跨領域及低資源泛化能力
- 標註數據成本高昂
- 標註品質不穩定
- 通用性問題
### 處理複雜的對話上下文與推理需求
- 處理複雜和長對話歷史
- 多步驟推理 Multi-step Reasoning
- 處理開放領域對話(ODD)
### 對話理解的穩健性挑戰
#### 錯誤傳播 Error Propagation
- ![image](https://github.com/user-attachments/assets/9c2479a1-0860-4820-964f-f9360f72c129)
#### 共指 Coreference
- 指多個詞或短語在語境中指向同一個實體
- Coreference Resolution是提升 DST 表現時的一大障礙：多輪對話中語言表達多樣性所導致，槽位與其值經常是以間接/代名詞方式表達
##### 對應議題
- 代詞消解（Pronoun Resolution）: 處理「他、她、它、他們」等代詞指涉誰的問題
- 命名實體共指（Named Entity Coreference）: 識別像「台達電子」與「Delta」、「台達電子工業股份有限公司」是否指同一個組織
- 跨句共指（Cross-sentence Coreference）: 不同句子中指涉同一實體的辨識
- 事件共指（Event Coreference）: 識別不同語句是否指的是同一個事件
- 共指鏈（Coreference Chains）: 將所有指涉同一實體的詞彙串成一條鏈，供後續理解與推理使用
#### 吵雜輸入 Noisy Input

## 資料集
- ![image](https://github.com/user-attachments/assets/fd301ff8-22fc-46c8-95a8-3595961838f9)
### Schema-Guided Dialogue(SGD)
- 2020推出
- 涵蓋16個領域、26個服務"
### MultiWOZ
- 目的是提升 DST 任務的評估準確性
- 2.2是2020推出；2.4是2022推出
- 2.4 版本的驗證集與測試集經過了精確的重新標註

## 評估指標
### Joint Goal Accuracy（JGA）
- 衡量評估的是在每一輪對話中，模型預測的所有資訊是否完全正確
- DST 任務中的主要評估指標
- 對於每一個對話輪次，若模型預測的所有槽位（slot）和值（value）與標註完全匹配，則該輪次計為正確
#### 舉例
- 句子：想預訂一家中餐廳，地點在市中心，時間是晚上七點
- 模型A：餐廳類型(中餐廳)、地點(市中心)、時間(晚上七點)  > JGA 100%
- 模型B：餐廳類型(中餐廳)、地點(None)、時間(晚上七點)  > JGA 0%

### Average Goal Accuracy（AGA）
- 評估的是在有活動槽位（active slots）的對話輪次中，模型對這些槽位的預測準確性
- AGA 計算每個輪次中正確預測的槽位比例，然後對這些比例取平均
- AGA 只考慮那些在標註中有非空槽位的輪次
#### 舉例
- 句子：想預訂一家中餐廳，地點在市中心，時間是晚上七點
- 模型A：餐廳類型(中餐廳)、地點(市中心)、時間(晚上七點)  > AGA 100%
- 模型B：餐廳類型(中餐廳)、地點(中心)、時間(晚上七點)  > AGA 66.7%

## 論文：
### [(2022)"Do you follow me?": A Survey of Recent Approaches in Dialogue State Tracking](https://arxiv.org/abs/2207.14627)

### [(2023)Towards LLM-driven Dialogue State Tracking](https://aclanthology.org/2023.emnlp-main.48.pdf)
#### 要解決的問題
- 期望不依賴大型閉源模型(如 ChatGPT)，實現具強泛化能力、且可實際部署的 DST系統
  - ChatGPT 雖強，但不適用於真實應用情境:
    - 雖然 ChatGPT 在 DST 任務中表現優異，但其「閉源、無法本地部署、API 限制、資料隱私風險」等缺陷，使其難以應用於實際系統
    - ChatGPT 回答常附帶冗長解釋，影響準確性
  - 既有微調方法對 prompt 設計過於敏感: 傳統指令微調（instruction tuning）若使用固定模板，模型對 prompt 的格式極度敏感，導致泛化能力弱
  - 現有 Dialogue State Tracking (DST) 模型難以泛化至新領域（Zero-shot）: 多數傳統 DST 模型依賴大量標註資料，對未知領域（unseen domains）的適應力差
  - 大型模型微調成本高、資源需求大
  - LoRA 等 PEFT 方法仍需調參與結構設計考量
#### 貢獻:
- 以GPT-3.5做實驗(擷取關鍵資訊)發現：
  - Prompt中若提供範例，對話範例會影響GPT取得關鍵資訊的正確率
  - 同時問多個Slot也會影響準確率
- 提出LDST框架: 基於開源LLaMA模型驅動DST，包含"組合式領域-槽位指令微調(assembled domainslot instruction tuning method)"和"參數高效微調技術(parameter efficient tuning technique)"實現
- 構建指令資料集
  - 以指令微調手法(針對任務設計的明確且具體的指令)來引導模型
  - 指令資料集
      - 以組合式領域-槽位指令生成法（Assembled Domain-Slot Instruction Generation)建構：透過隨機組合不同的指令模板與輸入模板來產生多樣化的指令樣本，讓模型在微調過程中接觸到各種形式的指令，有助於降低其對單一提示樣式的敏感度，提升泛化能力
      - 每一筆資料包含三個欄位：
        -  Instruction（任務指令）：會隨機二選一建構指令
          - 標準槽位追蹤指令（Standard Slot Tracking Instruction）
          - 自訂槽位追蹤指令（Customized Slot Tracking Instruction）：包含更具體的領域-槽位資訊
        -  Input（任務輸入）：由以下4部分組成
          - 對話上下文（dialogue context）：包含特殊區段Token: [USER], [SYSTEM]
          - 領域-槽位描述提示（domain-slot description prompt）：包含特殊區段Token: [domain], [slot]
          - 可能值列表（PVL, Possible Value List）提示：僅用於分類類型的槽位（categorical slots）
          - 查詢提示（query prompt）
        -  Output（期望輸出）
        -  提示詞設計是基於人工經驗的主觀選擇
      - 生成資料時，各有50%的機率生成"欠缺slot描述"/"欠缺可能值列表"的Input prompt
      - ![image](https://github.com/user-attachments/assets/b2355864-ce0e-4919-827f-271fa6407487)
#### 模型
- 以PEFT方法(LoRA)作微調
- 基底模型: LLaMa 7B
- 可學習參數：8.4M (總參數量的0.12%)

#### 表現
- ![image](https://github.com/user-attachments/assets/886bd8f2-6b1e-48bd-bd7d-a04cc6812eb4)
- ![image](https://github.com/user-attachments/assets/94831723-f0dd-4034-a7fc-a86a2ee7de02)
- ![image](https://github.com/user-attachments/assets/120e5116-720d-4604-92b4-8f77d30c3ff6)
- ![image](https://github.com/user-attachments/assets/d5a6a37a-8f5d-4050-9e95-15c37f627621)

- Error Propogation議題
  - ![image](https://github.com/user-attachments/assets/d8278b4d-52c6-4573-8354-00acf769d811)
  - LDST 的下降速度遠低於 LLaMa 與最佳基線方法，顯示其對錯誤傳遞的抵抗能力更強
- 其他議題：
  - 當對話或描述內容過長時，如何有效截斷或摘要化輸入內容亦成為一項挑戰
    
### [(2024)Enhancing Dialogue State Tracking Models through LLM-backed User-Agents Simulation](https://arxiv.org/abs/2405.13037)
- slot分兩種
  - 類別型（categorical）：有一組候選值（例 <hotel-parking> = “True”）
  - 非類別型（non-categorical）：值為對話中的一段文本 （例 <hotel-name> = “Alexander”）
  - 若對話中未提及特定欄位，則該欄位的值設為 “NONE”
#### 要解決的問題
- 如何在缺乏大量真實標註對話資料的情況下，有效訓練出適應多領域、可泛化的對話狀態追蹤（DST）模型
  - 對話狀態追蹤（DST）所需的標註資料成本高昂
  - 真實情境資料有限，缺乏足夠的資料會影響 DST 模型效能
  - 現有模型難以快速適應新領域: 多數 DST 模型依賴固定本體（ontology），泛化能力有限，無法即時拓展至未見過的任務或場景
  - 模擬對話生成的品質與一致性難以保證
  - 混合真實資料與生成資料時，可能出現資料分布不一致問題
    
#### 貢獻
- 提出LLM 支援的使用者–代理人模擬LUAS : 使用GPT-4模擬user和agent互動，生成帶有DST標籤的大量模擬對話，降低對話資料的收集與標註成本
- 生成了近 8,000 筆新對話資料，包含
  - User&Agent對話
  - 對話意圖(雙方可控制意圖)
  - 對應Slot
- 確保生成資料正確性並維持多樣性
  - 透過結構化Prompt控制生成的對話，避免生成的話題有偏差
  - 加入Slot Extraction檢查生成的對話內容Slot與Valut是否一致，避免生成的資料存在Error Propogation情況
  - 實作改寫模板擴增語言多樣性，避免過度重複
- 然後用這些合成數據對LLaMA 2模型進行兩階段微調，在兩個資料集上，加入生成資料可明顯提升模型效能
- 提出兩階段微調，避免生成資料與真實資料混和一起訓練模型導致資料分布不一致，避免訓練偏移 
  - 第一階段：使用生成的對話資料對 LLaMA 2 模型進行微調，學習基本的任務導向型對話模式
  - 第二階段：以真實對話資料繼續微調模型，協助模型對齊真實語料的語言分布，提升其在實際應用中的效能
  - ![image](https://github.com/user-attachments/assets/b69bfb33-99cc-472d-addc-071be3cce969)


#### LUAS
- 首先，LLM 會生成一個使用者設定檔，描述該使用者對各項任務的偏好(包含如預算、距離等具體條件
- LLM 被提示去模擬使用者與代理人之間的對話：
  - 使用者模擬器
    - 對話初始：提出需求並尋求推薦或協助預訂與購買
    - 後續對話：評估需求是否被滿足，並決定是否繼續對話
  - 代理人
    - 理解使用者需求、提供建議並採取適當行動
- 透過這樣反覆的對話模擬，加上由 LLM 所驅動的欄位提取器，得以產出大量標註過的多輪對話資料
- 將使用者與代理人的共通意圖進行抽象，並針對每種意圖設計專門提示語
  - 使用者意圖: Inform Requirement, Update Requirement, Ask for Recommendation, Inquire Properties, Ask for Action, General Chat
  - 代理人意圖: Inquire, Report Search Results, Recommendation, Answer, Report Action Result, General Chat
- 模擬器也會被提示生成控制識別碼（control identifiers），標記該回應所對應的意圖。根據輸入的控制識別碼，模擬器需選擇適當的意圖並生成相應的回覆
- 使用一個**欄位追蹤模組（slot tracking module）**來記錄哪些欄位已被填寫、哪些仍待補全
- 使用一個基於 GPT-4 的欄位提取模型，以驗證生成的對話與欄位填寫結果是否一致，若發現不一致，該段對話必須重新生成，以維持語義與流程的一致性
- 多樣性
  - 為確保生成資料具備豐富的語言變化，我們手動設計了 10 組改寫模板
  - 交由 GPT-4 擴充為數百組變化形式，用於提升使用者與代理人回應的語言多樣性
- ![image](https://github.com/user-attachments/assets/14c8e24b-9b08-445f-95b8-3628f6b4d52a)
- ![image](https://github.com/user-attachments/assets/f7addc90-c860-4b6d-a9f6-c77466f62fca)

#### 模型
- 資料生成：gpt-4-1106-preview
- 基底模型：LLaMa 2 7B
- 基準模型：LUAS-R，以真實資料對 LLaMA 2 進行微調
- 目標模型：LUAS-R+G，將生成資料與真實資料一同透過兩階段微調手法來微調LLaMA 2
- 算力：8 張 Nvidia A100（80GB）GPU，並透過 PyTorch 的 FSDP 框架（Zhao et al., 2023）進行全參數監督式微調(每張 GPU 的 batch size 為 8)
- 微調學習率：2e-5
- 採用 Adam 優化器（Kingma and Ba, 2015），設置參數為 β1 = 0.9，β2 = 0.999，warm-up 比例設為 3%。每個微調階段約持續兩小時。推論階段則使用 vLLM（Kwon et al., 2023）執行

#### 替代實驗(用生成資料替代真實資料)
- 在 MultiWOZ 2.2 上針對不同領域進行資料替換實驗
- 特定領域的所有對話片段移除，並將新生成的資料插入至被移除的位置
- 替換後的新訓練資料集中，將包含 1 個使用生成資料的領域與其他 4 個使用真實資料的領域
- 即使部分資料以生成內容替代，性能下降幅度相對訓練資料的減少而言非常輕微，表示生成資料在新領域中能有效協助模型適應並維持良好預測準確率
- 生成資料能有效緩解資料不足所帶來的限制
- 
#### 缺點
- 生成資料品質的客觀驗證不足
  - 沒有語言流暢性評估(BLEUM ROUGE, BERTScore)
  - 沒有人工評估生成資料流暢性、合理性、任務完成度
  - 沒有人工評估比較生成資料與真實資料差異&相似度
  - 宣稱的自我檢查機制(Slot Consistency)只透過Slot Extraction來驗證檢查使用者提供的 utterance 與填入的 slot values 是否一致，但這只是內部一致性驗證，沒有進行外部正確性評估
- 模擬資料雖高品質，但仍非真實對話語料，對話多樣性是個問題
  - 改寫是否真的涵蓋語義變異與語用多元性，未有實驗數據或對比說明
  - 缺乏與其他資料增強策略（如 paraphrasing, back-translation）之比較
- 跨領域遷移的挑戰處理有限
  - 論文主要測試在 MultiWOZ 內部領域（如餐廳、旅館）中做資料替換，並未測試在外部新領域（如法律、醫療）上的泛化能力。
  - 若任務 ontology 差異太大，GPT-4 的模板模擬仍可能無法涵蓋新領域的實際語境需求。
- 未深入分析錯誤案例
  - 不清楚哪些Slot類型最易出錯
  - 是因為llm幻覺? entity overlap ? 還是多輪推理導致?

### [(2024)Large Language Models as Zero-shot Dialogue State Tracker through Function Calling](https://arxiv.org/abs/2402.10466)
#### 要解決的問題
- 多數方法將 DST 視為獨立任務處理，無法自然整合於 LLM 的聊天流程中
  - **DST 所需的標註資料成本高昂**
  - **現有模型難以實現 Zero-shot 效能**: 有研究試圖以少量或跨領域資料進行遷移學習，但對於「未見領域」的泛化能力仍不理想
  - **ChatGPT 類模型的 DST 表現有限**: 在處理 DST 任務時，需特殊提示與格式規定，導致實務應用困難
  - **現有 Prompting 方法無法整合 DST 與回應生成**: 多數方法將 DST 視為獨立任務處理，無法自然整合於 LLM 的聊天流程中，不符合真實 TOD 系統需求
  - **開源模型難以達到商業模型的效能**: 中等規模開源模型（如 LLaMA、Vicuna）在缺乏函數呼叫能力與 DST 訓練的情況下，無法與 GPT-4 等進階模型競爭
#### 貢獻
- 提出 FNCTOD：以函數呼叫實現零樣本對話狀態追蹤（Zero-shot DST）
  - 將 DST 任務視為「函數呼叫」問題
  - 每個任務導向領域（如餐廳、旅館）被建模為一個函數，對話狀態（slot-value pairs）作為該函數的參數
  - 將函數定義（function specification）嵌入系統提示（system prompt），讓聊天型 LLM 能在回應中自然產出函數呼叫與對話回應
  - 使用少量異質資料微調 LLaMA2，達成與 ChatGPT 相當表現
    - 使用來自 36 個領域、7,200 筆對話資料（非 MultiWOZ）對 LLaMA2-13B 進行微調
    - 微調後的模型 FNCTOD-LLaMA2-13B 可產生函數呼叫與自然語言回應，其 DST 效能與 ChatGPT 相當，具備實用價值
    - 讓 7B–13B 模型超越過往 ChatGPT 達成的 SOTA，且 GPT-4 表現提升達 14%
- 提出兩階段函數呼叫生成流程，避免每輪對話都需重複載入完整函數規格，提升效能與成本效率
  - 階段一：函數選擇
  - 階段二：參數生成
  - 減少 token 數量（API 成本）同時提升預測準確率
- 在多個中等規模&**未經微調**開源模型上達成或超越現有 SOTA
  - 在 未經微調的 7B/13B 開源模型（如 Zephyr、Baichuan、LLaMA2）上，透過情境提示（in-context prompting）即可超越先前只能用 GPT-4/Codex 實現的 SOTA 表現
  - 尤其 GPT-4 在本方法下比原先 SOTA 提示方法提升 5.6% 的 JGA
- ![image](https://github.com/user-attachments/assets/01b7e0a1-1bf9-4159-95c3-2a8b3ad5c6f1)
- ![image](https://github.com/user-attachments/assets/8cf17d1f-829a-4ad2-8a81-c3f74f49c22f)
#### 缺點
- 現階段的 DST 準確率尚不足以實際部署
  - 雖然 FNCTOD 在多個模型上達到新 SOTA，但實驗中仍指出目前的準確度仍不足以應用在實際商業系統中
  - 在多輪長對話或複雜意圖中，仍可能出現錯誤的函數呼叫或漏判slot-value
- 評估TOD 回應生成品質的方法不夠全面，無法對應真實情境
  - TOD 中的回應品質僅使用 去詞彙化（delexicalized）回應評估法，這種方法無法全面反映 LLM 回應的自然性與可用性
  - 此評估方式容易被「格式正確但語意不自然」的回應作弊，與 LLM 訓練的真實分布不符
- 函數名稱錯誤可能導致連鎖錯誤
  - 雖有提出「函數選擇 + 參數生成」的兩階段策略，但若第一階段選錯函數，會影響後續生成參數的準確性
  - 「階段性錯誤累積」的情況會影響最終 DST 成效，特別在開源模型上更明顯
- Zero-shot 能力仍需仰賴 prompt 設計或微調
  - 對於未經微調的開源模型，若沒有提供足夠的 in-context 範例，效果會明顯下降
  - 雖然聲稱 zero-shot，但實務上仍需謹慎 prompt 設計與函數格式調整，否則效果不穩定
- 缺乏對多輪推理任務的處理能力討論
  - 雖整合函數呼叫與 DST，但未進一步探討是否能擴展至更高階任務（如多輪規劃、推理、目標分解）

### [(2025) Interpretable and Robust Dialogue State Tracking via Natural Language Summarization with LLMs](https://arxiv.org/abs/2503.08857)
#### 要解決的問題
- **結構化輸出(slot-value)缺乏對複雜語境與使用者意圖的表達力**
  - **難以捕捉多輪對話中的語境轉換與模糊、隱性的使用者需求**
  - 高度依賴人工設計規則與本體維護成本高
  - 結構化 DST 輸出格式可解釋性差
  - 多數 DST 方法無法應對使用者輸入中常見的錯字、語病或異常用語
#### 貢獻
- 提出了 自然語言 DST（NL-DST） 框架，訓練 LLM 直接產出可讀性高的"人類語言狀態描述"取代Slot-Value
- 微調模型的學習策略
  - Teacher Forcing: （讓模型看前一句來預測下一句）
  - Beam Search: （保留最可能的一群詞序列）
  - Nucleus Sampling: （只從機率前幾名的詞中選字）
#### Baseline模型
- Rule-based Slot-Filling DST
- Bert-based DST
- GPT-2 DST
#### 評估指標
- JGA
- Slot Accuracy
- Relevance 相關性 (人工評估)
- Informativeness 資訊性 (人工評估)
#### 缺點
- 沒揭露用來訓練模型學會任務(生成具高可讀性的"人類語言狀態描述")的資料集具體內容
  - 只提到資料集內的"人類語言狀態描述"是由語言專家人工標註，確保其品質與準確性
- 沒揭露所謂的高可讀性的"人類語言狀態描述"須包含哪些內容
  - 自然語言輸出容易冗長、重複、偏離重點，但論文未討論：
    - 如何控制生成摘要的長度
    - 如何確保輸出資訊的精確性與聚焦度 
- 沒揭露基底模型 
