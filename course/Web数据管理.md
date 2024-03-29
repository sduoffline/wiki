---
title: Web数据管理
description: 
published: true
date: 2024-02-26T09:36:03.486Z
tags: 
editor: markdown
dateCreated: 2024-02-26T09:36:03.486Z

---

## 课程介绍

### 课程考核

期末 80%

实验 20%，实验中心 504 机房，5~13周

| 序 号 | 实验题目名称             | 实验内容                                                     | 学时分配 | 实验类型  | 每组人数 | 实验要求                       |
| :---- | ------------------------ | ------------------------------------------------------------ | -------- | --------- | -------- | ------------------------------ |
| 1     | 简单爬虫                 | 选择并掌握合适的爬虫框架，合适的模块，开源工具               | 6        | 必做      | 1        | 实验报告，实验代码，爬取的数据 |
| 2     | 设计克服反爬虫障碍的爬虫 | 选择具有反爬虫障碍的网站为目标  分析网站的反爬虫障碍  编写爬虫软件，克服障碍获取相应的数据 | 6        | 必做      | 1        | 实验报告，实验代码，爬取的数据 |
| 3     | 可视化爬取的数据         | 对爬取的数据进行分析。  设计简单的web 网站，  能够采用各种可视化方法展示数据分析的结果 | 20       | 3-4二选一 | 1        | 实验报告，实验代码，现场演示   |
| 4     | 集成爬取的数据           | 爬取两个功能相似，设计不同的网站  设计简单的web 网站能够实现数据的查询功能，查询结果中要有信息的来源（数据站点， 可以重定向源网页）  后台数据库的设计要体现对不同结构网站的信息集成 | 20       | 3-4二选一 | 1        | 实验报告，实验代码，现场演示   |

### 参考书目

Web数据管理：概念与技术

Web数据挖掘（第二版）

数据挖掘概念与技术

现代信息检索

## 第一讲 绪论

> 老师说所谓绪论就当讲座来听，稍微了解一下就行，由于 ppt 过于精简，笔者对于某些概念进行了一点点的扩充，看个乐就行。
>

### 一、web 数据

#### WWW概述

WWW ：World Wide Web

WWW作为全球化信息空间，蕴含着具有巨大潜在价值的信息和知识。

WWW技术发展为数据管理带来了巨大的挑战和机遇。

Web将计算机网络技术和超媒体技术融合起来，在很短时间内就被各个领域的用户广泛接受，信息量呈爆炸式增长。

Web中这些海量和多样化数据不同于传统的数据模型。

#### web 数据的特点

1. 数据量大，并且仍然在不断增长

2. 存在各种类型的数据

   结构化表格、无结构化的文本、半结构化的网页、多媒体数据

3. 异构的信息

   内容相同，形式不同

4. 绝大部分信息是相连接的

   超文本：网站内部和网站之间的网页通过超链接联系

   Web有向图：网页为节点，HTML链接引用为有向边

   图结构符合幂律分布，可使用pagerank算法找到最重要的网页，或者是“垃圾”网页

5. 噪音的存在

   网页包含多个模块，对于特定任务只有一部分信息有用

   本身没有信息质量的控制 SPAM （垃圾）

6. 动态性

##### 扩展：计算广告学

计算广告学是一门正在兴起的分支学科，它涉及到大规模搜索和文本分析、信息获取、统计模型、机器学习、分类、优化以及微观经济学。

计算广告学所面临的最主要挑战是在特定语境下特定用户和相应的广告之间找到“最佳匹配”。语境可以是用户在搜索引擎中输入的查询词，也可以是用户正在读的网页，还可以是用户正在看的电影，等等。而用户相关的信息可能非常多也可能非常少。潜在广告的数量可能达到几十亿。因此，取决于对“最佳匹配”的定义，面临的挑战可能导致在复杂约束条件下的大规模优化和搜索问题。

##### 重点：各种类型的数据

- 半结构化或无结构化
- 非规范化，Web的开放性和用户的随意性使得信息资源的质量无法得到保证，其中可能包含一些各种各样的内容及自定义词汇等格式自由的数据
- 数据格式随意，机器难以自动处理

### 二、web 数据管理

定义：在Web环境下，对复杂信息的有效组织与集成，方便而准确的信息，查询、集成、发布（不要记）

### 三、web数据管理的内容

#### Web数据获取

通过机器学习发现Web上的信息结构或模式

数据抽取方法：全自动，半自动，手动

**全自动**：使用爬虫软件或者抓取工具自动访问网页，通过预定义的规则或模式匹配来提取数据。例如用Python的BeautifulSoup库或Scrapy框架来自动抓取新闻网站上的文章标题和内容。

**半自动**：结合自动化工具和人工审核，自动化工具负责初步抽取数据，人工审核则用于确保数据的准确性和完整性。例如使用自动化工具提取电商网站上的产品信息，然后人工检查和修正抽取结果中的错误或遗漏。

