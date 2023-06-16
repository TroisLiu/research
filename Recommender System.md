# 推薦系統
- 推薦符合用戶口味的項目
- 不推薦不符合用戶口味的項目
## 挑戰
- 稀疏性 sparsity
    - 推薦時沒有足夠的數據可用
- 冷啟動 cold start
    - 在服務開始時由於新用戶的湧入而缺乏評估數據，即第一個評估者
- 灰羊 gray sheep
    - 當與個別用戶的評估數據相似的用戶集合太小時，推薦困難的問題
## 評估指標
- 均方根誤差 RMSE
- 準確性 Accuracy
    - 評估推薦模型性能的指標
    - 表示成功推薦的項目數與所有推薦項目數的比例。
    - 準確性可以直觀地評估模型性能
    - 但當用戶偏好項目的信息數據不平衡時，準確性難以準確判斷模型性能
    - 0~1
    - 值越高，推薦模型的性能越好。
- 精確度 Precision
    - 計算模型推薦給用戶的項目中符合用戶口味的項目的比例
    - 0~1
    - 值越高，推薦模型的性能越好。
- 召回率 Recall
    - 計算實際用戶選擇的項目中被推薦給用戶的項目的比例
    - 0~1
    - 值越高，推薦模型的性能越好。
- F-Measure
    - 精確度和召回率是一種權衡。
    - 是精確度和召回率的調和平均值
    - 0~1
    - 值越高，推薦模型的性能越好。
- ROC Curve
    - 顯示偽陽性率（FPR）和真陽性率（TPR）之間關係的圖形
    - 視覺化解釋精確度和召回率的性能結果之比例
- AUC
    - AUC是ROC曲線下的面積，計算AUC的面積可以測量推薦模型的準確性
    - 為了彌補"ROC曲線是一個圖形，很難獲得定量值"這個問題
    - AUC值越接近1時，可以評估模型的性能優秀。通常情況下，如果AUC值為0.8或更高，則判定該模型具有高準確性
