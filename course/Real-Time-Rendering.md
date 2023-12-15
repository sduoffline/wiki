---
title: 实时渲染
description: 很惭愧，就做了一点微小的贡献
published: true
date: 2023-12-15T12:57:25.566Z
tags: 
editor: markdown
dateCreated: 2023-12-15T09:04:29.818Z
---

# Notes on Real-time Rendering

> 「我即将融入剧烈争斗的大人世界，要在那里边孤军奋战，必须变得比任何人都坚不可摧。」

## 图形学回顾

现代图形学流水线：（模型）-> 顶点处理 -> 三角面片处理 -> 光栅化 -> 片元（Fragment）处理 -> 帧缓冲（Frame buffer）操作

VBO（Vertex Buffer Object）：GPU中用来存放模型的区域，和obj文件非常相似。

顶点着色器的工作：

1.   顶点变换。位置坐标等。
2.   片段着色器中的要用的量。比如texture coordinates。

GLSL在GAMES 202中还没有到in/out的时代。顶点着色器的主要关键字如下：

-   `attribute`。顶点属性，只出现在顶点着色器中。
-   `varying`。在片段着色器中有同名定义，顶点着色器写好送给片段着色器。
-   `uniform`。相当于全局变量，顶点着色器和片段着色器都可以访问。

片段着色器的主要关键字如下：

-   `varying`。已经做好插值了的值，从顶点着色器来的。
-   `uniform`。相当于全局变量，顶点着色器和片段着色器都可以访问。

### Rendering Equation

描述了光线的传播
$$
L_{o}(p, \omega_{o})=L_{e}(p, \omega_{o})+\int_{H^{2}}f_{r}(p, \omega_{i}\rightarrow\omega_{o})L_{i}(p, \omega_{i})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}
$$
其中：

-   $L_{o}(p, \omega_{o})$ 为outgoing radiance。
-   $L_{e}(p, \omega_{o})$ 为emission。它表示表面自身发出的辐射度，比如自发光材质。
-   $f_{r}(p, \omega_{i}\rightarrow\omega_{o})$ 为BRDF。它接受两个向量作为输入：入射光线的方向和观察者的视线方向。然后，它输出一个表示光线在该表面上反射的强度的值。不严谨地说，带不带$\cos$项其实是无所谓的，都是BRDF。
-   $L_{i}(p, \omega_{i})$ 为incident radiance。它表示了从周围环境中来自方向$\omega_{i}$的光线的能量。

这样，方程就考虑了全局光照（global illumination）的影响。通过对周围环境中所有入射方向的辐射度进行积分，可以考虑到光线在环境中的散射、反射和遮挡等效应，从而获得更真实的渲染结果。在积分中，$\cos\theta_{i}$ 表示了入射光线的入射角度与表面法线的夹角的余弦值。这是因为入射角度越接近表面法线，入射光线对表面的影响越大。$\cos\theta_{i}$ 的作用是对入射光线的辐射度进行调整，使得入射角度较小的光线在积分中贡献更多的反射辐射度。这个 $\cos\theta_{i}$ 项是由于光线在表面上的能量分布与入射角度有关。通过乘以 $\cos\theta_{i}$，可以考虑到光线在不同入射角度下的能量变化，从而更准确地计算出反射辐射度。在渲染方程中，$L_{i}(p, \omega_{i})$ 与 BRDF $f_{r}(p, \omega_{i}\rightarrow\omega_{o})$ 相乘，表示了入射光线与表面的相互作用。这个乘积表示了从方向 $\omega_{i}$ 入射的光线经过表面反射后的辐射度。

而在**实时渲染**中，渲染方程如下：
$$
L_{o}(p, \omega_{o})=\int_{\Omega^{+}}L_{i}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}V(p, \omega_{i})\,\mathrm{d}\omega_{i}
$$
显式地引入visibility，本质上和上面的渲染方程等价。

其中：

-   $L_{o}(p, \omega_{o})$ 为outgoing lighting。
-   $L_{i}(p, \omega_{i})$ 为incident lighting（from source）。
-   $f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}$ 为（cosine-weighted）BRDF。
-   $V(p, \omega_{i})$ 为visibility。它描述了从点 p 出发沿方向 $\omega_{i}$ 的光线是否能够到达光源或其他可见点。
-   $\int_{\Omega^{+}}$ 表示对所有入射方向 $\omega_{i}$ 进行积分。$\Omega^{+}$ 表示半球面，即所有入射方向的集合。

------

对于渲染来说，渲染方程中的光应该要互相影响。假设所有的光都不反射，只渲染来自光源的光，称为直接光照（Direct Illumination）；加上间接光照（例如one-bounce的间接光），统称全局光照。

## 实时阴影

### Shadow Mapping

SM是一种2-Pass的算法，所谓2-Pass或者说两趟，就是说场景会被渲染两遍。

第一次渲染从光源的视角渲染场景，渲染场景中可见物体的深度图（可见的，也就是说深度最浅的）。

第二次从相机用light pass中拿下的Shadow Map（是一种texture），渲染整个场景。

SM是一种完全在图像空间中的算法。

优点：无需关注场景的几何关系，一旦Shadow Map生成，就可以直接用阴影图，而不必再看原场景。

缺点：会产生自遮挡（self occlusion）和走样问题（aliasing issue）。

由于Shadow Map记录的深度值是不连续的（所以实质上可以看成是阶梯状的锯齿，上面的锯齿当然可以遮挡下面的锯齿），因此可能产生自遮挡的问题，在不该产生阴影的地方产生了阴影。所以显然，最不容易自遮挡的地方就是光线垂直照射的时候，而当光线非常偏（几乎和物体表面平面平行的时候），自遮挡最严重。

Solution：认为发生遮挡，不仅要深度有大小关系，而且要“明显地”有大小关系，那就是说，如果差距不大，那就不认为发生了遮挡，也就不会产生自遮挡。即加一个bias。然而，添加bias虽然可以有效缓解自遮挡，但却可能造成悬空，也就是说，本来应该遮挡的地方，因为在bias阈值内，因此渲染看上去不被遮挡，就造成了阴影悬空。

*参考阅读：[《GAMES202：高质量实时渲染》1 实时阴影：阴影映射（Shadow Mapping）、PCSS、VSSM、SDF Shadows](https://zhuanlan.zhihu.com/p/563672775)，第一章**阴影映射（Shadow Mapping）***

### Second-depth Shadow Mapping

其实就是，Shadow Map不仅存最小深度，还同时存储次小深度。将最小深度和次小深度取平均，即得到midpoint。用midpoint当作遮挡关系的threshold。~~但是实际上没人用。~~

缺点：

-   要求物体必须watertight，比如平面的纸就不行。
-   实现稍微复杂了点，而且常数变大了，在RTR也很关注常数

### 来点数学

**Schwarz不等式**
$$
\left [ \int_{a}^{b} f(x)g(x) \, \mathrm{d}x\right ]^{2} \leq \int_{a}^{b} f^{2}(x) \, \mathrm{d}x \int_{a}^{b} g^{2}(x) \, \mathrm{d}x
$$
**Minkowski 不等式**
$$
\left( \int_a^b \left[ f(x) + g(x) \right]^2 \,\mathrm{d}x \right)^{\frac{1}{2}} \leq \left( \int_{a}^{b} f^{2}(x) \,\mathrm{d}x \right)^{\frac{1}{2}} + \left( \int_{a}^{b} g^{2}(x) , \,\mathrm{d}x \right)^{\frac{1}{2}}
$$
**RTR中的近似**

不关心不等，关心*近似相等*。也就是将这些不等式当成约等式用。

比如：
$$
\int_{\Omega}f(x)g(x)\,\mathrm{d}x\approx \frac{\int_{\Omega}f(x)\,\mathrm{d}x}{\int_{\Omega}\,\mathrm{d}x}\cdot\int_{\Omega}g(x)\,\mathrm{d}x
$$
什么时候这么做准确呢？

-   当$g$的积分区限很小的时候
-   当$g$足够光滑

有一个满足就能大胆约等。

### 化简渲染方程

运用上述约等式，即可将渲染方程：
$$
L_{o}(p, \omega_{o})=\int_{\Omega^{+}}L_{i}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}V(p, \omega_{i})\,\mathrm{d}\omega_{i}
$$
约等于：
$$
L_{o}(p, \omega_{o})\approx \frac{\int_{\Omega^{+}}V(p, \omega_{i})\,\mathrm{d}\omega_{i}}{\int_{\Omega^{+}}\,\mathrm{d}\omega_{i}}\cdot \int_{\Omega^{+}}L_{i}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}
$$
也就是把可见性项拿出来。

什么时候足够准确？

1.   积分区限足够小。点光源/方向光源时就可以，足够准确。
2.   积分式足够光滑。光照各处几乎不变的光源或者是diffuse的BRDF时，就比较准确。

所以反过来说，Shadow Mapping不准的时候，就是：

-   环境光（可以视作很大的面光源）导致BRDF不够smooth
-   材质很glossy，这样BRDF也不够smooth

### PCF

 Percentage Closer Filter（PCF）是一种用于阴影贴图采样的滤波器技术，用于减少阴影贴图采样带来的锯齿边缘。PCF 通过在每个阴影像素周围进行多次采样，并根据采样结果计算出阴影的平均值，从而实现柔和的阴影边缘。

