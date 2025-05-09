# Overview
- M$的論文

# 摘要
- 無監督模型的預訓練步驟面臨著難以承受的整體計算開銷。目前加速預訓練的方法要麼依賴於先進的硬件進行大規模並行計算，要麼不適用於語言建模。
- 提出一種基於**漸進式層丟棄( progressive layer dropping)**的方法，加速Transformer-based語言模型的訓練
    - 非以過度消耗硬件資源為代價
    - 改變模型架構和訓練技巧的提高效率
- 實驗於BERT
    - 平均每個樣本的時間減少了24％
    - 相較基準模型，預訓練速度提高了2.5倍，
    - 在下游任務上達到了類似的準確度
    - 使用相同數量的樣本進行預訓練時，實現了與基準模型相當甚至更高的GLUE分數。
       - 具有強大的知識可轉移性

# Introduction 
- 想探討什麼導致了帶有"層丟棄"BERT預訓練的不穩定性？

# Approach

## Switchable-transformer block (ST Block)
- 與原始Transformer block相比，有兩大改變
### Identity mapping reordering (使用PreLN取代PostLN)
- 將原先放置在每一個sub-layer的layer Normalization移除
- 只在Sub-layer的input stream放置Layer Normalization
### Switchable Gates
- 在每一個sub-layer增加一個gate (用來控制該sub-layer在模型訓練時是否有被disable)
- conditional gate function G
    - 值：0/1，隨機由Bernoulli distribution決定
        - G = 1: ST Block中的2個inner function都會被啟動
        - G = 0: ST Block會被skipped
    - 每一個Gate i 有p的機率值為1，也就是訓練時有機率p選中該Gate，但推論時都會出現

## progressive layer dropping (PLD)
### PLD Schedule
- 提出progressive schedule theta(t)
- 用於預期保留的st blocks數量
- t=0時, theta(0)=1，代表沒有發生layer drop (所有layer 保留)
- t趨近無窮大，則為theta', theta'屬於包含0但不包含1 (論文選0.5~0.9之間)
    - 只保留theat'比例的ST blocks ，加速訓練效率
- 當ST Block被skiped
    - 無須執行forward-backward計算或更新Gradient
    - 更新的網路更短，
### 介紹
- 基於ST Block&PLD Schedule設計一個stochastic training algorithm，能使BERT這類模型訓練更快
- 預期的網路深度L是個隨機變數
- 以BERT來說，若theta取0.5，公式換算後，平均訓練9層
    - 預期省下25% FLOPs  
