---
title: 机器学习
description: For 数媒。无论从哪方面来说都很抽象（
published: true
date: 2024-01-14T14:17:23.412Z
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
\text{cost}_{\text{norm}}&=\frac{\text{FNR}\times p\times \text{cost}_{01}+\text{FPR}\times (1-p)\times \text{cost}_{10}}{p\times \text{cost}_{01}+(1-p)\times \text{cost}_{10}}
\end{aligned}
$$

其中，$P(+)_{\text{cost}}$表示在成本敏感的情况下，预测为正的样本中，真正为正的样本的比例。$\text{cost}_{\text{norm}}$表示在成本敏感的情况下，模型的错误率。

$\text{FNR}$表示假反例率（False Negative Rate），$\text{FPR}$表示假正例率（False Positive Rate）。所谓的假反例率，就是在所有真正为正的样本中，被错误地预测为负的样本的比例。所谓的假正例率，就是在所有真正为负的样本中，被错误地预测为正的样本的比例；即$\text{FPR}=\frac{\text{FP}}{\text{FP}+\text{TN}}$；而假正例率，就是在所有真正为负的样本中，被错误地预测为正的样本的比例。

显然，$\text{FNR}=1-\text{TPR}$。这就是说，错误分成两组，一组是错误估计成了正例，一组是错误估计成了反例，因此加起来就是全部的错误。

### 比较检验

当我们回到性能比较的问题上来时，我们知道：

-   测试性能$\ne$泛化性能
-   测试性能会随着测试集的不同而不同
-   许多的 ML 算法都有一定的随机因子

这就是为什么，在上文中，我们只说“测试性能是泛化性能的近似”。

如果在测试集上观察到学习器A优于学习器B，我们如何保证A的泛化性能在统计意义上优于B?

#### 假设检验

假设检验（hypothesis testing）是统计学中的一个重要过程。假设检验评估关于总体的两个互斥的陈述，以确定样本数据最能支持哪个陈述。

所谓的假设（hypothesis），就是对 learner 泛化错误率分布的一些判断/猜测。

说人话就是：

首先我们取$\hat{\epsilon}$为学习器在测试集上的错误率,也就是说，如果有$m$个测试样本，那么就有$\hat{\epsilon}\cdot m$个样本被错误分类了。

然后，我们假定学习器的泛化错误率为$\epsilon$，那么这个学习器将$m^{\prime}$个样本错误分类，而将剩下的$m-m^{\prime}$个样本正确分类的概率就是：

$$
\epsilon^{m^{\prime}}(1-\epsilon)^{m-m^{\prime}}
$$

那么，在包含了$m$个测试样本的测试集上，泛化错误率为$\epsilon$的学习器被测得泛化错误率为$\hat{\epsilon}$的概率为：

$$
P(\hat{\epsilon};\epsilon)=\binom{m}{\hat{\epsilon}\cdot m}\epsilon^{\hat{\epsilon}\cdot m}(1-\epsilon)^{m-\hat{\epsilon}\cdot m}
$$

In case 你忘记了二项分布的写法，这里给出：

$$
\begin{aligned}
\binom{n}{k}&=\frac{n!}{k!(n-k)!}\\
&=C_{n}^{k}
\end{aligned}
$$

于是，使用二项检验（binomial test）的方法，假设：

$$
H_{0}:\epsilon\le\epsilon_{0}\\
H_{1}:\epsilon>\epsilon_{0}
$$

因此我们需要讨论单边检验的拒绝域形式。当$H_{1}$成立时，即$\epsilon>\epsilon_{0}$时，测试错误率往往偏大，拒绝域形式为$\hat{\epsilon}\ge k$，其中$k$是一个正整数。

$$
\begin{aligned}
P_{\epsilon\in H_{0}}(\hat{\epsilon}\ge k)&=\sum\limits_{i=\lceil mk\rceil}^{m}\binom{m}{i}\epsilon^{i}(1-\epsilon)^{m-i}\\
&\le\sum\limits_{i=\lceil mk\rceil}^{m}\binom{m}{i}\epsilon_{0}^{i}(1-\epsilon_{0})^{m-i}\\
&=\alpha
\end{aligned}
$$

这里，$\alpha$是显著性水平（significance level），通常取$0.01$或$0.05$活$0.1$。$1-\alpha$是置信度（confidence level）。通过这个公式，我们可以计算出$k$的值，即临界点。

进一步求出该检验的拒绝域，当测试错误率$\hat{\epsilon}\ge k$时，拒绝$H_{0}$，接受$H_{1}$。

#### t 检验

t 检验（t-test）是一种用于检验两组数据平均值差异是否显著的假设检验方法。它是一种单变量分析方法，用于比较两组平均值是否显著不同。t 检验的核心思想是，通过比较组间差异与组内差异来判断两组数据的差异是否显著。

很多时候我们会进行多次重复训练，得到多个错误率。假设有 $k$ 个错误率：$\hat{\epsilon}_{1},\hat{\epsilon}_{2},\cdots,\hat{\epsilon}_{k}$。

有平均测试错误率：

$$
\mu=\frac{1}{k}\sum\limits_{i=1}^{k}\hat{\epsilon}_{i}
$$

平均方差：

$$
\sigma^{2}=\frac{1}{k-1}\sum\limits_{i=1}^{k}(\hat{\epsilon}_{i}-\mu)^{2}
$$

写出t-分布的公式：

$$
\tau_{t}=\frac{\mu-\epsilon_{0}}{\sigma/\sqrt{k}}
$$

其中，$\epsilon_{0}$是假设的泛化错误率，该公式服从自由度为$k-1$的t-分布。

t-分布用于根据小样本来估计呈正态分布且方差未知的总体的均值。t-分布的形状和自由度有关。自由度越大，t-分布越接近正态分布。

**例子**

t-检验是针对分布期望 $\mu$ 的检验。假设一组服从正态分布的测试误差如下：

$$
\begin{matrix}
0.10 & 0.12 & 0.14 & 0.11 & 0.13 & 0.12 \\
\end{matrix}
$$

正态分布的期望 $\mu=0.11$。要判断这种说法的正确性，我们可以使用 t-检验。

首先，建立零假设 $H_{0}$ 和备择假设 $H_{1}$：

$$
\begin{aligned}
H_{0}&:\mu=0.11\\
H_{1}&:\mu\ne 0.11
\end{aligned}
$$

限定显著性水平 $\alpha=0.05$。

计算 T 统计量：

$$
\begin{aligned}
T=\frac{\bar{x}-0.11}{s/\sqrt{n}}\\
\approx 0.288675
\end{aligned}
$$

其中，$s$是样本标准差。样本中有 $n=6$ 个样本，所以自由度为 $n-1=5$。通过查表知道，$t_{0.025,5}=2.571$，$t_{0.975,5}=-2.571$。那么，接受假设 $H_{0}$，即认为 $\mu=0.11$ 是正确的。

#### 交叉验证 t 检验

1.  对一组样本 $D$，进行 $k$ 折交叉验证，得到 $k$ 个测试错误率；将两个 learner 都分别在每对数据子集上进行训练和测试，会分别产生两组测试错误率。
    $$
    \begin{aligned}
    \epsilon_{1}^{A},\epsilon_{1}^{A},\cdots,\epsilon_{1}^{A}\\
    \epsilon_{2}^{B},\epsilon_{2}^{B},\cdots,\epsilon_{2}^{B}
    \end{aligned}
    $$
2.  对每组结果求差值：$\nabla_{i}=\epsilon_{i}^{A}-\epsilon_{i}^{B}$
    如果两个 learner 的泛化性能相同，那么这些差值应该接近于 0。因此，可以用差值$\nabla_{1},\nabla_{2},\cdots,\nabla_{k}$来对假设“两个 learner 的泛化性能相同”进行 t-检验。
3.  假设检验：计算出差值样本的均值 $\mu$ 与方差 $\sigma^{2}$，在显著度 $\alpha$ 下，若变量 $\tau_{t}=\left| \frac{\sqrt{k}\mu}{\sigma}\right|$ 大于 t 分布的临界值 $t_{\alpha/2,k-1}$，则拒绝假设，即认为两个 learner 的泛化性能不同，并且选择平均错误率较小的 learner。

#### Bias 和 方差

![Bias_and_Variance_in_Machine_Learning](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Bias_and_Variance_in_Machine_Learning.png)

就这两个值可以描述模型的泛化性能。

然后：

![Relation_Between_Error_Bias_and_Variance](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Relation_Between_Error_Bias_and_Variance.png)

## Bayes 分类器

问就是 Bayes 条件概率理论。

$$
P(Y|X)=\frac{P(X|Y)P(Y)}{P(X)}
$$

不同形式的 Bayes Rules：

$$
\begin{aligned}
P(A\cap B)&=P(A|B)P(B)\\
P(A\cap B)&=P(B|A)P(A)\\
P(A|B)P(B)&=P(B|A)P(A)\\
P(A|B)&=\frac{P(B|A)P(A)}{P(B)}
\end{aligned}
$$

对于公式 $P(A|B)=P(A)\frac{P(B|A)}{P(B)}$，我们可以将其理解为：

-   $P(A|B)$：在事件 $B$ 发生的条件下，事件 $A$ 发生的概率；称之为“后验概率”。
-   $P(A)$：事件 $A$ 发生的概率；称之为“先验概率”。
-   $P(B|A)$：在事件 $A$ 发生的条件下，事件 $B$ 发生的概率；称之为“条件概率”。

### 基于最小错误率的 Bayes 决策

在 pattern classification 问题中，根据概率论中的 Bayes 公式最小化分类的误差，可以得到使错误率最小的分类规则，称为基于最小错误率的 Bayes 决策。

假设我们有一个简单的二元分类问题，我们希望根据某个样本的特征将其分为两个类别：A 和 B。我们使用一个特征 $x$ 来进行分类，而 $x$ 的取值范围是实数。

首先，我们需要计算在给定特征 $x$ 的条件下，样本属于类别 A 和 B 的后验概率。根据 Bayes 定理，后验概率可以表示为：

$$
P(A|x)=\frac{P(x|A)P(A)}{P(x)}\\
P(B|x)=\frac{P(x|B)P(B)}{P(x)} 
$$

其中，$P (x|A)$ 和 $P(x|B)$ 分别是在类别 A 和 B 下特征 $x$ 的条件概率，$P(A)$ 和 $P(B)$ 分别是类别 A 和 B 的先验概率，$P(x)$ 是特征 $x$ 的边缘概率。

接下来，我们需要选择一个决策规则，使得分类错误率达到最小。通常情况下，我们会选择后验概率最大的类别作为最终的分类结果。即，如果 $P(A|x)>P(B|x)$，则将样本分类为 A 类；如果 $P(A|x)<P(B|x)$，则将样本分类为 B 类。

具体到计算方式，那就是：

$$
P(A|x)=\frac{P(x|A)P(A)}{P(x)}=\frac{P(x|A)P(A)}{P(x|A)P(A)+P(x|B)P(B)}\\
$$

话说回来，前面可能还是有点抽象，可以这样说：

-   $P(A|x)$：在特征 $x$ 的条件下，样本属于类别 A 的概率
-   $P(x|A)$：在类别 A 的条件下，特征 $x$ 的概率
-   $P(x)$：特征 $x$ 的概率
-   也就是说，我想知道在特征 $x$ 的条件下，样本属于类别 A 的概率，那么根据 Bayes 公式，我需要知道在类别 A 的条件下，特征 $x$ 的概率，以及特征 $x$ 的概率。
-   而特征 $x$ 的概率，可以用全概率公式计算得到。即，$P(x)=P(x|A)P(A)+P(x|B)P(B)$。

如果是幻灯片上的例子，那就是：

![The_Bayesian_Classifier_in_the_Slides](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/The_Bayesian_Classifier_in_the_Slides.png)

显然，Bayes 模型是一种理论模型。现实中很难获得先验概率和类别条件概率，因此需要估计它们的值。

### 最大似然估计

最大似然估计（Maximum Likelihood Estimation, MLE）是一种参数估计方法，用于估计模型的参数。它的基本思想是：选择使得已知数据出现的概率最大的模型参数。

在公式 $P(c_{i}|x)=\frac{P(x|c_{i})P(c_{i})}{P(x)}=\frac{P(x|c_{i})P(c_{i})}{\sum\limits_{j=1}^{n}P(x|c_{j})P(c_{j})}$ 中，分子 $P(c_{i})P(x|c_{i})$ 就是要被估计的。

首先写出似然函数：

$$
\begin{aligned}
L(\theta)&=P(D|\theta)=\prod\limits_{i=1}^{m}P(x_{i}|c_{i};\theta)\\
&=P(x_{1}|c_{1};\theta)P(x_{2}|c_{2};\theta)\cdots P(x_{m}|c_{m};\theta)
\end{aligned}
$$

其中，$D$ 是带标签的数据集，$\theta$ 是模型参数。$P(x_{i}|c_{i};\theta)$ 是在类别 $c_{i}$ 的条件下，样本 $x_{i}$ 出现的概率。

第二步，整体取对数。

$$
\begin{aligned}
\log L(\theta)&=\log P(D|\theta)\\
&=\log \prod\limits_{i=1}^{m}P(x_{i}|c_{i};\theta)\\
&=\sum\limits_{i=1}^{m}\log P(x_{i}|c_{i};\theta)
\end{aligned}
$$

第三步，求导。

$$
\begin{aligned}
\frac{\partial \log L(\theta)}{\partial \theta}&=\frac{\partial \sum\limits_{i=1}^{m}\log P(x_{i}|c_{i};\theta)}{\partial \theta}\\
&=\sum\limits_{i=1}^{m}\frac{\partial \log P(x_{i}|c_{i};\theta)}{\partial \theta}
\end{aligned}
$$

然后令导数为 0，解出 $\theta$。这样求得的 $\theta$ 也可以写成 $\hat{\theta}$，表示是估计值，它观察到的数据的概率最大化。

### 朴素 Bayes 分类器

朴素 Bayes 分类器（Naive Bayes Classifier）是一种基于 Bayes 理论的简单分类器，它假设**特征之间相互独立**。

$$
P(c_{i}|x)=\frac{P(x|c_{i})P(c_{i})}{P(x)}=\frac{P(x|c_{i})P(c_{i})}{\sum\limits_{j=1}^{n}P(x|c_{j})P(c_{j})}
$$

其中，$n$ 是类别的数量，$P(c_{i}|x)$ 是在给定特征 $x$ 的条件下，样本属于类别 $c_{i}$ 的概率。

对于先验概率，如果特征是连续的，那么可以使用高斯分布来估计；如果特征是离散的，那么可以使用多项式分布来估计。

直接举例，来点人话：

| 编号 | 色泽 | 根蒂 | 敲声 | 纹理 | 脐部 | 触感 | 密度  | 含糖率 | 好瓜 |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: | :---: | :----: | :--: |
|  1   | 青绿 | 蜷缩 | 浊响 | 清晰 | 凹陷 | 硬滑 | 0.697 | 0.460  |  是  |
|  2   | 乌黑 | 蜷缩 | 沉闷 | 清晰 | 凹陷 | 硬滑 | 0.774 | 0.376  |  是  |
|  3   | 乌黑 | 蜷缩 | 浊响 | 清晰 | 凹陷 | 硬滑 | 0.634 | 0.264  |  是  |
|  4   | 青绿 | 蜷缩 | 沉闷 | 清晰 | 凹陷 | 硬滑 | 0.608 | 0.318  |  是  |
|  5   | 浅白 | 蜷缩 | 浊响 | 清晰 | 凹陷 | 硬滑 | 0.556 | 0.215  |  是  |
|  6   | 青绿 | 稍蜷 | 浊响 | 清晰 | 稍凹 | 软粘 | 0.403 | 0.237  |  是  |
|  7   | 乌黑 | 稍蜷 | 浊响 | 稍糊 | 稍凹 | 软粘 | 0.481 | 0.149  |  是  |
|  8   | 乌黑 | 稍蜷 | 浊响 | 清晰 | 稍凹 | 硬滑 | 0.437 | 0.211  |  是  |
|  9   | 乌黑 | 稍蜷 | 沉闷 | 稍糊 | 稍凹 | 硬滑 | 0.666 | 0.091  |  否  |
|  10  | 青绿 | 硬挺 | 清脆 | 清晰 | 平坦 | 软粘 | 0.243 | 0.267  |  否  |
|  11  | 浅白 | 硬挺 | 清脆 | 模糊 | 平坦 | 硬滑 | 0.245 | 0.057  |  否  |
|  12  | 浅白 | 蜷缩 | 浊响 | 模糊 | 平坦 | 软粘 | 0.343 | 0.099  |  否  |
|  13  | 青绿 | 稍蜷 | 浊响 | 稍糊 | 凹陷 | 硬滑 | 0.639 | 0.161  |  否  |
|  14  | 浅白 | 稍蜷 | 沉闷 | 稍糊 | 凹陷 | 硬滑 | 0.657 | 0.198  |  否  |
|  15  | 乌黑 | 稍蜷 | 浊响 | 清晰 | 稍凹 | 软粘 | 0.360 | 0.370  |  否  |
|  16  | 浅白 | 蜷缩 | 浊响 | 模糊 | 平坦 | 硬滑 | 0.593 | 0.042  |  否  |
|  17  | 青绿 | 蜷缩 | 沉闷 | 稍糊 | 稍凹 | 硬滑 | 0.719 | 0.103  |  否  |

一个测试用例：

| 编号 | 色泽 | 根蒂 | 敲声 | 纹理 | 脐部 | 触感 | 密度  | 含糖率 | 好瓜 |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: | :---: | :----: | :--: |
| 测1  | 青绿 | 蜷缩 | 浊响 | 清晰 | 凹陷 | 硬滑 | 0.697 | 0.460  |  ？  |

因此，能够算出 $P(\text{好瓜}=\text{是})=\frac{8}{17}$，$P(\text{好瓜}=\text{否})=\frac{9}{17}$。

