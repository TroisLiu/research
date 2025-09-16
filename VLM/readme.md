# Task
## Taxonomy
### Data Augmentation
#### 視覺層面增強
#### 文字層面增強
#### 跨模態一致性增強

### Synthetic Data
#### 圖像合成
#### 文本合成
#### 圖文配對合成

## 應用情境
- 多輪對話(圖文交雜)
- 單圖對話
- 跨圖推理
- 
### reference

## reference
- https://arxiv.org/pdf/2406.19593v2
- https://arxiv.org/pdf/2403.07750v2
- [Enhanced Visual Instruction Tuning with Synthesized Image-Dialogue Data](https://arxiv.org/pdf/2308.10253v2)
  - 生成圖像
    - 針對特定屬性(例如顏色)，會提供其他關鍵詞，避免頻繁輸出常見值，導致缺少多樣性
  - 生成對話
    - 第二階段對話聚焦"增強多圖推理能力
    - 建構圖文交織的多輪對話
    - 對話中的問題類型為回答明確不模糊的問題
      - 是/否
      - 5W1H
      - 二選一
      - 具誤導性的二選一問題
    - 生成提示、對話都提供上下文示例
  - 生成模板
    - 單圖&對話生成
    - 多圖生成
    - 交錯式多輪對話生成 
  - 數據過濾機制（Data Filtering Mechanism)
    - SD提示詞可能存在一定重複率，過濾後再用來生成圖像、對話
    - 圖像生成提示詞數量限制：太多會影響文本轉圖像模型捕抓關鍵資訊能力，導致生成圖像不符預期
    - 文本生成提示詞長度限制：設定長度上限，避免生成失控
    - 針對不同類型主題的生成能力差異
      - 例：在生成與「建築工人」相關的內容時，模型傾向於聚焦建築物。在提示中額外加入了「人物屬性」
  - 問題
    - 參考MMBench定義問題類型
      - LR（Logic Reasoning，邏輯推理）
      - AR（Attribute Reasoning，屬性推理）
      - RR（Relation Reasoning，關係推理）
      - CP（Commonsense Perception，常識感知）
      - FP-C（Fine-grained Perception – Color，細粒度感知-顏色）
      - FP-S（Fine-grained Perception – Shape，細粒度感知-形狀） 

## Struct-aware Embedding
- [Topological Perspectives on Optimal Multimodal Embedding Spaces](https://arxiv.org/pdf/2405.18867)
  - 深入分析CLIP與CLOOB的向量空間分布差異 
- [LARE: Latent Augmentation using Regional Embedding with Vision-Language Model](https://arxiv.org/pdf/2409.12597)
  - 利用 VLM 學習到的統一嵌入空間進行 區域嵌入（regional embedding）。
  - LARE 將影像嵌入在視覺語言嵌入空間中表示為「一個區域」而非單一點，並透過在該區域中取樣影像嵌入來實現數據增強，使得模型能泛化到多個未見領域 

## Contrastive Distillation 
- [Synthetic Data is an Elegant GIFT for Continual Vision-Language Models](https://arxiv.org/abs/2503.04229)

# reference

## (Paper) A Survey on Visual Understanding Multimodal Large Language Models
### 圖像理解
- 空間推理(Spatial Reasoning)
  - [Spatial Position Reasoning of Image Entities Based on Location Words](https://www.mdpi.com/2227-7390/12/24/3940)
    - 探討了 空間位置詞（spatial locators）在圖像描述任務中的作用
    - 例如：In front of
- 
### 短影片理解
- 空間推理
- 時間推理-單事件(Temporal Reasoning - Single Event)

### 長影片理解
- 空間推理
- 時間推理-多事件(Temporal Reasoning - MultiPle Event)
- 時間推理-單事件(Temporal Reasoning - Single Event)
- 長期推理（Long-term reasoning）
