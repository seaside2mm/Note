---
title: Dahua Lin的数学体系
date: 2020-05-05 18:28:21
categories: 文集
toc: true

---

摘录Dahua Lin的一系列涉及数学的博客。

https://dahuasky.wordpress.com/page/

# 三大分支

在过去的一年中，我一直在数学的海洋中游荡，research进展不多，对于数学世界的阅历算是有了一些长进。

## 为什么要深入数学的世界

作为计算机的学生，我没有任何企图要成为一个数学家。我学习数学的目的，是要想爬上巨人的肩膀，希望站在更高的高度，能把我自己研究的东西看得更深广一些。说起来，我在刚来这个学校的时候，并没有预料到我将会有一个深入数学的旅程。我的导师最初希望我去做的题目，是对appearance和motion建立一个unified的model。这个题目在当今Computer Vision中百花齐放的世界中并没有任何特别的地方。事实上，使用各种Graphical Model把各种东西联合在一起framework，在近年的论文中并不少见。

我不否认现在广泛流行的Graphical Model是对复杂现象建模的有力工具，但是，我认为它不是panacea，并不能取代对于所研究的问题的深入的钻研。如果统计学习包治百病，那么很多“下游”的学科也就没有存在的必要了。事实上，开始的时候，我也是和Vision中很多人一样，想着去做一个Graphical Model——我的导师指出，这样的做法只是重复一些标准的流程，并没有很大的价值。经过很长时间的反复，另外一个路径慢慢被确立下来——我们相信，一个图像是通过大量“原子”的某种空间分布构成的，原子群的运动形成了动态的可视过程。微观意义下的单个原子运动，和宏观意义下的整体分布的变换存在着深刻的联系——这需要我们去发掘。

在深入探索这个题目的过程中，遇到了很多很多的问题，如何描述一个一般的运动过程，如何建立一个稳定并且广泛适用的原子表达，如何刻画微观运动和宏观分布变换的联系，还有很多。在这个过程中，我发现了两个事情：

- 我原有的数学基础已经远远不能适应我对这些问题的深入研究。
- 在数学中，有很多思想和工具，是非常适合解决这些问题的，只是没有被很多的应用科学的研究者重视。

于是，我决心开始深入数学这个浩瀚大海，希望在我再次走出来的时候，我已经有了更强大的武器去面对这些问题的挑战。

我的游历并没有结束，我的视野相比于这个博大精深的世界的依旧显得非常狭窄。在这里，我只是说说，在我的眼中，数学如何一步步从初级向高级发展，更高级别的数学对于具体应用究竟有何好处。

## 集合论：现代数学的共同基础

现代数学有数不清的分支，但是，它们都有一个共同的基础——集合论——因为它，数学这个庞大的家族有个共同的语言。集合论中有一些最基本的概念：集合(set)，关系(relation)，函数(function)，等价(equivalence)，是在其它数学分支的语言中几乎必然存在的。对于这些简单概念的理解，是进一步学些别的数学的基础。我相信，理工科大学生对于这些都不会陌生。

不过，有一个很重要的东西就不见得那么家喻户晓了——那就是“选择公理”(Axiom of Choice)。这个公理的意思是“任意的一群非空集合，一定可以从每个集合中各拿出一个元素。”——似乎是显然得不能再显然的命题。不过，这个貌似平常的公理却能演绎出一些比较奇怪的结论，比如巴拿赫-塔斯基分球定理——“一个球，能分成五个部分，对它们进行一系列刚性变换（平移旋转）后，能组合成两个**一样大小**的球”。正因为这些完全有悖常识的结论，导致数学界曾经在相当长时间里对于是否接受它有着激烈争论。现在，主流数学家对于它应该是基本接受的，因为很多数学分支的重要定理都依赖于它。在我们后面要回说到的学科里面，下面的定理依赖于选择公理：

1. 拓扑学：Baire Category Theorem
2. 实分析（测度理论）：Lebesgue 不可测集的存在性
3. 泛函分析四个主要定理：Hahn-Banach Extension Theorem, Banach-Steinhaus Theorem (Uniform boundedness principle), Open Mapping Theorem, Closed Graph Theorem

**在集合论的基础上，现代数学有两大家族：分析(Analysis)和代数(Algebra)。**至于其它的，比如几何和概率论，在古典数学时代，它们是和代数并列的，但是它们的现代版本则基本是建立在分析或者代数的基础上，因此从现代意义说，它们和分析与代数并不是平行的关系。

## 分析：在极限基础上建立的宏伟大厦

### 微积分：分析的古典时代——从牛顿到柯西

先说说分析(Analysis)吧，它是从微积分(Caculus)发展起来的——这也是有些微积分教材名字叫“数学分析”的原因。不过，分析的范畴远不只是这些，我们在大学一年级学习的微积分只能算是对古典分析的入门。分析研究的对象很多，包括导数(derivatives)，积分(integral)，微分方程(differential equation)，还有级数(infinite series)——这些基本的概念，在初等的微积分里面都有介绍。如果说有一个思想贯穿其中，那就是极限——这是整个分析（不仅仅是微积分）的灵魂。

一个很多人都听说过的故事，就是牛顿(Newton)和莱布尼茨(Leibniz)关于微积分发明权的争论。事实上，在他们的时代，很多微积分的工具开始运用在科学和工程之中，但是，微积分的基础并没有真正建立。那个长时间一直解释不清楚的“无穷小量”的幽灵，困扰了数学界一百多年的时间——这就是“第二次数学危机”。直到柯西用数列极限的观点重新建立了微积分的基本概念，这门学科才开始有了一个比较坚实的基础。直到今天，整个分析的大厦还是建立在极限的基石之上。

柯西(Cauchy)为分析的发展提供了一种严密的语言，但是他并没有解决微积分的全部问题。在19世纪的时候，分析的世界仍然有着一些挥之不去的乌云。而其中最重要的一个没有解决的是“函数是否可积的问题”。我们在现在的微积分课本中学到的那种通过“无限分割区间，取矩阵面积和的极限”的积分，是大约在1850年由黎曼(Riemann)提出的，叫做黎曼积分。但是，什么函数存在黎曼积分呢（黎曼可积）？数学家们很早就证明了，定义在闭区间内的连续函数是黎曼可积的。可是，这样的结果并不令人满意，工程师们需要对分段连续函数的函数积分。

### 实分析：在实数理论和测度理论上建立起现代分析

在19世纪中后期，不连续函数的可积性问题一直是分析的重要课题。对于定义在闭区间上的黎曼积分的研究发现，可积性的关键在于“不连续的点足够少”。只有有限处不连续的函数是可积的，可是很多有数学家们构造出很多在无限处不连续的可积函数。显然，在衡量点集大小的时候，有限和无限并不是一种合适的标准。在探讨“点集大小”这个问题的过程中，数学家发现实数轴——这个他们曾经以为已经充分理解的东西——有着许多他们没有想到的特性。在极限思想的支持下，实数理论在这个时候被建立起来，它的标志是对实数完备性进行刻画的几条等价的定理（确界定理，区间套定理，柯西收敛定理，Bolzano-Weierstrass Theorem和Heine-Borel Theorem等等）——这些定理明确表达出实数和有理数的根本区别：完备性（很不严格的说，就是对极限运算封闭）。随着对实数认识的深入，如何测量“点集大小”的问题也取得了突破，勒贝格创造性地把关于集合的代数，和Outer content（就是“外测度”的一个雏形）的概念结合起来，建立了测度理论(Measure Theory)，并且进一步建立了以测度为基础的积分——勒贝格(Lebesgue Integral)。在这个新的积分概念的支持下，可积性问题变得一目了然。

上面说到的实数理论，测度理论和勒贝格积分，构成了我们现在称为实分析(Real Analysis)的数学分支，有些书也叫实变函数论。对于应用科学来说，实分析似乎没有古典微积分那么“实用”——很难直接基于它得到什么算法。而且，它要解决的某些“难题”——比如处处不连续的函数，或者处处连续而处处不可微的函数——在工程师的眼中，并不现实。但是，我认为，它并不是一种纯数学概念游戏，它的现实意义在于为许多现代的应用数学分支提供坚实的基础。下面，我仅仅列举几条它的用处：

