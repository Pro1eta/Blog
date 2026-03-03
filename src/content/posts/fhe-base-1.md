---
title: 【同态加密的数学基础1】近世代数基本概念
published: 2026-02-01
description: 理解全同态加密方案的基本知识，包含抽象代数的一些基本概念。
image: ""
tags:
  - Math
  - FHE
category: 笔记
draft: false
pinned: false
lang: zh_CN
series: 同态加密的数学基础
---
> **定义 1** （二元关系）
> 
> $S \neq \emptyset$，$S$ 上的二元关系 $R$ 是：$R \subseteq S \times S$。

> **定义 2** （等价关系）
> 
> $S$ 上的二元关系 “$\sim$” 称为等价关系，若 $\forall a, b, c \in S$ 满足：
> 
> 1. 自反性：$a \sim a$
> 2. 对称性：$a \sim b \implies b \sim a$
> 3. 传递性：$a \sim b$ 且 $b \sim c \implies a \sim c$

> **定义 ３** （等价类）
> 
> 称 $\bar{a}$ (或 $[a]$) $= \{x \in S \mid x \sim a\}$ 为 $a$ 确定的等价类。

> **定义 ４** （商集）
> 
> 所有等价类组成的集合称为商集，记作 $S / \sim = \{ [a] \mid a \in S \}$。

> **定义 ５** （划分）
> 
> 设 $S \neq \emptyset$。若 $S$ 的一组子集 $P = \{A_1, A_2, \dots\}$ 满足：
> 
> 1. 非空性：每个子集都不为空，即 $A_i \neq \emptyset$；
> 2. 互斥性：任意两个不同的子集不相交，即若 $A_i \neq A_j$，则 $A_i \cap A_j = \emptyset$；
> 3. 完备性：所有子集的并集等于原集合，即 $A_1 \cup A_2 \cup \dots = S$。
> 
> 则称集合 $P$ 为 $S$ 的一个**划分**，其中的每个子集 $A_i$ 称为该划分的一个类（Block）。

> **定理 １** （划分定理 Partition Theorem）
> 
> 设 “$\sim$” 是集合 $S$ 上的等价关系，则其商集 $S/\sim = \{[a] \mid a \in S\}$ 构成 $S$ 的一个划分。即满足：
> 1. 非空性：$\forall [a] \in S/\sim, [a] \neq \emptyset$。
> 2. 完备性：$S = \bigcup_{a \in S} [a]$。
> 3. 互斥性：$\forall a, b \in S$，$[a] = [b]$ 或 $[a] \cap [b] = \emptyset$。

**证明：**

1. 由 “$\sim$” 的自反性，对 $\forall a \in S$，有 $a \sim a$。故 $a \in [a]$，从而 $[a] \neq \emptyset$。
2. 由定义可知 $\forall a \in S, [a] \subseteq S$，故 $\bigcup_{a \in S} [a] \subseteq S$。另一方面，对 $\forall a \in S$，由上述可知 $a \in [a]$，故 $a \in \bigcup_{x \in S} [x]$，即 $S \subseteq \bigcup_{a \in S} [a]$。因此$S = \bigcup_{a \in S} [a]$。
3. 只需证：若 $[a] \cap [b] \neq \emptyset$，则 $[a] = [b]$。设 $c \in [a] \cap [b]$，则有 $c \sim a$ 且 $c \sim b$。由对称性与传递性，$a \sim c \sim b \implies a \sim b$。对 $\forall x \in [a]$，有 $x \sim a$。结合 $a \sim b$ 得 $x \sim b$，故 $x \in [b]$，即 $[a] \subseteq [b]$。同理可证 $[b] \subseteq [a]$。因此 $[a] = [b]$。

> **定义 5** （自然投影）
> 
> 设 $\sim$ 是集合 $S$ 上的等价关系，$S/\sim$ 为其诱导的商集。定义映射：
> $\pi : S \to S/\sim$，对应关系为： $\forall a \in ,\pi(a) = [a]$。
> 
> 称此映射为从 $S$ 到其商集 $S/\sim$ 的自然投影（或规范投影）。

