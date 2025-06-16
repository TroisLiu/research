# 快速索引
- [(2025)LLM-Friendly Knowledge Representation for Customer Support](https://github.com/TroisLiu/research/blob/master/Dailogue_System/System_Framework.md#2025llm-friendly-knowledge-representation-for-customer-support)
  - ICA + CoT
- [(2024)Chatbot Meets Pipeline: Augment Large Language Model with Definite Finite Automaton](https://github.com/TroisLiu/research/blob/master/Dailogue_System/System_Framework.md#2024chatbot-meets-pipeline-augment-large-language-model-with-definite-finite-automaton)
  - DFA
  - 每個應用情境資料可以建構一個自己的DFA決策樹
- [(2024)On Overcoming Miscalibrated Conversational Priors in LLM-based Chatbots]()

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
#### 貢獻:
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
#### 要解決的問題
- 
#### 假設
- 
#### 貢獻:
- 

#### 資料集
- 

#### 模型
- 

#### 實驗
- 

#### 評估

#### 優點
- 

#### 缺點
- 

