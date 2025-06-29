# 概述
## 特性(流程圖)
- 需要按特定順序遍歷步驟或決策
  - 序列性
    - 有向/無向邊 
  - 邏輯推理
    - 文字
    - 各種形狀（例如矩形、橢圓形和菱形）
- 流程圖需要理解方向邏輯
- 流程通常是多向的，代表了根據特定條件或決策而可能採取的各種路徑
- 直觀、多領域被廣泛使用
# 核心問題

# 研究議題
## 視覺基礎（visual grounding）
## 結構推理（structural reasoning）
## Flowchart Reasoning
## Graph-Encoder Models
  - FlowVQA僅將流程圖的圖結構用於生成拓撲問題
  - 若納入模型架構及推理策略設計，預期提升模型的結構推理能力
## Adversarial and Counterfactual probes
  - 資料集中添加多種探測問題集(負向路徑追蹤、反直覺問題及噪聲圖問題)...ETC
## Complex Subtasks
  - 以FlowVQA為例，主要任務是「流程圖→問答對」，可以衍生出多種任務
    - 文章→問答對
    - Mermaid.js→問答對
    - 流程圖→Mermaid.js
## 神經符號 AI
  - Trinh 等人（2024）提出，可用於增強模型在本資源上的效能與訓練，因為流程圖本身就是符號化且具序列結構的
## 圖像生成


## QA生成
### Q類型
- 事實檢索
- 情境應用
- 流程參照
- 拓撲問題
- 負向路徑追蹤
- 反直覺問題
- 噪聲圖問題
# 論文
## FlowchartQA: The First Large-Scale Benchmark for Reasoning over Flowcharts
- 2023
- 
### 要解決的問題
- 如何讓機器能夠理解與回答流程圖（flowchart）中的結構化資訊與圖像內容
  - 缺乏針對流程圖的視覺問答（VQA）基準數據集
  - 現有模型無法有效處理流程圖的圖形結構與推理
### 貢獻
-  建立首個針對流程圖的視覺問答（VQA）大規模基準數據集：FlowchartQA
-  提出一個多模態 Transformer 模型，用於多選流程圖問答任務
  - BERT + Vision Transformer（ViT）+ cross-attention
- 系統性實驗與消融分析，探討模型在不同圖結構與問題類型下的行為
  - 合成訓練 + 真實測試的遷移能力 
- 提供真實世界測試集與語義問答生成方法
  - 從 BPMN 商業流程圖中收集 166 張圖、266 組問答
  - 問答生成方式結合自然語言生成模型（fine-tuned BART）與節點語意資訊
### 資料集
- 大型的合成語料庫
  - 600 萬個問題-答案對 (5,964,647訓練, 610,309驗證, 585,179測試)
    - 幾何問題
    - 拓撲問題
    - 問題、答案、多選答案候選
  - 100 萬張流程圖圖片 (992,057 訓練, 99,284驗證, 99,139測試)
    - 8~16節點
    - 12~24邊 
  - 附有對應的標註資料
    - 基礎資料標註
    - 節點、邊的邊界框
    - 輪廓多邊形
    - 文字標籤
    - 示圖的鄰接矩陣
- 真實世界測試集
  - 使用BPMN(Business Process Model and Notation)
  - 用戶為四個不同任務繪製的流程圖
  - 每個任務的資料
    - 建模的流程描述
    - 多個用戶繪製的流程圖
    - 參考解答
  - 收集了總共 266 個問題，對應到 166 張圖片
#### 圖生成
- 自動化生成
  - 參數控制項
    - 節點
      - 最大數量
      - 對大度數
      - 樣式
    - 邊
      - 最大數量
      - 類型：有向/無向
      - 標籤：文字標籤/數字標籤/無標籤
      - 樣式
  - 生成策略
    - 在所選範圍內隨機生成節點數量
    - 節點標籤則從提供的詞彙表中抽取。
    - 根據生成參數設定，隨機向節點集添加邊，並生成邊標籤
    - 使用 graphviz dot 引擎佈局並渲染圖。獲得兩個不同版本：
      - 彩色圖片：節點用紅色、邊用綠色標示
      - 灰階圖片：作為最終輸出
#### 圖標註
-  節點的精確邊界框：渲染過程獲得
-  邊的標註資料：渲染彩色流程圖並提取邊圖。從圖渲染過程中獲得的邊界框
#### 問答生成
- 根據大量問題模板生成問題和答案
- 答案種類
  - 二元問題
  - 數值問題
  - 節點標籤問題 
