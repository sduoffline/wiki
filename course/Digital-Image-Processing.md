---
title: 数字图像处理
description: 哈哈。Fuck you.
published: true
date: 2024-01-02T14:44:46.328Z
tags: 
editor: markdown
dateCreated: 2024-01-02T14:44:46.328Z
---

# Notes on Digital Image Processing

## Chapter 1: Introduction

**这一章我觉得完全没必要看**。

先说说课程。考试 60%，平时分 10%，作业 30%。

考试：

- 选择题 10 题，每题 2 分，共 20 分
- 简答题 5 题，每题 6 分，共 30 分
- 计算题 5 题，每题 6 分，共 30 分
- 分析题 1 题，每题 10 分，共 10 分
- 综合题 1 题，每题 10 分，共 10 分

**不存在于复习幻灯片中的内容我将直接略去**。

<details>
    <summary>不用看的</summary>
    <p>计算机视觉有三层：</p>
    <ul>
        <li>底层：边缘检测、滤波、分割</li>
        <li>中层：相机标定、轨迹预测、三维重建</li>
        <li>高层：物体检测、表请识别、场景理解、运动事件识别</li>
    </ul>
    <p>数字图象处理是用<strong>数字计算机</strong>处理所获取<strong>视觉信息</strong>的技术。</p>
    <p>图像处理的特点：</p>
    <ul>
    <li>精度高</li>
    <li>再现性好</li>
    <li>处理的多样性、通用性、灵活性强</li>
    <li>图像信号在空间、频谱和时间上有相关性，表明图像数据中存在<strong>信息冗余    </strong></li>
    <li>许多数字图像处理操作具有空间并行性</li>
    <li>图像数据量庞大</li>
    <li>处理费时</li>
    <li>图像处理技术<strong>综合性</strong>强</li>
    </ul>
    <p>一些术语：</p>
    <ul>
    <li>Image：一般指用镜头等科技手段得到的视觉形象</li>
    <li>Picture：强调手工绘制的人物或景物画</li>
    <li>Drawing：人工绘制的工程图</li>
    </ul>
</details>

表示数字图像的一种方式：

$$
f(x,y)=\begin{bmatrix}
f(0,0)&f(0,1)&\cdots&f(0,N-1)\\
f(1,0)&f(1,1)&\cdots&f(1,N-1)\\
\vdots&\vdots&\ddots&\vdots\\
f(M-1,0)&f(M-1,1)&\cdots&f(M-1,N-1)\\
\end{bmatrix}
$$

灰度级别：$L=2^b$，其中 $b$ 是二进制位数。

> 然后经典什么尺寸什么位数，让你算图片容量大小

对于 RGB 三通道的情况来说，有两种存储方式：

![Two_Ways_to_Store_RGB_Image_in_Memory](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Two_Ways_to_Store_RGB_Image_in_Memory.png)

颜色空间看：https://wiki.sduonline.cn/zh/course/Computer-Animation-Technology#%E9%A2%9C%E8%89%B2%E7%A9%BA%E9%97%B4

### 邻域

最常见的是4-邻域和8-邻域。

<div align="center"><img src="https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Four-Neighborhoods.png" referrerpolicy="no-referrer" alt="Four-Neighborhoods"></div>

<div align="center">4-邻域</div>

<div align="center"><img src="https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Eight-Neighborhoods.png" referrerpolicy="no-referrer" alt="Eight-Neighborhoods"></div>

<div align="center">8-邻域</div>

### 步长

假设一幅图像中某像素在内存中的地址为 $p$，那么其 $3\times 3$ 邻域中的像素地址为：

$$
\begin{bmatrix}
p-step-1&p-step&p-step+1\\
p-1&p&p+1\\
p+step-1&p+step&p+step+1\\
\end{bmatrix}
$$

显然，图像**每行所占的字节数**为步长（step）。

### 采样和量化

常识，不写。

## Chapter 2: Fundamentals

总之是只写了出现在复习幻灯片里的东西。

### 亮度相对性

![Example_of_Relativity_of_Brightness](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Example_of_Relativity_of_Brightness.png)

背景亮度为 $I$，前景和背景亮度差距的绝对值为 $\Delta I$，所以能用 $\frac{\Delta I}{I}$ 来表示前景和背景的亮度差距。

对于这个例子来说，因为 B 的前景相对亮背景而言差距更小，因此 B 看起来更亮。

### 灰度插值

介绍双线性插值就够了。看图：

![Example_of_Bilinear_Interpolation](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Example_of_Bilinear_Interpolation.png)

步骤：

1. 对上端的两个顶点进行线性插值，可得：$f(x,0)=f(0,0)+x[f(1,0)-f(0,0)]$
2. 对下端的两个顶点进行线性插值，可得：$f(x,1)=f(0,1)+x[f(1,1)-f(0,1)]$
3. 对上述两个结果进行线性插值，可得：$f(x,y)=f(x,0)+y[f(x,1)-f(x,0)]$

### Resampling

基于邻近像素的值，计算非整数位置上的颜色值。主要有三种方式：

1. 最近邻插值
2. 双线性插值
3. 双三次插值

那肯定是越来越好的，因为用到的信息量增大了。

### 区域属性

区域属性是区域被标记（分割）后进行区域分析（或测量）的重要特征。

别的就不提了，说说周长和面积。给定一个简单多边形，其边界上的点坐标都是整数坐标（即网格点），用 Pick's Formula 可以计算该多边形的面积 $A(p)$：

$$
A(p)=n_{I}+\frac{n_{B}}{2}-1
$$

其中：
- $A(p)$ 是多边形的面积；
- $n_I$ 是多边形内部的网格点数（即多边形内部的整数点数）；
- $n_B$ 是多边形边界上的网格点数（即多边形边界上的整数点数）。

## Chapter 3: Enhancement

本章讲空域上的图像增强。

![Grayscale_Image_Enhancement_Method_Based_on_Spatial_Domain](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Grayscale_Image_Enhancement_Method_Based_on_Spatial_Domain.png)

### 灰度变换

![Basic_Gray_Level_Transformation_Functions](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Basic_Gray_Level_Transformation_Functions.png)

其中，对数变换的公式为：

$$
s=c\log(1+r)
$$

幂律变换（Power-Law Transformations）的公式为：

$$
s=cr^{\gamma}
$$

其中，$c$ 和 $\gamma$ 是正常数，$r$ 是原始图像的灰度值，$s$ 是变换后的灰度值。

幂律变换又称伽马校正，作用是避免灰度失真，校正显示屏的非线性变换。

显然，随着 $\gamma$ 的增大，曲线向下凹，图像的对比度增大。

------

灰度变换的输入输出当然可以是任意函数，因此可以随意调整，比如高亮某个灰度范围等等。

####  位平面切片

位平面切片（Bit-Plane Slicing）是一种灰度变换，它将图像的每个像素的二进制表示中的每一位作为一个灰度级，从而将图像分解为 8 个位平面。

显然，越是高位，其位平面越是低频，越保留图像的总体样子；越是低位，其位平面越是高频，越保留图像的细节。

Q：如何从一幅图像中抽出某一张位平面切片？

A：拿二进制串（如`00001000`）去和灰度值二进制按位求与（AND）。

<details>
    <summary>没出现在复习幻灯片的《二值化》</summary>
    <h4>二值化</h4>