1. 黎曼可积的函数空间不是完备的，但是勒贝格可积的函数空间是完备的。简单的说，一个黎曼可积的函数列收敛到的那个函数不一定是黎曼可积的，但是勒贝格可积的函数列必定收敛到一个勒贝格可积的函数。在泛函分析，还有逼近理论中，经常需要讨论“函数的极限”，或者“函数的级数”，如果用黎曼积分的概念，这种讨论几乎不可想像。我们有时看一些paper中提到Lp函数空间，就是基于勒贝格积分。
2. 勒贝格积分是傅立叶变换（这东西在工程中到处都是）的基础。很多关于信号处理的初等教材，可能绕过了勒贝格积分，直接讲点面对实用的东西而不谈它的数学基础，但是，对于深层次的研究问题——特别是希望在理论中能做一些工作——这并不是总能绕过去。
3. 在下面，我们还会看到，测度理论是现代概率论的基础。

### 拓扑学：分析从实数轴推广到一般空间——现代分析的抽象基础

随着实数理论的建立，大家开始把极限和连续推广到更一般的地方的分析。事实上，很多基于实数的概念和定理并不是实数特有的。很多特性可以抽象出来，推广到更一般的空间里面。对于实数轴的推广，促成了点集拓扑学(Point-set Topology)的建立。很多原来只存在于实数中的概念，被提取出来，进行一般性的讨论。在拓扑学里面，有4个C构成了它的核心：

1. Closed set（闭集合）。在现代的拓扑学的公理化体系中，开集和闭集是最基本的概念。一切从此引申。这两个概念是开区间和闭区间的推广，它们的根本地位，并不是一开始就被认识到的。经过相当长的时间，人们才认识到：开集的概念是连续性的基础，而闭集对极限运算封闭——而极限正是分析的根基。
2. Continuous function （连续函数）。连续函数在微积分里面有个用epsilon-delta语言给出的定义，在拓扑学中它的定义是“开集的原像是开集的函数”。第二个定义和第一个是等价的，只是用更抽象的语言进行了改写。我个人认为，它的第三个（等价）定义才从根本上揭示连续函数的本质——“连续函数是保持极限运算的函数”——比如y是数列x1, x2, x3, … 的极限， 那么如果 f 是连续函数，那么 f(y) 就是 f(x1), f(x2), f(x3), …的极限。连续函数的重要性，可以从别的分支学科中进行类比。比如群论中，基础的运算是“乘法”，对于群，最重要的映射叫“同态映射”——保持“乘法”的映射。在分析中，基础运算是“极限”，因此连续函数在分析中的地位，和同态映射在代数中的地位是相当的。
3. Connected set （连通集合）。比它略为窄一点的概念叫(Path connected)，就是集合中任意两点都存在连续路径相连——可能是一般人理解的概念。一般意义下的连通概念稍微抽象一些。在我看来，连通性有两个重要的用场：一个是用于证明一般的中值定理(Intermediate Value Theorem)，还有就是代数拓扑，拓扑群论和李群论中讨论根本群(Fundamental Group)的阶。
4. Compact set（紧集）。Compactness似乎在初等微积分里面没有专门出现，不过有几条实数上的定理和它其实是有关系的。比如，“有界数列必然存在收敛子列”——用compactness的语言来说就是——“实数空间中有界闭集是紧的”。它在拓扑学中的一般定义是一个听上去比较抽象的东西——“紧集的任意开覆盖存在有限子覆盖”。这个定义在讨论拓扑学的定理时很方便，它在很多时候能帮助实现从无限到有限的转换。对于分析来说，用得更多的是它的另一种形式——“紧集中的数列必存在收敛子列”——它体现了分析中最重要的“极限”。Compactness在现代分析中运用极广，无法尽述。微积分中的两个重要定理：极值定理(Extreme Value Theory)，和一致收敛定理(Uniform Convergence Theorem)就可以借助它推广到一般的形式。

从某种意义上说，点集拓扑学可以看成是关于“极限”的一般理论，它抽象于实数理论，它的概念成为几乎所有现代分析学科的通用语言，也是整个现代分析的根基所在。

### 微分几何：流形上的分析——在拓扑空间上引入微分结构

拓扑学把极限的概念推广到一般的拓扑空间，但这不是故事的结束，而仅仅是开始。在微积分里面，极限之后我们有微分，求导，积分。这些东西也可以推广到拓扑空间，在拓扑学的基础上建立起来——这就是微分几何。从教学上说，微分几何的教材，有两种不同的类型，一种是建立在古典微机分的基础上的“古典微分几何”，主要是关于二维和三维空间中的一些几何量的计算，比如曲率。还有一种是建立在现代拓扑学的基础上，这里姑且称为“现代微分几何”——它的核心概念就是“流形”(manifold)——就是在拓扑空间的基础上加了一套可以进行微分运算的结构。现代微分几何是一门非常丰富的学科。比如一般流形上的微分的定义就比传统的微分丰富，我自己就见过三种从不同角度给出的等价定义——这一方面让事情变得复杂一些，但是另外一个方面它给了同一个概念的不同理解，往往在解决问题时会引出不同的思路。除了推广微积分的概念以外，还引入了很多新概念：tangent space, cotangent space, push forward, pull back, fibre bundle, flow, immersion, submersion 等等。

近些年，流形在machine learning似乎相当时髦。但是，坦率地说，要弄懂一些基本的流形算法， 甚至“创造”一些流形算法，并不需要多少微分几何的基础。对我的研究来说，微分几何最重要的应用就是建立在它之上的另外一个分支：李群和李代数——这是数学中两大家族分析和代数的一个漂亮的联姻。分析和代数的另外一处重要的结合则是泛函分析，以及在其基础上的调和分析。

## 代数：一个抽象的世界

### 关于抽象代数

回过头来，再说说另一个大家族——代数。

如果说古典微积分是分析的入门，那么现代代数的入门点则是两个部分：线性代数(linear algebra)和基础的抽象代数(abstract algebra)——据说国内一些教材称之为近世代数。

代数——名称上研究的似乎是数，在我看来，主要研究的是运算规则。一门代数，其实都是从某种具体的运算体系中抽象出一些基本规则，建立一个公理体系，然后在这基础上进行研究。一个集合再加上一套运算规则，就构成一个代数结构。在主要的代数结构中，最简单的是群(Group)——它只有一种符合结合率的可逆运算，通常叫“乘法”。如果，这种运算也符合交换率，那么就叫阿贝尔群(Abelian Group)。如果有两种运算，一种叫加法，满足交换率和结合率，一种叫乘法，满足结合率，它们之间满足分配率，这种丰富一点的结构叫做环(Ring)，如果环上的乘法满足交换率，就叫可交换环(Commutative Ring)。如果，一个环的加法和乘法具有了所有的良好性质，那么就成为一个域(Field)。基于域，我们可以建立一种新的结构，能进行加法和数乘，就构成了线性代数(Linear algebra)。

代数的好处在于，它只关心运算规则的演绎，而不管参与运算的对象。只要定义恰当，完全可以让一只猫乘一只狗得到一头猪:-)。基于抽象运算规则得到的所有定理完全可以运用于上面说的猫狗乘法。当然，在实际运用中，我们还是希望用它干点有意义的事情。学过抽象代数的都知道，基于几条最简单的规则，比如结合律，就能导出非常多的重要结论——这些结论可以应用到一切满足这些简单规则的地方——这是代数的威力所在，我们不再需要为每一个具体领域重新建立这么多的定理。

抽象代数有在一些基础定理的基础上，进一步的研究往往分为两个流派：研究有限的离散代数结构（比如有限群和有限域），这部分内容通常用于数论，编码，和整数方程这些地方；另外一个流派是研究连续的代数结构，通常和拓扑与分析联系在一起（比如拓扑群，李群）。我在学习中的focus主要是后者。

### 线性代数：“线性”的基础地位

对于做Learning, vision, optimization或者statistics的人来说，接触最多的莫过于线性代数——这也是我们在大学低年级就开始学习的。线性代数，包括建立在它基础上的各种学科，最核心的两个概念是向量空间和线性变换。线性变换在线性代数中的地位，和连续函数在分析中的地位，或者同态映射在群论中的地位是一样的——它是保持基础运算（加法和数乘）的映射。

在learning中有这样的一种倾向——鄙视线性算法，标榜非线性。也许在很多场合下面，我们需要非线性来描述复杂的现实世界，但是无论什么时候，线性都是具有根本地位的。没有线性的基础，就不可能存在所谓的非线性推广。我们常用的非线性化的方法包括流形和kernelization，这两者都需要在某个阶段回归线性。流形需要在每个局部建立和线性空间的映射，通过把许多局部线性空间连接起来形成非线性；而kernerlization则是通过置换内积结构把原线性空间“非线性”地映射到另外一个线性空间，再进行线性空间中所能进行的操作。而在分析领域，线性的运算更是无处不在，微分，积分，傅立叶变换，拉普拉斯变换，还有统计中的均值，通通都是线性的。