- Confusion Matrix
    - 將預測值和實際值可視化
    - 真陽性（True Positives，TP）
        - 表示當推薦系統推薦該項目時，與用戶的喜好匹配的項目的數量。
    - 真陰性（True Negatives，TN）
        - 表示用戶喜好的項目中未被推薦的項目的數量。
    - 偽陽性（False Positives，FP）
        - 表示系統推薦了用戶不喜好的項目的數量。
    - 偽陰性（False Negatives，FN）
        - 表示系統未對用戶不喜好的項目提供推薦的數量。
    - 真陽性率（True Positive Rate，TPR）
        - 所有陽性中實際陽性的比例
        - 召回率（Recall）的概念相同。
    - 偽陽性率（False Positive Rate，FPR）
        - 所有陰性中偽陽性的比例
    - ![](https://i.imgur.com/tJEfzWA.png)
    - [confusion_matrix: creating a confusion matrix for model evaluation](http://rasbt.github.io/mlxtend/user_guide/evaluate/confusion_matrix/)

![](https://i.imgur.com/qT3zI1j.png)

## 發展趨勢
- ![](https://i.imgur.com/q61mKDP.png)
- ![](https://i.imgur.com/FV3OJo2.png)
- ![](https://i.imgur.com/ki6ZFIg.png)
- ![](https://i.imgur.com/kRgYrIl.png)

## 應用
- Streaming Service
    - ![](https://i.imgur.com/UZLdcpK.png)
- SNS
    - ![](https://i.imgur.com/vvuV8se.png)
    - ![](https://i.imgur.com/GA9zJvJ.png)
- E-Education
    - ![](https://i.imgur.com/XGNaHGW.png)
 
## 技術
- ![](https://i.imgur.com/jfYaEou.png)
- Data Mining
    - ![](https://i.imgur.com/iGfnQrs.png)
- Text Mining
    - 從數據中提取與文本相關信息的技術，用於發現有用的文本信息
    - 被用於在基於內容的過濾推薦模型中通過對項目信息進行語義分析來推薦
    - 協同過濾推薦模型通過評估用戶之間的信息數據的語義知識，實現了基於相似性的項目推薦
    - 文本挖掘技術主要應用於醫療保健、教育、旅遊和學術服務等領域。 
    - ![](https://i.imgur.com/wh6yDuz.png)
    - 文字向量化
        - 詞袋模型 Bag of Word (BoW)
            - 基於單詞出現頻率來表示文字的方法
            - 不考慮單詞的排列順序、或甚至是文法結構。
            - 缺點：隨著句子變長，重複的詞其特徵值會越來越大，在遇到許多贅詞的時候會顯得不符使用
            - 改進：TF-IDF
    - FLM
        - 模糊語言建模（FLM）將模糊邏輯引入自然語言處理，通過使用模糊子集分析語言的含義 [64]。
        - 如果在推薦系統中使用FLM，可以識別項目的多語言上下文。特別是當用戶偏好不明確或用戶偏好數據不足時
        - 通過分析使用FLM的項目間的文本數據後，進一步獲取不足的偏好數據
    - TF-IDF
        - 基於內容的過濾推薦模型中主要使用的文本挖掘技術
        - 根據特定文本的重複出現次數給予權重。該技術將文檔的文本組成部分表示為向量，然後通過計算使用權重函數TF-IDF在特定文檔中的詞頻相對頻率來識別詞項的重要性 [66]。
        - 缺點：
            - 不考慮詞擺放
            - 詞之間獨立
            - 詞庫增加，維度增加，產生稀疏性

## 演算法
- content-based
    - 把與使用者喜歡的相似內容推薦給使用者
    - 優點
        - 無冷啟動：
            - user有基本數據可以判斷喜好就可以推薦
            - 隨著user瀏覽越多，精準越高




## reference
- [Recommendation Systems](https://paperswithcode.com/task/recommendation-systems)
- [BERT 在推荐系统领域可能会有什么作为？](https://www.zhihu.com/question/308362332)
- [A Survey of Recommendation Systems: Recommendation Models, Techniques, and Application Fields](https://www.mdpi.com/2079-9292/11/1/141)
    - 2022
- [新闻推荐系统：基于内容的推荐算法——ＴＦＩＤＦ、衰减机制（github java代码）](https://blog.csdn.net/u010332284/article/details/80421604)
- [Day 07：初探推薦系統(Recommendation System)](https://ithelp.ithome.com.tw/articles/10219033)
- [无处不在推荐系统(1) TFIDF](https://zhuanlan.zhihu.com/p/320273027)
- [NLP 系列-2：文字向量化 - tfidf](https://vocus.cc/article/63b2241ffd89780001dd5096)

## Deep Learning
### 
### DeepMatch
#### reference
- [deepmatch 0.3.1](https://pypi.org/project/deepmatch/)
- [Run models on MovieLen1M in Google colab](https://deepmatch.readthedocs.io/en/latest/Examples.html#youtubednn-mind-with-sampled-softmax)
- [YoutubeDNN/MIND with sampled softmax](https://deepmatch.readthedocs.io/en/latest/Examples.html#run-models-on-movielen1m-in-google-colab)
- [shenweichen/DeepMatch](https://github.com/shenweichen/deepmatch)
- [Youtube的DeepMatch](https://juejin.cn/post/7197305502304108603)

### DeepCTR
#### reference
- [shenweichen/DeepCTR](https://github.com/shenweichen/DeepCTR)
- [DeepCTR：易用可扩展的深度学习点击率预测算法包](https://zhuanlan.zhihu.com/p/53231955)

## 聯邦學習
### 聯邦協同過濾
#### reference
- [在推荐系统中，我还有隐私吗？联邦学习：你可以有](https://www.jiqizhixin.com/articles/2020-11-19-5)
