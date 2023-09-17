# 20230723_watermark_first

## A Watermark for Large Language Models

### 检测方法

- a **statistical** test for detecting the watermark with **interpretable p-values**
  - 可解释p值

## Baselines for Identifying Watermarked Large Language Models

### 检测方法

- distributions of output tokens and logits 

### 主要的水印方法

- secret key, hashing, and pseudorandom function properties
  - 密钥、hashing、随机函数属性

### LLM水印与水印检测框架

#### 大语言模型

- 词汇表
  - 有encoder、decoder、T

- 大序列模型
  - 一个从**有限的 token 序列** T*（由文本中的单词或字符组成）到**所有 token 的 logit 集合** L ∈ R|T| 的映、
  - Logits 是指模型在输出层得到的未经过概率归一化的分数
    - 这个映射表示了模型对给定输入序列的预测结果，每个 token 都对应一个对应的 logit 值。
  - 公开托管的LLM中，**logits的分布非常不均匀**

- LLM
  - 大语言模型在生成文本时，其输出的 token 分布要比随机选择更具有可预测性

#### LLM中的水印

- 水印
  - 密钥s是用于生成特定的水印映射的关键信息
  - 水印是一种将**一个大语言模型的输出映射到另一个大语言模型输出**的方法

- 原则性水印(Principled Watermark)
  - 对于具有相同 token 排列的大语言模型，应用相同的水印映射会得到相同的结果

- 水印的可检测性(Detectability)
  - 在文本序列中可以通过算法进行检测
- 与标识模型的区别
  - 本文研究目的是检测出**哪些文本是被水印处理的**

- 强质量保持水印(Strong Quality-Preserving Watermark)
  - 对于水印 Ws（其中 s 的长度为 m）和一个大语言模型 L
    - 假设存在两个多项式函数 p 和 q，使得 n ≤ p(m)，且 q(m) ≥ 2
    - 考虑一个时间复杂度为 p(m) 的对手 A，它根据输入的文本对其进行分类，判断它是否为水印或正常的文本
    - 如果对于所有的 L、m、n、A、p 和 q，当给定由 L 和 Ws(L) 生成的长度为 n 的文本时，对手 A 在随机选择的 LLM 生成和 s 的情况下，其判断正确的概率最多为 1/2 + 1/q(m)，那么称水印 Ws 是强质量保持的
    - 这个定义要求在所有的情况下，**对手 A 在判断水印和正常文本时的准确率不能太高**，至多为 1/2 + 1/q(m)
- 质量保持与检测性的关系
  - 本文讨论的现有水印虽然在实际应用中具有相对的保质性，但都不满足这一保质标准
- 确定性 LLM 和强质量保持的冲突
  - 要实现强质量保持，就必须几乎不修改正常输出的文本，这样的水印则是难以被检测的
  - 但对于非确定性的语言模型，这种情况并不适用，因为它们的输出可能会有一定的随机性

- 可测量水印（Measurable Watermark）
  - 可以用一个区分器来有效地区分水印和非水印文本
    - 在多项式时间复杂度内

- 可检测性与不可检测性的冲突
  - 设计一个既容易检测的水印又能保持不可测性的水印是具有挑战性的

- 当一个**作用于固定提示的语言模型的自然分布是已知的**，它就不能添加不被检测到的水印
  - 自然分布：一个语言模型在给定特定输入（例如，固定的提示或上下文）时生成文本的概率分布

### 模型输出与概率分布

- 水印会影响原始LLM的 logits 分布

#### 随机位生成

- 每个模型的定性输出概率分布截然不同

#### RPLC——评估模型输出的概率分布是否偏离理想的均匀分布

- 每个token的输出概率，构建排名概率Lorenz曲线

### Baseline

#### Measuring Divergence of RNG Distributions——RNG分布散度

- 对随机数分布进行检验
- 使用双样本Kolmogorov-Smirnov检验

#### Mean Adjacent Token Differences——平均相邻token差异

- 计算相邻标记之间对数几率值的平均增加
  - 水印的强度_δ
  - 水印在模型中存在的比例_γ
- 适用于Kirchenbauer水印

#### Small-δ

- 对LLM输入重复给定的提示
- 收集每次提示下生成的logits
- 利用平均的logit值的频率来进行双峰性检测
  - 如果频率分布呈现双峰性，即存在带状间隔，那么就认为该LLM模型带有水印