> **定理 2** （自然投影的性质）
> 
> 1. $\pi$ 是满射；
> 2. $\pi(a) = \pi(b) \iff a \sim b$。

**证明：**

1. $\forall C \in S/\sim$，由商集定义知 $C$ 必为某个等价类 $[a]$（其中 $a \in S$）。则 $\pi(a) = [a] = C$，故 $\pi$ 为满射。
2. $\pi(a) = \pi(b) \iff [a] = [b] \iff a \sim b$。

> **定义 6** （二元运算）
> 
> 设 $S$ 为非空集合。映射 $\circ: S \times S \to S$ 称为 $S$ 上的一个二元运算。习惯上，将 $\circ(a, b)$ 记作 $a \circ b \in S$。

> **定义 7** （群）
> 
> 代数系统 $(G, \circ)$ 是一个非空集合 $G$ 连同其上的一个二元运算 $\circ$，若满足以下公理：
> 1. 结合律：$\forall a, b, c \in G, \quad (a \circ b) \circ c = a \circ (b \circ c)$
> 2. 单位元：$\exists e \in G, \forall a \in G, \quad a \circ e = e \circ a = a$
> 3. 逆元：$\forall a \in G, \exists a^{-1} \in G, \quad a \circ a^{-1} = a^{-1} \circ a = e$


> [!NOTE] 
> 阿贝尔群/交换群（Abelian Group）：若还满足交换律：$\forall a, b \in G, \quad a \circ b = b \circ a$。


:::important
方便起见，在我的笔记中，群上的二元运算统一叫作“加法”。而环、域上的二元运算统一叫作“加法”和“乘法”。请留意。
:::

> **定义 8** （环）
> 
> 代数系统 $(R, +, \cdot)$ 是一个非空集合 $R$ 连同其上的两个二元运算（加法 $+$ 和乘法 $\cdot$），若满足以下公理：
> 1. 加法交换：$(R, +)$ 是一个阿贝尔群。**单位元称为零元，记为** $0$。$a$ 的加法逆元记为 $-a$。
> 2. 乘法结合：$\forall a, b, c \in R, \quad (a \cdot b) \cdot c = a \cdot (b \cdot c)$。即 $(R, \cdot)$ 是一个半群（封闭、结合）。
> 3. 分配律：乘法对加法满足左、右分配律：
> 
> 左分配律：$a \cdot (b + c) = a \cdot b + a \cdot c$
> 
> 右分配律：$(b + c) \cdot a = b \cdot a + c \cdot a$

:::note
交换环：环的乘法运算也满足交换律。

含元环/含幺环：环中的乘法运算存在单位元（通常记为 $1$），即：
$\exists 1 \in R, \forall a \in R,1 \cdot a = a \cdot 1 = a$
:::

> **定义 9** （域）
> 
> 代数系统 $(F, +, \cdot)$ 称为一个域，如果它满足以下条件：
> 
> 1. 含元交换环：$(F, +, \cdot)$ 首先是一个含有单位元 $1$ 的交换环。
> 2. 非平凡性：$1 \neq 0$。
> 3. 乘法逆元存在性：每一个非零元素都存在乘法逆元。即对于任意 $a \in F$ 且 $a \neq 0$，存在 $a^{-1} \in F$，使得 $a \cdot a^{-1} = 1$。

> **定义 10** （子群）
> 
> 设 $(G, \cdot)$ 是一个群，$H$ 是 $G$ 的一个子集。若 $(H, \cdot)$ 在 $G$ 的相同运算下也构成一个群，则称 $H$ 是 $G$ 的子群，记作：$H \le G$。

> **定理 3** (子群判定定理)
> 
> $H \le G \iff \forall a, b \in H, a \cdot b^{-1} \in H$

**证明：**

$\Rightarrow$：$H$ 是群，由逆元存在性知 $b \in H \Rightarrow b^{-1} \in H$；再由封闭性知 $a \cdot b^{-1} \in H$。

$\Leftarrow$：对 $\forall a, b \in H$ 都有 $a \cdot b^{-1} \in H$，则取 $b = a$，则 $e = a \cdot a^{-1} \in H$。

