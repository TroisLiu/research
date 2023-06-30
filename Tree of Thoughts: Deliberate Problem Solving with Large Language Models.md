# Overview
## Tree of Thoughts
- 結合"基於語言的生成和評估多樣思維的能力" & "搜索算法（如廣度優先搜索（BFS）或深度優先搜索（DFS））"
- 允許對思維樹進行系統性的展望和回溯探索 
# Introduction
- ![image](https://github.com/TroisLiu/research/assets/5452974/b2705257-ffab-4cb9-bbfc-068f2e6f69f2)
- 現有使用語言模型解決一般問題的方法存在的兩個關鍵不足
  - 局部上，它們不探索思維過程中不同的延伸 - 樹的分支。
  - 全局上，它們不包含任何類型的計劃、向前看或回溯來幫助評估這些不同的選項 - 類似於人類問題解決的啟發式引導搜索。

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
