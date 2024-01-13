---
title: 机器学习
description: 
published: true
date: 2024-01-13T20:32:50.294Z
tags: 
editor: markdown
dateCreated: 2024-01-13T20:15:25.431Z
---

# Notes on Machine Learning

>   冬天来了 —— 那是悲喜交加、永远的瞬间。

## Basic Info

-   名词解释：4个 $\times$ 5分
-   简答题：6个 $\times$ 10分
-   综合题：1个 $\times$ 20分

重点大约是：

-   SVM
-   Bayes 和概统朋友
-   决策树

感觉这份笔记被数媒的机器学习课件给狠狠污染了……

## 绪论

经典定义：利用*经验*改善系统自身的性能。

所谓经验，通常表现为计算机中的数据。

DMP，即D（Training Data）+ M（Model）+ P（Prediction）。

ML 的主要任务，就是从数据中学习出一个模型，然后利用这个模型对未知数据进行预测。

假设我们有$\mathbf{x}$和$y$两个变量，其中$y$是我们想要预测的变量（或者说标签 label），$\mathbf{x}$是用来预测$y$的变量。那么，我们可以将$\mathbf{x}$和$y$的关系表示为$y = f(\mathbf{x})$，其中$f$是一个函数。我们的目标就是找到这个函数$f$。

ML 可以分为：

-   监督学习（Supervised Learning）：训练数据包含了标签 label，即我们知道$\mathbf{x}$和$y$的关系，我们的目标就是找到这个关系$f$。
    -   分类（Classification）：$y$是离散的。
    -   回归（Regression）：$y$是连续的。
-   无监督学习（Unsupervised Learning）：训练数据不包含标签 label。
    -   聚类（Clustering）：将数据分成若干类。
    -   异常检测（Anomaly Detection）：检测数据中的异常。

好像也没啥好说的，绪论能有啥内容……

## 模型选择与评估

模型选择（Model Selection）是：

-   从一组候选模型中选择统计模型的任务（比如选择 SVM、Logistic 回归等等）
-   或者为同一机器学习模型选择不同的超参数（比如选择 k-means 中的 $k$）

### 经验误差和过拟合

误差（Error）：样本的实际输出与预测输出之间的差异。

经验误差（Training / Empirical Error）：模型在训练集上的误差。

泛化误差（Generalization Error）：模型在未见过的样本上的误差。

我们的目标是：一个泛化误差最小的模型。

经验误差并非越小越好，因为会过拟合（overfitting）。

过拟合（Overfitting）：模型在训练集上表现很好，但是在测试集上表现很差。其本质是，模型描述了误差或噪声，而不是潜在的关系。

当模型过于复杂时，例如相对于观测值数量而言参数过多，就会发生过拟合。

解决方法：正则化；早点停下来。

>   正则化（Regularization）：在训练过程中向损失函数添加惩罚项。这种惩罚会阻止模型变得过于复杂或具有大的参数值，有助于控制模型对训练数据中噪声的拟合能力。
>
>   这能够防止过拟合，并提高模型泛化性能。
>
>   正则化方法包括L1和L2正则化、dropout、数据增强、提前停止等。通过应用正则化，模型变得更加健壮，并能更准确地预测未见过的数据。
>
>   L1和L2正则化是最常见的正则化类型。它们通过添加另一个 term（即正则化项）来更新一般的成本函数。L2正则化通过迫使权重向零衰减来降低权重矩阵的值，从而减少过拟合。L1正则化通过对权重的绝对值进行惩罚，可以将权重减少到零，适用于模型压缩。
>
>   Dropout是一种有趣的正则化技术，通过在每次迭代中随机选择一些节点并删除它们及其所有的输入和输出连接来引入更多的随机性。

>   早点停下来（Early Stopping）：主要思想是在模型开始过拟合之前停止训练，从而防止模型在训练数据上表现良好但在新数据上表现不佳。
>
>   三种主要方法：
>   1. 在预设的训练轮数后停止训练。
>   2. 当损失函数更新变得很小时停止训练。
>   3. 当验证损失函数开始增加时停止训练。

相对的，有欠拟合（Underfitting）的概念，即：当统计模型或机器学习算法无法捕获数据的潜在趋势时，就会发生欠拟合。

例如，当将线性模型拟合到非线性数据时，就会发生欠拟合。（简单来说就是直线怎么拟合得了曲线那么复杂的情况）这样的模型预测性能较差。

解决方法：增加模型复杂度；增加特征数量等。
-   对于决策树：扩展分支。
-   对于神经网络：更多次的训练。

### 评估方法

在实践中，我们会考虑模型的泛化能力、耗时、存储消耗、可解释性，最后做出决定。

By the way，我们将测试误差（Test Error）定义为模型在测试集上的误差，将测试误差视作泛化误差的近似。因为测试集（Test Set）是模型未见过的数据，和训练集（Training Set）互斥，所以测试误差可以近似地表示模型在未见过的数据上的误差。

#### 获取测试集

-   留出法（hold-out）
    -   比如数据集，取80%训练，20%测试
    -   要求：
        -   保证数据分布一致性（例如，分层采样）
        -   多次重复划分（例如100次随机划分）
        -   测试集不宜过大、过小（一般可能说 1/5 到 1/3 为宜）
-   交叉验证法（cross validation）
    -   or rather, k-fold cross validation（k-折交叉验证法）
    -   将数据集分为 $k$ 个子集，做 $k$ 轮，每轮取一个子集作为测试集，$k-1$ 个训练集
    -   为了避免切分子集的影响，随机切分 $m$ 种
    -   留一法（leave-one-out, LOO）：为了逼近 $M_{k}$，就切分 $k$ 个子集，取 1 个做测试集
        -   LOO 不受分区方法的影响，也更加准确。但是当数据集较大时，计算量会很大。