并且有离散值的条件概率：

$$
\begin{aligned}
P(\text{色泽}=\text{青绿}|\text{好瓜}=\text{是})&=\frac{3}{8}\\
P(\text{色泽}=\text{青绿}|\text{好瓜}=\text{否})&=\frac{3}{9}\\
P(\text{根蒂}=\text{蜷缩}|\text{好瓜}=\text{是})&=\frac{5}{8}\\
P(\text{根蒂}=\text{蜷缩}|\text{好瓜}=\text{否})&=\frac{3}{9}\\
P(\text{敲声}=\text{浊响}|\text{好瓜}=\text{是})&=\frac{6}{8}\\
P(\text{敲声}=\text{浊响}|\text{好瓜}=\text{否})&=\frac{4}{9}\\
P(\text{纹理}=\text{清晰}|\text{好瓜}=\text{是})&=\frac{7}{8}\\
P(\text{纹理}=\text{清晰}|\text{好瓜}=\text{否})&=\frac{2}{9}\\
P(\text{脐部}=\text{凹陷}|\text{好瓜}=\text{是})&=\frac{6}{8}\\
P(\text{脐部}=\text{凹陷}|\text{好瓜}=\text{否})&=\frac{2}{9}\\
P(\text{触感}=\text{硬滑}|\text{好瓜}=\text{是})&=\frac{6}{8}\\
P(\text{触感}=\text{硬滑}|\text{好瓜}=\text{否})&=\frac{6}{9}
\end{aligned}
$$

而连续值的条件概率：

$$
\begin{aligned}
\rho(\text{密度}=0.697|\text{好瓜}=\text{是})&=\frac{1}{\sqrt{2\pi}\cdot 0.129}\exp(-\frac{(0.697-0.574)^{2}}{2\cdot 0.129^{2}})\approx 1.959\\
\rho(\text{密度}=0.697|\text{好瓜}=\text{否})&=\frac{1}{\sqrt{2\pi}\cdot 0.195}\exp(-\frac{(0.697-0.496)^{2}}{2\cdot 0.195^{2}})\approx 1.203\\
\rho(\text{含糖率}=0.460|\text{好瓜}=\text{是})&=\frac{1}{\sqrt{2\pi}\cdot 0.101}\exp(-\frac{(0.460-0.279)^{2}}{2\cdot 0.101^{2}})\approx 0.788\\
\rho(\text{含糖率}=0.460|\text{好瓜}=\text{否})&=\frac{1}{\sqrt{2\pi}\cdot 0.108}\exp(-\frac{(0.460-0.154)^{2}}{2\cdot 0.108^{2}})\approx 0.066
\end{aligned}
$$

那么，对于这个测试用例，我们可以得到：

$$
P(\text{好瓜}=\text{是})\times P(\text{青绿}|\text{是})\times P(\text{蜷缩}|\text{是})\times P(\text{浊响}|\text{是})\times P(\text{清晰}|\text{是})\times P(\text{凹陷}|\text{是})\times P(\text{硬滑}|\text{是})\times \rho(\text{密度}=0.697|\text{是})\times \rho(\text{含糖率}=0.460|\text{是})\approx 0.038\\
P(\text{好瓜}=\text{否})\times P(\text{青绿}|\text{否})\times P(\text{蜷缩}|\text{否})\times P(\text{浊响}|\text{否})\times P(\text{清晰}|\text{否})\times P(\text{凹陷}|\text{否})\times P(\text{硬滑}|\text{否})\times \rho(\text{密度}=0.697|\text{否})\times \rho(\text{含糖率}=0.460|\text{否})\approx 6.80\times 10^{-5}
$$

正是因为 $0.038>6.80\times 10^{-5}$，所以这个测试用例被分类为“是好瓜”。

### Laplace 修正

对于上面的数据集，我们观察这个例子：

| 编号 | 色泽 | 根蒂 |   敲声   | 纹理 | 脐部 | 触感 | 密度  | 含糖率 | 好瓜 |
| :--: | :--: | :--: | :------: | :--: | :--: | :--: | :---: | :----: | :--: |
| 测2  | 青绿 | 蜷缩 | **清脆** | 清晰 | 凹陷 | 硬滑 | 0.697 | 0.460  |  ？  |

结果，数据集里面根本就没有敲声“清脆”还能是好瓜的，即：

$$
P(\text{敲声}=\text{清脆}|\text{好瓜}=\text{是})=0
$$

那我们算条件概率的时候，乘了个零，不就啥也没有了吗？虽然这事挺符合数据集的（没这种例子，可能性当然为零），但是不太符合现实。因此，我们需要对这种情况进行修正。

这种修正被称为“平滑”（Smoothing），而 Laplace 修正（Laplacian Correction）就是一种经典方法。

拉普拉斯平滑的核心思想是在概率估计中引入一个小的修正值，以确保后验概率永远不会为零。这样，即使在训练数据中没有观察到某个特征，其概率也不会被计算为零，从而避免了零概率问题。

$$
\begin{aligned}
\hat{P}(c)&=\frac{|D_{c}|+1}{|D|+N}\\
\hat{P}(x_{i}|c)&=\frac{|D_{c,x_{i}}|+1}{|D_{c}|+N_{i}}
\end{aligned}
$$

这其中，$N$就是特征的取值个数，$N_{i}$是第 $i$ 个特征的取值个数。

对于测试用例测2，就有：

$$
\begin{aligned}
P(\text{好瓜}=\text{是})&=\frac{8+1}{17+2}\approx 0.474\\
P(\text{好瓜}=\text{否})&=\frac{9+1}{17+2}\approx 0.526\\
\end{aligned}
$$

这里，我们可以看到，虽然数据集里面没有敲声“清脆”还能是好瓜的，但是我们的修正之后，这个概率不再是零了。下面分母所加的 $2$ 就是 $N$，即瓜在“是否为好瓜”上有两种取值。

$$
\begin{aligned}
P(\text{色泽}=\text{青绿}|\text{好瓜}=\text{是})&=\frac{3+1}{8+3}\approx 0.364\\
P(\text{色泽}=\text{青绿}|\text{好瓜}=\text{否})&=\frac{3+1}{9+3}\approx 0.333\\
P(\text{敲声}=\text{清脆}|\text{好瓜}=\text{是})&=\frac{0+1}{8+3}\approx 0.091\\
\end{aligned}
$$

这里呢，我们看到的 $3$，譬如说罢，对于敲声，一共有三种，即“浊响”、“沉闷”、“清脆”。我们用 Laplace 修正后的概率去计算，就不会出现零概率的情况了。

### 基于最小风险的 Bayes 决策

基于最小风险的 Bayes 决策（Bayes Decision with Minimum Risk）是一种基于 Bayes 理论的分类方法，它将分类错误的损失考虑在内，选择具有最小风险的类别作为最终的分类结果。

我们假设一个有 $N$ 个标签状态的集合：

$$
\mathcal{Y}=\{c_{1},c_{2},\cdots,c_{N}\}
$$

我们还考虑了风险，即 $\lambda_{ij}$。它意味着因将 $𝑐_{𝑖}$ 误认为 $𝑐_{𝑗}$ 而造成的损失。

现在，给定 $x=[x_{1},x_{2},\cdots,x_{d}]^{T}$，那么条件风险（Conditional Risk）即为：

$$
R(c_{i}|x)=\sum\limits_{j=1}^{N}\lambda_{ij}P(c_{j}|x)
$$

这个风险公式的意思就是说，如果将 $𝑐_{𝑖}$ 误认为 $𝑐_{𝑗}$，那么损失就是 $\lambda_{ij}$，而这个损失的概率就是 $P(c_{j}|x)$。把它们加起来，就是将 $c_{i}$ 认错的总损失。

>   更详细地说，$R(c_{i}|x)$ 的含义是：预测为 $c_{i}$ 可能会造成的损失。注意 $c_{i}$ 是预测值；一开始还以为是事实情况是 $c_{i}$，其实不是这样的。

现在，我们已经定义好了每一个状态认错的损失；基于最小风险的 Bayes 决策就是选择具有最小风险的类别作为最终的分类结果。关键在于，我们想要找到一种决策规则，使得**总体风险最小**。

$$
R(h)=E_{x}[R(h(x)|x)]
$$

其中，$h(x)$ 是决策规则，$R(h(x)|x)$ 是在给定 $x$ 的条件下，决策规则 $h(x)$ 的风险。

于是：

$$
h^{\ast}(x)=\arg\min\limits_{c_{i}\in \mathcal{Y}}R(c_{i}|x)
$$

在公式中，$h^{\ast}(x)$表示最优决策，即能够最小化期望风险的决策。$\arg\min$表示求取使得后面表达式取得最小值的参数。$c_i$表示决策空间$\mathcal{Y}$中的一个具体决策。

通过求解这个最小化问题，我们可以找到能够最小化给定观测数据下期望风险的最优决策。换句话说，$h^{\ast}(x)$是使得$R(c_i|x)$取得最小值的决策$c_i$。

>   幻灯片中给的是 $h\ast(x)$，但是总感觉是幻灯片错了，实际应该是 $h^{\ast}(x)$。

**例子**

假设在一个 local area 内，细胞识别正常和异常的先验概率为：

$$
\begin{aligned}
\text{Normal}&:P(c_{1})=0.9\\
\text{Abnormal}&:P(c_{2})=0.1
\end{aligned}
$$

现有一个待识别的细胞，从类条件概率密度分布曲线得到观测值为 $x$。

$$
\begin{aligned}
p(x|c_{1})&=0.2\\
p(x|c_{2})&=0.4
\end{aligned}
$$

试问正常还是异常？

首先，用 Bayes 公式计算后验概率：

$$
\begin{aligned}
P(c_{1}|x)&=\frac{p(x|c_{1})P(c_{1})}{p(x|c_{1})P(c_{1})+p(x|c_{2})P(c_{2})}\\
&=\frac{0.2\times 0.9}{0.2\times 0.9+0.4\times 0.1}\\
&=0.818\\
P(c_{2}|x)&=1-P(c_{1}|x)=0.182
\end{aligned}
$$

倘若直接用 Bayes 决策，那么就是：

$$
\begin{aligned}
P(c_{1}|x)&>P(c_{2}|x)\\
0.818&>0.182
\end{aligned}
$$

结果自然是正常。但是，现在我们考虑决策风险如下：

|     Loss      | 事实为$c_{1}$ | 事实为$c_{2}$ |
| :-----------: | :-----------: | :-----------: |
| 决策为$c_{1}$ |       0       |       6       |
| 决策为$c_{2}$ |       1       |       0       |

比如说，如果真实情况是 $c_{1}$，但是决策结果是 $c_{2}$，那么损失就是 $1$。至于决策对了，那自然没有 Loss。可以把这个表看作是 $\lambda_{ij}$ 表。

开始计算条件风险：

$$
\begin{aligned}
R(c_{1}|x)&=0\times P(c_{1}|x)+6\times P(c_{2}|x)=1.092\\
R(c_{2}|x)&=1\times P(c_{1}|x)+0\times P(c_{2}|x)=0.818
\end{aligned}
$$

计算了条件风险之后，我们就可以进行最小风险决策了：

$$
\begin{aligned}
R(c_{1}|x)&>R(c_{2}|x)\\
1.092&>0.818
\end{aligned}
$$

所以，最终的决策结果是异常。

分类的结果相反了，这是因为影响决策结果的因素多了一个，那就是“损失”。而两类错误决策造成的损失也截然不同，因此“损失”起了主导作用。

### 参数估计

从样本集中估计总体概率分布的方法可以概括如下：

-   监督参数估计 $(\ast)$
-   无监督参数估计
-   非参数估计

#### 监督参数估计

样本所属的**类别和条件**以整体概率密度函数的形式**已知**，而用于表示概率密度函数的一些参数是未知的。

例如，只知道样本的整体分布为正态分布，但是正态分布的参数（$\mu$，$\sigma$）未知。我们的目标是从一组已知类别的样本中统计判断分布。这种情况下的估计称为有监督的参数估计。

#### 无监督参数估计

总体概率密度函数已知，但样本所属类别未知，需要确定概率密度函数的一些参数。

有监督和无监督是指样本所属的类别是已知还是未知。常用的方法有两种，一种是最大似然估计，另一种是 Bayes 估计。

>   说点人话。所谓“样本所属类别”指的是样本数据在分类问题中所对应的类别或标签。我们通常有一组**已知类别**的样本数据，每个样本都被分配到一个特定的类别中。这些类别可以是离散的，比如二分类问题中的"正例"和"负例"，或者多分类问题中的不同类别标签（比如，嗯，“中杯”“大杯”“超大杯”？这样子，总之是多标签）。
>
>   相比之下，无监督参数估计中，样本数据的类别未知，我们需要确定概率密度函数的一些参数。在这种情况下，我们没有类别信息来指导参数估计，而是仅仅依靠样本数据本身的统计特征来进行估计。

最大似然估计（MLE）是一种基于观测数据的频率统计方法，用于估计参数的值。它的核心思想是选择使观测数据出现的概率最大的参数值作为估计值。简单来说，MLE认为最有可能产生观测数据的参数值就是我们要估计的参数值。

举个例子，假设我们有一组观测数据，表示一批学生的身高。我们假设这些身高数据服从正态分布，并且我们想要**估计正态分布**的 $\mu$ 和 $\sigma$。使用MLE方法，我们会选择使得观测数据出现的概率最大的 $\mu$ 和 $\sigma$ 作为估计值。

Bayes 估计是一种基于 Bayes 定理的方法，它将观测数据和先验知识结合起来，得出参数的后验分布，并使用后验分布来估计参数的值。Bayes 估计考虑了先验知识和观测数据之间的关系，通过更新先验分布来得到后验分布，从而进行参数估计。

继续以上述身高数据的例子， Bayes 估计会引入先验分布，对 $\mu$ 和 $\sigma$ 进行建模。我们可以选择一个合适的先验分布，比如正态分布，来表示我们对 $\mu$ 和 $\sigma$ 的先验知识。然后，通过 Bayes 定理，我们可以计算观测数据给定先验分布下的后验分布，即参数的估计分布。最后，我们可以使用后验分布的某种统计量（如 $\mu$ 或中位数）来估计参数的值。

关键在于，Bayes 估计将未知参数视为**具有一定分布的随机变量**。样本的观测结果将先验分布变换为后验分布，然后根据后验分布修正参数的原始估计。

#### 非参数估计

样本所属的类别是已知的，但概率密度函数的形式是未知的，因此需要我们直接推断概率密度函数本身。

##### Parzen Window

Parzen Window 是一种非参数估计方法，用于估计数据的概率密度函数。它基于核函数的思想，通过在每个数据点周围放置一个窗口（经常是 Gaussian Window），来估计数据的概率密度函数。

对于一个数据点 $x$，Parzen Window 估计的概率密度函数为：

$$
p(x)=\frac{k_{N}/N}{V}
$$

其中，$k_{N}$ 是位于 $x$ 周围的窗口内的数据点的数量，$N$ 是总的数据点的数量，$V$ 是窗口的体积。

>   这啥啊，半天没看懂，之后再说吧。Mark 一下。

##### KNN

非参数估计方法不对模型的具体形式做出假设，而是根据数据的分布情况来进行估计。

KNN作为一种非参数估计方法，可以用于分类和回归问题。在KNN中，我们根据样本数据的特征来判断一个新的数据点属于哪个类别或者预测其数值。

KNN的基本思想是，对于一个新的数据点，我们找到训练集中与其最接近的K个邻居，然后根据这K个邻居的类别或数值来进行估计。对于分类问题，通常采用投票的方式，选择K个邻居中出现最频繁的类别作为估计结果。对于回归问题，通常采用平均或加权平均的方式，根据K个邻居的数值来估计新数据点的数值。

KNN的关键参数是K值，它决定了选择多少个邻居参与估计。较小的K值会导致估计结果更加敏感，容易受到噪声的影响，而较大的K值会导致估计结果更加平滑，容易忽略局部细节。

然而，KNN算法也有一些缺点。首先，它的计算复杂度较高，特别是在大规模数据集上。其次，KNN对于特征空间中的异常值和噪声比较敏感。此外，选择合适的K值和距离度量方法也需要一定的经验或通过交叉验证等方法来确定。

总结一下，K最近邻（KNN）可以作为非参数估计方法的一种应用，用于分类和回归问题。它的优点是简单易于实现，适用于多分类和回归问题，而缺点是计算复杂度较高，并且对异常值和噪声比较敏感。

>   麻了，之后再说。Mark 一下。

### 半朴素 Bayes 分类器

#### Bayes 网络

半朴素 Bayes 分类器是一种基于 Bayes 网络的分类方法，它通过考虑属性之间的相关性，来改进朴素 Bayes 分类器。半朴素 Bayes 分类器的基本思想是，对于每个属性，我们假设它仅依赖于一个属性或者一组属性，而不是所有属性。这样，我们就可以考虑属性之间的相关性，从而改进朴素 Bayes 分类器。

所以，先插播一下 Bayes 网络的知识。

Bayes 网络是一个 DAG。举个例子：

![A_DAG_Bayes_Network](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_DAG_Bayes_Network.png)

在这个例子中，有四个节点表示四个属性：A、B、C 和 D。节点 A、B 和 D 都指向节点 C，表示属性 C 依赖于属性 A、B 和 D。

这个简单的 Bayes 网络展示了属性之间的依赖关系。属性 C 的取值依赖于属性 A、B 和 D 的取值。通过这个网络，我们可以推断在给定属性 A、B 和 D 的取值条件下，属性 C 的概率分布。

| 属性 | P(True) | P(False) |
|:----:|:-------:|:--------:|
| A    | 0.6     | 0.4      |
| B    | 0.8     | 0.2      |
| D    | 0.3     | 0.7      |
| C given A=True, B=True, D=True | 0.9 | 0.1 |

像这样，C 的概率分布可以通过属性 A、B 和 D 的概率分布来计算。这个计算过程就是 Bayes 网络的推断过程。

