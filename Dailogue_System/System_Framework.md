# 快速索引
- [(2025)LLM-Friendly Knowledge Representation for Customer Support](https://github.com/TroisLiu/research/blob/master/Dailogue_System/System_Framework.md#2025llm-friendly-knowledge-representation-for-customer-support)

# 概述
- 探索一個可行的架構實現對話管理

## 核心議題


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
- 
#### 貢獻:
- 本論文首度結合 DFA 與 LLM，提出可解釋、流程導向、可遷移的對話生成框架 DFA-LLM，實證其在實務任務中能有效提升對話品質與控制性
  - 讓LLM 生成回應時，**遵循DFA指定的流程路徑**: DFA 是從訓練對話中自動學習而來，能以結構化方式建模對話流程與狀態轉移
  - 把對話內容抽象為「語義標籤（tags）」，進而建構DFA
  - 實作與實驗：在多個資料集上驗證 DFA-LLM 的效能
  - 支援即插即用：不需微調，適合實際部署
#### 模型
#### 資料處理
#### 評估指標
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