<h5>Isodata 算法</h5>
<p>Isodata 算法是一种自动确定阈值的方法，它是一种迭代算法，其基本思想是：首先假设阈值为 <mjx-container class="MathJax" jax="SVG" style="position: relative;"><svg xmlns="http://www.w3.org/2000/svg" width="1.593ex" height="1.532ex" role="img" focusable="false" viewBox="0 -677 704 677" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" style="vertical-align: 0px;"><defs><path id="MJX-14262-TEX-I-1D447" d="M40 437Q21 437 21 445Q21 450 37 501T71 602L88 651Q93 669 101 677H569H659Q691 677 697 676T704 667Q704 661 687 553T668 444Q668 437 649 437Q640 437 637 437T631 442L629 445Q629 451 635 490T641 551Q641 586 628 604T573 629Q568 630 515 631Q469 631 457 630T439 622Q438 621 368 343T298 60Q298 48 386 46Q418 46 427 45T436 36Q436 31 433 22Q429 4 424 1L422 0Q419 0 415 0Q410 0 363 1T228 2Q99 2 64 0H49Q43 6 43 9T45 27Q49 40 55 46H83H94Q174 46 189 55Q190 56 191 56Q196 59 201 76T241 233Q258 301 269 344Q339 619 339 625Q339 630 310 630H279Q212 630 191 624Q146 614 121 583T67 467Q60 445 57 441T43 437H40Z"></path></defs><g stroke="currentColor" fill="currentColor" stroke-width="0" transform="scale(1,-1)"><g data-mml-node="math"><g data-mml-node="mi"><use data-c="1D447" xlink:href="#MJX-14262-TEX-I-1D447"></use></g></g></g></svg></mjx-container><script type="math/tex">T</script>，然后将图像中的所有像素分为两类：灰度值大于 <mjx-container class="MathJax" jax="SVG" style="position: relative;"><svg xmlns="http://www.w3.org/2000/svg" width="1.593ex" height="1.532ex" role="img" focusable="false" viewBox="0 -677 704 677" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" style="vertical-align: 0px;"><defs><path id="MJX-14262-TEX-I-1D447" d="M40 437Q21 437 21 445Q21 450 37 501T71 602L88 651Q93 669 101 677H569H659Q691 677 697 676T704 667Q704 661 687 553T668 444Q668 437 649 437Q640 437 637 437T631 442L629 445Q629 451 635 490T641 551Q641 586 628 604T573 629Q568 630 515 631Q469 631 457 630T439 622Q438 621 368 343T298 60Q298 48 386 46Q418 46 427 45T436 36Q436 31 433 22Q429 4 424 1L422 0Q419 0 415 0Q410 0 363 1T228 2Q99 2 64 0H49Q43 6 43 9T45 27Q49 40 55 46H83H94Q174 46 189 55Q190 56 191 56Q196 59 201 76T241 233Q258 301 269 344Q339 619 339 625Q339 630 310 630H279Q212 630 191 624Q146 614 121 583T67 467Q60 445 57 441T43 437H40Z"></path></defs><g stroke="currentColor" fill="currentColor" stroke-width="0" transform="scale(1,-1)"><g data-mml-node="math"><g data-mml-node="mi"><use data-c="1D447" xlink:href="#MJX-14262-TEX-I-1D447"></use></g></g></g></svg></mjx-container><script type="math/tex">T</script> 的像素为一类，灰度值小于 <mjx-container class="MathJax" jax="SVG" style="position: relative;"><svg xmlns="http://www.w3.org/2000/svg" width="1.593ex" height="1.532ex" role="img" focusable="false" viewBox="0 -677 704 677" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" style="vertical-align: 0px;"><defs><path id="MJX-14262-TEX-I-1D447" d="M40 437Q21 437 21 445Q21 450 37 501T71 602L88 651Q93 669 101 677H569H659Q691 677 697 676T704 667Q704 661 687 553T668 444Q668 437 649 437Q640 437 637 437T631 442L629 445Q629 451 635 490T641 551Q641 586 628 604T573 629Q568 630 515 631Q469 631 457 630T439 622Q438 621 368 343T298 60Q298 48 386 46Q418 46 427 45T436 36Q436 31 433 22Q429 4 424 1L422 0Q419 0 415 0Q410 0 363 1T228 2Q99 2 64 0H49Q43 6 43 9T45 27Q49 40 55 46H83H94Q174 46 189 55Q190 56 191 56Q196 59 201 76T241 233Q258 301 269 344Q339 619 339 625Q339 630 310 630H279Q212 630 191 624Q146 614 121 583T67 467Q60 445 57 441T43 437H40Z"></path></defs><g stroke="currentColor" fill="currentColor" stroke-width="0" transform="scale(1,-1)"><g data-mml-node="math"><g data-mml-node="mi"><use data-c="1D447" xlink:href="#MJX-14262-TEX-I-1D447"></use></g></g></g></svg></mjx-container><script type="math/tex">T</script> 的像素为另一类。然后计算这两类像素的平均灰度值 <mjx-container class="MathJax" jax="SVG" style="position: relative;"><svg xmlns="http://www.w3.org/2000/svg" width="2.974ex" height="1.339ex" role="img" focusable="false" viewBox="0 -442 1314.6 592" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" style="vertical-align: -0.339ex;"><defs><path id="MJX-14259-TEX-I-1D45A" d="M21 287Q22 293 24 303T36 341T56 388T88 425T132 442T175 435T205 417T221 395T229 376L231 369Q231 367 232 367L243 378Q303 442 384 442Q401 442 415 440T441 433T460 423T475 411T485 398T493 385T497 373T500 364T502 357L510 367Q573 442 659 442Q713 442 746 415T780 336Q780 285 742 178T704 50Q705 36 709 31T724 26Q752 26 776 56T815 138Q818 149 821 151T837 153Q857 153 857 145Q857 144 853 130Q845 101 831 73T785 17T716 -10Q669 -10 648 17T627 73Q627 92 663 193T700 345Q700 404 656 404H651Q565 404 506 303L499 291L466 157Q433 26 428 16Q415 -11 385 -11Q372 -11 364 -4T353 8T350 18Q350 29 384 161L420 307Q423 322 423 345Q423 404 379 404H374Q288 404 229 303L222 291L189 157Q156 26 151 16Q138 -11 108 -11Q95 -11 87 -5T76 7T74 17Q74 30 112 181Q151 335 151 342Q154 357 154 369Q154 405 129 405Q107 405 92 377T69 316T57 280Q55 278 41 278H27Q21 284 21 287Z"></path><path id="MJX-14259-TEX-N-31" d="M213 578L200 573Q186 568 160 563T102 556H83V602H102Q149 604 189 617T245 641T273 663Q275 666 285 666Q294 666 302 660V361L303 61Q310 54 315 52T339 48T401 46H427V0H416Q395 3 257 3Q121 3 100 0H88V46H114Q136 46 152 46T177 47T193 50T201 52T207 57T213 61V578Z"></path></defs><g stroke="currentColor" fill="currentColor" stroke-width="0" transform="scale(1,-1)"><g data-mml-node="math"><g data-mml-node="msub"><g data-mml-node="mi"><use data-c="1D45A" xlink:href="#MJX-14259-TEX-I-1D45A"></use></g><g data-mml-node="mn" transform="translate(911,-150) scale(0.707)"><use data-c="31" xlink:href="#MJX-14259-TEX-N-31"></use></g></g></g></g></svg></mjx-container><script type="math/tex">m_1</script> 和 <mjx-container class="MathJax" jax="SVG" style="position: relative;"><svg xmlns="http://www.w3.org/2000/svg" width="2.974ex" height="1.339ex" role="img" focusable="false" viewBox="0 -442 1314.6 592" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" style="vertical-align: -0.339ex;"><defs><path id="MJX-14260-TEX-I-1D45A" d="M21 287Q22 293 24 303T36 341T56 388T88 425T132 442T175 435T205 417T221 395T229 376L231 369Q231 367 232 367L243 378Q303 442 384 442Q401 442 415 440T441 433T460 423T475 411T485 398T493 385T497 373T500 364T502 357L510 367Q573 442 659 442Q713 442 746 415T780 336Q780 285 742 178T704 50Q705 36 709 31T724 26Q752 26 776 56T815 138Q818 149 821 151T837 153Q857 153 857 145Q857 144 853 130Q845 101 831 73T785 17T716 -10Q669 -10 648 17T627 73Q627 92 663 193T700 345Q700 404 656 404H651Q565 404 506 303L499 291L466 157Q433 26 428 16Q415 -11 385 -11Q372 -11 364 -4T353 8T350 18Q350 29 384 161L420 307Q423 322 423 345Q423 404 379 404H374Q288 404 229 303L222 291L189 157Q156 26 151 16Q138 -11 108 -11Q95 -11 87 -5T76 7T74 17Q74 30 112 181Q151 335 151 342Q154 357 154 369Q154 405 129 405Q107 405 92 377T69 316T57 280Q55 278 41 278H27Q21 284 21 287Z"></path><path id="MJX-14260-TEX-N-32" d="M109 429Q82 429 66 447T50 491Q50 562 103 614T235 666Q326 666 387 610T449 465Q449 422 429 383T381 315T301 241Q265 210 201 149L142 93L218 92Q375 92 385 97Q392 99 409 186V189H449V186Q448 183 436 95T421 3V0H50V19V31Q50 38 56 46T86 81Q115 113 136 137Q145 147 170 174T204 211T233 244T261 278T284 308T305 340T320 369T333 401T340 431T343 464Q343 527 309 573T212 619Q179 619 154 602T119 569T109 550Q109 549 114 549Q132 549 151 535T170 489Q170 464 154 447T109 429Z"></path></defs><g stroke="currentColor" fill="currentColor" stroke-width="0" transform="scale(1,-1)"><g data-mml-node="math"><g data-mml-node="msub"><g data-mml-node="mi"><use data-c="1D45A" xlink:href="#MJX-14260-TEX-I-1D45A"></use></g><g data-mml-node="mn" transform="translate(911,-150) scale(0.707)"><use data-c="32" xlink:href="#MJX-14260-TEX-N-32"></use></g></g></g></g></svg></mjx-container><script type="math/tex">m_2</script>，并计算新的阈值 <mjx-container class="MathJax" jax="SVG" style="position: relative;"><svg xmlns="http://www.w3.org/2000/svg" width="17.346ex" height="2.262ex" role="img" focusable="false" viewBox="0 -750 7667.1 1000" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" style="vertical-align: -0.566ex;"><defs><path id="MJX-14261-TEX-I-1D447" d="M40 437Q21 437 21 445Q21 450 37 501T71 602L88 651Q93 669 101 677H569H659Q691 677 697 676T704 667Q704 661 687 553T668 444Q668 437 649 437Q640 437 637 437T631 442L629 445Q629 451 635 490T641 551Q641 586 628 604T573 629Q568 630 515 631Q469 631 457 630T439 622Q438 621 368 343T298 60Q298 48 386 46Q418 46 427 45T436 36Q436 31 433 22Q429 4 424 1L422 0Q419 0 415 0Q410 0 363 1T228 2Q99 2 64 0H49Q43 6 43 9T45 27Q49 40 55 46H83H94Q174 46 189 55Q190 56 191 56Q196 59 201 76T241 233Q258 301 269 344Q339 619 339 625Q339 630 310 630H279Q212 630 191 624Q146 614 121 583T67 467Q60 445 57 441T43 437H40Z"></path><path id="MJX-14261-TEX-N-3D" d="M56 347Q56 360 70 367H707Q722 359 722 347Q722 336 708 328L390 327H72Q56 332 56 347ZM56 153Q56 168 72 173H708Q722 163 722 153Q722 140 707 133H70Q56 140 56 153Z"></path><path id="MJX-14261-TEX-N-28" d="M94 250Q94 319 104 381T127 488T164 576T202 643T244 695T277 729T302 750H315H319Q333 750 333 741Q333 738 316 720T275 667T226 581T184 443T167 250T184 58T225 -81T274 -167T316 -220T333 -241Q333 -250 318 -250H315H302L274 -226Q180 -141 137 -14T94 250Z"></path><path id="MJX-14261-TEX-I-1D45A" d="M21 287Q22 293 24 303T36 341T56 388T88 425T132 442T175 435T205 417T221 395T229 376L231 369Q231 367 232 367L243 378Q303 442 384 442Q401 442 415 440T441 433T460 423T475 411T485 398T493 385T497 373T500 364T502 357L510 367Q573 442 659 442Q713 442 746 415T780 336Q780 285 742 178T704 50Q705 36 709 31T724 26Q752 26 776 56T815 138Q818 149 821 151T837 153Q857 153 857 145Q857 144 853 130Q845 101 831 73T785 17T716 -10Q669 -10 648 17T627 73Q627 92 663 193T700 345Q700 404 656 404H651Q565 404 506 303L499 291L466 157Q433 26 428 16Q415 -11 385 -11Q372 -11 364 -4T353 8T350 18Q350 29 384 161L420 307Q423 322 423 345Q423 404 379 404H374Q288 404 229 303L222 291L189 157Q156 26 151 16Q138 -11 108 -11Q95 -11 87 -5T76 7T74 17Q74 30 112 181Q151 335 151 342Q154 357 154 369Q154 405 129 405Q107 405 92 377T69 316T57 280Q55 278 41 278H27Q21 284 21 287Z"></path><path id="MJX-14261-TEX-N-31" d="M213 578L200 573Q186 568 160 563T102 556H83V602H102Q149 604 189 617T245 641T273 663Q275 666 285 666Q294 666 302 660V361L303 61Q310 54 315 52T339 48T401 46H427V0H416Q395 3 257 3Q121 3 100 0H88V46H114Q136 46 152 46T177 47T193 50T201 52T207 57T213 61V578Z"></path><path id="MJX-14261-TEX-N-2B" d="M56 237T56 250T70 270H369V420L370 570Q380 583 389 583Q402 583 409 568V270H707Q722 262 722 250T707 230H409V-68Q401 -82 391 -82H389H387Q375 -82 369 -68V230H70Q56 237 56 250Z"></path><path id="MJX-14261-TEX-N-32" d="M109 429Q82 429 66 447T50 491Q50 562 103 614T235 666Q326 666 387 610T449 465Q449 422 429 383T381 315T301 241Q265 210 201 149L142 93L218 92Q375 92 385 97Q392 99 409 186V189H449V186Q448 183 436 95T421 3V0H50V19V31Q50 38 56 46T86 81Q115 113 136 137Q145 147 170 174T204 211T233 244T261 278T284 308T305 340T320 369T333 401T340 431T343 464Q343 527 309 573T212 619Q179 619 154 602T119 569T109 550Q109 549 114 549Q132 549 151 535T170 489Q170 464 154 447T109 429Z"></path><path id="MJX-14261-TEX-N-29" d="M60 749L64 750Q69 750 74 750H86L114 726Q208 641 251 514T294 250Q294 182 284 119T261 12T224 -76T186 -143T145 -194T113 -227T90 -246Q87 -249 86 -250H74Q66 -250 63 -250T58 -247T55 -238Q56 -237 66 -225Q221 -64 221 250T66 725Q56 737 55 738Q55 746 60 749Z"></path><path id="MJX-14261-TEX-N-2F" d="M423 750Q432 750 438 744T444 730Q444 725 271 248T92 -240Q85 -250 75 -250Q68 -250 62 -245T56 -231Q56 -221 230 257T407 740Q411 750 423 750Z"></path></defs><g stroke="currentColor" fill="currentColor" stroke-width="0" transform="scale(1,-1)"><g data-mml-node="math"><g data-mml-node="mi"><use data-c="1D447" xlink:href="#MJX-14261-TEX-I-1D447"></use></g><g data-mml-node="mo" transform="translate(981.8,0)"><use data-c="3D" xlink:href="#MJX-14261-TEX-N-3D"></use></g><g data-mml-node="mo" transform="translate(2037.6,0)"><use data-c="28" xlink:href="#MJX-14261-TEX-N-28"></use></g><g data-mml-node="msub" transform="translate(2426.6,0)"><g data-mml-node="mi"><use data-c="1D45A" xlink:href="#MJX-14261-TEX-I-1D45A"></use></g><g data-mml-node="mn" transform="translate(911,-150) scale(0.707)"><use data-c="31" xlink:href="#MJX-14261-TEX-N-31"></use></g></g><g data-mml-node="mo" transform="translate(3963.3,0)"><use data-c="2B" xlink:href="#MJX-14261-TEX-N-2B"></use></g><g data-mml-node="msub" transform="translate(4963.6,0)"><g data-mml-node="mi"><use data-c="1D45A" xlink:href="#MJX-14261-TEX-I-1D45A"></use></g><g data-mml-node="mn" transform="translate(911,-150) scale(0.707)"><use data-c="32" xlink:href="#MJX-14261-TEX-N-32"></use></g></g><g data-mml-node="mo" transform="translate(6278.1,0)"><use data-c="29" xlink:href="#MJX-14261-TEX-N-29"></use></g><g data-mml-node="TeXAtom" data-mjx-texclass="ORD" transform="translate(6667.1,0)"><g data-mml-node="mo"><use data-c="2F" xlink:href="#MJX-14261-TEX-N-2F"></use></g></g><g data-mml-node="mn" transform="translate(7167.1,0)"><use data-c="32" xlink:href="#MJX-14261-TEX-N-32"></use></g></g></g></svg></mjx-container><script type="math/tex">T=(m_1+m_2)/2</script>。重复上述过程，直到 <mjx-container class="MathJax" jax="SVG" style="position: relative;"><svg xmlns="http://www.w3.org/2000/svg" width="1.593ex" height="1.532ex" role="img" focusable="false" viewBox="0 -677 704 677" xmlns:xlink="http://www.w3.org/1999/xlink" aria-hidden="true" style="vertical-align: 0px;"><defs><path id="MJX-14262-TEX-I-1D447" d="M40 437Q21 437 21 445Q21 450 37 501T71 602L88 651Q93 669 101 677H569H659Q691 677 697 676T704 667Q704 661 687 553T668 444Q668 437 649 437Q640 437 637 437T631 442L629 445Q629 451 635 490T641 551Q641 586 628 604T573 629Q568 630 515 631Q469 631 457 630T439 622Q438 621 368 343T298 60Q298 48 386 46Q418 46 427 45T436 36Q436 31 433 22Q429 4 424 1L422 0Q419 0 415 0Q410 0 363 1T228 2Q99 2 64 0H49Q43 6 43 9T45 27Q49 40 55 46H83H94Q174 46 189 55Q190 56 191 56Q196 59 201 76T241 233Q258 301 269 344Q339 619 339 625Q339 630 310 630H279Q212 630 191 624Q146 614 121 583T67 467Q60 445 57 441T43 437H40Z"></path></defs><g stroke="currentColor" fill="currentColor" stroke-width="0" transform="scale(1,-1)"><g data-mml-node="math"><g data-mml-node="mi"><use data-c="1D447" xlink:href="#MJX-14262-TEX-I-1D447"></use></g></g></g></svg></mjx-container><script type="math/tex">T</script> 不再变化为止。</p>
<h5>Otsu 算法</h5>
<p>Otsu 算法基于聚类的思想，将图像按照灰度值分为两类，使得类内方差最小，类间方差最大。通过计算方差来寻找合适的阈值。</p>
</details>

