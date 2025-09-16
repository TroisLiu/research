# Task
## VLM
- 圖像編碼器 + LLM

## Taxonomy
### Data Augmentation
#### 視覺層面增強
- Towards Efficient and Robust VQA-NLE Data Generation with Large Vision-Language Models
#### 文字層面增強
- 基於模板+模型
  - InfoSeek(維基百科Wikidata)
  - Encyclopedic VQA(WIT+維基百科)
#### 跨模態一致性增強

### Synthetic Data
#### 圖像合成
#### 文本合成
- Towards Efficient and Robust VQA-NLE Data Generation with Large Vision-Language Models
- SK-VQA 
#### 圖文配對合成
- Enhanced Visual Instruction Tuning with Synthesized Image-Dialogue Data
- Synth2: Boosting Visual-Language Models with Synthetic Captions and Image Embeddings

## 應用情境
- 多輪對話(圖文交雜)
- 單圖對話
- 跨圖推理
- 
### reference

## reference
- [Towards Efficient and Robust VQA-NLE Data Generation with Large Vision-Language Models](https://aclanthology.org/2025.coling-main.292.pdf)
  - 2025
  - 目標：
    - 針對VQA-NLE 提出一系列生成流程
  - 特色
    - 自然語言解釋（Natural Language Explanation, NLE）
    - 帶有邊界框（bounding boxes）的視覺提示（visual prompts, ViP）
      - bounding box
      - 物件名稱 
  - 生成方法
    - 單步生成（SINGLE-STEP）
      - 一次生成Q,A,E
    - 單步生成 + 視覺提示（SINGLE-STEP-VIP）
      - 為資料 D 標註 bounding box b
      - 將問題前綴 qp 和物件名稱 o 加入提示：Φ(p, qp, o)。
      - 使用具備視覺提示能力的調優模型 M′（如 ViP-LLaVA）生成Q, A, E
        - 模型需要微調
          - 使用GQA資料集微調的模型
        - 
    - 多步生成（MULTI-STEP）
      - 依序生成 qi、ai、ei，而非一次性生成
      - 使用 re-ranking比較多個候選解釋，選出最優解釋  
- [SK-VQA: Synthetic Knowledge Generation at Scale for Training Context-Augmented Multimodal LLMs](https://arxiv.org/pdf/2406.19593v2)
  - 2024
  - 目標：增強上下文
    - 給定一張圖片，首先生成一段與圖像有關的文件/段落 
    - 再要求生成必須同時參考圖象與文件推理的QA對 
    - 同時生成上下文段落(文章段落)&QA對
  - 多種圖像來源
    - 已有文字段落描述
      - 部分只生成QA對
      - 其他依然有再生成文字段落   
    - 沒有文字段落描述
      - LAION-400m
      - COCO-Conunterfactuals  
  - 資料過濾
    - 圖像引用過濾
      - 避免生成"在圖片中...", "如圖所示..."等延伸內容文字
    - 答案存在於生成的文字段落
  - 其他知識密集型VQA
    - SnapNTell（Qiu et al., 2024）
    - 技術工程需求（Doris et al., 2024）
    - 科學期刊文章（Ding et al., 2024）
  - 使用LLaVA-34B替代 GPT-4 生成資料
    - 76% 問題無效(問題複雜度不夠，當靠上下文就可回答  
- [Synth2: Boosting Visual-Language Models with Synthetic Captions and Image Embeddings](https://arxiv.org/pdf/2403.07750v2)
  - 2024
  - Google Deepmind
  - 目標：
    - LLM生成合成字幕，再透過文字轉圖像模型合成圖像
    - 直接生成圖像嵌入(VQ-Embedding)，可以傳統生成圖像後還要編碼/減碼，加快25%
  - 字幕生成（Caption Generation）
    - 語義多樣性 
  - 圖像生成（Image Generation）
- [Enhanced Visual Instruction Tuning with Synthesized Image-Dialogue Data](https://arxiv.org/pdf/2308.10253v2)
  - 2023 
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