### 泛函分析：从有限维向无限维迈进

在大学中学习的线性代数，它的简单主要因为它是在有限维空间进行的，因为有限，我们无须借助于太多的分析手段。但是，有限维空间并不能有效地表达我们的世界——最重要的，函数构成了线性空间，可是它是无限维的。对函数进行的最重要的运算都在无限维空间进行，比如傅立叶变换和小波分析。这表明了，为了研究函数（或者说连续信号），我们需要打破有限维空间的束缚，走入无限维的函数空间——这里面的第一步，就是泛函分析。

泛函分析(Functional Analysis)是研究的是一般的线性空间，包括有限维和无限维，但是很多东西在有限维下显得很trivial，真正的困难往往在无限维的时候出现。在泛函分析中，空间中的元素还是叫向量，但是线性变换通常会叫作“算子”(operator)。除了加法和数乘，这里进一步加入了一些运算，比如加入范数去表达“向量的长度”或者“元素的距离”，这样的空间叫做“赋范线性空间”(normed space)，再进一步的，可以加入内积运算，这样的空间叫“内积空间”(Inner product space)。

大家发现，当进入无限维的时间时，很多老的观念不再适用了，一切都需要重新审视。

1. 所有的有限维空间都是完备的（柯西序列收敛），很多无限维空间却是不完备的（比如闭区间上的连续函数）。在这里，完备的空间有特殊的名称：完备的赋范空间叫巴拿赫空间(Banach space)，完备的内积空间叫希尔伯特空间(Hilbert space)。
2. 在有限维空间中空间和它的对偶空间的是完全同构的，而在无限维空间中，它们存在微妙的差别。
3. 在有限维空间中，所有线性变换（矩阵）都是有界变换，而在无限维，很多算子是无界的(unbounded)，最重要的一个例子是给函数求导。
4. 在有限维空间中，一切有界闭集都是紧的，比如单位球。而在所有的无限维空间中，单位球都不是紧的——也就是说，可以在单位球内撒入无限个点，而不出现一个极限点。
5. 在有限维空间中，线性变换（矩阵）的谱相当于全部的特征值，在无限维空间中，算子的谱的结构比这个复杂得多，除了特征值组成的点谱(point spectrum)，还有approximate point spectrum和residual spectrum。虽然复杂，但是，也更为有趣。由此形成了一个相当丰富的分支——算子谱论(Spectrum theory)。
6. 在有限维空间中，任何一点对任何一个子空间总存在投影，而在无限维空间中，这就不一定了，具有这种良好特性的子空间有个专门的名称切比雪夫空间(Chebyshev space)。这个概念是现代逼近理论的基础(approximation theory)。函数空间的逼近理论在Learning中应该有着非常重要的作用，但是现在看到的运用现代逼近理论的文章并不多。

### 继续往前：巴拿赫代数，调和分析，和李代数

基本的泛函分析继续往前走，有两个重要的方向。第一个是巴拿赫代数(Banach Algebra)，它就是在巴拿赫空间（完备的内积空间）的基础上引入乘法（这不同于数乘）。比如矩阵——它除了加法和数乘，还能做乘法——这就构成了一个巴拿赫代数。除此以外，值域完备的有界算子，平方可积函数，都能构成巴拿赫代数。巴拿赫代数是泛函分析的抽象，很多对于有界算子导出的结论，还有算子谱论中的许多定理，它们不仅仅对算子适用，它们其实可以从一般的巴拿赫代数中得到，并且应用在算子以外的地方。巴拿赫代数让你站在更高的高度看待泛函分析中的结论，但是，我对它在实际问题中能比泛函分析能多带来什么东西还有待思考。

最能把泛函分析和实际问题在一起的另一个重要方向是调和分析(Harmonic Analysis)。我在这里列举它的两个个子领域，傅立叶分析和小波分析，我想这已经能说明它的实际价值。它研究的最核心的问题就是怎么用基函数去逼近和构造一个函数。它研究的是函数空间的问题，不可避免的必须以泛函分析为基础。除了傅立叶和小波，调和分析还研究一些很有用的函数空间，比如Hardy space，Sobolev space，这些空间有很多很好的性质，在工程中和物理学中都有很重要的应用。对于vision来说，调和分析在信号的表达，图像的构造，都是非常有用的工具。

当分析和线性代数走在一起，产生了泛函分析和调和分析；当分析和群论走在一起，我们就有了李群(Lie Group)和李代数(Lie Algebra)。它们给连续群上的元素赋予了代数结构。我一直认为这是一门非常漂亮的数学：在一个体系中，拓扑，微分和代数走到了一起。在一定条件下，通过李群和李代数的联系，它让几何变换的结合变成了线性运算，让子群化为线性子空间，这样就为Learning中许多重要的模型和算法的引入到对几何运动的建模创造了必要的条件。因此，我们相信李群和李代数对于vision有着重要意义，只不过学习它的道路可能会很艰辛，在它之前需要学习很多别的数学。

## 现代概率论：在现代分析基础上再生　

最后，再简单说说很多Learning的研究者特别关心的数学分支：概率论。自从Kolmogorov在上世纪30年代把测度引入概率论以来，测度理论就成为现代概率论的基础。在这里，概率定义为测度，随机变量定义为可测函数，条件随机变量定义为可测函数在某个函数空间的投影，均值则是可测函数对于概率测度的积分。值得注意的是，很多的现代观点，开始以泛函分析的思路看待概率论的基础概念，随机变量构成了一个向量空间，而带符号概率测度则构成了它的对偶空间，其中一方施加于对方就形成均值。角度虽然不一样，不过这两种方式殊途同归，形成的基础是等价的。

在现代概率论的基础上，许多传统的分支得到了极大丰富，最有代表性的包括鞅论(Martingale)——由研究赌博引发的理论，现在主要用于金融（这里可以看出赌博和金融的理论联系，:-P），布朗运动(Brownian Motion)——连续随机过程的基础，以及在此基础上建立的随机分析(Stochastic Calculus)，包括随机积分（对随机过程的路径进行积分，其中比较有代表性的叫伊藤积分(Ito Integral)），和随机微分方程。对于连续几何运用建立概率模型以及对分布的变换的研究离不开这些方面的知识。

 **终于写完了——也谢谢你把这么长的文章看完，希望其中的一些内容对你是有帮助的。**

# 概率漫谈

前一段时间，随着研究课题的深入，逐步研习现代概率理论，这是一个令人耳目一新的世界。这个世界实在太博大，我自己也在不断学习之中。这篇就算起一个头吧，后面有空的时候还会陆续写一些文章和大家分享我在学习过程中的思考。

**概率论要解决的问题**

概率论是很古老的数学分支了——探讨的是不确定的问题，就是说，一件事情可能发生，也可能不发生。然后，我们要预计一下，它有多大机会会发生，这是概率论要解决的问题。这里面要特别强调概率和统计的区别，事实上这个区别在很多文章里面被混淆了。举一个简单的例子，比如抛硬币。那么我们可以做两件事情：

1. 我们预先知道抛硬币的过程是“平衡的”，也就是说出现正面的机会和出现背面的机会都是50%，那么，这就是我们的概率模型——这个简单的模型有个名字——伯努利试验(Bernoulli trial)。然后，我们可以预测，如果我们抛10000次硬币，那么正面和背面出现的次数大概各在5000次左右。这种执因“测”果的问题是概率论要解决的，它在事情发生之前进行。
2. 我们预先不知道抛硬币的过程遵循什么法则。于是，我们先去做个实验，抛10000次硬币，数一下正面和反面各出现了多少次。如果各出现了5000次，那么我们可以有很高的信心去认为，这是一个“平衡的”硬币。如果正面出现9000次，反面出现1000次，那么我们就可以基本认为这个硬币遵循一个严重偏向正面的非平衡法则——正面出现的概率是10%。这种执果溯因的事情是统计要解决的，它在事情发生之后进行，根据观察到的情况归纳背后的模型(Model)或者法则(Law)。

这篇文章只讨论概率论的问题。

**经典概率的困难**

什么是概率呢？长期以来，一个传统而直到今天还被广泛运用的概念是：概率就是一个事情发生的机会——这就是经典概率论的出发点和基础。大部门的初等概率论教科书，给出一个貌似颇为严谨的定义：我们有一个样本空间(sample space)，然后这个样本空间中任何一个子集叫做事件(event)，我们给每个事件A赋一个非负实数P(A)。如果P(A)满足