### 直方图

直方图（Histogram）是一种统计报告，它对图像中每个灰度级的像素数进行计数。

#### 直方图均衡化

直方图均衡化（Histogram Equalization）是一种用于增强图像对比度的图像处理技术。它通过重新分布图像的像素值，使得图像的直方图变得更加均匀，从而增强图像的视觉效果。

直方图均衡化的基本思想是将图像的像素值映射到一个新的值，使得映射后的直方图具有均匀分布。这样做的目的是使得图像中的像素值范围更广，从而增加图像的对比度。

下面是直方图均衡化的一般步骤：

1. 计算原始图像的灰度直方图：将图像转换为灰度图像，并统计每个灰度级别的像素数目，得到原始图像的直方图。

2. 计算累积分布函数（CDF）：对原始图像的直方图进行归一化，得到每个灰度级别的累积概率。CDF表示了每个灰度级别在图像中的累积分布情况。

3. 映射像素值：根据CDF，计算每个灰度级别的新的映射值。这可以**通过将CDF值乘以最大灰度级别**来实现。

4. 应用映射：将原始图像中的每个像素值替换为其对应的映射值。这样就完成了直方图均衡化的过程。

直方图均衡化可以使得图像的像素值范围更广，从而增加图像的对比度。它在许多图像处理应用中都有广泛的应用，例如图像增强、图像匹配和图像分割等。然而，直方图均衡化也可能导致图像的噪声增加，因为它会放大图像中的细微差异。

#### 直方图规定化

直方图均衡不允许交互式图像增强，并且仅生成一个结果：均匀直方图的近似值。但有时，我们需要能够指定能够突出显示某些灰度范围的特定直方图形状。为此，我们需要直方图规定化（Histogram Matching）。

步骤：

1. 均衡化原图。$s=T(r_{k})=\sum_{j=0}^{k}\frac{n_{j}}{n}$，其中，$n$ 是像素总数，$n_j$ 是灰度级别 $j$ 的像素数。
2. 指定所需的密度函数并获得变换函数 $G(z)$。$v=G(z)=\sum_{0}^{z}p_{z}(w)\approx\sum_{i=0}^{z}\frac{n_{i}}{n}$，其中，$p_z(w)$ 是所需的密度函数，$n_i$ 是灰度级别 $i$ 的像素数。
3. 对步骤一中的结果进行反变换。$z=G^{-1}(s)\rightarrow z=G^{-1}[T(r)]$，其中，$G^{-1}$ 是 $G$ 的反变换。

反变换函数主要有两种：

1. SML（Single Mapping Law）
   1. 有偏
   2. 自原始累积直方图到匹配累积直方图寻找对应关系
   3. 简单，但有时有较大的取整误差
2. GML（Group Mapping Law）
   1. 无偏
   2. 自匹配累积直方图到原始累积直方图寻找对应关系
   3. GML产生的误差远小于SML

### 局部增强

对局部增强，我们需要根据每个像素邻域的灰度分布设计变换函数，基于像素邻域对灰度的贡献设计变换函数。

说人话就是前面的方法不要全局应用了，划个小区域做。

定义一个正方形（或矩形）邻域，并将该区域的中心从一个像素移动到另一个像素。

两种方式：Pixel-to-pixel的（类似于滤波核）和 Non-overlapping region的（就是说划分成多个不重叠的矩形区域）

### 空域滤波

先来点有手就行的。

均值滤波：

$$
g(x,y)=\frac{1}{9}\sum_{s=-1}^{1}\sum_{t=-1}^{1}f(x+s,y+t)
$$

最大值滤波：

$$
g(x,y)=\max_{s,t}f(x+s,y+t)
$$

最小值滤波：

$$
g(x,y)=\min_{s,t}f(x+s,y+t)
$$

线性滤波（说白了就是均值滤波的均值变成了加权平均）：

$$
g(x,y)=\sum_{s=-1}^{1}\sum_{t=-1}^{1}w(s,t)f(x+s,y+t)
$$

其中，$w(s,t)$ 是权重函数，满足 $\sum_{s=-1}^{1}\sum_{t=-1}^{1}w(s,t)=1$。

> 为什么要满足这个条件呢？因为这样才能保证灰度值不变。

------

有一些滤波器是行列可分离的，在二维分离意味着，卷积核可以分解成两个一维向量的乘积。

$$
\begin{aligned}
g(x,y)&=\sum_{s=-a}^{a}\sum_{t=-b}^{b}w(s,t)f(x+s,y+t)\\
&=\sum_{s=-a}^{a}w_{1}(x)\sum_{t=-b}^{b}w_{2}(y)f(x+s,y+t)
\end{aligned}
$$

二维高斯函数卷积可分两步进行：首先将图像与一维高斯函数卷积，然后将卷积结果与方向垂直的相同一维高斯函数卷积。因此，二维高斯函数的计算量随模板宽度成线性增长而不是成平方增长，所以较大尺寸的高斯滤波器可以得以有效的实现。

#### 低通滤波

均值滤波就可以，不过不如 Gaussian 滤波好。

Gaussian 滤波：

$$
g(x,y)=\frac{1}{16}\begin{bmatrix}
1&2&1\\
2&4&2\\
1&2&1\\
\end{bmatrix}\ast f(x,y)
$$

#### 高通滤波

滤波核中心为正，边缘为负，这样就可以突出差异，可以说非常高频了。

一个简单的高通滤波器：

$$
g(x,y)=\begin{bmatrix}
-1&-1&-1\\
-1&8&-1\\
-1&-1&-1\\
\end{bmatrix}\ast f(x,y)
$$

来点 Laplacian 滤波器：

$$
g(x,y)=\begin{bmatrix}
0&-1&0\\
-1&4&-1\\
0&-1&0\\
\end{bmatrix}\ast f(x,y)
$$

这是一个4-邻域的 Laplacian 滤波器，如果加上原图像，就可以锐化边缘，即：

$$
\begin{bmatrix}
0&-1&0\\
-1&4&-1\\
0&-1&0\\
\end{bmatrix}+
\begin{bmatrix}
0&0&0\\
0&1&0\\
0&0&0\\
\end{bmatrix}=
\begin{bmatrix}
0&-1&0\\
-1&5&-1\\
0&-1&0\\
\end{bmatrix}
$$