*参考阅读：[《GAMES202：高质量实时渲染》1 实时阴影：阴影映射（Shadow Mapping）、PCSS、VSSM、SDF Shadows](https://zhuanlan.zhihu.com/p/563672775)，第二章**PCF（Percentage Closer Filtering）反走样***

PCF本身是为了做抗锯齿的，不过也能用来生成软阴影，那就是PCSS。

PCF对于遮挡检验得到的01矩阵做filtering，而不是对最后的结果做filtering。

**为什么不是对Shadow Map做filtering？**

因为就不是这么用的，对Shadow Map做filtering毫无意义。

做法：

1.   对每个fragment做多个深度比较（比如7x7）
2.   对比较得到的01值做平均，也可以加权平均

Filter的size越小，阴影越硬；filter越大，阴影越软。那么当size很大时，PCF可以用来做软阴影。

### PCSS

PCSS，即Percentage Closer Soft Shadows，是一种用于实时渲染中生成更真实、柔和阴影的技术。它是对传统 Shadow Mapping 方法的改进，旨在解决硬阴影（Hard Shadows）的锯齿边缘和不真实外观的问题。

PCSS 的核心思想是考虑阴影中的光源大小和光源的可见性，以模拟光在阴影区域的衍射和柔和效果。传统的 Shadow Mapping 只考虑了物体是否在阴影中，而没有考虑光源的大小和阴影的柔和程度。

PCSS发轫于PCF，因为PCF本身有使阴影软化的效果。不过关键在于，**遮挡物和阴影的距离影响filter的size**。

算法：

1.   从Shading point找，被遮挡自然就是被遮挡物（blocker）遮挡了。将blocker的深度记下来，取平均，得到average blocker depth
2.   半影估计。用average blocker depth来决定PCF的size
3.   做PCF。因为size能动态调整，因此距离近的阴影硬，远的软，符合认知

第一步做blocker search时，一种方法是采用固定的范围，例如 4x4。另一种更好的方法是动态计算遮挡范围，我们计算Shadow Map的时候在光源处设置过相机，把Shadow Map放在相机的近截面，然后将光源和要渲染的点相连，在Shadow Map上截出来的面就是要查询计算平均遮挡距离的部分，这部分的深度求一个均值，就是blocker到光源的平均遮挡距离。

PCF的实质是加权平均，是filter，是卷积。
$$
\left[ w\ast f \right](p)=\sum_{q\in \mathcal{N}(p)}w(p,q)f(q)
$$
在PCSS中，则有：
$$
V(x)=\sum_{q\in \mathcal{N}(p)}w(p,q)\cdot\chi^{+}\left[ D_{SM}(q)-D_{scene}(x) \right]
$$
其中，$\chi^{+}$是一个符号函数，当变量为正时值为$1$，否则为$0$。所以这玩意就是一个阴影比较的结果的数学表达式。

也可以看到，PCF并不是在filter阴影图，因为：
$$
V(x)\ne\chi^{+}\left\{ \left[w\ast D_{SM}\right](q)-D_{scene}(x) \right\}
$$
更不是直接对生成的图像做filter。

------

PCSS效果很好，但是朴素方式非常慢，因此需要加速。

慢的原因在于，在第一步和第三步中，都需要查询区域内全部*纹素*（texel，它包含纹理图像中的一个像素的信息）的深度信息。

最简单的方式就是不查询全部，而是采样。这样就是近似，缺点是会产生noise。最朴素的想法就是生成图像后，再在图像空间上滤波降噪来找补。

*参考阅读：[《GAMES202：高质量实时渲染》1 实时阴影：阴影映射（Shadow Mapping）、PCSS、VSSM、SDF Shadows](https://zhuanlan.zhihu.com/p/563672775)，第四章**PCSS（Percentage Closer Soft Shadows）***

### VSSM

VSSM是加速PCSS的一种新思路。Variance Soft Shadow Mapping（VSSM）通过在深度贴图中存储更多的信息来解决这些问题。具体来说，对于每个像素，传统 Shadow Mapping 只存储一个深度值，而 VSM 存储两个值：深度值和深度值的平方。这两个值的组合可以提供更多的信息，用于计算阴影的柔和度。

做PCF的过程，关键就在找区域内01值的比例，这个过程其实相当于在考试中估计自己的分数对应的排名。为了避免遍历所有的信息（所有texel的深度 / 所有学生的分数），那就相信正态分布。

确定正态分布，就只需要两个参数：

1.   均值
2.   方差

这样，就是说要从SM快速得到这两个参数，以确定正态分布，来快速估算区域内的01值的比例。

快速估算的方法，一方面可以直接查表（就像我们在概率论课中做的那样），另一方面可以用更简单的方式，即运用*Chebyshev*不等式估计。

不过到这里，只是优化了第三步。但是第一步还没什么优化，因为虽然得到区域平均深度，但是第一步需要的并不是它，而是遮挡物的平均深度。对于这里，有关系式：
$$
\frac{N_{1}}{N}\mathcal{z}_{unocc}+\frac{N_{2}}{N}\mathcal{z}_{occ}=\mathcal{z}_{avg}
$$
其中，$\frac{N_{1}}{N}$就是非遮挡物的比例，$\mathcal{z}_{unocc}$就是非遮挡物的平均深度；第二项同理。

那么，如何估计$\frac{N_{1}}{N}$、$\frac{N_{2}}{N}$呢？那就正是用正态分布、*Chebyshev*不等式的时候。

就立刻有：
$$
\frac{N_{1}}{N}=P(x>t)\\
\frac{N_{2}}{N}=1-P(x>t)
$$
现在，求的是$\mathcal{z}_{occ}$，两个系数能用上式估计，$\mathcal{z}_{avg}$是均值，就只有$\mathcal{z}_{unocc}$不知道了。于是大胆假设：

$\mathcal{z}_{occ}=t$，也就是说，假设非遮挡物的深度都和shading point的深度一致。

至此，VSSM流程结束。

#### 均值

最简单的方式就是Mipmap，缺点在于Mipmap毕竟是不准的，不同层级间还要插值，而且只能做正方形的。

更好的方式是做Summed Area Tables（SAT）。这事就很好做了，这玩意就是二维前缀和。

#### 方差

获取方差的方式是：
$$
Var(X)=E(X^{2})-E^{2}(X)
$$
期望就是均值，因此就拿下了，只要让SM存的不仅是深度，再加个通道记录深度平方就可以了。

#### Chebyshev不等式

对于单峰的概率密度函数，$t$大于均值，有：
$$
P(x>t)\le\frac{\sigma^{2}}{\sigma^{2}+(t-\mu)^{2}}
$$
其中$\mu$是均值，$\sigma^{2}$是方差。

这个式子对于随机变量的分布并没有要求。

### MSM

当VSSM的假设被挑战时，生成的效果就不太好，主要表现为漏光，在不该亮的地方亮了。

比如，当场景相对复杂时，假设服从正态分布还是合理的；但当场景非常简单时，它反而不一定服从正态分布，这样就导致估计比例这件事情的误差变得很大。变暗是可接受的，但变亮则非常显眼。

Moment Shadow Mapping（MSM）改进了VSSM。它使用多阶的moments（矩），来表示复杂函数。比如改进VSSM，那么就在生成SM的时候，记录$z$，$z^{2}$，$z^{3}$，$z^{4}$，这样就能更精准地拟合CDF，也就突破了高斯分布的局限性，能够有效改进VSSM。

球谐函数（Spherical Harmonics）是定义在单位球面上的一组正交函数，SH就可以用来做$z$函数。

### Distance Field Soft Shadows

距离函数：定义空间中的任何一个点到某物体表面上的最近距离。SDF就是Signed Distance Field，也就是说记录的是有向距离。

在传统方式中，如果对移动边界做插值，那么只能得到模糊的边界，而不是正确移动的边界。SDF则可以插值出正确的边界。

#### Ray Marching

Ray Marching想要做的事是找到光线和物体的交。Ray Marching  的基本思想是从视点发射射线，然后沿着射线进行步进，直到达到场景中的物体表面或达到最大步进距离。在每个步进点上，算法会根据场景中的几何形状和材质属性进行采样，并根据采样结果调整射线的步进距离。这样，算法可以有效地探测到场景中的交点，并进行光照计算。

从点$p$到某个点做ray marching，一个关键思想是：SDF意味着安全距离（不然的话SDF的值就会被范围内存在的物体更新了），所以可以快速来到$SDF(p)$（画出来是一个圆或者球）的边缘，然后重复上述过程。

#### SDF Shadow Mapping

可以用SDF来**近似**得到范围内有多少遮挡的部分。还是运用“安全距离”的思想，这时候一个点$p$同它的安全距离画出来的球，从视点过去其实就是一个cone（锥体）。这样，相当于运用SDF获得了“安全角度”。

根据“安全角度”，就可以估算出遮挡的量。如果安全角度非常大，意味着大范围内都没有遮挡，那么自然就很亮，visibility近似为1；反之同理。

那么如何求的“安全角度”呢？

还是做Ray Marching的流程，假设步进经过三个点$p_{1}$，$p_{2}$，$p_{3}$，那么就能得到各自对应的$\theta_{1}$，$\theta_{2}$，$\theta_{3}$。取最小的$\theta$就行了。

不过计算角度虽然可以用反三角函数轻松搞定，但是反三角函数对于实时渲染而言也太过复杂了，因此有另一种方式计算。

与其使用：
$$
\arcsin\frac{SDF(p)}{p-o}
$$
不如使用：
$$
\min\left\{ \frac{k\cdot SDF(p)}{p-o}, 1.0\right\}
$$
其中$p$是找出来最小的角度对应的点，$o$是视点。使用下面这种方式，就是认为这个比值已经足够可以表达大小关系，不需要用反三角函数算那么精确。

$k$的值可以调节软硬阴影的“硬度”。

优点：

-   快（但这个有点作弊，因为生成SDF是不计入软阴影渲染时间的）
-   高质量

缺点：

-   需要预计算SDF
-   高额的存储开销
-   接缝处等易有Artifact

## 实时环境光照

环境光贴图有Sperical map和Cube map，但是我们在渲染这里暂时不关心。我们只关心一个shading point能得到来自各个方向的光。

可以称之为Image-Based Lighting（IBL），也就是说如何（在不考虑阴影的情况下）对一个点着色。

既然不考虑阴影，那么自然渲染方程中的Visibility项就不用考虑了。那么有：
$$
L_{o}(p, \omega_{o})=\int_{\Omega^{+}}L_{i}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}
$$
通用方法：Monte Carlo。算任何数值积分都行。缺点是需要大量样本才行。

如何才能避免采样呢？

### 简化Lighting term

容易发现：

-   如果BRDF比较glossy，那么反射光场的lobe其实比较细长（体现为高光），积分区限就小。
-   如果BRDF比较diffuse，那么反射光场的lobe覆盖很大的区域，但是漫反射的lobe却很光滑。

于是：
$$
\int_{\Omega}f(x)g(x)\,\mathrm{d}x\approx \frac{\int_{\Omega_{G}}f(x)\,\mathrm{d}x}{\int_{\Omega_{G}}\,\mathrm{d}x}\cdot\int_{\Omega}g(x)\,\mathrm{d}x
$$
这一近似就能拿来用了。这里把$f$函数拿出来后，积分区限也只要取在$g$函数有值的地方就行了。

正是因为BRDF总是适合运用上述近似，就可以把上面的渲染方程改写为：
$$
L_{o}(p, \omega_{o})\approx \frac{\int_{\Omega_{f_{r}}}L_{i}(p, \omega_{i})\,\mathrm{d}\omega_{i}}{\int_{\Omega_{f_{r}}}\,\mathrm{d}\omega_{i}}\cdot \int_{\Omega^{+}}f_{r}(p, \omega_{i}, \omega_{o}) \cos{\theta_{i}}\,\mathrm{d}\omega_{i}
$$
这就是把Lighting term从乘积的积分中拆出来了。

而拆出来的Lighting term其实就是filtering，把环境光贴图给模糊了。

尤其是，这个事是prefiltering，因此在渲染的时候拿来用就可以了。不同大小的filtering，还可以用插值算中间结果，也就能得到任意filter size的环境光贴图。

------

这样以后，和过去根据BRDF的lobe去查询区域相比，现在只需要（如果是glossy的话）直接冲着镜面反射方向要值就行了，因为那个值是filter过的，因此能表示一片区域；如果是漫反射，就直接问normal方向要平均或者拿全图平均。这样，只要一次查询就能拿到一个区域的值，就很好。

### 预计算

$$
L_{o}(p, \omega_{o})\approx \frac{\int_{\Omega_{f_{r}}}L_{i}(p, \omega_{i})\,\mathrm{d}\omega_{i}}{\int_{\Omega_{f_{r}}}\,\mathrm{d}\omega_{i}}\cdot \int_{\Omega^{+}}f_{r}(p, \omega_{i}, \omega_{o}) \cos{\theta_{i}}\,\mathrm{d}\omega_{i}
$$

对于这个式子，前半部分已经搞定了，但是要做好后半部分还是相当麻烦的。如果说对于各种BRDF都做预计算，那么这个空间是相当巨大的。

如何让它可以预计算呢？

回顾Microfacet BRDF，有：
$$
f(\mathbf{i},\mathbf{o})=\frac{\mathbf{F}(\mathbf{i},\mathbf{h})\mathbf{G}(\mathbf{i},\mathbf{o},\mathbf{h})\mathbf{D}(\mathbf{h})}{4(\mathbf{n},\mathbf{i})(\mathbf{n},\mathbf{o})}
$$
其中：

-   $\mathbf{h}$半程向量，$\mathbf{o}$指向光源，$\mathbf{i}$指向视点
-   $\mathbf{F}(\mathbf{i},\mathbf{h})$是Fresnel项
-   $\mathbf{G}(\mathbf{i},\mathbf{o},\mathbf{h})$是shadowing-masking term
-   $\mathbf{D}(\mathbf{h})$是法线的distribution

关注Fresnel项和NDF项。

**Schlick近似**

Schlick近似可以用来近似估计Fresnel项。
$$
R(\theta)=R_{0}+(1-R_{0})(1-\cos{\theta})^{5}\\
R_{0}=(\frac{n_{1}-n_{2}}{n_{1}+n_{2}})^{2}
$$
其中，$R_{0}$是初始反射率，是一个颜色。 改变$\theta$即可近似Fresnel项。

**Beckmann分布**
$$
\mathbf{D}(\mathbf{h})=\frac{e^{-\frac{\tan^{2}\theta_{h}}{\alpha^{2}}}}{\pi\alpha^{2}\cos^{4}\theta_{h}}
$$
其中，$\alpha$表示材质的粗糙程度。改变$\theta_{h}$（其实和上面的$\theta$近似）即可近似NDF项。

------

经过这两项的近似，预计算的变量就只有：$R_{0}$，$\alpha$，$\theta$。但是三维还是太多。

将Schlick近似带入，得：
$$
\int_{\Omega^{+}}f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}\approx\\ R_{0}\int_{\Omega^{+}}\frac{f_{r}}{F}(1-(1-\cos{\theta_{i}})^{5})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}+\int_{\Omega^{+}}\frac{f_{r}}{F}(1-\cos{\theta_{i}})^{5}\cos{\theta_{i}}\,\mathrm{d}\omega_{i}
$$
这样，积分式的基础反射率就被拆出来了，剩下的式子中不再含有基础反射率$R_{0}$，只有表示roughness的$\alpha$和入射角$\theta_{i}$。

