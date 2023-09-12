# Research
paper &amp; learning tutorial reading list

## Knowledge 
### Knowledge Augmentation
### Knowledge Extraction
### Knowledge Graph
### Knowledge Fusion
### Knowledge Inference
### Knowledge Update
### Knowledge Validation

## LLM
### 機制
#### Chain of Thought

#### Tree of Thought
- [(2023)Tree of Thoughts: Deliberate Problem Solving with Large Language Models.md](https://github.com/TroisLiu/research/blob/master/Tree%20of%20Thoughts%3A%20Deliberate%20Problem%20Solving%20with%20Large%20Language%20Models.md)

#### 聯邦學習
##### reference
- [思考一下，联邦学习可以训练大语言模型吗？](https://www.jiqizhixin.com/articles/2023-07-10-4)

#### Machnine Unlearning
##### reference
- [给机器下「遗忘咒」？谷歌发起首个机器遗忘挑战赛](https://www.jiqizhixin.com/articles/2023-07-09-4)

### 微調
#### Adapter
- 介紹
- 優點
  - 参数效率更高：一个任务只需要少量参数，训练更快，占用的内存更少，对数据集较小的任务更难过拟合，也更有利于模型的存储和分发
  - 连续学习的遗忘问题：Adapter 冻结了原有模型的参数，保证了原来的知识不被遗忘。
  - 多任务学习：使用 adapter 也可以用比较少量的参数学习多个任务
#### LoRA (Low-Rank Adaptation of LLM)
- PEFT(Parameter-Efficient Fine-Tuning)的技術之一
- 介紹：在原模型基礎上，增加Adapter於另一分支路徑，訓練時可擇一路徑訓練
  - 微調時，走Adapter路徑，只更新Adapter的參數，保持原PLM參數不便
  - 推論時，走原路徑，代表只使用原模型參數，沒有遺忘原本知識
  - 推論時，走Adapter路徑，走微調知識，不走原PLM知識
- 優點
  - 低秩分解：從單一大矩陣W(d*d) 變成兩個小矩陣U (d*r)與V (r*d)的乘積，項低計算複雜度
    - 将 LoRA 的秩r设置为预训练权重矩阵的秩，就能大致恢复了全量微调的表现力。也就是说，随着增加可训练参数的数量，训练 LoRA 大致收敛于训练原始模型。
  - 多粒度：可以針對詞、段落、Downstream-task多種顆粒度去建模
  - 異構框架：可應用於不同結構的transformer當中
  - 無監督學習：使用預測-聚合損失函數進行adapter的參數學習，無須另外標註資料
  - 没有额外的推理延时
  - 减少内存和存储资源消耗
- 痛點
  - 參數空間小：LoRA中参与训练的参数量较少，解空间较小，效果相比全量微调有一定的差距。
  - 微调大模型成本高：对于上百亿参数量的模型，LoRA微调的成本还是很高。
  - 精度损失：针对第二点，可以采用int8或int4量化，进一步对模型基座的参数进行压缩。但是又会引发精度损失的问题，降低模型性能。
- 與传统的多任务学习相比
  - 好处：不同任务之间影响较少
  - 坏处：不同任务带来的相互的监督可能会变少 
##### reference
- [详解大模型微调方法LoRA Adapter(内附实现代码)](https://mltalks.medium.com/%E8%AF%A6%E8%A7%A3%E5%A4%A7%E6%A8%A1%E5%9E%8B%E5%BE%AE%E8%B0%83%E6%96%B9%E6%B3%95lora-adapter-%E5%86%85%E9%99%84%E5%AE%9E%E7%8E%B0%E4%BB%A3%E7%A0%81-aff4fb42beec)
- [ICLR2022高分文章：将Adapter、prompt-tuning、LoRA等纳入统一的框架](https://zhuanlan.zhihu.com/p/436571527)
- [微調大型語言模型LLM的技術LoRA及生成式AI-Stable diffusion LoRA](https://xiaosean5408.medium.com/%E5%BE%AE%E8%AA%BF%E5%A4%A7%E5%9E%8B%E8%AA%9E%E8%A8%80%E6%A8%A1%E5%9E%8Bllm%E7%9A%84%E6%8A%80%E8%A1%93lora%E5%8F%8A%E7%94%9F%E6%88%90%E5%BC%8Fai-stable-diffusion-lora-61a41d636772)
- [【peft】huggingface大模型加载多个LoRA并随时切换](https://blog.csdn.net/liuqixuan1994/article/details/130664198)
  - 介紹如何使用 Huggingface - PEFT
- [LLM - LoRA 模型合并与保存](https://bitddd.blog.csdn.net/article/details/132065177)
  - LoRA透過merge_and_unload() ：可將adapter與基本模型合併載入，消除延遲
  - Code實作介紹
- [Github - Huggingface - PEFT](https://github.com/huggingface/peft)
  - 開源框架PEFT
    - LoRA:
      - 通过学习小参数的低秩矩阵来近似模型权重矩阵W的参数更新，训练时只优化低秩矩阵参数。
      - 參考前面說明 
    - P-Tuning:    
      - 利用少量连续的 embedding 参数作为 prompt 使 GPT 更好的应用于 NLU 任务
      - P-Tuning 只在 embedding 层增加参数
      - P-Tuning V2方法的思路其实和 Prefix-Tuning 相似，在模型的每一层都应用连续的 prompts 并对 prompts 参数进行更新优化。同时，该方法是针对 NLU 任务优化和适配的。
    - Prefix Tuning:
      - Prefix-Tuning 是针对 NLG 任务设计
      - 固定 PLM 的所有参数，只更新优化特定任务的 prefix
      - Prefix-Tuning 在每一层都添加可训练参数
      - 在模型输入前添加一个连续的且任务特定的向量序列（continuous task-specific vectors），称之为前缀（prefix）。前缀被视为一系列“虚拟 tokens”
      - 優點
        - 只需要为每个任务存储特定 prefix，使 Prefix-tuning 模块化且节省存储空间
        - Prefix-tuning 性能好于 Infix-tuning，因为 prefix 能够同时影响x和y的隐层激活，而 infix 只能够影响y的隐层激活
      - 缺點
        - Prefix Tuning 难以优化，其性能随可训练参数规模非单调变化，
        - 为前缀保留部分序列长度必然会减少用于处理下游任务的序列长度。 
    - Prompt Tuning:
      - 可以看做是 Prefix Tuning 的简化
      - 固定整个预训练模型参数，只允许将每个下游任务的额外k个可更新的 tokens 前置到输入文本中，也没有使用额外的编码层或任务特定的输出层。
      - 在模型的输入或隐层添加K个额外可训练的前缀 tokens（这些前缀是连续的伪 tokens，不对应真实的 tokens），只训练这些前缀参数；
      - 提出了 Prompt Ensembling 方法来集成预训练语言模型的多种 prompts
        - 在同一任务上训练n个prompts，为一个任务创建了n个单独的模型，同时在整个过程中共享核心的预训练语言建模参数
    - Adapter-Tuning (PEFT技術之一，如LLM-Adapters是其實作)
      - 前兩個是添加prompt embedding 参数来以少量参数适配下游任务
      - 将较小的神经网络层或模块插入预训练模型的每一层，这些新插入的神经模块称为 adapter（适配器），下游任务微调时也只训练这些适配器参数
      - 種類：主要包括 Series Adapter（串行） 和 Parallel Adapter（并行）
      - 缺點 
        - Adapter Tuning 在 PLM 基础上添加适配器层会引入额外的计算，带来推理延迟问题
- [LLM-Adapters: An Adapter Family for Parameter-Efficient Fine-Tuning of Large Language Models](https://arxiv.org/abs/2304.01933)
  - LLM-Adapter是对 PEFT 库的扩展，是一个简单易用的框架，将各种适配器集成到 LLM 中
    - PEFT4種方法
    - AdapterH
    - AdapterP
    - Parallel
      - 将适配器模块与每层 Transformer 的多头注意力和前馈层并行计算集成。 
- [大模型参数高效微调(PEFT)](https://zhuanlan.zhihu.com/p/621700272)
  - 介紹Huggingface - PEFT
  - 介紹LLM-Adapters 
### 加速技巧
#### Optimizer

#### Layer Dropping
- [(2020)Accelerating Training of Transformer-Based Language Models with Progressive Layer Dropping](https://github.com/TroisLiu/research/blob/master/Accelerating%20Training%20of%20Transformer-Based%20Language%20Models%20with%20Progressive%20Layer%20Dropping.md)
#### Sparse Attention

#### Knowledge Distillation
- 抽取複雜模型訓練出的精華為另一個簡單模型所用，讓這個小的簡單模型也能達到跟複雜模型一樣的效果
  -  採用teacher/student師徒概念的框架來實現，由teacher模型先訓練好權重後，再抽取（蒸餾）精華作為student模型的訓練教材，讓student也能達到比美teacher的效果
  -  精華是指訓練好的參數權重，也有一些人稱為Dark knowledge（暗知識）。   
-  Geoffrey Hinton, Oriol Vinyals及Jeff Dean在2015年的論文「Distilling the Knowledge in a Neural Network」中推廣
-  [(2022)LLM-QAT: Data-Free Quantization Aware Training for Large Language Models](https://github.com/TroisLiu/research/blob/master/LLM-QAT%3A%20Data-Free%20Quantization%20Aware%20Training%20for%20Large%20Language%20Models.md)
##### Reference
- [知識蒸餾 KnowledgeDistillation](https://chtseng.wordpress.com/2020/05/12/%E7%9F%A5%E8%AD%98%E8%92%B8%E9%A4%BE-knowledgedistillation/#:~:text=Knowledge%20Distillation%E4%B8%AD%E8%AD%AF%E7%82%BA,%E8%9B%BB%E8%AE%8A%E6%88%90%E7%82%BA%E7%BE%8E%E9%BA%97%E7%9A%84%E8%9D%B4%E8%9D%B6%EF%BC%8C%E3%80%82)
- 

### 模型
- [生成式 AI 的技術門檻，護城河並非不可逾越](https://hitripod.com/generative-ai-might-have-no-moat/)
#### LLaMA
- LongLLaMA
  - [将上下文长度扩展到 256k，无限上下文版本的OpenLLaMA来了？](https://www.jiqizhixin.com/articles/2023-07-10-3)
  - 基於OpenLLaMA實作
  - 可商用
  - 採用FOT （ Focused Transformer )微調
#### LLaMA2
#### GPT
#### Bloomz

### 資料集
- 待閱讀
  - [(2021)Deduplicating training data makes language models better](https://arxiv.org/pdf/2107.06499.pdf)
    - 去除資料中重複的部分
  - [Scaling Data-Constrained Language Models](https://arxiv.org/pdf/2305.16264.pdf)
    - 命題：當資料不夠，能做什麼?
    - Focus: quantify the impact of multiple epochs in LLM training such that practitioners can decide how to allocate compute when scaling models
    - 提出 data-constrained scaling law, 通用化 Chinchilla scaling law
    - 儘管僅訓練單一epoch的模型始終具有最佳的驗證損失和計算效益，但在訓練多達4個epoch的模型之間的差異往往不重要，並且不會導致下游任務性能的差異。
    - 持續訓練額外的epoch是有益的，但回報最終會趨近於零。
    - 在受限的數據情境下，將新的計算資源分配給更多的參數和時期是必要的，並且時期應該稍微加快


## Recommendation System
- [Recommender System.md](https://github.com/TroisLiu/research/blob/master/Recommender%20System.md)

### Survey
- [(2022)A Survey of Recommendation Systems: Recommendation Models, Techniques, and Application Fields](https://github.com/TroisLiu/research/blob/master/A%20Survey%20of%20Recommendation%20Systems%3A%20Recommendation%20Models%2C%20Techniques%2C%20and%20Application%20Fields.md)

## Natural Language Processing
### Entity Linking

## life-long learning
- [(2022)Lifelong Pretraining: Continually Adapting Language Models to Emerging Corpora](https://aclanthology.org/2022.naacl-main.351.pdf)
  - 2種不同類型的知識轉移
    - 領域轉移
    - 時間遞進
- [(2023)CONTINUAL PRE-TRAINING OF LANGUAGE MODELs](https://openreview.net/pdf?id=m_GDIItaI3o)
- [(2020)LAMOL: LANGUAGE MODELING FOR LIFELONG LANGUAGE LEARNING](https://arxiv.org/pdf/1909.03329.pdf)
  - Memory Replay手法
  - gpt-2為基底

### reference
- [【2023Q2】LLM炼丹trick拾遗：LLM的MoE架构与Lifelong learning](https://zhuanlan.zhihu.com/p/636604779)
  - Mixure of Expert, MoE 