8-邻域的 Laplacian 滤波器：

$$
g(x,y)=\begin{bmatrix}
-1&-1&-1\\
-1&8&-1\\
-1&-1&-1\\
\end{bmatrix}\ast f(x,y)
$$

注意滤波器中的系数和为 0。

#### 微分滤波器

- 一阶导数在图像中产生较厚的边缘
- 二阶导数对精细细节有更强的响应
- 一阶导数对灰度阶跃有更强的响应
- 二阶导数在灰度阶跃变化时产生双重响应
- 二阶导数对 line 的响应强于对 step 的响应，对 point 的响应强于对 line 的响应

> 经典幻灯片不说人话。解释一下：
>
> 1. 一阶导数在图像中产生较厚的边缘：一阶导数滤波器对图像中的边缘具有较强的响应。当应用一阶导数滤波器时，边缘区域的像素值变化较大，导致滤波器的响应较强，从而产生较厚的边缘。
>
> 2. 二阶导数对精细细节有更强的响应：二阶导数滤波器对图像中的细节变化具有更敏感的响应。细节通常包含更高频率的变化，而二阶导数滤波器对高频信号有更强的响应，因此它能够捕捉到图像中的精细细节。
>
> 3. 一阶导数对灰度阶跃有更强的响应：当图像中出现灰度阶跃（像素值突然变化）时，一阶导数滤波器能够产生较强的响应。这是因为阶跃边缘的像素值变化较大，导致一阶导数滤波器的输出较大。
>
> 4. 二阶导数在灰度阶跃变化时产生双重响应：与一阶导数不同，二阶导数滤波器在灰度阶跃变化时会产生双重响应。这是因为二阶导数滤波器对图像中的边缘变化有两个极值点（正负），因此在阶跃边缘的两侧都会产生响应。
>
> 5. 二阶导数对线段的响应强于阶跃的响应，对点的响应强于线段的响应：二阶导数滤波器对不同类型的特征有不同的响应强度。它对线段的响应较强，因为线段具有较大的二阶导数值。相比之下，阶跃边缘的二阶导数值较小。对于点（如孤立的像素），二阶导数滤波器的响应最强，因为点处的二阶导数为零，而在点周围的像素处有较大的二阶导数值。

我们约定一个$3x3$邻域如下：

$$
\begin{bmatrix}
z_{1}&z_{2}&z_{3}\\
z_{4}&z_{5}&z_{6}\\
z_{7}&z_{8}&z_{9}\\
\end{bmatrix}
$$

梯度算子：

$$
\nabla f=\begin{bmatrix}
G_{x}\\
G_{y}\\
\end{bmatrix}=\begin{bmatrix}
\frac{\partial f}{\partial x}\\
\frac{\partial f}{\partial y}\\
\end{bmatrix}
$$

平均类似于积分，会导致模糊，因此**差分**预计会产生相反的结果并使图像锐化。

> 幻灯片似乎在混用微分和差分，不过在计算机上确实是差分。问题不大。

##### 一阶微分

$$
f^{\prime}(x)=f(x+1)-f(x)\\
f^{\prime\prime}=f(x+1)+f(x-1)-2f(x)
$$

###### Roberts 算子

$$
\begin{aligned}
G_{x}&=(z_{9}-z_{5})\\
G_{y}&=(z_{8}-z_{6})\\
\end{aligned}
$$

近似（Roberts 交叉梯度算子）：

$$
\nabla f\approx|z_{9}-z_{5}|+|z_{8}-z_{6}|
$$

Roberts 算子模板：

$G_{x}$：

$$
\begin{bmatrix}
-1&0\\
0&1\\
\end{bmatrix}
$$

$G_{y}$：

$$
\begin{bmatrix}
0&-1\\
1&0\\
\end{bmatrix}
$$

###### Prewitt 算子

近似（Prewitt 交叉梯度算子）：

$$
\nabla f=|(z_{7}+z_{8}+z_{9})-(z_{1}+z_{2}+z_{3})|+|(z_{3}+z_{6}+z_{9})-(z_{1}+z_{4}+z_{7})|
$$

$G_{x}$：

$$
\begin{bmatrix}
-1&-1&-1\\
0&0&0\\
1&1&1\\
\end{bmatrix}
$$

$G_{y}$：

$$
\begin{bmatrix}
-1&0&1\\
-1&0&1\\
-1&0&1\\
\end{bmatrix}
$$

###### Sobel 算子

近似（Sobel 交叉梯度算子）：

$$
\nabla f=|(z_{7}+2z_{8}+z_{9})-(z_{1}+2z_{2}+z_{3})|+|(z_{3}+2z_{6}+z_{9})-(z_{1}+2z_{4}+z_{7})|
$$

$G_{x}$：

$$
\begin{bmatrix}
-1&-2&-1\\
0&0&0\\
1&2&1\\
\end{bmatrix}
$$

$G_{y}$：

$$
\begin{bmatrix}
-1&0&1\\
-2&0&2\\
-1&0&1\\
\end{bmatrix}
$$

##### 二阶微分

$$
\nabla^{2}f=\left[f(x+1,y)+f(x-1,y)+f(x,y+1)+f(x,y-1)\right]-4f(x,y)
$$

二阶微分只说 Laplacian。

$$
g(x,y)=\begin{cases}
f(x,y)-\nabla^{2}f(x,y)&\text{如果滤波器中心是负数}\\
f(x,y)+\nabla^{2}f(x,y)&\text{如果滤波器中心是正数}\\
\end{cases}
$$

#### 总结

**平滑**

- 正值
- 和为 1
- 平滑量与模板尺寸成正比
- 过低频，滤高频

**微分**

- 相反的符号（就是说有正有负）用于在高对比度区域获得高响应
- 和为 0，这是为了不响应平坦区域
- 高对比度点的绝对值高

## Chapter 4: Frequency

空域表示：一幅图像是一个二维函数$f(x,y)$，其中$x$和$y$是空间坐标，幅值$f$在任何一点$(x,y)$处的值称为该点的**强度**或**灰度**。

类似于 Taylor 级数，任何连续周期信号都可以表示为一系列正弦曲线的和。频率从低到高排列，一些成分为直线是因为其振幅为零。也就是说，为了组成特殊的信号，有些正弦波成分是不需要的。

不同频率的正弦波被称为**频率分量**。

因为正弦波是一个圆周运动在一条直线的投影，所以频域的基本单元也可以理解成一个始终在旋转的圆。

在频域中，频率越大说明原始信号变化速度越快；频率越小说明原始信号越平缓。当频率为 0 时，表示直流信号，没有变化。因此，频率的大小反应了信号的变化快慢。且高频分量解释信号的突变部分，而低频分量决定信号的整体形
象。

> 我觉得这个没有人不知道。

空域：图像的原始表示，即图像的像素矩阵。

频域：通过 Fourier 变换将图像从空域转换到频域。处理的是图像的频率分量，而不是图像的像素值。

时域：图像灰度的分布函数。所谓空域通过 Fourier 变换转频域，倒不如说是时域通过 Fourier 变换转频域。

------

在开始和 Mr. Fourier 打交道之前，我们先约定符号，我们希望在整章中，公式中的符号总是一致的。

- $f(x,y)$：一般表示图像的空域表示，也就是图像的像素矩阵。其实，倒不如说是图像的时域表示。
- $F(u,v)$：图像的频域表示，也就是图像的频率分量。
- $\mathfrak{F}$：Fourier 变换。
- 和幻灯片中不一样，我们直接使用$i$表示虚数单位，说实话使用$j$也一样，没什么区别。

### Fourier 变换

$$
F(u)=\int_{-\infty}^{\infty} f(x) e^{-i 2 \pi u x} d x
$$

其中，$F(u)$ 是频率为 $u$ 的正弦波在 $f(x)$ 中的振幅。

我们也很关心滤波函数 $H(u)$，它处理 Fourier 变换后的图像（也就是频域），然后再进行反变换回到空域。

#### 一维连续 Fourier 变换

Fourier 变换：

$$
F(u)=\int_{-\infty}^{\infty} f(x) e^{-i 2 \pi u x}\,\mathrm{d}x
$$

Fourier 逆变换：

$$
f(x)=\int_{-\infty}^{\infty} F(u) e^{i 2 \pi u x}\,\mathrm{d}u
$$

可以这样理解：Fourier 逆变换中，$e$的指数是 Fourier 变换中的相反数，这样，$e$的指数相加就是 0，如果将一个 Fourier 变换和一个 Fourier 逆变换连续进行，那么积分中的 $e$ 的指数相加就是 0，积分结果就是 1，也就是说，连续进行 Fourier 变换和 Fourier 逆变换，最后结果不变。

Fourier 变换的物理意义：将图像的灰度分布函数变换为图像的频率分布函数。

Fourier 逆变换：将图像的频率分布函数变换为图像的灰度分布函数。

#### 基函数

基函数：

$$
\phi_{u}(x)=e^{-i 2 \pi u x}
$$

Euler 公式：

$$
e^{i \theta}=\cos \theta+i \sin \theta
$$

因此，Fourier 变换可以写成：

$$
\begin{aligned}
F(u)&=\int_{-\infty}^{\infty}f(x)e^{-i 2 \pi u x}\,\mathrm{d}x\\
&=\int_{-\infty}^{\infty}f(x)\left[\cos(2\pi u x)-i\sin(2\pi u x)\right]\,\mathrm{d}x\\
\end{aligned}
$$

其中，$u$ 体现了频率性质。

#### 一维离散 Fourier 变换

先看一维连续 Fourier 变换：

$$
F(u)=\int_{-\infty}^{\infty} f(x) e^{-i 2 \pi u x}\,\mathrm{d}x
$$

将离散 Fourier 变换应用采样函数，有 DFT：

$$
F(u)=\frac{1}{M} \sum_{x=0}^{M-1} f(x) e^{-i 2 \pi u x / M}
$$

其中，$M$ 是采样点数，$u=0,1,2,\cdots,M-1$。请注意，$u$ 的取值范围是 $[0,M-1]$，每个都要取的，下面引述 DFT 公式的时候不再赘述。

再看一维连续 Fourier 逆变换：

$$
f(x)=\int_{-\infty}^{\infty} F(u) e^{i 2 \pi u x}\,\mathrm{d}u
$$

则有 IDFT：

$$
f(x)=\sum_{u=0}^{M-1} F(u) e^{i 2 \pi u x / M}
$$

注意，图像的 Fourier 变换及其逆变换总是存在的。

#### 采样

采样增量在空域与频域的关系：

$$
\Delta x=\frac{1}{M\Delta u}\\
\Delta y=\frac{1}{N\Delta v}
$$

这里，$M$ 和 $N$ 是采样点数，$\Delta x$ 和 $\Delta y$ 是空域采样增量，$\Delta u$ 和 $\Delta v$ 是频域采样增量。

这样，DFT 可以写成：