- P(A) >= 0
- 全集（整个样本空间）的P值为1
- 对于（有限个或者可数个）互不相交的事件，它们的并集的P值等于各自P值的和。这个属性叫可数可加性 (Countable Additivity)

那么我们就称P为概率。这个定义，以及由此而演绎出来的整个经典概率体系，广为接受并被成功用在无数的地方。

但是，这样的定义藏着一个隐蔽很深的漏洞——使得从这个定义出发能在数学上严格导出互相矛盾的结果。假设样本空间是S=[0, 1]，里面的实数依循均匀分布，我们构造这样一个集合。首先，建立一个等价关系：相差值是有理数的实数是等价的。依据这个等价关系，把0到1之间的实数划分为等价类，这样我们有无数个等价类。从每个等价类中随便抽出一个实数作为代表，这些代表构成一个集合，记为H。（注意：我们有不可数无限个等价类，因此这个集合的存在依赖于选择公理(Axiom of Choice))

那么P(H) 是什么呢？如果P(H)等于零，那么P(S) = 0；如果P(H) > 0，那么P(S) = 无穷大。无论如何，都和P(S) = 1的要求矛盾。这下麻烦大了，我们一直依赖的概率定义竟然是自相矛盾的！

也许，从数学家的眼光看来，这个问题很严重。但是，这对于我们有什么意义呢。我们一辈子都用不着这种只存在于数学思辨中的特殊构造的集合！不过，即使我们从实用出发不顾及这类逻辑漏洞，传统概率论还是会给我们带来一定程度的麻烦。

一个问题，可能大家都有所感觉。那就是，我们在本科学习的概率论中有着两套系统：离散分布和连续分布，基本什么定理都得提供这两种形式，但是它们的推导过程似乎没什么太大差别，一个用求和一个用积分而已。几乎一样的事情，为什么要干两遍呢。

还有，那种离散和连续混合的分布又怎么处理呢？这种“离散连续混合的分布”不仅仅是一种理论可能，在实际上它的应用也在不断增长。一个重要的例子就是狄里克莱过程(Dirichlet Process)——它是learning中的无限混合模型的核心——这种模型用于解决传统有限混合模型中(比如GMM)子模型个数不确定的难题。这种过程，在开始时(t = 0)通常是连续分布， 随着时间演化，在t > 0时变成连续和离散混合分布，而且离散部分比例不断加重，最后（几乎必然）收敛到一个离散分布。这种模型用传统的连续和离散分离的处理方式就显得很不方便了。

事实上，我们是可以把对连续模型，离散模型，以及各种既不连续也不离散的模型，使用一种统一的表达。这就是现代概率论采取的方式。

**现代概率论——从测度开始**

现代概率论是前苏联大数学家Kolmogorov在上世纪30年代基于测度理论(Measure theory)的基础上重新建立的，它是一个非常严密的公理化体系。什么是测度呢？说白了，就是一个东西的大小。测度是非负的，而且符合可数可加性，比如几块不相交的区域的总面积，等于各自面积之和。这个属性和概率的属性如出一辙。测度理论自从勒贝格(Lebesgue)那个时候开始，已经建立了一套严格的数学体系。因此，现代概率论不需要把前辈的路子重新走一遍。基于测度论，概率的定义可以直接给出：

概率就是总测度（整个样本空间的测度）为1的测度。

测度理论和经典概率论有个很大的不同，不是什么集合都有一个测度的。比如前面构造的那个奇怪的集合，它就没有测度。所以，根据测度理论，样本空间中的集合分成两种：可测的(measurable)和不可测的。我们只对可测集赋予测度或者概率。特别留意，测度为零的集合也是可测的，叫做零测集。所谓不可测集，就是那种测度既不是零，也不是非零，就是什么都不能是的集合。

因此，根据测度理论，我们描述一个概率空间，需要三个要素：一个样本空间，所有可测集（它们构成sigma-代数：可测集的交集，并集和补集都是可测的)，还有就是一个概率函数，给每个可测集赋一个概率。

通过引入可测性的概念，那种给我们带来麻烦的集合被排除在外了。不过，可测性的用处远不仅仅是用于对付那些“麻烦集合”。它还表达了一个概率空间能传达什么样的信息。这里暂时不深入这个问题，以后要有机会写到条件概率(conditional probability)和鞅论(Martingale theory)时，再去讨论这个事情。这里只是强调一下（虽然有点空口说白话），可测性是讨论随机过程和随机分析的非常重要的概念，在实际计算和推导中也非常有用。

我们看到，这套理论首先通过可测性解决了逻辑上的漏洞。那怎么它又是怎么统一连续和离散的表达的呢？这里面，测度理论提供了一个重要的工具——勒贝格积分(Lebesgue Integral)。噢，原来是积分，那不也是关于连续的么。不过，这里的勒贝格积分和在大学微积分课里面学的传统的积分（也叫黎曼积分）不太一样，它对离散和连续通吃，还能处理既不离散又不连续，或者处处有定义而又处处不连续的各种各样的东西）。

举一个简单例子，比如定义在[0, 1]的函数，它在[0, 0.5)取值为1，在[0.5, 1]取值为2。这是一个简单的阶梯函数，期望是1.5。按照传统的黎曼积分求期望，就是把定义域[0, 1]分成很多小段，然后把每小段加起来。勒贝格积分反其道而行之，它不分定义域，而是去分值域，然后看看每个值对应的那块的面积（测度）是多大。这个函数取值只有两个：1和2。那么值为1那块的面积为0.5， 值为2的那块的面积也是0.5，积分就是以这些值为系数，把对应的面积加起来：0.5 x 1 + 0.5 x 2 = 1.5。

上面是连续的情况，离散的呢？假设我们在一个离散集[0, 1, 2]上定义一个概率，P(0) = 0.5, P(1) = P(2) = 0.25。对一个函数f(x) = x，求均值。那么，我们看到，值为0, 1, 2对应的测度分别是0.5, 0.25, 0.25，那么我们按照“面积加权法”可以求出：0 x 0.5 + 1 x 0.25 + 2 x 0.25 = 0.75。

对于取值范围连续的情况，它通过取值有限的阶梯函数逼近，求取上极限来获得积分值。

总体来说，勒贝格积分的idea很简单：划分值域，面积加权。不过却有效解决了连续离散的表达的统一问题。大家如果去翻翻基于测度理论建立起来的现代概率论的书，就会看到：所谓“离散分布”和“连续分布”的划分已经退出历史舞台，所有定理都只有一个版本——按照勒贝格积分形式给出的版本。对于传统的离散和连续分布的区别，就是归结为它们的测度函数的具体定义不同的区别。

那我们原来学的关于离散分布的点概率函数，或者连续分布的概率密度函数，也被统一了——积分的反操作就是求导，所以那两个函数都叫成了测度积分的“导数”，有一个名字Radon-Nikodym Derivative。它们的区别归结为原测度的具体不同，点概率函数是概率测度相对于计数测度的导数，而概率密度函数则是概率测度相对于勒贝格测度的导数。

我们看到，现代概率论建立了测度概念和概率概念的联系：

- 测度 ———— 概率
- 积分 ———— 期望

**谁是基础？概率 vs. 期望**

从上面的介绍看来，似乎概率（测度）是一个更基本的概念，而期望（积分）是从那引申出来的概念。实事上，整个过程可以反过来，我们可以把期望作为基本概念，演绎出概率的概念。整个概率论，也由此基于期望而展开——其实，如果不是历史惯性，整套理论叫做“期望论”也挺合适的，呵呵。关于这个事情，以后有机会，再做一个更详细的探讨。这里，由于篇幅原因，只提出两个关键点：

1. 如果我们定义了一个期望函数，那么某个子集（事件）的概率就是对它的指示函数的期望。比如一个事件A，它的指示函数IA定义为 IA(x) = 1 当x 属于A, 否则为0。那么A是一个取值要么是0要么是1的随机变量，它的期望就是A的概率。从这个意义上说，所谓概率就是期望的一个简单特例(随机变量是集合的指示函数)。

2. 我们观察到，随机变量的期望符合两个重要性质：

3. - 期望是单调的：如果总有 X1 <= X2，那么E(X1) <= E(X2)；
   - 期望是线性的：E(a * X1 + b * X2) = a * E(X1) + b * E(X2)；

