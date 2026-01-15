# Task
- 圖表的元素可劃分2種
  - 視覺標記：柱狀、折線、方框...etc
  - 文本標籤：坐標軸、圖例、文字...etc
## 挑戰
- VLM對圖表理解不佳的一大原因在於圖像特徵與文本語義對不齊，將錯誤的圖像區域映射到問題描述。
## 可行方案
- 先把圖表看懂、讀懂，再交給語言模型
### 圖表資訊抽取Pipeline
- 圖表分類 + 文字偵測 + 文字影像放大 + 文字辨識 + 文字角色分類
- <img width="1481" height="523" alt="image" src="https://github.com/user-attachments/assets/e6331b69-b460-4981-b353-f852323c2747" />
- <img width="752" height="348" alt="image" src="https://github.com/user-attachments/assets/30e3e4df-29ec-4ea2-9d16-97a80d51cbcf" />
### 圖表2表格
- DePlot
### 圖表2KG
- 圖表分類 + 內容抽取(物件識別、OCR、Graphics Mark Parsing) + KG建置(Entity分類、關聯建立)
### 圖文理解增強模組(LLM外部輔助)
### VLM
- 圖像編碼器 + LLM
- 
### Reference
- [ChartEye: A Deep Learning Framework for Chart Information Extraction](https://arxiv.org/pdf/2408.16123)
  - 圖表分類：Swim Transformer
  - 文字偵測：YOLOv7
  - 文字影像放大：ESRGAN
  - 文字辨識：TPS-ResNet-BiLSTM-Attn
  - 文字角色分類：Swim Transformer
  - 類型：圖表資訊抽取Pipeline
- [ChartKG: A Knowledge-Graph-Based Representation for Chart Images](https://arxiv.org/pdf/2410.09761)
  - 圖表分類： ResNet
  - 內容抽取：YOLOv5 + OCR
  - KG建置
  - 類型：圖表2KG
- [InternVL 3.5 : Best open-sourced Multi-Modal LLM](https://medium.com/data-science-in-your-pocket/internvl-3-5-best-open-sourced-multi-modal-llm-bc929e2b6338)
  - 圖像編碼器： EVA-CLIP 圖像編碼器+Q-Former
  - 對齊機制：密集視覺對齊機制，使模型能對每一個視覺片段對應正確的語詞描述
  - 預訓練融入了科學圖表資料
  - 類型：模型訓練層面的優化
- [UniChart: A Universal Vision-language Pretrained Model for Chart Comprehension and Reasoning](https://arxiv.org/pdf/2305.14761)
  - 類型：模型訓練層面的優化
- [ChartGemma: Visual Instruction-tuning for Chart Reasoning in the Wild](https://arxiv.org/pdf/2407.04172)
  - 類型：模型訓練層面的優化
## Taxonomy
### Data Augmentation
#### 視覺層面增強
- 表層形式(Surface form)方法：標準影像轉換手法(裁切、翻轉、鏡像)
  - MixGen 
- 潛在空間(latent Space)方法：
  - Early Fusion
    - 將來自所有模態的原始輸入或詞元嵌入結合起來
    - 利用低階特徵間的互動，特別適合於跨模態關聯性強的多模態任務
    - [On the Benefits of Early Fusion in Multimodal Representation Learning](https://arxiv.org/pdf/2011.07191)
  - Late Fusion
    - 每個模態的輸入會先由不同的骨幹網路獨立處理，再於後層（通常是在分類器層之前）將這些表徵融合
      - ensemble 
    - LeMDA: 對抗式增強網路（Liu et al., 2022） 
- 找出Bounding Box
  - VQA-NLE: Towards Efficient and Robust VQA-NLE Data Generation with Large Vision-Language Models
- 模型增生圖像
  - Multimodal Data Augmentation for Image Captioning using Diffusion Models

#### 文字層面增強
- 同義詞替換
- 句子改寫
  - Multimodal Data Augmentation for Image Captioning using Diffusion Models 
- 生成QA：基於模板+模型
  - InfoSeek(維基百科Wikidata)
  - Encyclopedic VQA(WIT+維基百科)
  - Data Augmentation for Visual Question Answering
- 生成對話：基於LLM生成對話
  - SciGraphQA: SciCap+資料集(包含圖、文章、標題、OCR文本、摘要)生成多輪對話
- 生成QA：人工撰寫+模型生成
  - ChartQA 
#### 跨模態一致性增強 (cross-modal consistency augmentation)
- 說明：確保不同模態之間的對齊一致性被強化或保持。
  - 例如：
    - 對影像做翻轉後，描述文字也要同步調整（"left" → "right"）。
    - 合成影像必須準確反映描述裡提到的物件、屬性與關係
- BiAug
- ARMADA


### Synthetic Data
#### 圖像合成
- PlotQA: Reasoning over Scientific Plots
  - 有基本文字資料
  - 將統計資訊轉換成統計圖表
  - QA以Template生成
#### 文本合成
- VQA-NLE: Towards Efficient and Robust VQA-NLE Data Generation with Large Vision-Language Models
- SK-VQA
- 
#### 圖文配對合成
##### 普通Image
- StableLLaVA: Enhanced Visual Instruction Tuning with Synthesized Image-Dialogue Data
- Synth2: Boosting Visual-Language Models with Synthetic Captions and Image Embeddings  
##### 專業圖像(Text-Rich)
  - CoSyn: Scaling Text-Rich Image Understanding via Code-Guided Synthetic Multimodal Data Generation
  - FiqureQA: An Annotated Figure Dataset for Visual Reasoning
    - 
  - DVQA: Understanding Data Visualizations via Question Answering
    - Bar Chart
    - 圖中的文字簡短(欄位名)
    - QA以Template生成

## 應用情境
- 多輪對話(圖文交雜)
- 單圖對話
- 跨圖推理
- 
### reference

### 中文文字辨識 Chinese Text Recognition
- GPT-4V表現不佳
  - <img width="858" height="756" alt="image" src="https://github.com/user-attachments/assets/a82ccf3e-b420-4260-8829-6245b8d203c9" />
- GPT-5宣稱有改善，但具體表現仍輸給Owen3-VL
  - <img width="727" height="798" alt="image" src="https://github.com/user-attachments/assets/baaea6a6-0976-4487-85b0-d8190455c39a" />
#### 資料集
- OCRBench
- OCRBench_v2-zh
- DocVQA
- CC-OCR
#### reference
- [EXPLORING OCR CAPABILITIES OF GPT-4V(ISION) : A QUANTITATIVE AND IN-DEPTH EVALUATION](https://arxiv.org/pdf/2310.16809)
- [Qwen3-VL Technical Report](https://arxiv.org/pdf/2511.21631)
## reference
- [LEARNING MULTIMODAL DATA AUGMENTATION IN FEATURE SPACE](https://arxiv.org/pdf/2212.14453)
  - 目標ˋ
    - 在潛在表徵 (latent representation) 上進行增強
      - 將增強轉換設計為可學習模組
      - 該模組與多模態網路一同訓練，透過對抗式訓練生成具資訊性的數據
      - 並透過一致性正則化 (consistency regularization) 來維持語意結構
      - 同時學習一個增強網路 G 與多模態任務網路 F
  - 作法
    - 把每個模態（文字、影像、表格）的輸入先丟進對應的骨幹網路（例如 BERT、ResNet），得到每個模態的特徵表示（embedding）
    - LeMDA 的 增強網路 (G) 在這些特徵表示上「學習」如何做合適的改動，產生新的「增強特徵」
      - cross-attention
      - concatenate
      - ensemble
    - 原始特徵 + 增強特徵一起送進下游任務網路進行訓練。 
- [ARMADA: Attribute-Based Multimodal Data Augmentation](https://arxiv.org/pdf/2408.10086v1)
  - 2024
  - 目標：針對圖像中的關鍵物件，透過修改文本中提及關鍵物件的視覺屬性值 來生成新圖像。
  - 資料：維基百科(WikiData& Image)
  - 模型：
    - 圖像編輯 InstructPix2Pix 
  - 作法
    - 先從KB(Wiki)取得文字-圖像配對 (文字通常為圖片描述或標籤)
    - 文字：
      - 實體抽取：透過LLM從文本中識別出實體、視覺屬性及其值
        - 例如：實體(海星)、視覺屬性(顏色)、屬性值(藍色)
      - 替換策略1：基於知識庫的屬性替換
        - 建構知識庫：基於實體建構，確保替換準確率、可靠性(*非任意替換)
          - 圖結構
            - Wiki條目(linckia laevigata) 與 Wiki條目(linckia guildingi) 都屬於「valvatida」類別，因此在知識圖中，它們各自有一條邊指向該父節點
          - 節點屬性
            - 從對應的 Wikipedia 條目中蒐集文本，並利用 LLM 抽取節點的視覺屬性及可能值
        - 屬性替換策略
          - 單一實體內部替換（Intra-entity substitution)：當前實體保持不變，但替換其屬性值
          - 跨兄弟實體替換（Cross-sibling substitution）：在 KB 中，許多屬於同一父類別的實體共享相似屬性。
      - 替換策略2：基於 LLM 的視覺屬性替換
        - 有時候，抽取的實體或屬性過於一般化，無法與 KB 中的節點對應（如背景「珊瑚礁」）
        - 使用 LLMs 來補充替代屬性值 
    - 圖像
      -  得到修改後的文本，編輯相對圖像，生成新圖像
        -  舉例：透過指令呼叫模型生成新圖「將 [entity] 的 [attribute] 改為 [value]」
  - 資料檢查
    - 使用 Fréchet Inception Distance (FID) 分數來衡量新舊圖之間相似性
- [Multimodal Data Augmentation for Image Captioning using Diffusion Models]
  - 2023
  - 聚焦：多模態資料增強方法
  - 資料來源
      - 基於MS COCO資料集(包含圖片&圖片描述
  - 生成方式
    - 基於資料集原有描述生成圖像
    - 先改寫資料集描述，再生成圖像
  - 資料品質評估
    - 影像–文字相似度：使用 CLIPScore（基於 CLIP 嵌入）的度量，衡量合成影像與輸入文字的一致性。
    - 關鍵物件是否被反映：使用 VIFIDEL，透過物件偵測找出影像主物件，並計算其與描述文字的相似度。
- [SCIGRAPHQA: A LARGE-SCALE SYNTHETIC MULTI-TURNQUESTION-ANSWERING DATASET FOR SCIENTIFIC GRAPHS](https://arxiv.org/pdf/2308.03349)
  - 2023 
- [Scaling Text-Rich Image Understanding via Code-Guided Synthetic Multimodal Data Generation](https://yueyang1996.github.io/papers/cosyn.pd)
  - Allen AI
  - 核心概念：文字密集型圖像通常是由程式碼渲染
    - 使用程式碼 C 作為中介表示，來橋接圖像與文字
    -  - 將底層程式碼作為合成圖像的文字表示
  - 目標
  - CoSyn提示LLM生成程式碼（如 Python、HTML、LaTeX 等），以渲染合成圖像
    - 11 種渲染工具
    - 20 條流程  
    - CoSyn: Code Guided Synthetic data generation system
  - 特色
    - 「指向數據」（pointing data）:
      - VLMs 能夠在輸入圖像中對資訊進行定位
      - 在圖像中提供具體座標，實現「指到答案」的功能
      - 所有圖像的源碼 → 可提示 LLM 修改程式碼，在圖像上標記點位
      - LLM 生成「指向問題」並在程式碼中畫出帶顏色的點
      - 從程式碼中提取 (x, y) 像素座標，即可獲得精確標註
  - 步驟
    - 主題生成（Topic generation）：基於抽樣的人格設定（persona），決定風格與內容。
      - 舉例：抽樣出 persona 「一位喜歡外星世界的科幻小說家」，因此主題變為「一本關於外星動植物的小說」，進而生成相應的書籍封面圖像
    - 數據生成（Data generation）：生成具體內容並轉換為程式碼。
    - 程式碼生成（Code generation）：生成可執行程式碼並渲染合成圖像。
    - 指令生成（Instruction generation）：以程式碼作為上下文，提示 LLM 生成相應的文字指令（問題、答案與 Chain-of-Thought 推理解釋）。
  - 支援圖像
    - 圖表（Charts）
    - 文件（Documents）
    - 數學題（Math problems）
    - 表格（Tables）
    - 流程圖/結構圖（Diagrams）
    - 向量圖（Vector graphics）
    - 樂譜（Music sheets）
    - 電路圖（Electrical circuits）
    - 化學結構（Chemical structures）
  - 渲染工具
    - 圖表類：Matplotlib、Plotly、Vega-Lite
    - 文件與表格：LaTeX、HTML
    - 流程圖/結構圖：Mermaid、Graphviz
    - 向量圖與數學內容：SVG、Asymptote
    - 專用領域：Lilypond（樂譜）、RDKit（化學結構）
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