$$
\begin{aligned}
F(u)&=\frac{1}{M}\sum_{x=0}^{M-1}f(x)e^{-i 2 \pi u x / M}\\
&=\frac{1}{M}\sum_{x=0}^{K-1}f(x)Ae^{-i 2 \pi u x / M}\\
&=\frac{A}{M}\sum_{x=0}^{K-1}f(x)e^{-i 2 \pi u x / M}
\end{aligned}
$$

其中：

- $A$：表示一个常数或者系数，用来调整 DFT 的幅度。在公式中，$A$是一个常数，可以用于缩放 DFT 的结果。通常情况下，$A$的取值为 1，表示不进行幅度调整。
- $M$：表示离散傅里叶变换（DFT）的长度或者采样点数。它决定了 DFT 的频率分辨率和计算的复杂度。在公式中，$M$代表了 DFT 的长度，也就是采样点的个数。$M$通常是一个正整数，且必须大于等于$K$。
- $K$：表示输入序列的长度或者有效数据点数。在公式中，$K$代表了输入序列的长度，也就是有效数据点的个数。$K$通常是一个正整数，且必须小于等于$M$。

需要注意的是，$M$和$K$的取值关系是 $K \leq M$，即有效数据点数$K$不能超过 DFT 的长度$M$。当$K<M$时，DFT 公式中的求和项只对前$K$个数据点进行求和，忽略了后面的$M-K$个数据点。这样可以在计算 DFT 时只考虑有效数据，提高计算效率。

为什么要区分$K$和$M$呢？这是因为在 DFT 中，我们通常将输入序列看作是周期为$M$的周期信号，其中只有前$K$个数据点是有效的，后面的$M-K$个数据点被认为是零值或者被忽略。

而$F(0)$自然就有：

$$
\begin{aligned}
F(0)&=\frac{A}{M}\sum_{x=0}^{K-1}f(x)\\
&=\frac{A}{M}\sum_{x=0}^{K-1}1\\
&=\frac{AK}{M}
\end{aligned}
$$

![Sampling_in_Fourier_Transformation](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Sampling_in_Fourier_Transformation.png)

#### 二维连续 Fourier 变换

具有两个变量的函数 $f(x,y)$ 的二维连续 Fourier 变换为：

$$
F(u,v)=\int_{-\infty}^{\infty}\int_{-\infty}^{\infty}f(x,y)e^{-i 2 \pi (ux+vy)}\,\mathrm{d}x\,\mathrm{d}y
$$

二维连续 Fourier 逆变换为：

$$
f(x,y)=\int_{-\infty}^{\infty}\int_{-\infty}^{\infty}F(u,v)e^{i 2 \pi (ux+vy)}\,\mathrm{d}u\,\mathrm{d}v
$$

其中，$u$ 和 $v$ 是频率空间的变量。

#### 二维离散 Fourier 变换

二维离散 Fourier 变换为：

$$
F(u,v)=\frac{1}{MN}\sum_{x=0}^{M-1}\sum_{y=0}^{N-1}f(x,y)e^{-i 2 \pi (ux/M+vy/N)}
$$

其中，$M$ 和 $N$ 是采样点数。正如一维离散 Fourier 变换一样，$N$ 其实就是第二个维度上的 $M$。

二维离散 Fourier 逆变换为：

$$
f(x,y)=\sum_{u=0}^{M-1}\sum_{v=0}^{N-1}F(u,v)e^{i 2 \pi (ux/M+vy/N)}
$$

#### Fourier 频谱

$$
|F(u,v)|=\sqrt{\operatorname{Re}^2F(u,v)+\operatorname{Im}^2F(u,v)}
$$

其中，$\operatorname{Re}F(u,v)$ 和 $\operatorname{Im}F(u,v)$ 分别是 $F(u,v)$ 的实部和虚部。

------

相位谱（Phase Spectrum）：

$$
\phi(u,v)=\arctan\left(\frac{\operatorname{Im}F(u,v)}{\operatorname{Re}F(u,v)}\right)
$$

------

功率谱（Power Spectrum）：

$$
\begin{aligned}
P(u,v)&=|F(u,v)|^2\\
&=\operatorname{Re}^2F(u,v)+\operatorname{Im}^2F(u,v)
\end{aligned}
$$

#### Fourier 频谱图移中

在实际图像频谱分析中，由于低频分量区域较小，并且分散在四角，因此不利于对它进行分析。根据图像频谱的周期性和共轭对称性对频谱坐标移位，将所有的低频分量集中到频谱图中心，同时高频分量分散在四周。

所谓移中处理，就是将$F(0,0)$移动到$F(M/2,N/2)$处。

在证明这个事情之前，我们首先要证明 Fourier 变换的线性性质，即$\mathfrak{F}[af(x,y)+bg(x,y)]=a\mathfrak{F}[f(x,y)]+b\mathfrak{F}[g(x,y)]$。

证明如下：

$$
\begin{aligned}
\mathfrak{F}[af(x,y)+bg(x,y)]&=\int_{-\infty}^{\infty}\int_{-\infty}^{\infty}[af(x,y)+bg(x,y)]e^{-i 2 \pi (ux+vy)}\,\mathrm{d}x\,\mathrm{d}y\\
&=a\int_{-\infty}^{\infty}\int_{-\infty}^{\infty}f(x,y)e^{-i 2 \pi (ux+vy)}\,\mathrm{d}x\,\mathrm{d}y+b\int_{-\infty}^{\infty}\int_{-\infty}^{\infty}g(x,y)e^{-i 2 \pi (ux+vy)}\,\mathrm{d}x\,\mathrm{d}y\\
&=a\mathfrak{F}[f(x,y)]+b\mathfrak{F}[g(x,y)]
\end{aligned}
$$

证明完毕。

> 其实说白了就是积分的线性性质。积分是可加的。

这样以后，我们通过卷积定理，得知：

> 在时域和频域之间，卷积操作和乘积操作是互相对偶的。也就是说，时域的卷积在频域中是乘积，时域的乘积在频域中是卷积。

所以：

$$
\begin{aligned}
\mathfrak{F}\left[f(x,y)\cdot g(x,y)\right]&=\frac{1}{2\pi}\mathfrak{F}[f(x,y)]\ast\mathfrak{F}[g(x,y)]\\
&=\frac{1}{2\pi}F(u,v)\ast G(u,v)\\
\end{aligned}
$$

其中，$\ast$表示卷积操作。

------

所以，我们可以得到：

$$
\begin{aligned}
\mathfrak{F}\left[f(x,y)(-1)^{x+y}\right]
&=\sum_{u=0}^{M-1}\sum_{v=0}^{N-1}f(x,y)(-1)^{x+y}e^{-i 2 \pi (ux/M+vy/N)}\\
&=\frac{1}{MN}\sum_{x=0}^{M-1}\sum_{y=0}^{N-1}f(x,y)e^{-i 2 \pi ((u-M/2)x/M+(v-N/2)y/N)}\\
&=F(u-M/2,v-N/2)\\
\end{aligned}
$$

$\mathfrak{F}$表示 Fourier 变换，$f(x,y)(-1)^{x+y}$表示将图像的原点移动到图像的中心。之所以要乘以$(-1)^{x+y}$，是因为这样可以使得图像的中心在原点，而不是在图像的四角。上面的式子证明了这个移中处理的正确性，它使得低频分量集中在频谱图的中心，而高频分量分散在四周。

> 移中的最后这一步我实在证明不出来了，放点参考资料吧。
>
> https://blog.csdn.net/a493823882/article/details/78460772

#### Fourier 频谱图的应用

Fourier 谱本身的动态范围很大，只有最亮的部分频谱可见，这会导致直接输出的 Fourier 谱图难以观察。因此，我们需要对 Fourier 谱图进行对数变换，以便于观察。

我们一般会走一个对数变换，将其变换到一个合适的范围内。

$$
D(u,v)=c\log(1+|F(u,v)|)
$$

其中，$c$ 是一个常数，用来调整对数变换的幅度；之所以要加 1，是为了避免对数变换中出现 0，导致无法计算。

#### Fourier 频谱的结论

回顾 Fourier 频谱图：

$$
|F(u,v)|=\sqrt{\operatorname{Re}^2F(u,v)+\operatorname{Im}^2F(u,v)}
$$

其中，$\operatorname{Re}F(u,v)$ 和 $\operatorname{Im}F(u,v)$ 分别是 $F(u,v)$ 的实部和虚部。

移中后，图像中心表示低频分量，图像四角表示高频分量。所以通常来说，Fourier 频谱图都是中心亮，四角暗的。

对于 Fourier 频谱图来说，显然，频谱图中心即为 $F(0,0)$，而图像四角则是 $F(M/2,N/2)$。$F(0,0)$ 表示图像的直流分量（也就是 Fourier 变换后的最低频率的那条直线），也就是图像的平均灰度。

#### Fourier 描述器

如果将一个坐标$(x,y)$看作是一个复数，然后对一系列点施加 Fourier 变换，那么就可以得到一个复数序列，这个序列就是 Fourier 描述器。

首先，我们将一个坐标$(x,y)$看作是一个复数：

$$
z=x+iy
$$

然后，对一系列点施加离散 Fourier 变换：

$$
\begin{aligned}
F(u)&=\frac{1}{K}\sum_{k=0}^{K-1}(x+iy)e^{-i 2 \pi uk / K}\\
\end{aligned}
$$

其中，$K$ 是点的个数。

重建原始序列的公式：

$$
\begin{aligned}
\hat{z}&=\frac{1}{K}\sum_{u=0}^{K-1}F(u)e^{i 2 \pi uk / K}\\
&=\frac{1}{K}\sum_{u=0}^{K-1}\left[\frac{1}{K}\sum_{k=0}^{K-1}(x+iy)e^{-i 2 \pi uk / K}\right]e^{i 2 \pi uk / K}\\
&=\frac{1}{K}\sum_{u=0}^{K-1}\sum_{k=0}^{K-1}(x+iy)e^{-i 2 \pi uk / K}e^{i 2 \pi uk / K}\\
&=\frac{1}{K}\sum_{u=0}^{K-1}\sum_{k=0}^{K-1}(x+iy)\\
&=\frac{1}{K}K(x+iy)\\
&=z
\end{aligned}
$$

> 应该说差不多是这个意思，不过这个推导可能有点不太严谨。仅供参考。

取不同的 $K$，可以得到不同的 Fourier 描述器。$K$ 越大，描述器越精确，但相应地，对噪声也越敏感，计算量也越大；$K$ 越小，描述器越不精确，但相应地，鲁棒性更强，计算量也越小。

> 然后幻灯片放了一些*形状相似度度量*之类的应用拓展的玩意，我认为不考，过。

#### 二维 Fourier 变换的性质

线性性上面说过了，直接回顾结论：

$$
\mathfrak{F}[af(x,y)+bg(x,y)]=a\mathfrak{F}[f(x,y)]+b\mathfrak{F}[g(x,y)]
$$

