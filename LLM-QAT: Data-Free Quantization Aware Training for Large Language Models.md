# Overview
- [(2023)LM-QAT: Data-Free Quantization Aware Training for Large Language Models](https://arxiv.org/pdf/2305.17888.pdf)
- 一个拥有650亿参数的LLaMA模型仅仅是其权重就占用了65GB的GPU内存
- Key-value（KV）cache在attention layers中保存激活值，很容易占用数十GB的内存
  - 在当前应用中常见的长序列长度范围内成为吞吐量的瓶颈。  
-  data-free knowledge-distillation
  -  使用LLM自身生成的数据进行知识蒸馏来规避"指令调优、强化学习很難複製"之情況
-  適用性
  -  适用于任何生成模型，无论原始训练数据是否可用。
-  这方法能更好地保留原始模型的输出分布，甚至与在原始训练集的大型子集上进行训练相比也如此。
-  仅使用少量（10万个）的采样数据成功地进行蒸馏量化模型，从而保持了合理的计算成本。
-  算力：
  -  所有实验都是在单个8 GPU训练节点上进行的。
-  結果
  -  能蒸餾7B、13B和30B的LLaMA模型的权重和KV缓存量化到4-bits  
  -  用QAT的大型模型在性能上优于使用浮点16位表示的较小模型

# Tag
- post-training quantization method
- knowledge distillation
- data-free knowledge-distillation
