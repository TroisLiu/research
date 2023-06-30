# Overview
## Tree of Thoughts
- 結合"基於語言的生成和評估多樣思維的能力" & "搜索算法（如廣度優先搜索（BFS）或深度優先搜索（DFS））"
- 允許對思維樹進行系統性的展望和回溯探索 
# Introduction
- ![image](https://github.com/TroisLiu/research/assets/5452974/b2705257-ffab-4cb9-bbfc-068f2e6f69f2)
- 現有使用語言模型解決一般問題的方法存在的兩個關鍵不足
  - 局部上，它們不探索思維過程中不同的延伸 - 樹的分支。
  - 全局上，它們不包含任何類型的計劃、向前看或回溯來幫助評估這些不同的選項 - 類似於人類問題解決的啟發式引導搜索。
- ToT的4大議題
  1. 如何將中間過程分解為思緒步驟
  2. 如何從每個狀態生成潛在的思緒
  3. 如何根據啟發式方法評估狀態
  4. 使用什麼搜索算法。

## Prompting LM
### Input-Output Prompting (IO Prompting)
- ![image](https://github.com/TroisLiu/research/assets/5452974/dbc6e620-d941-408c-a567-08527dd28830)


### Chain-of-Thought Prompting (CoT Prompting)
- ![image](https://github.com/TroisLiu/research/assets/5452974/ad5bb097-ecaa-426d-b8c6-ea7305647d1f)
- ![image](https://github.com/TroisLiu/research/assets/5452974/e62122f0-b5dd-4f42-a19d-a97118195800)


### Self Consistency with CoT Prompting (CoT-SC Prompting)
- ![image](https://github.com/TroisLiu/research/assets/5452974/34665394-e0f2-4e9d-9cea-e5af4ffd50e5)
- ![image](https://github.com/TroisLiu/research/assets/5452974/58d5d479-a8d2-4cf0-baa9-9a07da2a7b09)
- 抽樣k次產生答案, 挑選出現次數最多的答案作為最終答案
- 對於同一個問題通常存在不同的思維推理過程（例如，證明相同定理能以不同方式達到），通過探索更豐富的思維集合，輸出決策可以更加可信賴。
- 缺點: “最頻繁”的啟發式方法僅適用於輸出空間有限的情況（例如，多選擇問答）

### Tree of Thought Prompting (ToT Prompting)
- 人們在組合性問題空間中進行搜索，這個空間可以看作是一棵樹
  - node: 視為部份解
  - branch: 對應於修改這些解(node)的運算符
- 該空間中branch的選擇由啟發式方法決定，這些方法有助於在問題空間中導航，引導問題解決者走向解決方案。
#### 4大議題
##### Thought Decomposition
- 根據不同的問題，一個思緒可以是幾個詞（填字遊戲），一行方程式（24點遊戲）或一個完整的寫作計劃段落（創意寫作）
- thought 思緒
  - 足夠「小」: 以便語言模型可以生成有前途且多樣化的樣本（例如生成整本書通常太「大」而不連貫）
  - 足夠「大」: 以便語言模型可以評估它對解決問題的前景（例如生成一個標記通常太「小」無法評估）。

##### Thought Generator
- 考慮兩種策略來生成下一個思緒步驟的 k 個候選
  - Sample: 從 CoT 提示中獨立抽樣思緒
    - 當思緒空間豐富時（例如每個思緒是一段落），獨立抽樣可以帶來多樣性
  - Propose: 使用「提議提示」逐個提議思緒
    - 當思緒空間較受限制時（例如每個思緒只是一個詞或一行文字），在相同上下文中提議不同的思緒可避免重複。 
