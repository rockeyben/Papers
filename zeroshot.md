# Zero Shot文献整理

## 2013

- Label-Embedding for Attribute-Based Classification (CVPR).

 Zeynep Akata, Florent Perronnin, Zaid Harchaoui, Cordelia Schmid.

在attribute空间里做label embedding (ALE)。有些high level的语义类别可能含有共通的attribute，例如“动物”这个类别可能都含有“爪子、毛发、条纹”等attribute。

如果我们把分类任务表示成 X->Y, 之前的很多任务都是在X domain, 也就是图片domain找相似的attribute。本篇工作试图在Y domain，也就是label的属性层面寻找语义相通之处。假设把label(Y) embed到attribute空间，我们要做的就是找出image feature与attribute feature之间的映射关系。

假设image feature embedding映射在$\theta: \mathbb{R}^{D}$空间，label feature embedding映射在$\phi: \mathbb{R}^{E}$空间。那我们实际上需要寻找的映射函数其实就是$\mathbb{R}^{D} \rightarrow \mathbb{R}^{E}$的映射，用一个$D \times E$的矩阵$W$来表示。匹配函数$F$就可以表示成$F(x,y;W)=\theta(x)'W\phi(y)$。回归形式可以表示成$F=-||\theta(x)'W-\phi(y)||^2$。在维数很高的情况下可以考虑对$W$做低秩分解$W=U'V$，函数就表示成$F=(U\theta(x)') (V\phi(y))$。

ALE: 假设要把label(Y) $\mathbb{R}^{C}$映射到attribute(A) $\mathbb{R}^{E}$。$\rho_{y,i}$表示属性$a_i$与类别$y$之间的关系，那么embedding矩阵表示为$\phi(y)^{A}=[\rho_{y,1}, ..., \rho_{y,E}]$，是一个$E \times C$矩阵。

训练方法是Ridge Regression或SSVM。需要学习的参数是匹配矩阵$W \in \mathbb{R}^{D\times E}$与label embedding矩阵$\Phi \in \mathbb{R}^{C \times E}$。选用的视觉特征是SIFT。实际上在zero shot的场景下，作者是先训完$\Phi$(这个是比较好学的)，然后再固定$\Phi$，学习$W$。


![image](figures/ALE_1.png)

## 2015

- Zero-Shot Learning via Semantic Similarity Embedding (ICCV)

Ziming Zhang, Venkatesh Saligrame

有一种形式的zero-shot learning被称为`source-target`。

source: (attributes, labels)

target: (images, labels)

简化来说就是，source domain提供了attribute -> label的映射，target domain提供了image -> label的映射。其中，attribute-label的映射是稳定的、先验的。为了在source与target domain中架起桥梁，我们需要引入一个`第三方空间`，这个第三方空间就是基于已知类的直方图表示。

举个例子，假设car是一个未知类，在source domain中，我们可能可以根据他的一些属性（例如“有轮胎”，“运输工具”）等等，发现它可以由truck和boat两个类表出；在target domain中，我们可能也可以根据视觉feature判断出它可以由truck、boat这两个类表出，最后拿两个domain各自生成的直方图进行比较，找出最相似的那个类，我们就可以得到未知类的类别了。所以我们的目标是给出source domain的属性向量$c$以及target domain的图片$x$，我们能够把他们表示为一个由所有已知类构成的直方图$\Delta$。

![image](figures/SSE_1.png)



论文里的符号系统比较复杂，比较重要的一点是：$z$和$\pi$。$\pi$代表target domain(image)那一边的直方图，$z$代表source domain(attribute)那一边的直方图，两者说的是同一个东西。可以通过式子(6)看到，作者对样本x是否属于类别y的评分score的定义：$f(x,y)$，这个也是由source与target两个domain共同决定的。其中每一项的内积的部分代表在视觉feature层面，样本x属于已知类$s$的程度。再乘上一个权重$z_{y,s}$，代表在source domain，也就是attribute层面，未知类别$y$可由已知类$s$表达的程度。需要注意的是，$z_{y,s}$是在test的时候才给出来的。

![image](figures/SSE_2.png)

这里有一个假设，就是同一个class基于source与target domain得到的直方图应该是类似的，不能说根据属性，我得到的是“DOG=CAT+HORSE”，而根据视觉特征我得到的是“DOG=WOLF+LION”，这样这个方法就会不work了。这个假设会用一个约束来实现，也就是式(2)。根据之前的解释我们关注这两个符号$z$和$\pi$，分别代表source与target domain里对某个未知类别$y$的直方图表述，这个式子很明显就是强迫相同label在两个domain里的表述应该是最一致的（平均来看）。这个约束会在最后的优化目标函数里有所体现，也就是式(8)。

![image](figures/SSE_4.png)

![image](figures/SSE_3.png)