------

回到半朴素 Bayes 分类器来。在实际场景中，属性独立性假设常常不成立，即属性之间存在一定的相关性。此时，朴素 Bayes 分类器就不能很好地工作了。为了解决这个问题，我们可以使用半朴素 Bayes 分类器。

ODE（**One-Dependent** Estimator，**单相关**估计器）：假设一个属性仅依赖于大多数其他属性。

$$
P(c|x)\propto P(c)\prod\limits_{i=1}^{d}P(x_{i}|c,pa_{i})
$$

其中，$pa_{i}$ 表示属性 $x_{i}$ 的父属性。

假设我们有一个分类问题，其中属性 $x$ 是我们要预测的类别，而属性 $x_1, x_2, \ldots, x_d$ 是与属性 $x$ 相关的其他属性。ODE 假设属性 $x$ 只依赖于大多数其他属性，即属性 $x$ 仅依赖于其父属性 $pa_i$（父属性是指在 Bayes 网络中与属性 $x$ 相关的属性）。

公式中的 $P(c|x)$ 表示给定属性 $x$ 条件下类别 $c$ 的概率。该概率与先验概率 $P(c)$ 和条件概率 $P(x_i|c, pa_i)$ 相关。

$P(c)$ 表示类别 $c$ 的先验概率，即在没有任何属性信息的情况下，类别 $c$ 出现的概率。

$\prod\limits_{i=1}^{d}P(x_i|c,pa_i)$ 表示在给定类别 $c$ 和其父属性 $pa_i$ 的条件下，属性 $x_i$ 出现的概率的乘积。这个乘积表示了属性 $x$ 与其父属性之间的关联关系。

公式中的 $\propto$ 表示“与之成比例”，即公式右侧的结果与 $P(c|x)$ 成正比。为了得到准确的概率值，还需要进行归一化操作，使得所有类别的概率之和为1。

ODE 公式的基本思想是，通过考虑属性 $x$ 与其父属性之间的关系，综合考虑属性 $x$ 的先验概率和条件概率，来计算给定属性 $x$ 条件下类别的概率。这样可以更准确地估计类别的概率，尤其在属性之间存在相关性的情况下。

需要注意的是，ODE 公式是基于假设的前提，即属性 $x$ 仅依赖于大多数其他属性。如果这个假设不成立，ODE 公式可能不适用。在实际应用中，我们需要根据具体问题和数据的特点来选择合适的估计方法。

#### SPODE

SPODE（Super-Parent ODE）假设所有属性都依赖于一个属性，那个属性被称为 Super Parent 属性。

SPODE（Super-Parent ODE）是一种改进的 Bayes 网络结构，用于建模变量之间的依赖关系。与传统的 Bayes 网络中的节点只依赖于其父节点不同，SPODE引入了 Super Parent 节点，使节点可以依赖于 Super Parent 节点和其他父节点。

在传统的 Bayes 网络中，每个节点的条件概率是基于其父节点的条件概率定义的。这种建模方式有时会面临两个问题：

1.  当节点的父节点数量很大时，条件概率表的大小会急剧增加，导致参数估计困难
2.  节点的条件概率可能无法准确地捕捉节点之间的复杂依赖关系。

为了解决这些问题，SPODE引入了 Super Parent 节点。Super Parent 节点是一个虚拟节点，它与所有其他节点相连。每个节点不仅依赖于其父节点，还依赖于 Super Parent 节点。Super Parent 节点的目的是捕捉全局的依赖关系，帮助节点更好地建模。

$$
P(c|x)\propto P(c)\prod\limits_{i=1}^{d}P(x_{i}|c,x_{1})
$$

其中，$x_{1}$ 是 Super Parent 节点。

SPODE 公式与 ODE 公式的区别在于，SPODE 公式中的条件概率 $P(x_i|c, x_1)$ 是基于 Super Parent 节点的条件概率定义的。这样，每个节点的条件概率表的大小就不会随着父节点数量的增加而增加，从而避免了参数估计困难的问题。

#### AODE

AODE（Averaged One-Dependent Estimator）同样放宽了朴素 Bayes 的属性条件独立假设，也就是说，AODE 基于 SPODE。

AODE 假设每个属性只依赖于数据集中的另一个属性，即 Super Parent 属性。AODE 通过模型**集成**的思想，结合了多个 SPODE，从而提高了分类性能。

具体来说,AODE 为每个属性 $x_{i}$ 选择一个 Super Parent 属性 $pa_{i}$，使得 $x_{i}$ 只依赖于 $pa_{i}$。然后计算 $P(x_{i}|c,pa_{i})$的条件概率。最后将所有模型的分类结果进行平均，得到最终的分类结果。

AODE 可以视作是一种有足够的统计数据支持的 SPODE。

$$
P(c|x)\propto\sum\limits_{i=1,|D_{x_{i}}|\ge m}^{d}P(c,x_{i})\prod\limits_{j=1}^{d}P(x_{j}|c,pa_{i})
$$

其中，$m$ 是一个阈值，用于控制 Super Parent 属性的数量。$D_{x_{i}}$ 表示属性 $x_{i}$ 的取值集合。$N_{i}$ 表示属性 $x_{i}$ 的取值个数。

引入 Laplace 修正，那么：

$$
\begin{aligned}
\hat{P}(c,x_{i})&=\frac{|D_{c,x_{i}}|+1}{|D|+N_{i}}\\
\hat{P}(x_{j}|c,x_{i})&=\frac{|D_{c,x_{i},x_{j}}|+1}{|D_{c,x_{i}}|+N_{j}}
\end{aligned}
$$

回顾一下挑瓜数据集：

| 编号 | 色泽 | 根蒂 | 敲声 | 纹理 | 脐部 | 触感 | 密度  | 含糖率 | 好瓜 |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: | :---: | :----: | :--: |
|  1   | 青绿 | 蜷缩 | 浊响 | 清晰 | 凹陷 | 硬滑 | 0.697 | 0.460  |  是  |
|  2   | 乌黑 | 蜷缩 | 沉闷 | 清晰 | 凹陷 | 硬滑 | 0.774 | 0.376  |  是  |
|  3   | 乌黑 | 蜷缩 | 浊响 | 清晰 | 凹陷 | 硬滑 | 0.634 | 0.264  |  是  |
|  4   | 青绿 | 蜷缩 | 沉闷 | 清晰 | 凹陷 | 硬滑 | 0.608 | 0.318  |  是  |
|  5   | 浅白 | 蜷缩 | 浊响 | 清晰 | 凹陷 | 硬滑 | 0.556 | 0.215  |  是  |
|  6   | 青绿 | 稍蜷 | 浊响 | 清晰 | 稍凹 | 软粘 | 0.403 | 0.237  |  是  |
|  7   | 乌黑 | 稍蜷 | 浊响 | 稍糊 | 稍凹 | 软粘 | 0.481 | 0.149  |  是  |
|  8   | 乌黑 | 稍蜷 | 浊响 | 清晰 | 稍凹 | 硬滑 | 0.437 | 0.211  |  是  |
|  9   | 乌黑 | 稍蜷 | 沉闷 | 稍糊 | 稍凹 | 硬滑 | 0.666 | 0.091  |  否  |
|  10  | 青绿 | 硬挺 | 清脆 | 清晰 | 平坦 | 软粘 | 0.243 | 0.267  |  否  |
|  11  | 浅白 | 硬挺 | 清脆 | 模糊 | 平坦 | 硬滑 | 0.245 | 0.057  |  否  |
|  12  | 浅白 | 蜷缩 | 浊响 | 模糊 | 平坦 | 软粘 | 0.343 | 0.099  |  否  |
|  13  | 青绿 | 稍蜷 | 浊响 | 稍糊 | 凹陷 | 硬滑 | 0.639 | 0.161  |  否  |
|  14  | 浅白 | 稍蜷 | 沉闷 | 稍糊 | 凹陷 | 硬滑 | 0.657 | 0.198  |  否  |
|  15  | 乌黑 | 稍蜷 | 浊响 | 清晰 | 稍凹 | 软粘 | 0.360 | 0.370  |  否  |
|  16  | 浅白 | 蜷缩 | 浊响 | 模糊 | 平坦 | 硬滑 | 0.593 | 0.042  |  否  |
|  17  | 青绿 | 蜷缩 | 沉闷 | 稍糊 | 稍凹 | 硬滑 | 0.719 | 0.103  |  否  |

我们可以写出：

$$
\begin{aligned}
P(\text{好瓜}=\text{是},\text{敲声}=\text{浊响})&=\frac{6+1}{17+3}\approx 0.350\\
P(\text{脐部}=凹陷|\text{好瓜}=\text{是},\text{敲声}=\text{浊响})&=\frac{3+1}{6+3}\approx 0.444\\
\end{aligned}
$$

>   话又说回来，我不知道为什么这么写的。之后再说。Mark 一下。

#### 如何获得后验概率

在机器学习中，获得后验概率 $P(c|x)$ 有两种方法：

1.  判别式模型（Discriminative Model）。例如：SVM、决策树等。
2.  生成式模型（Generative Model）。例如：朴素 Bayes，AODE 等。

### 例题

不知道说什么了……来点题目吧。

Q：水果糖问题。两个一模一样的碗，一号碗：30颗水果糖和10颗巧克力糖；二号碗：20颗水果糖和20颗巧克力糖。现在随机选择一个碗，从中摸出一颗糖，发现是水果糖。请问这颗水果糖来自一号碗的概率有多大？

A：设 $A$ 为从一号碗中摸糖，$B$ 为从二号碗中摸糖。那么：

$$
\begin{aligned}
P(A)&=\frac{1}{2}\\
P(B)&=\frac{1}{2}\\
P(\text{水果糖}|A)&=\frac{3}{4}\\
P(\text{水果糖}|B)&=\frac{1}{2}\\
P(A|\text{水果糖})&=\frac{P(A)P(\text{水果糖}|A)}{P(A)P(\text{水果糖}|A)+P(B)P(\text{水果糖}|B)}\\
&=\frac{\frac{1}{2}\times\frac{3}{4}}{\frac{1}{2}\times\frac{3}{4}+\frac{1}{2}\times\frac{1}{2}}\\
&=\frac{3}{5}
\end{aligned}
$$

Q：已知某种疾病的发病率是$0.001$，即$1000$人中会有$1$个人得病。现有一种试剂可以检验患者是否得病，它的准确率是$0.99$，即在患者确实得病的情况下，它有$99\%$的可能呈现阳性。它的误报率是$5\%$，即在患者没有得病的情况下，它有$5\%$的可能呈现阳性。现有一个病人的检验结果为阳性，请问他确实得病的可能性有多大？

A：设$A$为病人确实得病，$B$为病人检验结果为阳性。那么：

$$
\begin{aligned}
P(A)&=0.001\\
P(B|A)&=0.99\\
P(B|\overline{A})&=0.05\\
P(A|B)&=\frac{P(A)P(B|A)}{P(A)P(B|A)+P(\overline{A})P(B|\overline{A})}\\
&=\frac{0.001\times 0.99}{0.001\times 0.99+0.999\times 0.05}\\
&\approx 0.019
\end{aligned}
$$

Q：$8$支步枪中有$5$支已校准过，$3$支未校准。一名射手用校准过的枪射击，中靶概率为$0.8$；用未校准的抢射击，中靶概率为$0.3$；现从$8$支抢中随机取一支射击，结果中靶。求该枪是已校准过的概率。

A：设$A$为枪已校准过，$B$为枪未校准过，$C$为射手中靶。那么：

$$
\begin{aligned}
P(A)&=\frac{5}{8}\\
P(B)&=\frac{3}{8}\\
P(C|A)&=0.8\\
P(C|B)&=0.3\\
P(A|C)&=\frac{P(A)P(C|A)}{P(A)P(C|A)+P(B)P(C|B)}\\
&=\frac{\frac{5}{8}\times 0.8}{\frac{5}{8}\times 0.8+\frac{3}{8}\times 0.3}\\
&=\frac{40}{49}\\
\approx 0.816
\end{aligned}
$$

## 决策树

![Example_of_A_Decision_Tree](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Example_of_A_Decision_Tree.png)

嘛，所谓「决策树」，大约就是长这样的一个东西。

-   每个非叶节点代表属性的一个 partition
-   每个 partition 的结果要么导致进一步的决策问题，要么导致最终结论
-   决策树通过从 root 开始并移动到分支直到叶节点来对实例进行分类
-   决策过程的最终结论对应一个目标值

显然，要构建一个决策树，我们需要：

1.  决定从哪个属性开始构建（也就是决定 root）
2.  非叶节点的分支如何构建（也就是决定后续每个分支上选择哪个属性）
3.  什么时候停止（也就是决定叶节点的判定条件）

我们当然希望选择**最好的**属性来分割剩余的实例，并使该属性成为一个节点。不断重复这个过程，直到所有实例都被分类到叶节点上。这意味着：

-   对于当前节点，所有实例都有相同的目标值
-   或者没有更多属性或者实例在所有剩余属性中具有相同的值
-   或者没有实例剩余

所以说，决策树的关键在于如何划分。

### ID3

ID3 算法是一种决策树算法，用于从一组实例中构建决策树。它基于**信息增益**的概念，选择信息增益（Information Gain）最大的属性来构建决策树。

所谓信息增益，就是指在得知某个条件后，信息的不确定性减少的程度。信息增益越大，意味着得知这个条件后，信息的不确定性减少的程度越大，也就是说，这个条件对于我们来说越有用。（非常像熵）

当 $p_{i}$ 用于划分标记为 $i$ 的实例时，信息熵的公式如下：

$$
\text{Entropy}(p_{1},p_{2},\ldots,p_{n})=-\sum\limits_{i=1}^{n}p_{i}\log{p_{i}}
$$

对于二元分一组实例的情况，熵为：

$$
\text{Entropy}(p,1-p)=-p\log{p}-(1-p)\log{(1-p)}
$$

显然，如果所有实例属于同一类，则熵为 $0$。因为这个时候 $p=1$，虽然不那么严谨，但是总之认为上述式子为 $0$ 就好了。

同样，显然当所有实例属于同一类时，熵最小；当实例均匀分布在各个类别时，熵最大。

属性的信息增益是由于对该属性进行划分而导致的预期熵减少。譬如，有属性 $a$ 将数据集 $D$ 划分出了一个子集 $D_{i}$，那么：

$$
\text{Gain}(D,a)=\text{Entropy}(D)-\sum\limits_{i=1}^{n}\frac{|D_{i}|}{|D|}\text{Entropy}(D_{i})
$$

这个式子用于衡量属性$a$对数据集$D$进行划分所带来的预期熵减少。显然，熵减意味着信息变得有序了，也就是说，我们可以更好地对数据进行分类。

$\text{Entropy}(D)$ 表示整个数据集$D$的熵。熵用于度量数据集的不确定性或混乱程度。而 $\sum\limits_{i=1}^{n}\frac{|D_{i}|}{|D|}\text{Entropy}(D_{i})$ 表示对属性$a$的每个取值，计算对应子集$D_i$的熵$\text{Entropy}(D_i)$并进行加权求和。其中，$|D_i|$表示子集$D_i$的样本数目，$|D|$表示整个数据集$D$的样本数目。

举个例子。假设 $D$ 是「五个好瓜样本，五个坏瓜样本」，通过一次 partition 操作 $a$，得到 $D_{1}$ 是「二个好瓜样本，一个坏瓜样本」，$D_{2}$ 是「三个好瓜样本，四个坏瓜样本」。那么：

$$
\begin{aligned}
\text{Gain}(D,a)&=\text{Entropy}(D)-\sum\limits_{i=1}^{2}\frac{|D_{i}|}{|D|}\text{Entropy}(D_{i})\\
&=\text{Entropy}(D)-\frac{3}{10}\text{Entropy}(D_{1})-\frac{7}{10}\text{Entropy}(D_{2})\\
\text{Entropy}(D)&=-\frac{5}{10}\log{\frac{5}{10}}-\frac{5}{10}\log{\frac{5}{10}}\\
&=\log{2}\\
\text{Entropy}(D_{1})&=-\frac{2}{3}\log{\frac{2}{3}}-\frac{1}{3}\log{\frac{1}{3}}\\
&=\frac{1}{3}\log{3}-\frac{2}{3}\log{2}\\
\text{Entropy}(D_{2})&=-\frac{3}{7}\log{\frac{3}{7}}-\frac{4}{7}\log{\frac{4}{7}}\\
&=\frac{3}{7}\log{7}-\frac{4}{7}\log{4}\\
\end{aligned}
$$

嘛，总之就是这么一套算法，通过它，写出类似于 $G(D,\text{色泽})$ 这样的信息增益公式，就可以判断某个属性做 partition 的时候，Info Gain 是多少了。然后我们总是选择 Info Gain 最大的属性来做 partition。

说一个 ID3 的缺点：如果我们把数据编号当作一个属性来 partition，那么 Info Gain 会很大，但是这个属性对于分类没有任何帮助。

### C4.5

因此，引入 Gain Ratio 来度量一个 partition 是不是真的好。

$$
\begin{aligned}
\text{GainRatio}(D,a)&=\frac{\text{Gain}(D,a)}{\text{IV}(D,a)}\\
\text{IV}(D,a)&=-\sum\limits_{i=1}^{n}\frac{|D_{i}|}{|D|}\log{\frac{|D_{i}|}{|D|}}
\end{aligned}
$$

其中，$\text{IV}(D,a)$ 用于衡量属性值的数量。所谓 $\text{IV}$，就是 Intrinsic Value，即属性分裂信息。通过 $\text{IV}$，我们可以衡量了属性 $a$ 的取值对于数据集 $D$ 的重要性。具体计算方式为对属性 $a$ 的每个取值计算其所占比例的负对数，并对所有取值的负对数进行加权求和。

这样，用增益比 $\frac{\text{Gain}(D,a)}{\text{IV}(D,a)}$ 我们就衡量一个 partition 是不是真的好了，因为增益比越大，意味着分裂信息越小，也就是说，属性 $a$ 的取值越少，也就是说，属性 $a$ 越重要，这个 partition 对于分类的贡献也就越大。