这样，积分值就可以预计算出一张二维图，以表示roughness的$\alpha$和$\cos{\theta}$作为坐标轴，做表，这样的图相当于一张纹理。要用积分值，就查表就行了。

By the way，这种方法叫Split Sum。

*参考阅读：[《GAMES202：高质量实时渲染》2 实时环境光照：Split Sum、PRT](https://zhuanlan.zhihu.com/p/563676455)，第二章 **The Split Sum Approximation**。*

### PRT

有了Environment Lighting后，在RTR中得到物体各个地方的阴影是非常难的。

两个对环境光的理解：

-   既然环境光来自四面八方，那么每个方向都可以视作是一个光源，也就转化成了 many-light problem。这样的话开销就线性于SM的开销。
-   视作一个采样的问题。知道shading point上半球各个方向不同的光线，解渲染方程，就要采样。难度在于，从shading point到各个方向的visibility term是不好知道的。此外，BRDF可能是很高频的，特别是glossy的BRDF；环境光（L项）的support其实很大。这样积分式中，$L_{i}$、$f_{r}$、$V$项都拆不出来。

$$
L_{o}(p, \omega_{o})=\int_{\Omega^{+}}L_{i}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}V(p, \omega_{i})\,\mathrm{d}\omega_{i}
$$

<div align="center"><i>（回看Rendering Equation）</i></div>

这里提了一嘴**环境光遮蔽**（Ambient Occlusion，后面会提），用AO近似Visibility是可以的，但是前提是认为环境光是Constant Environment Lighting（不能是任意环境光）。

Industrial solution：从最亮的光源（比如通常是太阳）下生成一个阴影（或者多一点，两三个差不多得了）。

------

PRT（Precomputed Radiance Transfer）虽然预计算了，但是做这个做的还挺好的。

*参考阅读：[《GAMES202：高质量实时渲染》2 实时环境光照：Split Sum、PRT](https://zhuanlan.zhihu.com/p/563676455)，第四章 **PRT**。*

首先，乘积的积分可以视作滤波（因为可以视作卷积）。
$$
\int_{\Omega}f(x)g(x)\,\mathrm{d}x
$$
这个可以看做时域上两个信号$f(x)$和$g(x)$的卷积，那么也就可以看作是频域上两个信号频谱的乘积。

如果两个频谱中，有一个是低频的，那么它们相乘的结果就是低频的。结果低频，意味着信号足够smooth。（有点像Low Pass Filter）

#### Basis Functions

用基函数的线性组合可以拟合一个函数，用来拟合的这组函数就叫基函数（Basis Functions）。
$$
f(x)=\sum\limits_{i}c_{i}\cdot B_{i}(x)
$$
Taylor（多项式基函数）级数展开，Fourier级数展开都是拟合的方式，SH（Spherical Harmonics，球谐）函数拟合也是。

SH是**一系列2D基函数**，定义在球上（可以看做对方向的函数，方向可以用$\theta$，$\phi$来描述，也就是二维的）。

另一种理解是，SH像一维中的Fourier基函数。

![Spherical_Harmonics_Visualized](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Spherical_Harmonics_Visualized.png)

如果用$SH(l,m)$来描述某一个球谐函数的话，$SH(0,0)$就是uniform的，在球上任何方向都一样。同一层的球谐函数，即$SH(l,\cdot)$的一组函数频率都是一样的。

第$l$阶的$SH$函数有$2l+1$个，编号从$-l$到$0$再到$l$。阶数越高，频率越高。

Q：为什么不用2D的Fourier（既然球面上的函数可以展开成2D的），而要用$SH$呢？

A：在渲染中常见的球面上的函数，如果先用2D的Fourier，再根据不同频率近似，再逆变换到球面上，很可能球面上会出现缝。而$SH$函数本来就定义在球面上，在球面上很连续，不会出现这种问题。

每个$SH$基函数$B_{i}(\omega)$是用Legendre多项式写的。

------

基函数有了，系数$c_{i}$也有很好的性质。

如果使用$SH$来拟合，其系数如下：
$$
c_{i}=\int_{\Omega}f(\omega)B_{i}(\omega)\,\mathrm{d}\omega
$$
这个过程称之为Projection（投影），即知道一个函数和基函数，求其系数的过程。

至于说算这个积分，那就sampling。（其实开销不小，很可能是RTR承受不起的，因此要预计算）

另外，Product Integral本质上就是点乘（这和我们以前做投影时做向量点乘是相通的），因为函数的积分就是向量点乘。

重建（Reconstruction）：使用（截断的，也就是说一部分）系数和基函数恢复原始函数。

------

说说prefiltering。有了一个球面上的环境光，对**某个点prefiltering后某个方向上做查询**，其效果基本和**没有prefiltering，在该点上多个方向上多次查询**等价。

回顾IBL的渲染方程：
$$
L_{o}(p, \omega_{o})=\int_{\Omega^{+}}L_{i}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}
$$
其实就是$L_{i}$（光照项）和$f_{r}$（BRDF）的product integral。而BRDF是定义在整个半球上相当smooth的函数，虽然$L_{i}$可以很复杂，有高频有低频，但如果BRDF是diffuse的话，那它就像low pass filter一样。

