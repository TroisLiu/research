# Visual Instruction Tuning
## 定義
- Visual Instruction Tuning（視覺指令微調） 是一種用於 多模態（視覺 + 語言）大模型訓練的方法
- 傳統的指令微調（Instruction Tuning） 是在純語言大模型上訓練模型去「理解人類給的文字指令並跟隨它們去完成任務」。
- Visual Instruction Tuning 讓模型能“一邊看圖一邊依照語言指令操作"
## 目標
- 將大型語言模型（LLM）與視覺模組結合，讓模型能：
  - 理解帶有圖片的指令（例如：「這張照片裡有什麼？為什麼這些物品一起出現？」）
  - 輸出與圖像內容相關的文字回答、描述、推理等
## 說明
- VQA是VIT的子集合

## 相關Keyword
### Image-Dialugue Dataset
### Time-sensitive Dataset/Question Answering
- 類型
  - 資料內容隨時間(對話)改變?
  - 圖像資料包含時間軸?

## Reference
### 單圖問答
- [(2301)SlideVQA: A Dataset for Document Visual Question Answering on Multiple Images](https://arxiv.org/pdf/2301.04883)
  - 延伸研究議題：Slide Image Understanding
  - 將Slide轉換成Image再進行QA
    - 單圖問答
    - 跨圖推理
### 多輪圖文對話資料

### 跨圖推理
- [(2301)SlideVQA: A Dataset for Document Visual Question Answering on Multiple Images](https://arxiv.org/pdf/2301.04883)
  - 見上

### 其他
- [(25)Less is More: High-value Data Selection for Visual Instruction Tuning](https://dl.acm.org/doi/pdf/10.1145/3746027.3755160)
- [(2512)Instruction-Following Evaluation of Large Vision-Language Models](https://arxiv.org/pdf/2512.23572v1)
- [(2508)VAGUE: Visual Contexts Clarify Ambiguous Expressions](https://arxiv.org/pdf/2411.14137)
- [(2505)Visual Question Answering: A Survey of Methods, Datasets,Evaluation, and Challenges](https://dl.acm.org/doi/epdf/10.1145/3728635)
  - VQA資料集分類
    - Real-world Images
    - Synthetic Images
    - Unbiased Images
    - Others  
- [(2502)MTPChat: A Multimodal Time-Aware Persona Dataset for Conversational Agents](https://arxiv.org/pdf/2502.05887)
  - 跨對話跨圖問答? (待確認)
  - 基於MPChat資料集拓展
- [(2502)TemporalVQA: Can Multimodal LLMs do Visual Temporal Understanding and Reasoning? The answer is No!](https://arxiv.org/pdf/2501.10674)
- [(2408)A Comprehensive Evaluation of GPT-4V on Knowledge-Intensive Visual Question Answering](https://arxiv.org/pdf/2311.07536v3)
  - 資料結構類似OK-VQA，但部分多了Reason (Rationale) 
- [(2406)MM-Instruct: Generated Visual Instructions for Large Multimodal Model Alignment](https://arxiv.org/pdf/2406.19736)
- [(2402)IWISDM: ASSESSING INSTRUCTION FOLLOWING IN MULTIMODAL MODELS AT SCALE](https://arxiv.org/pdf/2406.14343)
- [(2312)Visual Instruction Tuning towards General-Purpose Multimodal Model: A Survey](https://arxiv.org/pdf/2312.16602)
  - 相關資料集統整與介紹
- [(2308)VisIT-Bench: A Benchmark for Vision-Language Instruction Following Inspired by Real-World Use](https://arxiv.org/pdf/2308.06595)
- [(2308)Enhanced Visual Instruction Tuning with Synthesized Image-Dialogue Data](https://arxiv.org/pdf/2308.10253)
- [(2305)MPCHAT: Towards Multimodal Persona-Grounded Conversation](https://arxiv.org/pdf/2305.17388)
  - 建立Persona image-sentence pairs
  - 針對對話情境
  - 待閱讀
- [(2207)IconQA: A New Benchmark for Abstract Diagram Understanding and Visual Language Reasoning](https://arxiv.org/pdf/2110.13214)
  - 可能適用於架構圖的Icon代表圖識別?
- [(2110)A Dataset for Answering Time-Sensitive Questions]https://arxiv.org/pdf/2108.06314
  - 好像是文字資料   
- [(2001)Visual Question Answering on 360◦ Images](https://arxiv.org/pdf/2001.03339)
  - 針對360全景圖片 