### 对连续值划分

在决策树上，对离散值的划分是非常简单的，但是对于连续值的划分就不那么简单了。C4.5 采用 Bi-partition 的方法来处理连续值的划分，即：

$$
A_{d}=\begin{cases}
\text{true}&\text{if}\, A_{c}\le T_{a}\\
\text{false}&\text{otherwise}
\end{cases}
$$

>   我认为幻灯片上的公式打错了（

其中，$T_{a}$ 是属性 $a$ 的一个阈值，通过这种方式，我们可以将连续值 $A_{c}$ 转换为离散值 $A_{d}$。

如何选择阈值 $T_{a}$ 呢？我们的目标是：选择与信息增益最高的分区相对应的阈值。

例如，给定训练集 ${a^{1},a^{2},\ldots,a^{n}}$，可能的分区有：

$$
T_{a}={\frac{a^{i}+a^{i+1}}{2}}\quad i=1,2,\ldots,n-1
$$

手握这个可能的阈值集合，我们希望：

$$
\begin{aligned}
\text{Gain}(D,a)&=\max\limits_{t\in T_{a}}\text{Gain}(D,a,t)\\
&=\max\limits_{t\in T_{a}}\text{Entropy}(D)-\sum\limits_{i=1}^{2}\frac{|D_{i}|}{|D|}\text{Entropy}(D_{i})\\
\end{aligned}
$$

其中，$D_{1}=\{x|x\in D,a_{c}\le t\}$，$D_{2}=\{x|x\in D,a_{c}>t\}$。

求解这个最大化问题，我们就能选取到正确的阈值。

### 剪枝

过多的分支可能会导致过拟合，为此，我们需要剪枝（Pruning）。

剪枝分为两种：

-   预剪枝（Pre-Pruning）
-   后剪枝（Post-Pruning）

简单来说，pre-pruning 就是在构建决策树的过程中停止添加属性；post-pruning 就是在构建决策树之后，对决策树进行剪枝。两者各有优劣，主要看剪枝后泛化性能如何。

直接看例子：

![The_Decision_Tree_Generated](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/The_Decision_Tree_Generated.png)

这是直接从训练集生成的决策树。

![The_Decision_Tree_After_Pruning](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/The_Decision_Tree_After_Pruning.png)

就是这么回事。后剪枝会看剪除某分支前后的泛化性能（其实就是看测试集上准确度如何），来决定是否要剪枝。

所以，同 pre-pruning 相比，post-pruning 的优点是：

1.  与预剪枝相比，后剪枝的欠拟合风险较低。
2.  后剪枝的泛化能力通常优于预剪枝。

缺点：

1.  后剪枝的计算开销较大。

### 缺失值

缺失值是指在训练集中，某些实例的某些属性值缺失。

对于缺失值，我们有两个问题：

1.  当某些值缺失时如何选择属性？
2.  给定分区属性，如何对这些缺少属性值的示例进行分区？

对于第一个问题，我们是这样解决的。

首先约定三个符号：

-   $\tilde{D}$ 表示训练集 $D$ 中，拥有属性 $a$ 的样本集合
-   $\tilde{D}^{v}$ 表示 $\tilde{D}$ 中，属性 $a$ 的值为 $a^{v}$ 的样本集合
-   $\tilde{D}_{k}$ 表示 $\tilde{D}$ 中 label 为 $K$ 的样本集合

现在，我们对每个样本 $x$ 赋予一个权重 $\omega_{x}$。

属性 $a$ 上**有值**的样本的权重，我们设置为：

$$
\rho=\frac{\sum\limits_{x\in\tilde{D}}\omega_{x}}{\sum\limits_{x\in D}\omega_{x}}
$$

$\tilde{D}$ 中 label 为 $K$ 的样本的权重，我们设置为：

$$
\tilde{p}_{K}=\frac{\sum\limits_{x\in\tilde{D}_{K}}\omega_{x}}{\sum\limits_{x\in\tilde{D}}\omega_{x}}
$$

其中 $1\le K\le |y|$，$|y|$ 表示 label 的种类数。

$\tilde{D}$ 中属性 $a$ 具有值为 $a^{v}$ 的样本的权重，我们设置为：

$$
\tilde{r}_{v}=\frac{\sum\limits_{x\in\tilde{D}^{v}}\omega_{x}}{\sum\limits_{x\in\tilde{D}}\omega_{x}}
$$

其中，$1\le v\le V$，$V$ 表示属性 $a$ 的取值数目。

然后：

$$
\begin{aligned}
\text{Gain}(D,a)&=\rho\times\text{Gain}(\tilde{D},a)\\
&=\rho\times(\text{Entropy}(\tilde{D})-\sum\limits_{v=1}^{V}\tilde{r}_{v}\text{Entropy}(\tilde{D}^{v}))\\
\text{Entropy}(\tilde{D})&=-\sum\limits_{K=1}^{|y|}\tilde{p}_{K}\log{\tilde{p}_{K}}\\
\end{aligned}
$$

>   考虑到《缺失值》这一部分并不存在于大数据的幻灯片中，考不考我不好说……

对于第二个问题，我们是这样解决的。

对于在属性 $a$ 上有值的样本 $x$，我们将 $x$ 放入其对应的子节点中，并且其权重不变（$\omega_{x}$）。

对于属性 $a$ 缺失值的样本 $x$，我们将其放入所有子节点中，其权重变为 $\tilde{r}_{v}\ast\omega_{x}$。

>   经典不说人话（

看看幻灯片上的例子吧：

![Missing_Values_in_a_Decision_Tree_p1](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Missing_Values_in_a_Decision_Tree_p1.png)

![Missing_Values_in_a_Decision_Tree_p2](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Missing_Values_in_a_Decision_Tree_p2.png)

![Missing_Values_in_a_Decision_Tree_p3](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Missing_Values_in_a_Decision_Tree_p3.png)

![Missing_Values_in_a_Decision_Tree_p4](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Missing_Values_in_a_Decision_Tree_p4.png)

![Missing_Values_in_a_Decision_Tree_p5](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Missing_Values_in_a_Decision_Tree_p5.png)

![Missing_Values_in_a_Decision_Tree_p6](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Missing_Values_in_a_Decision_Tree_p6.png)

### 可解释性

决策树的可解释性非常好，因为它的每个节点都对应一个属性，每个分支都对应一个属性值，所以我们可以很容易地解释决策树的每个节点的含义。

不过，用决策树做 classification 的边界是与轴平行的。

![The_Classification_Boundary_of_Decision_Tree](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/The_Classification_Boundary_of_Decision_Tree.png)

因此，对于比较复杂的场景，这样的小片段（或者说小盒子）就会有很多。

决策树的优点：

1.  可以生成可理解的规则
2.  无需太多计算即可执行分类
3.  清楚地表明哪些属性对于预测或分类最重要
4.  （天然能）处理好矩形区域

缺点：

1.  决策树可能会遭受错误传播（Error Propagation）的影响
2.  自然，对于非矩形区域的分类效果不好

## SVM

SVM（Support Vector Machine）是一种监督学习算法，可以用于分类和回归问题。SVM 的基本模型是定义在特征空间上的间隔最大的线性分类器，间隔最大使得 SVM 对噪声的鲁棒性更好。

### 线性可分 SVM

原则：为了正确地对更靠近直线的新数据进行分类，直线与训练样本之间的最小距离应尽可能远。

![Support_Vector_and_Margin](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Support_Vector_and_Margin.png)

如图，将线性分类器的**间隔**（Margin）定义为在到达数据点之前边界可以增加的宽度。那两边的边界就是支持向量（Support Vector）。

显然，每个分类器都对应着一组 Margin 和支持向量，我们希望找到 Margin 最大的分类器。

------

以上的例子是在二维空间中说明的，不过样本的维度通常来说都不小，所幸 SVM 是可以推广到高维空间的。

定义一个超平面（hyperplane）：$\mathbf{w}^{T}\mathbf{x}+b=0$，其中 $\mathbf{w}$ 是法向量，$b$ 是偏置。

对于一个样本 $\mathbf{x_{i}}$，如果 $\mathbf{w}^{T}\mathbf{x_{i}}+b>0$，那么 $\mathbf{x_{i}}$ 属于正类；如果 $\mathbf{w}^{T}\mathbf{x_{i}}+b<0$，那么 $\mathbf{x_{i}}$ 属于负类。

------

我们设 Margin 为 $\gamma$，分类器为 $\mathbf{w}^{T}\mathbf{x}+b=0$，那么我们的目标就是使得 $\gamma$ 最大化。

同样地，支持向量在高维中也是超平面，如下图：

![Support_Vector_in_High_Dimension](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Support_Vector_in_High_Dimension.png)

我们将 plus plane 写作 $\mathbf{w}^{T}\mathbf{x}+b=c$，将 minus plane 写作 $\mathbf{w}^{T}\mathbf{x}+b=-c$。

这样，定义 $X^{-}$ 为 minus plane 上的点，$X^{+}$ 则为 plus plane 上距离 $X^{-}$ 最近的点。这个定义反过来写当然也是一样的效果。

我们说，$X^{+}=X^{-}+\alpha\mathbf{w}$ 成立，验证如下：

考虑 plus plane 上的点 $X^{+}$，那么：

$$
\mathbf{w}^{T}X^{+}+b=c
$$

同理，minus plane 上的点 $X^{-}$，有：

$$
\mathbf{w}^{T}X^{-}+b=-c
$$

我们希望找到一个向量 $\alpha\mathbf{w}$，使得 $X^{-}+\alpha\mathbf{w}$ 在 plus plane 上，也就是说，我们希望有：

$$
\mathbf{w}^{T}(X^{-}+\alpha\mathbf{w})+b=c
$$

展开，得到：

$$
(\mathbf{w}^{T}X^{-}+b)+\alpha\mathbf{w}^{T}\mathbf{w}=c
$$

带入 $\mathbf{w}^{T}X^{-}+b=-c$，得到：

$$
-c+\alpha\mathbf{w}^{T}\mathbf{w}=c
$$

解得：

$$
\begin{aligned}
\alpha&=\frac{2c}{\mathbf{w}^{T}\mathbf{w}}\\
&=\frac{2c}{||\mathbf{w}||^{2}}
\end{aligned}
$$

这就意味着，只要给定 $c$ 和 $\mathbf{w}$，我们总是可以找到一个 $\alpha$，使得 $X^{-}+\alpha\mathbf{w}$ 在 plus plane 上。

而我们要找的 Margin 就是 $X^{+}-X^{-}$，即：

$$
\begin{aligned}
\gamma&=||X^{+}-X^{-}||\\
&=||\alpha\mathbf{w}||\\
&=\alpha\cdot||\mathbf{w}||\\
&=\frac{2c}{||\mathbf{w}||}
\end{aligned}
$$

我们注意到，参数 $c$ 可以是任意的，因为我们总是能通过归一化把 $c$ 从式子中消去。因此，我们的目标就变成了最大化 $\frac{2}{||\mathbf{w}||}$。

我们注意到：

$$
\begin{aligned}
\mathbf{w}^{T}\mathbf{x_{i}}+b\gt 1\quad&\text{if}\, y_{i}=1\\
\mathbf{w}^{T}\mathbf{x_{i}}+b\lt -1\quad&\text{if}\, y_{i}=-1
\end{aligned}
$$

这样，我们就可以写出：

$$
y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)\ge 1
$$

我们原来的优化问题是：

$$
\max\limits_{\mathbf{w},b}\frac{2}{||\mathbf{w}||}\\
\text{s.t.}\quad y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)\ge 1
$$

它就转化成了：

$$
\min\limits_{\mathbf{w},b}\frac{1}{2}||\mathbf{w}||^{2}\\
\text{s.t.}\quad y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)\ge 1
$$

这里之所以是 $\frac{1}{2}||\mathbf{w}||^{2}$，是因为这样求导的时候会比较方便（据说），而且能消除模长天生自带的根号（据说）。

### Lagrange 对偶

>   建议直接跳到下文中的「求解对偶问题」，因为严谨的推导过程实在是又臭又长。头秃叻（
>
>   参考文档：https://brickexperts.github.io/2019/09/05/SVM%E8%A7%A3%E8%AF%BB(1)/

我们的优化问题是一个凸二次规划问题，可以用 Lagrange 乘子法求解。

**为什么要这么做**？

首先，这个最小化有个最简单的解，就是 $\mathbf{w}=0$，但其实这本质上是一个无效解，因为此时 $\mathbf{w}^{T}\mathbf{x}+b=0$ 无法再分开任何数据。也就是说，$\mathbf{w}$ **绝对不能**是一个零向量。我们希望 $y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)\ge 1$ 能被考虑其中，恰好，Lagrange 乘子法就能做到这一点。

**为什么可以**？

所谓“凸二次规划问题”，就是指目标函数是凸函数，约束条件是线性的。这样的问题，可以用 Lagrange 乘子法求解。

我们写出 Lagrange 函数：

$$
L(\mathbf{w},b,\alpha)=\frac{1}{2}||\mathbf{w}||^{2}-\sum\limits_{i=1}^{n}\alpha_{i}[y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)-1]\quad (\alpha_{i}\ge 0)
$$

其中，$\alpha_{i}$ 是 Lagrange 乘数。

**如何计算**？

我们希望 $L(\mathbf{w},b,\alpha)$ 对 $\alpha$ 最大化，对 $\mathbf{w}$ 和 $b$ 最小化。因此，我们的目标可以写成：

$$
\min\limits_{\mathbf{w},b}\max\limits_{\alpha}L(\mathbf{w},b,\alpha)
$$

首先，我们最大化 $L(\mathbf{w},b,\alpha)$，那么：

1.  当 $y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)\gt 1$ 时，函数中的 $\sum\limits_{i=1}^{n}\alpha_{i}[y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)-1]$ 为正。减去一个正值，意味着如果我想对 $L(\mathbf{w},b,\alpha)$ 最大化，那么 $\alpha_{i}$ 就应该为 $0$。
2.  当 $y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)\lt 1$ 时，函数中的 $\sum\limits_{i=1}^{n}\alpha_{i}[y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)-1]$ 为负。减去一个负值就是加上一个正值，意味着如果我想对 $L(\mathbf{w},b,\alpha)$ 最大化，那么 $\alpha_{i}$ 就应该趋近于无穷大。

注意到，如果 $\alpha_{i}$ 趋向于无穷大，那么我也别最小化了，重开吧；所以说，我们永远不能让 $y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)\lt 1$。我们发现，这恰好满足了我们的约束条件。

#### 从 Lagrange 函数到对偶问题

对于任何一个 Lagrange 函数，我们都可以写出对偶函数 $g(\alpha)$，只带有 Lagrange 乘数 $\alpha$ 作为唯一的变量。倘若 $L(x,a)$ 的最优解存在并且可以表示为 $\min{L(x,a)}$，那么 $g(\alpha)$ 的最优解也存在并且可以表示为 $\max{g(\alpha)}$。

于是有对偶差异：

$$
\Delta=\min\limits_{x}L(x,a)-\max\limits_{\alpha}g(\alpha)
$$

如果 $\Delta=0$，那么我们就说强对偶成立。此时，我们可以通过求解对偶问题来求解原问题。

#### KTT 条件

对于一个凸优化问题，如果存在最优解（或者干脆说强对偶关系存在），那么这个 Lagrange 函数一定满足 KTT 条件，即：

$$
\begin{aligned}
\frac{\partial L}{\partial x_{i}}=0\\
h_{i}(x)\le 0\\
\alpha_{i}\ge 0\\
\alpha_{i}h_{i}(x)=0
\end{aligned}
$$

这就是说：

1.  所有参数的一阶导数为 $0$
2.  约束条件中的函数本身要小于等于 $0$
3.  Lagrange 乘数要大于等于 $0$
4.  Lagrange 乘数和约束条件的函数的乘积为 $0$，也就是说，不论取什么值，我总要保证两者中至少有一个为 $0$

当这些条件被满足时，Lagrange 函数 $L(x,a)$ 的最优解与其对偶函数 $g(\alpha)$ 的最优解相等。这样，我们就可以通过求解对偶问题来求解原问题。

对 $L(\mathbf{w},b,\alpha)$ 求导，得到：

$$
\begin{aligned}
L(\mathbf{w},b,\alpha)&=\frac{1}{2}||\mathbf{w}||^{2}-\sum\limits_{i=1}^{n}\alpha_{i}[y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)-1]\\
\frac{\partial L}{\partial \mathbf{w}}&=\mathbf{w}-\sum\limits_{i=1}^{n}\alpha_{i}y_{i}\mathbf{x_{i}}=0\\
\frac{\partial L}{\partial b}&=-\sum\limits_{i=1}^{n}\alpha_{i}y_{i}=0
\end{aligned}
$$

所以：

$$
\begin{aligned}
\mathbf{w}&=\sum\limits_{i=1}^{n}\alpha_{i}y_{i}\mathbf{x_{i}}\\
0&=\sum\limits_{i=1}^{n}\alpha_{i}y_{i}
\end{aligned}
$$

并且，我们通过先求最大值再求最小值的方式，使得函数天然就满足：

$$
\begin{aligned}
-(y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)-1)&\le 0\\
\alpha_{i}&\ge 0\\
\end{aligned}
$$

所以只需要再满足：

$$
\alpha_{i}[(y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)-1)]=0
$$

这事好说，因为使得 $y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)-1=0$ 的就是支持向量，所有不在支持向量上的点，都必须满足 $\alpha_{i}=0$。

#### 化简

带入刚才得到的：

$$
\begin{aligned}
\mathbf{w}&=\sum\limits_{i=1}^{n}\alpha_{i}y_{i}\mathbf{x_{i}}\\
0&=\sum\limits_{i=1}^{n}\alpha_{i}y_{i}
\end{aligned}
$$

得到：

