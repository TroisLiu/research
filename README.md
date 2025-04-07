# Research
paper &amp; learning tutorial reading list
## Quick Link
- [Topic - Dialogue System](https://github.com/TroisLiu/research/blob/master/README.md#dialogue-system)
- [Topic - LLM](https://github.com/TroisLiu/research/tree/master#llm)
- [Topic - 推薦系統](https://github.com/TroisLiu/research/tree/master#recommendation-system)
- [Topic - Knowledge](https://github.com/TroisLiu/research/tree/master#knowledge)

## Dialogue System
### 對話分類
#### 開放域對話（open-domain dialogue）
- 目標：
  - 需要展現出知識淵博、風趣且語言自然的特性
  - 追求對話的豐富性和連貫性，比如閒聊、問答、娛樂
  - 避免不當言論
  - 強調個性化和情感溝通等品質
- LLM：
  - 優點：經過大規模預訓練的模型本身掌握了廣泛的常識和知識，能夠就各種話題侃侃而談，並產生類似人類風格的回應
  - 缺點：容易天馬行空：例如對於未知領域的提問，模型可能不願承認自己的不知道，而編造看似合理但實則錯誤的答案（即幻覺問題）
  - 
#### 任務導向對話（task-oriented dialogue, TOD）
- 目標：
  - 聚焦在完成特定目標上
  - 如: 預訂餐廳、查詢列車時刻
  - 需要與後端資料庫或API交互，遵循嚴格的業務邏輯
  - 要求對話精確且高效
- LLM：
  - 優點：讓LLM直接根據對話歷史生成對話行為和槽位填充結果，再據此產生回應，減少模組誤差傳遞
  - 缺點：容易天馬行空：例如對於未知領域的提問，模型可能不願承認自己的不知道，而編造看似合理但實則錯誤的答案（即幻覺問題）
- 作法
  - LLM在對話中自動決定何時調用API並填入參數，執行如資料庫查詢、計算等操作後再繼續對話
  - 混合式系統成為熱門方案：讓LLM處理自然語言理解和生成部分，但關鍵決策仍由顯式策略模組或規則把關
- 挑戰：
  - 當任務流程複雜或需要遵循業務政策時，僅靠在Prompt中添加大量指示往往不足以約束LLM的行為
  - LLM在任務型對話中的幻覺和不服從問題仍需關注
  - LLM在嚴格遵守對話流程上仍缺乏機制，開發者難以完全控制其每一步行為
#### 融合風格對話
- 在主要任務流程中穿插寒暄和安撫，用戶既能得到任務結果也有良好體驗​
- 挑戰：
  - 如何動態調節兩種對話模式的切換、確保不互相干擾
- 
### 相關研究議題
#### 對話狀態追蹤(Dialogue State Tracking, DST)
- LLM較善於利用完整對話語境來糾正先前輪次可能出現的錯誤
- LLM-DST的整體性能隨對話輪數增加而下降得更慢，對錯誤傳播（error propagation）的抵抗力比傳統模型更佳
- 用對話狀態（如槽位-值集合）作為對話歷史的高度摘要
- 論文：
  - [(2023)Towards LLM-driven Dialogue State Tracking](https://aclanthology.org/2023.emnlp-main.48.pdf)
    - 提出LDST框架: 基於開源LLaMA模型透過指令微調(domain-slot instruction tuning)實現
  - [(2024)Chain of Thought Explanation for Dialogue State Tracking](https://arxiv.org/html/2403.04656v1)
    - 將CoT引入至DST當中，模型在決定槽位值後生成逐步推理的解釋
    - 能引導模型從相關對話輪中蒐集資訊並推理正確的槽值，從而提高預測的準確可靠性
  - [(2024)Enhancing Dialogue State Tracking Models through LLM-backed User-Agents Simulation](https://aclanthology.org/2024.acl-long.473.pdf)
    - 因人工標註成本高，利用LLM來模擬對話生成標註數據
    - 論文使用GPT-4充當用戶和代理人，生成帶有DST標籤的大量模擬對話，然後用這些合成數據對LLaMA 2模型進行兩階段微調，在MultiWOZ等資料集上取得優於僅用真實數據訓練的效果
#### RL與對話決策
- 類別
  - RLHF
  - RLAIF 
- 論文：
  - [(2024)Plug-and-Play Policy Planner for Large Language Model Powered Dialogue Agents](https://openreview.net/pdf?id=MCNqgUFTHI)
    - 讓一個可調參的小型策略模型作為插件指導LLM的對話決策
    - 以有標註數據進行監督微調，隨後採用自我對弈(self-play)方式讓LLM與自身模擬對話，透過AI代理提供的目標導向反饋進行強化學習​
    - 經過RL微調的策略插件可以顯著提升LLM在主動式對話任務，使LLM在沒有人工介入的情況下，不斷改進對話策略，並能快速遷移到新場景
#### 外部知識增強對話管理
- 透過RAG改善LLM幻覺議題
- 這在任務型問答、推薦對話系統當中會很有幫助
- 系統
  - stanford的KITA
  - ChatCRS：工具增強的知識檢索代理、目標規劃代理
#### 上下文管理與長程對話
- LLM受限於有限的上下文視窗，在特別長的對話仍面臨挑戰
- 多數模型在輸入超過數千Token後，性能會下降，難以掌握對話中跨越很多倫次的因果關係、時間脈絡
- 資料集
  - LoCoMo長程對話資料集
    - 每段對話300輪次，9K個詞以上，有數十次獨立對話
    - 用來評估LLM長程記憶能力
- 對應策略
  - 增加模型上下文窗口：但可能丟失對話中間內容
  - 動態檢索相關歷史：
    - 參考RAG概念，將超出上下文窗口的歷史對話內容存入向量化資料庫
    - 產生回應時，透過檢索相關的過往語句，將相關內容附加給LLM)
  - 摘要與壓縮歷史：
    - 隨著對話進行，不斷壓縮舊對話內容生成摘要
    - 提取出對話狀態，以壓縮表示方式融入上下文
  - 顯式對話記錄與狀態跟蹤
    - 構建一個外部記憶來存儲對話中的關鍵資訊或狀態，舉例
     - 之前已確認的事實
     - 用戶偏好
    - 每次需要產生回應時，將使用者輸入及相關資訊提供給模型
- 方法:
  - [(2024)StreamingDialogue: Prolonged Dialogue Learning via Long Context Compression with Minimal Losses](https://openreview.net/pdf?id=eNvVjpx97O)
    - 利用對話中特殊的「發話結束」標記（End-of-Utterance）作為會話注意匯聚點
    - 將長長的對話歷史壓縮到這些標記上，儘可能保留關鍵資訊
    - 其他策略，減少壓縮帶來的信息損失​
      - 短期記憶重構
      - 長期記憶再激活
    - 大幅降低隨著對話輪數增加的計算成本
    - 模型能處理超過數百輪對話而保持良好效果

## Knowledge 
- Knowledge Augmentation
- Knowledge Extraction
- Knowledge Graph
- Knowledge Fusion
- Knowledge Inference
- Knowledge Update
- Knowledge Validation

## LLM
### prompt engineering
####  原則 1：撰寫清晰且具體的指示
  - 策略 1：使用分隔符清楚地標示輸入中不同的部分
    ```
    prompt = f"""
    Summarize the text delimited by triple backticks \ 
    into a single sentence.
    ```{text}```
    """
    ```
  - 策略 2：要求提供結構化的輸出
    ```
    prompt = f"""
    Generate a list of three made-up book titles along \ 
    with their authors and genres. 
    Provide them in JSON format with the following keys: 
    book_id, title, author, genre.
    """
    ```    
  - 策略 3：要求模型檢查條件是否滿足
    ```
    prompt = f"""
    You will be provided with text delimited by triple quotes. 
    If it contains a sequence of instructions, \ 
    re-write those instructions in the following format:
    
    Step 1 - ...
    Step 2 - …
    …
    Step N - …
    
    If the text does not contain a sequence of instructions, \ 
    then simply write \"No steps provided.\"
    
    \"\"\"{text_1}\"\"\"
    """
    ```        
  - 策略 4：Few-shot Prompting「少量範例」提示
    ```
    prompt = f"""
    You will be provided with text delimited by triple quotes. 
    If it contains a sequence of instructions, \ 
    re-write those instructions in the following format:
    
    Step 1 - ...
    Step 2 - …
    …
    Step N - …
    
    If the text does not contain a sequence of instructions, \ 
    then simply write \"No steps provided.\"
    
    \"\"\"{text_1}\"\"\"
    """
    ```         
#### 原則 2：給模型時間進行「思考」
  - 策略 1：明確指出完成任務所需的步驟
    ```
    f"""
    Your task is to perform the following actions: 
    1 - Summarize the following text delimited by 
      <> with 1 sentence.
    2 - Translate the summary into French.
    3 - List each name in the French summary.
    4 - Output a json object that contains the 
      following keys: french_summary, num_names.
    
    Use the following format:
    Text: <text to summarize>
    Summary: <summary>
    Translation: <summary translation>
    Names: <list of names in summary>
    Output JSON: <json with summary and num_names>
    
    Text: <{text}>
    """
    ```
  - 策略 2：指導模型在得出結論之前先自行推導解決方案
    ```
    prompt = f"""
    Your task is to determine if the student's solution \
    is correct or not.
    To solve the problem do the following:
    - First, work out your own solution to the problem including the final total. 
    - Then compare your solution to the student's solution \ 
    and evaluate if the student's solution is correct or not. 
    Don't decide if the student's solution is correct until 
    you have done the problem yourself.
    
    Use the following format:
    Question:
    '''
    question here
    '''
    Student's solution:
    '''
    student's solution here
    '''
    Actual solution:
    '''
    steps to work out the solution and your solution here
    '''
    Is the student's solution the same as actual solution \
    just calculated:
    '''
    yes or no
    '''
    Student grade:
    '''
    correct or incorrect
    '''
    
    Question:
    '''
    I'm building a solar power installation and I need help \
    working out the financials. 
    - Land costs $100 / square foot
    - I can buy solar panels for $250 / square foot
    - I negotiated a contract for maintenance that will cost \
    me a flat $100k per year, and an additional $10 / square \
    foot
    What is the total cost for the first year of operations \
    as a function of the number of square feet.
    '''
    Student's solution:
    '''
    Let x be the size of the installation in square feet.
    Costs:
    1. Land cost: 100x
    2. Solar panel cost: 250x
    3. Maintenance cost: 100,000 + 100x
    Total cost: 100x + 250x + 100,000 + 100x = 450x + 100,000
    '''
    Actual solution:
    """
    ```
#### 其他Task
- Summary
  ```
      prompt = f"""
    Your task is to generate a short summary of a product \ 
    review from an ecommerce site. 

    Summarize the review below, delimited by triple \
    backticks in at most 20 words. 

    Review: ```{reviews[i]}```
    """
  ```
- Sentiment (Positive/Negative)
  ```
  prompt = f"""
  What is the sentiment of the following product review, 
  which is delimited with triple backticks?

  Give your answer as a single word, either "positive" \
  or "negative".
  
  Review text: '''{lamp_review}'''
  """
  ```
- Emotion
  ```
  prompt = f"""
  Identify a list of emotions that the writer of the \
  following review is expressing. Include no more than \
  five items in the list. Format your answer as a list of \
  lower-case words separated by commas.
  
  Review text: '''{lamp_review}'''
  """

  prompt = f"""
  Is the writer of the following review expressing anger?\
  The review is delimited with triple backticks. \
  Give your answer as either yes or no.
  
  Review text: '''{lamp_review}'''
  """
  ```
- Extract Entity
  ```
  prompt = f"""
  Identify the following items from the review text: 
  - Item purchased by reviewer
  - Company that made the item
  
  The review is delimited with triple backticks. \
  Format your response as a JSON object with \
  "Item" and "Brand" as the keys. 
  If the information isn't present, use "unknown" \
  as the value.
  Make your response as short as possible.
    
  Review text: '''{lamp_review}'''
  """
  ```
- Multiple Task at once
  ```
  prompt = f"""
  Identify the following items from the review text: 
  - Sentiment (positive or negative)
  - Is the reviewer expressing anger? (true or false)
  - Item purchased by reviewer
  - Company that made the item
  
  The review is delimited with triple backticks. \
  Format your response as a JSON object with \
  "Sentiment", "Anger", "Item" and "Brand" as the keys.
  If the information isn't present, use "unknown" \
  as the value.
  Make your response as short as possible.
  Format the Anger value as a boolean.
  
  Review text: '''{lamp_review}'''
  """
  ```
- Translation
  ```
  prompt = f"""
  Translate the following English text to Spanish: \ 
  '''Hi, I would like to order a blender'''
  """

  prompt = f"""
  Tell me which language this is: 
  '''Combien coûte le lampadaire?'''
  """

  prompt = f"""
  Translate the following  text to French and Spanish
  and English pirate: \
  '''I want to order a basketball'''
  """

  prompt = f"""
  Translate the following text to Spanish in both the \
  formal and informal forms: 
  'Would you like to order a pillow?'
  """
  ```
- Writing Style Transformation
  ```
  prompt = f"""
  Translate the following from slang to a business letter: 
  'Dude, This is Joe, check out this spec on this standing lamp.'
  """
  ```
- Writing Format Conversion
  ```
  prompt = f"""
  Translate the following python dictionary from JSON to an HTML \
  table with column headers and title: {data_json}
  """
  ```
- Spell/Grammer Check
  ```
    prompt = f"""Proofread and correct the following text
    and rewrite the corrected version. If you don't find
    and errors, just say "No errors found". Don't use 
    any punctuation around the text:
    '''{t}'''"""
  ```
- prompt in messages data of api parameters
  ```
  messages.append(
  {'role':'system', 'content':'create a json summary of the previous food order. Itemize the price for each item\
   The fields should be 1) pizza, include size 2) list of toppings 3) list of drinks, include size   4) list of sides include size        5)total price '},    
  )
  ```
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

### MoE
- [混合专家模型 (MoE) 详解](https://github.com/huggingface/blog/blob/main/zh/moe.md?utm_source=chatgpt.com)
  - 增加更多专家可以提升处理样本的效率和加速模型的运算速度，但这些优势随着专家数量的增加而递减 (尤其是当专家数量达到 256 或 512 之后更为明显)。
  - 这意味着在推理过程中，需要更多的显存来加载整个模型。
  - Switch Transformers 的研究表明，其在大规模模型中的特性在小规模模型下也同样适用，即便是每层仅包含 2、4 或 8 个专家。 
#### Large Scale of Expert (FFN)
- [GShard: Scaling Giant Models with Conditional Computation and Automatic Sharding](https://arxiv.org/pdf/2006.16668)
- [MoE模型的前世今生](https://news.miracleplus.com/share_link/24433)
  - GShard进行了大量的实验，训练了包含4/32/256个expert的flat MoE模型和包含256/1024/4096个expert的hierarchical MoE模型。 
- [Model Card for Switch Transformers C - 2048 experts (1.6T parameters for 3.1 TB)](https://huggingface.co/google/switch-c-2048)

### AI Agent
#### Reference
- [GPT-4o 新突破！AI 僅需兩小時訪談，輕鬆複製人類性格，準確率高達 85%](https://www.techbang.com/posts/120060-gpt-4o-replicates-human-personality?fbclid=IwY2xjawHNr2pleHRuA2FlbQIxMQABHX4uWXcoR5-M7J8WEzKtHcX0408vX2lHKnAV0W8SjLAxVNZSOiPV5d_xEQ_aem_63DQ3T9dMgtkd2p9YP_ZEg)
- [近期正夯的AI Agent（智慧型代理）到底是什麼？](https://www.ectimes.org.tw/2024/05/%E7%88%86%E7%B4%85%E7%9A%84ai-agent%EF%BC%88%E6%99%BA%E6%85%A7%E5%9E%8B%E4%BB%A3%E7%90%86%EF%BC%89%EF%BC%8C%E5%88%B0%E5%BA%95%E6%98%AF%E4%BB%80%E9%BA%BC%EF%BC%9F/)
- [AI Agent概念及其應用](https://medium.com/vincent-chen/ai-agent%E6%A6%82%E5%BF%B5%E5%8F%8A%E5%85%B6%E6%87%89%E7%94%A8-e66c88e9a015)
- [完整指南LLM 2024年代理](https://botpress.com/tw/blog/llm-agents)

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

### 人類行為
- [Using Large Language Models to Simulate Multiple Humans
and Replicate Human Subject Studies](https://arxiv.org/pdf/2208.10264)
  - 使用GPT-3來模擬人類的反應，通過改變姓名和其他細節，在某些人類實驗（如終極博弈）中進行測試。

### 角色人格
- [Proxona: Leveraging LLM-Driven Personas to Enhance Creators’ Understanding of Their Audience](https://arxiv.org/pdf/2408.10937)
  - 利用大型語言模型（LLMs）生成基於數據的受眾角色，幫助創作者深入了解其受眾，並支持制定以受眾為中心的內容策略。 

### 語言應用
#### 笑話理解
- [INNOVATIVE THINKING, INFINITE HUMOR: HUMOR RESEARCH OF LARGE LANGUAGE MODELS THROUGH STRUCTURED THOUGHT LEAPS](https://arxiv.org/pdf/2410.10370)
  - 基於創意跳躍思維CLoT提出一個**"創意結構化思維跳躍"（CLoST）框架**
  - 首先，需要一個獎勵模型來達成糾錯目的(因為目前沒有幽默的專家模型或可用規則來判定內容是否幽默)
  - 通過強化學習，模型學會打磨思維鏈的推理，並改進其使用的策略。
  - 模型學會識別並修正錯誤，最終生成最具幽默性和創意的答案。
- [Cracking the Code of Juxtaposition: Can AI Models Understand the Humorous Contradictions](https://arxiv.org/pdf/2405.19088)
  - 當幽默涉及許多笑話和幽默線索所依賴的非線性敘事時，LLM在通過並列理解人類幽默的細微之處仍然面臨困難
  - 提出了**YESBUT基準**，包含不同難度的任務，旨在**評估AI識別和解釋這些漫畫的能力**，任務範圍從字面內容理解到深層敘事推理。
    
#### 拒絕能力
- [“As an AI language model, I cannot”: Investigating LLM Denials of User Requests](https://dl.acm.org/doi/pdf/10.1145/3613904.3642135)
- 討論有關互動中斷、修復策略的研究，以及中斷性溝通如何影響用戶對智能系統的期望和理解（例如，警告和錯誤信息）。
  - 歸納2類拒絕發生原因(將這些錯誤稱為“拒絕”)
    - technical技術性原因（即系統無法完成請求）
      - 訓練數據的空白、缺乏實時數據訪問、“數據不可用”、“不在訓練數據中”和“僅限文本輸出”（例如，無法訪問或訪問被禁止）, 訓練數據無法超過截止日期
      - 包含：基準拒絕, 轉移注意力的拒絕, 事實拒絕
      - 來自研究 1（技術性拒絕）的結果表明，與基準拒絕相比，參與者發現轉移注意力的拒絕在沮喪程度上較低，更有用、更適當且更相關。
      - 事實性拒絕在所有衡量標準上（除了沮喪）都比轉移注意力的拒絕評分低
      - 在技術性拒絕的情況下，帶有意見的回應並不合適，因為其根本原因並不需要解釋（例如，對於數據完全無法訪問，無需有關數據的任何見解）。
    - social社會性原因（即系統被禁止完成請求）
      - 社會性拒絕的原因通常是由於政策，例如不允許 LLM 生成仇恨或色情內容
      - 包含：基準拒絕, 轉移注意力的拒絕, 帶有意見的拒絕
      - 在研究 2（社會性拒絕）中，我們發現，轉移注意力的拒絕比基準拒絕和帶有意見的拒絕在沮喪程度上較低，並且更有用、更適當和更相關。
      - 帶有意見的拒絕在所有衡量標準上比基準拒絕更受參與者的正面評價。
      - 將事實性拒絕風格從社會性拒絕中排除，因為這裡的事實總是相同的：政策不允許提供回應。
  - 4種拒絕風格
    - 基準拒絕(baseline denials): 這種拒絕僅簡單地聲明 LLM 無法提供幫助
    - 事實拒絕(Factual denials): 該風格提供拒絕並附上拒絕的理由
    - 轉移注意力的拒絕(diverting denials): LLM 會引導用戶偏離請求
    - 帶有意見的拒絕(opinionated denials): 回應強調請求的不適當性，
  - 對於每種拒絕，我們評估了用戶對其的沮喪程度、有用性、適當性和相關性的感知，並收集了用戶的開放式反饋。我們通過不同的系統提示來展現每種拒絕風格，確保 LLM 生成適當的回應
  - 用戶對轉移注意力的拒絕給予較高評價，是因為即使原始請求無法滿足，這些拒絕仍能提供有價值的信息。
    - 例如通過提供不冒犯的笑話來代替請求中的冒犯性笑話。   
    - 禮貌的聊天機器人也可能被認為是“過於道歉且帶有居高臨下的語氣”
    - 例如，假設一個用戶要求 LLM 提供不當的笑話。與其提供像“請求不允許”這樣簡短且缺乏信息的回應，LLM 應該設計為這樣說：“我們的平台致力於促進積極和包容的體驗，而這個請求違反了我們的準則。這裡有一個你可能會喜歡的替代笑話[......]”
    - 用戶可能無法學會如何調整他們的請求以避免未來的中斷。
  - 建議 LLM 避免在未給出理由的情況下拒絕用戶請求。相反，它們應該提供有關拒絕原因的額外信息，並可能將拒絕情境化。
  - 提供無信息的回應（即我們的基準情境）是最不受喜愛的選項，因為這些拒絕可能未能向用戶解釋拒絕的根本原因。
  - 我們建議 LLM 配備糾正性拒絕，指導用戶改善他們的請求或調整行為。參與者對帶有意見的拒絕給予了比基準拒絕更高的評價。如前所述，帶有意見的拒絕作為一種糾正性信息起到了作用。
  
#### 雙關語
- [Ambipun: Generating humorous puns with ambiguous context.](https://arxiv.org/abs/2205.01825)
- [“A good pun is its own reword”: Can Large Language Models Understand Puns?](https://arxiv.org/pdf/2404.13599)
  - LLM其理解雙關語的能力尚未得到系統性研究，這限制了LLMs在創意寫作和幽默創作中的應用
  - 大多數LLMs能夠準確識別同形雙關和異形雙關中的雙關詞
  - 除GPT-4-Turbo和Claude-3-Opus外，其餘LLMs在識別異形雙關中的替代詞 和替代意義時存在困難。這一挑戰源於在異形雙關中，並未直接出現在文本中，而是依賴於通過上下文和相似性進行的喚起，這是解釋雙關語的基礎
  - 異形雙關的識別好(替代詞不影響雙關語的識別，但對正確解釋雙關語至關重要。)，但不一定能做出好的解釋
  - 儘管我們使用了目前最廣泛使用的雙關語數據集來評估LLMs的雙關語理解能力，但我們的雙關文本全部為英文。LLMs理解雙關語的能力可能因語言而異，非英語語言的雙關語在定義、結構或用途上可能有所不同。這一限制突顯了未來將研究擴展到其他語言雙關語的潛力。
  - 揭示了「**懶惰的雙關生成**」模式，並確定了LLMs在理解雙關語方面的主要挑戰
    
#### 中文俚語
- [DuanzAI: Slang-Enhanced LLM with Prompt for Humor Understanding](https://arxiv.org/pdf/2405.15818)
  - 提出了DuanzAI，一種增強大型語言模型（LLMs）深度理解中文俚語的創新方法。
  - 利用三個常見任務，即**雙關識別、雙關解釋和雙關生成**，系統**評估LLMs理解雙關語的能力**。
  - 
#### joke
- [Crowd Score: A Method for the Evaluation of Jokes using Large Language Model AI Voters as Judges](https://arxiv.org/pdf/2212.11214)
  - 以**不同個性風格LLM來評測笑話品質**
  - 認為個性的設定需要"足以誘導出具備這些特質的個性" (例：「作為一個喜歡$TypeOfHumour幽默的人，將以下[Joke]分類為Funny或$Opposite。」)
  - 
##### Joke定義
- [Individual differences in uses of humor and their relation to psychological well-being: Development of the Humor Styles Questionnaire](https://www.sciencedirect.com/science/article/abs/pii/S0092656602005342)
  - **定義笑話分類**：親和型、自我提升型、攻擊型和自我貶低型
  - 親和型幽默：一種非敵對、包容的幽默，肯定自我與他人
  - 自我提升型幽默：旨在讓人對自己感到良好
  - 攻擊型幽默：使用諷刺、揶揄、嘲笑、譏諷和貶低他人
  - 自我貶低型幽默：是為了讓他人開心而自嘲
  - 
##### Joke Dataset
- [Witscript: A System for Generating Improvised Jokes in a Conversation](https://computationalcreativity.net/iccc21/wp-content/uploads/2021/09/ICCC_2021_paper_15.pdf)
  - **笑話由人(專業笑話演員)與LLM(GPT-3, Witscript, Witscript2)生成**，皆屬於攻擊性/自我貶低型
  - 笑話並非僅依賴文字遊戲，基於常識
  - 同時包含人類評審幽默評分
- [Chumor 1.0: A Truly Funny and Challenging Chinese Humor Understanding Dataset from Ruo Zhi Ba](https://arxiv.org/pdf/2406.12754)
  - **Chumor 資料集**： 來源於「弱智吧」（RZB），一個類似Reddit的中文平台，專注於分享具有智力挑戰性和文化特性的笑話
  - 為每個笑話添加了解釋標註，並通過中文母語者的A/B測試
  - 資料集下載：https://github.com/dnaihao/Chumor-dataset
- [Is AI fun? HumorDB: a curated dataset and benchmark to investigate graphical humor](https://arxiv.org/pdf/2406.13564)
  - **HumorDB 資料集**： 新型純圖像數據集，旨在推動視覺幽默理解的進展
  - 包含精心挑選的圖像對，具有對比明顯的幽默評分
  - 突顯引發幽默的微妙視覺線索，並減少潛在的偏見。該數據集支持二元分類（有趣或無趣）、範圍回歸（幽默程度從1到10）和成對比較任務（哪個圖像更有趣）
- [Are U a Joke Master? Pun Generation via Multi-Stage Curriculum Learning towards a Humor LLM](https://aclanthology.org/2024.findings-acl.51.pdf)
  - 專注於賦予LLMs生成雙關語的能力，這是一種通過偏好學習方法實現的特定幽默類型。
  - 提出了一種多階段課程偏好學習框架，以優化雙關語結構偏好和幽默偏好，改進了直接偏好優化（DPO）算法，以解決多目標對齊問題。
  - 突顯引發幽默的微妙視覺線索，並減少潛在的偏見。該數據集支持二元分類（有趣或無趣）、範圍回歸（幽默程度從1到10）和成對比較任務（哪個圖像更有趣
  - **中文雙關語（ChinesePun）數據集**，包含2.1k雙關語及其對應標註。
  - 
#### 笑話生成
- [Getting Serious about Humor: Crafting Humor Datasets with Unfunny Large Language Models](https://arxiv.org/pdf/2403.00794)
  - 探討大型語言模型（LLMs）是否能夠通過編輯文本生成幽默檢測的合成數據
  - 顯示當前的LLM在「去幽默化」笑話方面表現出令人印象深刻的能力
  - GPT-4生成的合成數據得到了雙語標註者的高度評價，並為幽默分類器提供了具挑戰性的對抗示例。
- [Exploring Chinese Humor Generation: A Study on Two-part Allegorical Sayings](https://arxiv.org/pdf/2403.10781)
  - 本文研究了最先進的語言模型在理解和生成中文幽默方面的能力，特別聚焦於訓練它們創作諷喻語句
  - 用了兩種主要的訓練方法：對**中型語言模型進行微調**和對**大型模型進行提示**
  - 創新微調方法結合了融合拼音嵌入以考慮同音詞，並**使用對比學習與合成的困難負例來區分幽默元素**

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
#### DeepSeek-R1
- Pure RL Process: 發現不使用SFT做冷啟動，以RL做冷啟動的效果也能很好
- 群體相對策略優化（Group Relative Policy Optimization, GRPO)
  - 放棄了傳統 RL 中與策略模型（Policy Model）等尺寸的評論模型（Critic Model）
  - 改採基於群體得分（Group Scores）來估計基線值，從而大幅減少計算資源需求。
  - 透過KL 散度懲罰（KL Divergence Penalty）來調控新策略與參考策略之間的差異，使學習過程更加穩定
  - 使 RL 訓練能夠在無需評論模型的情況下，透過群體樣本的統計特性來實現高效的策略優化，提高推理能力的學習效率
  - 用了基於規則的獎勵系統（Rule-Based Reward System）
    - 準確性獎勵(E.G.數學有固定/明確答案)
    - 格式獎勵
- RL with Cold-Start
  - 少量高品質的長思維鏈（Long Chain-of-Thought, CoT）數據進行冷啟動
    - 數千條 
  - 4階段 
    - 冷啟動階段（Cold Start Phase）：引入少量高品質數據以提供初始指導。
    - 第一階段增強學習（First RL Stage）：探索更優化的推理模式，提升初步推理能力。
    - 監督微調階段（Supervised Fine-Tuning, SFT）：利用經過篩選的數據進一步對齊人類偏好，增強可讀性與通用性。
    - 第二階段增強學習（Second RL Stage）：在更強大的基礎上進一步優化推理性能，使模型具備更高級的推理能力與適應性。    

##### Reference
- [DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning](https://arxiv.org/pdf/2501.12948)
- [DeepSeek-V3 Technical Report](https://arxiv.org/pdf/2412.19437)
- [【LLM 專欄】Deepseek v3 的訓練時間到底合不合理？淺談 LLM Training efficiency](https://axk51013.medium.com/deepseek-v3-%E7%9A%84%E8%A8%93%E7%B7%B4%E6%99%82%E9%96%93%E5%88%B0%E5%BA%95%E5%90%88%E4%B8%8D%E5%90%88%E7%90%86-%E6%B7%BA%E8%AB%87-llm-training-6460374b6d0f)
- [不，你無法用 600 萬美元複製一個 DeepSeek R1](https://technews.tw/2025/01/28/you-cannot-copy-deepseekr1-with-6m)
  - DeepSeek R1
    - R1 模型的訓練費用其實和去年底發佈的 V3 模型相同
  - DeepSeek V3
    - V3 模型中的多數功能又和 2024 年初發佈的 V2 模型共用
    - 負載平衡
    - 多重 token 預測機制
  - DeepSeek V2
    - DeepSeekMoE: 多重專家混合（Mixture of Experts）
    - DeepSeekMLA: 多頭潛在注意力機制（Multi-Head Latent Attention）
- [Timeline of DeepSeek Papers on arXiv](https://medium.com/@fangkuoyu/timeline-of-deepseek-papers-on-arxiv-44ba1935f714https://medium.com/@fangkuoyu/timeline-of-deepseek-papers-on-arxiv-44ba1935f714)

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
#### LLaMA
- LongLLaMA
  - [将上下文长度扩展到 256k，无限上下文版本的OpenLLaMA来了？](https://www.jiqizhixin.com/articles/2023-07-10-3)
  - 基於OpenLLaMA實作
  - 可商用
  - 採用FOT （ Focused Transformer )微調
#### LLaMA2
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
#### MMLU
- 57個學科領域
- [要如何評估大型語言模型的預測能力？開箱大型語言模型評估程式碼](https://blog.infuseai.io/llm-model-evaluation-open-source-code-mmlu-tmmluplus-mistral-c84828178114)
- [MMLU 是什麼](https://ai.choozmo.com/blog/what-is-mmlu-dataset/)
#### TMMLU+
- 66個學科領域
- [TMMLU+ Dataset: 大規模繁體中文海量多任務語言理解](https://blog.infuseai.io/tmmluplus-dataset-brief-introduction-ecfd00297838)
- [TMMLU+: An Improved Traditional Chinese Evaluation Suite
for Foundation Models](https://arxiv.org/pdf/2403.01858v3)


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