这样，差不多只要$3$阶的$SH$，就能把diffuse BRDF恢复得很好。

我们的目的是：任意频率的environment lighting照亮diffuse物体，在不考虑visibility的情况下，求shading的值是多少。

**既然BRDF只要$3$阶就能描述，那么光照项也没有必要用很高的频率描述了，同样不需要超过$3$阶。**

------

**PRT**

现在有了上述技能，我们还希望：

1.   引入阴影（上面的论述中并不考虑阴影）
2.   对BRDF不做限制（上面的论述提的是diffuse BRDF）

PRT可以做到这些，不仅如此，还能做全局光照。

再次回顾渲染方程：
$$
L_{o}(p, \omega_{o})=\int_{\Omega^{+}}L_{i}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}V(p, \omega_{i})\,\mathrm{d}\omega_{i}
$$
本质上是对triple product的积分（光照项，BRDF，visibility）。

求解它最直接的思路当然是sampling（开销巨大）。

------

$$
L_{o}(p, \omega_{o})=\int_{\Omega^{+}}L_{i}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}V(p, \omega_{i})\,\mathrm{d}\omega_{i}
$$

因为有visibility，所以自然就能引入阴影。

PRT认为**场景中只有光照变化**（变化是指，可以换光照，或者同个光照旋转）。

这样，triple product可以看成两项之积，也就是：

1.   lighting项：$L_{i}(p, \omega_{i})$
2.   lighting transport项：$f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}V(p, \omega_{i})$，也就是其他与光照无关的全部项

PRT将lighting项用$SH$重建；既然认为只有光照变化，那么lighting transport就是不变的，相当于任意shading point的固有性值，也就可以**在渲染之前预计算好**。

而我们发现，在只有光照变化的场景中，lighting transport项还是个球面函数（固定观察视角），也就也能用$SH$重建。

讨论BRDF：

#### Diffuse Case

这个时候BRDF是个constant value，可以直接拉出来（注意这是等式）：
$$
L_{o}(p, \omega_{o})=\rho\int_{\Omega^{+}}L_{i}(p, \omega_{i})V(p, \omega_{i})\,\mathrm{d}\omega_{i}
$$
其中$\rho$就是BRDF的值。

用$SH$重建，那么lighting项就可以写成：
$$
L_{i}(p,\omega_{i})\approx\sum{l_{i}B_{i}(p,\omega_{i})}
$$
其中$l_{i}$是光照系数（lighting coefficient）。

渲染方程也就可以写成：
$$
L_{o}(p, \omega_{o})\approx\rho\sum{l_{i}\cdot\int_{\Omega^{+}}B_{i}(p,\omega_{i})V(p, \omega_{i})\,\mathrm{d}\omega_{i}}
$$

>   闫令琪语：在微积分里面，咱们经常会探讨说什么时候可以交换求和与积分。在图形学里从不考虑，啊，我们认为它一定可以交换。……在PRT的场合下，是绝对不需要考虑的。就是说我放心地交换积分与求和。
>
>   弹幕说：实变函数满足Fubini定理，才可以交换顺序。

然后，对于这个积分，$l_{i}$也是常数，因此可以拿出来。这样，积分式实际上就变成了light transport这个球面函数投影到某个basis function上的系数，这自然是可以预计算的。~~With some mathematical magic,~~只有lighting发生变化，那么任何一个shading point，都可以投影到任何一个basis function上去。比如说，投影到前$3$阶的$SH$，然后算出个数就可以了。

这样以后，即有：
$$
L_{o}(p, \omega_{o})\approx\rho\sum{l_{i}T_{i}}
$$
其中$T_{i}$是上面预计算得到的系数。这意味着，对于任何shading point的计算（shading，包括shadow），只要做一次向量点乘即可。

------

换个方式看，还是从渲染方程出发：
$$
L_{o}(p, \omega_{o})=\rho\int_{\Omega^{+}}L_{i}(p, \omega_{i})V(p, \omega_{i})\,\mathrm{d}\omega_{i}
$$
用上$SH$，有：
$$
L(\omega_{i})\approx\sum\limits_{p}c_{p}B_{p}(\omega_{i})\\
T(\omega_{i})\approx\sum\limits_{q}c_{q}B_{q}(\omega_{i})
$$
在积分中展开，有：
$$
L_{o}(p, \omega_{o})=\rho\int_{\Omega^{+}}L_{i}(p, \omega_{i})V(p, \omega_{i})\,\mathrm{d}\omega_{i}\\
\approx\sum\limits_{p}\sum\limits_{q}c_{p}c_{q}\int_{\Omega^{+}}B_{p}(\omega_{i})B_{q}(\omega_{i})\,\mathrm{d}\omega_{i}
$$
看起来这双重求和一眼$O(n^{2})$，但是向量点乘是$O(n)$。原因是$SH$是正交的，因此只有在$p$和$q$相同，也就是$B_{p}$ 和$B_{q}$是相同的基函数的时候，积分结果才会是$1$，否则就是$0$，因此实际上仍然是$O(n)$。

------

Light transport项不能变，首先就意味着visibility不能变，意味着每个地方的遮挡关系不能变，这就是说场景不能动，必须是静态的；Light项使用$SH$描述的，只要环境光贴图事先算好，就能事先确定$L_{i}$，就能做到换光源。

这样的方法，本身不能直接允许光源旋转，但是根据$SH$的性质，我们发现光源的旋转能与$SH$的系数变化对应起来（这是$SH$的性质），因此能立刻确定光源旋转后的$L_{i}$。

------

$SH$的美好的性质：

-   正交。将一个$SH$函数投影到另一个$SH$函数上，得到的就是$0$.
-   做投影和重建很简单，即product intgral。
-   做旋转很简单，正如上面提到支持光源旋转的原因。（有点像旋转坐标轴）
-   做卷积很简单。
-   基函数数量少。

当然了，$SH$函数的重建能力还是有所欠缺，对于高频信号需要的阶数极多，不过对于diffuse BRDF，$3$阶$SH$还是够了的。

#### Glossy Case

Diffuse和glossy的区别就在于BRDF。在diffuse的场景中，BRDF可以视作一个常数，但是在glossy的场景中却不行（此时就是完整的四维的，包括两维输入方向，两维输出方向）。

这意味着，给定任何一个观察方向，light transport项都会投影出一组完全不同的向量。

这样，如果还是套用diffuse case的方程：
$$
L_{o}(p, \omega_{o})\approx\rho\sum{l_{i}T_{i}(\omega_{o})}
$$
我们发现，$T_{i}$从原来的常数，变成了一个关于观察方向$\omega_{o}$的函数。那么这里$T$就从原来的向量变成了一个矩阵，就是说用上了不同观察方向$\omega_{o}$；换个角度说，glossy case得到的必然是一个向量（因为不同观察方向得到的结果不同），而一个向量只有点乘一个矩阵才能得到向量，因此$T$也就必须是一个矩阵。

Glossy case下，$SH$就不能只取$3$阶了。而当物体非常glossy，比如几乎接近specular的时候，那就太高频了，已经到PRT无法解决的程度了。

#### 多Bounce的Light Transport

首先说如何描述光路。

如果光线直接进入眼睛，那么光路就是`LE`。

如果直接光照打在glossy材质上进入眼睛，那么光路就是`LGE`。

如果直接光照在非specular材质上多bounce后进入眼睛，那么光路就是`L(D|G)*E`，称为`LGGE`光路。

如果直接光照在specular材质上，反射到diffuse材质上后多bounce后进入眼睛，那么光路就是`LS*(D|G)*E`，称为`LSDE`光路，这种反射情况称为"caustics"。

------

我们发现这些光路都是`L`和`Light transport`的结构，因此只要是PRT的思想，即使是多bounce的light transport，也没有什么特殊的。

至于做法，就是算light transport时，把每趟计算，各bounce中的$B_{i}$都看成是一种光照，然后复用no bounce的PRT即可。
$$
T_{i}\approx\int_{\Omega^{+}}B_{i}(p,\omega_{i})V(p, \omega_{i})\,\mathrm{d}\omega_{i}
$$

<div align="center"><i>Light transport项</i></div>

即使物体的材质非常复杂（比如一个生锈的铁壶，那么生锈处和金属处的BRDF自然不同，此时整个物体的材质将达到六维，两维表示位置，另外四维表示该位置的BRDF），但是都可以预计算。

#### 缺陷

1.   $SH$不够高频，对高频信号的重建能力弱。
2.   要求场景、材质静态。
3.   预计算一样有较大的负担。

### 其它基函数

-   Wavelet
-   Zonal Harmonics
-   Spherical Gaussian
-   Piecewise Constant

不过只介绍wavelet，主题是二维的Haar wavelet。

不同于$SH$定义在球面上，小波定义在图像块上，而且定义域不同。

![Haar_Wavelet](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Haar_Wavelet.png)