$$
\begin{aligned}
L(\mathbf{w},b,\alpha)&=\frac{1}{2}||\mathbf{w}||^{2}-\sum\limits_{i=1}^{n}\alpha_{i}[y_{i}(\mathbf{w}\mathbf{x_{i}}+b)-1]\\
&=\frac{1}{2}||\mathbf{w}||^{2}-\sum\limits_{i=1}^{n}\alpha_{i}y_{i}\mathbf{w}^{T}\mathbf{x_{i}}-\sum\limits_{i=1}^{n}\alpha_{i}y_{i}b+\sum\limits_{i=1}^{n}\alpha_{i}\\
&=\frac{1}{2}||\mathbf{w}||^{2}-w^{T}w+\sum\limits_{i=1}^{n}\alpha_{i}\\
&=-\frac{1}{2}w^{T}w+\sum\limits_{i=1}^{n}\alpha_{i}
\end{aligned}
$$

再代入，转换为内积形式，得到对偶式：

$$
L_{d}=\sum\limits_{i=1}^{n}\alpha_{i}-\frac{1}{2}\sum\limits_{i=1}^{n}\sum\limits_{j=1}^{n}\alpha_{i}\alpha_{j}y_{i}y_{j}\mathbf{x_{i}}\cdot\mathbf{x_{j}}
$$

因此：

$$
\Delta=\min\limits_{\mathbf{w},b}\max\limits_{\alpha_{i}\ge 0}L(\mathbf{w},b,\alpha)-\max\limits_{\alpha_{i}\ge 0}L_{d}
$$

#### 求解对偶问题

由于满足所有 KKT 条件，所以：

$$
\min\limits_{\mathbf{w},b}\max\limits_{\alpha_{i}\ge 0}L(\mathbf{w},b,\alpha)=\max\limits_{\alpha_{i}\ge 0}\min\limits_{\mathbf{w},b}L(\mathbf{w},b,\alpha)
$$

这样，我们只需要求解对偶函数的最大值即可。最终，目标函数变为：

$$
\max\limits_{\alpha_{i}\ge 0}(\sum\limits_{i=1}^{n}\alpha_{i}-\frac{1}{2}\sum\limits_{i=1}^{n}\sum\limits_{j=1}^{n}\alpha_{i}\alpha_{j}y_{i}y_{j}\mathbf{x_{i}}\cdot\mathbf{x_{j}})
$$

然后么，不管是梯度下降，还是 SMO 算法，还是什么 QP（Quadratic Programming，二次规划）都可以求解 $\alpha$。求得了 $\alpha$，我们就可以求得 $\mathbf{w}$ 和 $b$，然后就可以求得分类器了。

鉴于幻灯片中给了 SMO 算法，所以提一下。

#### SMO 算法

SMO（Sequential Minimal Optimization）算法是一种启发式算法，用于求解二次规划问题。

SMO 算法的思想是：每次循环选择两个变量（比如 $\alpha_{i}$ 和 $\alpha_{j}$），固定其他变量，针对这两个变量构建一个二次规划问题，这个二次规划问题的解应该更接近原始二次规划问题的解。这样，通过多次循环，就可以得到原始二次规划问题的解。

### 核函数

我们之前的讨论都是在线性可分的情况下进行的，但是实际上，很多时候数据是线性不可分的。这时，我们就需要引入核函数（Kernel Function），将数据映射到高维空间中，使得数据线性可分。

这时候，对偶优化问题就变成了：

$$
\max\limits_{\alpha}{L(\alpha)}=\sum\limits_{i=1}^{n}\alpha_{i}-\frac{1}{2}\sum\limits_{i=1}^{n}\sum\limits_{j=1}^{n}\alpha_{i}\alpha_{j}y_{i}y_{j}\phi(\mathbf{x_{i}})^{T}\phi(\mathbf{x_{j}})\\
\text{s.t.}\quad\sum\limits_{i=1}^{n}\alpha_{i}y_{i}=0\\
$$

其中，$\phi(\mathbf{x_{i}})$ 是将 $\mathbf{x_{i}}$ 映射到高维空间的函数。

计算特征空间中特征向量的内积可能成本很高，因为它是高维的。所以：

$$
k(\mathbf{x_{i}},\mathbf{x_{j}})=\phi(\mathbf{x_{i}})^{T}\phi(\mathbf{x_{j}})
$$

我们把 $k(\mathbf{x_{i}},\mathbf{x_{j}})$ 称为核函数（Kernel Function），如此一来，我们只要在原始空间中计算核函数的值就可以了。

**例子**

![An_Example_of_Kernel_Function_in_SVM](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/An_Example_of_Kernel_Function_in_SVM.png)

总之呢，只要我们能够计算特征空间中的内积，我们就不需要显式地进行映射。

------

Mercer 定理：如果一个函数 $k(\mathbf{x_{i}},\mathbf{x_{j}})$ 是一个对称函数，那么它就是一个（Mercer）核函数，当且仅当对于任意的 $n$ 和 $\mathbf{x_{1}},\mathbf{x_{2}},\ldots,\mathbf{x_{n}}$，矩阵 $K$ 是对称半正定的。

其中矩阵 $K$ 是：

$$
K=\begin{bmatrix}
k(\mathbf{x_{1}},\mathbf{x_{1}})&k(\mathbf{x_{1}},\mathbf{x_{2}})&\cdots&k(\mathbf{x_{1}},\mathbf{x_{n}})\\
k(\mathbf{x_{2}},\mathbf{x_{1}})&k(\mathbf{x_{2}},\mathbf{x_{2}})&\cdots&k(\mathbf{x_{2}},\mathbf{x_{n}})\\
\vdots&\vdots&\ddots&\vdots\\
k(\mathbf{x_{n}},\mathbf{x_{1}})&k(\mathbf{x_{n}},\mathbf{x_{2}})&\cdots&k(\mathbf{x_{n}},\mathbf{x_{n}})\\
\end{bmatrix}
$$

-----

常用的核函数有：

-   线性核函数：$k(\mathbf{x_{i}},\mathbf{x_{j}})=\mathbf{x_{i}}^{T}\mathbf{x_{j}}$
-   多项式核函数：$k(\mathbf{x_{i}},\mathbf{x_{j}})=(\mathbf{x_{i}}^{T}\mathbf{x_{j}}+1)^{p}$
-   Gaussian 核函数：$k(\mathbf{x_{i}},\mathbf{x_{j}})=\exp(-\frac{||\mathbf{x_{i}}-\mathbf{x_{j}}||^{2}}{2\sigma^{2}})$
-   Sigmoid 核函数：$k(\mathbf{x_{i}},\mathbf{x_{j}})=\tanh(\beta\mathbf{x_{i}}^{T}\mathbf{x_{j}}+\theta)$

### 软间隔

数据带有噪声，我们往往要允许一些数据点被误分类。这时，我们就需要引入软间隔（Soft Margin）。

本质上，是说这些点不满足约束条件 $y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)\ge 1$。

引入 loss 函数：

$$
l_{0/1}(x)=\begin{cases}
1&\text{if}\, x\lt 0\\
0&\text{otherwise}
\end{cases}
$$

最优化目标：

$$
\min\limits_{\mathbf{w},b}\frac{1}{2}||\mathbf{w}||^{2}+C\sum\limits_{i=1}^{n}l_{0/1}(y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)-1)
$$

其中，$C$ 是一个超参数，用于控制误分类点的惩罚程度。这使得，前半部分仍然是最大化 Margin，而后半部分则是最小化误分类点的个数。当 $C>0$ 时，它是一个 tradeoff 参数；当 $C\to\infty$ 时，它是一个 hard margin SVM。

然而，$l_{0/1}(x)$ 是一个不凸、不连续的函数，不利于求解。因此，我们引入 hinge loss 函数：

$$
l_{hinge}(x)=\max(0,1-x)
$$

![Surrogate_Loss_Function_in_SVM](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Surrogate_Loss_Function_in_SVM.png)

施加 hinge loss 函数后，我们的最优化目标变为：

$$
\min\limits_{\mathbf{w},b}\frac{1}{2}||\mathbf{w}||^{2}+C\sum\limits_{i=1}^{n}\max(0,1-y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b))
$$

我们引入松弛变量（Slack Variable） $\xi_{i}$，使得：

$$
\xi_{i}=\max(0,1-y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b))
$$

这样，我们的最优化目标就变为：

$$
\min\limits_{\mathbf{w},b}\frac{1}{2}||\mathbf{w}||^{2}+C\sum\limits_{i=1}^{n}\xi_{i}\\
\text{s.t.}\quad y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)\ge 1-\xi_{i}\\
\xi_{i}\ge 0
$$

每个 $\xi_{i}$ 都对应着一个数据点，它的值表示这个数据点的分类是否正确。如果分类正确，那么 $\xi_{i}=0$；如果分类错误，那么 $\xi_{i}>0$。换言之，$\xi_{i}$ 表示第 $i$ 个数据点的分类错误的程度。

写出 Lagrange 函数：

$$
L(\mathbf{w},b,\xi,\alpha,\mu)=\frac{1}{2}||\mathbf{w}||^{2}+C\sum\limits_{i=1}^{n}\xi_{i}-\sum\limits_{i=1}^{n}\alpha_{i}[y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)-1+\xi_{i}]-\sum\limits_{i=1}^{n}\mu_{i}\xi_{i}
$$

其中，$\alpha_{i}$ 和 $\mu_{i}$ 是 Lagrange 乘数。

分别求导，使之为零，得到：

$$
\begin{aligned}
\frac{\partial L}{\partial \mathbf{w}}&=\mathbf{w}-\sum\limits_{i=1}^{n}\alpha_{i}y_{i}\mathbf{x_{i}}=0\\
\frac{\partial L}{\partial b}&=-\sum\limits_{i=1}^{n}\alpha_{i}y_{i}=0\\
\frac{\partial L}{\partial \xi_{i}}&=C-\alpha_{i}-\mu_{i}=0
\end{aligned}
$$

在新约束条件下的对偶问题为：

$$
\max\limits_{\alpha}\sum\limits_{i=1}^{n}\alpha_{i}-\frac{1}{2}\sum\limits_{i=1}^{n}\sum\limits_{j=1}^{n}\alpha_{i}\alpha_{j}y_{i}y_{j}\mathbf{x_{i}}^{T}\mathbf{x_{j}}\\
\text{s.t.}\quad\sum\limits_{i=1}^{n}\alpha_{i}y_{i}=0\\
0\le\alpha_{i}\le C
$$

这与线性可分离情况下的优化问题非常相似，只是现在 $a_{i}$ 上有一个上限 $C$。然后也没啥，接着 SMO 求解 $\alpha_{i}$ 就完了。

它的 KKT 条件是：

$$
\begin{aligned}
\alpha_{i}\ge 0\\
\mu_{i}\ge 0\\
y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)-1+\xi_{i}\ge 0\\
\alpha_{i}[y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)-1+\xi_{i}]=0\\
\xi_{i}\ge 0\\
\mu_{i}\xi_{i}=0
\end{aligned}
$$

我们发现，对于每一个数据点 $(x_{i},y_{i})$，都有 $a_{i}=0$ 或者 $y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)=1-\xi_{i}$。

如果 $a_{i}=0$，那么该点对于 $\mathbf{w}$ 和 $b$ 没有任何影响。

如果 $a_{i}>0$，那么该点一定是一个支持向量。这种情况下，讨论 $a_{i}$ 的取值范围：

1.  $0<a_{i}<C$，那么 $\mu_{i}>0$，$\xi_{i}=0$，该点就在 Margin 上
2.  $a_{i}=C$，那么 $\mu_{i}=0$。倘若 $\xi_{i}\le 1$，那么该点就在 Margin 内部；倘若 $\xi_{i}\gt 1$，那么该点就是一个误分类点

### SVR

支持向量回归（Support Vector Regression，SVR）是一种使用支持向量机（SVM）进行回归任务的方法。与传统的回归方法不同，SVR通过在训练数据中找到一个边界（支持向量），来建立一个能够尽可能包含大部分训练样本的函数。

SVR的目标是找到一个函数，使得训练样本与函数的预测值之间的差异最小化。与分类问题中的SVM不同，SVR的目标不是完全分离样本，而是在一定的容忍度内尽量拟合样本。

SVR的基本思想是将回归问题转化为一个优化问题。给定训练样本 $(\mathbf{x}_i, y_i)$，其中 $\mathbf{x}_i$ 是输入特征，$y_i$ 是对应的目标值。SVR的优化目标是最小化预测值与实际值之间的误差，并尽量使得误差在一个容忍度内。

具体来说，SVR的优化问题可以表示为：

$$
\min_{\mathbf{w}, b, \xi, \xi^*} \frac{1}{2}||\mathbf{w}||^2 + C\sum_{i=1}^{N} (\xi_i + \xi_i^*)
$$

其中，$\mathbf{w}$ 是回归函数的权重向量，$b$ 是偏置项，$\xi_i$ 和 $\xi_i^*$ 是松弛变量，$C$ 是正则化参数。这个优化问题的目标是最小化权重向量的范数和误差项，并通过松弛变量来容忍一定的误差。

SVR的约束条件可以表示为：

$$
\begin{aligned}
y_i - \mathbf{w}^T \mathbf{x}_i - b &\leq \epsilon + \xi_i \\
\mathbf{w}^T \mathbf{x}_i + b - y_i &\leq \epsilon + \xi_i^* \\
\xi_i, \xi_i^* &\geq 0
\end{aligned}
$$

其中，$\epsilon$ 是容忍度，用于控制预测值与实际值之间的最大误差。

通过求解这个优化问题，可以得到最优的权重向量 $\mathbf{w}$ 和偏置项 $b$，从而得到SVR模型。在预测阶段，SVR使用学习到的模型来预测新样本的目标值。

### 题目

从幻灯片上摘的，我估摸着大致得会这个。

Q：试着自己推导，从：

$$
\begin{aligned}
\min\limits_{\mathbf{w},b}\frac{1}{2}||\mathbf{w}||^{2}\\
\text{s.t.}\quad y_{i}(\mathbf{w}^{T}\mathbf{x_{i}}+b)\ge 1
\end{aligned}
$$

到：

$$
\begin{aligned}
\max\limits_{\alpha}L(\alpha)=\sum\limits_{i=1}^{n}\alpha_{i}&-\frac{1}{2}\sum\limits_{i=1}^{n}\sum\limits_{j=1}^{n}\alpha_{i}\alpha_{j}y_{i}y_{j}\mathbf{x_{i}}^{T}\mathbf{x_{j}}\\
\text{s.t.}\quad\sum\limits_{i=1}^{n}\alpha_{i}y_{i}&=0\\
\alpha_{i}&\ge 0
\end{aligned}
$$

A：我特么……明天再写。先 Mark 一下。

## KNN

KNN 根据数据集与邻居的相似性对数据集进行分类。

KNN 算法的基本思想是：如果一个样本在特征空间中的 $k$ 个最相似（即特征空间中最邻近）的样本中的大多数属于某一个类别，则该样本也属于这个类别。

与其他监督学习算法不同，KNN 不会从训练数据中学习显式映射 $f$，而是直接利用训练数据进行预测。

给定训练数据集 $D=\{(\mathbf{x}_1, y_1), (\mathbf{x}_2, y_2), \ldots, (\mathbf{x}_m, y_m)\}$，其中 $\mathbf{x}_i \in \mathcal{X} \subseteq \mathbb{R}^n$，$y_i \in \mathcal{Y} = \{c_1, c_2, \ldots, c_k\}$，$i=1,2,\ldots,m$，$k$ 是类别的个数。对于新的样本 $\mathbf{x}$，KNN 的目标是找到与 $\mathbf{x}$ 最近的 $k$ 个样本，然后根据这 $k$ 个样本的类别来预测 $y$ 的值。

对于 classification 任务，KNN 的输出形式是一个离散变量，表示样本的类别。对于 regression 任务，KNN 的输出形式是一个连续变量，表示样本的数值。

KNN 需要：参数 $K$，以及距离度量方法（其实就是 Distance Function）。

### 距离度量

KNN 算法中**最常用**的距离度量方法是欧氏距离（Euclidean Distance），它是样本特征向量的欧氏距离。

即，对于 $\mathbf{p}=(p_1, p_2, \ldots, p_n)$ 和 $\mathbf{q}=(q_1, q_2, \ldots, q_n)$，它们之间的欧氏距离为：

$$
d(\mathbf{p}, \mathbf{q}) = \sqrt{\sum_{i=1}^{n} (p_i - q_i)^2}
$$

除了欧氏距离，KNN 还可以使用其他距离度量方法，比如 Manhattan 距离。

>   其实就是数格子

$$
d(\mathbf{p}, \mathbf{q}) = ||\mathbf{p}-\mathbf{q}|| = \sum_{i=1}^{n} |p_i - q_i|
$$

------

Minkowski 距离是欧氏距离和 Manhattan 距离的一般化。

$$
d(\mathbf{p}, \mathbf{q}) = \sqrt[m]{\sum_{i=1}^{n} |p_i - q_i|^m}
$$

这个公式是 $m$ 阶 Minkowski 距离的计算式。当 $m=1$ 时，Minkowski 距离等价于 Manhattan 距离；当 $m=2$ 时，Minkowski 距离等价于欧氏距离。

当 $m\to\infty$ 时，Minkowski 距离等价于 Chebyshev 距离。