其中，$a$ 和 $b$ 是常数。

##### 分配率

令 $a=1$，$b=1$，导出分配率：

$$
\mathfrak{F}[f(x,y)+g(x,y)]=\mathfrak{F}[f(x,y)]+\mathfrak{F}[g(x,y)]
$$

注意，对时域乘法，频域的分配率不成立。即：

$$
\mathfrak{F}[f(x,y)\cdot g(x,y)]\neq\mathfrak{F}[f(x,y)]\cdot\mathfrak{F}[g(x,y)]
$$

这个上面讲卷积定理的时候也说了。说白了就是，时域的乘法在频域中是卷积，时域的卷积在频域中是乘法。

##### 平移性质

平移性质描述如下：

$$
\begin{aligned}
f(x,y)e^{i 2 \pi (u_0x/M+v_0y/N)}&\Longleftrightarrow F(u-u_0,v-v_0)\\
f(x-x_0,y-y_0)&\Longleftrightarrow F(u,v)e^{-i 2 \pi (ux_0/M+vy_0/N)}
\end{aligned}
$$

第一个表达式表示，如果我们对函数$f(x, y)$进行平移操作，即将其在空域中的原点$(x, y)$平移到$(x_0, y_0)$，那么在频域中，对应的傅里叶变换$F(u, v)$将发生相应的平移，即从$(u, v)$平移到$(u_0, v_0)$。

具体地，$e^{i 2 \pi (u_0x/M+v_0y/N)}$是一个复数指数项，它表示一个平面波，其频率由$(u_0, v_0)$确定。乘以$f(x, y)$相当于将这个平面波与$f(x, y)$进行逐点乘积。然后，对这个乘积函数进行傅里叶变换，得到的结果$F(u-u_0, v-v_0)$表示了原始函数$f(x, y)$在频域中的平移。

第二个表达式表示，如果我们对函数$f(x, y)$进行平移操作，即将其在空域中的原点$(x, y)$平移到$(x_0, y_0)$，那么在频域中，对应的傅里叶变换$F(u, v)$将发生相应的相位反转和平移，即乘以一个复数指数项$e^{-i 2 \pi (ux_0/M+vy_0/N)}$。

具体地，$e^{-i 2 \pi (ux_0/M+vy_0/N)}$是一个复数指数项，它表示一个相位反转和平移的操作。乘以$F(u, v)$相当于对$F(u, v)$的每个频率分量进行相位反转和平移操作。然后，对这个乘积函数进行逆傅里叶变换，得到的结果$f(x-x_0, y-y_0)$表示了原始函数$f(x, y)$在空域中的平移。

注意，平移性质当然不会影响频率振幅。

##### 缩放

缩放性质描述如下：

$$
\begin{aligned}
af(x,y)&\Longleftrightarrow\ aF(u,v)\\
f(ax,by)&\Longleftrightarrow\frac{1}{|ab|}F\left(\frac{u}{a},\frac{v}{b}\right)
\end{aligned}
$$

第一个式子表明：对$f(x,y)$的幅度的缩放，将导致$F(u,v)$的幅度的缩放。

第二个式子表明：对$f(x,y)$的空间尺度的缩放，将导致$F(u,v)$的空间尺度的相反方向的缩放。不仅如此，还有一个系数$1/|ab|$，它表示了频域空间幅度缩放的比例。也就是说，如果$a < 1$，$b < 1$，那么$F(u,v)$的空间尺度将放大，其幅度也将增大。

一个感性的认知是：当源图像被缩小时（那么看起来就像图像变得更加高频了，因为更多周期进入了图像里），其频域图像中的亮点就会距离拉大。

![Fourier_Transformation_Scaling_p1](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Fourier_Transformation_Scaling_p1.png)

![Fourier_Transformation_Scaling_p2](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Fourier_Transformation_Scaling_p2.png)

![Fourier_Transformation_Scaling_p3](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Fourier_Transformation_Scaling_p3.png)

> 其实，还能注意到，空域中波形传播的方向和频域中亮点排列的方向是一样的。

##### 旋转

引入极坐标：

$$
\begin{aligned}
x&=r\cos\theta\\
y&=r\sin\theta\\
u&=\omega\cos\phi\\
v&=\omega\sin\phi\\
\end{aligned}
$$

> 然而课件幻灯片中写的是$v=\omega\cos\phi$，我怀疑是幻灯片打错了？（

这样，约定的记号就从 $f(x, y)$ 和 $F(u, v)$ 变成了 $f(r, \theta)$ 和 $F(\omega, \phi)$。

带入 Fourier 变换，得到：

$$
f(r,\theta+\theta_0)\Longleftrightarrow F(\omega,\phi+\theta_0)
$$

即，对 $f(r, \theta)$ 旋转 $\theta_0$，将导致 $F(\omega, \phi)$ 同样旋转 $\theta_0$。

证明不会。但是这个结论和上面说的“方向性”是一致的。

> “其实，还能注意到，空域中波形传播的方向和频域中亮点排列的方向是一样的。”

或者说，正是因为方向总是一致，所以空域旋转多少角度，频域就要旋转多少角度。

##### 周期性和共轭对称性

对实函数 $f(x, y)$，其傅里叶变换结果 $F(u, v)$ 具有共轭对称性：

$$
F(u,v)=F^*(-u,-v)
$$

其中，$F^*$ 表示 $F$ 的共轭复数（我们简称为复共轭）。

当然了，写成这样也是可以的：

$$
|F(u,v)|=|F(-u,-v)|
$$

共轭嘛，模长当然是一样的。

本质上，这个性质来自于 Fourier 变换的周期性。三角函数可不就有周期性吗？

然后，我们以一维 Fourier 变换为例，来说明这个性质。$F(u)$ 本身就有周期 $N$，所以$F(u)=F(u+N)$，或者说 $|F(u)|=|F(-u)|$。第二个式子成立是因为 $F(u)$ 的对称中心是原点。

拓展到二维也是类似的，只不过是两个方向而已。虽然听起来挺玄的，但总之就是周期性罢了。

##### 可分离性

DFT 可以表示为可分离的形式：

$$
F(u,v)=\frac{1}{M}\sum_{x=0}^{M-1}F(x,v)e^{-i 2 \pi ux/M}\\
F(x,v)=\frac{1}{N}\sum_{y=0}^{N-1}f(x,y)e^{-i 2 \pi vy/N}
$$

换句话说，一个二维 DFT 可以分解为：先对每一行进行一维 DFT，然后对每一列进行一维 DFT。

另外，公式表明，对于任意值 $x$，$F(x, v)$ 都是一维 DFT 的结果。因此，我们可以将二维 DFT 看作是一维 DFT 的一系列结果。

##### 卷积定理

上面提过了，反正证明很难，不是我们该会的，直接上结论：

$$
\begin{aligned}
f(x)\ast g(x)&\Longleftrightarrow F(u)G(u)\\
f(x)g(x)&\Longleftrightarrow F(u)\ast G(u)
\end{aligned}
$$

即：

- 空域上的卷积等价于频域上的乘积
- 空域上的乘积等价于频域上的卷积

离散卷积形式如下：

$$
f_{e}(x)\ast g_{e}(x)=\frac{1}{M}\sum_{m=0}^{M-1}f_{e}(m)g_{e}(x-m)
$$

> 对我来说啊，离散卷积反而是我学卷积最开始接触的形式。其实也挺好理解的……
>
> 参考视频：【官方双语】那么……什么是卷积？ - https://www.bilibili.com/video/BV1Vd4y1e7pj

#### FFT

> 噔 噔 咚

FFT（Fast Fourier Transform）是一种快速计算 DFT 的算法，它的计算复杂度为 $O(N\log N)$，而朴素的 DFT 的计算复杂度为 $O(N^2)$。

我们先说说朴素的 DFT 是怎么算的。公式回顾：

1D 连续 Fourier 变换：

$$
F(u)=\int_{-\infty}^{\infty} f(x) e^{-i 2 \pi u x} d x
$$

1D 离散 Fourier 变换：

$$
F(u)=\frac{1}{M} \sum_{x=0}^{M-1} f(x) e^{-i 2 \pi u x / M}
$$

外面先来一层 $M$ 趟的累加，里面呢，我们会用 Euler 公式将 $e$ 的指数拆开，如下：

$$
f(x)e^{-i 2 \pi u x / M}=f(x)\left[\cos(2\pi u x/M)-i\sin(2\pi u x/M)\right]
$$

所以里面算的是 $f(x)$ 和一个复数相乘。注意 $f(x)$ 是要遍历一遍 $u$ 的，也就是说每个频率分量都要算一次复数乘法。所以内层也是 $M$ 趟。

综上所述，朴素的 DFT 的计算复杂度为 $O(N^2)$。

> 如果你不熟悉时间复杂度的表示而对 $M$、$N$ 感到困惑，那我简单说明一下，分析中用的 $M$ 确实导出了 $O(M^2)$，然后呢，习惯上我们用 $N$ 表示输入数据的规模，所以我们说它是 $O(N^2)$ 的。
> 
> 主要是因为，在时间复杂度分析中，我们更关心的是关于**输入数据的规模**的分析，而不是输入数据用什么字母表示，除非真的有多个量同时对算法时间有贡献，那么那种情况下我们会区分开来。

然后，我们来看看 FFT 是怎么算的。课程中介绍的是最常见的 FFT 算法，即 Cooley-Tukey 算法。（事实上，还有其他做到 FFT 算法，不讲；下面统一将 Cooley-Tukey 算法称为 FFT）

FFT 算法基于**逐次加倍**方法。通过推导将原始 Fourier 变换转换成两个递推公式：

$$
\begin{aligned}
F(u)&=\frac{1}{M}\sum_{x=0}^{M-1}f(x)e^{-i 2 \pi u x / M}\\
F(u)&=\frac{1}{2}\left[F_{\text{even}}(u)+F_{\text{odd}}(u)W^{u}_{2k} \right]\\
F(u+k)&=\frac{1}{2}\left[F_{\text{even}}(u)-F_{\text{odd}}(u)W^{u}_{2k} \right]
\end{aligned}
$$

我们先规定，$M=2K$。然后，$W^{u}_{2k}$是一个复数旋转因子，很学术是不是？其实它的定义是$e^{-i 2\pi u / M}$，就是 Fourier 变换的基函数。

可以这样看：$W^{A}_{B}=e^{-i 2\pi A / B}$，就是说 $A$ 是旋转因子的指数，$B$ 是旋转因子的周期。我们称其为“$B$次单位根”。

在开始之前，我们应该介绍一下单位根的性质：

1. 周期性。那当然了，怎么想都是。$W^{k+N}_{N}=W^{k}_{N}$。
2. 对称性。$W^{k+N/2}_{N}=-W^{k}_{N}$。
3. 有 $W^{mkn}_{N}=W^{kn}_{N/m}$，如果 $m$ 是 $N$ 的因子的话。

现在，为了运用这个递推公式，我们首先要定义 $F_{\text{even}}(u)$ 和 $F_{\text{odd}}(u)$：

