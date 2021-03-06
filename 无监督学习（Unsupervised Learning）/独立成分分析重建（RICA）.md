## 独立成分分析重建（ RICA ）

### 1. 独立成分分析概述（ ICA Summary ）

独立成分分析（ ICA ）允许我们使用下面的公式来生成经白化后（译者注：白化是一个数据预处理步骤，用于降低数据冗余）数据的稀疏表示：

$$
\begin{array}{rcl}
{\rm minimize} & \lVert Wx \rVert_1  \\
{\rm s.t.}     & WW^T = I \\
\end{array}
$$

其中， $W$ 是权重矩阵， $x$ 是输入。在独立成分分析中，权重矩阵保持正交约束时，我们最小化隐藏层数据的 $L1$ 惩罚项（即 $L1$ 范数） $Wx$ 。正交约束的存在确保了特征数据的不相关。换句话说，白化过的数据经正交变换仍然是白化的数据。

>**正交的含义**
>正交最早出现于三维空间中的向量分析。 在三维向量空间中， 两个向量的内积如果是零， 那么就说这两个向量是正交的。
>换句话说， 两个向量正交意味着它们是相互垂直的。若向量 $\alpha$ 与 $\beta$ 正交，则记为 $\alpha \perp \beta$ 。
对于一般的希尔伯特空间， 也有内积的概念， 所以人们也可以按照上面的方式定义正交的概念。 特别的， 我们有 $n$ 维欧氏空间中的正交概念， 这是最直接的推广。
和正交有关的数学概念非常多， 比如正交矩阵， 正交补空间，施密特正交化法， 最小二乘法等等。
另外在此补充正交函数系的定义：在三角函数系中任何不同的两个函数的乘积在区间 $[-π,π]$ 上的积分等于 $0$ ，则称这样的三角函数组成的体系叫正交函数系。

独立成分分析中的正交约束带存在缺点，也就是说，随着输入 $x$ 的特征数据的增多，优化也因这个硬约束（正交约束）愈加困难，导致训练时间延长。那如何加速呢？如果数据维度大到不能白化，怎么办？记住！如果输入数据 $ x \in R_n$ ，那么白化矩阵的大小就是 $n \times n$ 维度的。

## 2. 独立成分分析重建（ RICA ）

正因如此，有一种称为 “重建独立成分分析”（Reconstrunction ICA，RICA）的算法，就是基于独立成分分析算法克服了这一缺点，以柔性重建惩罚替代独立成分分析的正交约束。

$$
\min_{W} \quad \lambda \left\|Wx\right\|_1  + \frac{1}{2} \left\| W^T Wx - x \right\|_2^2
$$

为了有助于理解该算法，当特征是不完备的，可以得到一个完美重建。要实现这一点，需要限制 $W^TW = I$ 。当特征非过完备、数据已经白化、 $\lambda$ 趋向于无穷大时，从 RICA 恢复到 ICA 也是可能的；在这一点上，完美重建变成一种硬约束。既然在目标函数中有重建的惩罚项，且没有硬约束，那么我们就能提高过完备特征的比例。但当我们使用过完备基时，结果还是合理的嘛？为了能解释这个问题，我们转向另一个常见的模型上——稀疏自编码。

>**过完备（over-complete）**
>表示信号的矩阵的列数大于行数，现有的向量远远多于应有的基的个数，这些向量构成的矩阵过完备了。 也就是拥有的向量对于表示整个空间的性质来说已经完全冗余了。

为了更好理解转移到一个过完备案例中，我们重新回顾一下稀疏自编码，其目标函数如下：

$$
\min_{W} \quad \lambda \left\|  \sigma\left(Wx\right) \right\|_1 + \frac{1}{2} \left\| \sigma \left(W^T \sigma\left(Wx \right) \right) - x \right\|_2^2
$$

自编码器有着不同的变化，但为了连续性的缘故，此公式使用了 $L1$ 稀疏惩罚，同时有一个对应的重建矩阵 $W$ 。这个稀疏编码和独立成分分析重建（RICA）唯一的区别是 sigmoid 的非线性。从自编码器的视角来观察重建惩罚项，可以看出重建惩罚起着退化控制的作用；也就是说，重建惩罚允许最稀疏的数据表示，这一过程是通过确保滤波矩阵不学重复或冗余的特征进行的。

因此我们可以将过完备例子中的 RICA 看成是带了 $L1$ 稀疏性约束且没有非线性的稀疏自编码器。这使得独立成分分析重建（RICA）超过过完备基并可以像稀疏自编码器一样使用反向传播进行优化。独立成分分析重建（RICA）对非白化数据上也展现出了更鲁棒的特性，这再一次体现了与自编码器行为上的相似。