>   虽然没提 Chebyshev 距离，但是简单说说。Chebyshev 距离是一种用于衡量两个点之间的距离的度量方式。它基于点之间在各个坐标轴上的最大差异。
>
>   给定两个点 $P=(x_1, y_1)$ 和 $Q=(x_2, y_2)$，它们在二维平面上的Chebyshev距离可以表示为：
>
>   $$
>   D_{\text{Chebyshev}}(P, Q) = \max(|x_2 - x_1|, |y_2 - y_1|)
>   $$
>
>   可以将Chebyshev距离推广到更高维度的情况。对于点 $P=(x_1, y_1, z_1)$ 和 $Q=(x_2, y_2, z_2)$ 在三维空间中的情况，Chebyshev距离可以表示为：
>
>   $$
>   D_{\text{Chebyshev}}(P, Q) = \max(|x_2 - x_1|, |y_2 - y_1|, |z_2 - z_1|)
>   $$
>
>   在一般情况下，对于点 $P=(x_1, x_2, \ldots, x_n)$ 和 $Q=(y_1, y_2, \ldots, y_n)$ 在$n$维空间中的情况，Chebyshev距离可以表示为：
>
>   $$
>   D_{\text{Chebyshev}}(P, Q) = \max(|x_1 - y_1|, |x_2 - y_2|, \ldots, |x_n - y_n|)
>   $$

**余弦相似度**

余弦相似度是内积空间的两个非零向量之间相似度的度量，用于测量它们之间角度的余弦。

$$
\text{Similarity} = \cos(\theta) = \frac{\mathbf{A}\cdot\mathbf{B}}{||\mathbf{A}||\cdot||\mathbf{B}||} = \frac{\sum_{i=1}^{n} A_iB_i}{\sqrt{\sum_{i=1}^{n} A_i^2}\sqrt{\sum_{i=1}^{n} B_i^2}}
$$

------

**Hamming 距离**

Hamming 距离度量了两个 features 之间的差异。直接看例子就懂了：

```markdown
1011101
1001001
```

可见差异为 $2$，所以 Hamming 距离为 $2$。

```markdown
2173896
2233796
```

可见差异为 $3$，所以 Hamming 距离为 $3$。

换而言之，Hamming 距离等价于将一个 feature 变换成另一个 feature 所需要替换的字符个数。

### 归一化

对于 KNN 而言，features 之间必须要进行归一化。

一种方法是：将 $p_{i}$ 用 $z_{im}=\frac{x_{im}-\bar{x}_{m}}{\sigma_{m}}$ 替换，其中 $\bar{x}_{m}$ 是第 $m$ 个 feature 的均值，$\sigma_{m}$ 是第 $m$ 个 feature 的标准差。

$$
\bar{x}_{m}=\frac{1}{n}\sum\limits_{i=1}^{n}x_{im}\\
\sigma_{m}=\sqrt{\frac{1}{n}\sum\limits_{i=1}^{n}(x_{im}-\bar{x}_{m})^{2}}
$$

我们使得每个 feature 的均值为 $0$，标准差为 $1$。

### 带权

由于 feature 间并不同等重要，因此我们可以对 feature 进行加权缩放。

$$
d(\mathbf{p}, \mathbf{q}) = \sqrt{\sum_{i=1}^{n} w_i(p_i - q_i)^2}
$$

其中，$w_i$ 是第 $i$ 个 feature 的权重。这个权重可以作为先验知识直接给出，也可以通过交叉验证（cross-validation）学习得到（后者幻灯片明说了 not covered，那就不管）。

### K 的选择

理论上，如果可用样本数量无限，$K$ 越大，分类效果越好，因为用上了更多的信息。然而在实践中，这肯定是不可能的，且不说计算量，就算是计算量可以接受，数据集也不可能无限大。

当 $K=1$ 时，KNN 可以看作是一个最近邻分类器（Nearest Neighbor Classifier）。这时，KNN 的分类结果对噪声非常敏感，因为它只考虑了一个最近邻。

1NN 有时候可以用于可视化（Visualization）。譬如Voronoi Diagram（维诺图）。Voronoi 图将空间分为一个一个的区域，每个区域内的点都离它的最近邻最近。

------

较小的 $K$：

-   为每个类别创建许多小区域
-   噪声敏感
-   决策边界可能不够平滑
-   可能造成过拟合

较大的 $K$：

-   为每个类别创建较少的大区域
-   通常可以得到更平滑的决策边界
-   可以减少 class 间的 label 噪声
-   可能造成欠拟合（这就是由太过平滑的决策边界造成的）

一般来说，$K$ 是奇数，这样可以避免平票的情况。

### 留出法

留出法（Hold-Out）是最简单的交叉验证方法。它将数据集 $D$ 划分为两个互斥的集合，一个作为训练集 $S$，一个作为测试集 $T$。在 $S$ 上训练出模型后，用 $T$ 来评估其测试误差，作为对泛化误差的估计。

比如说，我将数据集分为 $80\%$ 的训练集和 $20\%$ 的测试集。然后我用训练集训练模型，用测试集测试模型。

可以使用 Hold-Out 方法来选择最优的 $K$。一旦模型确定，我们就可以在测试集上评估模型的泛化误差。

Hold-Out 方法的缺点是：只使用了一部分数据来训练模型，这样会造成模型的训练效果不够好。而且，可能会造成数据的分布变化，因此为了保证数据分布的一致性，还是分层采样比较好。

### K-Fold 交叉验证

K-Fold 交叉验证是将数据集 $D$ 划分为 $k$ 个大小相似的互斥子集，每个子集都尽可能保持数据分布的一致性。然后，每次用 $k-1$ 个子集的并集作为训练集，余下的那个子集作为测试集，这样就可以获得 $k$ 组训练/测试集，从而可以进行 $k$ 次训练和测试，最终返回的是 $k$ 个测试结果的均值。

![K-Fold_Cross_Validation](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/K-Fold_Cross_Validation.png)

### 留一法

留一法（Leave-One-Out）是 K-Fold 交叉验证的特殊情况，即 $k=n$。这时，每个子集都只包含一个样本，这样就得到了 $n$ 组训练/测试集，从而可以进行 $n$ 次训练和测试，最终返回的是 $n$ 个测试结果的均值。

$$
\begin{aligned}
&\text{For k=}1,2,\cdots,K\\
&\quad\quad\text{err}(k)=0\\
&\quad\quad\text{For i=}1,2,\cdots,n\\
&\quad\quad\quad\quad\text{用剩余数据对}\mathbf{x}_{i}\text{预测标签}\hat{y}_{i}\\
&\quad\quad\quad\quad\text{err}(k)=\text{err}(k)+I(\hat{y}_{i}\ne y_{i})\\
\end{aligned}
$$

输出：

$$
k^{\ast}=\arg\min\limits_{1\le k\le K}\text{err}(k)
$$

### 维度灾难

维度灾难（Curse of Dimensionality）是指在高维空间中，数据变得非常稀疏，这会导致距离度量失效，从而影响 KNN 的分类效果。

高维时会有平时在低维空间中不会出现的问题，如：

-   存储复杂度
-   计算复杂度
-   采样困难
-   最近邻的搜索
-   非参数估计
-   etc.

### 复杂度优化

有用于降低复杂性的各种精确和近似方法。

对于计算复杂度，可以使用更好的数据结构，比如 KD-Tree；此外，还可以使用近似最近邻（Approximate Nearest Neighbor，ANN）算法、局部敏感哈希（Locality Sensitive Hashing，LSH）等。

#### KD-Tree

KD-Tree（K-Dimensional Tree）是一种用于高效存储和检索$k$维数据的数据结构。它是一种二叉树，每个节点代表一个$k$维数据点，并根据数据点在每个维度上的值将空间划分为两个子空间。KD-Tree的构建过程基于递归地选择一个维度和一个切分值，将数据点分配到左右子树中。

下面是KD-Tree的构建过程的简要描述：

1. 选择一个维度：从$k$个维度中选择一个维度作为切分维度。通常是按照某种规则选择，比如轮流选择或选择方差最大的维度。

2. 选择一个切分值：在选择的切分维度上，选择一个切分值，将数据点划分到左右子树中。切分值可以是中位数、平均值或其他选择方法。

3. 划分数据点：将数据点根据切分值划分到左右子树中。所有小于等于切分值的数据点分配到左子树，大于切分值的数据点分配到右子树。

4. 递归构建子树：对左右子树递归地执行以上步骤，直到每个叶节点只包含一个数据点或没有数据点。

构建完成后，KD-Tree可以用于高效地执行各种操作，如最近邻搜索、范围搜索和近似最近邻搜索。这些操作利用了KD-Tree的结构特点，通过避免不必要的搜索来提高效率。

最近邻搜索是KD-Tree中最常见的操作之一。给定一个查询点，KD-Tree可以快速找到与查询点最近的数据点。搜索过程从根节点开始，根据查询点在当前维度上与切分值的关系，选择进入左子树或右子树。然后，递归地在选择的子树中执行相同的搜索过程，直到找到最近的数据点或搜索完整个树。

范围搜索是另一个常见的操作，用于找到在给定范围内的所有数据点。搜索过程类似于最近邻搜索，但在每个节点上需要考虑查询范围与切分超平面的关系，以确定是否需要进入左子树或右子树。

反正问就是好好好（x

对于切分有困惑的话，可以看幻灯片中的二维 KD-Tree 构建样例。

KD-Tree 执行最近邻搜索之所以快，是因为它可以避免搜索不必要的子树。这有点像高维度中的二分查找，我是这样理解的。

不过，KD-Tree 不适合在**极高维**空间中有效地查找最近邻。因为此时，有太多分支需要回溯，使得开销接近线性时间。

复杂度也许没必要了解吧……

### 总结

优点：

-   简单直观，训练非常快，易于实施
-   特别适合多分类问题
-   凭借无限的训练数据和足够大的 $K$，KNN 方法效果很好

缺点：

-   对噪声特征敏感
-   即使在测试时也将所有训练数据存储在内存中
-   查询时速度慢：每个测试点的计算量为 O(nd)
-   高维时会有维度灾难

## 回归学习

牢记 classification 任务的输出是离散的，而 regression 任务的输出是连续的。

一些典型案例：

|  Regression  | Classification |
| :----------: | :------------: |
|   房价预测   |    性别分类    |
| 作物产量预测 |    电影种类    |
|   身高预测   |    好瓜坏瓜    |

回归学习的目标是学习一个预测函数 $f$，使得 $f(\mathbf{x})$ 尽可能接近 $y$；同时，对于新的输入 $\mathbf{x}$，$f(\mathbf{x})$ 可以预测出相应的输出 $y$。至于这个函数，可以是线性的，也可以是非线性的。

![Regression_Task_with_Linear_and_Non_Linear_Function](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Regression_Task_with_Linear_and_Non_Linear_Function.png)

回归是一个监督学习问题。之所以这么说，是因为我们需要有标签 $y$ 来指导我们的学习过程。

根据输入值的个数，回归学习可以分为一元回归（Univariate Regression）和多元回归（Multivariate Regression）；根据预测函数的形式，回归学习可以分为线性回归（Linear Regression）和非线性回归（Nonlinear Regression）。

### 线性回归

幻灯片的例子：

$$
\begin{aligned}
\text{二维：}\quad&y_{\text{房价}}=w_{1}\cdot x_{\text{面积}}+b\\
\text{三维：}\quad&y_{\text{房价}}=w_{1}\cdot x_{\text{面积}}+w_{2}\cdot x_{\text{楼层}}+b\\
\text{多维：}\quad&y_{\text{房价}}=w_{1}\cdot x_{1}+w_{2}\cdot x_{2}+\cdots+w_{n}\cdot x_{n}+b
\end{aligned}
$$

所以总是可以写成：

$$
\mathbf{y}=f(\mathbf{x})=\mathbf{w}^{T}\mathbf{x}+b
$$

其中，$\mathbf{w}=(w_{1}, w_{2}, \ldots, w_{n})^{T}$ 是权重向量，$b$ 是偏置项。

一定要牢记，在给定数据集的情况下，我们任务就是求出最优的 $\mathbf{w}$ 和 $b$，使得预测值 $f(\mathbf{x})$ 尽可能接近真实值 $y$。

### 最小二乘法

在回归任务中，经常使用最小二乘法（Least Square Method）。

定义误差：

$$
\text{err}=\sum\limits_{i=1}^{m}(y_{i}-f(\mathbf{x}_{i}))^{2}
$$

这其中，$m$ 是数据集的大小，$y_{i}$ 是第 $i$ 个样本的真实值，$f(\mathbf{x}_{i})$ 是第 $i$ 个样本的预测值。

那么，最小二乘法的目标就是：

$$
\min\limits_{\mathbf{w},b}\text{err}=\sum\limits_{i=1}^{m}(y_{i}-f(\mathbf{x}_{i}))^{2}
$$

注意，我们的 model 为：

$$
f(\mathbf{x})=\mathbf{w}^{T}\mathbf{x}+b
$$

Loss function 为：

$$
\text{Loss}=\sum\limits_{i=1}^{m}(y_{i}-f(\mathbf{x}_{i}))^{2}
$$

如何解出最优的 $\mathbf{w}$ 和 $b$，使得 Loss 最小呢？

我们可以对 $\mathbf{w}$ 和 $b$ 分别求偏导，然后令其为 $0$，从而得到最优解。

$$
\begin{aligned}
\frac{\partial\text{Loss}}{\partial\mathbf{w}}&=2(w\sum\limits_{i=1}^{m}x_{i}^{2}-\sum\limits_{i=1}^{m}(y_{i}-b)x_{i})=0\\
\frac{\partial\text{Loss}}{\partial b}&=\sum\limits_{i=1}^{m}2(y_{i}-f(\mathbf{x}_{i}))(-1)=0
\end{aligned}
$$

解得：

$$
\begin{aligned}
\mathbf{w}&=\frac{\sum\limits_{i=1}^{m}y_{i}(x_{i}-\bar{x})}{\sum\limits_{i=1}^{m}x_{i}^{2}-\frac{1}{m}(\sum\limits_{i=1}^{m}x_{i})^{2}}\\
b&=\frac{1}{m}\sum\limits_{i=1}^{m}(y_{i}-\mathbf{w}^{T}\mathbf{x}_{i})
\end{aligned}
$$

其中，$\bar{x}=\frac{1}{m}\sum\limits_{i=1}^{m}x_{i}$。

对于多元回归，我们可以将 $\mathbf{x}$ 看作是一个向量，$\mathbf{w}$ 也是一个向量，那么就有：

模型：

$$
f(\mathbf{x})=\mathbf{X}\hat{\mathbf{w}}
$$

其中，$\mathbf{X}$ 是一个 $m\times n$ 的矩阵。

Loss function：

$$
\text{Loss}=(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})^{T}(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})
$$

其中，$\hat{\mathbf{w}}=(\mathbf{w}, b)^{T}$；

$$
\mathbf{X}=\begin{bmatrix}
x_{11}&x_{12}&\cdots&x_{1n}&1\\
x_{21}&x_{22}&\cdots&x_{2n}&1\\
\vdots&\vdots&\ddots&\vdots&\vdots\\
x_{m1}&x_{m2}&\cdots&x_{mn}&1
\end{bmatrix}
=\begin{bmatrix}
\mathbf{x}_{1}^{T}&1\\
\mathbf{x}_{2}^{T}&1\\
\vdots&\vdots\\
\mathbf{x}_{m}^{T}&1
\end{bmatrix}
$$

而 $\mathbf{y}$ 是一个 $m\times 1$ 的向量。

对 $\hat{\mathbf{w}}$ 求偏导，令其为 $0$，解得：

$$
\hat{\mathbf{w}}=(\mathbf{X}^{T}\mathbf{X})^{-1}\mathbf{X}^{T}\mathbf{y}
$$

Tips：向量求导公式

$$
\begin{aligned}
\frac{\partial\mathbf{X}\theta}{\partial\theta}&=\mathbf{X}^{T}\\
\frac{\partial\theta^{T}\mathbf{X}}{\partial\theta}&=\mathbf{X}\\
\frac{\partial\theta^{T}\mathbf{X}\theta}{\partial\theta}&=\mathbf{X}\theta+\mathbf{X}^{T}\theta=\theta(\mathbf{X}+\mathbf{X}^{T})\\
\end{aligned}
$$

不过，最小二乘法的损失函数有可能会造成模型**过拟合**。

**缺点**：

回顾解析解：

$$
\hat{\mathbf{w}}^{\ast}=(\mathbf{X}^{T}\mathbf{X})^{-1}\mathbf{X}^{T}\mathbf{y}
$$

解析解中的 $(\mathbf{X}^{T}\mathbf{X})^{-1}$ 有可能不是满秩的，这时就无法求逆了，即 $(\mathbf{X}^{T}\mathbf{X})^{-1}$ 不存在。这就是说，样本的特征数大于样本的个数，这样，$\hat{\mathbf{w}}$ 就会有多个解。

所以说，想要应用最小二乘法，要求已有样本中的值比较准确，且样本的特征数小于样本的个数。

### Ridge 回归与 Lasso 回归

先说，岭回归（Ridge Regression）和下面要说的 Lasso 回归（Lasso Regression）都是为了解决最小二乘法的缺点而提出的，它们都属于线性回归。

Model:

$$
f(\mathbf{x})=\mathbf{w}^{T}\mathbf{x}+b
$$

Loss function:

$$
\begin{aligned}
\text{朴素线性回归}\quad&\text{Loss}=(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})^{T}(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})\\
\text{Ridge 回归}\quad&\text{Loss}=(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})^{T}(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})+\lambda||\mathbf{w}||_{2}^{2}\,(\lambda\gt 0)\\
\text{Lasso 回归}\quad&\text{Loss}=(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})^{T}(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})+\lambda||\mathbf{w}||_{1}\,(\lambda\gt 0)
\end{aligned}
$$

其中：

- $\mathbf{y}$ 是目标变量（或因变量）的向量，包含了观测到的实际值。
- $\mathbf{X}$ 是自变量（或特征）的矩阵，包含了观测到的特征值。
- $\hat{\mathbf{w}}$ 是模型的参数向量，用于拟合自变量和目标变量之间的关系。
- $\lambda$ 是正则化参数，用于控制正则化项的强度。

现在我们来逐个解释这些公式：

1. 朴素线性回归（Ordinary Least Squares, OLS）：
   朴素线性回归使用最小二乘法来拟合数据。损失函数是目标变量与预测变量之间的差异的平方和。公式中的 $(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})^{T}(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})$ 表示目标变量与预测变量之间的残差平方和。