- 問題種類
  - 幾何問題
    - ![image](https://github.com/user-attachments/assets/7a8f57d0-39eb-4ce1-9e05-dbd5c70e81a9)
 
  - 拓撲問題
    - ![image](https://github.com/user-attachments/assets/80d48f1b-cd96-489e-b40b-7d328a5a76fb)
- 數據集平衡
  - 答案類型較少的問題（即不要求節點標籤的問題）: 將每個答案的樣本數子抽樣到該問題最少樣本數的水平，接著再將每個問題的樣本數子抽樣到該問題最少樣本數的水平
  - 答案類型較多的問題（即答案為節點標籤的問題）: 將不同答案的樣本數子抽樣到與該問題最少樣本數的水平
  - 多選問題生成錯誤答案(最多至4個)
### 模型
- BERT +  Vision Transformer
### 缺點
- 高度依賴合成資料，尤其是隨機腳本合成的流程圖
  - 可能存在邏輯矛盾 
- 問答主要基於模板生成（e.g., "What is the leftmost node?")，語言表達受限，缺乏語義變異與自然語言歧義處理能力
- 推理類型偏向結構性問題（幾何位置、拓撲關係），缺乏跨節點語義邏輯或流程理解任務
  - 未說明是否包含"決策分支條件"
- 答案類型簡單：二元問題、label
- 需額外取得節點/邊的邊界資訊

### 相關連結
- [(2023)FlowchartQA: The First Large-Scale Benchmark for Reasoning over Flowcharts](https://aclanthology.org/2023.limo-1.5.pdf)
- 
## FlowVQA: Mapping Multimodal Logic in Visual Question Answering with Flowcharts
- 2024
### 要解決的問題
- 如何有效評估與強化多模態語言模型在處理流程圖這類結合視覺、順序與邏輯推理任務的能力
  - 現有VQA 缺乏針對結構化邏輯推理
  - VLM 難以理解流程圖這種非線性、多分支的圖形資訊
  - 流程圖推理需要結合視覺、邏輯與順序資訊
    - 資訊定位（localization）+ 推論判斷（inference）+ 結構理解（structure-aware reasoning）  
### 假設
- 任何基於流程的工作流程（無論領域）都可以轉換為流程圖
- 以逐步詳細的方式呈現該流程的關鍵要素
### 貢獻
- 構建大型流程圖推理資料集 FlowVQA
- 提出基於 GPT-4V 的資料生成框架
- 設計多種提示策略並系統性比較其效能
- 分析流程圖中的方向性偏誤（Directional Bias）
- 開放完整評測資源與實驗平台
### 資料集
- FlowVQA
#### 資料筆數
- 2,272個流程圖圖像
  - 基於Mermaid.js生成 
- 22,413問題-答案Pair
- ![image](https://github.com/user-attachments/assets/cad529fe-ea6b-4d69-9e11-360316f9e0c0)

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
  - 生成使用的資料量
    - <img width="320" alt="image" src="https://github.com/user-attachments/assets/0f8eb163-0035-41a6-b656-76dcefe881df" />
- 向 GPT-4 查詢以生成自上而下的 Mermaid.js 流程圖腳本。
  - Input: 第一步結果 + Mermaid.js 的模板腳本， 
  - 控制標籤協助將步驟映射到腳本中的節點類型。兩個提示同時提供了約束點，以改善標準化效果。
  - 接著，將 Mermaid.js 腳本編譯成高解析度的 PNG 圖像。
  - Prompt
    - <img width="401" alt="image" src="https://github.com/user-attachments/assets/3707c965-c31d-4ef9-9861-66fe13f98f73" />

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

### 實驗結果
#### 實驗1：準確率實驗
- ![image](https://github.com/user-attachments/assets/eee1eac7-5d9d-4887-ae6b-3ddf9445b3aa)
   - FlowVQA 具挑戰性 (GPT-4 結合 few-shot 指令提示策略最高才68.42% 多數投票正確率)
   - Few-Shot 指令對部分模型很有幫助
     - GPT-4 評估中提升了 7%
     - Gemini-Pro 提升了 12%
     - CogAgent-VQA 並未受益 (該模型無法生成指令，無法利用推理指令)
   - 專有模型表現優於開源模型。 GPT-4V with few-shot 指令的表現比 QwenVL-chat 高出近 30%
   - 微調有幫助。 對 Qwen-VLchat 進行微調
     - 顯示 VLM 預訓練階段缺乏對流程圖的理解 
     - Zero-Shot 提升了 3%
     - Zero-Shot CoT 提升了 11%。
     - T2、T3 及 T4 問題的提升（10%）明顯高於 T1（5%）
       - 事實檢索較簡單，不需要深入理解流程圖結構
       - FT後，T2, T3, T4有改善，證明 FlowVQA 可有效幫助現有 VLM 增強視覺邏輯及推理能力
   - 問題類型
     - 事實檢索（T1）及情境應用（T2）問題上表現普遍較佳，
     - 流程參照（T3）及拓撲問題（T4）上表現較弱，需要深入理解流程圖及複雜推理
   - 節點數量
     - ![image](https://github.com/user-attachments/assets/a721b9a0-1f8a-44a0-af51-9c277b22406b)
     - 節點數量多意味著更複雜的視覺資訊表示，因此更難推理
#### 問題對應領域的表現
- ![image](https://github.com/user-attachments/assets/76dadcd2-acae-44f6-9afb-188a573eff46)

#### 方向性偏差
- 解析 FlowVQA 的 Mermaid 腳本並系統性地將其反轉，生成「Bottom Top」倒置流程圖
  - 倒置的流程圖將起始節點放在下方
  - 終止節點在上方
- 實驗結果
  - ![image](https://github.com/user-attachments/assets/e1735569-4f8f-43a2-b3e0-f723026ebb37)
    - 最佳模型在處理流程圖推理時的確存在方向性偏差
    - GPT-4V 上的多數投票準確率下降了 15%。
    - 受預訓練資料混合中的偏見影響，無法將推理紮根於影像上下文，導致評估效能顯著下降。
### 結論
- 現有 VLM 難以應對流程圖推理任務
- Few-shot 指令式提示顯著提升效能
- 微調（Fine-tuning）對表現有幫助
- 模型存在方向性偏誤（Directional Bias）
  - 當將流程圖方向反轉（例如從「自上而下」改為「自下而上」）後，模型表現大幅下降（例如 GPT-4 準確率下降 15%） 
### 缺點
- 合成流程圖過多，真實性不足
  - 缺乏「真實世界中原生流程圖的語意噪音與不規範結構」
  - 能造成模型表現過度依賴規則化模式，而非學會真正處理非結構性視覺資訊
- 只對少數模型進行微調
  - 作者強調「Fine-tuning 能有效改善表現」，但實際上只對 Qwen-VL-chat 做了微調
- 缺乏與傳統 VQA 基準的對照
  - 沒有與現有 VQA 資料集(FlowChartQA之類)做比較，難評估資料集之間差異
- 流程圖節點數與難度的因果關係不明確
  - 節點變多不一定代表邏輯路徑變長 
### 相關連結
- [(2024)FlowVQA: Mapping Multimodal Logic in Visual Question Answering with Flowcharts](https://aclanthology.org/2024.findings-acl.78.pdf)

## FlowLearn: Evaluating Large Vision-Language Models on Flowchart Understanding
- [(2024)FlowLearn: Evaluating Large Vision-Language Models on Flowchart Understanding](https://arxiv.org/pdf/2407.05183v1)
### 要解的問題

### 貢獻
### 資料集
- Ovewview
  - ![image](https://github.com/user-attachments/assets/5e5994aa-c13e-490c-bbd4-6d83a6ef23a6)
- VQA任務
  - 通用 
    - ![image](https://github.com/user-attachments/assets/8d3bfa44-1192-4dff-bf8a-6c5ae1561331)
  - 僅適用模擬流程圖
    - ![image](https://github.com/user-attachments/assets/1d74b9a5-21f2-4ad0-ab6d-89e18503eac5)

#### 科學流程圖 Scientific Flowcharts
- 數量：3,858
- 包含
  - 流程圖 
  - 圖說（中位數長度為 25 個字詞）
  - 圖內文字標註
  - 視覺問答（VQA）中的問答配對
- 來源
  - ArXiv 下載了 27,000 篇科學論文
- 篩選條件
  - 採用了規則式過濾與人工審查相結合的方式
  - 「illustration」、「flowchart」、「model」、「step」、「overall」、「graphical representation」等與流程圖相關的關鍵字來挑選圖像
  - 排除如「normalized」、「plot」等無關關鍵字
  - 從 2,674 篇文件中取得了 3,858 張流程圖
- 取得中繼資料
  - 使用 PDFFigures 2.0 [5] 擷取圖像與相關中繼資料（metadata）
  - 其他中繼資料則由 SciPDF Parser 處理，該工具底層使用 GROBID 進行 PDF 解析
#### 模擬流程圖 Simulated Flowcharts
- 由 Mermaid 程式碼生成 10,000 張流程圖
- 包含
  - 流程圖
  - Mermaid 程式碼：
    - 每張圖皆提供對應 Mermaid 原始碼，方便進行流程圖結構的程式化理解與操作
  - 提供視覺元件的詳細標註
    - 從 SVG 擷取節點文字、文字位置、箭頭頭尾位置等詳細標註
    - 支援物件偵測與結構分析任務。 
  - 包含視覺問答（VQA）中的問答配對
- 來源
  - 使用 Mermaid（一款將 Markdown 類語法轉換為流程圖的 JavaScript 工具）所生成
  - 參數
    - 節點（Nodes）：
      - 3 至 10 個節點
      - 節點內文字為隨機英文單詞
    - 連線（Links）：
      - 節點之間的連線數量隨機生成
      - 所有節點至少有一個連線
      - 箭頭類型也隨機選擇為實線、粗線或虛線。
    - 背景顏色與流程圖方向：
      - 背景色為隨機的十六進位色碼，
      - 流程圖方向則從 Mermaid 支援的所有方向中隨機選擇。 
#### 標註資訊
- 視覺元件標記
- OCR（光學字元辨識）結果
- Mermaid 程式碼表達
#### 視覺問答（VQA)
- 共通
  - OCR：我們在流程圖內某段標註文字上隨機加上紅框，提示模型輸出紅框內的文字內容
  - 真偽判斷（True/False）：
    - 科學流程圖
      - 先根據圖說產生兩句正確敘述，並由標註者比對圖像驗證。
      - 若圖說不足，則標註者根據圖像產生新的正確敘述。再將其中一句做出語意反轉（改變幾個字但保留原用字風格）以製作錯誤敘述，確保詞彙風格一致。
    - 模擬流程圖
      - 使用模板生成真偽陳述
      - 如：「節點 {a} 與節點 {b} 之間有箭頭」、「一個箭頭從節點 {a} 指向節點 {b}」等
      - {a} 與 {b} 為來自視覺構件標註的節點文字。
  - 圖像描述（Description）：
    - 科學流程圖，我們使用圖說作為參考答案；
    - 模擬流程圖，則將 Mermaid 程式碼轉換為敘述，例如：「{a} 指向 {b}」
- 模擬流程
  - Mermaid 程式碼生成: 要求模型輸出能完整代表流程圖的 Mermaid 程式碼，考驗其對節點與箭頭等構件的綜合識別能力
  - 節點數量統計
  - 箭頭數量統計
### 模型
- Step-1V-32K
- GPT-4V
- Gemini-Pro-Vision
- Claude-3-Opus-20240229
- LLaVA-V1.6-Vicuna-34B
- InternLM-XComposer2-VL-7B
- Qwen-VL-Chat (2024/01/25 )
- DeepSeek-VL-7B-chat
### 評估指標
- 準確率（Accuracy）：此指標用於 OCR、真假敘述、節點數量與箭頭數量
  - 除了一般準確率，針對程式碼生成
    - 節點層級評估（Node-Level Evaluation）
    - 連線層級評估（Link-Level Evaluation）
    
- 相似度（Similarity）：針對描述任務，我們使用四種常見的文本相似度指標來比較模型生成的描述與參考描述之間的相似程度
  - BLEU, ROUGE-L, BERT Score, SBERT

### 缺點

## Arrow-Guided VLM: Enhancing Flowchart Understanding via Arrow Direction Encoding
### 要解決的問題
### 貢獻
### 缺點
### reference
- []()


- [(2023)DiagrammerGPT: Generating Open-Domain, Open-Platform Diagrams via LLM Planning](https://arxiv.org/pdf/2310.12128)
# 其他
- [Mermaid Diagramming and charting tool](https://mermaid.js.org/)