4. 所有定义在可测集合上的单调线性实函数E，并且有E(1) = 1，那么E就是一个期望，它施加于任何一个集合的指示函数，就产生那个集合的概率。

有了这么三条，我们可以抛开概率，先定义“期望”这个概念：定义在可测集合上的单调线性实函数。然后，再把指示函数的期望定义成概率。那么，期望就变成了一个更为基本的概念。

事实上，某些新出来的现代概率论的教科书已经处理得更为简洁：直接把“期望”和“概率”看成同一个概念——同时，把几个集合的指示函数和那个集合本身看成一回事。相比于把期望和概率分成两个不同的东西来处理，很多事情的描述和演绎变得非常简洁，而又不损失任何严密性（预先给出期望和概率的一致性的一个严格证明，大概思路是上面三点，不过数学上有一些处理）。由于，把期望视为线性函数，因此对于某个随机变量的期望就变成了有点类似于随机变量和测度的一种类似于“内积”的双线性运算结构。很多本来复杂的概率推演就转化为线性代数演算——不但使得演绎更为方便简洁，而且有助于对于结果的代数特性的更深刻的理解。

总而言之，从经典概率论到现代概率论，发生了两个非常重要的变化：

1. 测度的引入——解决了基础逻辑的难题，统一了离散分布和连续分布。
2. 期望的基础地位——一定程度上消弭了概率和期望的区别，同时把很多概率问题“代数化”。

# 变换与不变性

变换与不变是数学里面最令人神往的一对矛盾统一。所谓“变换不变性”，以不变刻画变化，其核心深刻反映了这种对偶的关系。

变换不变性贯彻于很多具体的数学领域之中，对它的全面讨论远非我力所能及。这篇文章只是讨论它的一个简单例子，希望通过一个小小的窗口管窥这个世界的奥妙。

**何谓旋转？**

这篇文章只想很初步地回答两个基本的问题

1. 什么叫做旋转(Rotation)？
2. 什么东西被旋转后是不变的（具有旋转不变性）？

为了简单起见，只考虑二维空间，它到高维的推广也并不特别困难。在代数上，所谓旋转，可以用下面的方程表达：

x’ = x cos(t) – y sin(t);  y’ = x sin(t) + y cos(t)。

但是，这样一种表达并不能给人以直观的感觉。所以，我们还是回到几何本身来看待这个问题。什么东西旋转后是不变的呢？“东西”这个概念太模糊了，在数学上，我们讨论问题得首先指定一个范围，就是所谓“对象的集合”。这里，我们先考虑最简单的几何构造——点，我们讨论的全集就是整个二维空间的点集。那么上面的问题，就细化为“哪些点旋转后是不变的？” 答案是非常显而易见的，只有一点——就是原点。

这样，我们得到了对于旋转变换的第一个变换不变性：原点不变。可是，这么个条件太宽了，我们很容易找到别的变换，比如缩放，它也是原点不变的。因此，依靠单点的不变性在这里并不能非常有效地刻画变换的特点，我们需要更强大的对不变性的表达。

**从“原点不变”到“圆不变”**

于是，数学界从对一个点的变换，推广到对一个集合的变换。比如集合 X = {x1, x2, … }, 那么一个变换 T 施加到集合上，得到“变换后的集合”就是：T (X) = { T(x1), T(x2), … }。 如果有 T(X) = X，我们说集合X是变换不变的。 事实上，T(X) 和 X 只要包含相同的那堆元素就行了，每个单个的元素都允许被变化到另一点的。

这个推广看似简单，看是表达能力完全不在了一个层次上。比如，一个集合如果有n点的话，它就有2^n个子集，如果只考虑单点，我们只能看n个东西，而如果允许考虑子集的话，我们能看2^n个东西，传递的信息自然也丰富得多。

基于“集合的变换不变性”这个概念，我们可以找到这么一些点集——“以原点为圆心的圆”，它们是旋转不变的。“圆不变”比前面的“原点不变”进步了很多，已经在相当程度上刻画出了旋转的特性，最起码，刚才那个反例“缩放变换”被排除了。如果我们把变换限定为仿射变换（Affine Transform），那么我们已经基于“圆不变性”得到对旋转的严格定义：“旋转就是圆不变的彷射变换”。

“旋转不变的集合”并不仅仅是圆，事实上“不同半径的圆的任意并集”都是旋转不变的，反过来，任何旋转不变的集合都是圆的并集。这样，以“圆”为基(basis)，通过任意并集生成(generate)的集簇(collection of subsets)就对应于全部旋转不变的集合。

这里，我们得到了两个大集合：{所有旋转变换的集合}，{所有圆的集合}，这两个集合都分别同胚于一维实空间，总维数是2，等于原空间的维数——这个结果并非偶然，它其实就是李群论中变换空间，轨迹空间（商空间），和原空间的维数关系定理的一个特例。

我们把讨论的范围限定为仿射变换（包括了平移，旋转，拉伸，缩放和它们的各种合成变换）的情况下，圆不变完整地刻画了旋转变换。旋转（变换）和圆（不变性）构成了一对对偶。

**从“旋转——圆”到“变换群——轨迹”**

旋转对应于圆，这个我们甚至可以直接观察得到。但是，对于一般的变换呢，我们如何找它们的不变集？这需要把概念推广到：“变换群”和“轨迹”。

一个变换群，就是指一群可逆变换，对于“合成”是封闭的——群里面两个变换的合成必然还在群里。所以，旋转是一个变换群：因为旋转再旋转还是旋转。对于空间一点，把变换群中每个变换都对它施加一下，那么，就可以得到一个集合，叫做“轨迹”。比如旋转群，它（各种角度的旋转）施加于任意一点，就得到一个圆，那么圆就是旋转的轨迹。由于变换群对于合成的封闭性，可以证明，对于任何一个变换群，它施加于任何一点得到的轨迹是变换不变的。这样，我们从“旋转——圆”的对偶关系，进一步推广到了“变换群——轨迹”的对偶关系，从而我们获得了以轨迹刻画变换的方法，这通常比代数方法更为直观和具有更加鲜明的几何意义。

如果变换是自由的，“就是说不同的变换施加于同一点会有不同结果”，那么，变换群和所有轨迹组成的空间（商空间）具有一个结论：它们的维度之和等于原空间维度。这在一定意义上，反映出它们的关系是“互补”的。

**概率分布的“变换不变性”**

进一步的，我们刚才把变换的对象，从点推广到集合，得到了很多重要的观察。那么，这个事情还可以进一步延伸。假设说，我们有一个空间概率分布，就是一个点以一定的概率出现在空间各处，对这么一个随机点，施加一个变换，在变换后的时刻，得到的点依旧是一个空间分布，变换后的分布是由变换前的分布和变换本身共同决定的。我们也可以这么理解，我们“对整个分布”进行了变换，得到了新的分布。

哪些分布是“旋转不变呢”？就是说，分布旋转后还是同样的分布。我们很容易找到一些：比如标准高斯分布，圆盘里面的均匀分布，等等。为了更清楚地说明这个问题，我们需要更明确的条件。对于连续分布，有一个很容易得到但是很重要的结论：如果这个分布在所有的轨迹上都具相等的概率密度，那么，这个分布是变换不变的。特别的，如果一个分布在每个圆上都是等概率密度，那么这个分布旋转不变。

反过来，标准高斯分布对于一个变换是不变的，那么这个变换是不是必然是旋转呢？

以变换不变性为桥梁，我们可以发现概率分布和几何有着某种内在的联系。一直以来，我们讨论分布时，都关注它的代数形式，而事实上分布的几何形态同样蕴含着丰富的信息，也提供了不同的视角。变换不变性，则是探讨这个问题，从而建立概率论和几何学的联系的一个重要工具。

另外，在随机过程中，对于转移变换的变换不变性，对于描述过程的“各态历经性”(ergodicity)也有着密切的关系。

# 第二篇 数学书推荐

- Algebra, Analysis, and Geometry

 代数，分析，和几何是现代数学的三大基础分支。在这些科目上的扎实基础对于理论探索是非常重要的。而点集拓扑则是分析和几何的共同基础。而抽象代数则是相对独立的一个分支，但是抽象代数中的很多概念和思想则广泛渗透于其它数学学科之中。

 而在分析中，实分析是一切分析的基础。在此基础上，发展出来的复分析，泛函分析，调和分析，和微分方程理论，在工程实践中都有重要应用。对于机器学习领域来说，泛函分析的希尔伯特空间理论和算子谱论发挥着核心作用（比如Reproducing Kernel Hilbert Space, Markov Process的转移核的谱）。调和分析则在信号处理和分解方面起着关键作用（傅里叶变换就是调和分析的研究内容之一，另外，现在非常火热的compressed sensing或者sparse coding的数学根源也是调和分析）。