**手动抽取**：直接通过人工操作，从网页中复制所需的数据并粘贴到适当的存储位置。例如从一个小型的、结构不规范的网站上，手动复制联系信息或者价格列表。

#### Web数据管理中的数据组织

研究Web信息的特点，找出适合Web信息的合理组织模式

半结构化数据模式：XML，JSON，CSV

信息检索数据结构：非关系型数据库，倒排表

**倒排表**：倒排表（Inverted Index）是一种索引数据结构，用于存储单词在文档中出现的位置。它是搜索引擎的核心组件，使得根据关键词快速检索文档成为可能。在倒排表中，每个单词关联一个列表，列表中包含了该单词出现在哪些文档中以及在每个文档中的位置。这种结构便于根据关键词快速定位到包含这些关键词的文档，从而提高检索效率。

#### Web上的信息集成

Web上的信息集成是指将来自不同网站或数据源的信息整合成一个统一的、对用户友好的视图。这是Web数据管理中的一个关键挑战，尤其是在信息碎片化严重的互联网环境中。信息集成的目标是提供一个综合视图，使用户能够方便地访问和比较来自不同来源的相关信息。

例如，在旅行预订领域，一个信息集成的网站可以从多个航空公司和酒店的网站收集数据，然后在一个页面上展示所有的航班和住宿选项，让用户能够轻松比较不同的选择并做出决策。在求职网站中，信息集成可以将来自不同公司和招聘平台的职位信息汇总在一起，帮助求职者更有效地搜索和申请工作。

#### Web查询

Web查询是指在Web环境下根据用户需求检索信息的过程。它能够根据更丰富的语义信息在有效的数据组织模式下找出更准确的信息。这种查询不同于传统的模糊查询，它可以精确地定位信息的位置，提高检索的准确性和相关性。

##### 扩展：基于内容的图像查询

基于内容的图像查询（Content-Based Image Retrieval, CBIR）是一种根据图像的视觉特征如颜色、形状和纹理进行搜索的技术。这种查询方法不依赖于图像的元数据或描述文字，而是直接分析图像的内容，使得用户能够通过提供一个样例图像或指定某些视觉特征来检索相似的图像。

##### 扩展：语义查询

语义查询是一种基于对图像内容的深层次理解和解释进行检索的方法。例如，在进行“高兴的梁朝伟”的语义查询时，系统不仅需要识别出图像中的人物是梁朝伟，还需要理解其表情或情绪是高兴的。这种查询方式依赖于图像语义分析和自然语言处理技术，使得用户可以使用更接近自然语言的方式来进行图像检索。

#### Web信息发布

用户画像，信息推送、推荐（老师说以后有时间再讲）

**用户画像**：通过收集和分析用户的行为数据、偏好和兴趣点，构建用户的画像。这有助于理解用户的需求和特征，为其提供更个性化的服务。

**信息推送**：根据用户画像和实时的上下文信息，主动向用户推送他们可能感兴趣的内容。这种推送可以是新闻、广告、通知等，旨在提高用户的参与度和满意度。

**推荐系统**：利用机器学习和数据挖掘技术，分析用户的历史行为和偏好，预测他们可能感兴趣的新内容，并将其推荐给用户。推荐系统在电商、视频、音乐和社交网络等领域有广泛的应用。

### 四、web 数据管理的基础

#### 数据获得

**爬虫**：爬虫是用于自动抽取Web数据的程序。它可以访问网页，解析HTML内容，提取出有用的信息，如文本、链接、图片等，并将这些信息存储起来供进一步处理。

**Web结构**：Web结构数据主要指的是网页之间的超链接关系。这种关系可以用图数据结构来表示，其中节点代表网页，边代表超链接。通过分析Web结构，可以了解网站的组织方式和页面之间的关联。

**Web内容**：Web内容是指网页上的实际信息，包括文本、图片、视频等。这些内容是用户最直接感兴趣的数据，也是Web数据获取的主要目标。

**Web使用**：Web使用数据是指用户与网站交互的日志信息，如点击、浏览、搜索等行为记录。这些数据对于理解用户行为和优化网站设计非常重要。

#### 数据预处理

数据筛选, 清洗

- **数据筛选**：根据分析目标和需求，从大量数据中筛选出相关的数据子集。
- **数据清洗**：处理缺失值、异常值和重复数据，纠正数据中的错误和不一致。

应用数据

- **应用数据合并**：将来自不同源或不同格式的数据合并成一个统一的数据集。
- **组织存储**：根据数据的特点和使用需求，选择合适的数据结构和存储方式。
- **检索**：建立索引和查询机制，以便快速检索和访问数据。
- **可视化**：通过图表、图像等形式直观展示数据，帮助理解数据特征和分布。