<div align="center"><i>2D Haar Wavelet可视化</i></div>

原理：投影到wavelet上后，发现很多时候存在大量的基函数，其系数为$0$或者说接近$0$. 那么就可以只取系数最大的一部分，drop掉其余部分。

这就是一种non-linear approximation，优势在于可以全频率表示，重建低频或高频均可。

对Cubemap表示的环境光贴图，对其六个图都做wavelet变换（即将图投影成小波的系数）。将高频信息留在右上、右下、左下，左上部分集中稍低频的内容，还可以接着做小波变换。

![Wavelet_Transform](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Wavelet_Transform.png)

BTW，Jpeg采用的就是**类似**小波变换的变换，**离散余弦变换**（DCT），也就是先投影，再取非零系数。压缩效果很好。

用wavelet取代$SH$，效果很好，因为能够显示更高频的信息（包括更精致的阴影、更真实的glossy表面）。然而小波的致命缺陷在于，它无法handle快速旋转。这是因为虽然$SH$的rotation非常容易，但小波却没有这样的性质，因此每一次rotation，小波都要重新求解。

>   闫令琪大二下入坑图形学，大三发Pacific Graphics 2012。所谓幸福感，难道不是像沉默在悲哀的河流底下微微闪耀着沙金一样的东西吗？经历过无限悲哀后，看到一丝朦胧的光明这种奇妙的心情。
>
>   也许有一天，哪怕是卑微渺小的自己，也能有这样冲破满是迷雾的牢笼的体悟——我是如此祈求着。

## 实时全局光照

正如***Rendering Equation***一章中提过的说法：

>   对于渲染来说，渲染方程中的光应该要互相影响。假设所有的光都不反射，只渲染来自光源的光，称为直接光照（Direct Illumination）；加上间接光照（例如one-bounce的间接光），统称全局光照。

环境光照也是直接光照。显然，间接光照是不好做的那个。

一切被直接光照（其光源称为primary light source）照到的物体，都会继续将自己作为光源（称为次级光源，secondary light source），再去照亮别的物体。

*参考阅读：[《GAMES202：高质量实时渲染》3 实时全局光照：RSM、LPV、VXGI、SSAO、SSDO、SSR](https://zhuanlan.zhihu.com/p/556057984)。*

### 3D Space

#### RSM

Reflective Shadow Mapping（RSM）是一种基于Shadow Mapping的实时全局光照（Global Illumination，GI）算法。

为了shading某点的间接光照，需要解决：

1.   哪些是次级光源？也就是说，哪些地方会被直接照亮？
2.   各个surface patch对某点上间接光照的贡献如何计算？

问题1可以被Shadow Map直接解决。问题2的回答是，将surface patch看作是area light。

SM天然就能很容易地描述次级光源，需要计算每个texel作为次级光源的贡献，因而是个计算量很庞大的工作。

另外，由于上面都是从某点处观察各个方向的光线得到的，但是这并不能直接得到从camera看向那点的结果。就是说，从那点看过去，和从camera看过去，得到的结果是不一样的。

为了不依赖观察方向，引出了一个**假设：所有的反射物（即次级光源，也就是被直接照亮的patch）都是diffuse的**。这样，不管从哪里看向次级光源，结果都一样。

------

算次级光源的贡献的时候，直接对立体角采样做积分开销太大，并不合适；更好的做法是直接对次级光源发射出的光线采样，然后直接对shading point着色。

那么从对立体角积分，到对Area积分，公式如下：
$$
L_{o}(p, \omega_{o})=\int_{\Omega_{patch}}L_{i}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}V(p, \omega_{i})\,\mathrm{d}\omega_{i}\\
=\int_{A_{patch}}L_{i}(q\rightarrow p)f_{r}(p, q\rightarrow p, \omega_{o})V(p, \omega_{i})\frac{\cos{\theta_{p}}\cos{\theta_{q}}}{||q-p||^{2}}\,\mathrm{d}A
$$
甚至于当patch比较小的时候，甚至不需要积分，直接乘A面积即可。

**For a diffuse reflective patch**

其BRDF（指的是次级光源reflector的BRDF，不是上面积分公式中p点的BRDF）可以认为是常数，$f_{r}=\rho/\pi$。

而$L_{i}$，有：
$$
L_{i}=f_{r}\cdot\frac{\Phi}{\,\mathrm{d}A}
$$
其中$\Phi$是incident flux。~~总之是一些光学魔法~~ 发现带进去$\mathrm{d}A$甚至能被消掉，甚好。

牵扯到visibility的问题，我们发现不可能对每个shading point，都去生成Shadow Map去检查visibility。不好算，那就不算了。（总之就是如此）

**不过不是所有的pixels都会有贡献。**

-   Visibility（但是较难处理）
-   Orientation（就是根据法向，次级光源不可能照亮shading point）
-   Distance（如果非常远，它的贡献就很小，那就干脆别管了）

对于第三点distance，我们希望只关心距离shading point“足够近”的次级光源。在世界坐标中找，比较繁琐，那么干脆投影到Shadow Map上，在SM中找近的。（**这里算是引入了一个比较大但是不算不合理的假设，即在SM中距离近的，在世界坐标中就距离近**）至于SM上找，那就可以有不同的采样方式。

综上，RSM需要存储的信息有：

-   **Depth** SM本来就要存深度
-   **World Coordinate** 世界坐标判断两点距离
-   **Normal** 反射物法向，以及算$\cos$项
-   **Flux** 和光源有关的属性

用在手电筒这种小区域上效果很好。

优点：

-   容易实现（因为基于SM）

缺点：

-   性能开销线性于光源数
-   没有考虑visibility（上面说了）
-   假设太多，不够真
-   采样和质量的tradeoff

说它是图像空间的方法，是因为SM的第一个pass得到的就是一张图，基于这张图做的后续操作；不过话又说回来，正是因为RSM在这种情况下已经足够获取它所需要的所有信息了，所以说它是3D空间的方法也没问题，因为它不会受到camera pass是否可见造成的影响。

#### LPV

Light Propagation Volumes（LPV）在基于3D空间中光线传播的特定。

如果在任何一个shading point上，如果可以立即获得间接光照到达shading point时来自所有方向的radiance，那么自然可以做间接光照了。

我们发现：光线在空间中传播时，radiance是一个不变的量。

那么LPV首先将空间分为3D网格（分为Voxel），这些格子用来传播间接光照的radiance。这样，求某格子收到的radiance，只要把次级光源给出的radiance按格子求了就行。

步骤：

1.   找出次级光源，即哪些点收到直接光照
     1.   用RSM一样的方法找出次级光源（同样是有多少光源就做多少遍）
2.   将上述点注入（injection）到三维网格中
     1.   划分3D的格子（一般直接用三维纹理搞定了：texel对应3D空间中的voxel）
     2.   在任何格子内部，将所有虚拟光源往各个不同方向的radiance加起来
     3.   格子内现在记录了向不同方向的radiance值，可以拿$SH$压缩，至少两阶四个（另外，用上了$SH$，其实就几乎可以默认为只能处理diffuse材质了）

3.   在3D的网格中做传播，即radiance propagation
     1.   认为radiance都是从网格中心向各个方向沿直线出发的
     2.   六个面传播radiance，每个格子都把radiance加起来，还是用$SH$表示
     3.   重复上述操作直到迭代到稳定状态（大约四五次即可）


传播完成后，整个场景就完全覆盖了。这样，已经知道了任意shading point来自各个方向的radiance，也就能直接拿来渲染了。

显然和RSM一样，LPV也没有考虑visibility。

**缺点：**

漏光。因为认为格子内的radiance都保持一致，那么有点地方（比如狭窄的墙或者细缝）明明不可能接收到radiance，但是却会在LPV下被照亮。**本质在于狭窄到比格子更细。**更细分格子虽然可以解决这个问题，但是会带来存储增加、传播开销增大的问题。

#### VXGI

不同于RSM，RSM中次级光源都是每个像素表示的微小的表面，而VXGI中场景完全离散化成一系列的格子，并且是hierarchical的。一言以蔽之，就是从pixel表示变成了voxel表示。

VXGI是一个two-pass的算法，其第二趟从camera出发，发射camera ray，打到任何一个像素上，对glosst材质，其反射光类似于锥，拿这个反射光锥和场景中的voxel求交，那么交上的voxel对shading point的贡献就可以计算出来，从而来计算光线的传播。这个过程称为cone-tracing。

**Pass 1：**

也就是Light Pass，决定哪些voxel会被直接照亮。这些反射物可以使diffuse的，也可以是glossy的。这是因为，VXGI的voxel中记录了光源分布，表面法向分布，这样一来，根据材质，就可以算出出射分布，比LPV那种限定diffuse然后$SH$压缩要准。

**Pass 2：**

也就是Camera Pass，做cone-tracing。最朴素的做法是遍历所有voxel看看相交没，但是不如在hierarchical的voxel上找对应的层级，快速找到这个cone能cover的voxel，那么他们就能对shading point，也就是cone的起点有所贡献。

------

上述glossy材质只产生一个cone，如果是diffuse材质，那么相当于产生了若干个cone（比如8个来cover），对每个cone都重复上述做法。

相对来说，LPV只要做一遍propagation，并且使用$SH$表示，因此更快、更不准；VXGI效果更好，但是相应的速度就慢了。另外，体素化是非常麻烦的事情，特别是动态场景。

### Screen Space

所谓屏幕空间，就是说只能拿到屏幕上能看到的东西，也就是做GI之前能得到的，比如直接光照。

要而言之，GI in SS，起手信息就是做了直接光照的屏幕场景。

#### SSAO

Screen Space Ambient Occlusion（屏幕空间的环境光遮蔽，SSAO）做了对全局光照的近似，使得物体之间有恰当的阴影，而这些阴影原来应该由间接光照产生。