微分方程理论，以及李群和李代数理论则是对动态过程进行建模的重要工具。而很多随机过程也和它们有密切关系，比如Markov Process的抽象分析往往可以归结为Markov Semigroup，这是一种类似李群的连续半群系统。而连续时间Markov process的分布变化则可以通过微分方程(比如Komolgorov Equation或者Fokker–Planck Equation)描述。

- Optimization and Numerical Computation

这个列表主要以优化方面的书为主。目前常用的优化包括一般的数值优化（比如Gradient Descent），凸优化(Convex Optimization)，组合优化(Combinatorial Optimization)，还有线性优化(Linear Programming)。其中，线性优化虽然可以视为凸优化的一个特例，但是它太特殊了，因此需要单列出来，而且它和组合优化有着千丝万缕的联系（比如很多基于图和网络的优化问题）。 

- Probability Theory and Stochastic Processes

这个列表主要包括两个方面的书：现代概率理论 和 随机过程。

现代概率理论的基础是测度理论。对测度理论和在此基础上建立的积分理论的切实理解是学好现代概率论的重要基础。在现代概率论的许多重要概念中，比如independence, conditional expectation, martingale，可测性都起着核心作用。 

而随机过程考察的是随机函数，虽然很多时候它们是定义在时间上的，不过很多过程可以拓展到空间上——比如高斯过程和泊松过程。在众多的随机过程概念中，我觉得有一些是特别需要深入理解的：Markov, Brownian motion, Gaussian Process, Martingale，还有Poisson Process。

作为Gaussian Process的重要特例，Brownian motion在研究连续时间过程中扮演特别重要的角色。Diffusion， 还有随机积分和随机微分方程理论就是在其基础上建立。

## 线性代数 (Linear Algebra)

我想国内的大学生都会学过这门课程，但是，未必每一位老师都能贯彻它的精要。这门学科对于Learning是必备的基础，对它的透彻掌握是必不可少的。我在科大一年级的时候就学习了这门课，后来到了香港后，又重新把线性代数读了一遍，所读的是
Introduction to Linear Algebra (3rd Ed.)  by Gilbert Strang.
这本书是MIT的线性代数课使用的教材，也是被很多其它大学选用的经典教材。它的难度适中，讲解清晰，重要的是对许多核心的概念讨论得比较透彻。我个人觉得，学习线性代数，最重要的不是去熟练矩阵运算和解方程的方法——这些在实际工作中MATLAB可以代劳，关键的是要深入理解几个基础而又重要的概念：子空间(Subspace)，正交(Orthogonality)，特征值和特征向量(Eigenvalues and eigenvectors)，和线性变换(Linear transform)。从我的角度看来，一本线代教科书的质量，就在于它能否给这些根本概念以足够的重视，能否把它们的联系讲清楚。Strang的这本书在这方面是做得很好的。
而且，这本书有个得天独厚的优势。书的作者长期在MIT讲授线性代数课(18.06)，课程的video在MIT的Open courseware网站上有提供。有时间的朋友可以一边看着名师授课的录像，一边对照课本学习或者复习。
http://ocw.mit.edu/OcwWeb/Mathematics/18-06Spring-2005/CourseHome/index.htm

## 概率和统计(Probability and Statistics)
概率论和统计的入门教科书很多，我目前也没有特别的推荐。我在这里想介绍的是一本关于多元统计的基础教科书：
Applied Multivariate Statistical Analysis (5th Ed.)  by Richard A. Johnsonand Dean W. Wichern
这本书是我在刚接触向量统计的时候用于学习的，我在香港时做研究的基础就是从此打下了。实验室的一些同学也借用这本书学习向量统计。这本书没有特别追求数学上的深度，而是以通俗易懂的方式讲述主要的基本概念，读起来很舒服，内容也很实用。对于Linear regression,factor analysis,principal component analysis (PCA), andcanonical component analysis (CCA)这些Learning中的基本方法也展开了初步的论述。
之后就可以进一步深入学习贝叶斯统计和Graphical models。一本理想的书是
Introduction to Graphical Models (draft version).  by M. Jordan and C. Bishop.
我不知道这本书是不是已经出版了（不要和Learning in Graphical Models混淆，那是个论文集，不适合初学）。这本书从基本的贝叶斯统计模型出发一直深入到复杂的统计网络的估计和推断，深入浅出，statistical learning的许多重要方面都在此书有清楚论述和详细讲解。MIT内部可以access，至于外面，好像也是有电子版的。

## 分析(Analysis)
我想大家基本都在大学就学过微积分或者数学分析，深度和广度则随各个学校而异了。这个领域是很多学科的基础，值得推荐的教科书莫过于
Principles of Mathematical Analysis, by Walter Rudin
有点老，但是绝对经典，深入透彻。缺点就是比较艰深——这是Rudin的书的一贯风格，适合于有一定基础后回头去看。
在分析这个方向，接下来就是泛函分析(Functional Analysis)。
Introductory Functional Analysis with Applications, by Erwin Kreyszig.
适合作为泛函的基础教材，容易切入而不失全面。我特别喜欢它对于谱论和算子理论的特别关注，这对于做learning的研究是特别重要的。Rudin也有一本关于functional analysis的书，那本书在数学上可能更为深刻，但是不易于上手，所讲内容和learning的切合度不如此书。
在分析这个方向，还有一个重要的学科是测度理论(Measure theory)，但是我看过的书里面目前还没有感觉有特别值得介绍的。

## 拓扑(Topology)：

在我读过的基本拓扑书各有特色，但是综合而言，我最推崇：
Topology (2nd Ed.)  by James Munkres
这本书是Munkres教授长期执教MIT拓扑课的心血所凝。对于一般拓扑学(General topology)有全面介绍，而对于代数拓扑(Algebraic topology)也有适度的探讨。此书不需要特别的数学知识就可以开始学习，由浅入深，从最基本的集合论概念（很多书不屑讲这个）到Nagata-Smirnov Theorem和Tychonoff theorem等较深的定理（很多书避开了这个）都覆盖了。讲述方式思想性很强，对于很多定理，除了给出证明过程和引导你思考其背后的原理脉络，很多令人赞叹的亮点——我常读得忘却饥饿，不愿释手。很多习题很有水平。

## 流形理论(Manifold theory)：
对于拓扑和分析有一定把握时，方可开始学习流形理论，否则所学只能流于浮浅。我所使用的书是
Introduction to Smooth Manifolds.  by John M. Lee
虽然书名有introduction这个单词，但是实际上此书涉入很深，除了讲授了基本的manifold, tangent space, bundle, sub-manifold等，还探讨了诸如纲理论(Category theory)，德拉姆上同调(De Rham cohomology)和积分流形等一些比较高级的专题。对于李群和李代数也有相当多的讨论。行文通俗而又不失严谨，不过对某些记号方式需要熟悉一下。
虽然李群论是建基于平滑流形的概念之上，不过，也可能从矩阵出发直接学习李群和李代数——这种方法对于急需使用李群论解决问题的朋友可能更加实用。而且，对于一个问题从不同角度看待也利于加深理解。下面一本书就是这个方向的典范：
Lie Groups, Lie Algebras, and Representations: An Elementary Introduction.  by Brian C. Hall
此书从开始即从矩阵切入，从代数而非几何角度引入矩阵李群的概念。并通过定义运算的方式建立exponential mapping，并就此引入李代数。这种方式比起传统的通过“左不变向量场(Left-invariant vector field)“的方式定义李代数更容易为人所接受，也更容易揭示李代数的意义。最后，也有专门的论述把这种新的定义方式和传统方式联系起来。

------------------------------------------------------------------------------------
无论是研究Vision, Learning还是其它别的学科，数学终究是根基所在。学好数学是做好研究的基石。学好数学的关键归根结底是自己的努力，但是选择一本好的书还是大有益处的。不同的人有不同的知识背景，思维习惯和研究方向，因此书的选择也因人而异，只求适合自己，不必强求一致。上面的书仅仅是从我个人角度的出发介绍的，我的阅读经历实在非常有限，很可能还有比它们更好的书（不妨也告知我一声，先说声谢谢了）。

# 第三篇 Learning中的代数结构的建立

