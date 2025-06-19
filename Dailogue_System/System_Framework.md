# 快速索引
- [(2025)LLM-Friendly Knowledge Representation for Customer Support](https://github.com/TroisLiu/research/blob/master/Dailogue_System/System_Framework.md#2025llm-friendly-knowledge-representation-for-customer-support)
  - ICA + CoT
- [(2025)Conversation Routines: A Prompt Engineering Framework for Task-Oriented Dialog Systems](https://github.com/TroisLiu/research/blob/master/Dailogue_System/System_Framework.md#2025conversation-routines-a-prompt-engineering-framework-for-task-oriented-dialog-systems)
  - 對話慣例（Conversation Routines, CR） 
- [(2024)Chatbot Meets Pipeline: Augment Large Language Model with Definite Finite Automaton](https://github.com/TroisLiu/research/blob/master/Dailogue_System/System_Framework.md#2024chatbot-meets-pipeline-augment-large-language-model-with-definite-finite-automaton)
  - DFA
  - 每個應用情境資料可以建構一個自己的DFA決策樹
- [(2024)On Overcoming Miscalibrated Conversational Priors in LLM-based Chatbots]()
  -    

# 概述
- 探索一個可行的架構實現對話管理

## 核心議題

## 研究議題
- 結構化對話系統（Structured Dialogue System）
  - 這些系統在穩定性與一致性方面表現良好
  - 面對突發問題或非預期查詢時缺乏彈性。 
- 檢索式增強生成（Retrieval-Augmented Generation, RAG）
  - 解決了傳統 LLM 的兩大限制：知識更新困難與推理過程不透明
- 任務導向對話系統（Task-oriented Dialogue System）
  - 方法1: 依賴於對話狀態標註來訓練策略導向的生成模型
  - 方法2: 簡化設計，直接訓練端到端（E2E）模型

## 資料集

## 評估指標

## 論文：
### [(2025)LLM-Friendly Knowledge Representation for Customer Support](https://aclanthology.org/2025.coling-industry.42.pdf)
- 投稿單位：airbnb
- 研討會：2025COLING
#### 要解決的問題
-  如何讓大型語言模型（LLM）能夠有效理解並執行企業內部複雜且非結構化的客服工作流程知識？
  - **業務知識結構複雜、不利於LLM理解**: 企業內部的知識（如政策、工作流程）往往以富文字、表格、樹狀說明等混合格式呈現，內容長且用語複雜，不利於LLM解析與推理
  - **大型LLM推理速度慢、延遲高**: 雖然大型模型具備高品質理解與生成能力，但推論速度慢、成本高，不適合即時客服場景
  - **缺乏高品質訓練資料**: 客服知識常是隱性經驗，不易轉為明確訓練樣本，導致無法有效訓練LLM
  - ![image](https://github.com/user-attachments/assets/a73de49f-2dbd-4c95-b968-8c62f098f7a8)

#### 貢獻:
- 提出結構化業務知識結構： ICA（Intent, Context, Action）格式
  - 客服工作流程（如富文字、表格、HTML）-> 類似「偽程式碼」的 ICA 樹狀結構 
- 設計一個合成資料生成流程（Synthetic Data Synthesis）
  - 利用現有知識庫與歷史查詢紀錄，自動產生大規模 ICA 工作流程的訓練資料
  - 搭配思維鏈（Chain of Thought, CoT）生成推理步驟，強化模型的邏輯能力與透明性
- 驗證小型開源 LLM（如 Mistral-7B）透過 ICA + 微調可達大型模型效能
  - 實驗證實：
    - 單純使用 ICA + CoT，大模型能有相當準確率，唯一缺點是高延遲
      - 實驗匿名模型可到9成
      - 適用非即時情境 
    - 使用 ICA + CoT & 微調後，小模型可接近大型封閉模型的準確率（如從 57% 提升至 85~86%），且延遲顯著更低。 
  - 適合部署於即時客服應用，兼顧效能與成本
- ![image](https://github.com/user-attachments/assets/8c0c2210-f2ce-47f5-aa79-3c81e038cb0c)

#### 模型
- 2個匿名LLM
  - Model1, Model2
- Mixtral-8x7B
- Mixtral-7B

#### 資料結構
- ICA格式
  - Intent 意圖
  - Context 情境
  - Action 動作
- 中介決策樹
  - 一個工作流程可以轉換為樹狀結構：
    - 根節點 表示與「意圖（Intent）」相關的條件；
    - 中間節點 表示與「情境（Contexts）」有關的條件；
    - 葉節點 則代表最終要執行的「動作（Actions）」
  - 根節點與中間節點的條件對應到 if-else 判斷
  - 葉節點則對應到 then-do 的執行區塊
- 合成資料
  -結構
    - User Query
    - 上下文資料
    - Pseudocode(轉換成ICA格式的候選工作流程)
    - CoT：回應中的推理過程
    - 最後要執行的Action
  - ![image](https://github.com/user-attachments/assets/60c3431b-6422-4adc-8f7e-e3148441b4ec)
#### 資料處理
- 整體步驟
  - 使用分類器從原始資料抽取出I/C/A，建構中介決策樹
  - 建立Action Map，在合成資料、實際線上預測時，讓LLM都只產生動作ID(簡化生成內容)，系統再針對ID做替換
    - 提升準確率
    - 降低延遲 
  - 再轉換成Pseudocode
- Raw2ICA
  - 原始資料為HTML結構
  - 使用了 HTML 分析工具（如 BeautifulSoup（Richardson, 2004）與 lxml（Behnel, 2005））將內容拆解並抽取，同時保留 XML 樹中的階層關係
  - 使用一個基於人工標註資料訓練的二元分類器對抽出來的關鍵資料進行分類
    - 「意圖/情境」的條件說明
    - 「應採取的動作」之描述
- 合成訓練資料
  - 建構2種資料集
    - 條件資料集：由內部知識庫中擷取的「意圖」與「情境」條件集合
    - 查詢與情境資料集：來自過往對話記錄與 API 返回資料中（已進行匿名化與敏感資訊移除）的使用者查詢與情境資料
  - ![image](https://github.com/user-attachments/assets/c1e449c7-d254-4361-9582-289795e1f67b)
  - 步驟
    - **合成一組對應分支（matched branch）**：隨機抽樣使用者查詢、一組情境資料，以及一個與之匹配的決策樹分支（包含一個意圖條件與多個情境條件）。
    - **合成分歧分支（divergent branches）**：
      - 為幫助訓練推理能力，額外產生一些與查詢/情境不符的分支。
      - 分支可透過變造 matched branch（如根節點變動需建新樹）或加入完全無關的分支來構建。
    - **合成思維鏈（CoT）**：為幫助 LLM 理解動作預測背後的推理邏輯，我們將「匹配分支的節點」與「造成不匹配的分支節點」之描述列出，構成推理過程說明。
    - 轉為 ICA 格式並建立 SFT 樣本：將決策樹轉為 ICA 格式後，將「使用者查詢、情境與 ICA」置於提示詞（prompt）中，將「推理過程與動作」置於標註（label）中，形成一筆 SFT 訓練樣本。
#### 評估指標
- 準確率（Accuracy, ACC）：預測正確的案例數除以總案例數。
- 平均延遲時間（Average Latency, AL）：模型產出回應所需的平均時間。
- 平均人工處理時間（Average Manual Processing Time, AMPT）：線上實驗中評估客服處理時間的指標，用以衡量模型是否能有效減少人力成本。
#### 表現
- 微調前
  - ![image](https://github.com/user-attachments/assets/9077eed3-62fc-4aca-8dca-2ea0216e6fc9)
  - 以 Model 1 為例，若不採用 ICA 與 CoT，基線準確率為 57%
    - 單獨加入 ICA 格式，可提升 13%；
    - 再加上 CoT，可再提升 8%；
    - 同時使用兩者時，整體可提升 25%
  - ICA 格式在所有模型中均明顯優於原始富文字格式
  - CoT 在兩種格式下也進一步提高準確率
- 微調後
  - ![image](https://github.com/user-attachments/assets/80996358-89c6-4e16-a0e2-4b017d1c134f)
  - Mixtral-8x7B 與 Mistral-7B 兩個模型進行微調，可將準確率提升至接近大型模型的水準（85%、86% 對 89%、92%）
  - CoT 雖提升準確率，卻會顯著增加延遲
    - 對大型模型而言不利於即時應用
    - 小型模型（如 Mistral-7B）幾乎不受此限制，延遲約為大型模型的十分之一
  - 小模型具備顯著較低的延遲，適合實務應用
- 線上測試
  - ![image](https://github.com/user-attachments/assets/6c62d7af-119a-4ff3-a24f-378278ec15d2)
  - 使用微調後的 Mistral-7B 搭配 CoT，可降低 AMPT 13%。
  - 雖然 Model 1 的預測品質更高，但加入 CoT 後因延遲過高，AMPT 反而上升；若不使用 CoT，AMPT 僅下降 3%。
  - 然而，不使用 CoT 將導致準確率顯著下降，造成較多錯誤決策。

#### 缺點
- 對 ICA 格式轉換的成本與可維護性討論不足
  - 雖提到 ICA 轉換需數月並仰賴人工知識撰寫者，但對實際轉換所需人力、技能門檻、錯誤率控制機制並未進行量化或深入分析。
  - 缺乏對 新知識加入時的維護成本與效能衰退風險的探討。 
- 合成資料 vs 真實資料的泛化性問題未充分討論
  - 合成資料雖有效提升模型理解 ICA 格式，但未評估其在面對真實語料中的誤解風險與語意多樣性適應力。
  - 缺乏對於合成資料品質&與真實資料偏差的相關分析
- 缺乏對使用者端體驗的質性評估
  - 雖有以 AMPT 測量人工作業時間，但沒有使用者回饋調查（如客服人員滿意度、錯誤修正次數、信任度）
  - 也沒有定性訪談佐證模型輔助是否真對實務工作流產生正面影響。
- 模型選擇與比較基準略嫌受限
  - 僅選擇兩個開源模型與兩個匿名大型模型作比較，並未說明為何未納入更近期的 SOTA 模型（如 LLaMA2-13B等）。 
- 缺乏跨領域（如法務、金融）實證應用
  - 雖聲稱方法可推廣至複雜領域（如法律、金融），但目前僅限於 Airbnb 客服知識，外部遷移性與通用性仍待驗證。
  
### [(2025)Conversation Routines: A Prompt Engineering Framework for Task-Oriented Dialog Systems](https://arxiv.org/pdf/2501.11613)
#### 要解決的問題
- 如何讓大型語言模型（LLM）在不需複雜程式設計或模型微調的情況下，可靠且可控地執行複雜的任務導向對話流程
  - 傳統對話系統開發困難與成本高昂
  - LLM 雖具語言理解力，但缺乏業務邏輯控制能力
  - 現有系統難以讓非工程背景者（如領域專家）參與設
#### 貢獻
- 提出「對話慣例（Conversation Routines, CR）」框架
- 賦予 LLM 「類程序化控制能力」
- 建立人機協作的責任分工模式
#### 對話代理系統（Conversational Agentic Systems, CAS）
- 一種專門用於聊天互動的 AI agent，它結合了 LLM 的對話能力與結構化函式呼叫（Function Calling），以執行特定任務與工作流程
- LLM通常需要微調學習
- 這些函式必須能被 LLM 順利調用。最佳實作方式是設計**無狀態（stateless）**函式
  - LLM 每次調用時都以參數傳遞必要資訊，這符合函數式程式設計原則，並讓 LLM 負責狀態維持。
- 若需實作有狀態（stateful）函式，則必須額外設計共享記憶或上下文維護機制，例如將狀態資訊嵌入 prompt，才能在多次呼叫間保留資訊一致性。 
#### 對話慣例（Conversation Routines, CR）
- 將業務邏輯以自然語言方式嵌入提示中，由 LLM 自主依照說明進行互動管理
  - **程序邏輯嵌入對話慣例（Conversation Routines, CR）**
  - 聚焦於透過自然語言（low-code／類似偽碼）的系統提示來編碼業務邏輯，讓 LLM 作為工作流程的解釋器
- **Routine（慣例）**
  - 最早由 OpenAI 在其實驗性開源框架 SWARM 中提出
  - 一組以自然語言撰寫的指令清單（系統提示），以及執行這些步驟所需的工具
  - 系統提示就像一份完整的軟體需求文件
#### 系統提示
- 用自然語言撰寫，但其結構是有明確格式的，以幫助 LLM 理解
- <img width="310" alt="image" src="https://github.com/user-attachments/assets/5717d22e-1962-4846-b3e5-9b754666bbb3" />

##### 身份與目的（Identities and Purpose）
- 提示的基礎在於清楚界定**Bot的身分與目的（即 bot-persona)**，以及**使用者的身分（user-persona）**
- 
##### 函式整合協定（Functions Integration Protocol）
- 在系統提示中定義代理該如何使用外部工具（函式）。將函式說明也納入系統提示中，能讓代理理解技術能力與語境適用性
- <img width="523" alt="image" src="https://github.com/user-attachments/assets/946aa1a4-b4ea-4bde-84ec-18b5986c413e" />

##### 工作流程控制樣式（Workflow Control Patterns）
- 代理行為的核心在於工作流程樣式，它們構成決策順序與行動流程的基礎
- 關鍵樣式
  - sequential steps with conditionals
  - iteration logic
  - user confirmation protocols
##### 條件式的順序步驟（Sequential Steps Including Conditionals）
- 透過系統化縮排（類似 Markdown 或 YAML）來表達層級與邏輯。
- **縮排**代表行為邏輯的深度
- <img width="159" alt="image" src="https://github.com/user-attachments/assets/cf68f466-01a1-4807-86c1-99f4b0650a30" />

##### 迴圈邏輯（Iteration Logic）
- 迴圈控制對於處理多輪交互與重試流程非常重要
- <img width="403" alt="image" src="https://github.com/user-attachments/assets/5754a306-b25f-48f2-b325-6ba967309d5a" />

##### 使用者確認協定（User Confirmation Protocols）
- 為避免誤解，在重要節點需使用者明確確認
  - <img width="455" alt="image" src="https://github.com/user-attachments/assets/066b2903-eb15-4b89-a439-ec07be598e66" />
- 若流程關鍵階段需要明確回應
  - <img width="440" alt="image" src="https://github.com/user-attachments/assets/2408fefd-b290-45cc-9458-3d9c9f3d5d2d" />

##### 輸出格式與語氣（Output Format and Tone）
- 維持一致的語調與表達方式
- <img width="286" alt="image" src="https://github.com/user-attachments/assets/83a17d9f-04cc-42b9-b3cc-7e40f4195104" />

##### 對話範例（Conversation Examples）
- 可以加入 one-shot 或 few-shot 對話範例
- <img width="425" alt="image" src="https://github.com/user-attachments/assets/4e3c884a-8758-4ffa-bc0b-9ec456092d2b" />

#### 模型
- openai GPT-4o-mini
  - 128K token 的長語境視窗
  - function calling 能力
  - 低延遲特性
#### User Case
- 每個應用皆採用 OpenAI SWARM 框架
- <img width="250" alt="image" src="https://github.com/user-attachments/assets/b6e24256-0bc6-4248-8115-29833f012ffb" />

##### 火車票訂票系統
- 本使用案例展示一個示範場景，說明 CAS（對話代理系統）如何協助用戶完成火車票購買。
  - 該代理整合了後端函式，可執行以下任務：
    - 查詢當前時間
    - 搜尋車站名稱資料庫
    - 判斷指定出發地與目的地之間的可行車次
    - 依照預定格式產生訂票結果
- 關鍵挑戰：**如何設計一個能涵蓋整體業務邏輯的提示（prompt），以確保訂票流程順暢**
  - LLM須能做到：**收集與驗證使用者資訊 → 呼叫後端函式 → 完成訂票與付款**
- 此對話實例證明了代理：
  - 高效處理複雜對話邏輯
  - 動態應對模糊輸入
  - 在多輪對話中維持狀態與邏輯流程一致性
- 經過超過 50 次測試運行，在幾乎所有情況下都能成功完成符合使用者需求的訂票任務。
  - 即使故意輸入不合作內容（如不存在的車站名），系統仍能維持確認機制，確保最後提交資料正確
  - 有時需額外輪次解決模糊問題
  - 即使在 prompt 中明確要求避免格式化，LLM 偶爾仍會輸出 markdown 格式的文字
##### 互動式故障排除
- 目的是**在故障診斷與維修流程中，協助技術人員進行輸送系統的故障診斷與維修，持續提供互動式指引**
  - 助理的角色被定義為「專家型」
  - 核心功能包括：擷取故障排除步驟、提供零件資訊、產生維修報告等
- 關鍵挑戰：**如何讓模型理解並執行特定的業務流程（如維修步驟）**
- 共有兩個代理
  - 故障排除助理代理（Troubleshooting Assistant Agent）
  - 報告生成代理（Troubleshooting Report Agent）
- 流程分四階段：
- 
#### 缺點
- 缺乏系統性的量化評估
- LLM 非決定性造成流程穩定性風險
- 函式與提示耦合，難以擴展與重構


### [(2024)Chatbot Meets Pipeline: Augment Large Language Model with Definite Finite Automaton](https://arxiv.org/pdf/2402.04411v1)
- 投稿單位：NEC Lab
- 研討會：arXiv
#### 要解決的問題
- 如何讓大型語言模型LLM在需要嚴格遵守流程與規則的應用場景中（如客服、情緒支持）生成穩定、可控且合規的回應，同時保有其語言生成的靈活性？
  - LLM缺乏結構性控制
  - 微調成本高、不靈活
  - 情境內學習（In-Context Learning, ICL）檢索樣本難以控制: ICL效果取決於樣本選擇，但如何挑選與當前對話最相關的歷史範例
  - 無法追蹤與解釋對話決策流程
#### 假設
- 訓練對話資料中隱含了特定的工作流程
  - 在真實情境中，人類代理人(真實客服)往往會根據一套預定的選項、經驗法則與潛在結果來給予回應
  - 可視為在一個由多個路徑與交會點所構成的網絡中，探索某條特定分支
- 每個語句都可以抽象為一組「關鍵詞」或「標籤（tags）」
  - 例：句子(為什麼我的手機只能用一個小時？) -> 標籤集合(#issues, #battery)
#### 貢獻
- 本論文首度結合 DFA 與 LLM，提出可解釋、流程導向、可遷移的對話生成框架 DFA-LLM，實證其在實務任務中能有效提升對話品質與控制性
  - DFA (deterministic finite automaton) 
  - 讓LLM 生成回應時，**遵循DFA指定的流程路徑**: DFA 是從訓練對話中自動學習而來，能以結構化方式建模對話流程與狀態轉移
  - 把對話內容抽象為「語義標籤（tags）」，進而建構DFA
  - 實作與實驗：在多個資料集上驗證 DFA-LLM 的效能
  - 支援即插即用：不需微調，適合實際部署
#### DFA-LLM
- 將一個結構化的流程嵌入到 LLM 的操作架構中。透過整合 DFA（確定有限自動機），我們希望能更貼近人類代理人在決策路徑上的行為方式，引導 LLM 的回應生成
- 每個狀態(Q)會維護一個對話編號集合（dialogue IDs），紀錄有哪些對話經過該狀態
##### 傳統DFA定義
- ![image](https://github.com/user-attachments/assets/48f04c59-331f-47e0-9175-b8fa8d5a2ca2)
- ![image](https://github.com/user-attachments/assets/f57624db-3ded-4090-9732-e9827ab4b035)

##### DFA架構定義
- 將每則對話可轉換為標籤序列，整體對話集則可建成一個 DFA
  - 狀態 (Q)：代表對話的某一階段，如開場、提問、回應、結束等
  - 字母表 (Σ)：由所有可能的標籤構成
  - 轉移函數 (δ)：描述特定標籤如何使對話從一個狀態轉移到另一個狀態
  - 起始狀態 (q₀)：代表對話的開端，如問候語或首個提問
  - 接受狀態 (F)：表示對話已完成，如問題解決或自然結束
- 
##### 從對話中學習DFA
- Step.1 使用 LLM 擷取標籤序列: 設計提示語（prompts）引導模型遵循兩項準則
  - 簡潔性：每個標籤應盡量簡短，最多三個字
  - 聚焦主題：強調對話中的事件、問題、詢問或解決方案
- Step.2 使用標籤構建樹狀結構: 建構原則
  - 以標籤**出現頻率**為排序原則，使常見標籤優先靠前，減少樹的大小
  - 區分對話輪次（user vs. system），並在處理時僅統計同一輪次的標籤
  - 從q₀開始建構
- Step.3 樹狀結構的狀態合併
  - 計算任意兩狀態間的相似度分數 φₛᵢₘ(q, q′)，減少重複子序列(例: #link → #thank)
  - ![image](https://github.com/user-attachments/assets/3ed97e61-0fe4-4ec9-84b7-62fc33cbb080)
  - 設定閾值 λ，將相似度超過 λ 的狀態對合併，以減少冗餘
- 演算法示意圖
  - ![image](https://github.com/user-attachments/assets/594bb755-fc9f-4a0c-9a65-824d51d163e3)
- 流程示意圖
  - ![image](https://github.com/user-attachments/assets/e1164b1f-7560-444e-a616-f29c677db6e8)
- 建構後的DFA示意
  - ![image](https://github.com/user-attachments/assets/9328ee9d-a7c6-4d74-9520-3837be13a56f)
    - 在自動機的起始點，可見多條通往「train（火車）」、「hotel（飯店）」與「taxi（計程車）」等主題的分支，反映這些為資料集中出現頻率最高的服務場景。
    - 「restaurant」與「hotel」等子領域時，會發現標籤如「price（價格）」、「name（名稱）」與「area（地區）」反覆出現，顯示這些為使用者常見詢問內容。
    - DFA 中的相同狀態會根據使用者輸入與系統回應的不同，引導出不同分支。例，若系統成功處理詢問，則可能輸出具體地址；若找不到對應結果，則會轉向標示為「no」的分支 
##### DFA與LLM整合，生成回覆
- Step.1 標註使用者語句：LLM 將使用者輸入轉換為標籤（與訓練時一致）
- Step.2 DFA 導引流程：根據標籤，在 DFA 中導引至對應狀態
- Step.3 擷取對話樣本：每個狀態 q 對應一組對話 ID（I(q)），代表曾走過相似路徑的歷史對話
- Step.4 構造 Prompt 給 LLM：從中取樣 5 筆對話，構成 ICL 用的提示
- Step.5 生成回應：LLM 根據提示生成新的回應
- Step.6 重複迴圈：使用者繼續對話，回到步驟1
#### 資料集
- 資料集
  - AmazonHelp
  - DeltaSupport
  - AskPlayStation
  - AirbnbHelp
  - NikeSupport
  - CambridgeInfo
- 來源
  - Tweet
  - MultiWOZ

- ![image](https://github.com/user-attachments/assets/f5d759cc-ca66-40d8-a31d-3adb11dae6d5)
  
#### 模型
- GPT-3.5
- GPT-4
#### 比較方法
- RandSamp: 隨機從訓練集中抽樣
- BM25: 傳統稀疏檢索法，尋找與測試語句關聯性高的訓練句子
- RAG: 使用 text-embedding-3-small 產生句子向量，並檢索與測試語句最相似的訓練樣本
- FT-LLM
#### 實驗
- 進行方式
  - 除了FT-LLM，另外4個都採用ICL，推論時都使用5個樣本作Few-shot Learning
  - FT-LLM使用 GPT-3.5 API 進行標準參數設定下的微調（fine-tuning）
  - 採用 GPT-4 作為主要評估模型 (詳見附錄 C)
#### 評估
##### 回應品質評估（Generation Quality Evaluation）
- 評估目標: **判斷哪個對話更接近「真實答案」（ground truth）**
- 評估指標: Win Rate 對話生成勝率
- 結果
  - ![image](https://github.com/user-attachments/assets/d53c1d6a-91ff-4449-8237-544a8f19d564)
    - FT-LLM 效能不佳，可能因如 NikeSupport 等資料集規模有限
    - RAG 效能優於 RandSamp 與 BM25，顯示其樣本檢索更準確
    - DFA-LLM 勝率高於所有基線方法約 4%，且提供具可解釋性的結構，便於理解對話流程 
##### 任務導向對話評估（Dialogue Task Evaluation）
- 評估目標: **是否成功解決使用者問題**
- 使用 MultiWOZ 資料集進行此任務評估
- 評估指標:
  - Inform Rate：衡量系統是否提供足夠且正確的資訊以回應使用者需求
  - Success Rate：衡量是否完成使用者任務（如成功訂房）
- 結果
  - ![image](https://github.com/user-attachments/assets/4f6cd3e6-6c2e-4a76-8c9f-f7ab5b034c12)

#### 優點
- 可解釋的結構：
  - 其他作法：採用基於向量空間融合資訊但缺乏可讀性的 RAG
  - 本篇做法：透過人類可理解的 DFA 結構產生回應，提升了LLM聊天機器人的可信度。
- 具情境感知的檢索能力：
  - 其他做法：將整段對話視為整體的檢索策略
  - 本篇做法：將對話劃分為多個可控片段，這種細粒度方式可更精確地檢索出與當前情境相關的資訊，進而增強聊天機器人在多樣化語境下的回應能力與準確性。
- 即插即用的相容性：
  - 從訓練資料中導出的 DFA 可輕鬆整合至任何預訓練的 LLM，具備高通用性。
#### 缺點
- DFA 的語義抽象可能過於簡化
  - 忽略語句間的語意及語境細節
  - 可能有相似的標籤，但實際對話意圖與應答策略可能截然不同，導致誤導 DFA 導向錯誤狀態
  - 舉例：「#battery」可能指電池損壞、耗電快、充電問題等，但系統視為同一狀態可能會錯誤生成回應
- 標籤擷取過程完全仰賴 LLM，自帶不穩定性與黑箱性
- 缺乏對 DFA 結構學習穩定性的深入分析
- DFA 的結構受到訓練對話數量與標籤頻率的影響，但論文對不同資料量、對話風格下 DFA 構造的穩定性與泛化能力未做深入分析。 
- 與 DFA-LLM 對比的幾個baseline（如 RandSamp、BM25）皆為簡單檢索或隨機樣本選擇，與 DFA-LLM 的結構引導方式在策略複雜度上有差距，這樣比較不完全公平
- 未說明可擴展性與可維護性問題

### [(2024)On Overcoming Miscalibrated Conversational Priors in LLM-based Chatbots](https://www.microsoft.com/en-us/research/wp-content/uploads/2024/06/Overcoming_RLHF.pdf)
- 投稿單位：Microsoft Research
- 研討會：arXiv
#### 要解決的問題
- LLM-based 聊天機器人在面對「資訊不足（under-specified）」的使用者查詢時，容易做出錯誤假設、冗長閃避、或拒絕回應，其回應策略與實際使用者需求不匹配，導致效用降低
  - 資訊不足查詢非常常見 :
    - 在 OpenAssistant 資料集中，超過 25% 的查詢屬於資訊不足（例如條件不清、偏好未明說）
    - ![image](https://github.com/user-attachments/assets/83dfd0f2-37d9-481b-ae9f-92b980f8d9e6)

  - LLM 的預設行為（RLHF）是錯誤校準的（miscalibrated）
    - 微調過程（RLHF）偏好簡單或明確查詢，對於多輪互動或模糊查詢處理不佳
    - 認為標註者偏好冗長回應、忽略澄清提問，使得 LLM 學到「直接回應或模糊敘述」的偏誤策略
    - 無法有效挖掘使用者的潛在目標
#### 假設
- RLHF 微調導致 LLM 偏好「直接回應（respond）」與「模糊回答（hedge）」，而非提出澄清問題（clarify）

#### 貢獻:
- 將資訊不足問題形式化為「部分可觀測決策過程（Partially Observed Decision Processes, PODP）」
- 提出LLM 回應策略類型的分類法
  - ![image](https://github.com/user-attachments/assets/21c38bbf-d960-4799-8245-4974e424bbd9)
  - 描述當模型面對資訊不完整的請求時，其「對話先驗」（conversational priors）行為特徵
  - 從效用與認知成本的角度分析
    - "效用": 根據使用者的潛在目標計算而來
    - 不同的回應策略。這些策略會產生在「認知成本」（x 軸）上不同、且對使用者而言具有不同「有用程度」（y 軸）的回答
    - 一個好的聊天機器人應該能回應出在效用上最大化的答案——也就是既有用又低成本的回答
- 提出兩種提示語誘導策略，改善 LLM 在資訊不足查詢下的回應行為
  - Clarify-Flex（資料不可知型提示語）：根據查詢內容引導 LLM 適時提出澄清問題
  - Meta-Policy（資料導向元策略）：利用對話歷史資料學習一個策略 β：對不同對話上下文選擇合適的提示語
#### 資料集
##### OppenAssistant
- 將研究範圍限制在英文查詢且長度至少為 3 個詞的查詢句子（約佔 10,000 筆對話中的 40%）

#### 部分可觀測決策過程（Partially Observed Decision Processes, PODP）
- 將「使用者-聊天機器人互動」形式化為一個部分可觀測決策過程
- 聊天機器人的策略 π 是從對話前綴（可能跨多輪）映射至自然語言回應的固定函數
- 每一個 PODP 任務
  - 開始於使用者以自然語言查詢 q ∈ Q 表達其目標 θ，但此表達可能是不完整或有資訊遺失的
  - 聊天機器人產生的自然語言回應即為 PODP 的「行動空間」，記為 a ∈ A
  - 使用者的回覆語句則視為「觀測值」，記為 o ∈ O
  - 將使用者與聊天機器人之間的多輪變長對話歷史記為 C := q × [a, o]
  - 對話歷史空間記為 C := Q × [A, O]
  - 對於任一使用者目標 θ 下的對話 C，聊天機器人的任務是**選擇能最大化效用的回應行動**，效用函數為 U: Θ × C → ℝ
    - 此效用函數 U 是未知的
    - 可透過其他方式間接觀察到其樣本值
    - 許多 LLM 聊天機器人允許使用者對對話進行評分，這些評分即可被視為 U(Θ, C)
    - U 的組成可能包含：
      - 隱含因素（如回應長度）
      - 顯性因素（如按讚/倒讚、使用者對比對回應的評分）
    - 實驗：挑選**回應長度 len(a)** 作為使用者在回應 a 上的認知成本的簡易代理指標
    - ![image](https://github.com/user-attachments/assets/8772554f-4f6a-4744-be0c-6613405a9864)
  - 關鍵
    - 平衡資訊探索（exploration）
    - 效用最大化（exploitation） 
##### clarification-aware prompt
##### meta-policy
#### 回應策略分類


#### 模型
- GPT-4

#### 實驗
##### 分類1: LLM-BASED QUERY UNDERSPECIFICATION CLASSIFIER
- 實驗：了解使用者向開放領域 LLM 聊天機器人發出「資訊不足查詢」的頻率
- 建立一個LLM-based分類器
  - 判斷每筆查詢是否為「資訊不足」
  - 使用合成語料庫驗證分類準確度
- 查詢分類(3類)
  - CRITICAL UNDER（嚴重不足）：查詢中缺乏一個或多個關鍵條件，使得無法產出高品質回應。
  - MINOR UNDER（輕微不足）：查詢中缺少次要條件，儘管如此，仍可能提供合理的回應。
  - SUFFICIENT（資訊充足）：查詢已充分表達作答所需的重要資訊。
- 實驗結果：
  - ![image](https://github.com/user-attachments/assets/df268147-d5a3-4ffd-a008-1effa4a9067f)
##### 分類2: LLM-BASED RESPONSE STRATEGY CLASSIFIER
- 回應策略類別
  - REFUSE（拒答）
  - RESPOND（直接回答）
  - HEDGE（模糊應對）
  - CLARIFY（提出澄清）
  - INTERROGATE（反覆詢問）
- 設計動機
  - 在 PODP 中，聊天機器人無法直接觀察使用者的目標 θ，只能根據信念狀態 Pr(θ | q, [a, o]*) 做出行動
- 光譜特性
  -  ![image](https://github.com/user-attachments/assets/11d8f7fa-a796-4739-ac1f-cd1457f7448c)
  -  不理會不確定性(uncertainty-agnostic)
    - 聊天機器人即便對使用者偏好不確定，直接依賴先驗知識做出回答
    - 回答依賴推論或語意上的聯想，可能會出錯  
  -  主動降低不確定性(uncertainty-reducing)
    - 聊天機器人可能不斷提問（INTERROGATE），直到精確掌握使用者目標
    - 這對使用者來說是極不理性的行為  
- LLM 分類器
  - 描述 LLM 的回應行為
  - 限制元策略的提示語空間

#### 評估

#### 優點
- 

#### 缺點
- 「RLHF 導致錯誤策略學習」的論證雖合理，但證據支持較薄弱
- 沒有進行人類標註對照分析來確認策略分類的準確性
- 若部署情境與訓練資料分布不同，所學策略可能過擬合歷史行為，未進一步探討如何應對概念漂移（concept drift）