特征提取：从原始数据中提取有用的信息作为特征，用于后续的分析和模型构建。

- **文本特征**：如词频、TF-IDF、词嵌入等。
- **图像特征**：如颜色直方图、SIFT、HOG等。
- **实体特征**：根据实体的属性和关系提取特征。

#### 数据变换

数据变换是数据预处理的重要步骤，旨在将数据转换成更适合分析和建模的形式。

1. **降维**：减少数据的维度，去除冗余和无关特征，提高计算效率，同时尽量保留数据的重要信息。常用的降维方法包括主成分分析（PCA）、线性判别分析（LDA）等。

2. **空间转换**：将数据从原始空间转换到新的特征空间，以便更好地表示数据的结构和关系。这种转换通常基于某种数学模型或算法。

3. **数据矩阵奇异值分解（SVD）**：SVD是一种常用的矩阵分解技术，可以用于降维、数据压缩和噪声过滤。在信息检索和推荐系统中有广泛应用。

4. **文本潜在语义分析（LSA）**：LSA是一种基于SVD的文本分析技术，通过提取文本数据的潜在语义结构，发现词汇之间的隐含关系。

5. **语义空间转换**：将词汇或文本从原始的文本空间转换到语义空间，以便更好地捕捉语言的含义和语境。常用的方法包括词嵌入（Word Embedding）技术，如Word2Vec、GloVe等。

通过这些数据变换方法，可以有效地处理高维度、稀疏性和非线性等问题，提高数据分析和机器学习模型的性能。

#### 数据挖掘与机器学习

##### 数据挖掘（Data Mining）

数据挖掘是从大量的、不完全的、有噪声的、模糊的、随机的实际应用数据中提取隐含的、人们事先不知道的、但又是潜在有用的信息和知识的过程。数据挖掘任务可以分为两类：

1. **预测任务**：根据其他属性的值预测特定属性的值。例如，通过分析房产的面积、位置、装修等属性，预测房产的销售情况。
2. **描述任务**：概括数据中潜在联系的模式。包括监督学习的分类任务、无监督学习的聚类任务、关联规则和序列模式的挖掘等。

##### 机器学习（Machine Learning, ML）

机器学习是人工智能的核心，是一门多领域交叉学科，涉及概率论、统计学、逼近论、凸分析、算法复杂度理论等多门学科。机器学习的目标是从有限的观察数据（样例）中通过算法学习出一般规律，学习一个预测模型，并利用这些规律对未知数据进行预测。机器学习的主要应用领域包括专家系统、认知模拟、规划和问题求解、数据挖掘、网络信息服务、图像识别、故障诊断、自然语言理解、机器人和博弈等。

##### Web数据挖掘

Web数据挖掘是一项综合性技术，包括以下几个方面：

1. **Web结构挖掘**：从表层Web结构的超链接中寻找知识。
2. **Web内容挖掘**：从网页内容中抽取有用的信息和知识。
3. **Web使用挖掘**：从记录每位用户点击情况的日志中挖掘用户访问模式。

数据挖掘常用的方法大多来自于机器学习这门学科，应用领域包括电信、银行、百货公司、超市、保险、信用卡、电子商务、税务部门、警察机关、医学等。

#### 知识发现

知识发现是指用数据库管理系统来存储数据，并使用机器学习的方法来分析这些数据，从而挖掘出大量数据背后隐藏的知识。这一过程称为数据库中的知识发现。

##### KDD过程

1. **实体（Entity）**：指的是具体的事物或概念，如人、地点、组织等。
2. **属性（Property）**：描述实体特征的信息，如人的年龄、地点的位置等。
3. **关系（Relation）**：表示实体之间的联系，如“属于”、“位于”等。

知识可以以形式化、简洁的方式表示，通常使用三元组（triple）的形式，即（实体，关系，实体/属性）。大量的三元组构成的知识库就成为了一个庞大的知识图。

##### 知识图谱构建

1. **命名实体识别（Named Entity Recognition, NER）**：识别文本中具有特定意义的实体，主要包括人名、地名、机构名等。
2. **实体链指（Entity Linking）**：将文档中的实体名字链接到知识库中特定的实体上。这涉及到实体识别和实体消歧两个经典问题。
3. **关系抽取（Relation Extraction）**：将文档中的实体关系抽取出来，主要涉及到词性标注、语法分析和依存关系树等技术。

##### 知识图谱应用

知识图谱在自动推理、问答系统等领域有广泛的应用。传统的语义解析方法将自然语言转化为一系列形式化的逻辑形式，而知识表示学习的方法则把知识库问答看做一个语义匹配过程，通过表示学习知识库以及用户问题的语义表示，得到低维空间的数值向量，实现问题和答案的向量匹配，进而计算问题-答案的得分，选择最优的候选答案。

## 第二讲 网络爬虫技术