Learning是一个融会多种数学于一体的领域。说起与此有关的数学学科，我们可能会迅速联想到线性代数以及建立在向量空间基础上的统计模型——事实上，主流的论文中确实在很大程度上基于它们。
R^n (n-维实向量空间) 是我们在paper中见到最多的空间，它确实非常重要和实用，但是，仅仅依靠它来描述我们的世界并不足够。事实上，数学家们给我们提供了丰富得多的工具。
“空间”(space)，这是一个很有意思的名词，几乎出现在所有的数学分支的基础定义之中。归纳起来，所谓空间就是指一个集合以及在上面定义的某种数学结构。关于这个数学结构的定义或者公理，就成为这个数学分支的基础，一切由此而展开。

## $\R^n$ 空间脉络

还是从我们最熟悉的空间——R^n 说起吧。大家平常使用这个空间的时候，除了线性运算，其实还用到了别的数学结构，包括度量结构和内积结构。

第一，它是一个拓扑空间(Topological space)。而且从拓扑学的角度看，具有非常优良的性质：Normal (implying Hausdorff and Regular), Locally Compact, Paracompact, with Countable basis, Simply connected (implying connected and path connected), Metrizable. 
第二，它是一个度量空间(Metric space)。我们可以计算上面任意两点的距离。
第三，它是一个有限维向量空间(Finite dimensional space)。因此，我们可以对里面的元素进行代数运算（加法和数乘），我们还可以赋予它一组有限的基，从而可以用有限维坐标表达每个元素。
第四，基于度量结构和线性运算结构，可以建立起分析(Analysis)体系。我们可以对连续函数进行微分，积分，建立和求解微分方程，以及进行傅立叶变换和小波分析。
第五，它是一个希尔伯特空间（也就是完备的内积空间）(Hilbert space, Complete inner product space)。它有一套很方便计算的内积(inner product)结构——这个空间的度量结构其实就是从其内积结构诱导出来。更重要的，它是完备的(Complete)——代表任何一个柯西序列(Cauchy sequence)都有极限——很多人有意无意中其实用到了这个特性，不过习惯性地认为是理所当然了。
第六，它上面的线性映射构成的算子空间仍旧是有限维的——一个非常重要的好处就是，所有的线性映射都可以用矩阵唯一表示。特别的，因为它是有限维完备空间，它的泛函空间和它本身是同构的，也是R^n。因而，它们的谱结构，也就可以通过矩阵的特征值和特征向量获得。
第七，它是一个测度空间——可以计算子集的大小（面积/体积）。正因为此，我们才可能在上面建立概率分布(distribution)——这是我们接触的绝大多数连续统计模型的基础。

我们可以看到，这是一个非常完美的空间，为我们的应用在数学上提供了一切的方便，在上面，我们可以理所当然地认为它具有我们希望的各种良好性质，而无须特别的证明；我们可以直接使用它的各种运算结构，而不需要从头建立；而且很多本来不一样的概念在这里变成等价的了，我们因此不再需要辨明它们的区别。

以此为界，Learning的主要工作分成两个大的范畴：

1. 建立一种表达形式，让它处于上面讨论的R^n空间里面。
2. 获得了有限维向量表达后，建立各种代数算法或者统计模型进行分析和处理。

## 在$\R^n$空间建立一种表达形式进行Learning

- 直接基于原始数据建立表达。

我们关心的最终目标是一个个现实世界中的对象：一幅图片，一段语音，一篇文章，一条交易记录，等等。这些东西大部分本身没有附着一个数值向量的。为了构造一个向量表达，我们可以把传感器中记录的数值，或者别的什么方式收集的数值数据按照一定的顺序罗列出来，就形成一个向量了。如果有n个数字，就认为它们在R^n里面。

不过，这在数学上有一点小问题，在大部分情况下，根据数据产生的物理原理，这些向量的值域并不能充满整个空间。比如图像的像素值一般是正值，而且在一个有界闭集之中。这带来的问题是，对它们进行线性运算很可能得到的结果会溢出正常的范围——在大部分paper中，可能只是采用某些heuristics的手段进行简单处理，或者根本不管，很少见到在数学上对此进行深入探讨的——不过如果能解决实际问题，这也是无可厚非的，毕竟不是所有的工作都需要像纯数学那样追求严谨。

- 量化(quantization)，这是在处理连续信号时被广泛采用的方式。

只是习以为常，一般不提名字而已。比如一个空间信号（Vision中的image）或者时间信号，它们的domain中的值是不可数无限大的(uncountably infinite)，不要说表示为有限维向量，即使表达为无限序列也是不可能的。在这种情况下，一般在有限域内，按照一定顺序每隔一定距离取一个点来代表其周围的点，从而形成有限维的表达。这就是信号在时域或空域的量化。

这样做不可避免要丢失信息。但是，由于小邻域内信号的高度相关，信息丢失的程度往往并不显著。而且，从理论上说，这相当于在频域中的低通过率。对于有限能量的连续信号，不可能在无限高的频域中依然保持足够的强度，只要采样密度足够，丢失的东西可以任意的少。

除了表示信号，对于几何形体的表达也经常使用量化，比如表示curve和surface。

找出有限个数充分表达一个对象也许不是最困难的。不过，在其上面建立数学结构却未必了。一般来说，我们要对其进行处理，首先需要一个拓扑结构用以描述空间上的点是如何联系在一起。直接建立拓扑结构在数学上往往非常困难，也未必实用。因此，绝大部分工作采取的方式是首先建立度量结构。一个度量空间，其度量会自然地诱导出一个拓扑结构——不过，很多情况下我们似乎会无视它的存在。

最简单的情况，就是使用原始向量表达的欧氏距离(Euclidean distance)作为metric。不过，由于原始表达数值的不同特性，这种方式效果一般不是特别好，未必能有效表达实际对象的相似性（或者不相似性）。因此，很多工作会有在此基础上进行度量的二次建立。方式是多种多样的，一种是寻求一个映射，把原空间的元素变换到一个新的空间，在那里欧氏距离变得更加合适。这个映射发挥的作用包括对信息进行筛选，整合，对某些部分进行加强或者抑制。这就是大部分关于feature selection，feature extraction，或者subspace learning的文章所要做的。另外一种方式，就是直接调节距离的计算方式（有些文章称之为metric learning）。

这两种方式未必是不同的。如果映射是单射，那么它相当于在原空间建立了一个不同的度量。反过来，通过改变距离计算方式建立的度量在特定的条件下对应于某种映射。

大家可能注意到，上面提到的度量建立方法，比如欧氏距离，它需要对元素进行代数运算。对于普通的向量空间，线性运算是天然赋予的，我们无须专门建立，所以可以直接进行度量的构造——这也是大部分工作的基础。可是，有些事物其原始表达不是一个n-tuple，它可能是一个set，一个graph，或者别的什么特别的object。

## 怎么建立代数运算呢

- 一种方法是直接建立。

就是给这些东西定义自己的加法和数乘。这往往不是那么直接（能很容易建立的线性运算结构早已经被建立好并广泛应用了），可能需要涉及很深的数学知识，并且要有对问题本身的深入了解和数学上的洞察力。不过，一个新的代数结构一旦建立起来，其它的数学结构，包括拓扑，度量，分析，以及内积结构也随之能被自然地诱导出来，我们也就具有了对这个对象空间进行各种数学运算和操作的基础。加法和数乘看上去简单，但是如果我们对于本来不知道如何进行加法和数乘的空间建立了这两样东西，其理论上的贡献是非常大的。

（一个小问题：大家常用各种graphical model，但是，每次这些model都是分别formulate，然后推导出estimation和evaluation的步骤方法。是否可能对"the space of graphical model"或者它的某个特定子集建立某种代数结构呢？（不一定是线性空间，比如群，环，广群， etc）从而使得它们在代数意义上统一起来，而相应的estimation或者evaluation也可以用过代数运算derive。这不是我的研究范围，也超出了我目前的能力和知识水平，只是我相信它在理论上的重要意义，留作一个远景的问题。事实上，数学中确实有一个分支叫做 Algebraic statistics 可能在探讨类似的问题，不过我现在对此了解非常有限。）

- 嵌入到某个向量空间

回到我们的正题，除了直接建立运算定义，另外一种方式就是嵌入(embedding)到某个向量空间，从而继承其运算结构为我所用。当然这种嵌入也不是乱来，它需要保持原来这些对象的某种关系。最常见的就是保距嵌入(isometric embedding)，我们首先建立度量结构（绕过向量表达，直接对两个对象的距离通过某种方法进行计算），然后把这个空间嵌入到目标空间，通常是有限维向量空间，要求保持度量不变。