-   自助法（bootstrap）
    -   基于“自助采样”（bootstrap sampling），又称“有放回采样”“可重复采样”
    -   $\lim\limits_{m\rightarrow\infty}(1-\frac{1}{m})^{m}=\frac{1}{e}$，约为 0.368。即大约 36.8% 的样本不会出现。（包外估计，out-of-bag estimation）
    -   缺陷在于，这种方式实际上改变了训练数据的分布

#### 性能度量

性能度量（performance measure）是衡量模型**泛化能力**的评价标准，反映了任务需求。

对于回归（regression）任务，常用均方误差（mean-square error, MSE）：
$$
E(f;D)=\frac{1}{m}\sum\limits_{i=1}^{m}(f(x_{i})-y_{i})^{2}
$$

其中，$f$ 是模型，$D$ 是数据集，$m$ 是样本数量，$x_{i}$ 是第 $i$ 个样本，$y_{i}$ 是第 $i$ 个样本的标签。

对于分类任务（classification），常用错误率（error rate）和精度（accuracy）。

错误率：
$$
E(f;D)=\frac{1}{m}\sum\limits_{i=1}^{m}I(f(x_{i})\ne y_{i})
$$

其中，$I$ 是指示函数（indicator function），当 $f(x_{i})\ne y_{i}$ 时，$I$ 的值为 1，否则为 0。

精度：
$$
\begin{aligned}
acc(f;D)&=\frac{1}{m}\sum\limits_{i=1}^{m}I(f(x_{i})=y_{i})\\
&=1-E(f;D)
\end{aligned}
$$

在信息检索和网络搜索领域，我们经常需要 Precision（查准率） 和 Recall（查全率）。

查准率（Precision）：预测为正的样本中，真正为正的样本的比例。

查全率（Recall）：真正为正的样本中，预测为正的样本的比例。

分类结果混淆矩阵：

![Classification_Result_Confusion_Matrix](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Classification_Result_Confusion_Matrix.png)

那么查准率：

$$
P=\frac{TP}{TP+FP}
$$

查全率：

$$
R=\frac{TP}{TP+FN}
$$

当$P=R$时，称这样的点为平衡点。

------

以下内容**不存在**于幻灯片中，仅供参考、了解。

F1 度量：
$$
\begin{aligned}
F1&=\frac{2PR}{P+R}\\
&=\frac{2TP}{\text{样本总数}+TP-TN}
\end{aligned}
$$

或者说：

$$
\frac{1}{F1}=\frac{1}{2}(\frac{1}{P}+\frac{1}{R})
$$

F1 实际上是 P 和 R 的调和平均。

若对 P / R 有不同偏好：
$$
F_{\beta}=\frac{(1+\beta^{2}PR)}{\beta^{2}P+R}\\
$$

或者说：

$$
\frac{1}{F_{\beta}}=\frac{1}{1+\beta^2}(\frac{1}{P}+\frac{\beta^{2}}{R})
$$

$\beta>1$时查全率影响更大，$\beta<1$时查准率影响更大。

#### 开销敏感的错误率

二分类代价矩阵如下：

![Binary_Classification_Cost_Matrix](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Binary_Classification_Cost_Matrix.png)

Cost-Sensitive Error Rate（成本敏感错误率）是一种用于衡量机器学习模型在不同类别上的错误率的指标。比如对于上表，我们在预测正确的情况下显然没有代价，而对于两种预测错误的情况赋予了两个代价权重。

有公式：

$$
E(f;D;\text{cost})=\frac{1}{m}(\sum\limits_{x_{i}\in D^{+}}I(f(x_{i})\ne y_{i})\times \text{cost}_{01}+\sum\limits_{x_{i}\in D^{-}}I(f(x_{i})\ne y_{i})\times \text{cost}_{10})
$$

在不平衡分类问题中，对于不同类别的错误分类，我们可能会赋予不同的成本。这个公式中的$\text{cost}_{01}$和$\text{cost}_{10}$就代表了模型在不同类别上的错误分类所带来的成本。

其中，$E(f;D;\text{cost})$表示模型$f$在数据集$D$上，根据成本$\text{cost}$计算出的错误率。$m$表示数据集D中样本的数量。

$\sum\limits_{x_{i}\in D^{+}}I(f(x_{i})\ne y_{i})\times \text{cost}_{01}$表示对于数据集$D$中属于正类别的样本，如果模型$f$对样本$x_{i}$的预测结果与真实标签$y_{i}$不一致，就将这个错误分类的成本$\text{cost}_{01}$计入总成本中。

$\sum\limits_{x_{i}\in D^{-}}I(f(x_{i})\ne y_{i})\times \text{cost}_{10}$表示对于数据集$D$中属于负类别的样本，如果模型$f$对样本$x_{i}$的预测结果与真实标签$y_{i}$不一致，就将这个错误分类的成本$\text{cost}_{10}$计入总成本中。

这样，有：

$$
\begin{aligned}
P(+)_{\text{cost}}&=\frac{p\times \text{cost}_{01}}{p\times \text{cost}_{01}+(1-p)\times \text{cost}_{10}}\\
\text{cost}_{\text{{norm}}}&=\text{FNR}\times p\times \text{cost}_{01}+\text{FPR}\times (1-p)\times \text{cost}_{10}
\end{aligned}
$$