爬虫的别名：Crawler，Spider，Robot (or bot)，Web agent，Wanderer,，worm

有名的爬虫例子：googlebot，scooter，slurp，msnbot

**爬虫是 web 数据管理的基础，是获取数据的主要办法**：

- 支持通用搜索引擎（Google、Yahoo、MSN/Windows Live、Ask等）
- 垂直（专业）搜索引擎，例如新闻、购物、报纸、食谱、评论等。
- 商业智能：跟踪潜在的竞争对手、合作伙伴
- 监视感兴趣的网站
- 邪恶方面：收集垃圾邮件、网络钓鱼的电子邮件

### 一、爬虫定义

爬虫一种自动获取网页内容的程序，是搜索引擎的重要组成部分，通俗的讲，也就是通过HTML源码解析来获得想要的内容。

爬虫不仅爬取网络上的文本，还要考虑到超链接，这样就能通过网页之间的连接爬取到其他网页的内容。 

![](https://s3.bmp.ovh/imgs/2024/03/04/1b8ceeb47ce44fa2.png)

需要注意的是，从一个网站不能爬取互联网上所有网站，因为有大量的网站不在互联网上（例如localhost）。

### 二、爬取过程

1. **获取（Fetch）**：首先，爬虫从种子URL开始，下载网页的HTML源代码文件。
2. **解析（Parse）**：随后，爬虫解析HTML源代码，识别其中的链接元素（例如`<a href=…>……</a>`）。
3. **提取URL（Extract URLs）**：从这些链接元素中提取出指向的URL。
4. **队列（Queue）**：将提取出来的URL放置在一个队列中。
5. **重复（Repeat）**：从队列中取出每个URL，重复上述的获取、解析和提取过程。
6. **停止条件（Stopping Condition）**：这个过程会持续进行，直到满足系统预设的一定停止条件，如爬取的深度、数量或时间限制等。

需要注意的是，互联网之间存在信息间隔，例如从山东大学的网站开始，不可能爬取到涩情网站的内容（上课就这么说的），同时，为了防止网站给爬虫设置陷阱，爬虫应该设置有限制器，例如爬取山东大学相关内容时，去掉指向国外网站的链接。

**过滤器**

过滤器主要用于确保爬虫只关注与目标任务相关的网页。这通常通过分析URL的结构和内容来实现，只有符合特定模式或条件的URL才会被加入到爬取队列中。过滤器的目的是减少不相关或低价值页面的爬取，优化爬虫的工作流程。例如，如果一个爬虫的任务是爬取特定网站上的新闻文章，那么过滤器可以设置为只接受包含`/news/`路径的URL。

**URL判重**

URL判重是指在将URL加入爬取队列之前，检查该URL是否已经被爬取或已存在于队列中。这一步骤是必要的，因为在Web爬取过程中，很容易从不同的页面中提取到相同的URL，如果不进行判重，爬虫可能会浪费大量资源去重复爬取相同的页面。判重通常通过维护一个已访问URL的集合来实现，每次提取到新URL时，都会先检查这个集合，如果URL已存在，则不再加入队列。

### 三、爬取框架

爬虫工作的流程，在爬取的过程中有以下需要处理的难点：

- 文档重复
- URL过滤器
- URL重复

![](https://s3.bmp.ovh/imgs/2024/03/04/ce1d16c64d50f799.png)

**爬取框架中的DNS查找**

在Web数据爬取框架中，DNS（域名系统）查找是一个关键环节。它负责将网站的域名转换为IP地址，使得爬虫能够访问目标网站。由于DNS服务是由分布在全球的一组服务器提供，因此查找的延迟可能较高，有时甚至达到几秒钟。此外，常见的操作系统实现的DNS查找是阻塞的，即一次只能处理一个外发请求，这可能会成为爬虫效率的瓶颈。

#### **URL规范化**

在Web数据爬取框架的解析阶段，一个关键的步骤是URL规范化。当从获取的文档中解析出链接时，一些提取的链接可能是相对URL。例如，在`http://en.wikipedia.org/wiki/Main_Page`页面中，可能存在一个指向`/wiki/Wikipedia:General_disclaimer`的相对链接。这个相对链接实际上等同于绝对URL `http://en.wikipedia.org/wiki/Wikipedia:General_disclaimer`。

- **确保一致性**：规范化过程确保所有的URL都以一种标准格式存储和处理，这对于后续的爬取、链接的去重以及数据的索引等操作至关重要。
- **解决相对URL问题**：在Web页面中，相对URL是非常常见的，它们依赖于当前页面的URL来指定目标资源的位置。通过规范化，可以将这些相对URL转换为绝对URL，从而使爬虫能够准确地访问到这些资源。

#### 文档指纹FP

爬虫大批量对文档内容进行快速判重，后续会讲对应的算法。

#### URL判重

在Web爬虫设计中，URL判重是一个关键的技术点，其目的是避免重复访问和处理相同的网页。由于搜索引擎在爬取过程中要访问大量的网页，有效的判重机制对于提高爬虫效率和减少资源消耗至关重要。

**访问标记**

访问标记是判重机制的核心，它用于记录每一个已访问过的网址。为了快速检查一个URL是否已经被访问过，并标记新的URL为已访问，通常会使用一个散列表（哈希表）来存储这些信息。

**散列函数**

为了减少散列表所占用的空间，通常不直接存储网址本身，而是存放网址经过散列函数计算出的散列值。常用的散列函数包括MD5、SHA-1等，它们可以将任意长度的输入（在这里是URL）转换成固定长度的输出。这样，不管实际的URL有多长，存储在散列表中的都是一个固定长度的散列值。

**效率和空间占用**

使用散列函数和散列表可以在平均情况下实现O(1)的查找和更新时间复杂度，这意味着无论已访问网址的数量有多少，检查一个URL是否被访问过的时间都是常数级别的。同时，由于只存储散列值而不是完整的URL，所需的存储空间也大大减少。

**直接寻址法**

直接寻址是另一种在特定场景下使用的方法，但由于它依赖于能够将URL直接映射到一个固定的地址空间内，这在处理大量或复杂的URL时可能不太实用。因此，对于Web爬虫而言，使用散列函数和散列表的方法更加普遍和有效。

### 四、爬虫必须具有的功能

爬虫具有的功能是评判一个爬虫好坏的标准。

#### 礼貌性

在Web数据爬取过程中，遵守服务器的访问控制策略和保持礼貌是非常重要的。这些策略和礼貌行为可以分为显式和隐式两种：

**隐式的礼貌**：

即使网站没有特别说明，爬虫也不应频繁访问同一个网站，以避免给网站服务器带来过重负担。这种做法是基于通用的网络礼仪和对其他用户服务质量的考虑。

**显式的礼貌**

根据网站管理员通过特定方式（如`robots.txt`文件）提供的说明，决定哪些内容是允许爬取的。这意味着爬虫应只爬取被明确允许的内容，并尊重网站对于爬虫访问的各种规定。

**Robots.txt 协议**

`Robots.txt`是一个长期以来用于网站根目录下的文本文件，通过它，网站可以告知爬虫哪些页面是可以被爬取的，哪些是禁止访问的。它基于1994年提出的一个协议，目的是让网站管理员能够更好地控制爬虫对网站资源的访问。

例如，以下`robots.txt`文件的内容指出：所有爬虫都不允许访问`/yoursite/temp/`目录下的内容，但是名为“searchengine”的爬虫除外：

```html
User-agent: *
Disallow: /yoursite/temp/
User-agent: searchengine
Disallow:
```

#### 鲁棒性

在Web数据爬取的过程中，鲁棒性是确保爬虫能有效、安全地执行任务的关键属性。这包括能力在面对各种网络环境和意外情况时保持稳定运作，具体挑战和应对策略包括：

1. **能从采集器陷阱中跳出**：设计爬虫时需要考虑如何避免或从爬虫陷阱中逃脱，这些陷阱可能是无限循环的动态生成页面，旨在阻止爬虫访问。
2. **处理恶意行为**：爬虫需要能够识别并规避恶意页面（可能包含有害软件的页面）和垃圾页面，这些页面不仅对用户无用，还可能对爬虫和服务器造成安全风险。
3. **面对非恶意页面的挑战**：即使是非恶意页面，也可能因为网络延时、带宽限制、网站镜像和重复页面等因素，给爬虫带来挑战。爬虫需要有策略地处理这些情况，以提高效率和准确性。
4. **决定爬取深度**：如何决定爬取网站URL层次结构的深度是一个策略问题。过深的爬取可能会消耗大量资源而收效甚微，而浅层爬取可能错过重要信息。爬虫需要在完整性和资源利用之间找到平衡点。

#### 性能和效率

在Web数据爬取的过程中，性能和效率是衡量爬虫设计成功与否的关键指标。为了实现高效的数据爬取，需要考虑如何充分利用可用的系统资源，包括处理器、存储器和网络带宽，并且优化爬取策略以抓取最有用的网页。

#### **充分利用系统资源**

爬虫应设计为能够在不同的系统环境中最大化资源使用效率，这包括合理分配处理器时间、有效管理存储空间以及优化网络带宽的使用。

**优先抓取有用的网页**

通常，网站的首页被认为是相当重要的，因为它往往链接到许多其他相关页面。因此，优先爬取这些关键页面可以提高爬虫的效率和数据的价值。

**搜索策略：深度优先 vs 广度优先**

深度优先搜索（DFS）和广度优先搜索（BFS）是两种基本的爬取策略。理论上，它们都能在相似的时间内完成对所有静态网页的爬取。然而，从实践角度考虑，BFS通常被认为优于DFS，因为它能更系统地覆盖网站结构，且在早期阶段就能抓取到更多有用的页面。然而，DFS的优势在于能减少网络通信的握手成本，特别是在分布式爬虫系统中，限定爬取深度有助于避免无限递归的情况发生，并且减少对深层次、信息价值较低页面的爬取。

**实际应用中的爬取策略**

实际中的网络爬虫通常不是简单的DFS或BFS，而是采用更复杂的基于优先级排序的下载策略。这种方法允许爬虫在有限的时间内最大化地爬取那些重要的网页。

#### 分布式

分布式爬虫系统通过在多台服务器上并行执行爬取任务来提高性能和效率，但这种架构同时也引入了一些挑战，特别是在URL去重和负载均衡方面。

**URL前沿（URL Frontier）**

在分布式爬虫中，维护一个高效的URL前沿（即待爬取URL列表）是至关重要的，以确保所有爬取线程始终保持忙碌状态。这个列表可以包含来自同一主机的多个页面，但需要避免同时抓取过多页面以免给单个主机造成过大负载。

**哈希表判重的挑战**

- 在单机爬虫系统中，通过在一台下载服务器上建立和维护哈希表来实现URL去重相对简单。
- 在分布式系统中，由于多台服务器并行下载网页，会遇到以下问题：
  - **问题1**：哈希表可能过大，单台服务器无法存储全部数据。
  - **问题2**：在开始下载前和下载完成后更新哈希表，需要在服务器之间进行通信，这种通信可能成为系统的瓶颈。

**解决方法**

1. **明确分工**：为每台下载服务器明确分工，即通过查看URL就能确定由哪台服务器负责下载。这通常通过URL的哈希值来实现，确保URL的分配既均匀又可预测。
2. **批量处理**：通过批量处理请求和更新操作来减少服务器之间的通信次数，从而减少通信开销。

#### 新鲜度

在网页的`<meta>`标签中会包含网页更新的信息，爬虫需要为更新较快的页面提高刷新率。同时，含有那些突增的搜索关键字的网站会得到较快的更新频率， Google就会优先对与这个主题有关的网站进行更新。

#### 功能可扩展性

支持多方面的功能扩展，例如处理新的数据格式、新的抓取协议等。

### 五、爬虫分类

实际的采集器往往是几种采集技术的结合。

#### 基于整个Web的信息采集(Universal Web Crawling)

是一种传统的采集方式，作为门户搜索引擎和大型的Web服务提供商的数据收集部分，从一些种子URL扩充到整个Web的信息采集。

**优点**：采集数据广，采集速度快，适用于广泛主题的搜索。

**缺点**：采集数据乱，数据利用率低，页面失效率高，采集周期长。

**典型代表**：Google Crawler，百度 

#### 增量式Web信息采集 (Incremental Web Crawling )

在页面刷新时，只需要采集新产生的或者已经发生变化的页面，而对于没有变化的页面不进行采集。

**预测变化的策略**

1. 基于统计的方法：观察网站的平均变化周期。
2. 基于数据建模的方法：通过网页中变化估计模型和参数。

**优点**：极大地减小数据采集量进而极大地减小采集时空开销 。

**缺点**：增加了一定的判别开销，网页相似度比较（后面详细讲）。

**典型代表**： Google Crawler，WebFountain。

#### 基于主题的Web信息采集(Focused Web Crawling )

通过给定特定的种子URL，选择性的搜寻那些与预先定义好的主题集相关页面进行采集。

该技术目前是研究热点，垂直搜索

**优点**：采集页面更加有针对性，采集效率更高。

**缺点**：采集速度较慢，判别相关性带来较大的开销。

**典型代表**：Focused Crwaler -- IIT&IBM

1. 采集系统首先保存一个经典的主题分类
2. 每个主题分类都保存若干个内容样本

#### 基于用户个性化的Web信息采集(Customized Web Crawling )

不同的用户对一个搜索引擎提交同一个检索词，他们期望的返回结果是不同的，通过用户兴趣制导或与用户交互等灵活手段来采集信息 。

1. 用户画像profile
2. 日志

**轻量级的信息采集，爬虫APP**

1. You-Get
2. 八爪鱼采集器
3. 集搜客GooSeeker
4. 后羿采集器

### 六、爬虫与法律

**爬虫背后的法律风险**

1. 为违法违规组织提供爬虫相关服务（验证码识别服务贩卖、SEO……）
2. 个人隐私数据抓取与贩卖
3. 利用无版权的商业数据获利
4. 侵犯著作权

**相关法律介绍**

- 非法获取计算机系统数据罪
  侵入国家事务、国防建设、尖端科学技术领域以外的计算机信息系统或者采用其他技术手段，获取该计算机信息系统中存储、处理或者传输的数据
- 侵犯商业秘密罪
  《反不正当竞争法》第九条，以不正当手段获取他人商业秘密的行为即已经构成侵犯商业秘密。而后续如果进一步利用，或者公开该等信息，则构成对他人商业秘密的披露和使用，同样构成对权利人的商业秘密的侵犯。
- 网络安全法
  如果爬虫在未经用户同意的情况下大量抓取用户的个人信息，则有可能构成非法收集个人信息的违法行为。
- 侵犯公民个人信息罪
  非法获取、出售或者提供行踪轨迹信息、通信内容、征信信息、财产信息五十条以上的；
  住宿信息、通信记录、健康生理信息、交易信息等其他可能影响人身、财产安全的公民个人信息五百条以上的；
  民个人信息五千条以上的便构成“侵犯公民个人信息罪”所要求的“情节严重”。

### 七、使用python  Padas模块 直接读取网页中的表格

#### HTML表格

表格可以规则的存放数据，可以帮助网页布局，基本结构如下：

```html
<table border=1>
	<tr>
		<td>学号</td>
		<td>姓名</td>
		<td>成绩</td>
	</tr>
	<tr>
		<td>001</td>   <td>李明</td>  <td>85</td>
	</tr>
	<tr>
		<td>002</td>  <td>王刚</td>  <td>91</td>
	</tr>
	<tr>
		<td>003</td>   <td>张玲</td><td>78</td>
	</tr>
</table>
```

`<table>`、`<tr>`、`<td>`、`<th>`是表格中最常用的四个标记，其余部分详见https://www.w3school.com.cn/html/html_tables.asp

#### Padas模块

pandas 是基于NumPy的一种工具，为了解决数据分析任务。Pandas 纳入了大量库和一些标准的数据模型，提供了高效地操作大型数据集所需的工具。

Pandas  是python的一个数据分析包。Pandas的名称来自于面板数据（panel data）和python数据分析（data analysis）。

- panel data是经济学中关于多维数据集的一个术语。

pandas 提供了大量能使我们快速便捷地处理数据的函数和方法。

**NumPy（Numerical Python）**

Python的一种开源的数值计算扩展，可用来存储和处理大型矩阵，支持大量的维度数组与矩阵运算，此外也针对数组运算提供大量的数学函数库。

## 第三讲 网页分析技术

本章主要讲如何使用代码实现爬虫，老师说考试不考代码，大伙就看看吧。

### 一、正则表达式

**介绍**

正则表达式是对字符串操作的一种逻辑公式，就是用事先定义好的一些特定字符、及这些特定字符的组合，组成一个“规则字符串”，这个“规则字符串”用来表达对字符串的一种过滤逻辑。

正则表通常被用来检索、替换那些符合某个模式(规则)的文本。

**语法**

构造正则表达式的方法是用多种元字符与运算符将小的表达式结合在一起来创建更大的表达式。

正则表达式的组件可以是单个的字符、字符集合、字符范围、字符间的选择或者所有这些组件的任意组合。

详情见网站[Python re模块](https://www.cnblogs.com/shenjianping/p/11647473.html)。

**基于正则表达式的信息提取**

1. 在获取数据前应尽量去除无用部分

   匹配出标签块后将其替换为空字符串，去掉注释、css、js 部分。

2. 提取网页内的链接

   选取链接块，提取地址属性，交给爬取调度程序筛选爬取。

3. 提取网页标题

4. 提取网页内的文本

   - 去除HTML代码中所有封闭标签；（`<[^>/][^>]*/>`匹配一个封闭的内部不嵌套任何内容的标签）

   - 将代码使用`</?[^>]*>`表达式分割为一组文本；（`</?[^>]*>`匹配一个标签的开始部分或者结束部分）

   - 将这组文本中的每个文本按换行符和大量空白分割为更多的文本。

   - 最后得到的这组文本即为网页内的文本部分。

**正则表达式匹配特点**

正则表达式匹配速度快，但表达能力较弱，只具有正规文法的表示能力。

在对网页内容的信噪比要求不高的情况下可以使用基于正则表达式匹配的爬取程序。

### 二、基于HTML DOM提取内容

**文档对象模型**

DOM 是一种跨平台和语言独立的接口，允许程序和脚本动态地访问和更新文档的内容、结构和样式。它将一个XML或HTML文档转换成一个对象模型（对象集合），这些对象表示文档的结构，并可以通过编程方式进行操作。

**“随机访问”协议**：这一机制允许在任何时间点访问文档的任何部分，无需按顺序逐行阅读文档。这种访问方式极大地提高了处理文档的灵活性和效率，使得开发者可以直接访问文档中的特定元素，以及修改、删除或插入新的数据。

**树状结构**：DOM 将HTML或XML文档视为一棵树，其中每个节点代表文档中的一个元素（比如标签）、文本或属性。根节点代表整个文档，而其他节点则形成了树的分支，代表文档的结构层次。这种树状结构使得文档的遍历、编辑和更新变得直观且高效。

**正则表达式与DOM树方法的比较**

- 正则表达式匹配

  正则表达式匹配速度快，但表达能力较弱，只具有正规文法的表示能力。

  在对网页内容的信噪比要求不高的情况下可以使用基于正则表达式匹配的爬取程序

- HTML DOM树

  提取HTML DOM树提取在解析HTML时速度较慢，但其表达能力相当于上下文无关文法。

  在网页自动分类等需要进行网页去噪处理的情况时使用基于HTML DOM树的爬取程序。

**HTML解析器**

HTML解析器的主要任务是将HTML代码解析成浏览器能够理解的结构，通常是一个解析树。这个解析树反映了HTML文档的结构，包括各种元素和属性的层次关系。解析树使得浏览器能够渲染出可视化的网页。

**jsoup**

- **直接解析URL或HTML内容**：jsoup能够直接从给定的URL地址下载HTML文档，或者解析一个HTML字符串。这使得处理网络上或本地的HTML文档变得非常方便。
- **简洁的API**：jsoup提供了一套简洁而强大的API，允许开发者通过DOM方法、CSS选择器，以及类似jQuery的操作方式来读取和修改HTML文档。
- **数据提取和操作**：开发者可以使用jsoup轻松地提取HTML文档中的数据、操作文档结构和元素属性等。

### 三、 Beautiful Soup模块

**bs4库**

Beautiful Soup是一个用于解析HTML和XML文档的Python库。它能够从网页中提取数据，是进行网页数据抓取和网页内容解析的常用工具之一。Beautiful Soup通过提供简单的方法和Pythonic的方式来处理导航、搜索、修改分析树等功能，大大简化了网页文档的解析工作。

ppt介绍了几个基础的方法，详情可见[Beautiful Soup 中文文档](https://beautifulsoup.cn/)

**编码格式**

在进行文档保存时要设置编码格式，否则会出现乱码，关于编码推荐视频[锟斤拷�⊠是怎样炼成的——中文显示“⼊”门指南【柴知道】_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1cB4y177QR/?spm_id_from=333.337.search-card.all.click&vd_source=9654359f3fbbf36d4e094add236fe34e)，作为科普视频能讲明白ANSI,unicode与utf-8的区别。

### 四、  python爬虫框架 Scrapy

Scrapy是一个快速、高级的Web爬虫框架，用Python开发，旨在用于爬取网站数据、提取结构性数据，并能够用于一系列的数据挖掘、自动化处理工作。

ppt内容比较散，推荐大家直接去看[Scrapy 入门教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/w3cnote/scrapy-detail.html)这个，上课内容大概包括创建scrapy项目以及根据不同需求进行相关配置。

**Scrapy 核心特点**

- **易于使用**：Scrapy为用户提供了一个命令行工具，可以快速地创建项目、定义抓取项以及启动爬虫。
- **高效**：Scrapy使用Twisted异步网络库进行数据下载，提高了爬取效率，使其成为处理大规模数据抓取的理想选择。
- **灵活**：Scrapy允许开发者自定义中间件、扩展、管道等组件，以适应各种网站结构和需求。
- **强大的选择器**：Scrapy内置了对XPath和CSS选择器的支持，使得从网页中提取数据变得更加方便和高效。
- **可扩展性**：Scrapy架构的可扩展性允许用户添加自己的功能到框架中，例如新的Item Pipeline、下载器中间件等。
- **内置支持多种输出格式**：Scrapy支持将抓取的数据导出到多种格式，如JSON、CSV、XML等。

**Xpath**

XPath（XML Path Language）是一种在XML文档中查找信息的语言。它可以用来在XML文档中对元素和属性进行遍历或搜索。XPath提供了非常丰富的表达式，可以进行复杂的层次结构、属性和元素的查询。由于HTML可以被看作特殊的XML，XPath同样适用于HTML文档的搜索和数据提取。

**CSV文件**

CSV（Comma-Separated Values）文件是一种常用的文本文件格式，用于存储表格数据。CSV文件以纯文本形式存储数据，其中的每一行代表表格中的一行，每个单元格的数据则通常由逗号（`,`）分隔开。由于其简单性和易于阅读的特点，CSV格式被广泛应用于数据交换和数据导入导出功能中。

**yield**

在Python中，`yield`是一个非常重要的关键字，它被用来从一个函数返回一个值，但与`return`不同的是，`yield`提供了一种生成器（generator）的实现方式，允许函数在保持当前执行状态的情况下暂时将控制权交还给调用者。这意味着函数可以在需要时产生一系列的值，而不是一次性返回所有值。

### 五、元搜索引擎