2. Ridge 回归：
   Ridge 回归是一种带有L2正则化的线性回归方法。它在最小二乘损失函数的基础上添加了正则化项，以限制参数的大小。正则化项 $||\mathbf{w}||_{2}^{2}$ 是参数向量的L2范数的平方。通过引入正则化项，Ridge 回归可以减小参数的方差，从而降低过拟合的风险。正则化参数 $\lambda$ 控制正则化项的强度，较大的 $\lambda$ 值会增加正则化的影响。

3. Lasso 回归：
   Lasso 回归是一种带有L1正则化的线性回归方法。与Ridge回归类似，Lasso回归也在最小二乘损失函数的基础上添加了正则化项。不同之处在于，Lasso回归使用参数向量的L1范数作为正则化项，即 $||\mathbf{w}||_{1}$。L1正则化倾向于产生稀疏解，即将某些参数置为零，从而实现特征选择的效果。正则化参数 $\lambda$ 控制正则化项的强度，较大的 $\lambda$ 值会增加正则化的影响。

Ridge 回归和 Lasso 回归可以防止模型过拟合，也可以解决样本的特征数远超过样本数的问题。

对 Ridge 回归的 Loss function 求偏导，令其为 $0$。

$$
\begin{aligned}
E_{\hat{\mathbf{w}}}&=(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})^{T}(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})+\lambda||\mathbf{w}||_{2}^{2}\\
&=(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})^{T}(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})+\lambda\hat{\mathbf{w}}^{T}\hat{\mathbf{w}}\\
&=(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})^{T}(\mathbf{y}-\mathbf{X}\hat{\mathbf{w}})+\lambda\hat{\mathbf{w}}^{T}\mathbf{I}\hat{\mathbf{w}}\\
\end{aligned}
$$

解得：

$$
\hat{\mathbf{w}}=(\mathbf{X}^{T}\mathbf{X}+\lambda\mathbf{I})^{-1}\mathbf{X}^{T}\mathbf{y}
$$

这是解析解。其中，$(\mathbf{X}^{T}\mathbf{X}+\lambda\mathbf{I})^{-1}$ 一定是满秩的。

------

对于 Lasso 回归，首先说：

$$
||\mathbf{w}||_{1}=\sum\limits_{i=1}^{n}|w_{i}|
$$

对 Lasso 回归的 Loss function 求偏导，令其为 $0$。

它在某点可导的条件是：

1.  函数在该点连续
2.  函数在该点存在左导数和右导数，且相等

考虑到 $||\mathbf{w}||_{1}$ 没有导数，所以使用近端梯度下降法（Proximal Gradient Descent Method），来最小化 Lasso 回归的 Loss function。

解析解：

$$
w^{i}_{k+1}=\begin{cases}
z^{i}-\lambda/L&\text{,}\,z^{i}\gt\lambda/L\\
0&\text{,}\,|z^{i}|\le\lambda/L\\
z^{i}+\lambda/L&\text{,}\,z^{i}\lt-\lambda/L
\end{cases}\\
\mathbf{z}=\mathbf{w}_{k}-1/L\nabla f(\mathbf{w}^{k})
$$

其中，$L$ 是 Lipschitz 常数，$f(\mathbf{w})$ 是 Loss function。所谓 Lipschitz 常数，总之就是一个大于零的常数。

Lasso 回归可以解决特征数大于样本数的问题。

然而，Lasso 回归容易产生稀疏解（Sparse Solution）。稀疏解指的是模型参数向量中的某些元素被压缩为零，从而实现特征选择（Feature Selection）的效果。

Lasso回归的L1正则化项 $||\mathbf{w}||_{1}$ 是参数向量 $\mathbf{w}$ 的L1范数，定义为参数向量中各个元素绝对值的和。L1正则化项的特点是在优化过程中促使某些参数变为零。这是因为L1正则化项在参数空间中的等值线是由菱形构成的，而不是圆形（L2正则化项的等值线是圆形）。因此，当优化算法在最小化损失函数的同时，受到L1正则化项的影响，它会倾向于将某些参数压缩为零，从而实现特征选择。

通过产生稀疏解，Lasso回归可以帮助我们识别和选择对目标变量具有显著影响的特征。这对于处理高维数据和特征选择非常有用。通过将某些特征的系数置为零，Lasso回归可以简化模型，减少过拟合的风险，并提高模型的解释性。

## 深度学习

反正生物上的神经元就是一个计算单元，通过输入线获取大量输入并进行一些计算，然后通过轴突将输出输出到其他节点或大脑中的其他神经元。

M-P 神经元模型就是这样的一个模型，它接收 $n$ 个输入信号 $x_{1}, x_{2}, \ldots, x_{n}$，每个输入信号 $x_{i}$ 通过带权重 $w_{i}$ 的连接线传递给神经元，神经元将所有输入信号的加权和 $z=\sum_{i=1}^{n}w_{i}x_{i}$ 作为输入，通过激活函数 $f$ 计算输出 $y=f(z)$。

### 激活函数

激活函数（Activation Function）是神经网络中的一个重要组成部分，它决定了神经元的输出是否被激活。激活函数的输入是神经元的加权和 $z$，输出是神经元的输出 $y$。

激活函数的作用是引入非线性因素，使得神经网络可以拟合非线性函数。如果没有激活函数，神经网络的输出将是输入的线性组合，无法拟合非线性函数。

激活函数的种类有很多，常见的有 Sigmoid 函数、Tanh 函数、ReLU 函数等。

![An_Example_of_Activation_Function](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/An_Example_of_Activation_Function.png)

不过，sigmoid 函数和 tanh 函数在深度学习中已经不常用了，因为它们的梯度在接近饱和区域时会变得非常小，从而导致梯度消失的问题。而且，sigmoid 输出不是以零为中心的。

$$
\begin{aligned}
\tanh(x)&=2\textrm{sigmoid}(2x)-1\\
\textrm{sigmoid}(x)&=\frac{1}{1+e^{-x}}
\end{aligned}
$$

人们发现，与 sigmoid/tanh 函数相比，它极大地加速了随机梯度下降的收敛（例如 Krizhevsky 等人的 6 倍）。有人认为这是由于其线性、非饱和形式造成的。

与涉及昂贵运算（指数等）的 tanh/sigmoid 神经元相比，ReLU 可以通过简单地将激活矩阵阈值设置为零来实现。

### 感知器

感知器（Perceptron）是一种简单的神经网络模型，它是由两层神经元组成的单层前馈神经网络。感知器的输入层接收输入信号，输出层产生输出信号。感知器的输出信号是由输入信号加权和经过激活函数计算得到的。

![An_Example_of_Perceptron](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/An_Example_of_Perceptron.png)

这是一种线性分类器。如果训练集是线性可分的，那么感知器就保证收敛。

Perceptron 的缺陷：单层 Perceptron 只能学习线性可分离模式。

### 多层感知器

多层感知器（Multilayer Perceptron, MLP）是一种前馈神经网络，它由多个感知器组成，可以用于解决非线性分类问题。

![An_Example_of_Multilayer_Perceptron](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/An_Example_of_Multilayer_Perceptron.png)

在全连接层中，每个神经元都连接到前一层中的每个神经元，并且每个连接都有自己的权重。

所谓前馈（Feedforward），就是指神经网络的信息流只能从输入层流向输出层，而不能从输出层流向输入层。在此网络中，信息仅沿一个方向移动，即向前移动，从输入节点经过隐藏节点（如果有）并到达输出节点。网络中不存在循环或环路。

MLP 的缺点：就内存（权重）和计算（连接）而言非常昂贵

### 反向传播

反向传播，即 Back Propagation。

1986年，提出了多层感知器（MLP）的BP算法（反向传播算法），利用 Sigmoid 激活函数进行非线性映射，有效解决了非线性分类和学习的问题。

看下 Sigmoid 函数。

![Sigmoid_Function_and_Its_Image](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Sigmoid_Function_and_Its_Image.png)

>   不想学了……TAT
>
>   先 Mark 一下吧。

### Learning and Training

神经网络的学习过程是根据训练数据来调整神经元的连接权值以及每个功能神经元的阈值。

-   在训练神经网络时，一个 epoch 意味着完整训练集的一次传递。通常它可能包含一些迭代。
-   通常我们将训练集分成 batches，每个 epoch 都会遍历整个训练集。每次 iteration 都会跑个 batch。
-   每一个 epoch 后，我们都会在 validation set 上测试一下模型的性能。
-   随机梯度下降（Stochastic Gradient Descent，SGD）意味着，基本上，我们使用 1 个样本的损失来更新每次迭代的参数。
-   批量梯度下降（Batch Gradient Descent，BGD）意味着，我们在每次迭代时使用训练集中所有示例的损失平均值。
-   小批量梯度下降（Mini-Batch Gradient Descent，MBGD）意味着，我们在每次迭代时使用训练集中的 $n$ 个样本（而不是 SGD 中的 $1$ 个样本）。

### 梯度下降

总之是在更新参数。正是为了让 Loss function 最小化，所以要更新参数。

$$
f(\theta_{0}, \theta_{1})\xrightharpoonup{\nabla}(\frac{\partial f}{\partial\theta_{0}}, \frac{\partial f}{\partial\theta_{1}})^{T}
$$

梯度 $\nabla f$ 的方向是函数 $f$ 在当前点的最大方向导数的方向，而梯度的模是方向导数的最大值。

$$
\textbf{minimize}f(\theta_{0}, \theta_{1})\\
\vec{\theta}\leftarrow\vec{\theta}-\eta\nabla f(\vec{\theta})
$$

梯度下降是一种一阶迭代优化算法，用于寻找函数的最小值。

### Softmax

Softmax 函数是一种归一化指数函数，它将 $n$ 维实数向量 $\mathbf{z}$ 映射到 $n$ 维实数向量 $\sigma(\mathbf{z})$，其中每个元素的范围是 $(0, 1)$，并且所有元素的和为 $1$。

$$
\sigma(\mathbf{z})_{i}=\frac{e^{z_{i}}}{\sum_{j=1}^{n}e^{z_{j}}}
$$

Softmax 函数常用于多分类问题的输出层，用于将神经网络的输出转换为概率分布。

### CNN

卷积神经网络（Convolutional Neural Network，CNN）是一种前馈神经网络，它的人工神经元可以响应一部分覆盖范围内的周围单元，对于大型图像处理有出色表现。

主要有三个关键概念：

1.  Receptive Field（感受野）：神经元对输入数据的局部区域。
2.  Parameter Sharing（权值共享）：在一个特征图中，所有神经元都使用相同的权重和偏置。
3.  Pooling（池化）：通过减少特征图的尺寸来减少参数数量。

对于 Receptive Field，每个隐层节点只连接到图像某个局部的像素区域，从而大大减少需要训练的权值参数。（和 MLP 不同）

而对于 Parameter Sharing，每个隐层节点都使用相同的权值和偏置，这样可以大大减少需要训练的权值参数。

对于 Pooling，它可以减少特征图的尺寸，从而减少参数数量。虽然尺寸减少，但是主要特征仍然保留。Pooling 本质上是一种 subsampling。

CNN 的主要结构是：

![The_Overall_Structure_of_CNN](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/The_Overall_Structure_of_CNN.png)

### 卷积层

![Some_Explanation_of_Convolution](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Some_Explanation_of_Convolution.png)

## 集成学习

集成学习（Ensemble Learning）是一种机器学习范式，它通过将多个学习器进行组合来解决单一预测问题。集成学习的目标是将多个学习器组合成一个预测模型，以获得比单个学习器更好的泛化能力。

集成的方式，总体来说可以分为：

1.  Boosting
2.  Bagging

集成学习：

-   同质集成（homogeneous ensemble）：集成中的所有学习器都是同一模型生成的。
-   异质集成（heterogeneous ensemble）：集成中的学习器可以是不同的模型生成的。
-   弱学习器（weak learner）：在学习过程中，学习器的性能比随机猜测略好的学习器。

基础学习器越准确、越多样化，集成就越好。（即好而不同）然而，基础学习器的“准确性”和“多样性”是相互矛盾的。

### 并行方法

并行方法（Parallel Methods）是一种集成学习方法，它通过训练多个基础学习器来构建集成模型。并行方法的基本思想是，训练多个基础学习器，然后将它们组合成一个集成模型。并行方法的优点是易于实现，但它的缺点是基础学习器之间的依赖性。

#### Baggging

Bagging（Bootstrap Aggregating）是集成学习中的一种方法，旨在通过构建多个基学习器并对它们的预测进行组合来改善模型的性能。Bagging的基本思想是通过自助采样（Bootstrap Sampling）和聚合（Aggregation）来减少模型的方差。

Bagging 的基础是通过使用一种名为 bootstrap 的统计技术获得与原始训练集 L 大小相同的不同训练集。

所谓 bootstrap，就是从原始训练集 L 中随机采样 $m$ 个样本，然后将这 $m$ 个样本放回，再从中随机采样 $m$ 个样本，重复这个过程 $m$ 次，最终得到 $m$ 个大小为 $m$ 的训练集。这个过程就是 bootstrap。它使得训练集 $L_{i}$ 每一个都带有和原数据集 $L$ 相比略有不同的特征。

$x^{\ast}=(x_{1}^{\ast}, x_{2}^{\ast}, \ldots, x_{m}^{\ast})$ 是从原样本 $x=(x_{1}, x_{2}, \ldots, x_{m})$ 中随机采样得到的样本。显然，$x^{\ast}$ 中有的样本可能重复，有的样本可能没有出现。

$$
\begin{aligned}
\lim\limits_{m\rightarrow\infty}[1-(1-\frac{1}{m})^{m}]&=\lim\limits_{m\rightarrow\infty}[1-((1+\frac{1}{-m})^{-m})^{-1}]\\
&=1-e^{-1}\\
&\approx 0.632
\end{aligned}
$$

这个公式揭示了，每个训练子集中仅使用原始训练集的 $63.2\%$ 样本，因此 $36.8\%$ 的样本可以用作验证集。这个大约 $1/3$ 的样本称为 out-of-bag（OOB）样本。

总体结构：

![The_Overall_Structure_of_Bagging](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/The_Overall_Structure_of_Bagging.png)

另外，Bagging 可以减小模型的方差。

>   集成学习是不是都可以减小方差……？

#### 随机森林

随机森林（Random Forest）是一种集成学习方法，它通过构建多个决策树并对它们的预测进行组合来改善模型的性能。随机森林的基本思想是通过随机选择特征子集来减少模型的方差。

随机森林是对 Bagging 的改进，它的 base learner 是决策树。

随机森林有放回构造训练集；随机选取$m$个特征构建决策树。同朴素的决策树不同，随机森林引入随机属性扰动，使得每棵树的训练集不同，从而使得每棵树的结构不同；并且，随机森林的每棵树都是完全生长的，不进行剪枝。

![The_Overall_Structure_of_Random_Forest](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/The_Overall_Structure_of_Random_Forest.png)

### 串行方法

串行方法依赖于基学习器之间的顺序关系。串行方法的基本思想是通过顺序训练多个基学习器，每个基学习器都专注于先前迭代中预测错误的样本。串行方法的优点是基础学习器之间的依赖性较低，但它的缺点是易于过拟合（当然，这意味着串行方法可以减小模型的偏差）。

#### Boosting

在集成学习中，串行方法是一种通过顺序训练多个基学习器的方法。其中，Boosting是一种常见的串行集成学习方法。

Boosting的基本思想是通过迭代训练多个弱学习器，每次迭代都根据前一轮的预测结果来调整样本的权重，使得基学习器更关注先前迭代中预测错误的样本。通过这种方式，Boosting可以逐步减小模型的偏差，并提高模型的准确性。

下面是Boosting的基本步骤：

1. 初始化样本权重：将每个样本的权重初始化为相等值，通常为 $\frac{1}{N}$，其中 $N$ 是样本数量。

2. 迭代训练基学习器：在每一轮迭代中，根据当前样本权重训练一个基学习器。训练过程中，样本的权重会根据前一轮的预测结果进行调整，即被错误预测的样本会被赋予更高的权重。

3. 更新样本权重：根据当前基学习器的预测结果，更新每个样本的权重。被正确预测的样本权重会减小，而被错误预测的样本权重会增加。

4. 组合基学习器：将每个基学习器的预测结果按照一定的权重进行组合，通常是通过加权投票或加权平均的方式。

5. 重复迭代：重复步骤2至步骤4，直到达到预定的迭代次数或满足停止条件。

最终，Boosting通过组合多个基学习器的预测结果，将它们的优势进行整合，得到一个更强大的集成模型。由于每个基学习器都专注于先前迭代中预测错误的样本，Boosting可以逐步减小模型的偏差，并提高整体模型的准确性。

常见的Boosting算法包括AdaBoost（Adaptive Boosting）、Gradient Boosting和XGBoost（eXtreme Gradient Boosting）等。它们在迭代过程中采用不同的策略来调整样本权重和基学习器的权重，以进一步提高模型的性能。

#### AdaBoost

![An_Example_of_AdaBoost_p1](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/An_Example_of_AdaBoost_p1.png)

![An_Example_of_AdaBoost_p2](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/An_Example_of_AdaBoost_p2.png)

输入：

$$
\begin{aligned}
\text{训练集}\quad&\mathcal{D}=\{(x_{1}, y_{1}), (x_{2}, y_{2}), \ldots, (x_{m}, y_{m})\}\\
\text{基学习器}\quad&\mathfrak{L}\\
\text{迭代次数}\quad&T
\end{aligned}
$$

过程：

![The_Process_of_AdaBoost](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/The_Process_of_AdaBoost.png)

输出：

$$
H(x)=\text{sign}(\sum_{t=1}^{T}\alpha_{t}h_{t}(x))
$$

其中，$\text{sign}$ 是符号函数，$\alpha_{t}$ 是基学习器 $h_{t}$ 的权重。

还是看看详细例子：

![A_Detailed_Example_of_AdaBoost_p1](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_Detailed_Example_of_AdaBoost_p1.png)