取 $a = e$，则对 $\forall b \in H$，$e \cdot b^{-1} = b^{-1} \in H$。

对 $\forall a, b \in H$，已知 $b^{-1} \in H$，则 $a \cdot (b^{-1})^{-1} = a \cdot b \in H$。

由于 $H \subseteq G$，结合律在 $H$ 中自然成立。综上，$H$ 构成群，即 $H \le G$。

:::note
群有唯一的加法单位元，子群和加法单位元和父群的加法单位元是同一个。
:::

> **定义 11 **（群同态）
> 
> 设 $(G, \cdot)$ 和 $(G', *)$ 是两个群。称映射 $f: G \to G'$ 为群同态，如果 $\forall a, b \in G, \quad f(a \cdot b) = f(a) * f(b)$。

:::note
核：$\ker f \triangleq \{ g \in G \mid f(g) = e' \}$

像：$\text{Im} f \triangleq \{ f(g) \mid g \in G \}$
:::

> **定理 4** (核和像的性质)
> 
> $\ker f \le G, \text{Im} f \le G'$

**证明：**

对于 $\forall a, b \in \ker f$，有 $f(a) = e'$ 且 $f(b) = e'$。
$f(a \cdot b^{-1}) = f(a) * f(b^{-1}) = f(a) * f(b^{-1}) * e' = f(a) * f(b^{-1}) * f(b) * (f(b))^{-1} = e' * (e')^{-1} = e'$。

$\therefore a \cdot b^{-1} \in \ker f$，由子群判定定理知 $\ker f \le G$。

对于 $\forall a', b' \in \text{Im} f$，由定义知存在 $a, b \in G$ 使得 $f(a) = a', f(b) = b'$。

$a' * (b')^{-1} = f(a) * (f(b))^{-1} = f(a) * (f(b))^{-1} * (f(b)) * f(b^{-1}) = f(a \cdot b^{-1})$

由于 $a \cdot b^{-1} \in G$，故 $f(a \cdot b^{-1}) \in \text{Im} f$。

:::note
容易进一步证明，对于群同态 $f$ 有：

$f(e)=e'$ 且 $f(a^{-1})=(f(a))^{-1}, \forall a$。
:::

> **定义 12** （陪集）
> 
> 设 $H \le G$，对于 $a \in G$：$aH \triangleq \{ah \mid h \in H\}$，称其为 $H$ 在 $G$ 中关于元素 $a$ 的左陪集。$a$ 称为代表元。

> **定理 5** （陪集形成划分）
> 
> 设 $H \le G$，在 $G$ 上定义一个关系 $\sim$：$a \sim b \iff a^{-1}b \in H$。则关系 $\sim$ 是一个等价关系，且其等价类构成的商集 $G/\sim$ 是 $G$ 的一个划分。

**证明：**

对于 $\forall a \in G$，有 $a^{-1}a = e$。因为 $H$ 是子群，所以单位元 $e \in H$。故 $a^{-1}a \in H \implies a \sim a$。满足自反性。

若 $a \sim b$，则 $a^{-1}b \in H$。因为 $H$ 是子群，逆元也在 $H$ 中，所以 $(a^{-1}b)^{-1} = b^{-1}(a^{-1})^{-1} = b^{-1}a \in H$。故 $b \sim a$。

若 $a \sim b$ 且 $b \sim c$，则 $a^{-1}b \in H$ 且 $b^{-1}c \in H$。因为 $H$ 是子群，对运算封闭，所以 $(a^{-1}b)(b^{-1}c) = a^{-1}(bb^{-1})c = a^{-1}ec = a^{-1}c \in H$。故 $a \sim c$。

根据划分定理，即证。

:::note
实际上 $a \sim b \iff a^{-1}b \in H \iff \exists h \in H, a^{-1}b = h \iff b = ah \iff b \in aH$。

因为 $\sim$ 是等价关系，又有 $a \sim b \iff b \sim a$。我们用 $\sim$ 划分等价类：

$b \sim a \iff b \in [a] \iff b \in aH$，这说明 $[a] = aH$。

$G/\sim = \{ [a] \mid a \in S \} = \{aH \mid a \in G\}$，关于集合元素的左陪集，形成了原集合的划分。

我们忽略用子群定义等价关系的环节，直接用子群记为：

$G/H \triangleq \{ aH \mid a \in G \}$
:::

:::note
已知 $\sim$ 是群 $G$ 上的等价关系，能否找到一个子群 $H$ 使得 $a \sim b \iff a^{-1}b \in H$ 呢？

我们可以定义 $H = \{a \in G \mid a \sim e\}$，容易证明 $H \leq G$。
:::

> **定义 13** （正规子群）
> 
> 设 $H \le G$（$H$ 是群 $G$ 的子群）。如果对于所有的 $g \in G$，都有：$gHg^{-1} = H$，则称 $H$ 为 $G$ 的一个正规子群，记作 $H \trianglelefteq G$。

:::tip
容易证明 $gHg^{-1} = H \iff gH = Hg$。
:::

> **定理 6** 
> 
> $H \trianglelefteq G \iff G/H$ 可通过运算 $(aH) * (bH) = (ab)H$ 构成群。

**证明：**

首先证明这个运算确实是良定义的，换言之，它首先应该是一个二元运算。假设 $aH = a'H$ 同时 $bH = b'H$，那么必须有 $(ab)H= (a'b')H$。 

- "$\Rightarrow$": 
  
  若 $a' \in aH, b' \in bH$，需证 $(a'b')H = (ab)H$。设 $a' = ah_1, b' = bh_2 \quad (\exists h_1, h_2 \in H)$，$a'b' = (ah_1)(bh_2) = a(h_1b)h_2$。
  
  由于 $H \trianglelefteq G$，则 $Hb = bH$，故 $\exists h_3 \in H$ 使得 $h_1b = bh_3$。代入得：$a'b' = a(bh_3)h_2 = (ab)(h_3h_2) \in (ab)H$，由此可知 $(a'b')H = (ab)H$。
  
  所以运算是良定义的，其他群性质显然。

- "$\Leftarrow$": 
  
  已知：$\forall a, b \in G$，若 $a' \in aH, b' \in bH$，则必有 $(a'b')H = (ab)H$。取 $\forall h_1 \in H, \forall b \in G$。令 $a = e, a' = h_1 \in eH$；令 $b' = b \in bH$。
  
  由运算良定义可知：$a'b' = h_1b \in (eb)H = bH$。即 $\exists h_3 \in H$ 使得 $h_1b = bh_3$。这说明 $Hb \subseteq bH$。
  
  同理可证明 $Hb = bH$，因此 $H \trianglelefteq G$。

> **定理 7** （群的第一同构定理）
> 
> 设 $f: G \rightarrow G'$ 是群同态，则：
> 1. $\ker f \trianglelefteq G$。 
> 2. $G/\ker f \cong \text{Im} f$ 。

**证明：**

1. $\forall g \in G, k \in \ker f, f(gkg^{-1}) = f(g)f(k)f(g)^{-1} = f(g)e'f(g)^{-1} = e'$, 故 $gkg^{-1} \in \ker f$。所以 $\ker f \trianglelefteq G$。
2. 群同构意味着两个群之间存在一个同态的双射。
   
   定义 $\bar{f}: G/\ker f \rightarrow \text{Im} f$，满足 $\bar{f}(g \ker f) = f(g), \forall g \in G$。说明下面几个事情即可：
   
   映射是良定义的，意思是它确实是一个映射：若 $g' \in g \ker f$，则存在 $h \in \ker f$ 使得 $g' = gh$。由同态性质：$f(g') = f(gh) = f(g)f(h)$。由于 $h \in \ker f$，则 $f(h) = e'$，故 $f(g') = f(g)e' = f(g)$。即 $\bar{f}(g' \ker f) = \bar{f}(g \ker f)$，映射结果不依赖代表元的选取。
   
   双射：显然，对 $\forall y \in \text{Im} f$，由定义 $\exists g \in G$ 使得 $f(g) = y$，则 $\bar{f}(g \ker f) = y$，因此是满射；设 $\bar{f}(g \ker f) = \bar{f}(h \ker f) \Rightarrow f(g) = f(h)$。等式两边左乘 $f(g)^{-1}$ 得 $e' = f(g)^{-1}f(h) = f(g^{-1}h)$。故 $g^{-1}h \in \ker f$，根据陪集性质有 $h \in g \ker f$，即 $g \ker f = h \ker f$，函数值相同则对应的自变量必然相同，因此也是单射。

> **定义 14** （运算相容性）
> 
> 设 $\sim$ 是集合 $S$ 上的等价关系，$\circ$ 是 $S$ 上的二元运算。若对 $\forall a, a', b, b' \in S$，满足：
> 
> $a \sim a', \quad b \sim b' \implies (a \circ b) \sim (a' \circ b')$
> 
> 则称等价关系 $\sim$ 与运算 $\circ$ 相容。

:::note
设 $(G, +)$ 是一个群。若等价关系 $\sim$ 与运算 $+$ 相容，则称 $\sim$ 是 $G$ 上的同余关系。

设 $(R, +, \cdot)$ 是一个环。若等价关系 $\sim$ 同时与加法 $+$ 和乘法 $\cdot$ 相容，则称 $\sim$ 是 $R$ 上的同余关系。
:::

> **定理 8** （商运算的良定义性）
> 
> 设 $\circ$ 是集合 $S$ 上的二元运算，$\sim$ 是 $S$ 上的等价关系。商集 $S/\sim$ 上的运算 $[a] \circ [b] = [a \circ b]$ 是良定义的 $\iff$ $\sim$ 是 $S$ 关于运算 $\circ$ 的同余关系。

**证明：**说明 $\forall a' \in [a], b' \in [b], [a' \circ b'] = [a \circ b]$ 即可。trivial.

> **定理 9** 
> 
> 群 $G$ 上的每一个同余关系 $\sim$，都唯一对应一个正规子群 $H \trianglelefteq G$。

**证明：**

1. 给定同余关系 $\sim$。令 $H = \{g \in G \mid g \sim e\}$，前面提到过，容易证明 $H \le G$。
   
   由运算相容性 $a \sim b \iff a^{-1}b \sim a^{-1}a \iff a^{-1}b \sim e \iff a^{-1}b \in H$，等价类 $[a]$ 恰好是左陪集 $aH$。
   
   由于 $\sim$ 是同余关系，由 Prop. 8 知商运算 $(aH)(bH) = (ab)H$ 是良定义的。根据正规子群的等价定义（已证明过），陪集运算满足良定义性当且仅当 $H \trianglelefteq G$。
1. 给定正规子群 $H \trianglelefteq G$。定义关系 $a \sim b \iff a^{-1}b \in H$，已经证明过这个关系一定是等价关系。设 $a \sim a', b \sim b'$，即 $a^{-1}a' \in H, b^{-1}b' \in H$。考虑 $(ab)^{-1}(a'b') = b^{-1}(a^{-1}a')b' = b^{-1} (h_1) b'$ (其中 $h_1 \in H$)。
   
   由于 $H \trianglelefteq G$，则 $b^{-1}h_1b \in H$，令其为 $h_2$。则 $(ab)^{-1}(a'b') = (b^{-1}h_1b)(b^{-1}b') = h_2 \cdot (b^{-1}b') \in H$。故 $ab \sim a'b'$，即 $\sim$ 是同余关系。
   
   当然，进一步不难说明 $H$ 是 $e$ 的等价类。

> **定义 15** （商群）
> 
> 设 $H \trianglelefteq G$，由 $H$ 在 $G$ 中所有左陪集构成的集合 $G/H = \{gH \mid g \in G\}$，在运算 $(aH)(bH) = (ab)H$ 下构成群。称 $(G/H, \cdot)$ 为 $G$ 对 $H$ 的商群。

:::tip
单位元： $eH = H$；
逆元： $(gH)^{-1} = g^{-1}H$
:::

