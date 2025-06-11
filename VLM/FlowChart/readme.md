# 概述
## 特性(流程圖)
- 需要按特定順序遍歷步驟或決策
  - 序列性
  - 邏輯推理
- 流程圖需要理解方向邏輯
- 流程通常是多向的，代表了根據特定條件或決策而可能採取的各種路徑
# 核心問題

# 研究議題

## 圖像生成

## QA生成

# 論文
## FlowchartQA: The First Large-Scale Benchmark for Reasoning over Flowcharts
- 2023
- 
### 要解決的問題

### 貢獻

### 資料集

### 模型

### 缺點

### 相關連結
- [(2023)FlowchartQA: The First Large-Scale Benchmark for Reasoning over Flowcharts](https://aclanthology.org/2023.limo-1.5.pdf)
- 
## FlowVQA: Mapping Multimodal Logic in Visual Question Answering with Flowcharts
- 2024
### 要解決的問題
### 假設
- 任何基於流程的工作流程（無論領域）都可以轉換為流程圖
- 以逐步詳細的方式呈現該流程的關鍵要素
### 貢獻

### 資料集
- FlowVQA
#### 資料筆數
- 2,272個流程圖圖像
  - 基於Mermaid.js生成 
- 22,413問題-答案Pair
#### 資料來源
- 來自3個不同內容來源
- WikiHow 文章:
  - 提供日常任務的逐步操作說明
  - 依文章主題分類
  - 抽樣1,268篇文章
- Instructables DIY 部落格
  - 提供日常任務的逐步操作說明
  - 依文章主題分類
  - 抽樣789 篇
- FloCo代碼片段:
  - 低複雜度的程式碼範例
  - 所屬類別：Code
  - 抽樣 475 個FloCo範例
- 人工挑選
- FloCo: 人工挑選高品質的代碼片段
- FloCo 圖片範例可以不斷比較生成的流程圖與原始樣本
- 協助優化提示詞，並使其能夠應用於 WikiHow 和 Instructables 資料集
#### 問題類型
- T1. 事實檢索（Fact Retrieval）：
  - 要求定位並檢索流程圖節點中的直接事實資訊。
  - 問題看似簡單，但仍需圖像分析並取得定位線索，才能找到正確答案。
  - 屬於細粒度的內容理解
- T2. 情境應用（Applied Scenario）：
  - 問題描述現實生活中的情境，測試模型將流程圖應用於實際問題的能力。
  - 這類問題模擬人類在日常中解讀流程圖的推理能力
  - 常以需要過濾干擾內容以找出關鍵邏輯的謎題式問題呈現。
  - 屬於細粒度的內容理解
- T3. 流程參照（Flow Referential）：
  - 此類問題會從流程圖中抽取一個隨機子圖（通常包含決策節點），並以決策邏輯設計前後向的問題。
  - 這評估了流程圖中的細節化路徑動態。
  - 屬於細粒度的內容理解
- T4. 拓撲問題（Topological）：
  - 此類問題需要分析流程圖的整體拓撲結構，並回答與圖結構相關的問題。
  - 這些問題透過解析 Mermaid.js 腳本、將其轉換為鄰接矩陣，再生成模板問題，通常有定量的正確答案。
  - 測試流程圖的結構資訊
#### 建置方式
##### 流程圖建置
- <img width="583" alt="image" src="https://github.com/user-attachments/assets/7f176027-53b6-4aa8-a4b8-3b995ecaff2e" />
- 我們使用 GPT-4 對來源文本進行查詢，生成具備功能控制標籤（例如「START」、「PROCESS」、「DECISION」）的逐步結構化表示。
  - 此步驟將來源文本轉換為標記化的文本表示，適合進一步轉換為 Mermaid 流程圖腳本。對於 FloCo 來源的文本，我們生成對應的偽代碼，作為下一步的輸入
  - Prompt
    - <img width="535" alt="image" src="https://github.com/user-attachments/assets/aebd530c-b943-4f1b-94e3-70134c3ad660" />

- 向 GPT-4 查詢以生成自上而下的 Mermaid.js 流程圖腳本。
  - Input: 第一步結果 + Mermaid.js 的模板腳本， 
  - 控制標籤協助將步驟映射到腳本中的節點類型。兩個提示同時提供了約束點，以改善標準化效果。
  - 接著，將 Mermaid.js 腳本編譯成高解析度的 PNG 圖像。
  - Prompt
    - <img width="401" alt="image" src="https://github.com/user-attachments/assets/3707c965-c31d-4ef9-9861-66fe13f98f73" />
  - <img width="320" alt="image" src="https://github.com/user-attachments/assets/0f8eb163-0035-41a6-b656-76dcefe881df" />
  
##### 問答對生成
- 整體流程圖
  - <img width="637" alt="image" src="https://github.com/user-attachments/assets/b1bec43d-c558-4dfa-b76a-2745be2eed3c" />
- 針對 T1、T2 及 T3
  - 構造相對應提示 (參考論文附錄D.2)
  - 將以下資訊一起輸入 GPT-4，以生成高品質的問答對
    - 帶標籤的文本表示
    - Mermaid.js 腳本
    - 提示
    - 文字示範例子 (包含多種創意且高品質的範例)
  - 每個問題都生成三個語義等價的標準答案（paraphrased gold answers）
- 針對 T4
  - 解析 Mermaid 腳本、轉換為鄰接矩陣
  - 生成模板問題來產生答案，通常為定量答案 (不是透過LLM)
  - 為每個模板答案生成兩個附加的同義答案，達到三個黃金標準答案

#### 驗證Pipeline
- 人工驗證(依循「生成-測試」範式)
  - 所有生成的流程圖及其問答對均需通過五位專家標註員的質量檢核。
  - 所有驗證產品均再經由兩位獨立的監督專家再次審核，以確保標註品質一致且不偏不倚。
- 生成的資料例子參考論文附錄A
##### 資料集品質評估
- 針對流程圖及問答對的邏輯流程
- 複雜性
- 上下文對齊
- 
### 模型
#### 開源小型多模態
- LLaVA（Liu 等人，2023b）
- OpenFlamingo（Awadalla 等人，2023）
- BLiPv2（Li 等人，2023a）
- mPLUG-OWL（Ye 等人，2023b）
- Sphinx（Lin 等人，2023）
#### 大模型
- 商用
  - GPT-4V（OpenAI, 2023）
  - Gemini Pro（Anil 等人，2023）
- 開源
  - CogAgent-VQA（Hong 等人，2023）
  - InternLM-X-Composer2（Dong 等人，2024）
  - QwenVL-chat（Bai 等人，2023）
### 實驗方式:
- Zero-Shot
  - 輸入流程圖並提示 VLM 以簡短的說明回答問題。
- Zero-Shot CoT（Chain-of-Thought）：
  - 輸入流程圖並提示模型先推理再回答（Wei 等人，2023）。
- Text Only Few-Shot CoT with Reasoning Directives
  - 構造了自訂提示詞，明確列出回答流程圖問題所需的推理步驟，靈感來自 Zhang 等人（2023）、Li 等人（2023b）及 Kojima 等人（2023）。
  - 針對流程圖特性，將複雜問題拆解為四個部分：
    - 問題本身
    - 方向性提示標籤
    - 逐步推理
    - 答案
  - 每個問題類型都從訓練集抽樣相似樣本作為示範。
- Fine-Tuning
  - 在 FlowVQA 訓練集上微調模型，然後再提示模型回答問題。
### 評估方式
-  AI 擔任評估者 (類似 Fu 等人（2023）、Lin 和 Chen（2023）及 Chiang 和 Lee（2023）的方法
  -  GPT-3.5
  -  Llama-2 70B
  -  Mixtral 8×7B
-  比較項目
  -  模型生成的回答
  -  三個黃金短答案（不含問題上下文）
-  評估者
  -  需拆解並對齊回答
  -  提出逐步推理的依據
  -  並給予正確或錯誤的二元標籤
-  最終以多數投票決定最終標籤
-  目標：聚焦於短文本的「長度不變」同義檢測
### 微調
- LORA
- 2×NVIDIA A100 40GB GPU
### 缺點

### 相關連結
- [(2024)FlowVQA: Mapping Multimodal Logic in Visual Question Answering with Flowcharts](https://aclanthology.org/2024.findings-acl.78.pdf)

## FlowLearn: Evaluating Large Vision-Language Models on Flowchart Understanding
- [(2024)FlowLearn: Evaluating Large Vision-Language Models on Flowchart Understanding](https://arxiv.org/pdf/2407.05183v1)

- [(2023)DiagrammerGPT: Generating Open-Domain, Open-Platform Diagrams via LLM Planning](https://arxiv.org/pdf/2310.12128)
# 其他
- [Mermaid Diagramming and charting tool](https://mermaid.js.org/)