假设：

1.   对任意shading point，不知道间接光照。所以不如像Blinn-Phong着色模型一样，假设任何shading point从各个方向来的间接光照都是常数。
2.   虽然来自各个方向的间接光照都是常数，但是不见得每个方向都能接收得到。显式地考虑visibility，如果有被挡住的部分，那么这里的间接光照就要暗一些。
3.   假设为diffuse材质。

回到渲染方程：
$$
L_{o}(p, \omega_{o})=\int_{\Omega^{+}}L_{i}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})V(p, \omega_{i})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}
$$
首先使用近似：
$$
\int_{\Omega}f(x)g(x)\,\mathrm{d}x\approx \frac{\int_{\Omega_{G}}f(x)\,\mathrm{d}x}{\int_{\Omega_{G}}\,\mathrm{d}x}\cdot\int_{\Omega}g(x)\,\mathrm{d}x
$$
事实上，可以把$\frac{\int_{\Omega_{G}}f(x)\,\mathrm{d}x}{\int_{\Omega_{G}}\,\mathrm{d}x}$看成是函数$f$在积分域$\Omega_{G}$上的平均，因此也可以写成：
$$
\int_{\Omega}f(x)g(x)\,\mathrm{d}x\approx
\overline{f(x)}
\cdot\int_{\Omega}g(x)\,\mathrm{d}x
$$
处理渲染方程，得到：
$$
L_{o}^{Indir}(p, \omega_{o})\approx
\frac{\int_{\Omega^{+}}V(p, \omega_{i})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}}{\int_{\Omega^{+}}\cos{\theta_{i}}\,\mathrm{d}\omega_{i}}\cdot
\int_{\Omega^{+}}L_{i}^{Indir}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}
$$
为什么处理完以后乘积左边还有带着$\,\mathrm{d}\omega_{i}$的$\cos$项呢？因为投影立体角（Projected Solid Angle）$\,\mathrm{d}x_{\perp}=\cos{\theta_{i}}\,\mathrm{d}\omega_{i}$，所谓立体角其实就是单位球面上的一个面积，乘上$\cos$项以后正好就是投影到球的大圆（也是单位圆）上的一个圆（的面积）上。

![Projected_Solid_Angle](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Projected_Solid_Angle.png)

这样就可以理解成在单位圆上做积分，从连着$\cos$项在立体角上积分，变成了不带$\cos$项而在投影立体角上积分（就是说怎么理解$\cos$项的事），被积分项的意义就变成了单位圆上的面积的微元，那么积出来就是单位圆的面积，也就是$\pi$。

一言以蔽之，就是说$\cos{\theta_{i}}\,\mathrm{d}\omega_{i}$本身就是一个微元，可以写成$\mathrm{d}A$，然后$A$是单位圆的面积的微元。

~~使用一些数学魔法~~发现近似式中，分母的积分式是对整个半球积分，立体角$\omega_{i}$可以拆成$\sin{\theta}\,\mathrm{d}\theta \,\mathrm{d}\phi$，积出来就是一个常数$\pi$。

BTW，这一恒等式已有定数，称为$k_{A}$：
$$
k_{A}=\frac{\int_{\Omega^{+}}V(p, \omega_{i})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}}{\pi}
$$
其意义是：任何一个点向四面八方看过去，看到的visibility的加权平均。

这样一来，渲染方程就是：
$$
L_{o}^{Indir}(p, \omega_{o})\approx
k_{A}\cdot
\int_{\Omega^{+}}L_{i}^{Indir}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}
$$
对于近似中积分式，我们有以下处理：

1.   已经假设间接光照是常数了，因此$L_{i}^{Indir}(p, \omega_{i})$那就是个常数；由于和角度已经没关系了，所以可以写成$L_{i}^{Indir}(p)$直接提出来。
2.   已经假设是diffuse材质了，因此其BRDF为常数，即$\frac{\rho}{\pi}$（其中$\rho$是diffuse albedo），因此也可以直接提出来。
3.   最后的$\cos$项积分本身会得到$\pi$。

>   Diffuse albedo（漫反射反射率）是描述材质表面对漫反射光线的反射特性的属性。它表示了材质表面对入射光均匀地反射多少光线，而不会发生明显的方向性反射。漫反射反射率通常用符号$\rho$表示。
>
>   对于漫反射材质，其BRDF（双向反射分布函数）描述了入射光线在材质表面上的反射分布情况。BRDF定义了出射辐射度与入射辐射度之间的关系，其中入射辐射度是指从一个给定方向进入材质表面的光线辐射度，而出射辐射度是指从该表面反射出来的光线辐射度。
>
>   对于漫反射材质，其BRDF是常数$\frac{\rho}{\pi}$。这意味着无论入射光线的方向如何，材质表面上的反射光线都均匀地分布在所有可能的出射方向上。这是因为漫反射材质对入射光线的反射是均匀且无方向性的，不会发生明显的镜面反射。

于是，有：
$$
L_{o}^{Indir}(p, \omega_{o})\approx
k_{A}\cdot
L_{i}^{Indir}(p)\cdot
\frac{\rho}{\pi}\cdot
\pi\\
=k_{A}\cdot
L_{i}^{Indir}(p)\cdot\rho
$$
这样相当于说，AO中的间接光照强度，相当于一个常数乘以半球表面上的平均visibility。

渲染方程就可以写成：
$$
L_{o}(p, \omega_{o})=\int_{\Omega^{+}}L_{i}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})V(p, \omega_{i})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}\\
=\frac{\rho}{\pi}\cdot
L_{i}(p)\cdot
\int_{\Omega^{+}}V(p, \omega_{i})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}
$$

------

那么，如何在屏幕空间中算出平均visibility呢？我们首先要限定一定范围内的遮挡情况，因为不同于环境光时假设无限远，这里的光源距离物体是有一定距离的，超出这个范围的光我们应该直接drop掉，不再考虑。（这也是一个假设，实际上不够准确）

不过在Screen Space中，我们没办法直接trace光线。因此SSAO的做法（同时也是一个大胆的假设）是：对任意的shading point，都往其周围半径为$r$的球中采样很多点。

![Use_SSAO_Calculating_Average_Visibility](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Use_SSAO_Calculating_Average_Visibility.png)

这样以后，虽然没有办法直接trace光线，但是深度信息是有的。在Shadow Map上直接判断可不可见就行。即，拿采样点的深度和SM中camera看过去的最小深度比较，来决定采样点是否可见。

*第二个球中最下面的红点被SM错误判断了，但是这完全可以接受。*

其次，只有半球需要考虑，因此上面图中的球可以直接水平截掉上面一半。现在我们都认为是能拿到法向信息的，这样只要选取法向指向的那半球就可以了，同时甚至还能根据法线方向对不同方向的visibility做加权。

当然了，SSAO会产生一些false occlusions，也就是说会错误遮挡原本不应该遮挡的地方。这个问题的本质在于：俩东西在屏幕空间中离得挺近的，但是实际上可能离得还挺远的。采用HBAO（也就是上面说的考虑了法向信息的AO）可以有效缓解这个问题。

#### SSDO

>   首先错过了闫令琪直播吃键盘，令人扼腕叹息。

所谓SSDO，就是Screen Space Directional Occlusion（屏幕空间的方向性遮挡）。它是SSAO的一个提升，与其说大胆假设间接光照到处都一样，不如考虑地更精准一些。

那为什么这么说呢？因为间接光照的信息其实是可以知道的，和RSM类似。也就是说，提供间接光照的，其实就是被直接光照照亮的次级光源；次级光源提供的直接光照，就是场景中的间接光照。

我们要用直接光照的信息，但不是从RSM中获取，而是在屏幕空间中获取。SSDO的做法和path tracing类似。即，对于任意shading point，想要计算它接收到的光照，做类似path tracing的操作，随机地向某方向发射光线；发射的光线打到的点，自然对shading point有所贡献。

所以，SSAO和SSDO的假设看上去其实是相反的，即：

-   AO：先假设shading point会接受到四面八方的间接光照，然后再根据阻挡情况，把一定比例的间接光照block掉
-   DO：不假设shading point接受四面八方的间接光照，而是只考虑次级光源的直接光照

![Differences_Between_AO_and_DO](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Differences_Between_AO_and_DO.png)

这是因为，SSAO假设间接光照来自比较远的地方，而SSDO假设间接光照只来自于非常近的地方。因此，AO要看一定范围内，哪些地方会阻挡间接光照，而DO要看哪些地方能够提供间接光照。它们都不完全正确。

在效果上，SSAO做不到像SSDO能做的color bleeding，也就是色溢。

所谓color bleeing，就是说光线从一个diffuse表面到另一个diffuse表面不断传播，于是不同颜色的diffuse表面就会互相照亮对方。显然，SSAO只做亮暗，无法做到color bleeding。

------

看看渲染方程：
$$
L_{o}^{dir}(p, \omega_{o})=
\int_{\Omega^{+},V=1}
L_{i}^{dir}(p)
f_{r}(p, \omega_{i}, \omega_{o})
\cos{\theta_{i}}\,
\mathrm{d}\omega_{i}
$$
这就是说，和path tracing一样，直接光照当然是可见（也就是$V=1$）的部分给出的。这种情况就没有间接光照。
$$
L_{o}^{indir}(p, \omega_{o})=
\int_{\Omega^{+},V=0}
L_{i}^{indir}(p)
f_{r}(p, \omega_{i}, \omega_{o})
\cos{\theta_{i}}\,
\mathrm{d}\omega_{i}
$$
这一部分，从shading point往某个方向打的时候，$V=0$意味着达到了某个点，那么这个点的radiance自然要算作间接光照的贡献。

