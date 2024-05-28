### 【零】八股文说明

***

> 收集一些常见的 LLM 八股文知识点，比较零碎，尽量归纳总结一下

***





### 【一】Transformer 基础

***

> Transformer 作为划时代的 LLM 架构，必须掌握其中的每个模块



#### 【1.1】模型架构

***

> CSDN：[Transformer 个人博客](https://blog.csdn.net/qq_34330456/article/details/102964628)，需要烂熟于心



#### 【1.2】推理加速

***

* **KV - Cahce**：从前往后，将前面已经计算过的 Key 和 Value 存下来，新进来的 Token 在计算 Attention 时直接拿来用，推理速度会快个 4 ~ 5 倍。
* **Page Attention**：有效利用显存空间，避免空间碎片化。
* **MQA**（Multi-Query Attention），**GQA**（Group-Query Attention，慢慢成为了标准）。
* 

