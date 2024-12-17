# Research
paper &amp; learning tutorial reading list
## Quick Link
- [Topic - Knowledge](https://github.com/TroisLiu/research/tree/master#knowledge)
- [Topic - LLM](https://github.com/TroisLiu/research/tree/master#llm)
- [Topic - 推薦系統](https://github.com/TroisLiu/research/tree/master#recommendation-system)

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
