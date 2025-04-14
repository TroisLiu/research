# Mixture of Expert (MoE)
## 相關議題
### 擴展行為（scaling behavior）
- 傳統的SB是將模型的驗證損失𝐿與參數數量𝑁以及訓練token數量𝐷相關聯
- 未納入Expert做考量
####「損失最優預算分配」（loss-optimal budget allocation）
- 結合對訓練成本 𝐶(𝑁, 𝐷)的估計，可以在給定訓練預算𝐶_0的情況下，找出一組最佳配置 (𝑁,𝐷)，以最小化驗證損失
#### 納入Expert

### 推論效率（inference efficiency）
- LLM 的推論過程對記憶體的需求極高，因為需要儲存所有 token 的中間狀態（即 KV-cache）
- 配置為𝐸max個專家的損失最優（loss-optimal）方案在理論上效果最佳，但這樣的設計在實際部署中並不切實可行。
  - 原因：
    1. 過多的專家數量會使模型在推論階段變得難以運行。
    2. 當專家數量增加，留給 KV-cache 的可用記憶體空間會被進一步壓縮，會導致批次大小（batch size）減少，進而降低吞吐量，導致每次查詢的成本增加


### reference
- [Toward Inference-optimal Mixture-of-Expert Large Language Models](https://arxiv.org/html/2404.02852v1)
  - 貢獻
    - 研究了 MoE 大型語言模型的擴展法則，揭示了驗證損失與三個關鍵因素（模型規模、數據集大小與專家數量）之間的關係
      - 提出納入Expert做loss考量的scaling behavior
    - 引入了一種新的視角來分析 MoE 模型的最佳訓練預算分配，將推論成本納入關鍵考量
      - 新評估指標：**每token成本（cost per token）**
    - 證明，採用更多專家但規模較小的過度訓練 MoE 模型，能夠在模型品質與推論效率上超越規模較大、專家數量較少的模型。
      - 新策略：**「過度訓練（over-trained）」策略**
        - 使用遠小於損失最優規模的模型進行訓練。雖然這樣的模型會帶來輕微的效能損失，但能夠大幅降低推論成本
        - 較小的模型節省下的預算可以用於增加訓練 token 的數量，因此我們稱這種設計為「過度訓練配置」
  - 因記憶體考量，認為過多的專家數量會使模型在推論階段變得難以運行
- [Unified Scaling Laws for Routed Language Models](https://proceedings.mlr.press/v162/clark22a.html)
  - 引入了一個乘法因子，用以描述模型參數數量𝑁與專家數量𝐸之間的交互
  - 當專家數量𝐸過多時，模型效益會出現遞減現象，因此引入了飽和閾值 𝐸max
  - 分析了三種不同的 Routing Network 訓練技術
    - Sinkhorn-BASE：這是一種稀疏專家混合（SMOE，Sparse Mixture-of-Experts）方法，基於 BASE（Lewis 等人，2021）進行修改
    - 非參數化的 HASH 層（Roller 等人，2021）；
    - 強化學習路由（RL-R）：這是一種使用於早期路由研究中的技術（Bengio 等人，2013）。
  - 結論：
    - Routing 能夠在所有模型規模與變體中提升語言模型性能
    - 使用強化學習（RL）訓練 Routing Network，其效果可媲美最先進的方法
    - 所有 Routing Network 的性能均可被專家數量與底層密集模型規模的擴展法則準確描述（這一法則是對 Kaplan 等人（2020）提出的擴展法則的推廣)
    - 這些擴展法則還可以轉換為基於「參數總量」與「推論計算量」的形式
    - 這些法則進一步推導出一個「有效參數數量（Effective Parameter Count）」的概念
  - 缺點：
    - 未考慮訓練資料集規模𝐷對模型效能的影響
    - 無法針對給定的訓練預算，提出損失最優配置建議
- [Mixture of A Million Experts](https://arxiv.org/html/2407.04153v1)
  - 一味的增加acitve expert不一定能改善表現效能
  - 貢獻:
    - 探索極端 MoE 設定
    - 可學習的路由索引結構
    - 設計： Parameter Efficient Expert Retrieval (PEER) 層
      - 結合產品鍵路由（product key routing）
      - 單神經元專家（single-neuron experts）  
- [Parameters vs FLOPs: Scaling Laws for Optimal Sparsity for Mixture-of-Experts Language Models](https://openreview.net/pdf?id=6OT73DA0dF)
  - 發現在不同的限制條件下（例如參數規模或總訓練計算量），都存在一個「最佳稀疏度水平」
  - 重點研究不同稀疏程度（即不活躍參數所佔比例）如何影響模型在預訓練過程以及下游評估中的表現
- [Joint MoE Scaling Laws: Mixture of Experts Can Be Memory Efficient](https://arxiv.org/html/2502.05172v2)
  - 探討了三種不同模型規模下（20 億參數、50 億參數、以及 100 億參數）的最優專家數量，並且在三種不同的情境中進行比較，3個情境下不同Expert數量的表現不同
    - 僅考慮模型規模
    - 包含 32k tokens 的 KV-cache 大小
    - 包含處理 1000 億 tokens 的推理成本
- [Mixture-of-Experts (MoE): The Birth and Rise of Conditional Computation](https://cameronrwolfe.substack.com/p/conditional-computation-the-birth)
## 模型
### Mixtral 8x7B
- 總共有467億個參數
- 稀疏混和專家(SMoE)模型架構
  - Layer設計
    - ![image](https://github.com/user-attachments/assets/19d4c450-a234-4210-848d-f34a0b808e14)
    - 每個專家就是標準的FFN
    - 8個不同領域的7B模型
    - 每次輸入只會觸發一小部分專家
      - 在處理每個Token時，模型只會選擇並使用其中的129億個參數
      - 經過訓練的路由器，能夠根據輸入資料的特性，分配任務給2個最適合的專家
  - MoE 層是針對每個 token 獨立應用的，並且取代了 Transformer 區塊中的前饋（FFN）子區塊
  - 2個專家各自處理完輸入後，專家輸出會被整合成最終的輸出
  - 整個模型有32層，代表共有256(32*8)個Expert
- 能處理32k Token上下文
- 缺點：
  - Expert負載均衡機制未做好
    - 若刪除Expert3, 模型會Fail
    - 刪除其他Expert，模型表現都差不多
- 與GShard比較
  - Mixtral將所有FFN都替換成MoE Layer
  - GShard每隔一個FFN才替換一次
- 與LLaMA比較
  - 將MLP layer复制成了8个expert layers并在一起
  - 通过一个gate layer，对每个token选择top-2的专家模型进行计算
  - 
##### Reference
 - [Mixtral of Experts](https://arxiv.org/pdf/2401.04088)
 - [Mixtral-8x7B 模型挖坑](https://zhuanlan.zhihu.com/p/674751021)
   - Mixtral-8x7B利用Mistral-7B訓練過程中early-stage的checkpoint訓練
   - Attention layer是直接繼承checkpoint
   - FFN複製8份
   - 再加上gate layer完成後續預訓練

#### DeepSeekMoE
- 目標：實現更高效的專家特化，提高 MoE 模型的知識利用效率，並減少冗餘計算成本。
- 傳統MoE
  - 傳統 MoE 架構中，Transformer 的前饋神經網路（Feed-Forward Networks, FFNs）通常被 MoE 層（MoE Layers） 取代。
  - 每個 MoE 層 由多個專家組成，每個專家的結構與標準 FFN 相同
  - 而每個 token 會被分配給一個（Fedus et al., 2021）或兩個（Lepikhin et al., 2021）專家。
  - 有兩大缺陷阻礙了 專家特化（Expert Specialization）：讓每個專家學習非重疊且高度聚焦的知識。  
- 傳統MoE的缺陷：
  - 知識混雜（Knowledge Hybridity)
    - 現有 MoE 方法通常使用有限數量的專家（例如 8 或 16 個），因此被分配到某個專家的 token 可能涉及多種不同的知識領域。
    - 單個專家需要存儲大量不同類型的知識，這些知識可能無法在推理時同時高效利用，從而影響模型的性能。
  - 知識冗餘（Knowledge Redundancy
    - 被分配到不同專家的 token 可能仍然需要共享某些常見知識（Common Knowledge）
    - 多個專家可能會在各自的參數中學習重複的知識，導致專家層的參數冗餘（Parameter Redundancy），進一步影響模型的效率與推理能力。
- 3個關鍵架構
  - 精細劃分專家（Fine Segmentation of Experts) : 將專家從 N 細分為 𝑚𝑁 個，並從中激活 𝑚𝐾 個
    - 保持總參數數量不變的前提下，我們透過劃分 FFN（前饋神經網路）的中間隱藏維度，將專家進行更細粒度的劃分
    - 保持總計算成本不變的條件下，我們激活更多的細粒度專家，從而形成更靈活且適應性更強的專家組合。
      - 當N=16，若路由策略一次選TOP-2 => C16取2 > 120種組合
      - 當N=64，路由策略調整為一次選TOP-8 => C64取8 > 4,426,165,368種 
  - 共享專家隔離(Isolation of Shared Experts):將𝐾𝑠個專家作為共享專家(Shared Experts)用於學習通用知識，以減少路由專家(Routed Experts)之間的冗餘
    - 將部分專家隔離為「共享專家（Shared Experts）」
    - Shared Experts始終被激活
      - 用於捕捉和整合通用知識（Common Knowledge）
      - 所有 token 都會被確定性地分配到這些專家。
    - 確保路由專家（Routed Experts）能夠專注於學習獨特且專精的知識
  - 負載均衡考量 (Load Balance Consideration): 為了避免以下2缺陷
    - 路由崩潰（Routing Collapse）：模型可能總是選擇少數幾個專家，導致其他專家無法獲得足夠的訓練機會，從而影響整體的專家多樣性與泛化能力。
    - 計算瓶頸（Computation Bottlenecks）：如果專家分佈在多個設備上，負載不均衡可能導致某些設備的計算資源過載

#### LLaMA4
- 預訓練
  - 訓練資料
    - Llama 4 的預訓練覆蓋多達 200 種語言
      - 超過 100 種語言的 token 數量各自超過 10 億
      - 整體多語言 token 數量是 Llama 3 的 10 倍
    - 整體訓練資料集超過 30 兆 tokens
      - 是 Llama 3 預訓練資料集的兩倍以上
    - 涵蓋多樣的文字、圖像與影片數據。
  - 訓練精度
    - 採FP8
    - 使用 FP8 精度並部署 32,000 張 GPU 預訓練 Llama 4 Behemoth 時，我們實現了每張 GPU 達到 390 TFLOPs 的運算效能
  - 新訓練技術 MetaP
    - 能穩定地設置模型關鍵超參數(e.g. 每層學習率、初始化比例
    - 這些超參數選擇在不同的 batch size、模型寬度、深度以及訓練 token 數量之間具有良好的遷移性
  - 訓練策略：「中期訓練」（mid-training）
    - 透過專門的資料集延伸長上下文能力並改進核心模型能力
    - 支援1000 萬字輸入上下文長度
  - 後訓練階段
    - 最大挑戰：如何平衡多模態輸入、推理能力與對話能力
    - 設計了一套精心策劃的課程策略，在不犧牲單一模態專家模型性能的前提下，實現了優秀的表現
      - 輕量級監督微調（SFT）> 線上強化學習（RL）> 輕量級直接偏好優化（DPO）。
        - SFT 和 DPO 過度約束模型，會限制模型在線上 RL 階段的探索能力，導致在推理、程式編碼和數學領域中的表現不佳
        - 使用"自適應數據篩選"，在SFT, RL階段，過濾掉標記簡單的資料/提示
      - SFT
        - 使用 Llama 模型作為判斷者，篩除掉超過 50% 的「容易」標記數據，僅對剩下的高難度數據進行輕量級 SFT
      - RL
        - 多模態線上 RL 階段，精選更具挑戰性的提示（prompts)
        - 採用"持續性線上強化學習策略"，在訓練模型的過程中不斷交替進行訓練與數據過濾，只保留中等至高難度的提示
      - DPO
        - 專門處理模型回應品質中的邊緣案例 
- 多Expert 機制
  - Llama 4 Scout 擁有 170 億個有效參數、配備 16 個專家的模型
    - 總計 1090 億參數 
    - 來自 Llama 4 Behemoth 的知識蒸餾
    - Int4 量化後可部署在單顆 H100 GPU 上
    - 支援1000萬Token
  - Llama 4 Maverick 擁有 170 億個有效參數，但配備多達 128 個專家
    - 總參數量則高達 4000 億
    - 設計上交替使用密集層與混合專家（MoE）層，以提升推理效率
    - 來自 Llama 4 Behemoth 的知識蒸餾
    - 可部署在單一 H100 主機上
  - Llama 4 Behemoth 擁有 2880 億個有效參數、配備 16 個專家
    - Llama 4 Behemoth 目前仍在訓練中
    - 徹底重新設計和優化整個訓練流程
      - 對比於小型模型只刪減 50% 的 SFT（監督微調）數據，在 Behemoth 模型中刪除了多達 95% 的 SFT 數據，以確保訓練數據的質量與效率
    -  RL 訓練策略
      - 聚焦於使用 policy 模型執行 pass@k 分析，挑選困難的提示（prompts），並設計一套逐步增加提示難度的訓練課程。
      - 在訓練過程中動態篩選出「無優勢」的提示，並將多種不同能力的提示混合進批次訓練中
      - 從多種系統指令（system instructions）中進行採樣，也對模型保持良好的指令理解能力
      - 全異步線上強化學習框架
- 交錯式注意力層（interleaved attention layers）
  - 取代傳統的位置嵌入（positional embeddings） 
- MoE Layer
  - 簡述
    - <img width="489" alt="image" src="https://github.com/user-attachments/assets/07ae112a-1e61-4a27-af2b-1c989b22a06d" />
    - 每個標記（token）只會啟用部分參數。
    - 這種架構在訓練和推理時的運算效率更高，在相同的訓練 FLOPs 預算下，相比傳統的密集模型，能夠帶來更優異的效果。
  - 運作機制
    - 設計上交替使用密集層與混合專家（MoE）層，以提升推理效率
    - MoE 層包含N個可路由的專家（routed experts）以及一個共享專家（shared expert）。
    - 每個 token 會同時傳送至共享專家以及N個可路由專家中的其中一個。
    - 雖然所有參數都存儲於記憶體中，但在模型運行時，實際啟用的僅是總參數中的一小部分。
    - 有效提升推理效率，降低模型部署成本與延遲
- Early Fusion(早期融合)
  - 將文字與視覺 token 無縫整合至統一的模型架構中
  - 使用大量未標註的文字、圖像和影片資料進行聯合預訓練
  - 優化Llama 4 的視覺編碼器，該編碼器以 MetaCLIP 為基礎，並與一個凍結的 Llama 模型一同獨立訓練
- iRoPE 架構
  - 「i」代表 interleaved attention layers（交錯式注意力層）
  - 「RoPE」則是指大多數層中所使用的 旋轉位置編碼（rotary position embeddings）。 
  - 在推理階段對注意力機制進行了溫度調整，進一步增強了模型的長文本泛化能力 
##### Reference

## 資料集