“嵌入”是一种在数学上应用广泛的手段。其主要目标就是通过嵌入到一个属性良好，结构丰富的空间，从而利用其某种结构或者运算体系。在拓扑学中，嵌入到metric space是对某个拓扑空间建立度量的重要手段。而在这里，我们是已有度量的情况下，通过嵌入获取线性运算的结构。除此以来，还有一种就是前些年比较热的manifold embedding，这个是通过保持局部结构的嵌入，获取全局结构，后面还会提到。

- 内积结构

接下来的一个重要的代数结构，就是内积(inner product)结构。内积结构一旦建立，会直接诱导出一种性质良好的度量，就是范数(norm)，并且进而诱导出拓扑结构。一般来说，内积需要建立在线性空间的基础上，否则连一个二元运算是否是内积都无法验证。不过，kernel理论指出，对于一个空间，只要定义一个正定核(positive kernel)——一个符合正定条件的二元运算，就必然存在一个希尔伯特空间，其内积运算等效于核运算。这个结论的重要意义在于，我们可以绕开线性空间，通过首先定义kernel的方式，诱导出一个线性空间(叫做再生核希尔伯特空间Reproducing Kernel Hilbert Space)，从而我们就自然获得我们所需要的度量结构和线性运算结构。这是kernel theory的基础。

在很多教科书中，以二次核为例子，把二维空间变成三维，然后告诉大家kernel用于升维。对于这种说法，我一直认为在一定程度上是误导的。事实上，kernel的最首要意义是内积的建立（或者改造），从而诱导出更利于表达的度量和运算结构。对于一个问题而言，选择一个切合问题的kernel比起关注“升维”来得更为重要。

- kernel

kernel被视为非线性化的重要手段，用于处理非高斯的数据分布。这是有道理的。通过nonlinear kernel改造的内积空间，其结构和原空间的结构确实不是线性关联，从这个意义上说，它实施了非线性化。不过，我们还应该明白，它的最终目标还是要回到线性空间，新的内积空间仍旧是一个线性空间，它一旦建立，其后的运算都是线性的，因此，kernel的使用就是为了寻求一个新的线性空间，使得线性运算更加合理——非线性化的改造最终仍旧是要为线性运算服务。

值得一提的是，kernelization本质上说还是一种嵌入过程：对于一个空间先建立内积结构，并且以保持内积结构不变的方式嵌入到一个高维的线性空间，从而继承其线性运算体系。

- 流形

上面说到的都是从全局的方式建立代数结构的过程，但是那必须以某种全局结构为基础（无论预先定义的是运算，度量还是内积，都必须适用于全空间。）但是，全局结构未必存在或者适合，而局部结构往往简单方便得多。这里就形成一种策略，以局部而达全局——这就是流形(manifold)的思想，而其则根源于拓扑学。

从拓扑学的角度说，流形就是一个非常优良的拓扑空间：符合Hausdorff分离公理（任何不同的两点都可以通过不相交的邻域分离），符合第二可数公理（具有可数的拓扑基），并且更重要的是，局部同胚于Rn。因此，一个正则(Regular)流形基本就具有了各种最良好的拓扑特性。而局部同胚于Rn，代表了它至少在局部上可以继承R^n的各种结构，比如线性运算和内积，从而建立分析体系。事实上，拓扑流形继承这些结构后形成的体系，正是现代流形理论研究的重点。继承了分析体系的流形，就形成了微分流形(Differential manifold)，这是现代微分几何的核心。而微分流形各点上的切空间(Tangent Space)，则获得了线性运算的体系。而进一步继承了局部内积结构的流形，则形成黎曼流形(Riemann manifold)，而流形的全局度量体系——测地距离(geodesics)正是通过对局部度量的延伸来获得。进一步的，当流行本身的拓扑结构和切空间上的线性结构发生关系——也就获得一簇拓扑关联的线性空间——向量丛(Vector bundle)。

虽然manifold theory作为现代几何学的核心，是一个博大精深的领域，但是它在learning中的应用则显得非常狭窄。事实上，对于manifold，很多做learning的朋友首先反应的是ISOMAP, LLE, eigenmap之类的算法。这些都属于embedding。当然，这确实是流形理论的一个重要方面。严格来说，这要求是从原空间到其映像的微分同胚映射，因此，嵌入后的空间在局部上具有相同的分析结构，同时也获得了各种好处——全局的线性运算和度量。不过，这个概念在learning的应用中被相当程度的放宽了——微分同胚并不能被完全保证，而整个分析结构也不能被完全保持。大家更关注的是保持局部结构中的某个方面——不过这在实际应用中的折衷方案也是可以理解的。事实表明，当原空间中的数据足够密集的情况下，这些算法工作良好。

Learning中流形应用的真正问题在于它被过滥地运用于稀疏空间(Sparse space)，事实上在高维空间中撒进去几千乃至几十万点，即使最相邻的几点也难称为局部了，局部的范围和全局的范围其实已经没有了根本差别，连局部的概念都立不住脚的时候，后面基于其展开的一切工作也都没有太大的意义。事实上，稀疏空间有其本身的规律和法则，通过局部形成全局的流形思想从本质上是不适合于此的。虽然，流形是一种非常美的理论，但是再漂亮的理论也需要用得其所——它应该用于解决具有密集数据分布的低维空间。至于，一些paper所报告的在高维空间（比如人脸）运用流形方法获得性能提升，其实未必是因为“流形”本身所起的作用，而很可能是其它方面的因素。

流形在实际应用中起重要作用的还有两个方面：一个是研究几何形体的性质（我们暂且不谈这个），还有就是它和代数结构的结合形成的李群(Lie group)和李代数(Lie algebra)。 当我们研究的对象是变换本身的时候，它们构成的空间是有其特殊性的，比如所有子空间投影形成了Grassmann流形，所有的可逆线性算子，或者仿射算子，也形成各自的流形。对他们的最重要操作是变换的结合，而不是加法数乘，因此，它们上面定义的更合适的代数结构应该是群和不是线性空间。而群和微分流形的结合体——李群则成为它们最合适的描述体系——而其切空间则构成了一种加强的线性空间：李代数，用于描述其局部变化特性。

李代数和李群的关系是非常漂亮的。它把变换的微变化转换成了线性空间的代数运算，使得移植传统的基于线性空间的模型和算法到李空间变得可能。而且李代数中的矩阵比起变换本身的矩阵甚至更能反映变换的特性。几何变换的李代数矩阵的谱结构就能非常方便地用于分析变换的几何特性。

最后，回头总结一下关于嵌入这个应用广泛的策略，在learning中的isometry, kernel和manifold embedding都属于此范畴，它们分别通过保持原空间的度量结构，内积结构和局部结构来获得到目标（通常是向量空间）的嵌入，从而获得全局的坐标表达，线性运算和度量，进而能被各种线性算法和模型所应用。

在获得这一系列好处的同时，也有值得我们注意的地方。首先，嵌入只是一种数学手段，并不能取代对问题本身的研究和分析。一种不恰当的原始结构或者嵌入策略，很多时候甚至适得其反——比如稀疏空间的流形嵌入，或者选取不恰当的kernel。另外，嵌入适合于分析，而未必适合于重建或者合成。这是因为嵌入是一个单射(injection)，目标空间不是每一个点都和原空间能有效对应的。嵌入之后的运算往往就打破了原空间施加的限制。比如两个元素即使都是从原空间映射过来，它们的和却未必有原像，这时就不能直接地回到原空间了。当然可以考虑在原空间找一个点它的映射与之最近，不过这在实际中的有效性是值得商榷的。

和Learning有关的数学世界是非常广博的，我随着学习和研究的深入，越来越发现在一些我平常不注意的数学分支中有着适合于问题的结构和方法。比如，广群(groupoid)和广代数(algebroid)能克服李群和李代数在表示连续变换过程中的一些困难——这些困难困扰了我很长时间。解决问题和建立数学模型是相辅相成的，一方面，一个清晰的问题将使我们有明确的目标去寻求合适的数学结构，另一方面，对数学结构的深入理解对于指导问题的解决也是有重要作用的。对于解决一个问题来说，数学工具的选择最重要的是适合，而不是高深，但是如果在现有数学方法陷入困难的时候，寻求更高级别的数学的帮助，往往能柳暗花明。数学家长时间的努力解决的很多问题，并不都是理论游戏，他们的解决方案中很多时候蕴含着我们需要的东西，而且可能导致对更多问题的解决——但是我们需要时间去学习和发现它们。

