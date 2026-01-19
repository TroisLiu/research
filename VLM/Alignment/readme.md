# 簡述
## 目標
1. 從文字輸入的應用轉變成多模態輸入的應用

## 挑戰
### 多模態檢索與語義對齊（Multimodal Retrieval & Alignment）
- 如何學習 共同的多模態 embedding 空間 以讓文字查詢能與圖像檢索結果對齊？
- 如何減少語義偏差，即同一句話對不同圖像的檢索 relevance 差異？

### 多模態資料庫索引與檢索機制（Indexing & Retrieval Strategies）
- 如何為圖像建立有效向量索引？（例如 CLIP 或其他視覺→語義 embedding）
- 是否對不同模態使用 separate vs shared indexing？
- 如何在檢索時整合跨模態相似度計算（文字↔圖像、圖像↔圖像、文字↔文字）

### 檢索結果融合與重排序（Retrieval Fusion & Re-Ranking）
- 如何衡量不同模態檢索結果的相對重要性？
- 是否需要訓練一個 cross-modal re-ranker 來提升 relevance？
- 如何處理同一 query 可能對文字/圖像給出矛盾或競合的檢索 evidence？

### 視覺＋語言理解與融合（Multimodal Understanding）
- 如何把圖像內容結合到 LLM 的 context 中，而非淺層把文字嵌入圖像 caption？
- 如何處理 複雜指令（例如「為什麼這些物品一起出現？」）— 這涉及原因推理，而非單純識別物件？
- 如何保證生成出的回答對圖像內容有正確 grounded 認知，而非hallucination？

### 多模態評估指標與 benchmark（Evaluation & Metrics）
- 傳統 RAG 多用文字 QA 的準確度評估，但多模態 RAG 需要新的評估方法來衡量：
  - 圖像理解正確性（比如對物件與場景理解）
  - 推理與因果合理性
  - 檢索效率與 recall precision（跨模態）

### 穩健性與安全性（Robustness & Safety）
- 加入視覺模態後也帶來新的風險：
  - 圖像中的 adversarial perturbation 可能破壞檢索與生成結果
  - 多模態 RAG 可能面臨跨模態幻覺或視覺誤解問題
 
### 訓練資料與資料集挑戰（Data & Dataset）
- 多模態 RAG 需要大量包含 圖像 + 文字 + 問答/指令 的訓練資料：
  - 如何收集合成高質量的三元數據（image + question + ground truth answer）？
  - 如何避免模型學習不正確或偏差的視覺語義對齊？
  - 是否需要自動生成訓練樣本（例如圖像 caption + Q&A）來擴充稀缺資料？

### 性能、可擴展性及系統效率（Efficiency & Scalability）
- 讓 RAG 系統同時處理圖像與文字意味著：
  - 檢索庫的向量空間與索引會變大且更重
  - 訓練與推理計算代價提高
  - 因此如何提升效率、設計更低成本的 multimodal retrieval pipeline 是實作與研究難點。

## reference
- [(2501)Ask in Any Modality A Comprehensive Survey on Multimodal Retrieval-Augmented Generation](https://arxiv.org/pdf/2502.08826)
- [(2503)VisRAG: Vision-based Retrieval-augmented Generation on Multi-modality Documents](https://arxiv.org/pdf/2410.10594)