要计算这些贡献的和，假设材质是diffuse的，那么它反射到任何一个shading point的radiance，于从camera看到的它反射出的radiance是一样的；这种情况下，就可以直接计算。至于如何找到哪些会被挡住，做法和SSAO是一样的，即：从某点出射的光线，不必真的trace它会不会被挡住，而是仍然看从camera到它会不会被挡住。

找到了被挡住的点，自然就可以根据它们的法向，来算出它们对shading point的间接光照的贡献。

缺点在于，因为它在屏幕空间做，所以有一些看不到的面**本应该**有所贡献，但是因为在屏幕空间中做，所以这些贡献是计算不上的。（这是所有屏幕空间做法的缺点）

另外，SSDO本身决定了它只能做小范围的间接光照。

#### SSR

Screen Space Reflection（或者说Screen Space Ray-tracing可能更准确）是一种实现全局光照的方法，它在屏幕空间中做ray tracing，以达到GI的效果。

SSR要做到：

1.   对任何光线和屏幕空间场景（其实就是一个壳）求交
2.   算相交的点的贡献

SSR的思想在于：反射的部分，绝大多是其实是屏幕空间中已经有了的部分，因此其实可能并不需要额外信息，只要重用场景中已有的信息即可。（例如雨后街道倒影，反射的是场景中有的街道上的东西）

由于实现的是屏幕空间的ray tracing，自然，SSR对材质并没有要求。

流程是：

-   首先已知不带SSR的屏幕空间场景（当然了，还有法向信息和深度信息两张贴图）
-   对于任意shading point，向场景中发射光线，与场景求交（详细来说，干的是ray marching的活，与最浅深度相交）
    -   做ray marching，从shading point出发，每隔一定步长，看看到那里是不是小于最小深度
    -   显然，如果步长太长，最后求到的交就不准；如果步长太小，开销就很大
    -   解决方法是hierarchical ray trace
-   将SSR的结果加到屏幕空间上，也就是shading

其中，所谓的hierarchical ray trace，需要用到Depth Mip-Map，存储的是一个区域内的最浅深度。这样，使用这个加速结构的意义就是：如果说查询Depth Mip-Map，如果连上层的一个最浅深度都交不上，那么就更不用说下级的所有深度了，那就可以快速跳过这一部分。

![Depth_Mip-Map](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Depth_Mip-Map.png)

示例代码：

```cpp
min = 0;
while (level > -1)
    step through current cell;
	if (above Z plane) ++level;
	if (below Z plane) --level;
```

找到交点后，将`level`置成负的，就可以跳出循环。

------

SSR的shading还是拿渲染方程：
$$
L_{o}(p, \omega_{o})=\int_{\Omega^{+}}L_{i}(p, \omega_{i})f_{r}(p, \omega_{i}, \omega_{o})V(p, \omega_{i})\cos{\theta_{i}}\,\mathrm{d}\omega_{i}
$$
如果BRDF完全是specular的，则其BRDF就是一个delta函数，积分就没了，直接找反射光反射的radiance即可；glossy的话就Monte Carlo之类的方法多采样几根。

虽然如此，SSR由于假设了从反射物反射到某点的radiance和反射到camera的值一样（这很重要），因此**反射物**（比如ppt中经典的布料）必须是diffuse的，而地板什么的材质无所谓。

SSR的缺点并不来自于光追，而还是在于屏幕空间：

1.   Hidden Geometry Problem。由于屏幕空间只有一层壳，所以说有一些屏幕空间看不见的部分，本应该有贡献但是没被考虑。
2.   Edge Cutoff。直说了，就是一个东西在屏幕空间中只有一部分（比如很高的楼，那它上面的部分大概率就不在屏幕空间中），那缺失的部分自然也不能被SSR反射出来。（一个取巧的办法是，将cut的边缘做一个平滑滤波之类的，看起来糊了那也算合理）

------

SSR的其他特性：

-   没有可见性和距离平方衰减的问题，因为是用BRDF sampling做的，在立体角范围内对shading point做tracing，而不是对次级光源的area做积分算贡献
-   对shading point有贡献的次级光源一定是可见的，遮挡关系是正确的
-   支持各种BRDF的就不说了（比如diffuse反射得比较糊之类）
-   天然支持Contact Hardening。也就是说，和地板（就这么说吧，反正是这么个东西，地板上面展示SSR的反射结果）接触地近的，反射出来的比较清晰；远端就比较模糊
-   Specular Elongation。就是说如果认为地板各项同性，那么反射的lobe其实有点像一个拉长的椭圆；于是光线反射出去以后，看到的东西就好像被拉长了
-   不挑地板。也就是说不论接受反射的是什么材质，都可以

SSR的空间复用的trick：

![SSR_Hit_Point_Reuse_Across_Neighhbors](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/SSR_Hit_Point_Reuse_Across_Neighhbors.png)

由于用上了邻居trace的结果，原本trace的两条光线，起到了四条光线的作用。

也可以像split sum一样，先filter一遍，那么在镜面反射的方向上查一次，就好像是在没有filter的情况下，对那个区域查好多次，这样对屏幕空间中glossy的部分，查一次就好了，非常方便。缺点是，在split sum中环境光是无穷远的，但是在SSR的情况下，屏幕空间可不是这样的，这样的filter就不好做。

SSR的优点：

-   做glossy和specular表面的反射很快
-   质量挺好的
-   没有spike，也没有遮挡上的问题

缺点：

-   diffuse场景就没那么高效了
-   继承了来自Screen Space方法的缺点

## PBR

所谓PBR，就是Physicaly-Based Rendering。这涉及到材质、光照、camera、light transport，当然材质是其中非常重要的一部分。

不论是Microfacet还是Disney，在RTR中其实都不够physically-based，Disney更加artist-friendly。

Microfacert / Disney主要处理表面材质，而对于volumes（比如云、头发等等），则专注处理一次/多次scattering。

### Microfacet BRDF

所谓微表面材质，其实就是假设了微观表面都是镜面反射，根据微表面的分布，最后材质能有diffuse、glossy、specular的分别。
$$
f(\mathbf{i},\mathbf{o})=\frac{\mathbf{F}(\mathbf{i},\mathbf{h})\mathbf{G}(\mathbf{i},\mathbf{o},\mathbf{h})\mathbf{D}(\mathbf{h})}{4(\mathbf{n},\mathbf{i})(\mathbf{n},\mathbf{o})}
$$

-   **Fresnel项** $\mathbf{F}(\mathbf{i},\mathbf{h})$，决定从一个角度看去，有多少能量会被反射
-   **Shadowing-masking项** $\mathbf{G}(\mathbf{i},\mathbf{o},\mathbf{h})$，或者说**G项**，
-   **Normals Distribution项** $\mathbf{D}(\mathbf{h})$，即微表面法线分布

#### Fresnel项

当入射角度和法线几乎垂直（即grazing angle）时，Fresnel项会反射最多的能量。

![Fresnel_Term_Dielectric](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Fresnel_Term_Dielectric.png)