![A_Detailed_Example_of_AdaBoost_p2](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_Detailed_Example_of_AdaBoost_p2.png)

![A_Detailed_Example_of_AdaBoost_p3](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_Detailed_Example_of_AdaBoost_p3.png)

![A_Detailed_Example_of_AdaBoost_p4](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_Detailed_Example_of_AdaBoost_p4.png)

![A_Detailed_Example_of_AdaBoost_p5](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_Detailed_Example_of_AdaBoost_p5.png)

![A_Detailed_Example_of_AdaBoost_p6](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_Detailed_Example_of_AdaBoost_p6.png)

![A_Detailed_Example_of_AdaBoost_p7](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_Detailed_Example_of_AdaBoost_p7.png)

![A_Detailed_Example_of_AdaBoost_p8](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_Detailed_Example_of_AdaBoost_p8.png)

![A_Detailed_Example_of_AdaBoost_p9](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_Detailed_Example_of_AdaBoost_p9.png)

![A_Detailed_Example_of_AdaBoost_p10](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_Detailed_Example_of_AdaBoost_p10.png)

![A_Detailed_Example_of_AdaBoost_p11](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_Detailed_Example_of_AdaBoost_p11.png)

### Fusion 策略

其实就是出了好多结果了，怎么把这些结果组合起来。

>   我会说它是融合策略，但我不确定一般是不是翻译成这个（

#### 平均

朴素的平均就只是对结果平均了一下而已：

$$
H(x)=\frac{1}{T}\sum_{t=1}^{T}h_{t}(x)
$$

加权平均：

$$
H(x)=\sum_{t=1}^{T}\omega_{t}h_{t}(x)
$$

其中：

$$
\omega_{t}\ge 0\\
\sum_{t=1}^{T}\omega_{t}=1
$$

一般而言，在个体学习器性能相差较大时宜使用加权平均法，个体学习器性能相近时宜使用简单平均法。（倒也合理）

#### 投票

学习器 $h_{i}$ 的预测结果是类别标签集合 ${c_{1}, c_{2}, \ldots, c_{n}}$ 中的一个，投票法的基本思想是选择预测结果最多的类别标签作为集成模型的预测结果。

我们先约定 $h_{i}$ 在样本 $x$ 上的预测结果是 $(h^{1}_{i}(\mathbf{x}), h^{2}_{i}(\mathbf{x}), \ldots, h^{n}_{i}(\mathbf{x}))$，其中 $h^{j}_{i}(\mathbf{x})$ 是 $h_{i}$ 在样本 $x$ 上预测为类别标签 $c_{j}$ 的输出。

**绝对多数投票法**

即 Majority Voting，它的基本思想是选择预测结果最多的类别标签作为集成模型的预测结果。

$$
H(\mathbf{x})=\begin{cases}
c_{j}&\text{,}\,\text{if}\,\sum_{i=1}^{T}h_{i}^{j}(\mathbf{x})\gt\frac{1}{2}\sum_{k=1}^{l}\sum_{i=1}^{T}h_{i}^{k}(\mathbf{x})\\
\text{Rejection}&\text{,}\,\text{otherwise}
\end{cases}
$$

其中，$l$ 是类别标签的数量。

**相对多数投票法**

即 Plurality Voting，它的基本思想是选择预测结果最多的类别标签作为集成模型的预测结果。

$$
H(\mathbf{x})=c_{\arg\max\limits_{j}\sum_{i=1}^{T}h_{i}^{j}(\mathbf{x})}
$$

**加权投票法**

即 Weighted Voting，它其实就是 Plurality Voting 的加权版本。

$$
H(\mathbf{x})=c_{\arg\max\limits_{j}\sum_{i=1}^{T}\omega_{i}h_{i}^{j}(\mathbf{x})}
$$

其中，$\omega_{i}$ 是学习器 $h_{i}$ 的权重。

### 多样性度量

显然，如果个体学习器的准确率越高，多样性越大，则集成越好。

误差-分歧分解（Error-Diversity Decomposition）是一种用于分析集成学习器性能的方法，它将集成学习器的误差分解为偏差、方差和分歧三个部分。

$$
E=\bar{E}-\bar{A}
$$

其中，$E$ 就是集成误差;

$\bar{E}=\sum_{t=1}^{T}\omega_{t}E_{t}$ 是个体学习器泛化误差的加权均值。

$\bar{A}=\sum_{t=1}^{T}\omega_{t}A_{t}$ 是个体学习器的加权差异性。

------

给定数据集 $\mathcal{D}=\{(x_{1}, y_{1}), (x_{2}, y_{2}), \ldots, (x_{m}, y_{m})\}$，对于二分类问题，假设 $y\in\{-1, +1\}$。分类器 $h_{i}$ 和 $h_{j}$ 的预测结果列联表（Contingency Table）如下：

|            | $h_{i}=+1$ | $h_{i}=-1$ |
| :--------: | :--------: | :--------: |
| $h_{j}=+1$ |     a      |     c      |
| $h_{j}=+1$ |     b      |     d      |

其中，$a$ 表示 $h_{i}$ 和 $h_{j}$ 都预测正确的样本数量，$b$、$c$ 和 $d$ 由此类推。而且，$a+b+c+d=m$。

**不合度量**

即 Disagreement Measure。

$$
\text{Dis}(ij)=\frac{b+c}{m}
$$

$\text{Dis}(ij)$ 表示 $h_{i}$ 和 $h_{j}$ 的不合度量，值域为 $[0, 1]$，值越大表示 $h_{i}$ 和 $h_{j}$ 的预测结果越不一致。

**相关系数**

即 Correlation Coefficient。

$$
\rho_{ij}=\frac{ad-bc}{\sqrt{(a+b)(c+d)(a+c)(b+d)}}
$$

$\rho_{ij}$ 表示 $h_{i}$ 和 $h_{j}$ 的相关系数，值域为 $[-1, 1]$。如果 $\rho_{ij}=1$，则表示 $h_{i}$ 和 $h_{j}$ 的预测结果完全一致；如果 $\rho_{ij}=-1$，则表示 $h_{i}$ 和 $h_{j}$ 的预测结果完全相反；如果 $\rho_{ij}=0$，则表示 $h_{i}$ 和 $h_{j}$ 的预测结果不相关。

**Q 统计量**

$$
Q_{ij}=\frac{ad-bc}{ad+bc}
$$

须知，$|Q_{ij}|\le|\rho_{ij}|$。

**Kappa 统计量**

$$
\kappa_{ij}=\frac{p_{1}-p_{2}}{1-p_{2}}\\
p_{1}=\frac{a+d}{m}\\
p_{2}=\frac{(a+b)(a+c)+(c+d)(b+d)}{m^{2}}
$$

其中，$p_{1}$ 表示 $h_{i}$ 和 $h_{j}$ 预测结果一致的概率，$p_{2}$ 表示 $h_{i}$ 和 $h_{j}$ 偶然达成一致的概率。

>   ？？？

$\kappa$越大，说明两个学习器的一致性越高。相反，$\kappa$越小，说明两个学习器的一致性越低。

### 多样性增强

在集成学习中需有效的生成多样性大的个体学习器，一般思路是在学习过程中引入随机性。

-   数据样本扰动
-   输入属性扰动
-   输出表示扰动
-   算法参数扰动

**数据样本扰动**

给定初始数据集，可从中产生出不同的数据子集，再利用不同的数据子集训练出不同的个体学习器。数据样本扰动通常是基于采样法：Boosting、Bagging。

这对于不稳定基学习器（决策树、神经网络...）很有效。

**输入属性扰动**

对于稳定基学习器（LDA、SVM、KNN...），从初始属性集中抽取出若干个属性子集，基于每个属性子集训练一个基学习器。

对包含大量冗余属性的数据，在子空间中训练个体学习器不仅能产生多样性大的个体，还会因属性数的减少而节省时间开销。

**输出表示扰动**

将原任务拆解为多个可同时求解的子任务或者是将分类输出转化为回归输出后构建个体学习器。

**算法参数扰动**

基学习算法一般都有参数需要设置，例如神经网络的神经元数，初始连接权值等，通过随机设置不同的参数。往往可产生差别较大的个体学习器。

### 题目

Q：试分析随机森林为什么比决策树bagging集成的训练速度更快？

A：随机森林是一种集成学习方法，它由多个决策树组成。相比于单个决策树的bagging集成，随机森林在训练速度上通常更快，这主要归因于以下几个方面：

1. 并行化处理：随机森林中的决策树可以并行生成，每个决策树都是独立训练的。这意味着在拥有多个处理器或多个计算节点的情况下，可以同时训练多个决策树，从而加快训练速度。相比之下，决策树的bagging集成通常是串行生成的，每个决策树都依赖于前一个决策树的结果，无法并行化处理。

2. 特征子集采样：随机森林在生成每个决策树时，对于每个节点的特征进行随机子集采样。这意味着每个决策树只使用了部分特征进行训练，而不是使用全部特征。这种特征子集采样可以减少每个决策树的计算量，进而提高训练速度。相比之下，决策树的bagging集成通常使用全部特征进行训练。

3. 数据子集采样：随机森林在生成每个决策树时，对于每个节点的训练样本进行随机子集采样。这意味着每个决策树只使用了部分训练样本进行训练，而不是使用全部训练样本。这种数据子集采样可以减少每个决策树的计算量，进而提高训练速度。相比之下，决策树的bagging集成通常使用全部训练样本进行训练。

综上所述，随机森林通过并行化处理、特征子集采样和数据子集采样等技术手段，能够在训练过程中减少计算量，从而提高训练速度。

Q：了解 GBDT、XGBoost 原理

A：GBDT（Gradient Boosting Decision Trees）是一种集成学习算法，它通过组合多个决策树来构建一个强大的预测模型。XGBoost（eXtreme Gradient Boosting）是GBDT的一种优化实现，它在GBDT的基础上进行了改进，提供了更高的性能和可扩展性。

GBDT的原理如下：
1. 初始化模型：将所有样本的预测值初始化为一个常数，通常为目标变量的均值。
2. 迭代训练：每次迭代中，GBDT通过拟合一个新的决策树来减少当前模型的残差。它通过计算当前模型对每个样本的预测值与实际值之间的残差，然后用这些残差作为新的目标值来训练一个新的决策树。
3. 更新模型：将新生成的决策树与当前模型进行加权组合，得到一个更新后的模型。为了避免过拟合，每棵树的贡献通过一个学习率进行缩放，通常小于1。
4. 重复迭代：重复步骤2和步骤3，直到达到预定的迭代次数或满足停止条件。

GBDT的优点包括：
- 能够处理各种类型的特征，包括连续特征和离散特征。
- 能够自动捕捉特征之间的非线性关系。
- 在处理缺失值时具有鲁棒性。
- 可以灵活处理不同类型的损失函数。

XGBoost是对GBDT的改进，它在GBDT的基础上引入了一些新的技术和优化：
1. 正则化：XGBoost引入了正则化项，包括L1正则化和L2正则化，用于控制模型的复杂度，防止过拟合。
2. 列抽样：XGBoost支持对特征进行列抽样，这样每次训练决策树时只使用部分特征，可以提高模型的泛化能力和效率。
3. 并行计算：XGBoost使用多线程进行并行计算，加快了模型的训练速度。
4. 缺失值处理：XGBoost能够自动处理缺失值，无需对缺失值进行特殊处理。
5. 特征权重：XGBoost可以计算特征的重要性，帮助理解模型的预测过程。

总的来说，GBDT和XGBoost都是强大的预测模型，它们通过组合多个决策树来提高预测性能。XGBoost在GBDT的基础上进行了优化，提供了更多的功能和性能，成为了机器学习竞赛中常用的算法之一。

## 聚类

聚类（Clustering）是一种无监督学习方法，它将数据集中的样本划分为若干个互不相交的子集，每个子集称为一个簇（Cluster）。聚类的目标是使得簇内的样本尽量相似，而簇间的样本尽量不同。

所谓无监督，就是说聚类任务的训练数据没有标签，也就是说，我们不知道训练数据的真实类别。我们在聚类任务中，只需要将训练数据划分为若干个簇，而不需要知道每个簇的真实类别。

度量样本的差异性，就用距离好了；然后关于距离什么的，上面已经说过了，那肯定是距离越小越相似嘛……

### K-Means

1.  随机选择K个样本作为聚类的质心。
2.  计算每个样本距K个质心点的距离。
3.  选择距离最小的簇质心 $c_{i}(i=1,2,\ldots,k)$ 来划分簇。
4.  **重新确定质心**。
5.  迭代直至收敛。

反正关键就是，根据随机质心决定簇了以后，簇的平均位置就是新的质心。文字看不明白的话就看幻灯片中的图。

实验结果表明，由于初始化不同，K-Means 每次结果都不同。

那么，我们就想了，我们需要所有可能的初始化并获得最佳结果。找到最佳结果的措施是最小化平方误差 $E$（SSE，平方误差之和）。

$$
E=\sum_{i=1}^{k}\sum_{x\in C_{i}}\|x-u_{i}\|^{2}_{2}\\
u_{i}=\frac{1}{|C_{i}|}\sum_{x\in C_{i}}x
$$

不过，最小化 $E$ 是 NP-Hard 的。K-Means 使用交互式最优算法。每次迭代的每一步都是优化 $E$ 的过程。我们可以选择多次初始化来得到最好的结果（不过这个措施是否有效取决于$k$）。

关于选 $K$ 这件事：

![How_Do_You_Choose_K_in_K-Means](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/How_Do_You_Choose_K_in_K-Means.png)

------

Q：假设使用 $K=3$ 和 $K=5$ 运行 K-means，发现 $K=5$ 时的 SSE 比 $K=3$ 时高得多。这说明什么？

A：这在数学上是不可能的。代码中一定有错误。正确的簇数是 $K=3$。在 $K=5$ 的运行中，K-Means 陷入了错误的局部最小值。应该尝试使用多个随机初始化重新运行 K-Means。

### Mixture of Gaussian

$$
p_{m}(x)=\sum\limits_{k=1}^{K}a_{i}p(x|\mu_{i}, \Sigma_{i})\\
\sum\limits_{i=1}^{K}a_{i}=1\\
$$

其中，$a_{i}$ 是相应的混合系数，$\mu_{i}$，$\Sigma_{i}$ 是第$i$个混合分量的均值向量和协方差矩阵。

目标函数：

$$
\begin{aligned}
p_{m}(z_{j}=i|x_{j})&=\frac{p_{m}(x_{j}|z_{j}=i)\cdot p(z_{j}=i)}{p(x_{j})}\\
&=\frac{a_{i}p_{m}(x_{j}|\mu_{i}, \Sigma_{i})}{\sum\limits_{l=1}^{K}a_{i}p_{m}(x_{j}|\mu_{l}, \Sigma_{l})}
\end{aligned}
$$

然后用 MLE 来优化函数。

>   一想到大数据没讲这个就不想写了
>
>   数媒，我真是谢谢你啊😅

### DBSCAN

$\epsilon$-邻域：对于数据集 $D$ 中的任意点 $p$，其 $\epsilon$-邻域包含数据集 $D$ 中与 $p$ 的距离不大于 $\epsilon$ 的点。

核心点：如果点 $p$ 的 $\epsilon$-邻域至少包含 MinPts 个点，则称点 $p$ 为核心点。

直接密度可达（Directly Density-Reached）：如果点 $q$ 在点 $p$ 的 $\epsilon$-邻域中，且点 $p$ 是核心点，则称点 $q$ 相对于点 $p$ 是直接密度可达的。

密度可达（Density-Reached）：对于点 $p$ 和 $q$，如果存在一个点序列 $p_{1}, p_{2}, \ldots, p_{n}$，其中 $p_{1}=p$，$p_{n}=q$，且 $p_{i+1}$ 相对于 $p_{i}$ 是直接密度可达的，则称点 $q$ 相对于点 $p$ 是密度可达的。

密度相连（Density-Connected）：对于点 $p$ 和 $q$，如果存在一个**核心点** $o$，使得点 $p$ 和点 $q$ 都相对于点 $o$ 是密度可达的，则称点 $p$ 和点 $q$ 是密度相连的。

![A_Simple_Description_of_DBSCAN](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/A_Simple_Description_of_DBSCAN.png)

DBSCAN将簇定义为**密度连通性最强的样本集**。

代码流程如下：

![Detailed_Algorithm_of_DBSCAN](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Detailed_Algorithm_of_DBSCAN.png)

优点：

-   不需要预先指定簇的个数。
-   它可以发现任何形状的空间聚类。
-   它可以丢弃远程点。

缺点：

-   当簇间距差异很大时不适合。
-   参数调整比较复杂。

### AGNES

>   据说大数据没讲
>
>   道心破碎了

### 性能度量

>   貌似
>
>   大数据没讲

好的聚类应该：

-   簇内相似度高
-   簇间相似度低

![Formula_of_Performance_Measure_in_Clustering](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Formula_of_Performance_Measure_in_Clustering.png)

总之是公式们，度量各种各样的距离。然后就是最优化问题云云。

内部指数，就是说不使用参考模型直接评估聚类结果；外部指数，将聚类结果与参考模型进行比较，例如领域专家给出的划分结果。

Internal Index 有两种：

**Davies-Boukdin 指数**

$$
\text{DBI}=\frac{1}{k}\sum_{i=1}^{k}\max\limits_{j\neq i}\left(\frac{\text{avg}({C_{i}})+\text{avg}({C_{j}})}{d_{\text{cen}}(C_{i},C_{j})}\right)
$$

越小越好。

**Dunn 指数**

$$
\text{DI}=\min\limits_{1\le i\le k}\left(\min\limits_{j\neq i}\left(\frac{d_{\text{min}}(C_{i},C_{j})}{\max\limits_{1\le l\le k}\text{diam}(C_{l})}\right)\right)
$$

越大越好。

External Index，不想写了。先 Mark 一下。

## 数据预处理

大约会考一点点？

## 特征选择与提取

不考，过！