$$
\begin{aligned}
F_{\text{even}}(u)&=\frac{1}{K}\sum_{x=0}^{K-1}f(2x)W^{ux}_{K}\\
F_{\text{odd}}(u)&=\frac{1}{K}\sum_{x=0}^{K-1}f(2x+1)W^{ux}_{K}
\end{aligned}
$$

很显然这里的 $u$ 是 $0,1,2,\cdots,K-1$。这里的 $F_{\text{even}}(u)$ 和 $F_{\text{odd}}(u)$ 分别是 $f(x)$ 中偶数项和奇数项的 DFT。

$$
F(u)=\frac{1}{2}\left[F_{\text{even}}(u)+F_{\text{odd}}(u)W^{u}_{2k} \right]
$$

这就是说，前 $k$ 个 $F(u)$ 可以通过奇部和偶部之和得到。

$$
F(u+k)=\frac{1}{2}\left[F_{\text{even}}(u)-F_{\text{odd}}(u)W^{u}_{2k} \right]
$$

这就是说，后 $k$ 个 $F(u+k)$ 可以通过奇部和偶部之差得到。

Cooley-Tukey 算法的特性：

- 一个 $M$ 点的 DFT 可以分解为两个 $M/2$ 点的 DFT
- 通过计算两个 $M/2$ 点的 DFT，得到$F_{\text{even}}(u)$ 和 $F_{\text{odd}}(u)$
- 奇部和偶部之和得到前 $k$ 个 $F(u)$
- 奇部和偶部之差得到后 $k$ 个 $F(u+k)$

所以总的来说，其思想是：

- 计算两个单点的 DFT，然后合并成一个双点的 DFT
- 计算两个双点的 DFT，然后合并成一个四点的 DFT
- 依此类推，在 $n\log{n}$ 内做完 FFT

> 上面是引述幻灯片里的 Cooley-Tukey 算法的讲解。我尽力用最简单的方式表述了，但是还是嫌晦涩难懂，评价为教的什么玩意。如果想要更好地理解，可以参考 Wikipedia 的讲解。
>
> 参考阅读：https://zh.wikipedia.org/wiki/%E5%BF%AB%E9%80%9F%E5%82%85%E9%87%8C%E5%8F%B6%E5%8F%98%E6%8D%A2#%E8%AE%BE%E8%AE%A1%E6%80%9D%E6%83%B3

### 频域滤波

之所以我们能在频域滤波，那多亏了卷积定理。

在空域（或者说时域）中，滤波器本质上是在做卷积。根据卷积定理，我们知道，在频域上做乘积等价于在空域上做卷积。所以，我们可以在频域上做乘积，然后再做逆变换，就得到了在空域上做卷积的结果。

滤波这件事，我们可以表述为：

$$
g(x,y)=f(x,y)\ast h(x,y)
$$

其中，$f(x,y)$ 是输入图像，$h(x,y)$ 是滤波器（也就是说，一个线性不变算子），$g(x,y)$ 是输出图像。

根据卷积定理，我们有：

$$
G(u,v)=F(u,v)H(u,v)
$$

这几个都是通过 Fourier 变换到频域中的，不细说了。

所以总的来说，做频域滤波的流程就是：

1. **转换到频域**：对输入图像 $f(x,y)$ 做 Fourier 变换，得到 $F(u,v)$
2. **转换到频域**：对滤波器 $h(x,y)$ 做 Fourier 变换，得到 $H(u,v)$
3. **在频域应用转移函数**：对 $F(u,v)$ 和 $H(u,v)$ 做乘积，得到 $G(u,v)$
4. **转换回空域**：对 $G(u,v)$ 做 Fourier 逆变换，得到 $g(x,y)$

我们可以拿一维频域的 Gaussian 滤波器来举例子。

$$
h(x)=\sqrt{2\pi}\sigma Ae^{-2\pi^2\sigma^2x^2}
$$

对它施加 Fourier 变换，得到：

~~别问我怎么得的，没推出来~~

$$
H(u)=Ae^{-u^2/2\sigma^2}
$$

而高通滤波器的 Fourier 变换为：

$$
h(x)=\sqrt{2\pi}\sigma_{1}Ae^{-2\pi^2\sigma_{1}^2x^2}-\sqrt{2\pi}\sigma_{2}Be^{-2\pi^2\sigma_{2}^2x^2}\\
H(u)=Ae^{-u^2/2\sigma_{1}^2}-Be^{-u^2/2\sigma_{2}^2}
$$

其中，$\sigma_1$ 和 $\sigma_2$ 分别是两个 Gaussian 函数的标准差，并且$\sigma_1\ge\sigma_2,A\ge B$。

> 值得注意的是，本章讲解了：
> 
> 1. 陷波滤波器（x）
> 2. 低通滤波器
> 3. 高通滤波器
> 4. 带通滤波器（x）
> 5. 同态滤波器（x）
>
> 其中，带有“（x）”记号的滤波器，不存在于复习幻灯片中。不讲。

#### 低通滤波器

简而言之，留低频（低频通过，称为低通），滤高频。效果是使图像变得模糊（平滑）。

##### ILPF

理想低通滤波器（Ideal Low Pass Filter）的转移函数为：

$$
H(u,v)=\begin{cases}
1&\text{if}\ D(u,v)\le D_0\\
0&\text{if}\ D(u,v)>D_0
\end{cases}
$$

这就是说，以截频 $D_{0}$ 为半径的圆内的所有频率都能无损通过，而以截频 $D_{0}$ 为半径的圆外的所有频率都被完全阻断。

![Results_of_ILPF](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Results_of_ILPF.png)

##### 振铃效应

这就自然引出了振铃效应。振铃效应是指，当我们使用 ILPF 时，图像的边缘会出现一系列明暗相间的环带。

原因：

输入一个尖峰信号，相当于一个快速的输入变化，则输出会有明显的振荡。因为 Fourier 变换的频谱是周期性的，所以这种振荡会在频谱中反映出来。

环的半径反比于截止频率 $D_{0}$，也就是说：

- $D_{0}$ 较小，$h(x,y)$ 产生数量少但是较宽的环
- $D_{0}$ 较大，$h(x,y)$ 产生数量多但是较窄的环

##### Butterworth 低通滤波器

Butterworth 低通滤波器的转移函数为：

$$
H(u,v)=\frac{1}{1+\left[\frac{D(u,v)}{D_0}\right]^{2n}}
$$

其中，$n$ 是滤波器的阶数，$D_0$ 是截止频率。

Butterworth 低通滤波器又称为最大平坦滤波器。和 ILPF 不同，它的通带和阻带之间没有明显的不连续性。也就是说，在通带和阻带之间有一个平滑的过渡带。

通常把 $H(u,v)$ 下降到某一值的那点定位为截止频率 $D_0$。譬如说罢，当 $D(u,v)=D_0$ 时，$H(u,v)$ 下降到 $1/2$，那么我们就说 $D_0$ 是 $H(u,v)$ 的截止频率。

![Essence_of_Butterworth_LPF](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Essence_of_Butterworth_LPF.png)

同 ILPF 的结果相比，Butterworth 低通滤波器的结果更加平滑（**因此模糊程度减少**），没有振铃效应；相应的，因为 $H(u,v)$ 的下降速度更慢，它的尾部有不少高频成分。

##### Gaussian 低通滤波器

Gaussian 低通滤波器的转移函数为：

$$
H(u,v)=e^{-D^2(u,v)/2D_0^2}
$$

其中，$D_0$ 是截止频率。

![Essence_of_Gaussian_LPF](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Essence_of_Gaussian_LPF.png)

#### 高通滤波器

简而言之，留高频（高频通过，称为高通），滤低频。效果是使图像变得锐化。

$$
H_{\text{HP}}(u,v)=1-H_{\text{LP}}(u,v)
$$

##### IHPF

$$
H(u,v)=\begin{cases}
0&\text{if}\ D(u,v)\le D_0\\
1&\text{if}\ D(u,v)>D_0
\end{cases}
$$

即，仅保留远离中心的值。

当然了，IHPF 因为生生截断，所以也会产生振铃效应。

##### Butterworth 高通滤波器

引入**高频强调**：在高通滤波器中添加一个常数以保留低频分量。

$$
H(u,v)=\frac{1}{1+\left[\frac{D_0}{D(u,v)}\right]^{2n}}
$$

同样的，$H_{\text{BHPF}}=1-H_{\text{BLPF}}$。

该滤波器没有明显的不连续性，在通过频率和滤波频率之间建立了清晰的截止点。这一特性和 BLPF 是一致的。

定义截止频率：在 $H(u,v)$ 降至其最大值的特定数值处，约定为截止频率。

![BHPF_and_BLPF](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/BHPF_and_BLPF.png)

##### Gaussian 高通滤波器

$$
H(u,v)=1-e^{-D^2(u,v)/2\sigma^2}
$$

其中，$\sigma$ 是控制截止频率的参数，即截止频率。

同样的，$H_{\text{GHPF}}=1-H_{\text{GLPF}}$。

#### 同态滤波器

貌似复习幻灯片里没怎么提，简单说点。

同态滤波是一种在频域中同时将图像亮度范围进行压缩和将图像对比度进行增强的方法，即把频率过滤和灰度变换结合起来。

![Homomorphic_Filter](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Homomorphic_Filter.png)

## Chapter 5: Restoration

典型的图像恢复是根据图像退化的先验知识建立一个退化模型，以此模型为基础，采用各种逆退化处理方法进行恢复，得到质量改善的图像。

过程：找退化原因 —— 建立退化模型 —— 反向推演 —— 恢复图像

![Basic_Ideas_of_Image_Recovery](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Basic_Ideas_of_Image_Recovery.png)

**图像恢复**和**图像增强**

相同之处：改进输入图像的视觉质量

不同之处：

- 图像增强借助人的视觉系统特性，以取得较好的视觉结果（不考虑退化原因）
- 图像恢复根据相应的退化模型和知识重建恢复原始的图像（考虑退化原因）

图像恢复的问题描述 (Problem Formulation)：

物体的理想图像应为 $f(x,y)$ ，但由于成像系统、采集系统不理想或成像环境不理想等原因，实际得到的是退化（变质）图像 $g(x,y)$。

图像恢复则是根据退化图像 $g(x,y)$ 和退化模型 $H(x,y)$，重建出理想图像 $\hat{f}(x,y)$。

### 数学描述

空域退化模型：

$$
g(x,y)=f(x,y)\ast h(x,y)+\eta(x,y)
$$

频域退化模型：

$$
G(u,v)=F(u,v)H(u,v)+N(u,v)
$$

### 只有噪声的空域恢复

**均值滤波**

算数均值（虽能降噪，但也模糊了图像）：

$$
\hat{f}(x,y)=\frac{1}{mn}\sum_{s=-a}^{a}\sum_{t=-b}^{b}g(x+s,y+t)
$$

几何均值（和算数均值差不多平滑，但丢失更少细节）：