<div align="center"><i>Fresnel Term (Dielectric, &#951;=1.5)</i></div>

![Fresnel_Term_Conductor](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Fresnel_Term_Conductor.png)

<div align="center"><i>Fresnel Term (Conductor)</i></div>

>   绝缘体是指那些不导电的材质，如塑料、玻璃、水等。在渲染中，绝缘体材质的外观通常由其折射率（Index of Refraction）和衰减系数（Attenuation）决定。折射率描述了光在材质中传播时的弯曲程度，而衰减系数则决定了光线在材质内部的衰减程度。绝缘体材质通常会发生折射、散射和吸收等光的相互作用，使得光线在材质表面和内部产生复杂的效果，如透明、散射、反射等。
>
>   导体是指那些能够导电的材质，如金属。导体材质的外观主要由其电导率（Conductivity）和折射率（Index of Refraction）决定。电导率描述了材质对电流的导电能力，而折射率描述了光在材质中传播时的弯曲程度。导体材质通常会发生反射和吸收等光的相互作用，而不会发生折射。导体材质的外观通常呈现出金属光泽，如镜面反射和漫反射。

Fresnel项的准确公式需要考虑s极化和p极化，如下：

![Fresnel_Formula_Accurate](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Fresnel_Formula_Accurate.png)

在RTR中，使用Schlick近似，有：
$$
R(\theta)=R_{0}+(1-R_{0})(1-\cos{\theta})^{5}\\
R_{0}=(\frac{n_{1}-n_{2}}{n_{1}+n_{2}})^{2}
$$
其中，$\theta$是入射角（光线与法线夹角），$R(\theta)$是反射率，$R_{0}$是基础反射率（带有RGB颜色）。当$\theta=90\degree$时（即grazing angle），$R(\theta)$就是$1$；当$\theta=0\degree$时（正对着看），$R(\theta)$就是$R_{0}$。

#### NDF

现在来聊聊NDF（Normal Distribution Function），即微表面的法向分布。

![NDF_in_a_Visual_Way](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/NDF_in_a_Visual_Way.png)

有多种方式描述NDF，介绍一下。

##### Beckmann

Beckmann NDF是一种常用的表面微平面法线分布函数，用于描述微表面的法线分布情况，有点像高斯分布，其公式如下：

$$
D(\mathbf{h}) = \frac{\exp(-\frac{\tan^2(\theta_{h})}{\alpha^2})}{\pi \alpha^2 \cos^4(\theta_{h})}
$$

其中：$D(\mathbf{h})$ 是Beckmann NDF的值；$\theta_{h}$是法向向量（$\mathbf{n}$）和半程向量（Half Vector, $\mathbf{h}$）的夹角；$\alpha$是表面的roughness，其值越小，表面越接近镜面，可以说和高斯分布中的$\sigma$类似。

之所以指数上面用的是$\tan$，原因在于Beckmann NDF是定义在Slope Space（坡度空间）上的。

![The_Way_You_Define_Beckmann_NDF_in_Slope_Space](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/The_Way_You_Define_Beckmann_NDF_in_Slope_Space.png)

好处是，和高斯一样，积分域都是无穷大，但是其角度的范围一定在$(-\pi/2,\pi/2)$中。这样实际上保证了永远不可能出现面朝下的微表面。直接用角度的话，反而就不能保证这一点了。

至于分母项，是一种归一化。因为我们希望NDF在Projected Solid Angle上积分为$1$。

##### GGX

GGX（Trowbridge-Reitz）相比于其他法线分布函数，如Beckmann，具有更好的性质。它在法线分布的尖锐区域和模糊区域之间具有平滑的过渡，因此可以更准确地模拟各种表面的反射特性。公式如下（应该是吧，闫令琪没说 ~~然后我笔记也没记~~）：
$$
D_{\text{GGX}}(\mathbf{h}) = \frac{\alpha^2}{\pi \left(\cos^2(\theta_{h}) (\alpha^2 + \tan^2(\theta_{h}))^2\right)}
$$

其中，$D_{\text{GGX}}(\mathbf{h})$ 是GGX模型的值，$\mathbf{h}$ 是半程向量（Half Vector），$\theta_{h}$ 是法向向量（$\mathbf{n}$）和半程向量（$\mathbf{h}$）的夹角，$\alpha$ 是表面的粗糙度（roughness）参数。

![GGX_With_Long_Tail_Comparing_to_Bckmann](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/GGX_With_Long_Tail_Comparing_to_Bckmann.png)

可以看到，同Beckmann相比，GGX具有长尾（Long Tail）的性质，即使到grazing angle仍然离$0$比较远。这使得GGX渲染的高光过度自然。

##### GTR

Extending GGX，或者说GTR（Generalized Trowbridge-Reitz）对GGX作出了一些改进，主要体现在有着更长的尾巴。

![GTR_Function](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/GTR_Function.png)

对于GTR而言，引入参数$\gamma$，当$\gamma=2$时，GTR退化为GGX。当$\gamma$值非常大时，GTR趋近于Beckmann。

#### Shadowing-Masking项

也就是$G$项。它解决的是微表面间的自遮挡问题（这个问题在grazing angle时尤为严重）。

>   其实这个说法我感觉有点误导，应该这么说：
>
>   自遮挡才是正确的，如果微表面不自遮挡，渲染出来的结果势必非常亮。为什么呢？比如说grazing angle的时候，BRDF式子分母中的入射光、出射光几乎和法向垂直，那么其余弦值几乎为$0$。它们相乘，还在分母，BRDF的值就贼大，简直是亮的不行。
>
>   所以要通过Shadowing-Masking项补偿（或者说mask）一部分，来使得那一部分变暗到适当的程度。

之所以叫Shadowing-Masking项，是因为：

-   当光线照射到微表面上却被微表面自遮挡时，那就shadowing了
-   由光路可逆，从微表面出射的光线也会被微表面自遮挡，对于眼睛来说这部分光就被masked了

本质上，这两点是一样的。正是因为，微表面自遮挡本身会导致材质一部分变暗，因此引入Shadowing-Masking项。我们希望，在正对法向看的时候，$G$项应该接近于$1$；而在grazing angle的时候，$G$项应该剧烈地减少到接近于$0$。

##### Smith Shadowing-Masking

说说Smith的$G$项。它分开考虑shadowing和masking，即：
$$
G(\mathbf{i},\mathbf{o},\mathbf{m})\approx
G_{1}(\mathbf{i},\mathbf{m})
G_{2}(\mathbf{o},\mathbf{m})
$$
![Smith_Shadowing_Masking](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Smith_Shadowing_Masking.png)

差不多长这样，然后就达成了我们想要的效果。

缺点：能量不守恒。走个白炉测试就知道了。

>   在不考虑 fresnel 项时，在一个各方向各位置 radiance 相同的环境光中放置一个物体，该场景能够测试材质是否满足能量守恒的特性，被称为白炉测试（white furnace test）。
>
>   https://airguanz.github.io/2019/03/18/white-furnace-test.html

>   就我的理解来说，其实是布置一个空的背景，给定一个uniform的全局光照，使得照出来如果能量守恒，那么反射的颜色就恰好是背景的颜色。

在roughness非常大的时候，那么光线就非常容易被遮挡，自然能量损失就巨大。

##### Kulla-Conty BRDF

**Patch**：把丢失的能量补充回来！

在RTR中的方法和离线渲染有所不同。基本的思想是，从一个微表面反射出去的光线，要么没有被其他微表面挡住（那就出去了，也就能被成功看到），要么被挡住（这意味着会有后续bounce，直到它离开微表面），于是“被遮挡”和“发生下次bounce”实际上可以等同。

这种近似方式就是Kulla-Conty近似，用一种经验性的方式补偿多次反射丢失的能量。

如果只考虑一次反射，那么有多少能量离开表面呢？
$$
E(\mu_{o})=
\int_{0}^{2\pi}
\int_{0}^{1}
f(\mu_{o},\mu_{i},\phi)
\mu_{i}\,\mathrm{d}\mu_{i}\,\mathrm{d}\phi
$$
其中，$\mu=\sin{\theta}$。这个公式拿BRDF，$\cos$项，lighting项一块，积了个分。首先认为所有入射光入射的radiance从任何一个方向$l_{i}$都是$1$。BRDF假设各向同性（isotropic），即$\phi$和$i$、$o$无关。为什么说积了$\cos$项但是式子里没有呢？~~一点小小的数学震撼~~ 因为如果要把一个球面展开成$\theta$和$\phi$的积分，立体角展开成$\sin{\theta}\,\mathrm{d}\theta\,\mathrm{d}\phi$。这样以后，定义$\mu=\sin{\theta}$，化出来式子就长这样。

这个式子的值介于$0$到$1$之间，也就是说有多少的能量在这一bounce中被遮挡掉了。那么，为了补偿这一part的丢失，那就加上$1-E(\mu_{o})$即可。

所以我们补一个BRDF，让它积分出来的值等于$1-E(\mu_{o})$。然后考虑BRDF的可逆（或者说对称性），有$c(1-E(\mu_{i}))(1-E(\mu_{o}))$，$c$是一个归一化函数。

>   不这么设计当然可以，因为原则上我们只要一个积出来值对的函数就行了，任何函数积出来值对了都可以。但为什么这么设计呢？因为这样比较简单。~~没看出来~~

然后直接揭晓答案：
$$
f_{ms}(\mu_{o},\mu_{i})=
\frac
{(1-E(\mu_{i}))(1-E(\mu_{o}))}
{\pi(1-E_{avg})}\\
E_{avg}=
2\int_{0}^{1}E(\mu)\mu\,\mathrm{d}\mu
$$
显然，$c$就是分母那套玩意。

推导过程：
$$
E_{ms}(\mu_{o})=
\int_{0}^{2\pi}
\int_{0}^{1}
f_{ms}(\mu_{o},\mu_{i},\phi)
\mu_{i}\,\mathrm{d}\mu_{i}\,\mathrm{d}\phi\\
=2\pi\int_{0}^{1}\frac
{(1-E(\mu_{i}))(1-E(\mu_{o}))}
{\pi(1-E_{avg})}
\mu_{i}\,\mathrm{d}\mu_{i}\\
=2\frac{1-E(\mu_{o})}{1-E_{avg}}
\int_{0}^{1}(1-E(\mu_{i}))\mu_{i}\,\mathrm{d}\mu_{i}\\
=\frac{1-E(\mu_{o})}{1-E_{avg}}(1-E_{avg})\\
=1-E(\mu_{o})
$$
~~昏了。~~

不过话虽如此，$E_{avg}(\mu_{o})=2\int_{0}^{1}E(\mu_{i})\mu_{i}\,\mathrm{d}\mu_{i}$仍然未知（as analytic）。我们可以使用split sum中处理难以求解析解积分的方法，比如预计算（打表！）。不过与此同时，我们也不希望维度太高。

那首先这个式子就依赖于观测方向$o$，以及BRDF。不过BRDF太过复杂，只记录roughness就够了。这是因为，BRDF的描述基于NDF，而NDF的性质是由roughness决定的。**这样，预计算就只依赖于$\mu_{o}$和roughness两个维度了。**

------

如果有颜色，那就是说有额外的能量损失。这时候的做法是：

1.   先考虑没有颜色，那么按照上面的步骤走；
2.   再考虑颜色造成的额外损失，来决定最终颜色。

这依赖于平均Fresnel项（也就是决定平均会反射掉多少能量）：
$$
F_{avg}=\frac
{\int_{0}^{1}F(\mu)\mu\,\mathrm{d}\mu}
{\int_{0}^{1}\mu\,\mathrm{d}\mu}\\
=2\int_{0}^{1}F(\mu)\mu\,\mathrm{d}\mu
$$
能被直接看到的部分：$F_{\text{avg}}\cdot E_{\text{avg}}$

而一个bounce后，能看见的就是：$F_{\text{avg}}(1-E_{\text{avg}})\cdot F_{\text{avg}}E_{\text{avg}}$

很显然，$k$个bounces后，能看见的就是：$F^{k}_{\text{avg}}(1-E_{\text{avg}})^{k}\cdot F_{\text{avg}}E_{\text{avg}}$

把它们加起来，得到Kulla-Conty的颜色项：
$$
\frac
{F_{\text{avg}}E_{\text{avg}}}
{1-F_{\text{avg}}(1-E_{\text{avg}})}
$$
我们将它直接乘在不带颜色的BRDF项上即可。

### LTC



### Disney Principled BRDF

### NPR

## 光追，启动！