$$
\hat{f}(x,y)=\left[\prod_{s=-a}^{a}\prod_{t=-b}^{b}g(x+s,y+t)\right]^{\frac{1}{mn}}
$$

谐波均值（对盐噪声效果更好，但不适用于胡椒噪声，并且善于处理 Gaussian 噪声等其他噪声）：

$$
\hat{f}(x,y)=\frac{mn}{\sum_{s=-a}^{a}\sum_{t=-b}^{b}\frac{1}{g(x+s,y+t)}}
$$

逆谐波均值（适用于椒盐噪声，但不能同时消除）：

$$
\hat{f}(x,y)=\frac{\sum_{s=-a}^{a}\sum_{t=-b}^{b}g(x+s,y+t)^{Q+1}}{\sum_{s=-a}^{a}\sum_{t=-b}^{b}g(x+s,y+t)^{Q}}
$$

其中，$Q$ 能改变滤波器的性质。当 $Q>0$ 时，滤波器对胡椒噪声更敏感；当 $Q<0$ 时，滤波器对盐噪声更敏感。当 $Q=0$ 时，退化为算数均值滤波器；当 $Q=-1$ 时，退化为谐波均值滤波器。

统计排序（中值滤波）：

$$
\hat{f}(x,y)=\text{median}\left\{g(x+s,y+t)\right\}
$$

其中，$s=-a,\cdots,a$，$t=-b,\cdots,b$。中值滤波对多种随机噪声有良好的去噪能力，引起的模糊更少，尤其对脉冲噪声非常有效。

最大（小）值滤波：

$$
\hat{f}(x,y)=\max\left\{g(x+s,y+t)\right\}\\
\hat{f}(x,y)=\min\left\{g(x+s,y+t)\right\}
$$

最大值滤波可发现图像中的亮点，消除胡椒噪声；最小值滤波可发现图像中的暗点，消除盐噪声。

中点滤波器：

$$
\hat{f}(x,y)=\frac{1}{2}\left[\max\left\{g(x+s,y+t)\right\}+\min\left\{g(x+s,y+t)\right\}\right]
$$

中点滤波器结合了统计排序和求平均操作，使得其对于高斯和均匀随机分布的噪声有较好的效果。

Alpha-Trimmed 均值滤波器：

$$
\hat{f}(x,y)=\frac{1}{mn-d}\sum_{s=-a}^{a}\sum_{t=-b}^{b}g_{r}(x+s,y+t)
$$

其中，$d$ 可取 $0,1,\cdots,mn-1$，$r=(d-1)/2$。当 $d=0$ 时，退化为算数均值滤波器；当 $d=mn-1$ 时，退化为中值滤波器。取其他值时，适合包含多种噪声的图像处理，如高斯和椒盐噪声的混合情况。

### 自适应中值滤波

中值滤波器的尺寸如果太小，可以较好地保护图像的某些细节，但往往遗漏噪声；多次滤波造成特征丧失、图像模糊。反之，如果太大的话，可以加强噪声抑制能力，但图像会很模糊，有时候会滤去图像中的一些细线、尖锐边角等重要细节，从而破坏图像的几何结构。

因此，先采用小尺寸的中值滤波；当发现滤波器不够好时，扩大其尺寸，再次滤波；再不好；再扩大，直到达到最大尺寸。此谓之“自适应”。

AMF 的三个主要目的：

1. 去除椒盐(脉冲)噪声
2. 平滑其他非脉冲噪声
3. 减少诸如物体边界细化或粗化等失真

![Algorithm_Description_of_AMF](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Algorithm_Description_of_AMF.png)

### 逆向滤波

如果噪声可忽略，并且退化函数已知或已经获得，那么我们可以直接将退化函数除掉：

$$
\hat{F}(u,v)=\frac{G(u,v)}{H(u,v)}
$$

我们把 $\frac{1}{H(u,v)}$ 称为**逆滤波器**。

逆滤波复原过程：

1. 对退化图像 $g(x,y)$ 做 Fourier 变换，得到 $G(u,v)$
2. 计算系统点扩散函数 $h(x,y)$ 的 Fourier 变换，得到 $H(u,v)$
3. 计算逆滤波 $F(u,v)=G(u,v)/H(u,v)$
4. 对 $F(u,v)$ 做 Fourier 逆变换，得到 $\hat{f}(x,y)$

由于 $G(u,v)=H(u,v)F(u,v)+N(u,v)$，所以：$\hat{F}(u,v)=F(u,v)+\frac{N(u,v)}{H(u,v)}$。因此，当 $H(u,v)$ 的值很小的时候，噪声将占据主导地位。

倘若不存在噪声，那么逆滤波就是一个完美的恢复过程。但是，由于噪声的存在，逆滤波很可能会放大噪声，导致结果很差。除非我们知道噪声的分布情况（事实上，这也很难知道），逆滤波几乎不可用。

解决策略：忽略掉 $H(u,v)$ 中的小值，或者用一个截止频率 $D_0$ 来限制 $H(u,v)$ 的值。

$$
M(u,v)=\begin{cases}
1/H(u,v)&\text{if}\ u^{2}+v^{2}\le w^{2}\\
1&\text{if}\ u^{2}+v^{2}> w^{2}
\end{cases}
$$

## Chapter 6: Morphological

膨胀（Dilation）是数学形态学中的一种基本操作，用于对图像或集合进行形态学变换。在二值图像中，膨胀可以扩展或增大图像中的对象，使其更加粗壮或更接近原始形状。

$$
A\oplus B=\left\{z\mid (\hat{B})_{z}\cap A \subseteq A\right\}
$$

B 的反射进行平移与A的交集是 A 的子集。在给定的定义中，$(\hat{B})_z$ 表示结构元素 $B$ 的反射（reflection）进行平移操作。这个操作将结构元素 $B$ 沿着水平和垂直方向翻转，并将其在空间中进行平移，使得结构元素的原点（通常是结构元素的中心）与元素 $z$ 对齐。

平移操作是指将一个对象在平面上移动一定的距离，保持其形状和方向不变。在这种情况下，结构元素 $B$ 的反射进行平移是指对结构元素 $B$ 进行翻转并移动，使得结构元素的原点与元素 $z$ 对齐。

然后，$(\hat{B})_z \cap A$ 表示结构元素 $B$ 的反射进行平移后与原始集合 $A$ 的交集。这个交集表示了在结构元素 $B$ 的形状和位置经过反射平移后，与原始集合 $A$ 重叠的部分。

最后，定义中指出如果 $(\hat{B})_z \cap A$ 是 $A$ 的子集，即所有在结构元素 $B$ 的反射平移后与 $A$ 重叠的部分都包含在 $A$ 中，那么元素 $z$ 就被包含在膨胀操作的结果中。

这个定义的意思是，膨胀操作将结构元素 $B$ 的反射平移到原始集合 $A$ 上，然后检查反射平移后的结构元素与 $A$ 的交集。如果这个交集的所有元素都包含在 $A$ 中，那么元素 $z$ 就被包含在膨胀操作的结果中。换句话说，膨胀操作通过将结构元素 $B$ 的形状扩展到原始集合 $A$ 上来增大集合的大小。

------

腐蚀（Erosion）：$A\ominus B=\left\{z\mid (B)_{z}\subseteq A \right\}$

腐蚀（Erosion）是数学形态学中的另一种基本操作，用于对图像或集合进行形态学变换。腐蚀操作可以使图像中的对象变得更加细小或更接近原始形状。

在给定的定义中，$A$ 和 $B$ 是两个集合，其中 $A$ 是原始集合，$B$ 是结构元素（也称为核）。腐蚀操作 $A\ominus B$ 的结果是一个新的集合，其中包含满足以下条件的所有元素 $z$：

1. 将结构元素 $B$ 的原点（通常是结构元素的中心）与元素 $z$ 对齐。
2. 检查结构元素 $B$ 的所有非零元素是否都包含在原始集合 $A$ 中。
3. 如果结构元素 $B$ 的所有非零元素都包含在原始集合 $A$ 中，则元素 $z$ 被包含在腐蚀操作的结果中。

换句话说，腐蚀操作将结构元素 $B$ 在原始集合 $A$ 上滑动，并检查每个位置上的包含关系。如果结构元素 $B$ 的所有非零元素都被包含在原始集合 $A$ 中，那么该位置就被包含在腐蚀操作的结果中。

腐蚀操作可以用于图像处理中的多种应用，例如去除图像中的小噪点、分离相连的对象或缩小对象的边界。它也可以与其他形态学操作（如膨胀、开运算和闭运算）结合使用，以实现更复杂的图像处理任务。

------

开运算（Opening）：$A\circ B=(A\ominus B)\oplus B$

在不改变形状的前提下，使图像的轮廓变得光滑，能够断开狭窄的间断，消除细的突出物。

开操作的性质：

- 平移。$O(A+x, B)=O(A, B)+x$
- 反广延性（Antiextensivity）。$O(A, B)\subseteq A$
- 增加单调性（Increasing Monotonicity）。$A_{1}\subseteq A_{2}\Rightarrow (A_{1}\circ B)\subseteq (A_{2}\circ B)$
- 幂等性（Idempotence）。$A\circ B\circ B=A\circ B$

闭运算（Closing）：$A\bullet B=(A\oplus B)\ominus B$

在不明显改变面积前提下，使图像的轮廓变得光滑。可以弥合狭窄的间断和细长的鸿沟，消除小的孔洞，填补轮廓线中的裂痕。

闭运算的性质：

- 平移。$C(A+x, B)=C(A, B)+x$
- 广延性（Extensivity）。$A\subseteq C(A, B)$
- 增加单调性（Increasing Monotonicity）。$A_{1}\subseteq A_{2}\Rightarrow (A_{1}\bullet B)\subseteq (A_{2}\bullet B)$
- 幂等性（Idempotence）。$A\bullet B\bullet B=A\bullet B$

### 边界提取

边界提取（Boundary Extraction）：$\beta(A)=A-(A\ominus B)$

把腐蚀后的图像从原图像中减去，得到的就是边界叻。

### 区域填充

区域填充（Region Filling）：$X_{k}=X_{k-1}\oplus B\cap A^{c}$

![Region_Filling](https://cloud.icooper.cc/apps/sharingpath/PicSvr/PicMain/Region_Filling.png)

### 提取连通分量

令 $Y$ 表示一个包含于集合 $A$ 中的连通分量，并假设 $Y$ 中的一个点 $p$ 是已知的。用下列迭代式生成 $Y$ 的所有元素：

$$
X_{k}=X_{k-1}\oplus B\cap A
$$

其中，$X_0=p$，$k=1,2,\cdots$。当 $X_k=X_{k-1}$ 时，迭代停止，$X_k$ 就是 $Y$ 的所有元素。

### 拓扑描述符

我们先定义该区域的孔数 $H$，然后定义连通分量的数目 $C$，最后定义欧拉数 $E=C-H$。

## Chapter 7: Segmentation

考个锤子。